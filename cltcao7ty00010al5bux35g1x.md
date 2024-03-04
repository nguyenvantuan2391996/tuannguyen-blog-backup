---
title: "Thuật toán Quick sort"
seoTitle: "Thuật toán Quick sort"
seoDescription: "QuickSort là một thuật toán sắp xếp hiệu quả và phổ biến được phát minh bởi Tony Hoare vào năm 1960. Nó thuộc loại thuật toán chia để trị"
datePublished: Mon Mar 04 2024 02:00:53 GMT+0000 (Coordinated Universal Time)
cuid: cltcao7ty00010al5bux35g1x
slug: thuat-toan-quick-sort
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1706457036441/6a06d164-47fa-40ba-ae9e-46799af4b2bb.avif
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1706457073602/3f926ed6-d3dd-4d9e-a12d-4a7f2a1453fb.avif
tags: go, golang, sorting, quick-sort

---

# 1. Giới thiệu

**QuickSort** là một thuật toán sắp xếp hiệu quả và phổ biến được phát minh bởi **Tony Hoare** vào năm 1960. Nó thuộc loại thuật toán chia để trị, tức là chia dãy số cần sắp xếp thành các phần nhỏ hơn, sắp xếp các phần đó, và sau đó kết hợp chúng để có dãy số hoàn chỉnh đã được sắp xếp.

**QuickSort** có nhiều ưu điểm khi được so sánh với các thuật toán sắp xếp khác:

✅ Hiệu Quả Thời Gian

- Trong trường hợp trung bình, thời gian chạy của QuickSort là **O(nlogn)**, làm cho nó hiệu quả và phù hợp với sắp xếp dữ liệu lớn.
- Tốc độ chạy nhanh hơn so với nhiều thuật toán sắp xếp khác như **Bubble Sort, Insertion Sort.**

✅ Không Yêu Cầu Bộ Nhớ Phụ

- QuickSort không đòi hỏi bộ nhớ phụ (auxiliary memory), điều này làm cho nó phù hợp với các ứng dụng yêu cầu không gian bộ nhớ thấp.

✅ Dễ Cài Đặt và Hiểu

- QuickSort có thể được cài đặt đơn giản và dễ hiểu so với một số thuật toán sắp xếp phức tạp hơn như **Merge Sort** hoặc **Heap Sort**.

Các bạn có thể xem video Visual Explanation Quick Sort ở đây nhé: [Visual Explanation](https://www.youtube.com/watch?v=WprjBK0p6rw&ab_channel=CuriousWalk)

# 2. Cách Quick sort hoạt động

Bước 1: Khởi tạo 2 con trỏ **curA** và **curB** có giá trị = -1 và giá trị **pivot**. Ở đây mình chọn giá trị **pivot** = số cuối cùng trong mảng.

Bước 2: Loop mảng cần sắp xếp -> tăng giá trị con trỏ curA lên 1 đơn vị. Nếu:
- Giá trị của phần tử tại **curA > pivot** -> pass.
- Giá trị phần tử **curA <= pivot** -> tăng giá trị **curB** lên 1 đơn vị và tiếp tục kiểm tra giá trị **curA == curB** -> pass. Còn ngược lại **curA > curB** -> swap giá trị phần tử ở vị trí 2 con trỏ lại với nhau -> pass

Bước 3: Sau khi loop hết mảng -> chia mảng ra 2 phần: phần bên trái sẽ có giá trị **< pivot** và bên phải có giá trị **>= pivot**

Bước 4: Thực hiện đệ quy lại bước 1 với 2 mảng được chia ở bước 3. Nếu số lượng phần tử trong mảng < 2 thì return và dừng.

# 3. Implement thuật toán

```golang
package main

func partition(nums []int) ([]int, []int) {
	curA, curB := -1, -1
	pivot := nums[len(nums)-1]

	for _, value := range nums {
		curA++
		if value <= pivot {
			curB++
			if curA > curB {
				nums[curA], nums[curB] = nums[curB], nums[curA]
			}
		}
	}

	return nums[:curB], nums[curB:]
}

func QuickSort(nums []int) {
	if len(nums) < 2 {
		return
	}

	left, right := partition(nums)
	QuickSort(left)
	QuickSort(right)
}
```
```golang
func main() {
	nums := []int{3, 2, 5, 0, 1, 8, 7, 6, 9, 4}
	fmt.Println(fmt.Sprintf("Before: %v", nums))
	QuickSort(nums)
	fmt.Println(fmt.Sprintf("After: %v", nums))
}
```

Output

> Before: [3 2 5 0 1 8 7 6 9 4]

> After: [0 1 2 3 4 5 6 7 8 9]




