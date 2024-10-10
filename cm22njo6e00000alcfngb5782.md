---
title: "Two pointer with Golang"
seoTitle: "Two pointer with Golang"
seoDescription: "Write a function that reverses a string. The input string is given as an array of characters s."
datePublished: Thu Oct 10 2024 02:00:56 GMT+0000 (Coordinated Universal Time)
cuid: cm22njo6e00000alcfngb5782
slug: two-pointer-with-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1724596595915/5b3783aa-7fb9-444c-a8ba-12bdfa08c0ab.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1724596623149/45b3a1f1-bec8-4b7a-8efc-822f32ede39d.png
tags: go, go-cjffccfnf0024tjs1mcwab09t, two-pointers

---

Write a function that reverses a string. The input string is given as an array of characters `s`.

You must do this by modifying the input array [in-place](https://en.wikipedia.org/wiki/In-place_algorithm) with `O(1)` extra memory.

**Example 1:**

```go
Input: s = ["h","e","l","l","o"]
Output: ["o","l","l","e","h"]
```

**Example 2:**

```go
Input: s = ["H","a","n","n","a","h"]
Output: ["h","a","n","n","a","H"]
```

**Constraints:**

* `1 <= s.length <= 10<sup>5</sup>`
    
* `s[i]` is a [printable ascii character](https://en.wikipedia.org/wiki/ASCII#Printable_characters).
    

Reference: [https://leetcode.com/problems/reverse-string/description/](https://leetcode.com/problems/reverse-string/description/)

**Idea:**

![Reverse An Array](https://afteracademy.com/images/reverse-an-array-visualization-44657f9f5e374db8.gif align="center")

**Implement with Golang:**

```go
package main

import "fmt"

func reverse[T comparable](arr []T) []T {
	left, right := 0, len(arr)-1
	for left < right {
		arr[left], arr[right] = arr[right], arr[left]
		left++
		right--
	}

	return arr
}

func main() {
	arr := []int{1, 2, 3, 4, 5, 6, 7, 8, 9}
	fmt.Println(reverse(arr))
}
```