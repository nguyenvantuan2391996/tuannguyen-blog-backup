---
title: "[ Design Patterns ] - Builder pattern with Golang"
seoTitle: "[ Design Patterns ] - Builder pattern with Golang"
seoDescription: "My team must build one application for selling offline fruits to one restaurant. The customer will select fruits and add them to their cart"
datePublished: Thu Nov 24 2022 02:00:45 GMT+0000 (Coordinated Universal Time)
cuid: claufh2px00smhunv8r5p7e1w
slug: design-patterns-builder-pattern-with-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1669204519015/nsOv4CdnY.png
tags: design-patterns, go, golang, developer, builder-pattern

---

**Ha Noi, on Thursday 24/11/2022**

At the moment, a little speech to give to the present...

![ha-noi.jpeg](https://cdn.hashnode.com/res/hashnode/image/upload/v1669212284101/M5RWVJEGV.jpeg align="left")

*Hà Nội mùa này vắng những cơn mưa  
Cái rét đầu đông khăn em hiu hiu gió lạnh  
Hoa sữa thôi rơi ta bên nhau một chiều tan lớp  
Đường Cổ Ngư xưa chầm chậm bước ta về...😥😥😥*

Ha Noi is approaching winter, when there will be no rain. I'm sitting in my room and writing this post. Today's temperature in Ha Noi is 25°C, cloudy, and starting to get colder than previous days. The weather is appropriate for lonely people 😉😉😉

The news about layoffs in big tech is not good for everyone around the world, and it was beginning to affect employees in Vietnam. I hope that by 2023, everything will be back to normal. Best wishes to everyone! 😍😍😍

# I. Builder pattern

*The Builder pattern is used when the desired product is complex and requires multiple steps to complete. In this case, several construction methods would be simpler than a single monstrous constructor. The potential problem with the multistage building process is that a partially built and unstable product may be exposed to the client. The Builder pattern keeps the product private until it’s fully built.*

Reference : [Builder pattern](https://refactoring.guru/design-patterns/builder/go/example)

# II. Components of the builder pattern

![uml-of-builedr.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1669214955214/Rvx7uMTpu.jpg align="left")

1. **Product** : These are the resultant objects. Products will have different results. It will be determined by how the object is constructed.
    
2. **Concreate Builder** : Provides implementation for Builder. It is an object able to construct other objects. Constructs and assembles parts to build the objects.
    
3. **Builder** : Abstract interface for creating objects (product) and getting concreate builder.
    
4. **Director** : The class will call order-by-step to build objects.
    

# III. Implement

## 1\. The problem

*My team must build one application for selling offline fruits to one restaurant. The customer will select fruits and add them to their cart after going to the counter and requiring payment. The employee of the restaurant will scan the QR code of the fruit, and your application will display information about the fruit's price, amount, and discount.  
The application must calculate the amount of money that the customer must pay for the restaurant according to the rules below.*

**Example rules :**

| Name | Amount | Price | Discount |
| --- | --- | --- | --- |
| Orange | More than 10 | Price = Price - $5  
**eg :** each fruit's price = $49.99 =&gt; Price = $49.99 - $5 = 44.99$ | Discount = Discount + 2%  
**eg :** each fruit's discount = 10% =&gt; Discount = 10 + 2 = 12% |
| Orange | Less than 10 | fruit's price | fruit's discount |
| Apple | More than 10 | Price = Price - $10  
**eg :** each fruit's price = $49.99 =&gt; Price = $49.99 - $10 = 39.99$ | Discount = Discount + 5%  
**eg :** each fruit's discount = 10% =&gt; Discount = 10 + 5 = 15% |
| Apple | Less than 10 | fruit's price | fruit's discount |

**Note :** \*The restaurant will have far more fruit, and the rules will be far more complicated. \*

## 2\. Problem solving

I think you will code as in the example below. We will use if-else to check the fruit's name and process logic for it in each case.

```golang
type Output struct {
	Money float64
}

func GetMoney(name string, amount int64, price, discount float64) Output {
	money := float64(0)
	if name == "orange" {
		if amount > 10 {
			price -= 5
			discount += 2
			money = price * (discount - discount/100) * float64(amount)
		}
		// do anything logical
	} else if name == "apple" {
		if amount > 10 {
			price -= 10
			discount += 5
			money = price * (discount - discount/100) * float64(amount)
		}
		// do anything logical
	}

	return Output{Money: money}
}
```

Sure, it will still work, but when restaurants need to calculate the price, discount, and scale the sale of fruit, our codebase becomes too large, and updating the codebase becomes difficult.

When the restaurant adds one new fruit, updates, or adds one new rule, we must hold a meeting to confirm and update the codebase in a unified manner.

So, to resolve the problem, we will use the builder design pattern.

## 3\. Implement builder design pattern

### 3.1. ifruit\_builder.go : Builder interface

```golang
package builder

import (
	"design-pattern-golang-example/builder-pattern/concreate_builder"
	"design-pattern-golang-example/builder-pattern/constant"
	"design-pattern-golang-example/builder-pattern/product"
)

type IFruitBuilder interface {
	SetPrice(in *product.InfoInput)
	SetDiscount(in *product.InfoInput)
	SetMoneyPayment(in *product.InfoInput)
	ToOutputMoney() *product.InfoOutput
}

func GetFruitBuilder(fruitName string) IFruitBuilder {
	switch fruitName {
	case constant.Orange:
		return concreate_builder.NewOrangeBuilder()
	case constant.Apple:
		return concreate_builder.NewAppleBuilder()
	}
	return nil
}
```

### 3.2. orange\_builder.go: Concreate builder for orange

```golang
package concreate_builder

import "design-pattern-golang-example/builder-pattern/product"

type OrangeBuilder struct {
	Price        float64
	Discount     float64
	MoneyPayment float64
}

func NewOrangeBuilder() *OrangeBuilder {
	return &OrangeBuilder{}
}

func (o *OrangeBuilder) SetPrice(in *product.InfoInput) {
	if in.Amount > 10 {
		o.Price = in.Price - 5
	}
}

func (o *OrangeBuilder) SetDiscount(in *product.InfoInput) {
	if in.Amount > 10 {
		o.Discount = in.Discount + 2
	}
}

func (o *OrangeBuilder) SetMoneyPayment(in *product.InfoInput) {
	o.MoneyPayment = o.Price * (100 - o.Discount/100) * float64(in.Amount)
}

func (o *OrangeBuilder) ToOutputMoney() *product.InfoOutput {
	return &product.InfoOutput{
		MoneyPayment: o.MoneyPayment,
	}
}
```

### 3.3. apple\_builder.go: Concreate builder for apple

```golang
package concreate_builder

import "design-pattern-golang-example/builder-pattern/product"

type AppleBuilder struct {
	Price        float64
	Discount     float64
	MoneyPayment float64
}

func NewAppleBuilder() *AppleBuilder {
	return &AppleBuilder{}
}

func (o *AppleBuilder) SetPrice(in *product.InfoInput) {
	if in.Amount > 10 {
		o.Price = in.Price - 10
	}
}

func (o *AppleBuilder) SetDiscount(in *product.InfoInput) {
	if in.Amount > 10 {
		o.Discount = in.Discount + 5
	}
}

func (o *AppleBuilder) SetMoneyPayment(in *product.InfoInput) {
	o.MoneyPayment = o.Price * (100 - o.Discount/100) * float64(in.Amount)
}

func (o *AppleBuilder) ToOutputMoney() *product.InfoOutput {
	return &product.InfoOutput{
		MoneyPayment: o.MoneyPayment,
	}
}
```

### 3.4. fruit.go : Product

```golang
package product

type InfoInput struct {
	Price    float64
	Discount float64
	Amount   int64
}

type InfoOutput struct {
	MoneyPayment float64
}
```

### 3.5. director.go: Director

```golang
package director

import (
	"design-pattern-golang-example/builder-pattern/builder"
	"design-pattern-golang-example/builder-pattern/product"
)

type Director struct {
	FruitBuilder builder.IFruitBuilder
}

func NewDirector(f builder.IFruitBuilder) *Director {
	return &Director{
		FruitBuilder: f,
	}
}

func (d *Director) BuildOutput(in *product.InfoInput) *product.InfoOutput {
	d.FruitBuilder.SetPrice(in)
	d.FruitBuilder.SetDiscount(in)
	d.FruitBuilder.SetMoneyPayment(in)
	return d.FruitBuilder.ToOutputMoney()
}
```

### 3.6. main.go

```golang
package main

import (
	"fmt"

	"design-pattern-golang-example/builder-pattern/builder"
	"design-pattern-golang-example/builder-pattern/constant"
	"design-pattern-golang-example/builder-pattern/director"
	"design-pattern-golang-example/builder-pattern/product"
)

func main() {
	in := &product.InfoInput{
		Price:    100,
		Discount: 10,
		Amount:   15,
	}

	builderApple := builder.GetFruitBuilder(constant.Apple)
	directorApple := director.NewDirector(builderApple)
	outApple := directorApple.BuildOutput(in)

	builderOrange := builder.GetFruitBuilder(constant.Orange)
	directorOrange := director.NewDirector(builderOrange)
	outOrange := directorOrange.BuildOutput(in)

	fmt.Printf("Pay for orange : $%v\n", outOrange.MoneyPayment)
	fmt.Printf("Pay for apple : $%v\n", outApple.MoneyPayment)
}
```

Ouput :

```c
Pay for orange : $142329
Pay for apple : $134797.5

Process finished with the exit code 0
```

# IV. Source code

* Source code builder pattern : [Example with Golang](https://github.com/nguyenvantuan2391996/design-pattern-golang-example/tree/master/builder_pattern)
    
* Source almost popular design patterns : [Design patterns](https://github.com/nguyenvantuan2391996/design-pattern-golang-example)