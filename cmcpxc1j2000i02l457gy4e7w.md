---
title: "Circuit Breaker Pattern - Cách bảo vệ hệ thống khỏi lỗi lan truyền"
seoTitle: "Circuit Breaker Pattern - Cách bảo vệ hệ thống khỏi lỗi lan truyền"
seoDescription: "Circuit Breaker Pattern là một mẫu thiết kế phần mềm được sử dụng để xử lý các lỗi liên quan đến các cuộc gọi tới các dịch vụ từ các thành phần khác nhau"
datePublished: Sat Jul 05 2025 07:30:41 GMT+0000 (Coordinated Universal Time)
cuid: cmcpxc1j2000i02l457gy4e7w
slug: circuit-breaker-pattern-cach-bao-ve-he-thong-khoi-loi-lan-truyen
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1751700431293/470e4e12-b266-464d-b9f8-1c2e23e81a33.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1751700567693/6e5fe813-06de-4bc7-af8c-fbade9c179cd.jpeg
tags: microservices, design-patterns, go, circuit-breaker

---

<center><iframe width="560" height="315" src="https://www.youtube.com/embed/FyX5A_mduyw?si=jffGBf9MpOAGfU-v" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe></center>

Ngày cuối tuần vào mùa mưa Hà Nội 2025... Ghi lại một chút cảm xúc gửi gắm thời gian.

Giọt mưa đưa em về xa xăm... Có lẽ để chạm đến cảm xúc trong mỗi chúng ra thì chẳng có điều gì có thể làm tốt hơn mưa. Mỗi khi thấy mưa, tôi không biết bạn thấy thế nào nhưng với bản thân, tôi lại nhớ đến bài hát này. Mỗi hoàn cảnh, mỗi thời điểm hay mỗi địa điểm lại làm cho tôi nhớ đến những ký ức khác nhau nhưng đều rất đỗi thân quen. Hạt mưa rơi trong buổi đầu gặp gỡ bóng hình mà mình đã dõi theo từ nhiều năm về trước, mưa ào ạt từng cơn trong buổi chiều trốn học, mưa rả rích nhiều ngày làm lòng nao nao chả còn tâm trạng ôn bài, hay mưa chỉ đơn giản là ngồi dưới mái hiên nhìn từng giọt...

Với mỗi người cảm nhận về mưa, về bài hát này sẽ là một bức hình, một tâm trạng hay có thể là một nỗi buồn, niềm vui khác nhau. Phải thú nhận rằng mỗi khi lắng nghe giai điệu này, tôi tìm thấy mình trong ký ức với Mưa!

# I. Vấn Đề
Nhóm của tôi hiện đang phát triển, vận hành hệ thống nhận diện khuôn mặt cho các khách sạn của Vinpearl, Melia, Marriott,... các khu vui chơi, công viên giải trí Vinwonders, Safari và trong hệ sinh thái của Vinpearl. Nghiệp vụ của hệ thống nôm na là: khách tới check-in khách sạn, mua vé vui chơi thì sẽ đăng kí khuôn mặt vào hệ thống của khách sạn tương ứng -> đi qua các cửa tay quay vào khu nghỉ dưỡng, vui chơi thì chỉ cần quét mặt rồi đi vào.

Vì khách sạn nào khi làm thủ tục xong đều có thể vào chơi một số khu vui chơi, tham quan theo quy định -> khi đi qua các cửa thì phải gọi qua các hệ thống khách sạn để kiểm tra có quyền vào hay không. Dạo gần đây, nhóm vận hành ghi nhận tình trạng: khi một khách sạn gặp sự cố về mạng, server thì khi hệ thống của team gọi api qua thì bị 500 và timeout của 1 request đang để là 30s -> khách đi chơi sẽ đợi lâu và càng ngày sẽ càng dồn đông -> hệ thống tắc nghẽn 😩😩😩

Mỗi lần như vậy, nhóm vận hành giải quyết bằng cách remove khách sạn này khỏi danh sách kiểm tra quyền để hệ thống chúng tôi làm việc lại bình thường với các khách sạn khác -> nhóm đợi hệ thống khách sạn kia phục hồi lại -> add lại khách sạn lỗi. Tất cả thao tác đều phải xử lý bằng cơm thủ công -> việc vận hành trở nên khó khăn và thụ động trong việc xử lý sự cố ( Mặc dù có alert service Back-end bắn về teams hay nhóm vận hành có dựng cả Grafana, Monitoring,... tôi không vận hành nên không rõ :)) chắc mỗi team đều có một nổi khổ riêng :D )

