---
title: "Websocket - Document editor Real-time with Golang"
seoTitle: "Websocket - Document editor Real-time with Golang"
seoDescription: "Recently, I saw my manager coding a web application for the customer. It has some functions: drawing, communicating, and presenting their ideas."
datePublished: Mon Sep 30 2024 16:44:25 GMT+0000 (Coordinated Universal Time)
cuid: cm1p8pbpp000109jve1k6bhdn
slug: websocket-document-editor-real-time-with-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1727714585457/03cd2c68-c0d1-47d2-b6e6-03df2672e3a0.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1727714628571/efed5f33-933a-4198-9036-60bbbbaa1ec8.png
tags: websockets, go, js, javascript, golang

---

**Hà Nội**, Chủ nhật 29-09-2024 - một ngày mưa…

<center><iframe width="560" height="315" src="https://www.youtube.com/embed/PV-SwMEzqwY?si=ZGI4x5DaZUQqrF0p"></iframe></center>

*Mẹ ơi thế giới mênh mông mênh mông không bằng nhà mình…*

Càng lớn tôi càng hiểu tại sao những ngày còn đi học, tôi viết văn không bao giờ hay. Tại vì khi còn nhỏ, tôi không có được **cảm xúc thật** của cuộc sống mà những gì tôi viết đều là đọc từ sách và có chăng là cảm xúc của người khác trong những bài văn mẫu.

Cảm ơn Nhạc sĩ **Trần Tiến**, Ca sĩ **Tùng Dương**

# I. Introduction

Recently, I saw my manager coding a web application for the customer. It has some functions: drawing, communicating, and presenting their ideas to other people. I think the application will look like Google Docs but the sure thing is it is not as good as =))

On the weekends, I have pretty much free time, and instead of surfing the website or watching YouTube =)) I try to implement the code for creating a web application. It will resolve the basic problem. In the article, I will guide and present to everyone about my idea and how to develop it.

About WebSocket

*WebSocket is a two-way communication protocol over a single TCP connection. It allows communication between the browser and the server to take place in real-time, without having to reopen the connection each time data is sent. WebSockets are useful for applications that need constantly updated information such as chat, online games, real-time dashboards, and more.*

![IoT Hub: What Use Case for WebSockets? | Scaleway](https://www-uploads.scaleway.com/blog-websockets-bigger-4.webp align="center")

# II. Idea

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727626152112/edf26671-c89e-4a95-9c81-988c7805ed3d.png align="center")

**The sequence diagram presents three flows.**

* Create a channel and after the server will return a link to join the channel
    
* Initiate a connection to WebSocket whenever the user joins the channel.
    
* Store the date and send it to everyone who joins the channel.
    

**The detail flow:**

* When the user needs to present, they will create a link that contains the channel’s information. They will send it to everyone in their group.
    
* Receivers click the link and join this channel → the server will initiate a WebSocket connection with a UUID.
    
* Whenever a person changes the data → the system will call to the server an API to store data and send it to all connections WebSocket in this channel.
    

This is a basic idea so we must resolve many problems in real. Special, I think the conflict data is the most difficult problem :D

# III. Implement

## 1\. Back end

About the back end, I use the WebSocket library with Golang language. I will create a variable to store all channel information. The channel information is stored by hashmap and in this map, I define another hashmap to store all connections WebSocket of this channel.

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

Four APIs: LoadPage, InitWebsocket, CloseWebsocket, SaveData

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

About the user interface, I get a source code on the internet. I will handle about JavaScript. The link is below thank bro!

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

Before initiating the new connection WebSocket, I will close the old connection WebSocket.

UUID will auto-generate by the crypto - a library of JavaScript.

This code will handle open, close, and receive event messages from the WebSocket server.

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

This call will handle closing the old connection WebSocket.

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

This code will handle storing data. Here, I set a timeout that every 500ms when the user stops clicking, the data will be saved to the server and the server will send this data to other connections WebSocket.

# IV. Demo

<center><iframe width="560" height="315" src="https://www.youtube.com/embed/Vg_IFPAbF2A?si=riBewTzy6V9xH8VG"></iframe></center>

# V. Reference

* Source code HTML: [https://codepen.io/fajarnurwahid/pen/NWvxeXj](https://codepen.io/fajarnurwahid/pen/NWvxeXj)
    
* Source code: [https://github.com/nguyenvantuan2391996/example-code/tree/master/docs-editor-using-websocket](https://github.com/nguyenvantuan2391996/example-code/tree/master/docs-editor-using-websocket)