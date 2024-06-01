---
title: "Design a HashMap"
seoTitle: "Design a HashMap"
seoDescription: "Design a HashMap without using any built-in hash table libraries."
datePublished: Sat Jun 01 2024 02:00:29 GMT+0000 (Coordinated Universal Time)
cuid: clwvgui8e000408ml81g67hx5
slug: design-a-hashmap
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1716737460540/73b0944e-df1a-4496-94e0-7181983a0fe6.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1716739009407/2c446f03-414a-4b30-b229-d8590e103fee.jpeg
tags: go, golang, hashmap, design-hashmap

---

Design a HashMap without using any built-in hash table libraries.

Implement the `MyHashMap` class:

* `MyHashMap()` initializes the object with an empty map.
    
* `void put(int key, int value)` inserts a `(key, value)` pair into the HashMap. If the `key` already exists in the map, update the corresponding `value`.
    
* `int get(int key)` returns the `value` to which the specified `key` is mapped, or `-1` if this map contains no mapping for the `key`.
    
* `void remove(key)` removes the `key` and its corresponding `value` if the map contains the mapping for the `key`.
    

**Example 1:**

```yaml
Input
["MyHashMap", "put", "put", "get", "get", "put", "get", "remove", "get"]
[[], [1, 1], [2, 2], [1], [3], [2, 1], [2], [2], [2]]
Output
[null, null, null, 1, -1, null, 1, null, -1]

Explanation
MyHashMap myHashMap = new MyHashMap();
myHashMap.put(1, 1); // The map is now [[1,1]]
myHashMap.put(2, 2); // The map is now [[1,1], [2,2]]
myHashMap.get(1);    // return 1, The map is now [[1,1], [2,2]]
myHashMap.get(3);    // return -1 (i.e., not found), The map is now [[1,1], [2,2]]
myHashMap.put(2, 1); // The map is now [[1,1], [2,1]] (i.e., update the existing value)
myHashMap.get(2);    // return 1, The map is now [[1,1], [2,1]]
myHashMap.remove(2); // remove the mapping for 2, The map is now [[1,1]]
myHashMap.get(2);    // return -1 (i.e., not found), The map is now [[1,1]]
```

**Constraints:**

* `0 <= key, value <= 10<sup>6</sup>`
    
* At most `10<sup>4</sup>` calls will be made to `put`, `get`, and `remove`.
    

Reference: [https://leetcode.com/problems/design-hashmap/description/](https://leetcode.com/problems/design-hashmap/description/)

**Idea:**

![Implementing our Own Hash Table with Separate Chaining in Java -  GeeksforGeeks](https://media.geeksforgeeks.org/wp-content/cdn-uploads/implementing-own-hash-table.png align="left")

**Implement with Golang:**

```go
type MyHashMap struct {
    Hm [][]*Node
} 

type Node struct {
    Key int
    Value int
}

func hash(key int) int {
    return key % 1000
}

func Constructor() MyHashMap {
    hm := make([][]*Node, 1000)
    return MyHashMap{
        Hm: hm,
    }
}


func (this *MyHashMap) Put(key int, value int)  {
    keyHash := hash(key)
    for index, element := range this.Hm[keyHash] {
        if element.Key == key {
            this.Hm[keyHash][index].Value = value
            return
        }
    }

    this.Hm[keyHash] = append(this.Hm[keyHash], &Node{
        Key: key,
        Value: value,
    })
}


func (this *MyHashMap) Get(key int) int {
    keyHash := hash(key)
    for _, element := range this.Hm[keyHash] {
        if element.Key == key {
            return element.Value
        }
    }

    return -1
}


func (this *MyHashMap) Remove(key int)  {
    keyHash := hash(key)
    for index, element := range this.Hm[keyHash] {
        if element.Key == key {
            this.Hm[keyHash][index].Value = -1
        }
    }
}
```