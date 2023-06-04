---
title: "Tăng tốc quá trình upload file lớn với kỹ thuật phân mảnh và tải lên đa luồng"
seoTitle: "Tăng tốc quá trình upload file lớn với kỹ thuật phân mảnh"
seoDescription: "Phân mảnh (chunking) và tải lên đa luồng (multipart upload) là hai khái niệm quan trọng trong việc xử lý quá trình upload file lớn."
datePublished: Sun Jun 04 2023 14:00:39 GMT+0000 (Coordinated Universal Time)
cuid: clihhqf9y04w9a3nvf3xu19ct
slug: tang-toc-qua-trinh-upload-file-lon-voi-ky-thuat-phan-manh-va-tai-len-da-luong
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1684758784234/f610c80e-c431-4a7c-898d-77d3dd29b04b.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1684758919774/9aae94a8-7f50-452c-bcbb-d2b3eca1b45a.jpeg
tags: file-upload, chunking, multipart-upload

---

# I. Giới thiệu

Trong thế giới kỹ thuật số ngày nay, việc tải lên và chia sẻ các file lớn đã trở thành một yêu cầu phổ biến. Tuy nhiên, việc xử lý quá trình upload file lớn có thể gặp nhiều thách thức như giới hạn kích thước tệp tin, tốc độ mạng chậm hay mất kết nối. Để giải quyết vấn đề này, kỹ thuật phân mảnh và tải lên đa luồng ra đời. Trong bài viết này, chúng ta sẽ khám phá cách sử dụng kỹ thuật này để tăng tốc quá trình upload file lớn.

