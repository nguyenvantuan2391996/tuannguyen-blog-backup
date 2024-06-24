---
title: "Redis Publish/Subscribe with Golang"
seoTitle: "Redis Publish/Subscribe with Golang"
seoDescription: "In my opinion, Redis Pub/Sub is usually when the system wanna low latency because Redis is saved in memory, so it processes very fast."
datePublished: Mon Jun 24 2024 02:00:10 GMT+0000 (Coordinated Universal Time)
cuid: clxsbyp3a000708mi0l4r8yne
slug: redis-publishsubscribe-with-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1719070174973/d14987b2-2cfa-4fa6-a5ad-8b9d8c9513c4.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1719070248478/005113d0-8bbf-4e09-80f3-30c8bd4b98f6.png
tags: redis, golang, pubsub

---

[`SUBSCRIBE`, `UNSUBSC`](https://redis.io/docs/latest/commands/subscribe/)[`RIBE` and `PUBLIS`](https://redis.io/docs/latest/commands/unsubscribe/)[`H` implem](https://redis.io/docs/latest/commands/publish/)ent the [Publish/Subscribe messaging paradigm where (citing Wikipedia) senders (p](http://en.wikipedia.org/wiki/Publish/subscribe)ublishers) are not programmed to send their messages to specific receivers (subscribers). Rather, published messages are characterized into channels, without knowledge of what (if any) subscribers there may be. Subscribers express interest in one or more channels and only receive messages that are of interest, without knowledge of what (if any) publishers there are. This decoupling of publishers and subscribers allows for greater scalability and a more dynamic network topology.

![](https://images.viblo.asia/c5051c43-10af-41ad-825e-b149a73f36c6.png align="center")

In my opinion, Redis Pub/Sub is usually when the system wanna low latency because Redis is saved in memory, so it processes very fast. However, the message in Redis may be lost when the system or machine is shut down.

Pub/Sub in Redis is implemented and maintained very easily because the engineer knows Redis and backs up it.

Redis Pub/Sub channels can have multiple subscribers, but too many may have a performance impact.

What are the appropriate use cases for the Redis Pub/Sub “connected” delivery semantics? 

1. Real-time, low-latency, urgent messages: If messages are short-lived and age rapidly, so therefore are only relevant to subscribers for a short time window (basically “immediately”)
    
2. Unreliable delivery/lossy messaging: If it doesn’t matter if some messages are simply discarded (e.g. redundant messages of low importance rather than uniquely critical “business” messages) due to unreliable delivery (failures in the network and subscribers, or [**failover from master to replicas**](https://support.huaweicloud.com/intl/en-us/ae-ad-1-usermanual-dcs/dcs-faq-0730011.html), may all result in discarded messages)
    
3. A requirement for at-most-once delivery per subscriber, i.e. subscribers are not capable of detecting duplicate messages and target systems are not idempotent
    
4. If subscribers have short-lived, evolving, or dynamic interest in channels, and only want to receive messages from specific channels for finite periods of time (e.g. mobile IoT devices may only be intermittently connected, and only interested and able to respond to current messages at their location)
    
5. If subscribers (and channels and publishers too!) are themselves potentially short-lived
    
6. There are only a small number of subscribers and patterns per channel
    

Bellow this is the sample Pub/Sub when we use redis client

Subscribe channel **test**

```basic
127.0.0.1:6379> SUBSCRIBE test
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "test"
3) (integer) 1
```

Publish channel **test** with message "hihi"

```basic
127.0.0.1:6379> PUBLISH test hihi
(integer) 1
127.0.0.1:6379>
```

Result when subscriber receives the message from publisher

```basic
127.0.0.1:6379> SUBSCRIBE test
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "test"
3) (integer) 1
1) "message"
2) "test"
3) "hihi"
```

Now, let's implement Redis Pub/Sub with Golang. My flow: I will publish the message into the channel **test** by one goroutine and one goroutine will subscribe to the channel **test** and push it into one channel. I use 5 workers to read messages from the channel and process them.

Init the Redis connection

```go
func initRedis() (*RedisClient, error) {
	var redisClient *redis.Client

	opts, err := redis.ParseURL("redis://default:@localhost:6279")
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

The goroutine publishes the message to the channel **test** and process them

```go
func main() {
	// init redis
	redisClient, err := initRedis()
	if err != nil {
		logrus.Warnf("init redis client is failed with err: %v", err)
		return
	}

	ctx, channel := context.Background(), "test"
	ch := make(chan string, 1)
	numberOfWorkers := 5
	quit := make(chan os.Signal)
	signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM, syscall.SIGQUIT)

	// 5 workers
	for i := 0; i < numberOfWorkers; i++ {
		go func() {
			for msg := range ch {
				fmt.Println(msg)
			}
		}()
	}

	subscriber := redisClient.Redis.Subscribe(ctx, channel)
	go func() {
		for {
			message, err := subscriber.ReceiveMessage(ctx)
			if err != nil {
				continue
			}

			// push to channel
			ch <- message.Payload
		}
	}()

	time.Sleep(1 * time.Second)
	go func() {
		for i := 0; i < 1000; i++ {
			redisClient.Redis.Publish(ctx, channel, fmt.Sprintf("hello %v", i))
		}
	}()

	<-quit
	log.Println("shutting down")
}
```

Reference:

* Source code: [https://github.com/nguyenvantuan2391996/example-code/tree/master/redis-pub-sub](https://github.com/nguyenvantuan2391996/example-code/tree/master/redis-pub-sub)
    
* [https://redis.io/docs/latest/develop/interact/pubsub/](https://redis.io/docs/latest/develop/interact/pubsub/)