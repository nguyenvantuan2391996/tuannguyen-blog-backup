---
title: "Nguyên lý DRY: Tối ưu hóa mã nguồn và tái sử dụng"
seoTitle: "Nguyên lý DRY: Tối ưu hóa mã nguồn và tái sử dụng"
seoDescription: "Nguyên lý DRY viết tắt là Don't Repeat Yourself - là một nguyên lý trong lập trình phần mềm, nó khuyến khích việc tránh lặp lại mã code"
datePublished: Mon Jul 10 2023 02:30:39 GMT+0000 (Coordinated Universal Time)
cuid: cljw8xqw70qwv6pnv030vdfql
slug: nguyen-ly-dry-toi-uu-hoa-ma-nguon-va-tai-su-dung
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1688055900465/c06f1496-1ded-4e3e-ba7b-0d97adfa5899.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1688055918697/ad2a7a7e-5027-484f-b198-752e48d9ee02.png
tags: golang, dry, optimize, dont-repeat-yourself

---

# I. Giới thiệu

## 1\. Nguyên lý DRY

Nguyên lý DRY viết tắt là **Don't Repeat Yourself** - là một nguyên lý trong lập trình phần mềm, nó khuyến khích việc tránh lặp lại mã code không cần thiết trong hệ thống.

Nguyên lý DRY đề cao sự đơn giản và hiệu quả trong việc quản lý mã nguồn. Theo nguyên lý này, một phần của logic hoặc một đoạn mã nên được triển khai một lần duy nhất và được sử dụng lại trong nhiều nơi trong hệ thống. Điều này giúp giảm sự trùng lặp code, tăng tính tái sử dụng, dễ dàng bảo trì và giảm nguy cơ xảy ra lỗi.

