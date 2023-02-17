# [Phần 3] Trò chơi Audition - Xây dựng chức năng nhảy Beat Up với JavaScript

# 1\. Giới thiệu

**Audition** - Một tựa game online mỗi khi nhắc tới thì đại đa số thế hệ **8x** và **9x** đời đầu sẽ bất chợt có cảm giác bồi hồi, xao xuyến với bao nhiêu kỉ niệm ùa về 😊😊😊 Là thế hệ giữa 9x nhưng may sao ngày bé, mình được các ông anh dẫn đi NET cỏ đưa mình vào con đường nghiện game online ngày ấy... 😃

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1655017143339/b-5rUEGc8.png align="center")

Ở [Phần 1](%5Bhttps://tuannguyenhust.hashnode.dev/phan-1-tro-choi-audition-xay-dung-chuc-nang-co-ban-voi-javascript%5D) mình đã hướng dẫn mọi người xây dựng các tính năng cơ bản : select music, dance with type 4K, 4K reverse, tính điểm,... Và [Phần 2](https://tuannguyenhust.hashnode.dev/phan-2-tro-choi-audition-xay-dung-chuc-nang-nhay-8k-voi-javascript) mình đã hướng dẫn thêm chức năng *dance with type 8K and 8K reverse*, *chức năng home page để chọn lựa kiểu nhảy và chọn nhạc*

Trong phần 3 này mình sẽ cùng mọi người xây dựng thêm chế độ nhảy **Beat Up** trong game Audition.

**Beat Up** :

* Đây là chế độ nhảy chỉ sử dụng bàn phím số, nhưng khác với chế độ nhảy 8k , cách phím số được sử dụng trong chế độ này là: 1,4,7,3,6,9 - khác với 8K vì chế độ này không có 2 phím lên xuống , Các phím xác định là Spacebar , 0 , 5.
    
* Khác với chế độ nhảy bình thường , bạn phải nhấn phím tương ứng với mũi tên truợt đến chình giữa khung hình khi thanh bar màu xanh đạt đến vạch màu đỏ.
    
    ![Hướng dẫn và thảo luận về cách chơi Beat Up... Detay](https://2img.net/h/i53.photobucket.com/albums/g67/ducbe/detay.jpg align="center")

![Test Beat Up Audition VTC - Let It Go - ( Nhảy Còn Gà :( ) Production  Rapper Gi - YouTube](https://i.ytimg.com/vi/drb65ecEFOo/maxresdefault.jpg align="center")

Về phần giao diện dùng HTML, CSS do mình không chuyên về Front-end nên giao diện game sẽ không được đẹp và chỉnh chu 😃😃😃 Hi vọng sẽ được sự đón nhận, phản hồi từ mọi người để game được tốt và nhiều chức năng hơn 😘

# 2\. Giao diện chơi

## 2.1. Home page chế độ Beat Up

```html
<div class="options" style="margin-top: 30px">
        <label for="list-type-dance"></label><select id="list-type-dance" class="hide-option option">
        <option selected="selected" disabled="disabled" value="">Select type dance</option>
        <option value="4k">4K Dance</option>
        <option value="8k">8K Dance</option>
        <option value="beat-up">Beat-up Dance</option>
    </select>
    </div>
```

**home.html**: Thêm 1 option để select chế độ chơi Beat Up

## 2.2. Giao diện Audition chế độ Beat Up

```html
<div id="beat-up-dance">
    <div id="result-beat-up">
        <img id="pic-beat-up" src="images/Ready.png" class="center" alt="">
    </div>

    <div class="bars-beat-up">
        <div class="layout">
            <p class="col col-main">
                <img id="left-up-beat-up" src="images/left-up.png" style="width: 80px; height: 80px; margin: -25px;" alt="left-up">
                <div class="position-space-beat-up" style="margin-left: 460px; height: 62px; opacity: 40%"></div>
            <p class="col col-complementary" role="complementary">
                <img id="right-up-beat-up" src="images/right-up.png" style="width: 80px; height: 80px; margin: -25px -25px -25px 450px;" alt="right-up">
                <div class="position-space-beat-up" style="margin-left: 640px; height: 62px; opacity: 40%"></div>
        </div>
    </div>

    <div class="bars-beat-up">
        <div class="layout">
            <p class="col col-main">
                <img id="left-beat-up" src="images/left.png" style="width: 50px; height: 50px; margin: -10px" alt="left">
                <div class="position-space-beat-up" style="margin-left: 460px; height: 62px; opacity: 40%"></div>
            <p class="col col-complementary" role="complementary">
                <img id="right-beat-up" src="images/right.png" style="width: 50px; height: 50px; margin: -8px -25px -25px 470px;" alt="right">
                <div class="position-space-beat-up" style="margin-left: 640px; height: 62px; opacity: 40%"></div>
        </div>
    </div>

    <div class="bars-beat-up">
        <div class="layout">
            <p class="col col-main">
                <img id="left-down-beat-up" src="images/left-down.png" style="width: 80px; height: 80px; margin: -25px" alt="left-down">
                <div class="position-space-beat-up" style="margin-left: 460px; height: 62px; opacity: 40%"></div>
            <p class="col col-complementary" role="complementary">
                <img id="right-down-beat-up" src="images/right-down.png" style="width: 80px; height: 80px; margin: -25px -25px -25px 450px;" alt="right-down">
                <div class="position-space-beat-up" style="margin-left: 640px; height: 62px; opacity: 40%"></div>
        </div>
    </div>

    <div class="container-beat-up">
        <div class="position-space-beat-up" id="position-space-beat-up"></div>
        <div class="box" id="box-beat-up"></div>
    </div>
</div>
```

**audition.html** : Thêm giao diện và css chế độ Beat Up. Chúng ta thêm 3 thanh bars ở 2 bên trái, phải chứa các phím để người chơi bấm và phần thanh bar - người chơi căn tới vạch đỏ

# 3. Xử lý trò chơi cho chức năng nhảy Beat Up

```javascript
function moveLeftUp() {
    if (posLeftUp < 450) {
        posLeftUp += increase
    }
    leftUpBeatUpElement.style.marginLeft = posLeftUp + "px"
    if (posLeftUp === 450) {
        hide("left-up-beat-up")
        setTimeout(function () {
            posLeftUp = 0
        }, timeLeftUp)
        show("left-up-beat-up")
    }
}

function moveRightUp() {
    if (posRightUp > 0) {
        posRightUp -= increase
    }
    rightUpBeatUpElement.style.marginLeft = posRightUp + "px"
    if (posRightUp === 0) {
        hide("right-up-beat-up")
        setTimeout(function () {
            posRightUp = 450
        }, timeRightUp)
        show("right-up-beat-up")
    }
}

function moveLeft() {
    if (posLeft < 450) {
        posLeft += increase
    }
    leftBeatUpElement.style.marginLeft = posLeft + "px"
    if (posLeft === 450) {
        hide("left-beat-up")
        setTimeout(function () {
            posLeft = 0
        }, timeLeft)
        show("left-beat-up")
    }
}

function moveRight() {
    if (posRight > 0) {
        posRight -= increase
    }
    rightBeatUpElement.style.marginLeft = posRight + "px"
    if (posRight === 0) {
        hide("right-beat-up")
        setTimeout(function () {
            posRight = 450
        }, timeRight)
        show("right-beat-up")
    }
}

function moveLeftDown() {
    if (posLeftDown < 450) {
        posLeftDown += increase
    }
    leftDownBeatUpElement.style.marginLeft = posLeftDown + "px"
    if (posLeftDown === 450) {
        hide("left-down-beat-up")
        setTimeout(function () {
            posLeftDown = 0
        }, timeLeftDown)
        show("left-down-beat-up")
    }
}

function moveRightDown() {
    if (posRightDown > 0) {
        posRightDown -= increase
    }
    rightDownBeatUpElement.style.marginLeft = posRight + "px"
    if (posRight === 0) {
        hide("right-down-beat-up")
        setTimeout(function () {
            posRight = 450
        }, timeRightDown)
        show("right-down-beat-up")
    }
}
```
**moveLeftUp**, **moveRightUp**, **moveLeft**, **moveRight**, **moveLeftDown**, **moveRightDown** : 6 functions này mỗi function xử lý việc chuyển động của các phím mà người chơi cần bấm. Chúng sẽ thay đổi vị trí theo từng px để chạy tới vạch cần bấm.

```javascript
function moveSpaceBeatUp() {
    if (posSpaceBeatUp < 450) {
        posSpaceBeatUp += increase
    }
    spaceBeatUpElement.style.left = posSpaceBeatUp + "px"
    if (posSpaceBeatUp === 450) {
        posSpaceBeatUp = 0
    }
}
```
**moveSpaceBeatUp**: function này cũng tương tự như 6 functions trên => nó để xử lý việc chuyển động của box di chuyển tới vạch cần press *Space*.

```javascript
// 280 - 410
function setScoreBeatUpSpace(pos) {
    if (325 <= pos && pos <= 365) {
        picBeatUpElement.src = "images/Perfect.png"
        score += isReverse ? 1200 : 800
    } else if ((310 <= pos && pos < 325) || (365 < pos && pos <= 380)) {
        picBeatUpElement.src = "images/Great.png"
        score += isReverse ? 600 : 350
    } else if ((295 <= pos && pos < 310) || (380 < pos && pos <= 395)) {
        picBeatUpElement.src = "images/Cool.png"
        score += isReverse ? 350 : 150
    } else if ((280 <= pos && pos < 295) || (395 < pos && pos <= 410)) {
        picBeatUpElement.src = "images/Bad.png"
        score += isReverse ? 200 : 50
    } else {
        picBeatUpElement.src = "images/Miss.png"
    }
    scoreElement.textContent = score
}

// 370 - 450
function setScoreBeatUpLeft(pos) {
    if (400 <= pos && pos <= 420) {
        picBeatUpElement.src = "images/Perfect.png"
        score += isReverse ? 1200 : 800
    } else if ((390 <= pos && pos < 400) || (420 < pos && pos <= 430)) {
        picBeatUpElement.src = "images/Great.png"
        score += isReverse ? 600 : 350
    } else if ((380 <= pos && pos < 390) || (430 < pos && pos <= 440)) {
        picBeatUpElement.src = "images/Cool.png"
        score += isReverse ? 350 : 150
    } else if ((370 <= pos && pos < 380) || (440 < pos && pos <= 450)) {
        picBeatUpElement.src = "images/Bad.png"
        score += isReverse ? 200 : 50
    } else {
        picBeatUpElement.src = "images/Miss.png"
    }
    scoreElement.textContent = score
}

// 0 - 80
function setScoreBeatUpRight(pos) {
    if (30 <= pos && pos <= 50) {
        picBeatUpElement.src = "images/Perfect.png"
        score += isReverse ? 1200 : 800
    } else if ((20 <= pos && pos < 30) || (50 < pos && pos <= 60)) {
        picBeatUpElement.src = "images/Great.png"
        score += isReverse ? 600 : 350
    } else if ((10 <= pos && pos < 20) || (60 < pos && pos <= 70)) {
        picBeatUpElement.src = "images/Cool.png"
        score += isReverse ? 350 : 150
    } else if ((0 <= pos && pos < 10) || (70 < pos && pos <= 80)) {
        picBeatUpElement.src = "images/Bad.png"
        score += isReverse ? 200 : 50
    } else {
        picBeatUpElement.src = "images/Miss.png"
    }
    scoreElement.textContent = score
}
```
**setScoreBeatUpSpace**, **setScoreBeatUpLeft**, **setScoreBeatUpRight** : 3 functions set lại điểm khi người chơi press phím. Mình dựa vào pixel mà box di chuyển + vị trí của vạch cần press để phân chia điểm số : perfect, create, cool, bad or miss.

```javascript
if (e.code === "Space" || e.code === "Numpad5") {
    hide("box-beat-up")
    setScoreBeatUpSpace(posSpaceBeatUp)
    setTimeout(function() {
        show("box-beat-up")
        posSpaceBeatUp = 0
    }, 3000)
}

// Key dance
if (e.code === "ArrowLeft" || e.code === "Numpad4") {
    setScoreBeatUpLeft(posLeft)
    posLeft = 0
}
if (e.code === "Numpad7") {
    setScoreBeatUpLeft(posLeftUp)
    posLeftUp = 0
}
if (e.code === "Numpad1") {
    setScoreBeatUpLeft(posLeftDown)
    posLeftDown = 0
}
if (e.code === "ArrowRight" || e.code === "Numpad6") {
    setScoreBeatUpRight(posRight)
    posRight = 0
}
if (e.code === "Numpad9") {
    setScoreBeatUpRight(posRightUp)
    posRightUp = 0
}
if (e.code === "Numpad3") {
    setScoreBeatUpRight(posRightDown)
    posRightDown = 0
}
```
Đoạn mã code JavaScript này để xử lý mỗi khi người chơi press phím bấm và nút space : Gọi function set điểm và set lại vị trí của phím mỗi khi press xong.

# 4. Kết luận

Đây là 1 số hình ảnh và video kết quả sau khi build code xong nhé mọi người ^^

<center><iframe width="560" height="315" src="https://www.youtube.com/embed/2atNbUNvhKA"></iframe></center>

* Website game: https://nguyenvantuan2391996.github.io/game-development/games/audition/home.html
    
* Source code: https://github.com/nguyenvantuan2391996/game-development/tree/master/games/audition
