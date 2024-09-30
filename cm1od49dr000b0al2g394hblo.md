---
title: "Websocket - Chỉnh sửa document real-time với Golang"
seoTitle: "Websocket - Chỉnh sửa document real-time với Golang"
seoDescription: "Dạo gần đây, thấy sếp mình đang viết một web app để cung cấp cho khách hàng vẽ, xem, trao đổi và trình bày ý tưởng của mình với người khác."
datePublished: Mon Sep 30 2024 02:00:14 GMT+0000 (Coordinated Universal Time)
cuid: cm1od49dr000b0al2g394hblo
slug: websocket-chinh-sua-document-real-time-voi-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1727629205998/c29dca8b-351c-4845-a7e4-13e58b8f6426.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1727629147028/82f4eb87-9ce5-489c-ad84-1921371c5e19.png
tags: websockets, go, javascript, html5

---

**Hà Nội**, Chủ nhật 29-09-2024 - một ngày mưa…

<center><iframe width="560" height="315" src="https://www.youtube.com/embed/PV-SwMEzqwY?si=ZGI4x5DaZUQqrF0p"></iframe></center>

*Mẹ ơi thế giới mênh mông mênh mông không bằng nhà mình…*

Càng lớn tôi càng hiểu tại sao những ngày còn đi học, tôi viết văn không bao giờ hay. Tại vì khi còn nhỏ, tôi không có được **cảm xúc thật** của cuộc sống mà những gì tôi viết đều là đọc từ sách và có chăng là cảm xúc của người khác trong những bài văn mẫu.

Cảm ơn Nhạc sĩ **Trần Tiến**, Ca sĩ **Tùng Dương**

# I. Giới thiệu

Dạo gần đây, thấy sếp mình đang viết một web app để cung cấp cho khách hàng vẽ, xem, trao đổi và trình bày ý tưởng của mình với người khác. Hệ thống theo tưởng tượng của mình là nó sẽ na ná google docs nhưng chắc chắn là sẽ không xịn bằng =))

Tối cuối tuần, mình rảnh tay thay vì lướt linh tinh thì ngồi implement thử một app simple để giải quyết basic nhất bài toán này. Ở bài viết này, mình sẽ hướng dẫn từ ý tưởng cho tới cách xử lý.

Định nghĩa chút về WebSocket

*WebSocket là một giao thức truyền thông hai chiều qua một kết nối TCP duy nhất. Nó cho phép giao tiếp giữa trình duyệt và máy chủ diễn ra trong thời gian thực, mà không cần phải mở lại kết nối mỗi lần gửi dữ liệu. WebSocket rất hữu ích cho các ứng dụng cần cập nhật thông tin liên tục như chat, trò chơi trực tuyến, bảng điều khiển thời gian thực, và nhiều hơn nữa.*

![IoT Hub: What Use Case for WebSockets? | Scaleway](https://www-uploads.scaleway.com/blog-websockets-bigger-4.webp align="center")

# II. Ý tưởng

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727626152112/edf26671-c89e-4a95-9c81-988c7805ed3d.png align="center")

Mình vẽ nhanh nên có sai gạch đá nhẹ thôi nhé :D

Trên hình là ý tưởng với 3 flows. Tạo channel và trả về link join channel, khởi tạo mỗi khi người dùng join và lưu dữ liệu gửi tới những người đang xem trong channel.

Chi tiết từng bước

* Mỗi khi cần trình bày thì host sẽ tạo 1 đường link có chứa thông tin channel và gửi cho tất cả mọi người cùng join.
    
* Người dùng click link và join vào channel → hệ thống sẽ khởi tạo 1 connection ws tương ứng với uuid mà người dùng gửi lên ws server.
    
* Mỗi khi host hay bất kì 1 ai thay đổi dữ liệu → gọi 1 api lên web app server → write dữ liệu này vào các connection ws có trong channel.
    

Như nói ở trên, đây chỉ là ý tưởng cơ bản nhất → để sử dụng thực tế thì phải giải quyết rất nhiều bài toán nghiệp vụ nữa đặc biệt là bài toán về conflict khi nhiều người có trong channel sửa dữ liệu.

# III. Implement

## 1\. Back end

Ở BE mình dùng thư viện WebSocket của Go để xử lý. Mình sẽ tạo ra 1 biến để lưu thông tin các channel dưới dạng là 1 map và trong map đó sẽ lại là 1 map lưu lại các connections của channel đó =))

```go
var mapWsConn = make(map[string]map[string]*websocket.Conn)
```

```go
func main() {
	http.HandleFunc("/index", LoadPage)
	http.HandleFunc("/ws", InitWebsocket)
	http.HandleFunc("/ws/close", CloseWebsocket)
	http.HandleFunc("/save", SaveData)

	log.Fatal(http.ListenAndServe(":3000", nil))
}
```

Server gồm có 4 APIs: LoadPage, InitWebsocket, CloseWebsocket, SaveData

```go
func LoadPage(w http.ResponseWriter, r *http.Request) {
	w.Header().Set("Access-Control-Allow-Origin", "*")

	path, err := os.Getwd()
	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
		return
	}

	content, err := os.ReadFile(path + "/docs-editor-using-websocket/index.html")
	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
		return
	}

	_, err = fmt.Fprintf(w, "%s", content)
	if err != nil {
		return
	}
}
```

