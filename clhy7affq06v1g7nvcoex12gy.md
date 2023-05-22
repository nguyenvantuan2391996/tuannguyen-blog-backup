---
title: "Lua Script trong Redis: Sức mạnh và Ứng dụng"
seoTitle: "Lua Script trong Redis: Sức mạnh và Ứng dụng"
seoDescription: "Lua là một ngôn ngữ lập trình nhúng (embeddable) nhẹ nhàng, nhanh chóng và có khả năng mở rộng. Redis sử dụng Lua Scripting Engine để thực thi các đoạn mã."
datePublished: Mon May 22 2023 02:00:39 GMT+0000 (Coordinated Universal Time)
cuid: clhy7affq06v1g7nvcoex12gy
slug: lua-script-trong-redis-suc-manh-va-ung-dung
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1684721871361/a04b138e-4373-4a59-9e60-3e07c34aed1f.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1684644937328/7275f4bb-fa18-4b59-be88-f4eb906588a6.jpeg
tags: redis, golang, lua, lua-script

---

# I. Giới thiệu về Redis

Redis là một hệ thống quản lý cơ sở dữ liệu key-value in-memory mã nguồn mở. Tên gốc của Redis là "Remote Dictionary Server", nhưng hiện nay nó đã được phát triển thành một hệ thống đa chức năng, hỗ trợ nhiều kiểu dữ liệu và có thể sử dụng cho nhiều mục đích.

**Một vài đặc điểm và ưu điểm của Redis**

* **Hiệu suất cao:** Redis được thiết kế để đạt hiệu suất tối đa. Vì dữ liệu được lưu trữ trong bộ nhớ chính, nó cho phép truy cập nhanh và thời gian đáp ứng rất ngắn. Redis cũng hỗ trợ việc lưu trữ dữ liệu trên đĩa nếu cần.
    
* **Hỗ trợ kiểu dữ liệu đa dạng:** Redis hỗ trợ nhiều kiểu dữ liệu như strings, lists, sets, hashes, sorted sets và bitmaps. Điều này cho phép bạn lưu trữ và làm việc với các dạng dữ liệu phức tạp một cách dễ dàng.
    
* **Cơ chế bộ nhớ chia sẻ:** Redis sử dụng cơ chế bộ nhớ chia sẻ cho tất cả các kết nối, giúp tiết kiệm tài nguyên và tăng hiệu suất. Điều này cho phép Redis xử lý hàng ngàn yêu cầu mỗi giây.
    
* **Khả năng mở rộng và phân tán:** Redis hỗ trợ cơ chế sharding và replica sets, cho phép mở rộng và phân tán hệ thống dễ dàng. Bạn có thể thêm các node Redis mới và chia sẻ tải công việc giữa các node để đáp ứng với lưu lượng truy vấn lớn.
    
* **Hỗ trợ các tính năng bổ sung:** Redis cung cấp nhiều tính năng bổ sung như pub/sub (publish/subscribe), transactions, Lua scripting, phân tích và ghi log, và hỗ trợ nhiều ngôn ngữ lập trình.
    

