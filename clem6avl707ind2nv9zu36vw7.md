# Trò chơi cờ Caro - Xây dựng game chơi với Human và Computer bằng JavaScript

**Tháng ba bất chợt một ngày trắng tinh hoa sưa về đây... Hà Nội, Chủ Nhật 26/02/2023...**

*Ta vội bước trên phố phường Hà Nội*  
*Bỗng gặp hàng sưa trắng một màu hoa*  
*Trắng cả góc trời níu bước chân qua*  
*Lưu luyến qúa chùm hoa sưa lặng lẽ*

*Hương hoa không nồng nàn như hoa sữa*  
*Không sắc màu tím biếc cánh bằng lăng*  
*Không vàng thẫm một màu hoa điệp lan*  
*Mà nhẹ nhàng nét duyên sưa trắng muốt...*

![](https://voz.vn/attachments/dscf2938-jpg.1678776/ align="center")

# 1\. Giới thiệu

## 1.1. Tổng quan

Trò chơi cờ caro cũng không quá xa lạ với tuổi thơ của các bạn đúng không? Chúng ta có thể chơi cờ caro ở chế độ người vs người với nhau. Hoặc nâng cao hơn một chút sẽ chơi người với máy. Và trở nên fancy hơn nữa là chúng ta tạo ra 2 máy cho chúng tự chơi với nhau. Ở bài viết này cùng mình xây dựng trò chơi caro với 3 chế độ nhé:

* Human vs Human
    
* Human vs Computer
    
* Computer vs Computer
    

## 1.2. Giới thiệu trò chơi

Cờ ca-rô (hay sọc ca-rô) là một trò chơi dân gian. Cờ ca-rô trong tiếng Triều Tiên là omok (오목) và trong tiếng Nhật là 五目並べ (gomoku narabe); tiếng Anh, sử dụng lại tiếng Nhật, gọi là gomoku. Ban đầu loại cờ này được chơi bằng các quân cờ vây (quân cờ màu trắng và đen) trên một bàn cờ vây (19x19). Quân đen đi trước và người chơi lần lượt đặt một quân cờ của họ trên giao điểm còn trống. Người thắng là người đầu tiên có được một chuỗi liên tục gồm 4 quân hàng ngang, hoặc dọc, hoặc chéo không bị chặn đầu nào. Một khi đã đặt xuống, các quân cờ không thể di chuyển hoặc bỏ ra khỏi bàn, do đó loại cờ này có thể chơi bằng giấy bút. Ở Việt Nam, cờ này thường chơi trên giấy tập học sinh (đã có sẵn các ô ca-rô), dùng bút đánh dấu hình tròn (O) và chữ X để đại diện cho 2 quân cờ.

## 1.3. Luật chơi

Theo các giải thi đấu quốc tế hiện tại, Gomoku được chơi theo luật Swap2 để đáp ứng công bằng. Người đi trước xếp 3 quân cờ đầu tiên lên bàn theo ý muốn và để người đi sau chọn:

* Nếu đồng ý với thế cờ, người đi sau chỉ cần chơi tiếp từ nước thứ 4 như thường lệ.
    
* Nếu không đồng ý với thế cờ, người đi sau có thể đổi bên hoặc đặt thêm 2 quân cờ tiếp theo (tạo thế mới) để người đi trước chọn màu (quân cờ đầu tiên luôn là màu đen), ván cờ tiếp tục như thường lệ từ thế cờ đó.
    

Theo luật Standard gomoku, một hàng có nhiều hơn 5 quân liên tiếp cùng màu (overline) không được coi là thắng lợi, trận đấu vẫn tiếp tục cho đến khi một bên có đúng 5 quân trong hàng hoặc kết quả sẽ là hòa, nếu không có ai đáp ứng điều kiện đó. Biến thể Free gomoku thì chỉ cần có từ 5 quân thẳng hàng trở lên, không cần cố định phải là đúng 5 quân như luật Standard. Trong thi đấu quốc tế, luật thi đấu dựa trên hình thức: người nào ăn 5 trên 9 ván cờ trước thì thắng.

# 2\. Cấu trúc game project

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1677426859081/41a8ea0a-11fc-4b70-a78a-9d644e3e2486.png align="center")

