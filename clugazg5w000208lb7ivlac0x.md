---
title: "Cấu hình master slave trong Redis bằng Docker"
seoTitle: "Cấu hình master slave trong Redis bằng Docker"
seoDescription: "Khởi tạo 3 containers master và 2 slave tương ứng với các port: 6279, 6179 và 6079. Cả 3 containers đều được expose port ra bên ngoài bằng cổng 6379"
datePublished: Mon Apr 01 2024 02:00:24 GMT+0000 (Coordinated Universal Time)
cuid: clugazg5w000208lb7ivlac0x
slug: cau-hinh-master-slave-trong-redis-bang-docker
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1707149839276/2f6b9d16-94d8-4ec8-8ef2-380549b48b78.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1707149880283/1a9b8ea9-9791-49d3-b969-04f640485bec.jpeg
tags: redis, master, slave, master-slave

---

File docker compose

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

Khởi tạo 3 containers master và 2 slave tương ứng với các port: 6279, 6179 và 6079. Cả 3 containers đều được expose port ra bên ngoài bằng cổng 6379

\-&gt; Chạy lệnh **docker compose -f docker-compose.yml up** để pull image và start containers

![docker-compose](https://images.viblo.asia/b77db75b-f10e-4162-aeec-d6f2ed680878.png align="center")

\-&gt; Thực hiện exec vào container redis-slave-1 và redis-slave-2 chạy lệnh **slaveof redis-master 6379** và sau đó **info replication** kiểm tra trạng thái của slave xem đã connect tới redis-master chưa

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

Trạng thái của master đã nhận cấu hình của 2 slave

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

\-&gt; Chạy lệnh set 1 key test giá trị 123 ở master và kiểm tra key này ở 2 slave. Kết quả đã đồng bộ thành công

![docker-compose](https://images.viblo.asia/253b080e-b92c-4ae8-9d6f-366b4776dd27.png align="center")