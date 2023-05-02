---
title: "[ Design Patterns ] - Decorator pattern with Golang"
seoTitle: "[ Design Patterns ] - Decorator pattern with Golang"
seoDescription: "Decorator is a structural design pattern that lets you attach new behaviors to objects by placing these objects inside special wrapper objects that contain"
datePublished: Mon Mar 13 2023 02:31:39 GMT+0000 (Coordinated Universal Time)
cuid: clf67knyg02z0l4nv0f9p90jf
slug: design-patterns-decorator-pattern-with-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1678631296185/71a55dfc-cb34-4b54-ab0f-1c8b2097b3db.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1678635067531/434a96e1-e526-4ce9-a6f2-3c39a066c92b.png
tags: patterns, golang, decorators, decorator-design-pattern

---

**Ha Noi, on Thursday 12/03/2023**

At the moment, a little speech to give to the present...

<center><iframe width="560" height="315" src="https://www.youtube.com/embed/eP-F668jMN0"></iframe></center>

The cafe, the cigarette, and some rainy days...

Listening to this song, I miss the memories of when we were young, stupid children, and we still loved. When the cafe has no sugar, I often drink something that is sweet. Now, it has a pale taste, like water, or if it has a taste, then the taste is the memories or the regret of all the great old days.

# I. Decorator pattern

\*Decorator is a structural design pattern that lets you attach new behaviors to objects by placing these objects inside special wrapper objects that contain the behaviors.

Using decorators you can wrap objects countless number of times since both target objects and decorators follow the same interface. The resulting object will get a stacking behavior of all wrappers.\*

