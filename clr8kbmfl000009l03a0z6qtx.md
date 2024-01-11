---
title: "Shortener Link - Xây dựng hệ thống từ A-Z"
seoTitle: "Shortener Link - Xây dựng hệ thống từ A-Z"
seoDescription: "Chúng ta có thể thấy rút gọn link không chỉ giúp tối ưu hóa việc quản lý đường dẫn mà còn mang lại những trải nghiệm kết nối đơn giản, an toàn và linh hoạt."
datePublished: Thu Jan 11 2024 02:00:33 GMT+0000 (Coordinated Universal Time)
cuid: clr8kbmfl000009l03a0z6qtx
slug: shortener-link-xay-dung-he-thong-tu-a-z
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1704880999885/61a0276c-c9d9-425c-8e2a-bfb7eff82e2b.avif
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1704881070118/5a51ff2d-ff47-43f9-bfe0-25e108434487.avif
tags: js, javascript, golang, html5, url-shortener, shortener

---

# I. Giới thiệu

Việc chia sẻ liên kết trực tuyến trở nên phổ biến hơn bao giờ hết trong thế giới kết nối nhanh chóng của chúng ta. Nhưng liệu có cách nào để biến đường dẫn dài và phức tạp thành những đoạn mã ngắn, dễ nhớ mà vẫn giữ được tính hữu ích và an toàn? Đó chính là nhiệm vụ của dịch vụ rút ngắn liên kết.

Một số lợi ích cơ bản khi dùng **shortener link**

1. **Đơn Giản Hóa Liên Kết, Tăng Tính Thân Thiện:** Một liên kết dài không chỉ làm cho tin nhắn của bạn trở nên rối bời mà còn khó nhớ. Dịch vụ rút ngắn liên kết giúp bạn biến đổi những đường dẫn không thân thiện thành những mã ngắn, dễ nhớ, tạo điều kiện thuận lợi cho việc chia sẻ trên mọi nền tảng truyền thông.

2. **Quản Lý và Theo Dõi Hiệu Suất:** Một trong những lợi ích quan trọng nhất của việc sử dụng dịch vụ rút ngắn liên kết là khả năng theo dõi hiệu suất. Bạn có thể xem thông tin chi tiết về số lần nhấp, nguồn gốc của người xem và thậm chí là thời gian mà liên kết được truy cập. Điều này giúp bạn đánh giá độ phổ biến và hiệu suất của liên kết.

3. **An Toàn và Bảo Mật Tuyệt Đối: ** Dịch vụ rút ngắn liên kết không chỉ mang lại sự thuận tiện mà còn đảm bảo tính an toàn cho người sử dụng. Những dịch vụ chất lượng sẽ cung cấp các biện pháp bảo mật mạnh mẽ, bảo vệ thông tin cá nhân và ngăn chặn sự lạm dụng của liên kết.

4. **Tùy Chọn Tùy Chỉnh và Quản Lý Linh Hoạt:** Không chỉ giới hạn ở việc rút ngắn liên kết, mà còn có khả năng tùy chỉnh và quản lý chúng. Bạn có thể đặt tên, thay đổi địa chỉ, và quản lý tất cả các liên kết của mình thông qua giao diện quản lý dễ sử dụng.

5. **Ứng Dụng Đa Dạng và Linh Hoạt:** Dịch vụ rút ngắn liên kết không chỉ làm việc trên các liên kết web. Nó cũng có thể được tích hợp trong các chiến dịch tiếp thị, quảng cáo, và thậm chí là trong các ứng dụng di động, tạo ra trải nghiệm kết nối mượt mà và linh hoạt.

6. **Tiết Kiệm Dung Lượng và Tăng Tốc Độ Tải Trang:** Việc sử dụng liên kết ngắn không chỉ mang lại sự gọn gàng mà còn giúp giảm dung lượng trang web và tăng tốc độ tải trang. Điều này có ảnh hưởng lớn đến trải nghiệm người dùng, đặc biệt là trên các thiết bị di động.

