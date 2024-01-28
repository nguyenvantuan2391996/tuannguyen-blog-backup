---
title: "Thực hiện nhiều workers chạy đồng thời sử dụng Channel và Goroutine"
seoTitle: "Thực hiện nhiều workers chạy đồng thời sử dụng Channel và Goroutine"
seoDescription: "Bài viết này, mình xin chia sẻ mọi người cách mình hay implement code chạy nhiều workers đồng thời để xử lý tác vụ và logic biz."
datePublished: Sun Jan 28 2024 02:00:17 GMT+0000 (Coordinated Universal Time)
cuid: clrwusrkd000109l0dfxe5m4c
slug: thuc-hien-nhieu-workers-chay-dong-thoi-su-dung-channel-va-goroutine
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1706368277881/7b4a5ab1-ff63-4cb6-94ff-dcb2374112fc.webp
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1706368268356/242aa877-86dd-48a5-878b-1c6331ca86bf.webp
tags: golang, goroutines

---

Hà Nội và ngày cuối tuần lạnh 5cm, mình vừa đi chơi tối về và ngồi chill chill viết blog.

Gần đây, mình mới chuyển công ti mới, có thêm nhiều đồng nghiệp mới và đặc biệt môi trường mới toanh đối với mình - khá lạ lẫm và hơi ngợp lúc đầu 😷 Hi vọng, nó sẽ giúp mình thêm kinh nghiệm trong công việc cũng như trải nghiệm cuộc sống ✌️

Vào chủ đề chính nhé! Bài viết này, mình xin chia sẻ mọi người cách mình hay implement code chạy nhiều workers đồng thời để xử lý tác vụ và logic biz ví dụ như: crawl data, xử lý import file hay xử lý các tác vụ cần chạy đồng thời.

Đầu tiên là cái function để handle logic cần xử lý. Ở đây mình sleep time để mô phỏng lại thôi nhé =))

```golang
func processUrl(url string) {
	fmt.Println(fmt.Sprintf("url %v", url))

	// processing
	time.Sleep(5 * time.Second)

	// done
	fmt.Println(fmt.Sprintf("url %v is done", url))
}
```

Khởi tạo 1 constant khai báo số lượng workers chạy đồng thời và thực hiện khởi chạy cho từng worker đứng đọc dữ liệu từ 1 channel.

```golang

const (
	NumberOfWorkers = 5
)

func crawl(ch chan string, wg *sync.WaitGroup) {
	for i := 0; i < NumberOfWorkers; i++ {
		wg.Add(1)
		go func() {
			defer wg.Done()
			for url := range ch {
				processUrl(url)
			}
			return
		}()
	}
}
```

Khi channel mà worker đọc dữ liệu được close và worker xử lý xong hết các message thì sẽ return nhé. Đoạn này nhớ là phải báo cho waitgroup done không là deadlock nhé.

Giờ tới hàm main xử lý push message vào channel và khởi chạy nhiều woker đồng thời xử lý tác vụ.

```golang
func main() {
	ch := make(chan string, NumberOfWorkers)
	var wg sync.WaitGroup

	// push the url to channel
	go func() {
		for i := 0; i < 1000; i++ {
			ch <- fmt.Sprintf("%v", i)
		}

		close(ch)
	}()

	// get data from channel and process crawl
	crawl(ch, &wg)

	wg.Wait()
	fmt.Println("Done...")
}
```
Mình khai báo 1 buffer channel với capacity là NumberOfWorkers. Ở đoạn này, mọi người có tự hỏi sao lại là NumberOfWorkers mà không phải số khác không =)) có thể nếu phỏng vấn mọi người sẽ bị hỏi về vấn đề này đó =)) Thì ở đây câu trả lời là tuỳ vào việc xử lý việc push message nhanh hay chậm và số lượng workers để xử lý. Ở đây có 3 TH

- Số lượng workers < số capacity -> workers có thể không xử lý không kịp -> các message được chứa trong buffer cho đến khi đầy. Nếu khai báo số capacity quá lớn -> tốn chi phí lưu trữ.
- Số lượng workers > số capacity -> có thể TH push message không kịp vào channel -> 1 số nhiều workers sẽ đứng chơi và đợi message được gửi tới channel.
- Số lượng workers = số capacity -> mỗi worker sẽ phụ trách 1 message. Nếu woker xử lý không kịp -> số lượng message trong buffer = số lượng workers hoặc nếu push message không kịp vào channel -> số lượng workers đứng chơi sẽ ít hơn.

-> Kết luận: Tuỳ trường hợp và tình huống mà khai báo số lượng capacity. Tuy nhiên, mình thấy ổn nhất là số lượng workers = số capacity.

Tiếp đến là đoạn close(ch) -> mình tin là sẽ có nhiều bạn nghĩ là close xong rồi là channel được giải phóng và các workers sẽ dừng lại luôn. Tuy nhiên, đọc vào function này thì có desctiption

> The close built-in function closes a channel, which must be either bidirectional or send-only. It should be executed only by the sender, never the receiver, and has the effect of shutting down the channel after the last sent value is received. After the last value has been received from a closed channel c, any receive from c will succeed without blocking, returning the zero value for the channel element.

Đại loại là func close này được dựng lên để close 1 channel nhận & gửi và send-only. Cái channel mà được close bị shut down sau khi message cuối cùng trong channel được nhận.

Reference code: [Source Golang](https://github.com/nguyenvantuan2391996/example-code/tree/master/crawl-urls-channel)