Reference: [Decorator pattern](https://refactoring.guru/design-patterns/decorator)

# II. Implement

![The solution with the Decorator pattern](https://refactoring.guru/images/patterns/diagrams/decorator/solution2.png?id=cbee4a27080ce3a0bf773482613e1347 align="center")

## 1\. The problem

We need to build a system for the notification when the user has the action: create an account, update an account, or delete an account,... and for each action, we will send the notification to some platforms, eg: Slack, SMS, Facebook,...

Example:

* When a user creates an account. The system will send the notification to the user's phone by SMS.
    
* When a user updates the user's account, eg: password, and name,... then we will send the notification to your SMS, your Facebook, and our Slack.
    
* When a user deletes the user's account then we will send the notification to your SMS, our Slack.
    

**Note:** *The system will have a lot of actions and platforms to receive notifications. For each action, we must send a lot of platforms.*

## 2\. Problem solving

![Structure of the library after creating class combinations](https://refactoring.guru/images/patterns/diagrams/decorator/problem3.png?id=f3b3e7a107d870871f2c3167adcb7ccb align="center")

When we have a new action and will send the notification to some platforms. We tried to address that problem by creating special subclasses which combined several notification methods within one class. However, it quickly became apparent that this approach would bloat the code immensely, not only the library code but the client code as well.

So, to resolve the problem, we will use the decorator design pattern.

## 3\. Implement decorator design pattern

### 3.1. Component

```golang
package component

type INotifier interface {
	SendNotification(msg string)
}
```

### 3.2. Concrete component

This is a folder that handles common logic, eg: getting common information from the database, config, or some common action,...

```golang
package concrete_component

import (
	"fmt"
	"time"
)

type Notifier struct {
}

func (n *Notifier) SendNotification(msg string) {
	fmt.Println("Handle process logic common...")
	time.Sleep(2 * time.Second)
}
```

### 3.3. Concrete decorator

This is a folder that handles and implements sending the notification to each platform, eg: Slack notification, SMS notification, and Facebook notification,...

```golang
package concrete_decorator

import (
	"fmt"
	"time"

	"design-pattern-golang-example/decorator-pattern/component"
)

type FacebookNotifier struct {
	Notifier component.INotifier
}

func (f *FacebookNotifier) SendNotification(msg string) {
	f.Notifier.SendNotification(msg)
	fmt.Println(fmt.Sprintf("Send the notification to Facebook with message %s", msg))
	time.Sleep(2 * time.Second)
	fmt.Println("Done")
}
```

```golang
package concrete_decorator

import (
	"fmt"
	"time"

	"design-pattern-golang-example/decorator-pattern/component"
)

type SlackNotifier struct {
	Notifier component.INotifier
}

func (s *SlackNotifier) SendNotification(msg string) {
	s.Notifier.SendNotification(msg)
	fmt.Println(fmt.Sprintf("Send the notification to Slack with message %s", msg))
	time.Sleep(2 * time.Second)
	fmt.Println("Done")
}
```

```golang
package concrete_decorator

import (
	"fmt"
	"time"

	"design-pattern-golang-example/decorator-pattern/component"
)

type SmsNotifier struct {
	Notifier component.INotifier
}

func (s *SmsNotifier) SendNotification(msg string) {
	s.Notifier.SendNotification(msg)
	fmt.Println(fmt.Sprintf("Send the notification to SMS with message %s", msg))
	time.Sleep(2 * time.Second)
	fmt.Println("Done")
}
```

### 3.4. Main function and the result

```golang
package main

import (
	"fmt"
	"time"

	concreteComponent "design-pattern-golang-example/decorator-pattern/concrete-component"
	concreteDecorator "design-pattern-golang-example/decorator-pattern/concrete-decorator"
)

func main() {
	// If you create your account then we will send the notification to your SMS
	CreateAccount("Tuan Nguyen")

	// If you update your account, eg: password, name,... then we will send the notification to your SMS, your Facebook and our Slack
	UpdateAccount("Tuan Nguyen")

	// If you delete your account then we will send the notification to your SMS, our Slack
	DeleteAccount("Tuan Nguyen")
}

func CreateAccount(name string) {
	fmt.Println(fmt.Sprintf("Create the account with name %s is done.", name))
	time.Sleep(2 * time.Second)

	fmt.Println("Begin to send the notification...")
	time.Sleep(2 * time.Second)

	notifier := &concreteComponent.Notifier{}

	// add sms notification
	sms := &concreteDecorator.SmsNotifier{
		Notifier: notifier,
	}

	// send the notification
	sms.SendNotification("create account")
}

func UpdateAccount(name string) {
	fmt.Println(fmt.Sprintf("Update the account with name %s is done.", name))
	time.Sleep(2 * time.Second)

	fmt.Println("Begin to send the notification...")
	time.Sleep(2 * time.Second)

	notifier := &concreteComponent.Notifier{}

	// add sms notification
	sms := &concreteDecorator.SmsNotifier{Notifier: notifier}

	// add facebook notification
	facebookSMS := &concreteDecorator.FacebookNotifier{Notifier: sms}

	// add Slack notification
	slackFacebookSMS := &concreteDecorator.SlackNotifier{Notifier: facebookSMS}

	// send the notification
	slackFacebookSMS.SendNotification("update account")
}

func DeleteAccount(name string) {
	fmt.Println(fmt.Sprintf("Delete the account with name %s is done.", name))
	time.Sleep(2 * time.Second)

	fmt.Println("Begin to send the notification...")
	time.Sleep(2 * time.Second)

	notifier := &concreteComponent.Notifier{}

	// add sms notification
	sms := &concreteDecorator.SmsNotifier{Notifier: notifier}

	// add Slack notification
	slackSMS := &concreteDecorator.SlackNotifier{Notifier: sms}

	// send the notification
	slackSMS.SendNotification("delete account")
}
```

**The result:**

*Create an account*

```golang
Create the account with name Tuan Nguyen is done.
Begin to send the notification...
Handle process logic common...
Send the notification to SMS with message create account
Done
```

*Update an account*

```golang
Begin to send the notification...
Handle process logic common...
Send the notification to SMS with message update account
Done
Send the notification to Facebook with message update account
Done
Send the notification to Slack with message update account
Done
```

*Delete an account*

```golang
Delete the account with name Tuan Nguyen is done.
Begin to send the notification...
Handle process logic common...
Send the notification to SMS with message delete account
Done
Send the notification to Slack with message delete account
Done
```

# III. Source code

* Source code decorator pattern: [Example with Golang](https://github.com/nguyenvantuan2391996/design-pattern-golang-example/tree/master/decorator_pattern)
    
* Source almost popular design patterns: [Design patterns](https://github.com/nguyenvantuan2391996/design-pattern-golang-example)