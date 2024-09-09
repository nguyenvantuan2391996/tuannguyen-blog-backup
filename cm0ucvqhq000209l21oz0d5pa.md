---
title: "Rate limiter with Redis and Golang"
seoTitle: "Rate limiter with Redis and Golang"
seoDescription: "Rate limiter is a mechanism that controls the number of requests or tasks performed within a certain period. It helps prevent excessive resource"
datePublished: Mon Sep 09 2024 02:00:31 GMT+0000 (Coordinated Universal Time)
cuid: cm0ucvqhq000209l21oz0d5pa
slug: rate-limiter-with-redis-and-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1724488921668/3e9007f2-5603-4e04-898e-50219d8d0731.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1724488970218/f1cf1e0f-eac5-4ce6-9c92-dc867e9bc5f0.png
tags: go, redis, golang, rate-limit, ratelimit

---

![Diagramming System Design: Rate Limiters](https://www.codesmith.io/hs-fs/hubfs/Blog%20Images/Blog%20Photos/rate-limiter-intro.png?width=1518&height=684&name=rate-limiter-intro.png align="center")

**Rate limiter** is a mechanism that controls the number of requests or tasks performed within a certain period. It helps prevent excessive resource usage or denial of service (DDoS) attacks. When the number of requests exceeds a set limit, subsequent requests may be denied or delayed until the limit is reset. This ensures that the system operates stably and fairly for all users.

Some algorithm applies to implement Rate limiter

* **Leaky Bucket**
    
* **Fixed Window Counter**
    
* **Sliding Window Log**
    
* **Sliding Window Count**er
    

Now, there are a lot of services that support and provide for you to config Rate limiter on your website as soon as

Today, I will guide you through the implementation **Rate limiter** with **Redis**, **Golang**, and **Fixed Window Counter** algorithm:

* **Concept**: The Fixed Window Counter algorithm counts the number of requests in a fixed period, called a "window". For example, you can define a window of 1 minute.
    
* **Requests**: Every time a request arrives, the system checks the number of requests made in the current window. The new request will be rejected if the number of requests exceeds the specified limit during that period. The window is reset every fixed period.
    

Now, let's implement with Golang

Init the Redis connection

```go
func initRedis(redisUrl string) (*redis.Client, error) {
	opts, err := redis.ParseURL(redisUrl)
	if err != nil {
		log.Fatal("failed to connect redis:", err)
		return nil, nil
	}

	opts.PoolSize = 30
	opts.ReadTimeout = 5 * time.Second
	opts.WriteTimeout = 5 * time.Second
	opts.Username = ""

	redisClient := redis.NewClient(opts)

	cmd := redisClient.Ping(context.Background())
	if cmd.Err() != nil {
		log.Fatal("failed to ping redis: ", cmd.Err())
		return nil, nil
	}

	return redisClient, nil
}
```

The function gets IP from the request. I will limit the number of requests in a minute following client's IP with 60 requests / 1 minute

```go
func getIPFromRequest(r *http.Request) string {
	ips := r.Header.Get("X-Forwarded-For")
	ipList := strings.Split(ips, ",")
	for _, ip := range ipList {
		if ip = strings.TrimSpace(ip); ip != "" && ip != "::1" && ip != "127.0.0.1" {
			return ip
		}
	}

	ip := r.Header.Get("X-Real-IP")
	if ip != "" {
		return ip
	}

	ip, _, err := net.SplitHostPort(r.RemoteAddr)
	if err != nil {
		return r.RemoteAddr
	}
	return ip
}
```

Middleware to check rate limit. I will write a Lua script with Redis. I will increase one unit when the request is accessed and check if the counter is greater than the maximum number of requests in a minute then return **"not pass"** or else **"pass".** If the request passes the middleware then It forwards it to the service to process and return status code 200 else it returns status code 429 for too many requests.

```go
func (h *HandlerAPI) RateLimiter(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		w.Header().Set("Access-Control-Allow-Origin", "*")
		w.Header().Set("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, OPTIONS")
		w.Header().Set("Access-Control-Allow-Headers", "Content-Type, Origin, Authorization")
		w.Header().Set("Access-Control-Allow-Credentials", "true")

		// Handle preflight requests
		if r.Method == "OPTIONS" {
			w.WriteHeader(http.StatusOK)
			return
		}

		// rate limit
		ip := getIPFromRequest(r)
		script := `
		local currentCount = tonumber(redis.call('GET', KEYS[1]) or '0')
		if currentCount == 0 then
			redis.call('SET', KEYS[1], 0, 'EX', ARGV[1])
		end

		redis.call('SET', KEYS[1], currentCount + 1, 'KEEPTTL')

		if currentCount > tonumber(ARGV[2]) then
			return "not pass"
		else
			return "pass"
		end`

		// Running Lua Script
		resultStr, err := h.RedisClient.Eval(context.Background(), script, []string{ip}, 60, MaxRequestOneMinute).Result()
		if err != nil {
			logrus.Warnf("Running Lua Script is failed with err: %v", err)
			return
		}

		if resultStr == "not pass" {
			w.WriteHeader(http.StatusTooManyRequests)
			return
		}

		next.ServeHTTP(w, r)
	})
}
```

Test handler will return "Hello Viet Nam" with status code 200

```go
func (h *HandlerAPI) testHandler(w http.ResponseWriter, r *http.Request) {
	if r.Method != http.MethodGet {
		http.Error(w, "Method not allowed", http.StatusMethodNotAllowed)
		return
	}

	w.WriteHeader(http.StatusOK)
	_, err := fmt.Fprintf(w, "Hello Viet Nam")
	if err != nil {
		return
	}
}
```

The main goroutine

```go
func main() {
	redisClient, err := initRedis("redis://default:@localhost:6379")
	if err != nil {
		panic("failed to init redis")
	}

	handler := HandlerAPI{
		RedisClient: redisClient,
	}

	mux := http.NewServeMux()
	mux.Handle("/test", handler.RateLimiter(http.HandlerFunc(handler.testHandler)))

	// Start the server
	log.Fatal(http.ListenAndServe(":3000", mux))
}
```

I will write the function to test the Rate limiter. I test to call 100 current request.

```go
func TestHandlerAPI_testHandler(t *testing.T) {
	var wg sync.WaitGroup
	for i := 0; i < 100; i++ {
		wg.Add(1)
		go func() {
			defer wg.Done()
			callAPI()
		}()
	}

	wg.Wait()
	fmt.Println("finish")
}

func callAPI() {
	url := "http://localhost:3000/test"
	method := "GET"

	client := &http.Client{}
	req, err := http.NewRequest(method, url, nil)

	if err != nil {
		fmt.Println(err)
		return
	}
	res, err := client.Do(req)
	if err != nil {
		fmt.Println(err)
		return
	}
	defer func(Body io.ReadCloser) {
		err := Body.Close()
		if err != nil {
			return
		}
	}(res.Body)

	fmt.Println(res.StatusCode)
}
```

The result

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724488589534/e6d66033-1080-4bac-87e0-a637f9645d9f.png align="center")

Reference:

* Source code: [https://github.com/nguyenvantuan2391996/example-code/tree/master/rate-limiter](https://github.com/nguyenvantuan2391996/example-code/tree/master/rate-limiter)