![Don't Repeat Yourself' (DRY) Software Principle – The Daily Dose](https://onequestionadayhome.files.wordpress.com/2019/05/dry-repeat.gif align="center")

## 2. Lợi ích của DRY

Các lợi ích của việc áp dụng nguyên lý DRY bao gồm:

- Tiết kiệm thời gian và công sức: Việc viết mã code một lần và sử dụng lại trong nhiều vị trí giúp tiết kiệm thời gian và công sức so với việc viết lại cùng một logic nhiều lần.

- Dễ dàng bảo trì: Khi logic chỉ được triển khai một lần, nếu có sự thay đổi, chỉ cần chỉnh sửa tại một nơi duy nhất. Điều này giúp giảm nguy cơ xảy ra lỗi và giúp việc bảo trì mã nguồn dễ dàng hơn.

- Tính tái sử dụng cao: Mã nguồn tuân thủ nguyên lý DRY có thể được sử dụng lại trong các phần khác nhau của hệ thống hoặc trong các dự án khác nhau, tăng tính tái sử dụng và khả năng mở rộng của mã nguồn.

- Tăng tính nhất quán: Khi logic được triển khai một lần và sử dụng lại, đảm bảo rằng các phần của hệ thống sẽ hoạt động theo cùng một nguyên tắc và đảm bảo tính nhất quán trong ứng dụng.

# II. Một số phương pháp và kỹ thuật

Việc áp dụng nguyên lý DRY cũng tạo ra tính tái sử dụng cao cho mã nguồn. Khi chúng ta tạo ra các phần logic có thể tái sử dụng, chúng có thể được sử dụng lại trong nhiều phần của hệ thống hoặc trong các dự án khác. Điều này giúp tiết kiệm thời gian và công sức trong việc phát triển và mở rộng ứng dụng.

Ngoài ra, áp dụng nguyên lý DRY còn giúp tăng tính nhất quán trong ứng dụng. Khi logic chỉ được triển khai một lần và sử dụng lại, đảm bảo rằng các phần của hệ thống sẽ hoạt động theo cùng một nguyên tắc và đảm bảo tính nhất quán trong toàn bộ ứng dụng.

## 1. Modularization

**Modularization**: Chia nhỏ hệ thống thành các module nhỏ, mỗi module chịu trách nhiệm cho một phần cụ thể của ứng dụng. Điều này giúp tách biệt và tái sử dụng mã nguồn một cách dễ dàng.

Phần chia nhỏ này chúng ta có thể thấy rõ trong microservice. Nó đã áp dụng phương pháp này vào thiết kế kiến trúc và tư tưởng.

Ví dụ: Người dùng cần lấy thông tin user và product họ mua.

Example code:

```golang
func GetData(userID, productID int) (*User, *Product) {
	user := &User{
		ID:   userID,
		Name: fmt.Sprintf("%v-%v", "Tuan Nguyen", userID),
	}

	product := &Product{
		ID:          productID,
		ProductName: fmt.Sprintf("%v-%v", "iPhone 14 pro max", productID),
	}

	return user, product
}
```

Ở ví dụ này, chúng ta sẽ có 1 API viết code chung xử lý lấy thông tin user và product trong database. Nếu serivce của bạn down => người dùng sẽ không lấy được thông tin => trải nghiệm tệ

Vậy ở đây áp dụng kĩ thuật Modularization chúng ta sẽ chia nhỏ thành 2 service: user và product.

```Golang
func GetInformationFromUserService(id int) *User {
	return &User{
		ID:   id,
		Name: fmt.Sprintf("%v-%v", "Tuan Nguyen", id),
	}
}

func GetInformationFromProductService(id int) *Product {
	return &Product{
		ID:          id,
		ProductName: fmt.Sprintf("%v-%v", "iPhone 14 pro max", id),
	}
}
```
Việc tách nhỏ ra sẽ giúp chúng ta dễ dàng quản lý source code hơn và các service sẽ không bị phụ thuộc vào nhau.

## 2. Functions và Classes

**Functions và Classes**: Sử dụng functions và classes để đóng gói logic liên quan vào các đối tượng có thể tái sử dụng.

=> Ở đây chính là chúng ta phải viết các hàm, phương thức common dùng chung để tái sử dụng thay vì sử dụng nhiều nơi

Example code: Func lấy ngẫu nhiên 1 phần tử trong mảng string

```Golang
func GetElementRandomFromArray(arr []string) string {
	rand.Seed(time.Now().UnixNano())
	min := 0
	max := len(arr) - 1

	return arr[rand.Intn(max-min+1)+min]
}
```

## 3. Libraries và Frameworks

**Libraries và Frameworks**: Sử dụng thư viện và frameworks đã có sẵn để sử dụng các chức năng phổ biến và giảm việc viết lại mã code.

Việc sử dụng các thư viện giúp chúng ta giảm việc viết mã code và tăng tính clean code. 1 số thư viện sẵn có trong Golang: fmt, strings, os, http, sync,...

Example code: kiểm tra chuỗi có chứa subStr hay không?

```Golang
fmt.Println(strings.Contains("Tuan Nguyen", "tu"))
```

## 4. Sử dụng biến và hằng số

Sử dụng biến và hằng số: Đặt các giá trị thường xuyên sử dụng trong biến hoặc hằng số để dễ dàng thay đổi và tái sử dụng.

Việc đặt hằng số rất quan trọng trong việc viết mã. Nó giúp mã code chúng ta sạch sẽ, có ý nghĩa và tái sử dụng được nhiều hơn.

Example code:

```Golang
	time.Now().AddDate(0, 1, 1)

   // áp dụng hằng số
    const (
	    NumberOfDaysAdditional = 1
    )
	time.Now().AddDate(0, 0, NumberOfDaysAdditional)
```

## 5. Sử dụng kỹ thuật tái sử dụng

**Sử dụng kỹ thuật tái sử dụng**: Tạo ra các phần code có thể tái sử dụng và gói chúng thành các thư viện, modules hoặc components để có thể sử dụng lại trong các dự án khác.

Ví dụ: 1 số thư viện của Golang [https://github.com/robfig/cron](https://github.com/robfig/cron) hay là Lodash trong JavaScript

# III. Kết luận

Nguyên lý DRY đóng vai trò quan trọng trong việc xây dựng các hệ thống phần mềm linh hoạt, dễ bảo trì và tái sử dụng mã nguồn. Việc áp dụng nguyên lý này không chỉ giúp cải thiện hiệu suất và năng suất lập trình, mà còn tạo ra những ứng dụng ổn định và dễ dàng mở rộng trong tương lai.

Source code: [https://github.com/nguyenvantuan2391996/example-code/blob/master/dry/main.go](https://github.com/nguyenvantuan2391996/example-code/blob/master/dry/main.go)