* **css**: Thư mục lưu các file css để căn chỉnh, làm đẹp giao diện.
    
* **images**: Thư mục sẽ lưu các hình ảnh của trò chơi : Phím bấm, ảnh nền,...
    
* **js**: Thư mục chứa các file JavaScript để xử lý các chức năng của game.
    
* **caro.html, home.html**: Giao diện trò chơi.
    

# 3\. Xây dựng trò chơi

## 3.1. Giao diện trò chơi

* **home.html** : Giao diện trang chủ game Ca-rô
    

```html
<!DOCTYPE html>
<html lang="en">

<link rel="stylesheet" href="../common/css/home.css">
<link rel="stylesheet" href="css/caro.css">

<head>
    <meta charset="UTF-8">
    <title>Home game tic tac toe</title>
</head>
<body>
<div>
    <div class="options">
        <label for="list-type-play"></label><select id="list-type-play" class="hide-option option">
        <option selected="selected" disabled="disabled" value="">Select type play</option>
        <option value="2-players">2 players</option>
        <option value="player-computer">Player and computer</option>
        <option value="computer-computer">Computer and computer</option>
    </select>
    </div>

    <div class="options" style="margin-top: 30px">
        <label for="list-row"></label><select id="list-row" class="hide-option option">
        <option selected="selected" disabled="disabled" value="">Select amount of rows</option>
        <option value="10">10</option>
        <option value="20">20</option>
        <option value="30">30</option>
        <option value="40">40</option>
        <option value="50">50</option>
        <option value="60">60</option>
    </select>
    </div>

    <div class="options" style="margin-top: 30px">
        <label for="list-column"></label><select id="list-column" class="hide-option option">
        <option selected="selected" disabled="disabled" value="">Select amount of columns</option>
        <option value="10">10</option>
        <option value="20">20</option>
        <option value="30">30</option>
        <option value="40">40</option>
        <option value="50">50</option>
        <option value="60">60</option>
    </select>
    </div>
</div>

<div class="button" id="button" onclick="handleLetGo()">Let's go!</div>
</body>
</html>

<script type="text/javascript" src="js/caro-home.js"></script>
```

* **caro.html**: Giao diện trò chơi
    

```html
<!DOCTYPE html>
<html lang="en">

<link rel="stylesheet" href="css/caro.css">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Game caro</title>
    <link href="https://fonts.googleapis.com/css?family=Indie+Flower" rel="stylesheet">
</head>
<body>

<h1>Game caro X-O</h1>
<table id="table_game">
</table>

</body>
</html>

<script type="text/javascript" src="js/constants.js"></script>
<script type="text/javascript" src="js/caro-main.js"></script>
```

* **caro.css**: Thêm style vào các phần tử HTML đó như đổi bố cục, màu sắc trang, đổi màu chữ cho trang home và giao diện trò chơi
    