![Redis Explained](https://architecturenotes.co/content/images/2022/07/Redis-v2-separate-05.jpg align="center")

Reference: [Introduction to Redis](https://redis.io/docs/about)

# II. Lua Script trong Redis

Một trong những tính năng quan trọng của Redis là khả năng chạy các Lua Script trực tiếp trong máy chủ Redis. Trong bài viết này, chúng ta sẽ khám phá sức mạnh của Lua Script trong Redis, xem các ứng dụng phổ biến của chúng và cùng mình implement lua script với Golang.

## 1\. Lua Script là gì?

Lua là một ngôn ngữ lập trình nhúng (embeddable) nhẹ nhàng, nhanh chóng và có khả năng mở rộng. Redis sử dụng Lua Scripting Engine để thực thi các đoạn mã Lua trong môi trường máy chủ Redis. Điều này mang lại sự linh hoạt và hiệu suất cao cho việc thực thi các tác vụ phức tạp trên dữ liệu Redis.

## 2\. Lợi ích của việc sử dụng Lua Script trong Redis

* **Hiệu suất**: Lua Script được thực thi trực tiếp trong máy chủ Redis, giảm thiểu việc giao tiếp mạng và giúp tăng tốc độ thực thi. Ngoài ra, Redis cung cấp bộ nhớ chia sẻ cho tất cả các kịch bản Lua, giúp tăng hiệu suất và giảm tải cho máy chủ.
    
* **Tuân thủ nguyên tắc ACID:** Redis hỗ trợ giao dịch nguyên tắc (ACID) thông qua việc thực thi Lua Script. Lua Script có thể thực hiện nhiều lệnh Redis liên quan đến nhiều khóa trong một giao dịch duy nhất, đảm bảo tính toàn vẹn dữ liệu.
    

## 3\. Các ứng dụng phổ biến của Lua Script trong Redis

* **Atomically Increment và Decrement:** Lua Script cho phép bạn tăng hoặc giảm giá trị của một khóa Redis một cách atomic. Điều này hữu ích trong các trường hợp như đếm lượt xem, đếm số lượt thích hoặc xử lý bất kỳ hoạt động nào yêu cầu tăng giảm số lượng.
    
* **Filtering và Sorting:** Bằng cách sử dụng Lua Script, bạn có thể thực hiện các thao tác lọc và sắp xếp trên các cấu trúc dữ liệu Redis như Sorted Set hoặc List. Việc này cho phép bạn truy xuất và truy vấn dữ liệu theo nhu cầu cụ thể.
    
* **Atomicity và isolation:** Lua Script cung cấp tính năng atomicity và isolation trong một môi trường phân tán. Bạn có thể sử dụng Lua Script để thực hiện các tác vụ phức tạp trên nhiều khóa Redis trong một giao dịch duy nhất, đảm bảo tính nhất quán dữ liệu.
    
* **Xử lý dữ liệu phức tạp:** Lua Scripting cho phép bạn viết mã tùy chỉnh để xử lý dữ liệu Redis theo logic phức tạp. Bạn có thể thực hiện các tính toán, biểu thức điều kiện, vòng lặp và gọi các hàm Redis trong một đoạn mã Lua duy nhất.
    

![Lua](https://addons-content.teamspeak.com/1ea680fd-dfd2-49ef-a259-74d27593b867/images/648_366/lua%20plugin.jpg align="center")

# III. Ứng dụng Lua Script trong Redis

## 1\. Bài toán

Chúng ta cần đếm 1 biến counter được lưu ở trong redis. Mỗi khi có request tới biến counter này sẽ được cộng thêm 1.

Trước khi đi vào implement theo cách dùng và không dùng lua script. Mình sẽ khởi tạo redis trước.

**Init redis với Golang**

```Golang
func initRedis() (*redis.Client, error) {
	var redisClient *redis.Client

	opts, err := redis.ParseURL("redis://:@localhost:6384")
	if err != nil {
		log.Fatal("failed to init redis:", err)
		return nil, err
	}

	opts.PoolSize = 30
	opts.DialTimeout = 10 * time.Second
	opts.ReadTimeout = 5 * time.Second
	opts.WriteTimeout = 5 * time.Second
	opts.IdleTimeout = 5 * time.Second

	redisClient = redis.NewClient(opts)

	cmd := redisClient.Ping(context.Background())
	if cmd.Err() != nil {
		return nil, cmd.Err()
	}

	return redisClient, nil
}
```

## 2\. Xử lý không dùng Lua Script

* Bước 1: Get giá trị theo key **counter** truyền vào. Nếu key không tồn tại thì gắn default giá trị là "0".
    
* Bước 2: Convert giá trị vừa get được từ redis về kiểu số.
    
* Bước 3: Tăng biến đếm thêm 1.
    
* Bước 4: Set giá trị biến đếm mới vào redis.
    

Nếu xử lý thông thường chúng ta sẽ có 4 bước như vậy để tăng biến đếm thêm 1 và sẽ phải gọi vào redis 2 lần với function get và set.

**Implement Golang**

```Golang
func DoNotUseLuaScript(redisClient *redis.Client) int {
	// Step 1
	currentCountStr, err := redisClient.Get(context.Background(), "counter").Result()
	if err != nil && err != redis.Nil {
		logrus.Warnf("Get value redis by key %v is failed with err: %v", "counter", err)
		return 0
	}

	if err == redis.Nil {
		currentCountStr = "0"
	}

	// Step 2
	currentCount, err := strconv.Atoi(currentCountStr)
	if err != nil {
		logrus.Warnf("Convert string to int is failed with err: %v", err)
		return 0
	}

	// Step 3: increase
	newCount := currentCount + 1

	// Step 4: Set value redis
	isSet := redisClient.Set(context.Background(), "counter", newCount, 0).Val()
	if isSet != "OK" {
		logrus.Warnf("Set value redis by key %v is failed with err: %v", "counter", err)
		return 0
	}

	return newCount
}
```

## 3\. Xử lý dùng Lua Script

Chúng ta dùng Lua Script bằng cách gộp 4 bước xử lý ở trên thành 1 script và gọi vào redis để xử lý. Ưu điểm sẽ chỉ cần 1 lần gọi vào redis thay vì nhiều lần như ở cách phía trên.

**Implement Golang**

```Golang
func UseLuaScript(redisClient *redis.Client) int {
	script := `
		local currentCount = tonumber(redis.call('GET', KEYS[1]) or '0')
		local newCount = currentCount + tonumber(ARGV[1])
		redis.call('SET', KEYS[1], newCount)
		return newCount
	`

	// Running Lua Script
	resultStr, err := redisClient.Eval(context.Background(), script, []string{"counter-lua"}, 1).Result()
	if err != nil {
		logrus.Warnf("Running Lua Script is failed with err: %v", err)
		return 0
	}

	result, err := strconv.Atoi(fmt.Sprintf("%v", resultStr))
	if err != nil {
		logrus.Warnf("Convert string to int is failed with err: %v", err)
		return 0
	}

	return result
}
```

## 4\. Hàm main

```Golang
func main() {
	// init redis
	redisClient, err := initRedis()
	if err != nil {
		logrus.Warnf("init redis client is failed with err: %v", err)
		return
	}

	// use lua script
	resultLua := 0
	startLua := time.Now()
	for i := 0; i < 10; i++ {
		resultLua = UseLuaScript(redisClient)
	}
	endLua := time.Now()
	logrus.Infof("Result lua script type: %v and time %v ms", resultLua, endLua.Sub(startLua).Milliseconds())

	// do not use lua script
	result := 0
	start := time.Now()
	for i := 0; i < 10; i++ {
		result = DoNotUseLuaScript(redisClient)
	}
	end := time.Now()
	logrus.Infof("Result do not use lua script type: %v and time %v ms", result, end.Sub(start).Milliseconds())
}
```

## 5\. Kết quả

Để rõ hiệu quả, ưu điểm của việc sử dụng lua script trong redis chúng ta sẽ chạy hàm main để đo thời gian xử lý của 2 cách.

**Khi có 10 request tăng biến đếm**

```c
INFO[0000] Result lua script type: 10 and time 17 ms    
INFO[0000] Result do not use lua script type: 10 and time 26 ms
```

**Khi có 100 request tăng biến đếm**

```c
INFO[0000] Result lua script type: 100 and time 99 ms   
INFO[0000] Result do not use lua script type: 100 and time 121 ms
```

Nhìn vào kết quả chúng ta cũng thấy việc áp dụng lua script vào trong redis sẽ giúp xử lý rất nhanh. Ở đây mình mới chỉ lấy ví dụ rất đơn giản. Nếu logic xử lý phức tạp thì việc áp dụng lua script vào redis sẽ có nhiều ưu điểm: nhanh, ít resource gọi redis, clean code,...

# IV. Source code

* Source code: [Example with Golang](https://github.com/nguyenvantuan2391996/example-code/blob/master/lua-script-redis/main.go)
    
* Reference: [Scripting with Lua](https://redis.io/docs/manual/programmability/eval-intro/)