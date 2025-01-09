---
title: "Facial Recognition - Hệ thống nhận diện khuôn mặt"
seoTitle: "Facial Recognition - Hệ thống nhận diện khuôn mặt"
seoDescription: "Nhận diện khuôn mặt - mình thấy nó đang được ứng dụng rất nhiều và khả năng đáp ứng về độ chính xác cũng như ổn định đã stable khá nhiều rồi."
datePublished: Thu Jan 09 2025 17:19:00 GMT+0000 (Coordinated Universal Time)
cuid: cm5pleuls000l09lg0iai8qa9
slug: facial-recognition-he-thong-nhan-dien-khuon-mat
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1736442553942/6983a930-4378-464b-ad82-7e77f3f24007.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1736442799224/0017769e-ce6f-4b0c-a566-ba40e1599c85.jpeg
tags: javascript, python, golang, pgvector, nhan-dien-khuon-mat, deepface

---

**Hà Nội** 2025 - Wish all the best will come to you!

![Happy New Year 2025" Images – Browse 34,680 Stock Photos, Vectors, and  Video | Adobe Stock](https://t4.ftcdn.net/jpg/09/31/30/21/360_F_931302121_jhwmfOKfW6WMs7GLUUx59aacNnXm1IxP.jpg align="center")

# **I. Giới thiệu**

Dạo gần đây, AI đang nổi lên mạnh mẽ với mô hình ngôn ngữ lớn LLM ( [Large Language Model](https://200lab.io/blog/large-language-model-la-gi/?srsltid=AfmBOoqMLs4X1At8-IkLtdgJWMsRRWT5Xi4hKI74_66Sh0pADy9qNyLX) ). Chúng nhận câu hỏi từ người dùng và tự suy luận, tìm kiếm dữ liệu trong model data khổng lồ rồi đưa ra câu trả lời. Nhờ sự thông minh và biết suy luận mà nó được các công ti, tổ chức đầu tư mạnh mẽ để cố gắng làm chủ công nghệ. Tuy nhiên, cá nhân mình thấy mô hình này có nhược điểm chí mạng là latency. Hiện tại, latency để đưa ra câu trả lời khá là cao và chi phí để scale, data cũng là 1 bài toán khó.

Trong lĩnh vực AI không chỉ có LLM mà còn có các mô hình, model truyền thống hiện tại - đã được tối ưu, open-source khá là nhiều để đáp ứng nhu cầu phần mềm. Chúng đã được ứng dụng rộng rãi xung quanh cuộc sống chúng ta và mới gần đây [Giám sát và xử lý vi phạm Trật tự an toàn giao thông](https://solutions.viettel.vn/vi/vi/chinh-phu-so/giam-sat-va-xu-ly-vi-pham-trat-tu-an-toan-giao-thong-trafficid.html) đã được áp dụng ^^

Một bài toán nữa đó là **nhận diện khuôn mặt** - mình thấy nó đang được ứng dụng rất nhiều và khả năng đáp ứng về độ chính xác cũng như ổn định đã stable khá nhiều rồi. Lướt web vài vòng thì thấy nó được ứng dụng vào việc chấm công khi đi làm khá hay thay cho việc check vân tay còn nhiều hạn chế. Search trên các sàn thương mại điện tử mình thấy các máy giao động từ 2-10 triệu =)) rẻ thật. Tuy nhiên, các máy này đều có hạn chế về số lượt chấm công và số khuôn mặt đăng kí hệ thống.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735483214345/270311c9-a277-4478-b065-8b4f6d0eaa2f.png align="center")

Dạo này cuối năm, công việc cũng vào giai đoạn nghiệm thu nên thời gian này không bận lắm nên quyết định implement thử 1 cái source nhỏ về cái hệ thống này. Ở bài viết này, mình sẽ hướng dẫn mọi người từng bước cách hoạt động và chạy thử trên ứng dụng web.

**Note**: Source code sẽ chỉ implement những base logic: đăng kí khuôn mặt, tìm khuôn mặt. Việc log chấm công hay các logic nghiệp vụ khác mọi người có thể tự clone về rồi code thêm nhé =))