```css
body {
    background-color: rgb(32, 32, 32);
    background-image: url("https://janschreiber.github.io/img2/black-chalk.jpg");
    color: rgb(230, 230, 230);
    text-align: center;
    font-family: 'Indie Flower', 'Comic Sans', cursive;
    font-size: 0.7em;
}
h1 {
    line-height: 1em;
    margin-bottom: 0;
    padding-bottom: 5px;
    font-size: 2.8em;
    font-weight: bold;
}
h2 {
    font-size: 1.3em;
    font-weight: bold;
    padding: 0;
    margin: 0;

}
h3 {
    font-size: 1.1em;
    text-decoration: underline;
    text-decoration-style: dashed;
    padding: 0;
    margin: 10px 0 2px 0;
}
table {
    margin: 2% auto;
    border-collapse: collapse;
}
#table_game {
    position: relative;
    font-size: 120px;
    margin: 1% auto;
    border-collapse: collapse;
}
.td_game {
    border: 4px solid rgb(230, 230, 230);
    width: 90px;
    height: 90px;
    padding: 0;
    vertical-align: middle;
    text-align: center;
}
.fixed {
    width: 90px;
    height: 90px;
    line-height: 90px;
    display: block;
    overflow: hidden;
    cursor: pointer;
}
.td_list {
    text-align: center;
    font-size: 1.3em;
    font-weight: bold;
}
.th_list {
    font-size: 1.3em;
    font-weight: bold;
    text-align: center;
    text-decoration: underline;
}
#restart {
    font-size: 3em;
    width: 1em;
    height: 0.9em;
    cursor: pointer;
    margin: 0 auto;
    overflow: hidden;
}
.x {
    color: darksalmon;
    position: relative;
    top: -8px;
    font-size: 1.2em;
    cursor: default;
}
.o {
    color: aquamarine;
    position: relative;
    top: -7px;
    font-size: 1.0em;
    cursor: default;
}

/* modal background */
.modal {
    display: none;
    position: fixed;
    z-index: 1;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    overflow: auto; /* enable scroll if needed */
    background-color: black; /* fallback color */
    background-color: rgba(0, 0, 0, 0.6);
}

/* modal content */
.modal-content {
    background-color: rgb(240, 240, 240);
    color: rgb(32, 32, 32);
    font-size: 2em;
    font-weight: bold;
    /* 16 % from the top and centered */
    margin: 16% auto;
    padding: 20px;
    border: 2px solid black;
    border-radius: 10px;
    width: 380px;
    max-width: 80%;
}
.modal-content p {
    margin: 0;
    padding: 0;
}

/* close button for modal dialog */
.close {
    color: rgb(170, 170, 170);
    float: right;
    position: relative;
    top: -25px;
    right: -10px;
    font-size: 34px;
    font-weight: bold;
}
.close:hover,
.close:focus {
    color: black;
    text-decoration: none;
    cursor: pointer;
}

.win-color {
    background-color: rgb(240, 240, 240);
}
```

Vì phần giao diện html và css cũng không có gì quá phức tạp nên các bạn có thể tự đọc hiểu nó nhé! 😄😄😄 Sau khi html và css 1 chút chúng ta sẽ được giao diện như thế này.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1677427583957/cc869fa4-aa06-4e9f-bb28-96eee39a4b87.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1677427590832/d1ee5e93-9a88-44ad-a2b1-accdf1e4120c.png align="center")

## 3.2. Xử lý các chức năng của trò chơi

**caro-home.js**: Xử lý các sự kiện, chức năng ở trang home. Ở trang này đơn giản chỉ có 1 sự kiện là click button **Let's go**. Ở chức năng này đơn giản ta chỉ cần truyền các tham số: *chế độ chơi*, *số dòng*, *số cột* của trò chơi.

```javascript
function handleLetGo() {
    let typePlay = document.getElementById("list-type-play").value;
    let rows = document.getElementById("list-row").value;
    let columns = document.getElementById("list-column").value;

    if (typePlay === "" || rows === "" || columns === "") {
        alert("Vui lòng chọn kiểu chơi");
        return
    }
    window.location.href = "/game-development/games/caro/caro.html?type=" + typePlay + "&rows=" + rows + "&columns=" + columns;
}
```

**caro-main.js**: Xử lý các sự kiện của trò chơi. Sẽ có rất nhiều sự kiện, chức năng chúng ta cần xây dựng. Cùng mình đi từng dòng code xem nó làm gì nhé 😁😁😁

Đương nhiên rồi, trò chơi gì đầu tiên cũng phải khởi tạo các biến, các sự kiện, nhạc, hình ảnh,... **function init** sẽ lấy thông tin *chế độ chơi*, *số dòng*, *số cột* từ trang home truyền qua. Dựa vào các thông tin đó chúng ta tạo ra 1 ma trận *số dòng* x *số cột* để lưu thông tin trạng thái của trò chơi và vẽ bảng ma trận này bằng thẻ table

```javascript
let tableXO = document.getElementById("table_game");
tableXO.innerHTML = tableContent
```

Full source code function init