![tinyurl](https://cdn.yespo.io/photos/shares/Blog/article/linkshorteners/image3.webp align="center")

# II. Yêu cầu hệ thống

## 1. Functional requirements

- Với một URL dài, hệ thống rút gọn link sẽ tạo một định danh ngắn hơn và duy nhất cho URL đó. Ví dụ: **https://tuannguyenhust.hashnode.dev** sẽ có định danh tương ứng là **MoSb0iT**
- Khi người dùng truy cập vào một liên kết rút gọn, hệ thống sẽ chuyển hướng và truy cập đến liên kết ban đầu.
- Liên kết rút gọn sẽ hết hạn sau một khoảng thời gian mặc định tiêu chuẩn.

## 2. Non-Functional requirements

- Hệ thống cần có tính available cao. Nếu dịch vụ ngừng hoạt động, tất cả các chuyển hướng URL sẽ bắt đầu không thành công.
- Việc chuyển hướng URL sẽ diễn ra trong thời gian thực với độ trễ tối thiểu.
- Link rút gọn nên không thể đoán trước được.
- Nếu không tìm thấy link rút gọn tồn tại trong cơ sở dữ liệu thì hệ thống sẽ redirect sang một trang **404 not found**.

# III. Thiết kế hệ thống

## 1. Thiết kế cơ sở dữ liệu

Dựa vào yêu cầu bài toán chúng ta tạo ra 1 bảng **urls**: id, long_url, short_url, expired_time. Chúng ta sẽ đánh index trường **short_url** bởi vì sẽ thường xuyên query column này để lấy ra thông tin.

```
URL max length in the address bar is about 2048 characters
```
Độ dài kí tự giới hạn của một url là 2048 vậy để cover hết các case thì ta tạo long_url với kiểu là **varchar(2048)**

```sql
CREATE TABLE `urls` (
  `id` int NOT NULL AUTO_INCREMENT,
  `long_url` varchar(2048) CHARACTER SET utf8 COLLATE utf8_general_ci DEFAULT NULL,
  `short_url` varchar(100) CHARACTER SET utf8 COLLATE utf8_general_ci DEFAULT NULL,
  `expired_time` datetime DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `short_url_idx` (`short_url`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb3;
```
**Note**: Để xử lý nhanh chóng mình chỉ tạo ra một bảng urls. Còn các chức năng phân quyền, thông tin user,... thì mình skip nhé ^^

## 2. Thiết kế API

Chúng ta sẽ có hai API bao gồm:
1. **Generate the short url:** API này sẽ yêu cầu hệ thống tạo ra link rút gọn.
```curl
curl --location 'localhost:3000/generate-short-url' \
--header 'Content-Type: application/json' \
--data '{
    "url": "https://www.youtube.com/watch?v=KhP11ESdV9A&ab_channel=SBCMemories"
}'
```

2. **Redirect url:** API này sẽ lấy thông tin dữ liệu từ hệ thống và điều hướng tới link gốc. Response API sẽ trả về 301 và redirect tới link gốc và html template 404 not found nếu không tìm được thông tin trong cơ sở dữ liệu.
```
curl --location 'http://localhost:3000/MoSb0iT'
```

## 3. Thiết kế hệ thống

![tinyurl](https://images.viblo.asia/df986e17-4cdd-425e-8ee0-487e11f9841b.png align="center")

Flow hệ thống được tóm tắt như sau:
1. Người dùng nhấp vào liên kết URL ngắn: **http://localhost:3000/MoSb0iT**
2. Bộ cân bằng tải chuyển tiếp yêu cầu đến máy chủ web.
3. Nếu URL ngắn đã có trong bộ đệm, trả lại trực tiếp URL dài và thời hạn URL.
4. Nếu shortURL không có trong bộ đệm, tìm longURL từ cơ sở dữ liệu slave. Nếu nó không có trong cơ sở dữ liệu trả về **html template 404 not found**.
5. LongURL được trả về cho người dùng và redirect sang url này.

## 4. Thiết kế hàm hash

Trong việc xây dựng hệ thống shortener link, việc thiết kế hàm hash đóng vai trò quan trọng để tạo ra các mã ngắn và duy nhất. Chúng ta có thể sử dụng hàm hash base62 và MD5 để đảm bảo tính ngắn gọn, duy nhất và an toàn trong quá trình rút ngắn liên kết.

- **Hàm hash base62** là một lựa chọn phổ biến để tạo mã ngắn và dễ nhớ. Base62 sử dụng 62 ký tự (bao gồm chữ cái in hoa, chữ cái thường, và các chữ số từ 0 đến 9), giảm độ dài so với hệ thống hex hoặc base64, giúp tối ưu hóa không gian ký tự.

- **MD5 (Message Digest Algorithm 5)** là một hàm hash phổ biến và đã được sử dụng rộng rãi. Mặc dù không còn được coi là an toàn đối với mục tiêu bảo mật cao, nhưng MD5 vẫn đủ tốt để tạo ra các mã ngắn và không thể dự đoán.

Rất nhiều bài viết đều sử dụng 2 thuật toán mã hoá này. Vậy trong bài viết này của mình sẽ xây dựng khác đi một chút dùng **nanoid**

**Ưu Điểm và Ứng Dụng Trong Thực Tế:**

1. **Tăng Hiệu Suất:** Nhờ vào kích thước nhỏ và sự đơn giản, Nanoid tăng hiệu suất cho quá trình tạo ID trong ứng dụng của bạn.

2. **Phù Hợp Với Các Dự Án Nhỏ và Lớn:** Dù là dự án nhỏ cá nhân hay hệ thống lớn với hàng triệu người dùng, Nanoid vẫn là một lựa chọn phù hợp.

3. **Flexible:** Nanoid cũng hỗ trợ các tùy chọn để điều chỉnh độ dài của ID và cài đặt việc sử dụng bảng ký tự tùy chỉnh.

# IV. Xây dựng back end service

## 1. Khởi tạo kết nối tới cơ sở dữ liệu

```
func initDatabase() (*gorm.DB, error) {
	dsn := "root:admin123@tcp(127.0.0.1:3306)/shortner_url?parseTime=true&loc=Local"
	db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		log.Fatal("failed to open connect to database:", err)
		return nil, nil
	}

	return db, nil
}
```

## 2. Khởi tạo kết nối tới redis cache

```
func initRedis(redisUrl string) (*redis.Client, error) {
	opts, err := redis.ParseURL(redisUrl)
	if err != nil {
		log.Fatal("failed to connect redis:", err)
		return nil, nil
	}

	opts.PoolSize = 30
	opts.ReadTimeout = 5 * time.Second
	opts.WriteTimeout = 5 * time.Second
	opts.Username = ""

	redisClient := redis.NewClient(opts)

	cmd := redisClient.Ping(context.Background())
	if cmd.Err() != nil {
		log.Fatal("failed to ping redis: ", cmd.Err())
		return nil, nil
	}

	return redisClient, nil
}
```
## 3. Tương tác CSDL và redis

Xây dựng function tương tác với database và redis: tạo bản ghi urls, lấy thông tin bản ghi trong cơ sở dữ liệu.

**Note**:
- Khi tạo 1 record thành công trong cơ sở dữ liệu, chúng ta sẽ cache lại thông tin này ở redis để giảm tải cho việc lấy thông tin từ CSDL.
- Khi lấy dữ liệu, chúng ta sẽ kiểm tra trong redis nếu có thì return luôn ngược lại thì query tìm trong CSDL.

```
func (h *HandlerAPI) Create(ctx context.Context, record *URL) error {
	err := h.DBClient.Create(record).Error
	if err != nil {
		return err
	}

	data, errMarshal := json.Marshal(record)
	if errMarshal != nil {
		return nil
	}
	_, err = h.RedisClient.Set(ctx, record.ShortURL, data, ExpiredHours*time.Hour).Result()
	if err != nil {
		return err
	}

	return nil
}

func (h *HandlerAPI) GetURLByQueries(ctx context.Context, queries map[string]interface{}) (*URL, error) {
	var record *URL

	// get from redis
	result, errRedis := h.RedisClient.Get(ctx, fmt.Sprintf("%s", queries["short_url"])).Result()
	if errRedis == nil {
		errUnmarshal := json.Unmarshal([]byte(result), &record)
		if errUnmarshal != nil {
			return nil, errUnmarshal
		}
		return record, nil
	}

	err := h.DBClient.WithContext(ctx).Where(queries).First(&record).Error
	if err != nil {
		return nil, err
	}

	return record, nil
}
```

## 4. Hàm hash
Hàm hash định danh cho short link sử dụng nanoid

```
func GenerateURL() (string, error) {
	key, err := nanoid.GenerateString(Alphabet, NumberOfCharacters)
	if err != nil {
		return "", err
	}

	return key, nil
}
```

## 5. API

Xây dựng **middleware** xử lý dữ liệu, verify,... trước khi vào xử lý request API. Ở đây hiện tại mình skip authen, author,... nên chỉ allow CORS và skip nếu request method OPTIONS

```
func Middleware(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		w.Header().Set("Access-Control-Allow-Origin", "*")
		w.Header().Set("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, OPTIONS")
		w.Header().Set("Access-Control-Allow-Headers", "Content-Type, Origin, Authorization")
		w.Header().Set("Access-Control-Allow-Credentials", "true")

		// Handle preflight requests
		if r.Method == "OPTIONS" {
			w.WriteHeader(http.StatusOK)
			return
		}

		next.ServeHTTP(w, r)
	})
}
```

**API generate short link:** Nếu trong CSDL có tồn tại short link thì retry lại **10 lần**. Nếu quá số lần retry thì trả về lỗi cho người dùng. Việc xử lý logic thì chúng ta kết hợp với các function được xây dựng ở phía trên.

```
func (h *HandlerAPI) generateShortUrlHandler(w http.ResponseWriter, r *http.Request) {
	if r.Method != http.MethodPost {
		http.Error(w, "Method not allowed", http.StatusMethodNotAllowed)
		return
	}

	// Read the body request
	body, err := ioutil.ReadAll(r.Body)
	if err != nil {
		http.Error(w, "Error reading request body", http.StatusBadRequest)
		return
	}

	var request RequestData
	err = json.Unmarshal(body, &request)
	if err != nil {
		http.Error(w, "Error unmarshal request body", http.StatusBadRequest)
		return
	}

	// Generate short link by using nanoid
	shortLink := ""
	for i := 0; i < MaxRetryGenerate; i++ {
		tmp, errGenerate := GenerateURL()
		if errGenerate != nil {
			continue
		}

		_, errGet := h.GetURLByQueries(r.Context(), map[string]interface{}{
			"short_url": Domain + tmp,
		})
		if errors.Is(errGet, gorm.ErrRecordNotFound) {
			shortLink = Domain + tmp
			break
		}
	}

	if len(shortLink) == 0 {
		http.Error(w, "Error generate short url", http.StatusBadRequest)
		return
	}

	// create record
	err = h.Create(r.Context(), &URL{
		LongURL:     request.URL,
		ShortURL:    shortLink,
		ExpiredTime: time.Now().Add(DefaultExpiredTime),
	})
	if err != nil {
		http.Error(w, "Error generate short url", http.StatusBadRequest)
		return
	}

	w.WriteHeader(http.StatusOK)
	_, err = fmt.Fprintf(w, shortLink)
	if err != nil {
		return
	}
}
```

**API redirect url:** Chuyển hướng tới link gốc nếu không tìm được link gốc với short link tương ứng thì trả về 1 html template 404 not found.

```
func (h *HandlerAPI) redirectHandler(w http.ResponseWriter, r *http.Request) {
	if r.Method != http.MethodGet {
		http.Error(w, "Method not allowed", http.StatusMethodNotAllowed)
		return
	}

	longURL, err := h.GetURLByQueries(r.Context(), map[string]interface{}{
		"short_url": Domain + strings.TrimLeft(r.RequestURI, "/"),
	})
	if err != nil || (longURL != nil && time.Now().After(longURL.ExpiredTime)) {
		w.Header().Set("Content-Type", "text/html")
		_, err := fmt.Fprint(w, PageNotFound)
		if err != nil {
			return
		}
		return
	}

	http.Redirect(w, r, longURL.LongURL, http.StatusMovedPermanently)
}
```

![404 not found page](https://images.viblo.asia/10967225-b098-405d-8114-8df48ac3f4f7.png align="center")

# V. Xây dựng front end

Việc xây dựng front end cũng khá simple. Dựng 1 giao diện có 2 ô input và button submit để generate link.

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Design url shortener</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/mdbootstrap/4.2.0/css/mdb.css">
</head>
<body>
<nav class="navbar navbar-inverse">
    <div class="container">
    </div>
</nav>
<p class="text-center">Enter any long URL Lets shorten it!</p>
<div class="form-group" style="width: 50%; margin-left: 25%; text-align: -webkit-center;">
    <label for="longURL"></label>
    <input class="form-control" type="text" id="longURL" placeholder="Enter URL" required>
    <label for="shortenURL"></label>
    <input class="form-control" type="text" id="shortenURL" placeholder="Short URL">
    <button class="btn btn-success" type="submit" id="shorten" onclick="handleShortURL()">SHORTEN</button>
</div>
</body>
</html>
```

![home page](https://images.viblo.asia/8a975428-edbc-4d90-ba7b-76c6316177cf.png align="center")

Xử lý JavaScript gọi API phía Back-end. Để cho simple mình sẽ dùng luôn fetch có sẵn của JavaScript. Rất đơn giản chỉ call API và xử lý hiển thị lên giao diện và alert nếu có lỗi.

```
<script>
    const DOMAIN_SHORT_URL = "http://localhost:3000"
    async function handleShortURL() {
        let longURL = document.getElementById("longURL").value
        // call the api to generate short url
        const myHeaders = new Headers();
        myHeaders.append("Content-Type", "application/json");
        const raw = JSON.stringify({
            "url": longURL
        });
        const requestOptions = {
            method: 'POST',
            headers: myHeaders,
            body: raw,
            redirect: 'follow'
        };
        await fetch(DOMAIN_SHORT_URL + "/generate-short-url", requestOptions).then(response => {
            if (!response.ok) {
                alert("Something went wrong")
            }
            return response.text()
        }).then(result => {
            document.getElementById("shortenURL").value = result
        }).catch(error => {
            console.log('error', error)
            alert("Something went wrong")
        });
    }
</script>
```

# VI. Kết luận

Một số improve để tối ưu hoá hệ thống khi số lượng người dùng, request tăng cao

1. **Rate limiter:** Một vấn đề bảo mật tiềm ẩn mà chúng ta có thể gặp phải là việc người dùng gửi một số lượng lớn yêu cầu rút ngắn URL. Rate limiter giúp lọc các yêu cầu dựa trên địa chỉ IP hoặc các quy tắc lọc khác. Hoặc đơn giản chúng ta sẽ giới hạn request hàng ngày cho user.
2. **Database scaling:** Sử dụng database replication và sharding database. Ví dụ bảng urls chứa thông tin long, short url -> số lượng bản ghi sẽ rất lớn -> chúng ta có thể sharding bảng này dựa vào: id + long_url + short_url.
3. **Remove old link:** Tạo ra job hàng ngày đi xoá bỏ các bản ghi cũ hoặc bản ghi hết hạn.

Đó là 1 vài options để tối ưu. Tuỳ vào biz logic phát triển chúng ta sẽ có các cách để triển khai việc tối ưu này.

Cuối cùng, chúng ta có thể thấy rút gọn link không chỉ giúp tối ưu hóa việc quản lý đường dẫn mà còn mang lại những trải nghiệm kết nối đơn giản, an toàn và linh hoạt cho người sử dụng. Với những tiện ích này, nó trở thành công cụ không thể thiếu trong hộp công cụ của các bạn marketing hay những người sử dụng short link trong việc đăng sản phẩm. Bài viết này hy vọng sẽ giúp bạn hiểu rõ hơn về cơ chế hoạt động và cách áp dụng kỹ thuật này trong bài toán Shortener link.

* Source code: [Example with Golang, JavaScript](https://github.com/nguyenvantuan2391996/example-code/tree/master/design-url-shortener)