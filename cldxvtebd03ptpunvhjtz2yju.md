# [Phần 2] Trò chơi Audition - Xây dựng chức năng nhảy 8K với JavaScript

# 1\. Giới thiệu

**Audition** - Một tựa game online mỗi khi nhắc tới thì đại đa số thế hệ **8x** và **9x** đời đầu sẽ bất chợt có cảm giác bồi hồi, xao xuyến với bao nhiêu kỉ niệm ùa về 😊😊😊 Là thế hệ giữa 9x nhưng may sao ngày bé, mình được các ông anh dẫn đi NET cỏ đưa mình vào con đường nghiện game online ngày ấy... 😃

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1655017143339/b-5rUEGc8.png align="center")

Ở [Phần 1](%5Bhttps://tuannguyenhust.hashnode.dev/phan-1-tro-choi-audition-xay-dung-chuc-nang-co-ban-voi-javascript%5D) mình đã hướng dẫn mọi người xây dựng các tính năng cơ bản : select music, dance with type 4K, 4K reverse, tính điểm,... Trong bài viết ở phần 2 này, mình sẽ hướng dẫn thêm chức năng *dance with type 8K and 8K reverse*, *chức năng home page để chọn lựa kiểu nhảy và chọn nhạc*

Mình hi vọng sẽ được sự đón nhận, phản hồi từ mọi người để game được tốt và nhiều chức năng hơn 😘

# 2\. Home page Audition

```xml
<h1>Guide</h1>
<table>
    <tr class="heading">
        <th>Button</th>
        <th>Content</th>
    </tr>
    <tr>
        <td>Del</td>
        <td>Dance with reverse button</td>
    </tr>
    <tr>
        <td>⬆ ⬇ ⬅ ➡</td>
        <td>Up Down Left Right</td>
    </tr>
    <tr>
        <td>↖ ↙ ↗ ↘</td>
        <td>Left-Up Left-Down Right-Up Right-Down</td>
    </tr>
</table>

<div>
    <div class="options" >
        <label for="list-music"></label><select id="list-music" class="hide-option option">
        <option selected="selected" disabled="disabled" value="">Select music</option>
        <option value="https://firebasestorage.googleapis.com/v0/b/tuvandaihoc-c8a1c.appspot.com/o/Waiting%20For%20You%20-%20MONO_%20Onionn.mp3?alt=media&token=a10b4da9-9967-4dc4-b120-76c7e08e13e2">Waiting For You - Mono</option>
        <option value="https://firebasestorage.googleapis.com/v0/b/tuvandaihoc-c8a1c.appspot.com/o/dua%20nao%20lam%20em%20buon_%20-%20Phuc%20Du.mp3?alt=media&token=5123bcc3-3305-408c-adf9-e3cb3d95d8cd">Đứa nào làm em buồn? - Phúc Du</option>
        <option value="https://firebasestorage.googleapis.com/v0/b/tuvandaihoc-c8a1c.appspot.com/o/See%20Tinh%20-%20Hoang%20Thuy%20Linh.mp3?alt=media&token=87095bae-98a7-4d18-9573-2e129d0c136b">See Tình - Hoàng Thuỳ Linh</option>
    </select>
    </div>

    <div class="options" style="margin-top: 30px">
        <label for="list-type-dance"></label><select id="list-type-dance" class="hide-option option">
        <option selected="selected" disabled="disabled" value="">Select type dance</option>
        <option value="4k">4K Dance</option>
        <option value="8k">8K Dance</option>
    </select>
    </div>
</div>

<div class="button" id="button" onclick="handleLetGo()">Let's go!</div>
```

* home.html: File này chứa giao diện trang chủ cho phép mọi người lựa chọn nhạc và kiểu nhảy ( Mình không phải dân chuyên Front-end nên html, css không tốt lắm nên các bạn cố gắng bỏ qua nha 😃😃😃 )
    

```javascript
function handleLetGo() {
    let music = document.getElementById("list-music").value;
    let typeDance = document.getElementById("list-type-dance").value;

    if (music === "" || typeDance === "") {
        alert("Vui lòng chọn nhạc và kiểu nhảy");
        return
    }
    window.location.href = "/game-development/games/audition/audition.html?music="+ music + "&type=" + typeDance;
}

window.addEventListener("load", (event) => {
    let music = LIST_MUSIC[Math.floor(Math.random()*LIST_MUSIC.length)];
    let audio = new Audio(music);
    audio.play().catch(function (error) {
        console.log("Chrome cannot play sound without user interaction first" + error)
    });
});
```

* audition-home.js : Xử lý khi load trang chủ sẽ random bật 1 bài nhạc chạy trong nền dựa vào sự kiện *load*. Mỗi khi người dùng lựa chọn nhạc và kiểu nhảy xong thì sẽ xử lý redirect vào trong giao diện chính của trò chơi.
    

# 3\. Xử lý trò chơi cho chức năng nhảy 8K, 8K reverse

```javascript
const LIST_KEY_HAS_REVERSE_8K = ["right", "up", "down", "left", "right-reverse", "up-reverse", "down-reverse", "left-reverse", "right-up", "left-up", "right-down", "left-down", "right-up-reverse", "left-up-reverse", "right-down-reverse", "left-down-reverse"]
const LIST_KEY_8K = ["right", "up", "down", "left", "right-up", "left-up", "right-down", "left-down"]
const MAP_KEY_8K = new Map([
    ["right", "right"],
    ["up", "up"],
    ["down", "down"],
    ["left", "left"],
    ["right-reverse", "left"],
    ["up-reverse", "down"],
    ["down-reverse", "up"],
    ["left-reverse", "right"],
    ["right-up", "right-up"],
    ["left-up", "left-up"],
    ["right-down", "right-down"],
    ["left-down", "left-down"],
    ["right-up-reverse", "left-down"],
    ["left-up-reverse", "right-down"],
    ["right-down-reverse", "left-up"],
    ["left-down-reverse", "right-up"],
])
```

* LIST\_KEY\_8K : Danh sách phím bấm gồm 8 nút : lên, xuống, trái, phải, chéo phải lên, chéo phải xuống, chéo trái lên, chéo trái xuống
    
* LIST\_KEY\_HAS\_REVERSE\_8K : Danh sách phím bấm trường hợp nút ngược
    
* MAP\_KEY\_8K : Map lưu value nút tương ứng khi người chơi bấm. VD right : right, right-reverse : left,...
    

```javascript
function initAudio() {
    clearInterval(intervalID)

    const urlParams = new URLSearchParams(window.location.search);
    if (urlParams.get('music') === null || urlParams.get('type') === null) {
        window.location.href = "../audition/home.html";
    }

    audio.src = urlParams.get('music')
    audio.play().catch(function (error) {
        console.log("Chrome cannot play sound without user interaction first" + error)
    });

    typeDance = urlParams.get('type')

    intervalID = setInterval(move, 0)
    initVariable()
}

window.addEventListener("load", initAudio)
```

* initAudio : Khởi tạo audio phát nhạc dựa vào giá trị query param trên url
    

```javascript
setTimeout(function () {
    listKeyRandom = isReverse ?
        (typeDance === "4k" ?
                getListKey(level, LIST_KEY_HAS_REVERSE_4K) : getListKey(level, LIST_KEY_HAS_REVERSE_8K)
        ) :
        (typeDance === "4k" ?
                getListKey(level, LIST_KEY_4K) : getListKey(level, LIST_KEY_8K)
        )
    console.log(listKeyRandom)
    for (let i = 0; i < listKeyRandom.length; i++) {
        setKey(listKeyRandom[i], i + 1)
    }
}, 1000)
```

*listKeyRandom:* Lấy danh sách random key theo từng kiểu nhảy. Nếu:

* Nếu bật reverse và kiểu nhảy 4K =&gt; random LIST\_KEY\_HAS\_REVERSE\_4K
    
* Nếu bật reverse và kiểu nhảy 8K =&gt; random LIST\_KEY\_HAS\_REVERSE\_8K
    
* Nếu không bật reverse và kiểu nhảy 4K =&gt; random LIST\_KEY\_4K
    
* Nếu không bật reverse và kiểu nhảy 8K =&gt; random LIST\_KEY\_8K
    

```javascript
function compareKeyPressAndRandom(key) {
    if (listKeyPress.length === listKeyRandom.length) {
        return
    }
    const mapKey = typeDance === "4k" ? MAP_KEY_4K : MAP_KEY_8K
    if (mapKey.get(listKeyRandom[listKeyPress.length]) === key && !isReverse) {
        listKeyPress.push(key + "-success")
        setKey(key + "-success", listKeyPress.length)
    } else if (mapKey.get(listKeyRandom[listKeyPress.length]) === key && isReverse) {
        listKeyPress.push(key + "-success")
        setKey(key + "-success", listKeyPress.length)
    } else {
        listKeyPress = []
        for (let i = 0; i < listKeyRandom.length; i++) {
            setKey(listKeyRandom[i], i + 1)
        }
    }
}
```

*compareKeyPressAndRandom:* so sánh value phím bấm mỗi khi người chơi bấm. Nếu:

* Kiểu nhảy 4K =&gt; tìm trong MAP\_KEY\_4K
    
* Kiểu nhảy 8K =&gt; tìm trong MAP\_KEY\_8K Khi value phím người chơi bấm:
    
* Tồn tại =&gt; đánh dấu phím đó người chơi bấm đúng.
    
* Không có trong list nào =&gt; reset những phím người chơi đã bấm.
    

# 4\. Kết luận

Ở phần 2 này mình update mã code thêm nên sẽ có một số chỗ người mới đọc sẽ khó hiểu. Mọi người có thể xem lại bài viết [Phần 1 - xây dựng chức năng cơ bản](%5Bhttps://tuannguyenhust.hashnode.dev/phan-1-tro-choi-audition-xay-dung-chuc-nang-co-ban-voi-javascript%5D) ở đây nhé.

Đây là 1 số hình ảnh và video kết quả sau khi build code xong nhé mọi người ^^

<center><iframe width="560" height="315" src="https://www.youtube.com/embed/us5uUZF5m4M"></iframe></center>

* Website game: https://nguyenvantuan2391996.github.io/game-development/games/audition/home.html
    
* Source code: https://github.com/nguyenvantuan2391996/game-development/tree/master/games/audition