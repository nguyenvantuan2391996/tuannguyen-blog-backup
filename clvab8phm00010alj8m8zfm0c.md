---
title: "Setup master slave in Redis by using Docker"
seoTitle: "Setup master slave in Redis by using Docker"
seoDescription: "We will exec into redis-slave-1 and redis-slave-2 and run the command "slaveof redis-master 6379". And continue, we enter the command "info replication" to "
datePublished: Mon Apr 22 2024 02:00:42 GMT+0000 (Coordinated Universal Time)
cuid: clvab8phm00010alj8m8zfm0c
slug: setup-master-slave-in-redis-by-using-docker
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1713715370914/28a0f05a-7ecd-45d3-a87e-e2eeee394372.avif
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1713715411388/2c63abcd-8e88-4b56-9d39-05e998f3f7b2.avif
tags: redis, master, slave, master-slave

---

File docker-compose

```plaintext
version: '3'

services:
  redis-master:
    image: redis
    container_name: redis-master
    ports:
      - "6279:6379"

  redis-slave-1:
    image: redis
    container_name: redis-slave-1
    ports:
      - "6179:6379"

  redis-slave-2:
    image: redis
    container_name: redis-slave-2
    ports:
      - "6079:6379"
```

We will create three containers master and two slave with ports: 6279, 6179 and 6079. All containers will be exposed port by gate 6379.

\-&gt; Running the command "**docker-compose -f docker-compose.yml up"** to pull images and start all containers.

![docker-compose](https://images.viblo.asia/b77db75b-f10e-4162-aeec-d6f2ed680878.png align="center")

\-&gt; We will exec into redis-slave-1 and redis-slave-2 and run the command "**slaveof redis-master 6379**". And continue, we enter the command "**info replication**" to check the status of Redis slave that connect to Redis-master.

```plaintext
# redis-cli
127.0.0.1:6379> slaveof redis-master 6379
OK
127.0.0.1:6379> info replication
# Replication
role:slave
master_host:redis-master
master_port:6379
master_link_status:up
master_last_io_seconds_ago:7
master_sync_in_progress:0
slave_read_repl_offset:14
slave_repl_offset:14
slave_priority:100
slave_read_only:1
replica_announced:1
connected_slaves:0
master_failover_state:no-failover
master_replid:76199e2ac4b98caee734d87f52db6844b60ed264
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:14
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:14
127.0.0.1:6379>
```

```plaintext
Last login: Mon Feb  5 23:02:51 on ttys008
➜  ~ docker exec -it 36b5113c9fff /bin/sh
# redis-cli
127.0.0.1:6379> slaveof redis-master 6379
OK
127.0.0.1:6379> info replication
# Replication
role:slave
master_host:redis-master
master_port:6379
master_link_status:up
master_last_io_seconds_ago:5
master_sync_in_progress:0
slave_read_repl_offset:28
slave_repl_offset:28
slave_priority:100
slave_read_only:1
replica_announced:1
connected_slaves:0
master_failover_state:no-failover
master_replid:76199e2ac4b98caee734d87f52db6844b60ed264
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:28
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:29
repl_backlog_histlen:0
127.0.0.1:6379>
```

![docker-compose](https://images.viblo.asia/d15ec8de-5427-4dd2-adb9-27e2c1b42790.png align="center")

This is status of Redis master that be received the configuration by Redis slave one and Redis slave two.

```plaintext
➜  ~ docker exec -it 4c14c8f23a39 /bin/sh
# redis-cli
127.0.0.1:6379> info replication
# Replication
role:master
connected_slaves:2
slave0:ip=172.21.0.2,port=6379,state=online,offset=308,lag=1
slave1:ip=172.21.0.3,port=6379,state=online,offset=308,lag=1
master_failover_state:no-failover
master_replid:76199e2ac4b98caee734d87f52db6844b60ed264
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:308
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:308
127.0.0.1:6379>
```

![docker-compose](https://images.viblo.asia/d5dd2339-af10-4d02-a7e2-ee05065b1eee.png align="center")

\-&gt; In the Redis master, we will run the command "**set test 123**" and check the key "test" in two Redis slaves. This is the result, and all Redis slaves are synced successfully.

![docker-compose](https://images.viblo.asia/253b080e-b92c-4ae8-9d6f-366b4776dd27.png align="center")