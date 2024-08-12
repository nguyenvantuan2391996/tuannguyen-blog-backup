---
title: "Queue with Redis lists and Golang"
seoTitle: "Queue with Redis lists and Golang"
seoDescription: "Queue is a data structure in the programming. It is used to transmit the message between system components. Queue operates the FIFO principle."
datePublished: Mon Aug 12 2024 02:00:18 GMT+0000 (Coordinated Universal Time)
cuid: clzqcjlsy000409k42lhj16j7
slug: queue-with-redis-lists-and-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1723399954416/0dacf621-b11c-4768-9fed-a605ab48bc0f.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1723399996043/cdb61e18-fea6-4b9e-b45e-5ddf74982123.png
tags: go, redis, golang, queue, redis-list

---

![The Cost of Long Queues: 3 Reasons to Use a Queue Management Solution for  Brick & Mortars - AnalyticsWeek | All Things Analytics Leadership News,  Blogs, and Magazine](https://v-count.com/wp-content/uploads/2021/07/shutterstock_1891209922-3.png align="center")

Queue is a data structure in the programming. It is used to transmit the message between system components. Queue operates the FIFO ( First in first out ) principle. It means if the message is pushed to the queue before then it will be the consumer handle and process.

Components in Queue:

1. Producer: where the message is produced and pushed to the queue.
    
2. Consumer: where the message is received and processed. It will get the message from the queue.
    
3. Message queue: where the message is stored.
    
4. Message: It is the message and is created by the producer.
    

Message queues usually increase the durability of the system. Message queues support asynchronous processing. Example: The user imports a list of products, it will be pushed to the queue and the system will process it. The user will receive a notification once the video has finished processing, without having to wait for the process to complete before continuing to use the service.

Bellow this is the sample and guide implemented with Redis lists client

**Redis lists** are linked lists of string values. **Redis lists** are frequently used to:

* Implement stacks and queues.
    
* Build queue management for background worker systems.
    

**Basic commands**

* [`LPUSH`](https://redis.io/docs/latest/commands/lpush/) add a new element to the head of a list; [`RPUSH`](https://redis.io/docs/latest/commands/rpush/) adds to the tail.
    
* [`LPOP`](https://redis.io/docs/latest/commands/lpop/) removes and returns an element from the head of a list; [`RPOP`](https://redis.io/docs/latest/commands/rpop/) does the same but from the tails of a list.
    
* [`LLEN`](https://redis.io/docs/latest/commands/llen/) returns the length of a list.
    
* [`LMOVE`](https://redis.io/docs/latest/commands/lmove/) atomically moves elements from one list to another.
    
* [`LRANGE`](https://redis.io/docs/latest/commands/lrange/) extracts a range of elements from a list.
    
* [`LTRIM`](https://redis.io/docs/latest/commands/ltrim/) reduces a list to the specified range of elements.
    

**Blocking commands**

Lists support several blocking commands. For example:

* [`BLPOP` remo](https://redis.io/docs/latest/commands/blpop/)ves and returns an element from the head of a list. If the list is empty, the command blocks until an element becomes available or until the specified timeout is reached.
    
* `BRPOP` [is](https://redis.io/docs/latest/commands/rpop/) a blocking list pop primitive. It is the blocking version of [`RPOP`](https://redis.io/docs/latest/commands/rpop/) because it blocks the connection when there are no elements to pop from any of the given lists. An element is popped from the tail of the first list that is non-empty, with the given keys being checked in the order that they are given.
    

Because queue operates the FIFO ( First in first out ) principle, I will use LPush and BRPop to implement.

**127.0.0.1:6379&gt; brpop tuan 0** -&gt; consume queue **"tuan"** without timeout. BRPop will wait with a timeout if the queue is empty. If the timeout is 0 it will wait forever.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1723398821820/6672c46d-2ea0-4fa9-a074-6169d2fb4510.png align="center")

**127.0.0.1:6379&gt; lpush tuan "hello-world"** -&gt; push to the queue **"tuan"** a message with value **"hello-world"**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1723398922627/e34fba87-93c9-45c4-bb93-c0f336c425ab.png align="center")

A result consumer gets this message from the queue **"tuan".** Redis return \[\]string with the key and value of the message

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1723398996189/cd8962dd-f766-406e-8381-d157e8097287.png align="center")

Now, let's implement the queue with Golang

Init the Redis connection

```go
func initRedis() (*RedisClient, error) {
	var redisClient *redis.Client

	opts, err := redis.ParseURL("redis://default:@localhost:6379")
	if err != nil {
		log.Fatal("failed to init redis:", err)
		return nil, err
	}

	opts.PoolSize = 30
	opts.DialTimeout = 10 * time.Second
	opts.ReadTimeout = 5 * time.Second
	opts.WriteTimeout = 5 * time.Second
	opts.IdleTimeout = 5 * time.Second
	opts.Username = ""

	redisClient = redis.NewClient(opts)

	cmd := redisClient.Ping(context.Background())
	if cmd.Err() != nil {
		return nil, cmd.Err()
	}

	return &RedisClient{
		Redis: redisClient,
	}, nil
}
```

The worker will consume the queue. It gets and processes the message from the queue.

```go
// the worker consumes queue
	go func() {
		for {
			// using BRPop will wait with a timeout if the queue is empty. If timeout is 0 it will wait forever
			message, err := redisClient.BRPop(context.Background(), queue, 0)
			if err != nil {
				fmt.Println(err)
				continue
			}

			fmt.Println(fmt.Sprintf("message: %v", message))
		}
	}()
```

The worker push the message to the queue

```go
go func() {
		for i := 0; i < 10; i++ {
			_, err := redisClient.LPush(ctx, queue, fmt.Sprintf("hello %v", i))
			if err != nil {
				return
			}
		}
	}()
```

This is the main function to implement

```go
func main() {
	// init redis
	redisClient, err := initRedis()
	if err != nil {
		logrus.Warnf("init redis client is failed with err: %v", err)
		return
	}

	ctx, queue := context.Background(), "tuan"
	quit := make(chan os.Signal)
	signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM, syscall.SIGQUIT)

	// the worker consumes queue
	go func() {
		for {
			// using BRPop will wait with a timeout if the queue is empty. If timeout is 0 it will wait forever
			message, err := redisClient.BRPop(context.Background(), queue, 0)
			if err != nil {
				fmt.Println(err)
				continue
			}

			fmt.Println(fmt.Sprintf("message: %v", message))
		}
	}()

	go func() {
		for i := 0; i < 10; i++ {
			_, err := redisClient.LPush(ctx, queue, fmt.Sprintf("hello %v", i))
			if err != nil {
				return
			}
		}
	}()

	<-quit
	log.Println("shutting down")
}
```

Reference:

* Source code: [https://github.com/nguyenvantuan2391996/example-code/tree/master/redis-queue](https://github.com/nguyenvantuan2391996/example-code/tree/master/redis-queue)
    
* [https://redis.io/docs/latest/develop/data-types/lists/](https://redis.io/docs/latest/develop/data-types/lists/)
    
* [https://redis.io/docs/latest/commands/?group=list](https://redis.io/docs/latest/commands/?group=list)