# II. Yêu Cầu & Giải pháp
Từ vấn đề nêu ở trên, khách hàng và product owner yêu cầu phải đảm bảo hệ thống ngắt kết nối ngay việc gọi tới khách sạn bị lỗi và khi hệ thống bên đó được phục hồi thì sẽ gọi lại được. Tất cả đều phải tự động, chủ động xử lý sự cố và có alert cảnh báo về teams chat khi có sự cố thay vì thụ động chờ blame thì khách hàng thì mới nhảy vào xử lý.

Từ vấn đề, yêu cầu và để giải quyết, team ngồi bàn luận và đưa ra 1 số giải pháp. Nhóm vận hành thì có một bạn rất sôi nổi đưa ra idea. Bạn này bằng tuổi tôi và cá nhân tôi đánh giá là một người có đam mê, tinh thần trách nhiệm với sản phẩm nhất trong nhóm vận hành 😃 Dưới đây là một số ý kiến tổng hợp được

- Làm một API để add/remove khách sạn lỗi ra khỏi việc check quyền để chủ động call API khi có sự cố
- Làm một nút bấm trên hệ thống CMS để click gọi API
- Làm một con job ngoài health check định kì vào server -> thấy server có vấn đề thì call API để remove -> khoẻ lại thì call API add lại.

Vì làm sản phẩm nên tất cả các teams đều đưa ra giải pháp, idea cho bất cứ feature, vấn đề nào cần giải quyết miễn sao cuối cùng công việc hoàn thành và hệ thống trở nên tốt nhất. Với cá nhân tôi sau vài năm làm product, tôi đều hiểu được tâm lý người đưa ra ý kiến và mỗi khi cần tranh luận gì tôi luôn kể ra ưu nhược điểm của từng cái để thuyết phục họ.

- Với ý kiến đầu tiên, tôi thấy chỉ là giải pháp tạm thời để rút gọn các bước xử lý sự cố.
- Ý kiến thứ 2 cũng ok và người non-it cũng có thể làm được nhưng vẫn phải có con người vào để giải quyết.
- Với ý kiến thứ 3 tôi thấy ok nhất trong 3 ý kiến. Tuy nhiên, ý nghĩa việc health check server còn sống hay không chứ chưa kiểm tra được các thành phần: database, redis,... down thì dịch vụ cũng không thể xử lý được và chưa kể nếu health check vào 1 đầu api xử lý nặng thì tạo ra traffic rác gây chậm hệ thống.

Ví dụ: Hệ thống đang chịu được 2 request và đang xử lý bình thường với 2 request -> health check thêm 1 request -> hệ thống down mà rõ ràng ở đây nếu không có thì vẫn sẽ xử lý bình thường. Vậy nên health check người ta chỉ ping tới 1 api nhẹ và không xử lý logic gì.

![](https://images.viblo.asia/733x529/a4327550-41e0-4829-b6f1-57a096abe6ee.png align="center")

Cuối cùng, tôi đưa ra ý kiến dùng *Circuit Breaker Pattern* - Một design pattern để bảo vệ hệ thống khỏi lỗi lan truyền.

# III. Circuit Breaker Pattern
## 1. Giới Thiệu
Circuit Breaker Pattern là một mẫu thiết kế phần mềm được sử dụng để xử lý các lỗi liên quan đến các cuộc gọi tới các dịch vụ từ các thành phần khác nhau trong một hệ thống phân tán. Khi một dịch vụ không sẵn sàng hoặc bị lỗi, Circuit Breaker Pattern giúp tăng tính ổn định và độ tin cậy của hệ thống bằng cách ngắt kết nối tới dịch vụ đó và sử dụng một cơ chế phục hồi thay thế. Circuit Breaker là một pattern giúp: 

- Bảo vệ hệ thống khỏi lỗi dây chuyền (cascading failure). 
- Ngăn các yêu cầu tiếp tục đến dịch vụ bị lỗi. 
- Cho dịch vụ bị lỗi có thời gian “hồi phục”. 

## 2. Hoạt Động
Circuit Breaker thực hiện tự động thông qua 3 trạng thái:
- **Closed:** Tất cả request đều được gửi đến dịch vụ Marriott. Nếu có request thất bại (timeout, lỗi 500,...) -> ghi nhận n lần thất bại ( config ). Khi số lỗi vượt ngưỡng lỗi cho phép -> chuyển sang trạng thái Open. 
- **Open:** Chặn tất cả request sang Marriott ngay lập tức -> giảm tải cho server đang gặp vấn đề, tránh gây thêm áp lực -> sau một thời gian chờ định sẵn ( config ) -> chuyển sang trạng thái Half-Open. 
- **Half-Open:** Cho phép một số ít request “thử nghiệm” đến dịch vụ -> nếu các request này thành công -> chuyển về Closed (Marriott đã call được bình thường). Nếu vẫn lỗi -> quay lại trạng thái Open 

![](https://images.viblo.asia/971x642/b452f1ec-a96d-430d-a5c0-f686b6f1e197.jpg align="center")

**Note:** Cứ tưởng tượng như cầu dao: Cầu dao đóng ( Closed ) thì điện chạy (request chạy). Cầu dao mở ( Open ) thì ngắt điện (request bị chặn). 

## 3. Áp Dụng
Ở phần áp dụng team Back-end sử dụng thư viện gobreaker ( 1 thư viện implement circuit breaker của Golang) để giải quyết -> https://github.com/sony/gobreaker

Tuy nhiên, để hiểu hơn tôi có ngồi tự implement lại để hiểu cách hoạt động và thực ra có lẽ là không quá khó nên tôi mới implement thôi :))

