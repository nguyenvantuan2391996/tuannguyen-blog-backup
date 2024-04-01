---
title: "Change Data Capture - Cấu hình connector với Kafka"
seoTitle: "Change Data Capture - Cấu hình connector với Kafka"
seoDescription: "Change Data Capture là một tiến trình được sử dụng trong quản lý database để định danh và chụp lại log hoặc stream."
datePublished: Mon Apr 01 2024 15:50:14 GMT+0000 (Coordinated Universal Time)
cuid: cluh4mm0s000508jx7d7ebtik
slug: change-data-capture-cau-hinh-connector-voi-kafka
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1711986425574/b516ddb1-e7c2-4000-8db1-70142b9a6995.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1711986485005/c84b8128-cfdc-477d-a875-030d5852d0dd.png
tags: kafka, cdc, debezium, change-data-capture

---

# 1\. Giới thiệu

**Change Data Capture** là một tiến trình được sử dụng trong quản lý database để định danh và chụp lại log hoặc stream. Nó cho phép các ứng dụng hoặc hệ thông có thể consume và xử lý những dữ liệu thay đổi mới nhất.

Ngày xưa, khi chúng ta cần sync dữ liệu data từ cơ sở dữ liệu **mysql - mysql**, **postgresql - postgresql** hay **mongodb - mongodb** thì chỉ việc export từ cơ sở dữ liệu này rồi import sang cơ sở dữ liệu cần đồng bộ.

Cách làm này rất đơn giản! Tuy nhiên, chúng ta cần đánh đổi **down time máy chủ** 1 khoảng thời gian và khi lượng data rất lớn thì mất rất nhiều thời gian hoặc có thể bị treo máy chủ. Chưa kể việc muốn đồng bộ từ cơ sở dữ liệu này sang một hệ thống hoặc cơ sở dữ liệu khác loại. Ví dụ như **mysql - elasticsearch**, **mysql - postgresql**, **mysql - mongodb**,... thì đều khó hoặc loại cơ sở dữ liệu đó không hỗ trợ.

Để giải quyết những bài toán trên thì **CDC - Change Data Capture** đã được sinh ra. Hiện nay, **Debezium** là 1 CDC tool được rất nhiều công ty lớn sử dụng. Ở **TIKI** thì **CDC** được dùng khá phổ biến trong các service. Nó được sử dụng kết hợp với **Kafka** - các **topic, partition** của **Kafka** là nơi chứa các message và các service sẽ tạo **worker** để **consume topic** rồi xử lý nghiệp vụ. Dưới đây là flow cách hoạt động của **Debezium**

