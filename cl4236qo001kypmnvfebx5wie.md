## [Phần 3] Hệ thống tư vấn chọn người yêu: Build back-end với Golang và Gin-Gonic framework

**Hà Nội, Chủ nhật, 05/06/2022...**

Hello mọi người ! Một tuần làm việc của các bạn sao rồi? Vui vẻ và hứng thú với công việc của mình hiện tại chứ 😁😁😁 Mình thì dạo này cũng hơi lu bu với công việc quên cả việc thể dục, thể thao nâng cao sức khoẻ mỗi ngày... Hôm nay, cuối tuần vào bếp nấu ăn mà cắt bí chảy máu cả ngón tay cái. Một ngày cuối tuần khá là đen đủi :LOL: 😥

Luyên thuyên thế đủ rồi :v Chúng ta tiếp tục đến với phần 3 của series xây dựng Hệ thống tư vấn chọn người yêu ( Các bạn có thể xem [phần 1](https://tuannguyenhust.hashnode.dev/phan-1-he-thong-tu-van-chon-nguoi-yeu-tong-quan-va-phuong-phap-topsis) và [phần 2](https://tuannguyenhust.hashnode.dev/phan-2-he-thong-tu-van-chon-nguoi-yeu-thiet-ke-wireframe-api-va-database) ở link này nhé ).

Ở phần 3 này hãy cùng mình xây dựng **back-end** xử lý nghiệp vụ cho hệ thống này base trên document thiết kế API, Database, UI ở [phần 2](https://tuannguyenhust.hashnode.dev/phan-2-he-thong-tu-van-chon-nguoi-yeu-thiet-ke-wireframe-api-va-database). Source code mình sẽ để ở cuối bài viết nhé. Quẩy lên nào gét gô 😁😁😁

# I. Ngôn ngữ và Framework dựng Back-end

Phần ngôn ngữ mình sử dụng [Golang](https://go.dev/) và framework [Gin-Gonic](https://github.com/gin-gonic/gin) để build rest api.

Golang thì có nhiều ưu điểm để build hệ thống Back-end: Gọn, nhẹ, nhanh, deploy dễ dàng,... Ở bản này mình dùng version 1.18 để dùng luôn cái **generic** mới ra nhé. 😁😁😁

Còn Gin-Gonic thì bạn nào code Go thì không thể không biết tới framework này. Gin giờ đã đạt gần 60k stars trên gitHub.

# II. Project structure

Mình dựng structure dựa theo [Clean Architecture](https://topdev.vn/blog/clean-architecture-dung-tren-vai-nhung-ga-khong-lo/). Dưới đây là sơ đồ được sử dụng để giải thích ý tưởng tổng quát :

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1654441998032/c-ObNaNDG.png align="left")

Dưới đây là hình ảnh kiến trúc của project này base theo **clean architecture**

![Screen Shot 2022-06-05 at 22.18.01.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1654442403152/aCtbkQpuq.png align="left")

## 1. cmd/service/main.go

Ở main, chúng ta khai báo, khởi tạo các thứ cần thiết như : **Open connect database, load config, init domain usecase, repository, validator, routes,...**

```golang
newLogger := logger.New(
		log.New(os.Stdout, "\r\n", log.LstdFlags), // io writer
		logger.Config{
			SlowThreshold:             time.Second, // Slow SQL threshold
			LogLevel:                  logger.Info, // Log level
			IgnoreRecordNotFoundError: true,        // Ignore ErrRecordNotFound error for logger
			Colorful:                  false,       // Disable color
		},
	)

	db, err := gorm.Open(mysql.Open(cfg.DBSource), &gorm.Config{
		Logger: newLogger,
	})
	if err != nil {
		log.Fatal("failed to open database:", err)
		return
	}
```

```golang
func initRoutes(userDomain *usecase.UserDomain, standardDomain *usecase.StandardDomain, scoreRatingDomain *usecase.ScoreRatingDomain, consultDomain *usecase.ConsultDomain, validate *validator.Validate) {
	// init handler
	h := handler.NewHandler(userDomain, standardDomain, scoreRatingDomain, consultDomain, validate)

	r := gin.Default()
	r.Use(cors.New(cors.Config{
		AllowOrigins:     []string{"*"},
		AllowMethods:     []string{"POST", "GET", "PUT", "PATCH"},
		AllowHeaders:     []string{"*"},
		ExposeHeaders:    []string{"Content-Length"},
		AllowCredentials: true,
	}))

	api := r.Group("v1/api")
	{
		// API users
		api.POST("/users", h.CreateUser)

		// API standards
		api.POST("/standards", h.CreateStandard)
		api.GET("/standards/:user_id", h.GetStandards)
		api.DELETE("/standards/:id", h.DeleteStandard)

		// API score_ratings
		api.POST("/score-ratings", h.BulkCreateScoreRating)
		api.GET("/score-ratings/:user_id", h.GetScoreRatings)
		api.DELETE("/score-ratings/:id", h.DeleteScoreRating)

		// API Consult
		api.POST("/consult/:user_id", h.Consult)
	}
	err := r.Run(":3000")
	if err != nil {
		return
	}
}
```

## 2. Handler

Ở handler, chúng ta tạo các file .go và các function để xử lý nhận request và trả về response cho từng api được khai báo ở phần **initRoutes**. **Handler** sẽ gọi **usecase** để xử lý business logic. Ở phần này đa số sẽ là CRUD nên mình sẽ chỉ đi luồng xử lý cho api create user còn các api còn lại cũng sẽ na ná như vậy.

```golang
func (h *Handler) CreateUser(c *gin.Context) {
	logrus.Info("Start api create user...")

	// Parse request
	var userRequest *model.UserRequest
	err := c.ShouldBindJSON(&userRequest)
	if err != nil {
		logrus.Errorf("Parse request create user fail: %v", err)
		c.JSON(http.StatusBadRequest, &model.ErrorSystem{
			Code:    http.StatusBadRequest,
			Message: constant.ParseRequestFail,
		})
		return
	}

	// Validate request
	listErr := h.ValidateRequest(userRequest)
	if len(listErr) > 0 {
		logrus.Errorf("Request is invalid: %v", listErr)
		c.JSON(http.StatusBadRequest, listErr)
		return
	}

	userCreated, err := h.userDomain.CreateUser(c, userRequest.Name)
	if err != nil {
		logrus.Errorf("Create new user fail: %v", err)
		c.JSON(http.StatusBadRequest, &model.ErrorSystem{
			Code:    http.StatusBadRequest,
			Message: constant.CreateNewUserFail,
		})
		return
	}

	logrus.Info("Create new user success")
	c.JSON(http.StatusCreated, &model.UserResponse{
		ID:   userCreated.ID,
		Name: userCreated.Name,
	})
	return
}
```

**Function validate request** mình dùng generic của golang luôn nhé ! Phải gọi là ngon luôn đỡ phải dùng interface rồi switch case để xử lý từng trường hợp. Validate thì mình dùng [golang validator](https://github.com/go-playground/validator) - trên gitHub được ~11k stars 😁😁😁

```golang
func (h *Handler) ValidateRequest(requestStruct any) []*model.ErrorValidateRequest {
	var (
		ve      validator.ValidationErrors
		listErr []*model.ErrorValidateRequest
	)

	err := h.validate.Struct(requestStruct)
	if errors.As(err, &ve) {
		for _, e := range ve {
			listErr = append(listErr, &model.ErrorValidateRequest{
				Field:   e.Field(),
				Message: constant.MapErrorValidateRequest[e.Field()],
			})
		}
	}

	return listErr
}
```

## 3. Usecase

Usecase thì sẽ xử lý business logic và gọi repository để CRUD data trong database. À database thì project này mình dùng **mysql** nhé 😁😁😁

Do chưa phải xử lý logic gì nên chỉ cần gọi repository rồi return về **Handler** luôn.
```golang
func (u *UserDomain) CreateUser(ctx context.Context, name string) (*model.User, error) {
	return u.userRepo.CreateUser(ctx, &model.User{
		Name: name,
	})
}
```

## 4. Repository

Repository thì sẽ tương tác với database: **Insert, Update, Select,...** rồi trả về cho usecase. Ở đây mình dùng gorm để xử lý cho nhanh nhé.
```golang
func (u *UserRepository) CreateUser(ctx context.Context, user *model.User) (*model.User, error) {
	if err := user.Model.GenerateID(); err != nil {
		return nil, err
	}
	if err := u.db.Create(&user).Error; err != nil {
		return nil, err
	}
	return user, nil
}
```

## 5. Xử lý hỗ trợ đưa ra quyết định tư vấn

Phần xử lý này là quan trọng và hay ho nhất trong phần hệ thống. Để đưa ra tư vấn thì mình sử dụng phương pháp [TOPSIS](https://en.wikipedia.org/wiki/TOPSIS). Mọi người có thể đọc tổng quan giới thiệu ở [phần 1](https://tuannguyenhust.hashnode.dev/phan-1-he-thong-tu-van-chon-nguoi-yeu-tong-quan-va-phuong-phap-topsis) nhé 😁😁😁

Các bước trong phương pháp TOPSIS để xử lý

![Screen Shot 2022-06-05 at 23.48.54.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1654447770659/sYcFDwi24.png align="left")

Ban đầu, mình định chỉ xây dựng xử lý cho chỉ hệ thống này nhưng cuối cùng nghĩ lại và xây dựng base code để xử lý mọi data mà người dùng nhập vào để cần tư vấn 😁😁😁

```json
[
    {
        "id": "1cd8f047-594d-40b2-85f3-58164d4ec8f4",
        "metadata": "[{\"name\": \"Nguyen Van A\", \"score\": 6, \"standard_name\": \"Chiều cao\"}, {\"name\": \"Nguyen Van A\", \"score\": 8, \"standard_name\": \"Tuổi\"}, {\"name\": \"Nguyen Van A\", \"score\": 9, \"standard_name\": \"Thu nhập\"}, {\"name\": \"Nguyen Van A\", \"score\": 3, \"standard_name\": \"Tiền tiết kiệm\"}]"
    },
    {
        "id": "8f96ab6d-8aff-4d01-ac13-234a755931f4",
        "metadata": "[{\"name\": \"Nguyen Van B\", \"score\": 2, \"standard_name\": \"Chiều cao\"}, {\"name\": \"Nguyen Van B\", \"score\": 6, \"standard_name\": \"Tuổi\"}, {\"name\": \"Nguyen Van B\", \"score\": 6, \"standard_name\": \"Thu nhập\"}, {\"name\": \"Nguyen Van B\", \"score\": 10, \"standard_name\": \"Tiền tiết kiệm\"}]"
    },
    {
        "id": "aa59ab59-59f3-4604-9482-853ba8e8e3a0",
        "metadata": "[{\"name\": \"Nguyen Van C\", \"score\": 6, \"standard_name\": \"Chiều cao\"}, {\"name\": \"Nguyen Van C\", \"score\": 2, \"standard_name\": \"Tuổi\"}, {\"name\": \"Nguyen Van C\", \"score\": 8, \"standard_name\": \"Thu nhập\"}, {\"name\": \"Nguyen Van C\", \"score\": 5, \"standard_name\": \"Tiền tiết kiệm\"}]"
    },
    {
        "id": "c5ba77f5-e530-4f26-9b00-b71376cf4503",
        "metadata": "[{\"name\": \"Nguyen Van F\", \"score\": 8, \"standard_name\": \"Chiều cao\"}, {\"name\": \"Nguyen Van E\", \"score\": 5, \"standard_name\": \"Tuổi\"}, {\"name\": \"Nguyen Van E\", \"score\": 6, \"standard_name\": \"Thu nhập\"}, {\"name\": \"Nguyen Van E\", \"score\": 6, \"standard_name\": \"Tiền tiết kiệm\"}]"
    },
    {
        "id": "d89f6c3d-29af-4d6f-bbd9-70d349fa4211",
        "metadata": "[{\"name\": \"Nguyen Van D\", \"score\": 9, \"standard_name\": \"Chiều cao\"}, {\"name\": \"Nguyen Van D\", \"score\": 4, \"standard_name\": \"Tuổi\"}, {\"name\": \"Nguyen Van D\", \"score\": 4, \"standard_name\": \"Thu nhập\"}, {\"name\": \"Nguyen Van D\", \"score\": 2, \"standard_name\": \"Tiền tiết kiệm\"}]"
    },
    {
        "id": "f9dc6c8a-fccc-41e1-8949-d0ab48f00a2c",
        "metadata": "[{\"name\": \"Nguyen Van E\", \"score\": 6, \"standard_name\": \"Chiều cao\"}, {\"name\": \"Nguyen Van E\", \"score\": 5, \"standard_name\": \"Tuổi\"}, {\"name\": \"Nguyen Van E\", \"score\": 7, \"standard_name\": \"Thu nhập\"}, {\"name\": \"Nguyen Van E\", \"score\": 2, \"standard_name\": \"Tiền tiết kiệm\"}]"
    }
]
```

Data đầu vào sẽ là đánh giá của người dùng về các lựa chọn. Ở đây mình ví dụ theo tư vấn chọn người yêu đánh giá qua 4 yếu tố : **Chiều cao, mức độ hợp tuổi, mức độ thu nhập, mức độ tiền tiết kiệm** và các đánh giá cho 6 anh chàng : **A, B, C, D, E, F**

Đầu vào input đã có vậy cùng mình đi xử lý đống data này theo từng bước của phương pháp TOPSIS nhé 😁😁😁 Gét gô...

**Step 1: Create an evaluation matrix consisting of m alternatives and n criteria**

Tạo một ma trận đánh giá bao gồm m phương án và n tiêu chí. Ở bước này mình sẽ lấy dữ liệu từ database và xử lý ra được : **ma trận m phương án và n tiêu chí, trọng số của từng tiêu chí.**

```golang
// Get list standards
	wg.Add(1)
	go func() {
		defer wg.Done()
		standards, scopedErr := c.standardRepo.GetStandardByQueries(ctx, map[string]interface{}{
			"user_id": userId,
		})
		if scopedErr != nil {
			err = scopedErr
			return
		}
		// Parse weight
		weights = ParseWeight(standards)
	}()

	// Get list score ratings
	wg.Add(1)
	go func() {
		defer wg.Done()
		scoreRatings, scopedErr := c.scoreRatingRepo.GetScoreRatingByListQueries(ctx, map[string]interface{}{
			"user_id": userId,
		})
		if scopedErr != nil {
			err = scopedErr
			return
		}
		// Parse metadata score rating
		matrix, listName, scopedErr = ParseMetadata(scoreRatings)
		if scopedErr != nil {
			err = scopedErr
			return
		}
	}()

	wg.Wait()
	if err != nil {
		return nil, err
	}
```

Xử lý lấy ra matrix

```golang
func ParseMetadata(scoreRatings []*model.ScoreRating) (matrix [][]float64, listName []string, err error) {
	var metadataStruct []*model.MetadataScoreRating
	for _, value := range scoreRatings {
		err = json.Unmarshal([]byte(value.Metadata), &metadataStruct)
		if err != nil {
			return nil, nil, err
		}
		var score []float64
		for _, v := range metadataStruct {
			score = append(score, float64(v.Score))
		}
		matrix = append(matrix, score)
		if len(metadataStruct) > 0 {
			listName = append(listName, metadataStruct[0].Name)
		}
	}
	return matrix, listName, nil
}
```

Xử lý lấy ra trọng số đánh giá

```golang
func ParseWeight(standards []*model.Standard) (weights []float64) {
	for _, value := range standards {
		weights = append(weights, float64(value.Weight))
	}
	return weights
}
```

**Step 2: The matrix is then normalised to form the matrix**

Chuẩn hoá ma trận và trọng số đánh giá. Ở bước này sẽ làm mịn data và đưa dữ liệu của các đánh giá về cùng 1 miền giá trị. Ở đây mình đưa các đánh giá từng tiêu chí của từng phương án về miền [0;1]

Chuẩn hoá ma trận

```golang
func NormalizeMatrix(matrix [][]float64) [][]float64 {
	for col := 0; col < len(matrix[0]); col++ {
		var sumSquare float64
		for row := 0; row < len(matrix); row++ {
			sumSquare += matrix[row][col] * matrix[row][col]
		}
		// Set data is normalized
		for row := 0; row < len(matrix); row++ {
			matrix[row][col] /= math.Sqrt(sumSquare)
		}
	}
	return matrix
}
```
Chuẩn hoá trọng số từng tiêu chí. Tổng trọng số của các tiêu chí = 1 ( 100% )

```golang
func NormalizeWeights(weights []float64) []float64 {
	var sum float64
	for _, value := range weights {
		sum += value
	}
	for index, value := range weights {
		weights[index] = value / sum
	}
	return weights
}
```

**Step 3: Calculate the weighted normalised decision matrix**

Ở bước này, ta sẽ tính ma trận đã được chuẩn hoá với trọng số của từng tiêu chí. Để công bằng hơn khi so sánh ở mức độ các tiêu chí ta cần 1 bộ trọng số **weights** được tính ở trên bằng cách nhân các đánh giá với trọng số của từng tiêu chí.

```golang
func CalculateTheWeighted(matrix [][]float64, weights []float64) [][]float64 {
	for row := 0; row < len(matrix); row++ {
		for col := 0; col < len(matrix[0]); col++ {
			matrix[row][col] *= weights[col]
		}
	}
	return matrix
}
```

**Step 4: Determine the worst alternative and the best alternative**

Ở bước này, ta tính toán được phương án tồi nhất và tốt nhất từ ma trận m phương án và n tiêu chí bằng cách:

- Duyệt tất cả đánh giá tiêu chí của từng phương án rồi lấy ra **đánh giá tốt nhất** từng tiêu chí -> được matrix **bestAlternative**
- Duyệt tất cả đánh giá tiêu chí của từng phương án rồi lấy ra **đánh giá tồi nhất** từng tiêu chí -> được matrix **worstAlternative**

**Lưu ý:** đánh giá tốt nhất, tồi nhất chứ không phải là max và min giá trị nha (Ví dụ: Tiêu chí đánh giá số lượng bug nhiều -> giá trị tồi, số lượng bug ít -> giá trị tốt 😁😁😁, thu nhập nhiều -> giá trị tốt, thu nhập ít -> giá trị tồi 😁😁😁). Do ở đây lúc design, mình quên chưa tính đến nên đang không có field nào để phân biệt cái này -> Mặc định các đánh giá tiêu chí sẽ theo hướng của ví dụ **Thu nhập** nha.

Code xử lý lấy ra **bestAlternative và worstAlternative**

```golang
func GetBestAndWorst(matrix [][]float64) (bestAlternative, worstAlternative []float64) {
	var listColumns [][]float64
	for col := 0; col < len(matrix[0]); col++ {
		var columns []float64
		for row := 0; row < len(matrix); row++ {
			columns = append(columns, matrix[row][col])
		}
		listColumns = append(listColumns, columns)
	}

	for _, value := range listColumns {
		bestAlternative = append(bestAlternative, helper.FindMax(value))
		worstAlternative = append(worstAlternative, helper.FindMin(value))
	}
	return bestAlternative, worstAlternative
}
```

**Step 5: Calculate the L2-distance between the target alternative and the worst condition and the best condition**

Ở bước 5, chúng ta tính khoảng cách của từng phương án với phương án tốt nhất và tồi nhất được tính ở bước 4. Việc tính khoảng cách, chúng ta sẽ sử dụng khoảng cách [Euclidean distance](https://en.wikipedia.org/wiki/Euclidean_distance) rất là quen thuộc với chúng ta từ hồi cấp 2 phải không? Nhiều bạn vẫn thắc mắc học toán sẽ áp dụng vào đâu, ở Việt Nam không áp dụng gì? Sau bài này, thì các bạn biết nó áp dụng vào 1 ví dụ này nè 😁😁😁 **I LOVE MATHS 😘**

bestDistance, worstDistance

```golang
func CalculateDistance(matrix [][]float64, bestAlternative, worstAlternative []float64) (bestDistance, worstDistance []float64) {
	for _, value := range matrix {
		bestDistance = append(bestDistance, helper.CalculateEuclideanDistance(bestAlternative, value))
		worstDistance = append(worstDistance, helper.CalculateEuclideanDistance(worstAlternative, value))
	}
	return bestDistance, worstDistance
}
```

function tính khoảng cách euclidean mình cũng dùng generic nhé 😁😁😁

```golang
func CalculateEuclideanDistance[T int64 | int32 | float64 | float32](pointA, pointB []T) T {
	if len(pointA) != len(pointB) {
		var zero T
		return zero
	}
	var sum T
	for index := 0; index < len(pointA); index++ {
		sum += (pointA[index] - pointB[index]) * (pointA[index] - pointB[index])
	}
	return T(math.Sqrt(float64(sum)))
}
```

**Step 6: Calculate the similarity to the worst condition**

Ở bước 6 này, chúng ta tính toán độ đo tương tự với điều kiện tồi nhất. Độ đo tương tự [Similarity measure](https://en.wikipedia.org/wiki/Similarity_measure) cho biết hai đối tượng giống nhau đến mức nào. Nếu:

- Phương án có **khoảng cách xa nhất** so với phương án tồi nhất -> Phương án này sẽ là **phương án tốt nhất.**
- Phương án có **khoảng cách gần nhất** so với phương án tồi nhất -> Phương án này sẽ là **phương án tồi nhất.**

Tương tự ngược lại ta cũng sẽ tính được **phương án tốt nhất** và **phương án tồi nhất** nếu tính khoảng cách so với phương án tốt nhất. 😁😁😁

 Ở đây trên **Wikipedia** phương pháp **TOPSIS** họ đang mô tả tính độ đo tương tự bằng công thức **Similarity measure = worst / (best + worst)**. Phương án có Similarity measure cao nhất sẽ gần với phương án lý tưởng nhất và ngược lại. Dưới đây là code tính toán độ đo tương tự của các phương án:

```golang
func CalculateSimilarity(bestDistance, worstDistance []float64) (similarity []float64) {
	if len(bestDistance) != len(worstDistance) {
		return []float64{}
	}
	for index := 0; index < len(bestDistance); index++ {
		similarity = append(similarity, worstDistance[index]/(bestDistance[index]+worstDistance[index]))
	}
	return similarity
}
```

**Step 7: Rank the alternatives**

Cuối cùng, chúng ta cũng có thành quả rồi. Bước này chúng ta chỉ sort lại từ cao đến thấp các phương án theo list độ đo tương tự bằng cách dùng sort trong golang rồi trả về kết quả tư vấn cho người dùng. Các bạn nhớ thuật toán sort có độ phức tạp trung bình là O(nlogn) nhé 😁😁😁 

```golang
func ConvertToListConsultResult(similarity []float64, listName []string) (list []*model.ConsultResult) {
	if len(similarity) != len(listName) {
		return []*model.ConsultResult{}
	}
	for index := 0; index < len(similarity); index++ {
		list = append(list, &model.ConsultResult{
			Name:       listName[index],
			Similarity: similarity[index],
		})
	}
	sort.Slice(list, func(i, j int) bool {
		return list[i].Similarity > list[j].Similarity
	})
	return list
}
```

# III. Kết quả tư vấn

Sau các bước tính toán, xử lý mệt nhọc giờ là lúc chúng ta nhìn lại thành quả nhé. Dưới đây là data kết quả trả về của hệ thống

```json
[
    {
        "name": "Nguyen Van B",
        "similarity": 0.6400835490382881
    },
    {
        "name": "Nguyen Van F",
        "similarity": 0.5251968636092139
    },
    {
        "name": "Nguyen Van A",
        "similarity": 0.5079943556919193
    },
    {
        "name": "Nguyen Van C",
        "similarity": 0.40214796693267396
    },
    {
        "name": "Nguyen Van E",
        "similarity": 0.333626357397698
    },
    {
        "name": "Nguyen Van D",
        "similarity": 0.30366058109323785
    }
]
```

**Kết luận:** Đó đơn giản vậy thôi là chúng ta đã xây dựng được phần Back-end cho hệ thống. Ở phần 4 sau thì mình sẽ hướng dẫn build Front-end bằng **ReactJS** để tích hợp API. Nếu có gì cần thảo luận hãy để lại bình luận ở phía dưới để chúng ta cùng trao đổi nhé.

Source code : [https://github.com/nguyenvantuan2391996/be-topsis](https://github.com/nguyenvantuan2391996/be-topsis)