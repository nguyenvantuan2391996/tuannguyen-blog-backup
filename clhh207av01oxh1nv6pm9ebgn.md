---
title: "[ Design Patterns ] - Observer pattern with Golang"
seoTitle: "[ Design Patterns ] - Observer pattern with Golang"
seoDescription: "Observer pattern giúp bạn định nghĩa một cơ chế đăng ký để thông báo tới nhiều đối tượng về bất kì sự kiện xảy ra cái mà đối tượng đó đang quan sát"
datePublished: Wed May 10 2023 02:00:39 GMT+0000 (Coordinated Universal Time)
cuid: clhh207av01oxh1nv6pm9ebgn
slug: design-patterns-observer-pattern-with-golang
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1683653939782/eceb06fd-7948-438a-845b-476af80e68a0.png
tags: design-patterns, observer-pattern

---

**Hà Nội, Thứ Tư 10/05/2023**

Một vài lời gửi gắm thời gian...

<center><iframe width="560" height="315" src="https://www.youtube.com/embed/DOyzdJk7_1w"></iframe></center>

Cơn mưa ngang qua mang em đi xa
Cơn mưa ngang qua khiến em nhạt nhòa...

Nay ngồi nghe bài hát cũ của Sơn Tùng, mình thấy hoài niệm về nhiều thứ trong quá khứ và có vài thứ bồi hồi thoáng chút buồn trong đó. Cùng thời với bài hát này, mình cũng tình cờ đọc được câu chuyên trên Voz ["[Kể lại 1 câu chuyện] Đặt tên là "CƠN MƯA NGANG QUA"](https://vozforum.org/threads/ke-lai-1-cau-chuyen-dat-ten-la-con-mua-ngang-qua.51597/). Chuyện rất hay mọi người đọc nhé 😊😊😊

# I. Observer Pattern

*Observer pattern là một kiểu behavioral design pattern. Pattern này giúp bạn định nghĩa một cơ chế đăng ký để thông báo tới nhiều đối tượng về bất kì sự kiện xảy ra cái mà đối tượng đó đang quan sát.*

![The structure of the Observer pattern](https://refactoring.guru/images/patterns/diagrams/observer/structure.png align="center")

Reference: [Observer Pattern](https://refactoring.guru/design-patterns/observer)

# II. Implement

## 1\. Vấn đề

![The problem](https://refactoring.guru/images/patterns/content/observer/observer-comic-1-en.png align="center")

Hãy tưởng tượng, chúng ta có 2 đối tượng: khách hàng và cửa hàng bán điện thoại Apple Store.

Khách hàng thì là một big fan của Apple và anh ấy sẽ luôn luôn muốn có được mẫu điện thoại mới nhất của hãng khi mà Apple cho ra mắt điện thoại mới. Vì vậy, anh ấy sẽ phải ghé thăm Apple Store hàng ngày để hỏi nhân viên xem sản phẩm mới nhất của Apple mới ra đã được chuyển về cửa hàng chưa? Nhưng có thể sản phẩm mới nhất vẫn chưa về tới cửa hàng cho nên anh ấy sẽ phải dành rất nhiều ngày để tới cửa hàng để kiểm tra.

Hmm, như trên hình vẽ để giải quyết vấn đề phải quay tới, quay lui cửa hàng thì cửa hàng sẽ gửi thông báo email cho tất cả các khách hàng trong danh sách của Store đang có khi sản phẩm mới nhất về. Điều này sẽ giúp anh chàng kia về việc dành thời gian để kiểm tra. Tuy nhiên, hiện tại có những khách hàng chỉ thích điện thoại Android vì vậy khi nhận được thông báo có iPhone mới họ đều rất giận giữ và cảm thấy không happy.

## 2\. Cách giải quyết vấn đề

![The problem](https://refactoring.guru/images/patterns/content/observer/observer.png align="center")

Vậy để giải quyết triệt để cho anh chàng kia và các tập khách hàng không thích iPhone cửa hàng đã mở form đăng ký đặt mua iPhone mới nhất. Khi mẫu iPhone này về cửa hàng thì sẽ thông báo cho những người đã đăng ký đặt mua. Ở trường hợp này, chúng ta sẽ áp dụng Observer Pattern để xử lý bài toán.

## 3\. Implement Observer Pattern

### 3.1. Interface Publisher

**ipublisher.go**: Thành phần khai báo interface các hành vi của publisher.

```golang
package publisher

import "design-pattern-golang-example/observer_pattern/subscriber"

type IPublisher interface {
	Subscribe(s subscriber.ISubscriber)
	UnSubscribe(s subscriber.ISubscriber)
	NotifySubscribes()
}
```

### 3.2. Concrete Publisher

**apple_store.go**: Thành phần publisher xử lý logic để thực hiện các hành vi được khai báo ở interface publisher. Ở đây mình khai báo 1 publisher đại diện cho cửa hàng Apple Store.

```golang
package concrete_publisher

import (
	"fmt"
	"time"

	"design-pattern-golang-example/observer_pattern/publisher"
	"design-pattern-golang-example/observer_pattern/subscriber"
)

type AppleStore struct {
	Subscribers []subscriber.ISubscriber
	Name        string
}

func NewAppleStore(subscribers []subscriber.ISubscriber, name string) publisher.IPublisher {
	return &AppleStore{
		Subscribers: subscribers,
		Name:        name,
	}
}

func (as *AppleStore) Subscribe(s subscriber.ISubscriber) {
	as.Subscribers = append(as.Subscribers, s)
	time.Sleep(1 * time.Second)
	fmt.Println(s.GetEmail() + " was subscribed...")
}

func (as *AppleStore) UnSubscribe(s subscriber.ISubscriber) {
	subscribersUpdate := make([]subscriber.ISubscriber, 0)
	for _, sub := range as.Subscribers {
		if sub.GetEmail() != s.GetEmail() {
			subscribersUpdate = append(subscribersUpdate, sub)
		}
	}

	time.Sleep(1 * time.Second)
	fmt.Println(s.GetEmail() + " was unsubscribed...")

	as.Subscribers = subscribersUpdate
}

func (as *AppleStore) NotifySubscribes() {
	for _, sub := range as.Subscribers {
		sub.SendEmail("We have had a new model of the iPhone")
		time.Sleep(1 * time.Second)
	}
}
```

### 3.3. Interface Subscriber

**isubscriber.go**: Thành phần khai báo interface các hành vi của subscribers.

```golang
package subscriber

// ISubscriber this is the observer
type ISubscriber interface {
	SendEmail(notification string)
	GetEmail() string
}
```

### 3.4. Concrete Subscriber

**customer_subscriber.go**: Thành phần subscriber xử lý logic để thực hiện các hành vi được khai báo ở interface subscriber. Ở đây mình khai báo 1 subscriber đại diện cho đối tượng anh chàng big fan của iPhone là customer.

```golang
package concrete_subscriber

import (
	"fmt"

	"design-pattern-golang-example/observer_pattern/subscriber"
)

type Customer struct {
	Email string
}

func NewCustomer(email string) subscriber.ISubscriber {
	return &Customer{Email: email}
}

func (c *Customer) SendEmail(notification string) {
	content := fmt.Sprintf(`Sending the notification for %s with content "%s"`, c.GetEmail(), notification)
	fmt.Println(content)
}

func (c *Customer) GetEmail() string {
	return c.Email
}
```

### 3.5. Main Function

**main.go**: Hàm chạy chương trình

```golang
package main

import (
	"time"

	"design-pattern-golang-example/observer_pattern/publisher/concrete_publisher"
	"design-pattern-golang-example/observer_pattern/subscriber"
	"design-pattern-golang-example/observer_pattern/subscriber/concrete_subscriber"
)

func main() {
	// init publisher
	appleStore := concrete_publisher.NewAppleStore([]subscriber.ISubscriber{}, "apple store")

	// init observer
	subscriberOne := concrete_subscriber.NewCustomer("subscribe-one@gmail.com")
	subscriberTwo := concrete_subscriber.NewCustomer("subscribe-two@gmail.com")

	// the observer subscribes publisher
	appleStore.Subscribe(subscriberOne)
	appleStore.Subscribe(subscriberTwo)

	// send notification
	time.Sleep(3 * time.Second)
	appleStore.NotifySubscribes()

	// the observer unsubscribes publisher
	appleStore.UnSubscribe(subscriberTwo)

	// send notification
	time.Sleep(3 * time.Second)
	appleStore.NotifySubscribes()
}
```

**Kết quả**

```golang
subscribe-one@gmail.com was subscribed...
subscribe-two@gmail.com was subscribed...
Sending the notification for subscribe-one@gmail.com with content "We have had a new model of the iPhone"
Sending the notification for subscribe-two@gmail.com with content "We have had a new model of the iPhone"
subscribe-two@gmail.com was unsubscribed...
Sending the notification for subscribe-one@gmail.com with content "We have had a new model of the iPhone"

Process finished with the exit code 0
```

# III. Source code

* Source code factory pattern: [Example with Golang](https://github.com/nguyenvantuan2391996/design-pattern-golang-example/tree/master/observer_pattern)
    
* Source almost popular design patterns: [Design patterns](https://github.com/nguyenvantuan2391996/design-pattern-golang-example)