```go
func InitWebsocket(w http.ResponseWriter, r *http.Request) {
	w.Header().Set("Access-Control-Allow-Origin", "*")

	channel := r.URL.Query().Get("channel")
	uuid := r.URL.Query().Get("uuid")
	if r.Header.Get("Origin") != "http://"+r.Host {
		http.Error(w, "the origin is invalid", http.StatusInternalServerError)
		return
	}

	conn, err := upgrader.Upgrade(w, r, nil)
	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
		return
	}

	if len(mapWsConn[channel]) == 0 {
		mapWsConn[channel] = make(map[string]*websocket.Conn)
	}

	mapWsConn[channel][uuid] = conn
}
```

```go
func CloseWebsocket(w http.ResponseWriter, r *http.Request) {
	w.Header().Set("Access-Control-Allow-Origin", "*")

	channel := r.FormValue("channel")
	uuid := r.FormValue("uuid")

	if _, ok := mapWsConn[channel]; !ok {
		http.Error(w, "the channel is not found", http.StatusInternalServerError)
		return
	}

	if _, ok := mapWsConn[channel][uuid]; !ok {
		http.Error(w, "the uuid is not found", http.StatusInternalServerError)
		return
	}

	err := mapWsConn[channel][uuid].Close()
	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
		return
	}

	delete(mapWsConn[channel], uuid)

	w.WriteHeader(http.StatusOK)
	_, err = w.Write([]byte("success"))
	if err != nil {
		return
	}
}
```

```go
func SaveData(w http.ResponseWriter, r *http.Request) {
	channel := r.FormValue("channel")
	uuid := r.FormValue("uuid")
	data := r.FormValue("data")

	if _, ok := mapWsConn[channel]; !ok {
		http.Error(w, "the channel is not found", http.StatusInternalServerError)
		return
	}

	for key, ws := range mapWsConn[channel] {
		if key != uuid {
			err := ws.WriteJSON(map[string]interface{}{
				"data": data,
			})

			if err != nil {
				continue
			}
		}
	}

	w.WriteHeader(http.StatusOK)
	_, err := w.Write([]byte("success"))
	if err != nil {
		return
	}
}
```

## 2\. Front end

Về giao diện thì mình clone trên mạng cho nhanh còn phần JavaScript thì mình sẽ tự code :D Mình lấy html, css ở link này. Thank bro!

[https://codepen.io/fajarnurwahid/pen/NWvxeXj](https://codepen.io/fajarnurwahid/pen/NWvxeXj)

```javascript
  let ws;

  if (window.WebSocket === undefined) {
    console.log("Your browser does not support WebSockets")
  } else {
    ws = initWS();
  }

  function initWS() {
    // close ws before init new ws
    closeWs(localStorage.getItem("uuid"))

    let uuid = self.crypto.randomUUID()
    localStorage.setItem("uuid", uuid)
    let socket = new WebSocket("ws://" + window.location.host + "/ws" + window.location.search + "&uuid=" + uuid)

    socket.onopen = function () {
      console.log("Socket is open")
    };

    // receive data from server
    socket.onmessage = function (e) {
      document.getElementById("content").innerHTML = JSON.parse(e.data).data
    }

    // close socket
    socket.onclose = function () {
      console.log("Socket closed")
      closeWs(localStorage.getItem("uuid"))
    }

    return socket;
  }
```

Khởi tạo connection ws :D trước khi tạo connection mình sẽ gửi lên server uuid cũ được lưu trong local storage trước để close connection cũ.

UUID sẽ tự bằng thư viện crypto của JavaScript :D

Đoạn code này cũng xử lý các event open, close và nhận message từ WebSocket server để hiển thị lên giao diện cho người dùng

```javascript
  // api close ws
  async function closeWs(uuid) {
    const formData = new FormData();

    if (!!!uuid) {
      console.log("uuid is not found")
      return
    }

    formData.append("channel", window.location.search.split("=")[1]);
    formData.append("uuid", uuid);

    const requestOptions = {
      method: "POST",
      body: formData,
      redirect: "follow"
    };

    await fetch("http://" + window.location.host + "/ws/close", requestOptions)
            .then((response) => response.text())
            .then((result) => console.log(result))
            .catch((error) => console.error(error));
  }
```

Đoạn script để close connection WebSocket cũ

```javascript
  // onchange text editor to save data
  const div = document.getElementById('content');
  let timeout;
  div.addEventListener('input', function() {
    clearTimeout(timeout);
    timeout = setTimeout(async function () {
      await saveData()
    }, 500)
  })
  
// api save data
  async function saveData() {
    const formData = new FormData();
    formData.append("channel", window.location.search.split("=")[1]);
    formData.append("uuid", localStorage.getItem("uuid"));
    formData.append("data", document.getElementById("content").innerHTML);

    const requestOptions = {
      method: "POST",
      body: formData,
      redirect: "follow"
    };

    await fetch("http://" + window.location.host + "/save", requestOptions)
            .then((response) => response.text())
            .then((result) => console.log(result))
            .catch((error) => console.error(error));
  }
```

Save lại dữ liệu mỗi khi thay đổi. Ở đây, mình set timeout cứ sau 500ms người dùng dừng bấm thì sẽ save lại dữ liệu lên server và server sẽ gửi dữ liệu này cho các connection ws khác.

# IV. Kết quả

Dưới đây là video mình demo nhé :D

<center><iframe width="560" height="315" src="https://www.youtube.com/embed/Vg_IFPAbF2A?si=riBewTzy6V9xH8VG"></iframe></center>

# V. Nguồn

* Source code HTML: [https://codepen.io/fajarnurwahid/pen/NWvxeXj](https://codepen.io/fajarnurwahid/pen/NWvxeXj)
    
* Source code: [https://github.com/nguyenvantuan2391996/example-code/tree/master/docs-editor-using-websocket](https://github.com/nguyenvantuan2391996/example-code/tree/master/docs-editor-using-websocket)