```javascript
function init() {
    player = X;
    matrixGame = [];
    typeGame = TWO_PLAYER;
    const urlParams = new URLSearchParams(window.location.search);
    let rows = urlParams.get("rows");
    let columns = urlParams.get("columns");

    if (rows === "" || columns === "" || (urlParams.get("type") !== TWO_PLAYER && urlParams.get("type") !== COMPUTER && urlParams.get("type") !== COMPUTER_COMPUTER)) {
        window.location.href = "/game-development/games/caro/home.html";
    }

    typeGame = urlParams.get("type")

    // Data table
    let tableXO = document.getElementById("table_game");
    let tableContent = "";

    for (let row = 0; row < rows; row++) {
        let arr = [];
        let rowHTML = "<tr>";
        for (let col = 0; col < columns; col++) {
            arr.push("");
            rowHTML += `<td class="td_game"><div id="` + row.toString() + "-" + col.toString() + `" onclick="handleClick(this.id)" class="fixed"></div></td>`
        }
        rowHTML += "</tr>";

        tableContent += rowHTML;
        matrixGame.push(arr);
    }

    tableXO.innerHTML = tableContent
}

window.addEventListener("load", (event) => {
    init();
});
```

Việc xử lý kiểm tra trạng thái hoà và chiến thắng sau mỗi nước đi của từng người cũng không quá khó:

* Nếu trạng thái hoà: Chúng ta sẽ loop tất cả các element ma trận trạng thái trò chơi *số dòng* x *số cột*. Chúng ta kiểm tra mà thấy phần tử nào có giá trị = "" tức là chưa có nước đi ở phần từ đó =&gt; trò chơi chưa kết thúc và ngược lại nếu các phần tử của ma trận mà đều != "" =&gt; trò chơi hoà.
    
* Nếu trạng thái chiến thắng: Chúng ta kiểm tra hàng ngang, dọc, chéo phải, chéo trái. Nếu có lớn hơn hoặc bằng 5 nước chơi liên tiếp giống nhau =&gt; Người chơi đó dành chiến thắng trò chơi.
    

Source code:

*Draw*

```javascript
function checkDraw() {
    for (let i = 0; i < matrixGame.length; i++) {
        for (let j = 0; j < matrixGame[0].length; j++) {
            if (matrixGame[i][j] === "") {
                return false
            }
        }
    }

    return true
}
```

*Win*

```javascript
function getHorizontal(x, y, player) {
    let count = 1;
    for (let i = 1; i < 5; i++) {
        if (y + i < matrixGame[0].length && matrixGame[x][y + i] === player) {
            count++;
        } else {
            break
        }
    }

    for (let i = 1; i < 5; i++) {
        if (y - i >= 0 && y - i < matrixGame[0].length && matrixGame[x][y - i] === player) {
            count++;
        } else {
            break
        }
    }

    return count;
}

function getVertical(x, y, player) {
    let count = 1;
    for (let i = 1; i < 5; i++) {
        if (x + i < matrixGame.length && matrixGame[x + i][y] === player) {
            count++;
        } else {
            break
        }
    }

    for (let i = 1; i < 5; i++) {
        if (x - i >= 0 && x - i < matrixGame.length && matrixGame[x - i][y] === player) {
            count++;
        } else {
            break
        }
    }

    return count;
}

function getRightDiagonal(x, y, player) {
    let count = 1;
    for (let i = 1; i < 5; i++) {
        if (x - i >= 0 && x - i < matrixGame.length && y + i < matrixGame[0].length && matrixGame[x - i][y + i] === player) {
            count++;
        } else {
            break
        }
    }

    for (let i = 1; i < 5; i++) {
        if (x + i < matrixGame.length && y - i >= 0 && y - i < matrixGame[0].length && matrixGame[x + i][y - i] === player) {
            count++;
        } else {
            break
        }
    }

    return count;
}

function getLeftDiagonal(x, y, player) {
    let count = 1;
    for (let i = 1; i < 5; i++) {
        if (x - i >= 0 && x - i < matrixGame.length && y - i >= 0 && y - i < matrixGame[0].length && matrixGame[x - i][y - i] === player) {
            count++;
        } else {
            break
        }
    }

    for (let i = 1; i < 5; i++) {
        if (x + i < matrixGame.length && y + i < matrixGame[0].length && matrixGame[x + i][y + i] === player) {
            count++;
        } else {
            break
        }
    }

    return count;
}

function checkWin(points) {
    return getHorizontal(Number(points[0]), Number(points[1]), player) >= 5
    || getVertical(Number(points[0]), Number(points[1]), player) >= 5
    || getRightDiagonal(Number(points[0]), Number(points[1]), player) >= 5
    || getLeftDiagonal(Number(points[0]), Number(points[1]), player) >= 5
}
```

