## Two sum - Một bài toán kinh điển cho bất kì ai mới bước chân vào luyện algorithm

[Two sum](https://leetcode.com/problems/two-sum/) Một bài toán kinh điển cho bất kì ai mới bước chân vào luyện algorithm.

Yêu cầu của đề bài:
```
Given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target.
```
Hiểu đơn giản là:

Cho đầu nào mảng số nguyên **nums** và 1 số nguyên **target**, trả về vị trí của 2 số sao cho chúng cộng với nhau bằng **target**

Example 1:
```
Input: nums = [2,7,11,15], target = 9
Output: [0,1]
Explanation: Because nums[0] + nums[1] == 9, we return [0, 1].
```
Example 2:
```
Input: nums = [3,2,4], target = 6
Output: [1,2]
```

Example 3:
```
Input: nums = [3,3], target = 6
Output: [0,1]
```

Với đề bài trên, cách đơn giản là chúng ta sẽ vét cạn bằng cách duyệt mảng 2 lần lồng nhau và tính tổng từng cặp. Nếu tổng mà bằng **target** thì return về luôn vị trí của 2 phần tử -> Thuật toán sẽ có độ phức tạp O(n^2)
```golang
func twoSum(nums []int, target int) []int {
	for i, value := range nums {
		for j := i + 1; j < len(nums); j++ {
			if value+nums[j] == target {
				return []int{i, j}
			}
		}
	}
	return []int{}
}
```

Cách trên rất đơn giản phải không? Nó ok nhưng không thời gian chạy O(n^2) của solution thật quá là chậm! Bạn thử tưởng tưởng mảng số nguyên nums truyền vào có 10^6 phần tử -> khi thực hiện thuật toán máy tính của chúng ta phải thực hiện 10^12 phép tính (Một con số nghìn tỉ ^^). Vậy hãy cùng mình optimize xuống xem sao nhé ^^

**Ý tưởng:** Tạo ra một map[int][int] có key lưu giá phần tử, value lưu vị trí phần tử trong mảng nums. Chúng ta duyệt 1 vòng for và kiểm tra nếu **target - value** có trong map thì return lại vị trí còn không thì push giá trị này vào map với key là  **value**, value vị trí phần tử -> Thuật toán sẽ có độ phức tạp O(n)
```golang
func twoSum(nums []int, target int) []int {
    mapNums := make(map[int]int)
    for i, value := range nums {
        if j, ok := mapNums[target-value]; ok {
            return []int{j, i}
        }
        mapNums[value] = i
    }
    return []int{}
}
```