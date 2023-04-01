---
title: "[ Design Patterns ] - Factory pattern with Golang"
seoTitle: "[ Design Patterns ] - Factory pattern with Golang"
seoDescription: "We need to build a system for detecting animal sounds. The input will be the animal and the output will return the sound of the animal."
datePublished: Sat Apr 01 2023 08:47:25 GMT+0000 (Coordinated Universal Time)
cuid: clfxqd2u3000809lcdpep3we9
slug: design-patterns-factory-pattern-with-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1680338737799/5ee14d4c-c5b3-47b5-a6c4-fe832176aded.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1680338812769/4b61be5b-608b-4124-b402-70cf8100ccfb.png
tags: factory, patterns, golang, factory-design-pattern

---

**Ha Noi, on Thursday 01/04/2023**

At the moment, a little speech to give to the present...

<center><iframe width="560" height="315" src="https://www.youtube.com/embed/VTF2G6Egtq8"></iframe></center>

I miss Ha Noi's autumn. Sometimes, I hung out with her on all the streets in Ha Noi. We hugged, and she said to me: "I love you". Ha Noi is the best beauty in the older days that we love us...

# I. Factory Pattern

*Factory Method is a creational design pattern that provides an interface for creating objects in a superclass, but allows subclasses to alter the type of objects that will be created.*

![The structure of the Factory Method pattern](https://refactoring.guru/images/patterns/diagrams/factory-method/structure.png align="center")

Reference: [Factory Pattern](https://refactoring.guru/design-patterns/factory-method)

# II. Implement

## 1\. The Problem

We need to build a system for detecting animal sounds. The input will be the animal and the output will return the sound of the animal.

Example:

* The cat will say "meow meow"
    
* The Dog will say "gauw gauw"
    

## 2\. The Problem Solving

The first version of your app can only handle sound from the cat, so the bulk of your code lives inside the Animal class.

When we need to implement a new animal, we will update its codebase. Moreover, if later you decide to add another animal to the app, you will probably need to make all of these changes again.

As a result, you will end up with some pretty nasty code, riddled with conditionals that switch the app’s behavior depending on the class of animal objects.

So, to resolve the problem, we will use the Factory Pattern.

## 3\. Implement Factory Pattern

### 3.1. Factory

**ianimal\_factory.go**

```golang
package factory

type IAnimalFactory interface {
	GetSound() string
}
```

**animal\_factory.go**

```golang
package factory

import (
	"errors"

	"design-pattern-golang-example/factory_pattern/concrete"
	"design-pattern-golang-example/factory_pattern/constants"
)

type AnimalFactory struct {
	Cat *concrete.Cat
	Dog *concrete.Dog
	Rat *concrete.Rat
}

func (af *AnimalFactory) GetAnimalFactory(name string) (IAnimalFactory, error) {
	if name == constants.CAT {
		return af.Cat, nil
	} else if name == constants.DOG {
		return af.Dog, nil
	} else if name == constants.RAT {
		return af.Rat, nil
	}

	return nil, errors.New("getting animal factory is failed")
}
```

### 3.2. Concrete

**cat.go**

```golang
package concrete

type Cat struct {
}

func NewCat() *Cat {
	return &Cat{}
}

func (c *Cat) GetSound() string {
	return "meow meow"
}
```

**dog.go**

```golang
package concrete

type Dog struct {
}

func NewDog() *Dog {
	return &Dog{}
}

func (d *Dog) GetSound() string {
	return "gauw gauw"
}
```

**rat.go**

```golang
package concrete

type Rat struct {
}

func NewRat() *Rat {
	return &Rat{}
}

func (r *Rat) GetSound() string {
	return "chit chit"
}
```

### 3.3. Main Function

**main.go**

```golang
package main

import (
	"fmt"

	"design-pattern-golang-example/factory_pattern/concrete"
	"design-pattern-golang-example/factory_pattern/constants"
	"design-pattern-golang-example/factory_pattern/factory"
)

func main() {
	// init
	cat := concrete.NewCat()
	dog := concrete.NewDog()
	rat := concrete.NewRat()
	animalFactory := &factory.AnimalFactory{
		Cat: cat,
		Dog: dog,
		Rat: rat,
	}

	// factory
	processor, err := animalFactory.GetAnimalFactory(constants.CAT)
	if err != nil {
		fmt.Println(err)
	}
	sound := processor.GetSound()
	fmt.Println(sound)
}
```

**The result:**

```golang
meow meow
```

# III. Source code

* Source code factory pattern: [Example with Golang](https://github.com/nguyenvantuan2391996/design-pattern-golang-example/tree/master/factory_pattern)
    
* Source almost popular design patterns: [Design patterns](https://github.com/nguyenvantuan2391996/design-pattern-golang-example)