## 3.3. Chế độ người-người

Ở 3.2 chúng ta xây dựng source code để xử lý các chức năng: khởi tạo game, kiểm tra trạng thái game: hoà, chiến thắng. Ở 3.3 này chúng ta sẽ xử lý sự kiện khi người chơi đi nước chơi của mình trên ma trận trò chơi.

* Nếu function **processClick** trả về win =&gt; thông báo 1 message người chơi X/O dành chiến thắng =&gt; khởi tạo lại trò chơi.
    
* Nếu function **processClick** trả về draw =&gt; thông báo 1 message 2 người chơi hoà =&gt; khởi tạo lại trò chơi.
    

```javascript
function handleClick(id) {
    switch (processClick(id)) {
        case WIN:
            setTimeout(function () {
                alert("Player: " + player + " is winner");

                // reset game
                init();
            }, 100);
            break;
        case DRAW:
            setTimeout(function () {
                alert("Draw");

                // reset game
                init();
            }, 100);
            break;
    }
}
```

**function processClick**: Xử lý sự khi người chơi đánh nước cờ của mình ở chế độ người-người. Chúng ta thực hiện các kiểm tra sau:

* Step 1: Nếu vị trí đó trong ma trận trò chơi có giá trị "X" hoặc "O" rồi =&gt; đã có nước đánh =&gt; return void luôn.
    
* Step 2: Nếu người chơi là X =&gt; Set giá trị vị trí đó trên ma trận trò chơi là "x" =&gt; vẽ hình X trên giao diện `html document.getElementById(id).innerHTML = XText;`
    
* Step 3: Nếu người chơi là O =&gt; Set giá trị vị trí đó trên ma trận trò chơi là "o" =&gt; vẽ hình X trên giao diện `html document.getElementById(id).innerHTML = OText;`
    
* Step 4: Kiểm tra trạng thái game hoà hay win. Nếu hoà return "draw", win return "win".
    
* Step 5: Chuyển lượt chơi `javascript player = player === X ? O : X;`
    

```javascript
function processClick(id) {
    let points = id.split("-");

    switch (typeGame) {
        case TWO_PLAYER:

            if (matrixGame[Number(points[0])][Number(points[1])] === X || matrixGame[Number(points[0])][Number(points[1])] === O) {
                return
            }

            if (player === X) {
                matrixGame[Number(points[0])][Number(points[1])] = X;
                document.getElementById(id).innerHTML = XText;
            }

            if (player === O) {
                matrixGame[Number(points[0])][Number(points[1])] = O;
                document.getElementById(id).innerHTML = OText;
            }

            if (checkWin(points)) {
                return WIN;
            }

            // check draw
            if (checkDraw()) {
                return DRAW;
            }

            player = player === X ? O : X;
            break;
        case COMPUTER:
            // source code to process play with computer
    }
}
```

## 3.4. Chế độ người-computer

Có rất nhiều thuật toán: minimax, leo đồi, hàm lượng giá,... để áp dụng vào trò chơi để lựa chọn ra nước đi tối ưu nhất. Ví dụ về thuật toán minimax

*Giải thuật Minimax Hai người chơi trong game được đại diện là MAX và MIN. MAX đại diện cho người chơi luôn muốn chiến thắng và cố gắng tối ưu hóa ưu thế của mình còn MIN đại diện cho người chơi cố gắng cho người MAX giành số điểm càng thấp càng tốt. Giải thuật Minimax thể hiện bằng cách định trị các Node trên cây trò chơi: Node thuộc lớp MAX thì gán cho nó giá trị lớn nhất của con Node đó. Node thuộc lớp MIN thì gán cho nó giá trị nhỏ nhất của con Node đó. Từ các giá trị này người chơi sẽ lựa chọn cho mình nước đi tiếp theo hợp lý nhất.*