Code một product service và trong service có 1 api lấy danh sách sản phẩm
```Go
package main

import (
	"encoding/json"
	"log"
	"net/http"
)

type Product struct {
	ProductName string `json:"product_name"`
	ID          int    `json:"id"`
}

func getProducts(w http.ResponseWriter, r *http.Request) {
	if r.Method != http.MethodGet {
		http.Error(w, "Method not allowed", http.StatusMethodNotAllowed)
		return
	}

	w.Header().Set("Content-Type", "application/json")
	w.WriteHeader(http.StatusOK)
	err := json.NewEncoder(w).Encode([]*Product{
		{
			ID:          1,
			ProductName: "iPhone 16 Pro Max",
		},
		{
			ID:          2,
			ProductName: "iPhone 8 Plus",
		},
	})
	if err != nil {
		return
	}
}

func main() {
	http.HandleFunc("/products", getProducts)
	log.Fatal(http.ListenAndServe(":3001", nil))
}
```

Một service chính để client có thể call -> service lấy call product lấy danh sách sản phẩm
```Go

func (h *Handler) getExample(w http.ResponseWriter, r *http.Request) {
	if r.Method != http.MethodGet {
		http.Error(w, "Method not allowed", http.StatusMethodNotAllowed)
		return
	}

	products, err := h.product()
	if err != nil {
		http.Error(w, "Error", http.StatusInternalServerError)
		return
	}

	w.Header().Set("Content-Type", "application/json")
	w.WriteHeader(http.StatusOK)
	err = json.NewEncoder(w).Encode(products)
	if err != nil {
		return
	}
}

func main() {
	h := &Handler{CircuitBreaker: NewCircuitBreaker(5*time.Second, 5)}
	http.HandleFunc("/example", h.getExample)

	log.Fatal(http.ListenAndServe(":3000", nil))
}
```

Tiếp đến là implement **Circuit Breaker** để giải quyết vấn đề. Tôi khai báo một struct bao gồm
- State: trạng thái của CB
- Wait: thời gian chờ để CB chuyển từ open -> half-open
- Expiry: nếu now quá giá trị này thì CB call sang dịch vụ lỗi để kiểm tra đã phục hồi chưa.
- FailureThreshold: Ngưỡng lỗi để chuyển trạng thái Open ngăn không cho request chạy qua
- Failure: Số lần call lỗi dịch vụ
- Mu: 1 mutex để việc xử lý atomic ở 1 vài chỗ.