Bạn nào start-up hay công ty nhỏ có thể clone về thêm chút rồi apply cho công ti cho **fancy** =)) chỉ cần 1 server hay máy tính cá nhân ok chút là chạy phà phà rồi :D

# **II. Thành phần hệ thống**

## 1\. PGVector

![pgvector extends PostgreSQL as a nice vector database for generative AI](https://www.ewota.com/files/inline-images/pgvector_ai.png align="center")

PGVector là 1 extension của Postgresql - nó có kiểu dữ liệu **vector** cho phép chúng ta so sánh khoảng cách để từ độ tương tự giữa các vector. **PGVector** thì support 3 loại khoảng cách

* **Euclidean Distance**: Khoảng cách theo đường chim bay quá quen thuộc với chúng ta từ hồi cấp 2 rồi.
    
* **Inner Product**: Đo sự tương quan giữa hai vector bằng công thức tổng tích các thành phần của 2 vector
    
* **Cosine Similarity**: Đo góc giữa hai vector trong không gian. Độ tương tự này thì cấp 3 quen thuộc :D
    

**Note**: Các công thức hay chi tiết về các khoảng cách, độ tương đồng các bạn có thể research trên mạng đã nói rất nhiều và chi tiết rồi :D

Có khá nhiều loại database dùng để search vector. Bên dưới là 5 database tốt nhất trong 2025 dùng cho việc này. Mình chọn **PGVector** đơn giản vì dùng nó làm database lưu trữ dữ liệu luôn :D còn các database khác mọi người có thể thử và tự đưa ra đánh giá nhé!

Link: [https://www.datacamp.com/blog/the-top-5-vector-databases](https://www.datacamp.com/blog/the-top-5-vector-databases)

## 2\. Deep Face

![GitHub - serengil/deepface: A Lightweight Face Recognition and Facial  Attribute Analysis (Age, Gender, Emotion and Race) Library for Python](https://raw.githubusercontent.com/serengil/deepface/master/icon/model-portfolio-20240316.jpg align="center")

Dùng AI mà liên quan đến face thì không thể thiếu được để library open-source này rồi :D Trên github được hơn 15k stars đủ thấy độ uy tín rồi. Mọi người có thể đọc thêm ở link dưới nhé. Document cách sử dụng rất chi tiết.

Link: [https://github.com/serengil/deepface](https://github.com/serengil/deepface)

| Model | Measured Score | Declared Score |
| --- | --- | --- |
| Facenet512 | 98.4% | 99.6% |
| Human-beings | 97.5% | 97.5% |
| Facenet | 97.4% | 99.2% |
| Dlib | 96.8% | 99.3 % |
| VGG-Face | 96.7% | 98.9% |
| ArcFace | 96.7% | 99.5% |
| GhostFaceNet | 93.3% | 99.7% |
| SFace | 93.0% | 99.5% |
| OpenFace | 78.7% | 92.9% |
| DeepFace | 69.0% | 97.3% |
| DeepID | 66.5% | 97.4% |

Deep face có nhiều models - độ chính xác đo được và công bố đều có thông tin như bảng trên. Ở đây mình chọn model **ArcFace**. Trong code các bạn có thể tự truyền tên model và so sánh nhé :D

## 3\. Minio storage

![How to Set Up Minio Object Storage on Linux with Systemd - DEV Community](https://media2.dev.to/dynamic/image/width=1000,height=420,fit=cover,gravity=auto,format=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2F1kco4frqn1sh3y3umvye.png align="center")

**Minio** là 1 storage lưu trữ dữ liệu: ảnh, video, âm thanh,… Được thiết kế để hoạt động hiệu quả trên các máy chủ tiêu chuẩn và cung cấp khả năng lưu trữ dữ liệu nhanh, an toàn, và có tính mở rộng cao. Nó có vài đặc điểm nổi bật dưới đây:

* **MinIO** tương thích hoàn toàn với API của Amazon S3, giúp các ứng dụng sử dụng **MinIO** có thể dễ dàng di chuyển lên **Amazon S3** (hoặc ngược lại).
    
* **MinIO** có thể chạy trên máy chủ cục bộ, trong môi trường container (Docker, Kubernetes) hoặc trên các đám mây công cộng như AWS, Azure, GCP.
    
* MinIO có mã nguồn mở, cho phép tùy chỉnh và sử dụng miễn phí.
    

Link trang chủ Minio → [https://min.io/](https://min.io/)

## 4\. Ngôn ngữ sử dụng

![Go vs Python — Bitfield Consulting](https://images.squarespace-cdn.com/content/v1/5e10bdc20efb8f0d169f85f9/1601909135781-K9ZBZ584ZUILX572P0HS/python-vs-go-slim.png align="center")

* **Golang**: là một ngôn ngữ lập trình mã nguồn mở được phát triển bởi Google. Như các bạn biết rồi Go hiệu suất cao, đơn giản nên mình dùng để build phần BE xử lý logic
    
* **Python**: là một ngôn ngữ lập trình mã nguồn mở, đa năng, và cấp cao được thiết kế bởi Guido van Rossum vào năm 1991. Dính đến AI thì Python luôn là sự lựa chọn hàng đầu rồi :D vì nó support hầu hết tất cả mọi thứ, libray thì nhiều vô kể và đặc biệt dễ học vì nó gần gũi với ngôn ngữ con người nhất ( Quan điểm cá nhân ) :D
    
* **JavaScript**: là một ngôn ngữ lập trình đa năng, mã nguồn mở, và động được sử dụng chủ yếu trong lập trình web. JavaScript được thiết kế bởi Brendan Eich và ra mắt vào năm 1995. Làm FE mà k đụng đến JS là không thể rồi :D
    

Phía trên là các công cụ, ngôn ngữ mình sẽ sử dụng để build hệ thống

# **III. Cách thức hệ thống hoạt động**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736355224077/92ee28ba-28a2-43d3-8261-9110aee1e114.png align="center")

Ở đây, mình vẽ nhanh flow hoạt động của hệ thống nên không được đẹp nhé :D nhưng nói chung nó sẽ hoạt động như trên

* Front-end gửi thông tin ảnh lên Back-end.
    
* BE gọi AI service để detect khuôn mặt → gọi vào thư viện **deepface** → extract vector image khuôn mặt.
    
* BE thêm thông tin hoặc search trong **PGVector** với input là vector mà AI service trả về.
    
* Lưu thông tin ảnh lên **MinIO.**
    

# **IV. Xây dựng hệ thống**

## 1\. AI service

Như đã giới thiệu ở thành phần hệ thống thì lõi AI mình dùng open-source deepface → Link: [https://github.com/serengil/deepface](https://github.com/serengil/deepface)

For sure! Build AI thì không thoát được khỏi Python rồi nên AI service mình dùng deepface và viết thêm api để gọi AI xử lý và trả kết quả. Mình dùng thư viện **fast-api** của Python để build rest api

Mọi người có thể tham khảo source ở github của mình nhé → [https://github.com/nguyenvantuan2391996/example-code/tree/master/face-recognition-attendance/face-extraction](https://github.com/nguyenvantuan2391996/example-code/tree/master/face-recognition-attendance/face-extraction)

```python
def extract_face_vector(image_input, model_name) -> np.ndarray:
    img = cv2.cvtColor(image_input, cv2.COLOR_RGB2BGR)
    embedding = DeepFace.represent(img, model_name=model_name)
    if embedding is not None and len(embedding) > 0:
        return embedding[0]["embedding"]
    return np.empty(())


@app.post("/api/v1/extract/")
async def extract(file: UploadFile):
    try:
        file_content = await file.read()
        image_preprocess = Image.open(BytesIO(file_content))
        vector = extract_face_vector(np.array(image_preprocess), "ArcFace")

        return {
            "vector": vector,
        }
    except Exception as e:
        return {"error": f"System error. {str(e)}"}
```

Mình phải công nhận 1 điều là code **Python** thực sự ngắn vì tất cả mọi thứ đã có thư viện lo =)) Ở đây mình khai báo 1 api nhận đầu vào là 1 ảnh → sử dụng deepface để **extract** ảnh đó thành 1 vector có 512 chiều → response trả lại

Ở đây mình chỉ cần extract ra được vector. Mọi tham số như: mặt giả, đeo kính,… mọi người có thể xem thêm mô tả các tham số trong function và document trên github đã giải thích rất chi tiết :D

## 2\. Back-end service

Ở phần BE thì mình ưu tiên build bằng Golang vì nó nhẹ và chạy nhanh :D kiến trúc source code thì mình cũng apply theo **Clean Architecture**. Bạn nào chưa biết thì có thể đọc tham khảo ở link sách này → [https://github.com/ropalma/ICMC-USP/blob/master/Book%20-%20Clean%20Architecture%20-%20Robert%20Cecil%20Martin.pdf](https://github.com/ropalma/ICMC-USP/blob/master/Book%20-%20Clean%20Architecture%20-%20Robert%20Cecil%20Martin.pdf)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736351394507/24746ac3-12ca-45fa-a4b2-2bc6e2896d6b.png align="center")

* **build**: Dùng để lưu env và các phần triển khai, cài đặt thành phần sử dụng trong hệ thống
    
* **common**: Lưu các thành phần client gọi bên thứ 3, biến, model database dùng chung,…
    
* **handler**: Như cái tên thôi :D thư mục dùng để hứng request từ client, validate, parse, middlware,… trước khi request đi vào xử lý bên trong hệ thống
    
* **internal**: Bên trong internal được chia các domains cụ thể và tầng infrastructure. Các domain này sẽ xử lý logic nghiệp vụ và gọi xuống infrastructure tương tác database, redis,…
    
* **static**: Thư mục lưu giao diện ứng dụng. Mình không phải dân chuyên FE nên viết luôn vào source của BE. Lúc run thì mình dùng luôn server BE để gọi api → view ra UI và xử lý logic :D
    

Các thành phần còn lại: **main, Dockerfile, docker-compose**,… thì chắc mọi người cũng biết rồi nên mình cũng không giải thích nữa. Giờ vào source code thôi nào :D

```go
    r.Static("/static", "./static")

	r.GET("/checkin", func(c *gin.Context) {
		c.File("./static/checkin.html")
	})

	r.GET("/enroll", func(c *gin.Context) {
		c.File("./static/enroll.html")
	})

	// employee APIs
	employeeAPI := r.Group("api/v1/employees")
	{
		// auth
		employeeAPI.Use(middlewares.APIKeyAuthentication())

		employeeAPI.POST("insert", h.Insert)
		employeeAPI.POST("search", h.Search)
	}

	err = r.RunTLS(":"+viper.GetString("PORT"), "server.crt", "server.key")
	if err != nil {
		return
	}
```

Ở file **main.go** mình khởi tạo các thành phần hệ thống và khai báo các **router api** của BE. Như trên kia thì khi vào **/checkin, /enroll** thì sẽ vào UI của 2 chức năng này. Ở BE APIs thì có 2 đầu: **insert** dùng để thêm khuôn mặt vào dữ liệu hệ thống, **search** dùng để lấy thông tin từ hình ảnh client gửi lên.

```go
func (ar *EmployeeRepository) GetTopByDistanceType(ctx context.Context, distanceType string,
	embedding []float32) (*entities.Employee, error) {
	var record *entities.Employee

	vector, err := utils.ConvertArrayFloat32(embedding)
	if err != nil {
		return nil, err
	}

	query := fmt.Sprintf(`
		SELECT id, employee_id, employee_name, image_path, embedding %v '%v' as score
		FROM employees
		WHERE embedding %v '%v' <= %v
		ORDER BY score ASC
		LIMIT 1`, distanceType, vector, distanceType, vector, viper.GetFloat64(constants.Threshold))

	err = ar.db.Raw(query).Scan(&record).Error
	if err != nil {
		return nil, err
	}

	return record, nil
}
```

Ở **infrastructure** có function **GetTopByDistanceType** dùng để lấy ra thông tin employee có ảnh gần với ảnh khuôn mặt gửi lên nhất :D tất nhiên là sẽ có 1 **threshold** để kiểm tra. Ở đây mình set là **0.4** và sử dụng khoảng cách Euclid để so sánh.

**Note**: Các bạn hiểu đơn giản là sẽ tính khoảng cách Euclid từ input vector với tất cả vector trong database. Nếu vector nào càng gần thì kết quả sẽ càng tiến đến 0 → kết quả gần 0 nhất thì đó chính là người đó :D

```go
func (es *Employee) Insert(ctx context.Context, input *models.ImageInsertInput) (map[string]interface{}, error) {
	logrus.Info(fmt.Sprintf(constants.FormatBeginTask, "Insert", input))

	// extract the image to vector
	extraction, err := third_party.GetInstance().ExtractImage(&third_party.ImageExtractionRequest{
		Image:    input.ImageFile,
		FileName: input.ImageName,
	})
	if err != nil {
		logrus.Errorf(constants.FormatTaskErr, "ExtractImage", err)
		return nil, err
	}

	// search
	employee, err := es.employeeRepo.GetTopByDistanceType(ctx, constants.EuclideanDistance, extraction.Vector)
	if err != nil {
		logrus.Errorf(constants.FormatTaskErr, "GetTopByDistanceType", err)
		return nil, err
	}

	if employee != nil && employee.EmployeeID != input.EmployeeID {
		return nil, fmt.Errorf("employee id is invalid")
	}

	// seek file
	_, err = input.ImageFile.Seek(0, 0)
	if err != nil {
		logrus.Errorf(constants.FormatTaskErr, "Seek", err)
		return nil, err
	}

	// upload minio
	imagePath, err := es.uploadImageToMinIO(input)
	if err != nil {
		logrus.Errorf(constants.FormatTaskErr, "uploadImageToMinIO", err)
		return nil, err
	}

	// insert database
	err = es.employeeRepo.Create(ctx, &entities.Employee{
		EmployeeID:   input.EmployeeID,
		EmployeeName: input.EmployeeName,
		ImagePath:    imagePath,
		Embedding:    pgvector.NewVector(extraction.Vector),
	})
	if err != nil {
		logrus.Errorf(constants.FormatCreateEntityErr, "employee", err)
		return nil, err
	}

	return map[string]interface{}{
		"employee_id": input.EmployeeID,
	}, nil
}
```

Code trên chính là logic thêm ảnh khuôn mặt của nhân viên vào hệ thống bao gồm 4 bước:

* Gọi **AI service** để **extract** ảnh khuôn mặt thành **vector**
    
* Tìm kiếm trong **database** vector này → nếu mã nhân viên mà khác với input đăng kí khuôn mặt gửi lên thì trả về lỗi
    
* Upload ảnh lên **minio** để lấy path ảnh của nhân viên
    
* Thêm thông tin nhân viên: id, tên,… vào **database**
    

```go
func (es *Employee) Search(ctx context.Context, input *models.ImageSearchInput) (map[string]interface{}, error) {
	logrus.Info(fmt.Sprintf(constants.FormatBeginTask, "Search", input))

	extraction, err := third_party.GetInstance().ExtractImage(&third_party.ImageExtractionRequest{
		Image:    input.ImageFile,
		FileName: input.ImageName,
	})
	if err != nil {
		logrus.Errorf(constants.FormatTaskErr, "ExtractImage", err)
		return nil, err
	}

	if extraction.Vector == nil {
		return map[string]interface{}{
			"employee_name": "Unknown",
		}, nil
	}

	employee, err := es.employeeRepo.GetTopByDistanceType(ctx, constants.EuclideanDistance, extraction.Vector)
	if err != nil {
		logrus.Errorf(constants.FormatTaskErr, "GetTopByDistanceType", err)
		return nil, err
	}

	if employee == nil {
		return map[string]interface{}{
			"employee_name": "Unknown",
		}, nil
	}

	return map[string]interface{}{
		"employee_id":   employee.EmployeeID,
		"employee_name": employee.EmployeeName,
		"image_path":    es.getMinIOPublicURL(employee.ImagePath),
	}, nil
}
```

Search thông tin thì cũng gồm 2 bước chính:

* Gọi AI service để **detect khuôn mặt** → **extract vector** từ ảnh detect được.
    
* Tìm kiểm trong **database** - Nếu có thì trả về thông tin còn không thì trả về kết quả **Unkown**
    

Trên đây là các thành phần, bước chính của phần BE service :D các phần logic còn lại các bạn đọc thêm nhé và khi sử dụng thì thêm logic của các bạn vào :D

## 3\. Front-end service

Ở phần FE thì mình dùng **JavaScript + Html + Tailwind** để css cho đẹp hơn + dùng thêm thư viện **face detect api**. Mình có tham khảo thư viện + model detect face trên repo của thím này + 1 số css mình không biết sửa nên remove bỏ luôn. Ví dụ như hiển thị viền khung xanh phát hiện khuôn mặt thì mình sẽ thay thế bằng text face detected ở bên dưới màn hình :D Các bác thấy ok thì cho thím đó 1 star nhé :D

[https://github.com/bensonruan/Face-Detection](https://github.com/bensonruan/Face-Detection)

**CHECKIN**

```javascript
  const video = document.getElementById('webcam');
  const statusText = document.getElementById('status');

  // Load FaceAPI Models
  async function loadModels() {
    await Promise.all([
      faceapi.nets.tinyFaceDetector.load('./static'),
    ])
  }

  // Start Webcam
  async function startWebcam() {
    try {
      video.srcObject = await navigator.mediaDevices.getUserMedia({video: true});
    } catch (error) {
      console.error("Error accessing webcam:", error);
      statusText.textContent = "Unable to access webcam. Check permissions.";
      statusText.classList.add("text-red-500");
    }
  }
```

Trên là source code load model detect face và cho phép bật camera của điện thoại lên khi truy cập vào ứng dụng web.

```javascript
  async function detectFaces() {
    const detections = await faceapi.detectAllFaces(
            video,
            new faceapi.TinyFaceDetectorOptions()
    );

    // Update status
    if (detections.length > 0) {
      statusText.textContent = `Faces detected: ${detections.length}`;
      statusText.classList.add("text-green-500");
      statusText.classList.remove("text-red-500");

      document.getElementById("employee-name").textContent = "";
      document.getElementById("time").textContent = "";
      document.getElementById("checkin-status").textContent = "Checking...";

      captureImage()
      await sendImageToAPI()
    } else {
      statusText.textContent = "No face detected.";
      statusText.classList.add("text-red-500");
      statusText.classList.remove("text-green-500");
    }
  }

  function startDetection() {
    setInterval(() => detectFaces(), 2000);
  }
```

Đoạn code **detect face** nếu phát hiện ra khuôn mặt có trong **camera** thì sẽ call **api check-in** để lấy thông tin nhân viên. Ở đây để tránh spam thì sau mỗi **2s** thì mình mới cho detect → con số delay tuỳ mọi người điều chỉnh nhé! Con số **2s** mình thấy hợp lý là vì để nhân viên đưa mặt vào rồi bắt là vừa :D

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736354326855/9e6db228-4ffe-49c8-9845-3160c9fd1cf7.jpeg align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736442425692/fc301ca6-8611-486d-9faf-f8b5f5a9c7f6.jpeg align="center")

Một số hình ảnh mình checkin :D và kết quả

```javascript
async function sendImageToAPI() {
    const canvas = document.getElementById('snapshotCanvas');

    // Convert canvas to Blob (image file)
    canvas.toBlob(async (blob) => {
      const myHeaders = new Headers();
      myHeaders.append("X-API-Key", "c3kvxLTTVaAF5aQU2BHRBnaI7kFvVzt3aJSZfUav6RzmKM6cBN");

      const formData = new FormData();
      formData.append('image', blob, 'image.png'); // Append the image as 'file'

      const requestOptions = {
        method: 'POST',
        body: formData,
        headers: myHeaders,
        redirect: "follow"
      };

      await fetch('https://10.124.67.6:8502/api/v1/employees/search', requestOptions)
              .then((response) => response.json())
              .then((result) => {
                const date = new Date();
                document.getElementById("employee-name").textContent = result.employee_name;
                document.getElementById("time").textContent = date.toDateString();
                document.getElementById("checkin-status").textContent = "Success";
              })
              .catch((error) => {
                console.log(error)
                document.getElementById("checkin-status").value = "Fail";
              });

    }, 'image/png');
  }
```

Nhìn là biết rồi dúng không :D đoạn code lấy ảnh thì camera và gọi API check-in và show kết quả nhận được từ back-end server

**ENROLL: đăng kí khuôn mặt nhân viên**

```javascript
    async function sendImageToAPI() {
        const canvas = document.getElementById('snapshotCanvas');

        // Convert canvas to Blob (image file)
        canvas.toBlob(async (blob) => {
            const myHeaders = new Headers();
            myHeaders.append("X-API-Key", "c3kvxLTTVaAF5aQU2BHRBnaI7kFvVzt3aJSZfUav6RzmKM6cBN");

            const formData = new FormData();
            formData.append('image', blob, 'image.png');
            formData.append('employee_id', document.getElementById("employee-id").value);
            formData.append('employee_name', document.getElementById("employee-name").value);

            const requestOptions = {
                method: 'POST',
                body: formData,
                headers: myHeaders,
                redirect: "follow"
            };

            await fetch('https://10.124.67.6:8502/api/v1/employees/insert', requestOptions)
                .then((response) => response.json())
                .then((result) => {
                    if (!!result.error) {
                        alert('Error enroll: ' + result.error);
                        return
                    }
                    alert('Successfully!');
                })
                .catch((error) => {
                    alert('Error enroll: ' + error.message);
                });
        }, 'image/png');
    }

    // Capture image from video feed
    async function captureImage() {
        const video = document.getElementById('webcam');
        const canvas = document.getElementById('snapshotCanvas');
        const context = canvas.getContext('2d');
        canvas.width = video.videoWidth;
        canvas.height = video.videoHeight;
        context.drawImage(video, 0, 0, canvas.width, canvas.height);

        // Show captured image
        document.getElementById('captureResult').classList.remove('hidden');

        await sendImageToAPI()
    }
```

Giống với check-in thì phần đăng kí này sẽ cho nhân viên nhập thông tin và đưa mặt vào camera rồi gửi thông tin lên BE xử lý

# **V. Kết luận**

<center><iframe width="491" height="872" src="https://www.youtube.com/embed/YiWgMJaPy0E"></iframe></center>

Đây là 1 số hình ảnh và video kết quả sau khi build code xong nhé mọi người ^^

* Source code: [https://github.com/nguyenvantuan2391996/example-code/tree/master/face-recognition-attendance](https://github.com/nguyenvantuan2391996/example-code/tree/master/face-recognition-attendance)