![](https://lh7-us.googleusercontent.com/Of-ZSgA3qLGkF6gXQl0KQwZw_GAKBoBDxKiFe7eDlW0V1awwmobc418AN3p3EFbLwi3ewo17kqBiYFMOhWVlpffY6YJROEJueCnbX3Zx-ZAebgdFXtJtBX7jVRU02DHIPtY63Bq4txVRYn_jcYbEajEptA=s2048 align="left")

**Debezium** sẽ đứng để capture lại sự thay đổi ở cơ sở dữ liệu -&gt; bắn các message thay đổi dữ liệu vào các **topic Kafka** \-&gt; các sink connector sẽ consume các topic và tiêu thụ message -&gt; sync dữ liệu sang các hệ thống thứ 3.

***Note:*** *Sink connector ở đây là 1 connector trong hệ sinh thái Apache Kafka. Nó có nhiệm vụ consume và tiêu thụ các message từ các topic.*

Đây là 1 flow của **Debezium** để sync dữ liệu. Tuy nhiên, cá nhân mình hay các ae SE thì chỉ cần chú ý tới bước đẩy message vào các topic trong Kafka. Chúng ta sẽ tự viết 1 worker để consume topic để xử lý logic nghiệp vụ hệ thống.

# 2\. Cấu hình

Dạo gần đây, mình hay dùng docker nên thực hành dùng docker luôn. Bật mí là mình chỉ biết dùng docker thôi chứ không có chuyên :v đi phỏng vấn mà hỏi xoáy docker là mình auto tạch à :((

```apache
version: '2'
services:
  zookeeper:
    image: quay.io/debezium/zookeeper:${DEBEZIUM_VERSION}
    ports:
     - 2181:2181
     - 2888:2888
     - 3888:3888
  kafka:
    image: quay.io/debezium/kafka:${DEBEZIUM_VERSION}
    ports:
     - 9092:9092
    links:
     - zookeeper
    environment:
     - ZOOKEEPER_CONNECT=zookeeper:2181
  kafka-ui:
    image: provectuslabs/kafka-ui:latest
    ports:
      - 8080:8080
    environment:
     - KAFKA_CLUSTERS_0_NAME=local-kafka
     - KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS=kafka:9092
     - KAFKA_CLUSTERS_0_ZOOKEEPER=zookeeper:2181
  mysql:
    image: quay.io/debezium/example-mysql:${DEBEZIUM_VERSION}
    ports:
     - 3306:3306
    environment:
     - MYSQL_ROOT_PASSWORD=debezium
     - MYSQL_USER=mysqluser
     - MYSQL_PASSWORD=mysqlpw
     - MYSQL_DATABASE=cdc-db
  connect:
    image: quay.io/debezium/connect:${DEBEZIUM_VERSION}
    ports:
     - 8083:8083
    links:
     - kafka
     - mysql
    environment:
     - BOOTSTRAP_SERVERS=kafka:9092
     - GROUP_ID=1
     - CONFIG_STORAGE_TOPIC=my_connect_configs
     - OFFSET_STORAGE_TOPIC=my_connect_offsets
     - STATUS_STORAGE_TOPIC=my_connect_statuses
```

Trên đây là file docker compose mình khai báo tạo các container kafka, kafka-ui mysql và debezium connect. Sau khi tạo file xong thì cd tới folder rồi chạy lệnh

```apache
sudo DEBEZIUM_VERSION=1.9 docker-compose up -d
```

Sau khi chạy lệnh thì chúng ta được các container đang run như ảnh dưới đây

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1711985669529/d7a25d94-4ffb-48af-afe5-387b75379ac7.png align="center")

```abap
curl --location 'http://localhost:8083/connectors/' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data '{
    "name": "cdc-connector",
    "config": {
        "connector.class": "io.debezium.connector.mysql.MySqlConnector",
        "database.hostname": "mysql",
        "database.port": "3306",
        "database.user": "root",
        "database.password": "debezium",
        "tasks.max": "1",
        "database.server.name": "server_name",
        "database.whitelist": "cdc-db",
        "table.include.list": "cdc-db.users",
        "database.history.kafka.bootstrap.servers": "kafka:9092",
        "database.history.kafka.topic": "history.users"
    }
}'
```

CURL để tạo connector của Debezium. Mình khai báo các connect tới database mysql, danh sách database và bảng cần capture. Cụ thể ở đây mình capture **database cdc-db** và bảng **users**. Message được bắn vào topic **server\_name.cdc-db.users**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1711985914237/683b4c79-17ce-4d7a-8ac9-f7e459dd42f3.png align="center")

Ở đây mình đã test rồi nên có 14 message. Giờ mình thử vào database và insert 1 record. Sau khi, insert xong thì message thay đổi dữ liệu được bắn vào topic trên Kafka

```abap
INSERT INTO `users` (`name`) VALUES
('Tuan test cdc');
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1711986053467/38a53b7f-4d9b-4b37-8daa-8be87388beec.png align="center")

```yaml
{
	"schema": {
		"type": "struct",
		"fields": [
			{
				"type": "struct",
				"fields": [
					{
						"type": "int32",
						"optional": false,
						"field": "id"
					},
					{
						"type": "string",
						"optional": true,
						"field": "name"
					}
				],
				"optional": true,
				"name": "server_name.cdc_db.users.Value",
				"field": "before"
			},
			{
				"type": "struct",
				"fields": [
					{
						"type": "int32",
						"optional": false,
						"field": "id"
					},
					{
						"type": "string",
						"optional": true,
						"field": "name"
					}
				],
				"optional": true,
				"name": "server_name.cdc_db.users.Value",
				"field": "after"
			},
			{
				"type": "struct",
				"fields": [
					{
						"type": "string",
						"optional": false,
						"field": "version"
					},
					{
						"type": "string",
						"optional": false,
						"field": "connector"
					},
					{
						"type": "string",
						"optional": false,
						"field": "name"
					},
					{
						"type": "int64",
						"optional": false,
						"field": "ts_ms"
					},
					{
						"type": "string",
						"optional": true,
						"name": "io.debezium.data.Enum",
						"version": 1,
						"parameters": {
							"allowed": "true,last,false,incremental"
						},
						"default": "false",
						"field": "snapshot"
					},
					{
						"type": "string",
						"optional": false,
						"field": "db"
					},
					{
						"type": "string",
						"optional": true,
						"field": "sequence"
					},
					{
						"type": "string",
						"optional": true,
						"field": "table"
					},
					{
						"type": "int64",
						"optional": false,
						"field": "server_id"
					},
					{
						"type": "string",
						"optional": true,
						"field": "gtid"
					},
					{
						"type": "string",
						"optional": false,
						"field": "file"
					},
					{
						"type": "int64",
						"optional": false,
						"field": "pos"
					},
					{
						"type": "int32",
						"optional": false,
						"field": "row"
					},
					{
						"type": "int64",
						"optional": true,
						"field": "thread"
					},
					{
						"type": "string",
						"optional": true,
						"field": "query"
					}
				],
				"optional": false,
				"name": "io.debezium.connector.mysql.Source",
				"field": "source"
			},
			{
				"type": "string",
				"optional": false,
				"field": "op"
			},
			{
				"type": "int64",
				"optional": true,
				"field": "ts_ms"
			},
			{
				"type": "struct",
				"fields": [
					{
						"type": "string",
						"optional": false,
						"field": "id"
					},
					{
						"type": "int64",
						"optional": false,
						"field": "total_order"
					},
					{
						"type": "int64",
						"optional": false,
						"field": "data_collection_order"
					}
				],
				"optional": true,
				"field": "transaction"
			}
		],
		"optional": false,
		"name": "server_name.cdc_db.users.Envelope"
	},
	"payload": {
		"before": null,
		"after": {
			"id": 9,
			"name": "Tuan test cdc"
		},
		"source": {
			"version": "1.9.7.Final",
			"connector": "mysql",
			"name": "server_name",
			"ts_ms": 1711985987000,
			"snapshot": "false",
			"db": "cdc-db",
			"sequence": null,
			"table": "users",
			"server_id": 223344,
			"gtid": null,
			"file": "mysql-bin.000004",
			"pos": 373,
			"row": 0,
			"thread": 11,
			"query": null
		},
		"op": "c",
		"ts_ms": 1711985987219,
		"transaction": null
	}
}
```

Ở đây, chúng ta cần chú ý tới cục data **payload** có **before, after**. **Before** là data trước đó và **after** là data sau khi thay đổi. Ở đây **op = "c"** tức là hành động create -&gt; before = null và after là data record ở bảng **users.** Tương tự, dưới đây là message khi mình update record trong bảng users

```yaml
{
	"schema": {
		"type": "struct",
		"fields": [
			{
				"type": "struct",
				"fields": [
					{
						"type": "int32",
						"optional": false,
						"field": "id"
					},
					{
						"type": "string",
						"optional": true,
						"field": "name"
					}
				],
				"optional": true,
				"name": "server_name.cdc_db.users.Value",
				"field": "before"
			},
			{
				"type": "struct",
				"fields": [
					{
						"type": "int32",
						"optional": false,
						"field": "id"
					},
					{
						"type": "string",
						"optional": true,
						"field": "name"
					}
				],
				"optional": true,
				"name": "server_name.cdc_db.users.Value",
				"field": "after"
			},
			{
				"type": "struct",
				"fields": [
					{
						"type": "string",
						"optional": false,
						"field": "version"
					},
					{
						"type": "string",
						"optional": false,
						"field": "connector"
					},
					{
						"type": "string",
						"optional": false,
						"field": "name"
					},
					{
						"type": "int64",
						"optional": false,
						"field": "ts_ms"
					},
					{
						"type": "string",
						"optional": true,
						"name": "io.debezium.data.Enum",
						"version": 1,
						"parameters": {
							"allowed": "true,last,false,incremental"
						},
						"default": "false",
						"field": "snapshot"
					},
					{
						"type": "string",
						"optional": false,
						"field": "db"
					},
					{
						"type": "string",
						"optional": true,
						"field": "sequence"
					},
					{
						"type": "string",
						"optional": true,
						"field": "table"
					},
					{
						"type": "int64",
						"optional": false,
						"field": "server_id"
					},
					{
						"type": "string",
						"optional": true,
						"field": "gtid"
					},
					{
						"type": "string",
						"optional": false,
						"field": "file"
					},
					{
						"type": "int64",
						"optional": false,
						"field": "pos"
					},
					{
						"type": "int32",
						"optional": false,
						"field": "row"
					},
					{
						"type": "int64",
						"optional": true,
						"field": "thread"
					},
					{
						"type": "string",
						"optional": true,
						"field": "query"
					}
				],
				"optional": false,
				"name": "io.debezium.connector.mysql.Source",
				"field": "source"
			},
			{
				"type": "string",
				"optional": false,
				"field": "op"
			},
			{
				"type": "int64",
				"optional": true,
				"field": "ts_ms"
			},
			{
				"type": "struct",
				"fields": [
					{
						"type": "string",
						"optional": false,
						"field": "id"
					},
					{
						"type": "int64",
						"optional": false,
						"field": "total_order"
					},
					{
						"type": "int64",
						"optional": false,
						"field": "data_collection_order"
					}
				],
				"optional": true,
				"field": "transaction"
			}
		],
		"optional": false,
		"name": "server_name.cdc_db.users.Envelope"
	},
	"payload": {
		"before": {
			"id": 9,
			"name": "Tuan test cdc"
		},
		"after": {
			"id": 9,
			"name": "Tuan test update cdc"
		},
		"source": {
			"version": "1.9.7.Final",
			"connector": "mysql",
			"name": "server_name",
			"ts_ms": 1711986230000,
			"snapshot": "false",
			"db": "cdc-db",
			"sequence": null,
			"table": "users",
			"server_id": 223344,
			"gtid": null,
			"file": "mysql-bin.000004",
			"pos": 684,
			"row": 0,
			"thread": 11,
			"query": null
		},
		"op": "u",
		"ts_ms": 1711986230583,
		"transaction": null
	}
}
```

**The end! Thanks for reading**