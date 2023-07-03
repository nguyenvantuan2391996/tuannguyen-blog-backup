---
title: "Bot Slack sends daily leetCoding challenge"
seoTitle: "Bot Slack sends daily leetCoding challenge"
seoDescription: "Thuật toán là một tập hợp các quy tắc và quy trình logic được sử dụng để giải quyết một vấn đề hoặc thực hiện một nhiệm vụ. Nó là một bước quyết định hướng"
datePublished: Mon Jul 03 2023 02:30:39 GMT+0000 (Coordinated Universal Time)
cuid: cljm8us9b0h2bzznv058k1q4h
slug: bot-slack-sends-daily-leetcoding-challenge
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1687593722798/7a7cb79e-5cc9-440d-b39b-cd70b7b35c9b.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1687593799886/4317c919-7b3c-4a13-bc44-a53cef57e4ee.png
tags: golang, slack, webhooks, leetcode, daily-leetcode-challenge

---

# 1\. Giới thiệu tổng quan

Thuật toán là một tập hợp các quy tắc và quy trình logic được sử dụng để giải quyết một vấn đề hoặc thực hiện một nhiệm vụ. Nó là một bước quyết định hướng dẫn máy tính hoặc người dùng để thực hiện một loạt các thao tác theo một trình tự cụ thể.

Qua việc sử dụng thuật toán, chúng ta có thể giải quyết các vấn đề phức tạp, xử lý dữ liệu, tìm kiếm thông tin, tối ưu hóa quy trình, và thực hiện nhiều nhiệm vụ khác trong lĩnh vực khoa học máy tính và công nghệ thông tin.