```Go
type CircuitBreaker struct {
	State            string
	Wait             time.Duration
	Expiry           int64
	FailureThreshold int64
	Failure          int64
	Mu               sync.Mutex
}

func NewCircuitBreaker(wait time.Duration, failureThreshold int64) *CircuitBreaker {
	return &CircuitBreaker{
		Wait:             wait,
		Mu:               sync.Mutex{},
		State:            Closed,
		FailureThreshold: failureThreshold,
	}
}

const (
	Open     = "open"
	HalfOpen = "half-open"
	Closed   = "closed"
)
```
Hàm Allow kiểm tra xem request được tiếp tục chạy qua dịch vụ cần check hay không?
```Go
func (cb *CircuitBreaker) Allow() bool {
	if cb.Expiry < time.Now().Unix() && cb.State == Open {
		cb.State = HalfOpen
		return true
	}

	if cb.Expiry >= time.Now().Unix() || cb.State == Open {
		return false
	}

	return true
}
```
Reset lại số lần call lỗi dịch vụ về zero khi trạng thái chuyển từ Closed sang Open
Update lại state về open khi số lượng lỗi vượt quá ngưỡng config từ trước
```Go
func (cb *CircuitBreaker) ResetFailure() {
	cb.Failure = 0
}

func (cb *CircuitBreaker) UpdateState() {
	if cb.Failure > cb.FailureThreshold {
		cb.State = Open
		cb.Expiry = time.Now().Add(cb.Wait).Unix()
		cb.ResetFailure()
	}
}
```

Hàm Execute thực hiện logic CB. Nếu trạng thái Open thì không call dịch vụ, Closed, Half-open thì thực hiện. Ở Half-open nếu call thấy lỗi -> chuyển trạng thái Open và thêm thời gian wait config trước để chờ và thử lại.
```Go
func (cb *CircuitBreaker) Execute(fc func() ([]byte, error)) (body []byte, err error) {
	if !cb.Allow() {
		fmt.Println("circuit breaker is open")
		return nil, fmt.Errorf("circuit breaker is open")
	}

	switch cb.State {
	case Closed:
		fmt.Println("circuit breaker is closed")
		body, err = fc()
		if err != nil {
			cb.Mu.Lock()
			defer cb.Mu.Unlock()

			cb.Failure++
			cb.UpdateState()
			return nil, err
		}

		return body, err
	case HalfOpen:
		fmt.Println("circuit breaker is half-open")
		cb.Mu.Lock()
		defer cb.Mu.Unlock()

		body, err = fc()
		if err != nil {
			cb.State = Open
			cb.Expiry = time.Now().Add(cb.Wait).Unix()
			return nil, err
		}

		cb.State = Closed
		return body, err
	}

	return
}
```
Ở phần gọi sang product service để lấy danh sách sản phẩm -> tôi wrap lại hàm Execute của CB để call api
```Go
func (h *Handler) product() ([]*ProductData, error) {
	body, err := h.CircuitBreaker.Execute(func() ([]byte, error) {
		client := &http.Client{}
		req, err := http.NewRequest(http.MethodGet, "http://0.0.0.0:3001/products", nil)

		if err != nil {
			return nil, err
		}

		res, err := client.Do(req)
		if err != nil {
			return nil, err
		}
		defer func(Body io.ReadCloser) {
			errClose := Body.Close()
			if errClose != nil {
				return
			}
		}(res.Body)

		body, err := io.ReadAll(res.Body)
		if err != nil {
			fmt.Println(err)
			return nil, err
		}

		return body, nil
	})

	if err != nil {
		return []*ProductData{}, nil
	}

	var response []*ProductData
	err = json.Unmarshal(body, &response)
	if err != nil {
		return nil, err
	}

	return response, nil
}
```

Và dưới đây là kết quả
```
circuit breaker is closed
circuit breaker is closed
circuit breaker is closed
circuit breaker is closed
circuit breaker is closed
circuit breaker is closed
circuit breaker is open
circuit breaker is open
circuit breaker is open
circuit breaker is open
circuit breaker is open
circuit breaker is half-open
circuit breaker is open
circuit breaker is open
circuit breaker is open
circuit breaker is open
circuit breaker is open
circuit breaker is open
circuit breaker is half-open
circuit breaker is closed
circuit breaker is closed
circuit breaker is closed
circuit breaker is closed
```

# IV. Kết Luận
Phần implement trên của tôi chỉ cho service chạy 1 node. Nếu áp dụng cho hệ thống phân tán, service nhiều nodes xử lý cần sử dụng 1 hệ thống lock bên thứ 3: Redis, Database,... thay vì sync.Mutex. Hiện tại, team Back-end chúng tôi đang implement giải pháp. Sau khi lên production chạy và monitoring thì tôi sẽ chia sẻ ở các bài viết sau nhé :))

- Source: https://github.com/nguyenvantuan2391996/example-code/tree/master/design-pattern/circuit-breaker





