![Lý do tốc độ upload thường chậm hơn so với tốc độ download? - TOTOLINK Việt  Nam](https://www.totolink.vn/public/uploads/img_post/ly-do-toc-do-upload-thuong-cham-hon-so-voi-toc-do-download-1.png align="center")

# II. Kỹ thuật phân mảnh và multipart upload

Phân mảnh (chunking) và tải lên đa luồng (multipart upload) là hai khái niệm quan trọng trong việc xử lý quá trình upload file lớn. Dưới đây là giải thích chi tiết về hai khái niệm này:

## 1\. Phân mảnh

Phân mảnh (chunking) là quá trình chia nhỏ một tệp tin lớn thành các phần nhỏ hơn gọi là "chunk". Mỗi chunk có kích thước nhỏ hơn so với tệp tin gốc, điều này giúp quá trình upload trở nên dễ dàng và ổn định hơn. Các chunk được tải lên đồng thời lên máy chủ mục tiêu.

Việc phân mảnh tệp tin giúp chia nhỏ công việc upload thành nhiều phần nhỏ, giảm thiểu tác động của mất kết nối hoặc tốc độ mạng chậm. Nếu một chunk bị lỗi trong quá trình upload, chỉ cần tải lên lại phần đó mà không cần upload lại toàn bộ tệp tin.

## 2\. Tải lên đa luồng

Tải lên đa luồng (multipart upload) là quá trình tải lên một tệp tin lớn bằng cách chia thành nhiều phần nhỏ hơn gọi là "part" và tải lên đồng thời lên máy chủ. Mỗi part được tải lên độc lập và sau đó kết hợp lại thành tệp tin hoàn chỉnh.

Đối với tệp tin lớn, việc sử dụng tải lên đa luồng giúp tăng tốc quá trình upload bằng cách tận dụng khả năng tải lên đồng thời từ nhiều nguồn. Điều này giúp giảm thiểu thời gian hoàn thành quá trình upload và tăng hiệu suất của ứng dụng.

![Upload Files Of Any Size – Free Up To 5 GB - Filemail](https://www.filemail.com/images/marketing/upload-your-files.svg align="center")

Kết hợp giữa kỹ thuật phân mảnh và tải lên đa luồng cho phép tối ưu quá trình upload file lớn. Bằng cách chia nhỏ tệp tin thành các phần nhỏ (chunk) và tải lên đồng thời (multipart upload), chúng ta có thể đạt được hiệu suất cao và đảm bảo quá trình upload file lớn được thực hiện một cách nhanh chóng và ổn định.

## 3\. Ưu và nhược điểm

**Ưu điểm của kỹ thuật phân mảnh và tải lên đa luồng:**

* Tăng tốc độ upload file lớn: Tận dụng khả năng tải lên nhiều phần của file đồng thời, từ đó giảm thời gian hoàn thành quá trình upload.
    
* Đảm bảo độ tin cậy: Nếu một phần trong quá trình upload bị lỗi, chỉ cần tải lên lại phần đó mà không cần upload lại toàn bộ file.
    

**Nhược điểm, thách thức và giải pháp:**

* Xử lý mất kết nối hoặc tốc độ mạng chậm: Giải quyết các vấn đề liên quan đến mất kết nối hoặc tốc độ mạng không ổn định để đảm bảo quá trình upload được hoàn thành một cách thành công.
    
* Khi phân mảnh lỗi dẫn đến không ghép được file hoàn chỉnh.
    
* Logic của việc ghép file xử lý khá phức tạp và tốn nhân lực.
    

# III. Áp dụng kỹ thuật phân mảnh và tải lên đa luồng

## 1. Ý tưởng

![Uploading Extremely Large Files, The Awesome Easy Way [Tus Server] – Abdel  Hady Muhammad](https://abdelhadymu.files.wordpress.com/2018/11/concat.jpeg align="center")

Nguồn ảnh lấy từ bài viết: https://abdelhady.net/2018/11/17/uploading-extremely-large-files-the-awesome-easy-way-tus-server/

Hình vẽ mô tả flow để thực hiện phân mảnh và upload file đa luồng. Chúng ta có 1 file lớn 100GB. Áp dụng chúng ta sẽ có các bước thực hiện như sau:

- Bước 1: Nhận đầu vào file 100GB

- Bước 2: Phân mảnh file thành các mảnh nhỏ. Mỗi phần 100MB chẳng hạn

- Bước 3: Call API upload các mảnh file lên server

- Bước 4: Back end lưu thông tin các mảnh file và thực hiện ghép các mảnh này đủ khi nhận đủ các file phân mảnh.

## 2. Mã nguồn mẫu front-end

**Front-end**

Ở FE ta sẽ dựng giao diện upload file. Ở đây mình có 1 button và 1 input kiểu file đơn giản như thế này

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Upload file</title>
</head>
<body>
<button id="btn-import">Upload</button>
<input id="file-input" type="file" />
<div id="status"></div>
</body>
</html>

<script src="upload.js"></script>
```

Hàm xử lý gọi API để upload file. Đầu vào sẽ là file phân mảnh và số thứ tự của chúng

```javascript
async function uploadChunk(chunk, chunkIndex) {
    const formData = new FormData();
    formData.append("chunk", chunk);
    formData.append("chunkIndex", chunkIndex);

    try {
        const response = await fetch("http://localhost:3000/upload", {
            method: "POST",
            body: formData,
        });

        if (!response.ok) {
            document.getElementById("status").textContent = "failed";
            throw new Error("Error uploading chunk.");
        }
    } catch (error) {
        document.getElementById("status").textContent = "failed";
        console.error(error);
    }
}
```

Xử lý sự kiện khi click button upload. Khi click chọn file chúng ta sẽ phân từng mảnh nhỏ có kích thước được config cố định. Ở đây mình đang để 100MB. Mỗi lần phân mảnh xong sẽ gọi hàm **uploadChunk** để tải file lên server BE.

```javascript
const importInvoices = () => {
    document.getElementById("file-input").click();
    // Example usage
    const fileInput = document.getElementById("file-input");
    fileInput.addEventListener("change", async (event) => {
        document.getElementById("status").textContent = "uploading";
        const file = event.target.files[0];
        const chunkSize = 100 * 1024 * 1024; // Set the desired chunk size (100MB in this example)
        const totalChunks = Math.ceil(file.size / chunkSize);

        // Iterate over the chunks and upload them sequentially
        for (let chunkIndex = 0; chunkIndex < totalChunks; chunkIndex++) {
            const start = chunkIndex * chunkSize;
            const end = Math.min(start + chunkSize, file.size);
            const chunk = file.slice(start, end);

            // Make an API call to upload the chunk to the backend
            await uploadChunk(chunk, chunkIndex);
        }
        document.getElementById("status").textContent = "successfully";
    });
};

document.getElementById("btn-import").addEventListener("click", (event) => {
    event.preventDefault();
    importInvoices();
});
```

## 3. Mã nguồn mẫu back-end

Ở API mình sẽ đọc từ form ra giá trị file phân mảnh và số thứ tự của chúng => lưu file dạng video%v với v là giá trị của số thứ tự file phân mảnh.

```golang
func uploadHandler(w http.ResponseWriter, r *http.Request) {
	w.Header().Set("Access-Control-Allow-Origin", "*")

	index := r.FormValue("chunkIndex")
	file, _, err := r.FormFile("chunk")
	if err != nil {
		http.Error(w, "Failed to retrieve file", http.StatusBadRequest)
		return
	}
	defer func(file multipart.File) {
		errClose := file.Close()
		if errClose != nil {
			return
		}
	}(file)

	// Process and save the chunk to a file
	outputFile, err := os.Create(fmt.Sprintf("./upload-file/video%v", index))
	if err != nil {
		http.Error(w, "Failed to create file", http.StatusInternalServerError)
		return
	}
	defer func(outputFile *os.File) {
		errClose := outputFile.Close()
		if errClose != nil {
			return
		}
	}(outputFile)

	_, err = io.Copy(outputFile, file)
	if err != nil {
		http.Error(w, "Failed to write file", http.StatusInternalServerError)
		return
	}

	w.Write([]byte("Chunk uploaded successfully"))
}

func main() {
	http.HandleFunc("/upload", uploadHandler)

	// Start the server
	log.Fatal(http.ListenAndServe(":3000", nil))
}
```

Tiếp tục xử lý ghép các file lại. Chúng ta sẽ đọc file theo format như ở đã nói ở trên => được 1 list. Loop vòng lặp các file và sử dụng **io.Copy** để ghép các file vào file output.mp4. Ở đây mình upload file hơn 700MB nên tạm config ghép 8 file nhé.

```golang
package main

import (
	"fmt"
	"io"
	"os"
)

func concatenateVideo(length int) bool {
	// Open the output file for writing
	outputFile, err := os.Create("./upload-file/output.mp4")
	if err != nil {
		fmt.Println("Error:", err)
		return false
	}
	defer func(outputFile *os.File) {
		errClose := outputFile.Close()
		if errClose != nil {
			return
		}
	}(outputFile)

	// List the video chunks in the desired order
	chunkPaths := make([]string, 0)
	for i := 0; i < length; i++ {
		chunkPaths = append(chunkPaths, fmt.Sprintf("upload-file/video%v", i))
	}

	// Concatenate the video chunks
	for _, chunkPath := range chunkPaths {
		// Open the current chunk file
		chunkFile, errChunk := os.Open(chunkPath)
		if errChunk != nil {
			fmt.Println("Error:", errChunk)
			return false
		}
		defer func(chunkFile *os.File) {
			errClose := chunkFile.Close()
			if errClose != nil {
				return
			}
		}(chunkFile)

		// Copy the chunk content to the output file
		_, errCopy := io.Copy(outputFile, chunkFile)
		if errCopy != nil {
			fmt.Println("Error:", errCopy)
			return false
		}
	}

	return true
}

func main() {
	isConcatenate := concatenateVideo(8)
	if !isConcatenate {
		fmt.Println("concatenate is failed")
		return
	}

	fmt.Println("concatenate is successfully")
}
```

Ở phần ghép file này do demo thôi nên mình viết sẵn hàm main rồi chạy. Còn nếu áp dụng vào thực tế, chúng ta sẽ nên có 1 queue để xử lý việc upload file và 1 worker để đi thực hiện ghép các mảnh file lại với nhau.

# IV. Kết luận

Kỹ thuật phân mảnh và tải lên đa luồng là một giải pháp hiệu quả để tăng tốc quá trình upload file lớn. Với khả năng chia nhỏ file thành các phần nhỏ hơn và tải lên đồng thời, chúng ta có thể tận dụng tối đa tốc độ và hiệu suất của mạng. Bài viết này hy vọng sẽ giúp bạn hiểu rõ hơn về cơ chế hoạt động và cách áp dụng kỹ thuật này trong các dự án upload file lớn của mình.

* Source code: [Example with Golang](https://github.com/nguyenvantuan2391996/example-code/tree/master/upload-file)

