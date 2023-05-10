---
title: "[ Design Patterns ] - Observer pattern with Golang"
seoTitle: "[ Design Patterns ] - Observer pattern with Golang"
seoDescription: "Observer pattern defines a subscription mechanism to notify multiple objects about any events that happen to the object they’re observing."
datePublished: Wed May 10 2023 03:42:53 GMT+0000 (Coordinated Universal Time)
cuid: clhh5no50000108mqbzph3hak
slug: design-patterns-observer-pattern-with-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1683690051300/3954e5ba-3c03-46e7-a70d-3559bcb58718.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1683690099234/c8fa87d8-3b32-49d3-81c0-4a3630424507.png
tags: design-patterns, observer-pattern

---

**Ha Noi, Wed 10/05/2023**

At the moment, a little speech to give to the present...

<center><iframe width="560" height="315" src="https://www.youtube.com/embed/DOyzdJk7_1w"></iframe></center>

Cơn mưa ngang qua mang em đi xa
Cơn mưa ngang qua khiến em nhạt nhòa...

Today, I listened to Son Tung's old song. I missed something in the past. At the same time as this song, I read a story on Voz that's name is ["[Kể lại 1 câu chuyện] Đặt tên là "CƠN MƯA NGANG QUA"](https://vozforum.org/threads/ke-lai-1-cau-chuyen-dat-ten-la-con-mua-ngang-qua.51597/). This story is very perfect, let's read it! 😊😊😊

# I. Observer Pattern

*Observer is a behavioral design pattern that lets you define a subscription mechanism to notify multiple objects about any events that happen to the object they’re observing.*

![The structure of the Observer pattern](https://refactoring.guru/images/patterns/diagrams/observer/structure.png align="center")

Reference: [Observer Pattern](https://refactoring.guru/design-patterns/observer)

# II. Implement

## 1\. The problem

![The problem](https://refactoring.guru/images/patterns/content/observer/observer-comic-1-en.png align="center")

Imagine that we have two objects: customers and the Apple Store.

The customer is a big fan of Apple, and he always wants to have the newest brand model whenever Apple introduces a new brand model. So, he must go to the Apple Store every day and ask the Apple Store's employees about the new brand model of Apple. Maybe it is still not available in the Apple Store, so he must spend lots of days going to the store.

Hmm, as in the image above, to resolve his problem, the store will notify and email all customers whenever the new brand model is available in the store. Nicely, the solution resolved his problem about spending time going to the store. But some customers like Android phones, so whenever they receive a notification about the new brand model from Apple, they are very angry and not happy.

## 2\. The solution

![The problem](https://refactoring.guru/images/patterns/content/observer/observer.png align="center")

So, to resolve this problem for him and some other customers who like Android phones, the store created a registration form to buy the new brand model. Whenever the product is available, the store will send a notification to all customers registered to buy iPhones.

So, to resolve the problem, we will use the Observer Pattern.

## 3\. Implement Observer Pattern

### 3.1. Interface Publisher

**ipublisher.go**: This is the action interface of the publisher.

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

**apple_store.go**: The publisher will handle and implement the logic for all actions that define by the interface publisher. Here, I define a publisher as Apple Store.

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

**isubscriber.go**: This is the action interface of the subscribers.

```golang
package subscriber

// ISubscriber this is the observer
type ISubscriber interface {
	SendEmail(notification string)
	GetEmail() string
}
```

### 3.4. Concrete Subscriber

**customer_subscriber.go**: The subscriber will handle and implement the logic for all actions that define by the interface subscriber. Here, I define a subscriber as a customer.

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

**Result**

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