## [Phần 1] Hệ thống tư vấn chọn người yêu: Tổng quan và phương pháp TOPSIS

> Hạnh phúc của một người phụ là gì em biết không? Đó chính là có một người đàn ông thật sự thương mình, là có những đứa con ngoan, là có một gia đình để chăm lo vun vén. ❤️ ❤️ ❤️

Lý thuyết là vậy đó nhưng bạn đã bao giờ mắc kẹt trong nhiều sự lựa chọn chưa? 😝 Bạn phân vân không biết nên chọn ai và lúc nào cũng đau đáu một câu hỏi trong suy nghĩ:

**Vậy nên chọn người mình yêu hay người yêu mình?**

Đây chính là đề tài muôn thuở trong tình yêu đối với cả người đàn ông và người phụ nữ. Chọn người mình yêu hay người yêu mình là câu hỏi mà khó có câu trả lời cho thỏa đáng. 

Vậy thay vì mặc số phận trời định thì hãy cùng mình vớt vát lại ít xác suất phần trăm lựa chọn đúng người mà mình gắn bó suốt cuộc đời cho các bạn gái nhé 😆 Chúng ta sẽ dựng một hệ thống website application tư vấn để lựa chọn ra người yêu phù hợp nhất dựa theo các tiêu chí đề ra ví dụ như: Tuổi, chiều cao, tiền tiết kiệm, thu nhập, sự quan tâm & chăm sóc.

Vấn đề và đầu vào đã có! Vậy hãy cùng mình bắt tay vào xây dựng hệ thống thôi nào! Gét gô  😃 😃 😃

Dự định mình sẽ chia series này thành 5 phần chính :
- Giới thiệu tổng quan và phương pháp TOPSIS.
- Thiết kế Wireframe, API và Database.
- Dựng Back-end: Golang với Gin-Gonic framework.
- Dựng Front-end: ReactJS + TypeScript + Ant Design UI.
- Deploy website và demo.

Ở phần 1 này mình sẽ giới thiệu tổng quan về phương pháp TOPSIS.

[TOPSIS](https://en.wikipedia.org/wiki/TOPSIS) là viết tắt của từ **Technique for Order Preference by Similarity to Ideal Solution** được ra đời vào những năm 1980 như một phương pháp ra quyết định dựa trên đa tiêu chí. TOPSIS dựa trên khái niệm rằng phương án được chọn phải có khoảng cách Euclid ngắn nhất từ ​​nghiệm lý tưởng dương (PIS) và khoảng cách Euclid dài nhất từ ​​nghiệm lý tưởng âm (NIS).

> Đại loại hiểu nôm na là chúng ta sẽ chọn ra đánh giá tốt nhất của từng tiêu chí và tạo thành một vector. Sau đó, chúng ta sẽ tính khoảng cách từng lựa chọn tới vector này bằng khoảng cách Euclid. Lựa chọn tốt nhất sẽ có khoảng cách gần với vector nhất 😃

Quá trình TOPSIS được thực hiện như sau:

![Screen Shot 2022-05-13 at 00.57.52.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1652378289643/Rl4U3gFYJ.png align="left")
![Screen Shot 2022-05-13 at 00.54.50.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1652378118717/igkjZtEfy.png align="left")

> Các bạn xem tạm ảnh nhé cái hash node này nó không support viết công thức toán học dạng inline $ $ LOL 😓 😓 😓 Vậy nên ở phần 3 mình sẽ làm và nói rõ chi tiết từng bước sau nhé.