![Sorting algorithm | Definition, Time Complexity, & Facts | Britannica](https://cdn.britannica.com/09/239909-050-573D293B/visualization-sorting-algorithms.jpg align="center")

Giờ đây đã được các công ty công nghệ áp lớn áp dụng thuật toán vào các bài test phỏng vấn như là một phần bắt buộc cho các Software Engineer. Có rất nhiều website giúp bạn luyện thuật toán:

* [LeetCode](https://leetcode.com): LeetCode cung cấp một bộ sưu tập lớn các bài tập thuật toán và câu hỏi phỏng vấn từ các công ty công nghệ hàng đầu. Bạn có thể giải các bài tập và kiểm tra đáp án của mình với các ngôn ngữ lập trình khác nhau.
    
* [HackerRank](https://www.hackerrank.com): HackerRank cung cấp nhiều bài tập thuật toán và lập trình cho người luyện tập. Bạn có thể tham gia vào các cuộc thi, giải các bài tập và xem xếp hạng của mình so với các thành viên khác.
    
* [CodeSignal](https://codesignal.com): CodeSignal cung cấp các bài tập thuật toán và phỏng vấn kỹ thuật, giúp bạn nâng cao kỹ năng lập trình và chuẩn bị cho quá trình tuyển dụng.
    
Phổ biến hơn cả trong số này là [LeetCode](https://leetcode.com). LeetCode là một trang web trực tuyến nổi tiếng dành cho việc luyện tập và nâng cao kỹ năng lập trình, đặc biệt là trong lĩnh vực thuật toán và phỏng vấn công nghệ thông tin. Tại LeetCode, bạn có thể tìm thấy một bộ sưu tập lớn các bài tập thuật toán và câu hỏi phỏng vấn được lấy từ các công ty công nghệ hàng đầu. Trang web cung cấp môi trường trực tuyến để bạn có thể viết và chạy code của mình với nhiều ngôn ngữ lập trình khác nhau, sau đó kiểm tra kết quả và đánh giá hiệu năng.

![LeetCode Add Two Numbers Solution Explained - Java - YouTube](https://i.ytimg.com/vi/aM4Iv7eEr2o/maxresdefault.jpg align="center")

# 2\. Ý tưởng

Mình làm việc từ 8h-8h30 cho đến 18h-18h30 hàng ngày. Công cụ trao đổi với mọi người trong team đa phần dùng Slack nên ở đây mình tạo bot trên Slack để tự động gửi cho mình bài daily leetcoding challenge vào 9h sáng hàng ngày:

* 9h hàng ngày, bot gọi API leetcode để lấy thông tin bài daily challenge hôm nay và sau đó lọc thông tin và tự động gửi vào Slack cho mình.
    

![Set Up A Cron Job In cPanel - WPQuickies](https://zpd.wordifysites.com/wp-content/uploads/2020/11/Replace-WP-Cron-With-A-cPanel-Cron-Job-WPQuickies-youtube.jpg align="center")

# 3\. Sequence diagram

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1687592280974/7751fc2b-b5f7-4fb0-affe-4c19b228dc58.png align="center")

**Note**: Các bước thực hiện của Bot mình đã mô tả ở biểu đồ tuần tự phía bên trên. Có 3 actor: Scheduler, LeetCode, Slack Webhook. Sau khi lấy dữ liệu từ Leetcode bot sẽ tạo message và gửi vào Slack Webhook.

# 3\. Tạo bot Slack

![Animated GIF](https://media2.giphy.com/media/v1.Y2lkPTc5MGI3NjExaXp0b3Y5ZDA5NzF6aWI2eDMzcnQ5MWlnaGh1NGNzMWJvOTdieG91ZyZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/YYXzsMW5RLBhI946xA/giphy.gif align="center")

Để tạo một con bot có thể gửi tin nhắn vào channel thì cần tạo một webhook trong Slack. Khi có trigger hành động thì con bot sẽ tự động post message tới channel thông qua webhook này. Cách tạo thì mọi người tham khảo link Incoming Webhook của Slack.

Để implement code bằng Go thì cũng đơn giản thôi chỉ cần generate theo format của Slack rồi gọi webhook. Slack có hướng dẫn từng bước mọi người [tham khảo đây nhé](https://api.slack.com/messaging/webhooks#:~:text=Incoming%20Webhooks%20are%20a%20simple,make%20the%20messages%20stand%20out.)

```Golang
func sendMessage(webhook string, message *model.SlackMessage) error {
    logrus.Info("Send message to webhook ", webhook)

    payload, err := json.Marshal(message)
    if err != nil {
        return err
    }
    req, err := http.NewRequest("POST", webhook, bytes.NewReader(payload))
    if err != nil {
        return err
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Set("Accept", "application/json")
    res, err := http.DefaultClient.Do(req)

    if err != nil {
        return err
    }

    defer func(Body io.ReadCloser) {
        err = Body.Close()
        if err != nil {
            return
        }
    }(res.Body)
    if res.StatusCode != http.StatusOK {
        if res != nil {
            bodyBytes, err := ioutil.ReadAll(res.Body)
            if err != nil {
                logrus.Warnf("response %v", string(bodyBytes))
                return err
            }
            return errors.New("notification failure")
        }
    }
    logrus.Info("successfully sent notification...")
    return nil
}
```

# 4\. Tạo cron job với Golang

Ở phần này nhanh thì mình dùng luôn thư viện trên github **robfig/cron/v3** được vote 10k starts để tạo cron job của Golang.

Cấu hình cron job: 9h sáng hàng ngày -&gt; CRON\_NOTIFY\_SUMMARY=0 9 \* \* \*

![Cron Jobs - How to Setup, Edit and Save them](https://cdn.webservertalk.com/wp-content/uploads/cron-jobs-656x410.png align="center")

Source code:

```Golang
c := cron.New()

// Notify summary 09:00 every day
	notifySummary, err := c.AddFunc(cfg.CronNotifySummary, func() {
		err = notifyBot.ProcessNotifyDailyLeetCodingChallenge(ctx)
		if err != nil {
			logrus.Warnf(constants.TaskErrorMessage, "ProcessNotifyDailyLeetCodingChallenge", err)
		}
	})
```

# 5\. Lấy thông tin leetcode

Ở trang leetcode bật F12 và bắt request lấy thông daily leetcoding. Ở đây leetcode họ dùng Graphql. Sau khi lấy được API mình implement client để gọi API lấy thông tin thôi.

Source code Golang:

```Golang
func GetDailyCodingChallenge(url string, params *model.ParamDailyCodingChallenge) (*model.DailyCodingChallenge, error) {
	logrus.Info("Get daily leetcoding challenge")

	req, err := http.NewRequest("POST", url, strings.NewReader(params.Payload))
	if err != nil {
		return nil, err
	}

	rand.Seed(time.Now().UnixNano())

	req.Header.Add("authority", "leetcode.com")
	req.Header.Add("accept", "*/*")
	req.Header.Add("accept-language", "en-US,en;q=0.9,vi;q=0.8,es;q=0.7")
	req.Header.Add("content-type", "application/json")
	req.Header.Add("origin", "https://leetcode.com")
	req.Header.Add("referer", "https://leetcode.com/problemset/all/")
	req.Header.Add("sec-ch-ua", "\"Google Chrome\";v=\"111\", \"Not(A:Brand\";v=\"8\", \"Chromium\";v=\"111\"")
	req.Header.Add("sec-ch-ua-mobile", "?0")
	req.Header.Add("sec-ch-ua-platform", "\"macOS\"")
	req.Header.Add("sec-fetch-dest", "empty")
	req.Header.Add("sec-fetch-mode", "cors")
	req.Header.Add("sec-fetch-site", "same-origin")
	req.Header.Add("user-agent", model.ArrayUserAgent[rand.Intn(len(model.ArrayUserAgent)-1)])

	res, err := http.DefaultClient.Do(req)

	if err != nil {
		return nil, err
	}

	defer func(Body io.ReadCloser) {
		err = Body.Close()
		if err != nil {
			logrus.Errorf("Close body fail %v", err)
		}
	}(res.Body)
	body, err := ioutil.ReadAll(res.Body)
	if res.StatusCode != http.StatusOK {
		if res != nil {
			bodyBytes, errReadAll := ioutil.ReadAll(res.Body)
			if errReadAll != nil {
				logrus.Warnf("response %v", string(bodyBytes))
				return nil, errReadAll
			}
			return nil, errors.New("get daily leetcoding challenge information failure")
		}
	}

	dailyCodingChallenge := &model.DailyCodingChallenge{}
	err = json.Unmarshal(body, dailyCodingChallenge)
	if err != nil {
		return nil, err
	}
	logrus.Info("successfully get daily leetcoding challenge information...")

	return dailyCodingChallenge, nil
}
```

# 6\. Bot Slack gửi bài daily

Sau khi khởi tạo cron job chạy lúc 9AM hàng ngày và lấy được thông tin từ leetcode rồi thì việc cuối cùng chúng ta chỉ cần truyền đủ thông tin input. Khi nhận được response thì fill data vào message rồi gọi API webhook Slack là hoàn thành con bot rồi đó.

Source code:

```Golang
func (b *BotNotify) ProcessNotifyDailyLeetCodingChallenge(ctx context.Context) error {
	logrus.Infof(constants.BeginningTaskMessage, "ProcessNotifyDailyLeetCodingChallenge", ctx.Value(constants.XRequestID))

	dailyCodingChallenge, err := leetcode.GetDailyCodingChallenge(model.URLGraphql, &model.ParamDailyCodingChallenge{
		Payload: model.Payload,
	})
	if err != nil {
		logrus.Errorf("get daily leetcoding challenge information from leetcode.com fail %v", err)
		return err
	}

	textMsgDailyCodingChallenge := dailyCodingChallenge.ToTextMessageDailyCodingChallenge()

	message := &model.SlackMessage{
		Text:        fmt.Sprintf(model.FormatTextLeetCode, b.cfg.TagsSlackLeetCode, "This is the leetcoding challenge for today: "+dailyCodingChallenge.Data.ActiveDailyCodingChallengeQuestion.Question.Title),
		Attachments: textMsgDailyCodingChallenge.ToAttachment(),
	}

	return client.SendMessageSlack(b.cfg.WebhookSlackLeetCode, message)
}
```

# 7\. Một số hình ảnh kết quả

Sau khi thực hiện các bước ở trên thì thành quả chúng ta nhận được sẽ như sau. Dưới đây là 1 số hình ảnh

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1687593134074/f27adf4a-dbec-44d1-8d9d-cdfe90ba3e36.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1687593139562/a7b8404f-62b1-40fc-b3f3-4ec3ec7a6a05.png align="center")

# 8\. Kết luận

Sau quá trình giới thiệu cũng như hướng dẫn làm một con bot đơn giản phục vụ cho cuộc sống cá nhân hàng ngày, mình hi vọng mọi người sẽ thấy thú vị và bớt nhàm chán sau những ngày code căng thẳng, mệt mỏi.

Source code : github.com/nguyenvantuan2391996/be-project

Tiện thể TIKI đang có chương trình refer giới thiệu mua bảo hiểm xe máy, ô tô, sức khoẻ AIA,... Cùng tham gia để được nhận thật nhiều xu từ chương trình nhé

Link chi tiết: https://tiki.vn/khuyen-mai/ban-tot-gioi-thieu

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1687593529932/42aa3b71-1f1b-4903-9a3c-b90a895c39c8.png align="center")