![](https://images.viblo.asia/4db4c04a-890f-405c-9399-6e1dd0448a18.png align="center")

Ở đây để đơn giản hoá chúng ta dựa vào kinh nghiệm, chiến lược của người chơi để dành chiến thắng chứ chưa cần phải áp dụng các thuật toán hay là trí tuệ nhân tạo

Các bạn có thể tham khảo thêm paper ở đây:

See this paper: [L. Victor Allis, H. J. van den Herik, M. P. H. Huntjens, 1993. Go-Moku and Threat-Space Search](https://web.archive.org/web/20140411074912/http://chalmersgomoku.googlecode.com/files/allis1994.pdf)

Vậy để áp dụng kinh nghiệm, chiến lược đó vào trò chơi như thế nào. Cùng mình xây dựng nhé 😘😘😘

Trước hết chúng ta tạo ra 2 hằng số: *MAP\_SCORE\_COMPUTER*, *MAP\_POINT\_HUMAN*

* *MAP\_SCORE\_COMPUTER*: Chúng ta sẽ kiểm tra nước đánh đó nếu có đủ 5 nước chơi của máy giống liền kề nhau =&gt; giành chiến thắng =&gt; điểm số cho nước đánh đó là dương vô cùng Infinity. Tương tự: 4 nước liền nhau =&gt; 2000 points, 3 nước liền nhau =&gt; 500 points, 2 nước liền nhau =&gt; 300 points, 1 nước liền nhau =&gt; 100 points. Số điểm này đại diện cho việc *tấn công*.
    
* *MAP\_POINT\_HUMAN*: Chúng ta sẽ kiểm tra nước đánh đó nếu có đủ 4 nước chơi của người giống liền kề nhau =&gt; cần ưu tiên đánh vào đây để *phòng thủ* =&gt; điểm số cho nước đánh đó là 999999. Tương tự: 3 nước liền nhau =&gt; 1000 points, 2 nước liền nhau =&gt; 400 points, 1 nước liền nhau =&gt; 10 points, 0 nước liền nhau =&gt; 0 points. Số điểm này đại diện cho việc *phòng thủ*.
    

```javascript
const MAP_SCORE_COMPUTER = new Map([
    [5, Infinity], [4, 2000], [3, 500], [2, 300], [1, 100]
])
const MAP_POINT_HUMAN = new Map([
    [4, 999999], [3, 1000], [2, 400], [1, 10], [0, 0]
])
```

**function getPointsComputer**: Lấy vị trí đánh của máy

* Step 1: *maxScore* =&gt; số điểm lớn nhất nếu máy đánh vào vị trí đó, *listScorePoint* =&gt; danh sách điểm theo từng theo từng vị trí có thể trên ma trận trò chơi, *pointsComputer* =&gt; danh sách các vị trí có điểm số cao nhất nếu máy đánh vào vị trí đó.
    
* Step 2: Loop ma trận trò chơi và kiểm tra nếu vị trí đó giá trị = "" ( chưa có nước đi ở đó ) =&gt; lấy số nước liền kề tối đa theo chiều ngang, dọc, chéo trái, chéo phải =&gt; số điểm nếu đánh vào vị trí đó = **MAP\_SCORE\_COMPUTER\[số nước liền kề lớn nhất\] + MAP\_POINT\_HUMAN\[số nước liền kề lớn nhất\]**
    
* Step 3: Loop danh sách điểm theo từng theo từng vị trí có thể trên ma trận trò chơi =&gt; push vị trí mà có điểm = maxScore ( số điểm tối đa ) vào danh sách các vị trí có điểm số cao nhất nếu máy đánh vào vị trí đó ( *pointsComputer* )
    
* Step 4: Vì mỗi trạng thái game đều sẽ có nhiều nước đi khác nhau mà có cùng số điểm tối đa =&gt; để khách quan và công bằng hơn thì chúng ta sẽ random trong pointsComputer.
    

```javascript
function getPointsComputer() {
    let maxScore = -Infinity
    let pointsComputer = []
    let listScorePoint = []
    for (let i = 0; i < matrixGame.length; i++) {
        for (let j = 0; j < matrixGame[0].length; j++) {
            if (matrixGame[i][j] === "") {
                let score = MAP_SCORE_COMPUTER.get(Math.max(getHorizontal(i, j, O),getVertical(i, j, O),getRightDiagonal(i, j, O),getLeftDiagonal(i, j, O))) +
                    MAP_POINT_HUMAN.get(Math.max(getHorizontal(i, j, X),getVertical(i, j, X),getRightDiagonal(i, j, X),getLeftDiagonal(i, j, X)) - 1)
                if (maxScore <= score) {
                    maxScore = score
                    listScorePoint.push({
                        "score": score,
                        "point": [i,j],
                    })
                }
            }
        }
    }

    // get list max score
    for (const element of listScorePoint) {
        if (element.score === maxScore) {
            pointsComputer.push(element.point)
        }
    }
    return pointsComputer[Math.floor(Math.random()*pointsComputer.length)]
}
```

## 3.5. Chế độ computer-computer

Chế độ này thật thú vị, fancy phải không mọi người 😄😄😄 Ở chế độ này chúng ta clone tương tự cách chơi human-computer. Thế chỗ lượt chơi của human bằng 1 computer khác. Chúng ta vẫn sử dụng hàm getPointsComputer để lấy vị trí đánh. Chúng ta delay 100ms cho có cảm giác máy suy nghĩ đánh nhé 😄😄😄

```javascript
async function ComputerAndComputer(sumPoints) {
    for (let i = 0; i < sumPoints; i++) {
        await delay(100);
        // computer A
        let pointsComputerA = getPointsComputer()
        matrixGame[pointsComputerA[0]][pointsComputerA[1]] = X;
        document.getElementById(pointsComputerA[0].toString() + "-" + pointsComputerA[1].toString()).innerHTML = XText;

        // check win
        if (checkWin(pointsComputerA)) {
            return WIN;
        }

        // check draw
        if (checkDraw()) {
            return DRAW;
        }

        player = player === X ? O : X;

        await delay(100);
        // computer B
        let pointsComputerB = getPointsComputer()
        matrixGame[pointsComputerB[0]][pointsComputerB[1]] = O;
        document.getElementById(pointsComputerB[0].toString() + "-" + pointsComputerB[1].toString()).innerHTML = OText;

        // check win
        if (checkWin(pointsComputerB)) {
            return WIN;
        }

        // check draw
        if (checkDraw()) {
            return DRAW;
        }

        player = player === X ? O : X;
    }
}
```

Ở function xử lý sự kiện load chúng ta kiểm tra nếu ở chế độ *comuter-computer* thì sẽ call function **ComputerAndComputer** để 2 máy tự chơi.

Note : *let sumPoints = matrixGame.length \* matrixGame\[0\].length* tổng số nước tối đa mà 2 máy có thể đi được. Nếu đi hết mà không ai thắng thì cờ sẽ hoà.

```javascript
window.addEventListener("load", (event) => {
    init();

    if(typeGame === COMPUTER_COMPUTER) {
        let sumPoints = matrixGame.length * matrixGame[0].length
        ComputerAndComputer(sumPoints).then(state => {
            switch (state) {
                case WIN:
                    setTimeout(function () {
                        alert("Player: " + player + " is winner");

                        // reset game
                        init();
                        location.reload();
                    }, 100);
                    break;
                case DRAW:
                    setTimeout(function () {
                        alert("Draw");

                        // reset game
                        init();
                        location.reload();
                    }, 100);
                    break;
            }
        })
    }
});
```

# 4\. Kết luận

Đây là 1 số hình ảnh và video kết quả sau khi build code xong nhé mọi người ^^

<center><iframe width="560" height="315" src="https://www.youtube.com/embed/jm5ieVDNo8g"></iframe></center>

* Website game: https://nguyenvantuan2391996.github.io/game-development/games/caro/home.html
    
* Source code: https://github.com/nguyenvantuan2391996/game-development/tree/master/games/caro