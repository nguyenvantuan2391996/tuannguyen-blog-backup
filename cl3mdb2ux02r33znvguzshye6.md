# [Phần 2] Hệ thống tư vấn chọn người yêu: Thiết kế Wireframe, API và Database

Chúng ta tiếp tục phần 2 của series xây dựng [Hệ thống tư vấn chọn người yêu](https://tuannguyenhust.hashnode.dev/phan-1-he-thong-tu-van-chon-nguoi-yeu-tong-quan-va-phuong-phap-topsis). Ở phần 2 này hãy cùng mình thiết kế **Wireframe**, **API** và **Database** cho hệ thống này nhé 😁😁😁

# I. Thiết kế  Wireframe

Mở đầu phần này, chúng ta hãy cùng nhau vẽ **Wireframe** các màn hình trước để có cái nhìn tổng quan về cấu trúc website và phác hoạ các thứ chúng ta cần làm. Mình không phải dân design nên mình lựa chọn 1 tool đơn giản để vẽ đó là [draw.io](https://app.diagrams.net/) (Cái này hay dùng để vẽ UML nhưng mình dùng nó để design 🙏 :lol:). Dưới đây là ảnh wireframe màn hình

![topsis.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1653498363416/wHv90IA19.jpg align="left")

Cấu trúc website chúng ta sẽ có 4 màn chính như trên hình:
1. Màn hình nhập tên.
2. Màn hình nhập danh sách tiêu chí.
3. Màn hình nhập thông tin lựa chọn.
4. Màn hình tư vấn: Tư vấn sắp kết quả tốt nhất từ cao xuống thấp theo thứ tự.

# II. Thiết kế API

Phác hoạ từ **wireframe**, chúng ta dựa vào đó để design các **API Back-end** cần thiết cho website. Để tạo document api thì mình dùng tool [stoplight.io](https://stoplight.io)! Gét gô 😃😃😃

**Note**: Do không cần login gì liên quan xác thực nên mình bỏ qua đoạn authentication bằng token ở các api! Chi tiết document api mình có public ở [link](https://topsis.stoplight.io/docs/topsis) này nhé

**II.1. Màn hình nhập tên:** Chúng ta thấy người dùng sẽ nhập tên của mình vào để hệ thống lưu lại thông tin này.

**API tạo thông tin người dùng**

![Screen Shot 2022-05-25 at 01.22.19.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653416552831/EtU5QGZ2q.png align="left")

```
curl --request POST \
  --url http://localhost:3000/user \
  --header 'Content-Type: application/json' \
  --data '{
  "name": "user@example.com"
}'
```

**II.2. Màn hình nhập danh sách tiêu chí:** Cho phép người dùng thêm, xoá tiêu chí đánh giá các lựa chọn ví dụ như: Tuổi, chiều cao, tiền tiết kiệm, thu nhập, sự quan tâm & chăm sóc...

**API tạo thông tin tiêu chí**

![Screen Shot 2022-05-25 at 01.20.42.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653416470503/2pz5b7k_s.png align="left")

```
curl --request POST \
  --url http://localhost:3000/standard \
  --header 'Content-Type: application/json' \
  --data '{
  "id": "string",
  "user_id": "string",
  "standard_name": "string",
  "weight": 0
}'
```

**API xoá thông tin tiêu chí**

![Screen Shot 2022-05-26 at 00.07.47.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653498482747/99ySiNeVP.png align="left")

```
curl --request DELETE \
  --url http://localhost:3000/standard \
  --header 'Content-Type: application/json'
```

**II.3. Màn hình nhập thông tin lựa chọn:** Cho phép người dùng thêm, xoá thông tin đánh giá các lựa chọn dựa vào các tiêu chí được thêm ở màn 2

**API lấy thông tin tiêu chí theo user id**

![Screen Shot 2022-05-25 at 01.33.12.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653417213537/g0yya_1sk.png align="left")

```
curl --request GET \
  --url http://localhost:3000/standard \
  --header 'Content-Type: application/json'
```

**API thêm thông tin đánh giá các lựa chọn theo tiêu chí**

![Screen Shot 2022-05-25 at 01.52.33.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653418367725/jk1LbENBp.png align="left")

```
curl --request POST \
  --url http://localhost:3000/score-rating/bulk \
  --header 'Content-Type: application/json' \
  --data '[
  {
    "id": "string",
    "name": "string",
    "metadata": "string",
    "user_id": "string"
  }
]'
```

**API xoá thông tin đánh giá các lựa chọn theo tiêu chí**

![Screen Shot 2022-05-26 at 00.27.58.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653499698966/bsVjKYFNf.png align="left")

```
curl --request DELETE \
  --url http://localhost:3000/score-rating/bulk \
  --header 'Content-Type: application/json'
```

# III. Thiết kế Database

Dựa vào wireframe, api chúng ta sẽ hình dung ra được database hệ thống này sẽ cần có các bảng nào. Cùng mình phân tích nhé ! Ở đây mình cũng dùng 1 tool online để thiết kế Database đó là [dbdiagram.io](https://dbdiagram.io/) 😃😃😃 Các bạn có thể xem trực tiếp trên [link](https://dbdiagram.io/d/6170d7c56239e146477acf7e) này nhé!

![topsis.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653499848104/ybo00tgf1.png align="left")

**Kết luận:** Đó đơn giản vậy thôi là chúng ta đã thiết kế được **wireframe, api, database.** Xem xong phần 2 này, các bạn cũng có thể đã hình dung được na ná chúng ta sẽ phải code thế nào rồi. Lời khuyên của mình là mỗi khi làm 1 gì đó cần nghĩ kĩ, vẽ chi tiết, phân tích thiết kế hệ thống càng chi tiết càng tốt chứ đừng nhảy vào code luôn là dễ đập đi xây lại mỗi khi bế tắc lắm =)) 😅😅😅