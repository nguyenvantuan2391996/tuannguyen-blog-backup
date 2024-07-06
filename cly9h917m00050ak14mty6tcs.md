---
title: "Websocket - Chat in real-time with Golang"
seoTitle: "Websocket - Chat in real-time with Golang"
seoDescription: "I will start with 2 channels: Alice and Bob. When Alice enters the message chat, it will send data to the web socket server with channel Alice."
datePublished: Sat Jul 06 2024 02:00:15 GMT+0000 (Coordinated Universal Time)
cuid: cly9h917m00050ak14mty6tcs
slug: websocket-chat-in-real-time-with-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1720204744204/472592f1-ac35-4a86-968b-4c1db68bf937.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1720204810946/2d281b2b-29b6-4651-90f6-865d71072a24.jpeg
tags: websockets, go, javascript

---

![](https://camo.githubusercontent.com/550568f767db0382fca4b561c2fc0c43e9c0d8aa6e7bb610796d50c6aa3eb62f/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f7964505752544e4d523378386d415754646b2f67697068792e676966 align="center")

Today Saturday, July 6, 2024, I hope Portugal will win France and play a semi-final and champion Euro 2024.

Siuuu

# I. How to the system work?

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720203068751/e606fc2e-16c5-4068-ba13-39b4e1762c79.png align="center")

I will start with 2 channels: Alice and Bob. When Alice enters the message chat, it will send data to the web socket server with channel Alice. We will receive the data and write the message data to Bob's channel -&gt; Bob will read the message. The opposite will be the same.

# II. Implement

## 1\. Back-end

```go
var upgrader = websocket.Upgrader{
	ReadBufferSize:  1024,
	WriteBufferSize: 1024,
}

var mapWsConn = make(map[string]*websocket.Conn)

func main() {
	http.HandleFunc("/chat", LoadPageChat)
	http.HandleFunc("/ws", InitWebsocket)

	log.Fatal(http.ListenAndServe(":3000", nil))
}
```

The main goroutine will init 2 API: load page chat and init web socket server. The server will run in port 3000

```go
func LoadPageChat(w http.ResponseWriter, r *http.Request) {
	w.Header().Set("Access-Control-Allow-Origin", "*")

	path, err := os.Getwd()
	if err != nil {
		fmt.Fprintf(w, "%s", "error")
		return
	}

	content, err := os.ReadFile(path + "/chat-using-websocket/chat.html")
	if err != nil {
		fmt.Fprintf(w, "%s", "error")
		return
	}

	fmt.Fprintf(w, "%s", content)
}
```

The function **LoadPageChat** will read the file chat.html and handle the user interface. The API will return HTML for front-end to display for the user.

```go
func InitWebsocket(w http.ResponseWriter, r *http.Request) {
	w.Header().Set("Access-Control-Allow-Origin", "*")

	channel := r.URL.Query().Get("channel")
	if r.Header.Get("Origin") != "http://"+r.Host {
		fmt.Fprintf(w, "%s", "error")
		return
	}

	if _, ok := mapWsConn[channel]; !ok {
		conn, err := upgrader.Upgrade(w, r, nil)
		if err != nil {
			fmt.Fprintf(w, "%s", "error")
			return
		}

		mapWsConn[channel] = conn
	}

	for {
		var msg map[string]string
		err := mapWsConn[channel].ReadJSON(&msg)
		if err != nil {
			fmt.Println("Error reading JSON:", err)
			break
		}
		fmt.Printf("Received: %s\n", msg)

		otherConn := getConn(channel)
		if otherConn == nil {
			continue
		}

		err = otherConn.WriteJSON(msg)
		if err != nil {
			fmt.Println("Error writing JSON:", err)
			break
		}
	}
}

func getConn(channel string) *websocket.Conn {
	for key, conn := range mapWsConn {
		if key != channel {
			return conn
		}
	}

	return nil
}
```

The function will init web socket's connection. We will consume the channel to get the message and send it to another channel.

Example: Alice sends the message **"Hi"** to channel Alice -&gt; the channel Alice will receive **"Hi"** \-&gt; write the message **"Hi"** to channel Bob

## 2\. Front-end

```xml
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Chat application</title>
</head>
<body>
<div class="container">
    <div class="chat">
        <div class="santaSays">
            <div class="text-box-santa">
                <div class="text">
                    <p>Hi there, my child!</p>
                    <p>What can I help you with?</p>
                </div>
            </div>
        </div>
        <div class="userSays">
            <div class="text">
                <p>Hello, Santa!</p>
                <p>I'd like to know when you'll bring my gift?</p>
            </div>
        </div>
    </div>
    <hr>
    <div class="message-box">
        <div class="message-input">
            <input id="inputText" type="text" placeholder="What can I help you with?">
        </div>
        <div class="send-btn">
            <i class="fa-solid fa-paper-plane plane"></i>
        </div>
    </div>
</div>
</body>
</html>
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720204125271/bf05786d-d94d-4a0f-a5b3-40228e3dcf0e.png align="center")

This is the chat application interface.

```javascript
<script>
    const chat = document.querySelector(".chat");
    const inputText = document.getElementById("inputText");
    let ws;

    if (window.WebSocket === undefined) {
        console.log("Your browser does not support WebSockets")
    } else {
        ws = initWS();
    }

    function initWS() {
        let socket = new WebSocket("ws://" + window.location.host + "/ws" + window.location.search)

        socket.onopen = function() {
            console.log("Socket is open")
        };

        // receive data from server
        socket.onmessage = function (e) {
            let pS = document.createElement("p");
            pS.innerHTML = JSON.parse(e.data).message;
            pS.classList.add("santaMessage");
            chat.appendChild(pS);
            chat.scrollTop = chat.scrollHeight;
        }

        // close socket
        socket.onclose = function () {
            console.log("Socket closed")
        }

        return socket;
    }

    inputText.addEventListener("keyup", (e) => {
        if (e.key === "Enter") {
            let pU = document.createElement("p");
            pU.innerHTML = inputText.value;
            pU.classList.add("userMessage");
            chat.appendChild(pU);
            chat.scrollTop = chat.scrollHeight;

            ws.send(JSON.stringify({message: inputText.value}));
            inputText.value = "";
        }
    })
</script>
```

The source JavaScript loads the page, init web socket, sends the message to the web socket server and displays it to the interface.

# III. Result

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720204470779/8af090a7-5c63-4ff9-b76a-fb39736a763b.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720204474730/3bbcb0ff-922f-42fb-a14f-7e4d2fbac733.png align="center")

# IV. Reference

* Source code HTML: [https://codepen.io/nicoHDL/pen/KKJObjX](https://codepen.io/nicoHDL/pen/KKJObjX) thank bro!
    
* Source Golang: [https://github.com/nguyenvantuan2391996/example-code/tree/master/chat-using-websocket](https://github.com/nguyenvantuan2391996/example-code/tree/master/chat-using-websocket)