---
title: "Caro game - Developing game modes for Humans and Computers with JavaScript"
seoTitle: "Caro game - Developing game modes for Humans and Computers"
seoDescription: "Caro is a simple game that we all played as kids. we make it fancy, we can create two computers, and they can play with them"
datePublished: Mon Feb 27 2023 14:33:10 GMT+0000 (Coordinated Universal Time)
cuid: clemx6mbt000109mh9ow08ogx
slug: caro-game-developing-game-modes-for-humans-and-computers-with-javascript
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1677498616829/0cc415b4-4c99-407b-8945-9dba23809915.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1677508331408/402355b9-5a13-4246-babc-72780fe9ed4b.png
tags: javascript, game-development, games, gomoku, caro

---

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

# 1\. Introduce

## 1.1. Overview

Caro is a simple game that we all played as kids. We can play games in two modes: human vs human or, more advanced, human vs computer. Howerver, we make it fancy, we can create two computers, and they can play with them. In the article, I will develop the game with three modes:

* Human vs Human
    
* Human vs Computer
    
* Computer vs Computer
    

## 1.2. Introduce the caro game

Gomoku has existed in Japan since before the Meiji Restoration (1868).\[7\] The name "gomoku" is from the Japanese language, in which it is referred to as gomokunarabe (五目並べ). Go means five, moku is a counter word for pieces and narabe means line-up. The game is popular in China, where it is called Wuziqi (五子棋).\[8\] Wu (五 wǔ) means five, zi (子 zǐ) means piece, and qi (棋 qí) refers to a board game category in Chinese. The game is also popular in Korea, where it is called omok (오목 \[五目\]) which has the same structure and origin as the Japanese name. In the nineteenth century, the game was introduced to Britain where it was known as Go Bang, said to be a corruption of the Japanese word goban, which was itself adapted from the Chinese k'i pan (qí pán) "go-board."

## 1.3. The game rules

Freestyle Gomoku has no restrictions on either player and allows a player to win by creating a line of five or more stones, with each player alternating turns placing one stone at a time.

Black (the player who makes the first move) has long been known to have an advantage, even before L. Victor Allis proved that black could force a win (see below). Renju attempts to mitigate this imbalance with extra rules that aim to reduce blacks' first-player advantage. It is played on a 15×15 board, with the rules of three and three, four and four, and overlines applied to Black only.

* The rule of three and three bans a move that simultaneously forms two open rows of three stones (rows not blocked by an opponent's stone at either end).
    
* The rule of four and four bans a move that simultaneously forms two rows of four stones (open or not).
    
* Overlines prevent a player from winning if they form a line of 6 or more stones. Renju also makes use of various tournament opening rules, such as Soosõrv-8, the current international standard.
    

In Caro, (also called gomoku+, popular among Vietnamese), the winner must have an overline or an unbroken row of five stones that is not blocked at either end (overlines are immune to this rule). This makes the game more balanced and provides more power for White to defend.

# 2\. The game project structure

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1677426859081/41a8ea0a-11fc-4b70-a78a-9d644e3e2486.png align="center")

* **css**: This is the folder where all CSS files are saved: style.html, make pages beautiful, and so on.
    
* **images**: This is the folder where all images of the game are saved: background, X, O,...
    
* **js**: This is the folder where all JavaScript files are saved: handle and process all functions of the game.
    
* **caro.html, home.html**: The game interface.
    

# 3\. Developing the game

## 3.1. The game interface

* **home.html** : Caro game's home page.
    

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

* **caro.html**: The main game interface
    

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

* **caro.css**: We can add style to all HTML elements: color page, background, font, and size words.
    

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

Because the game interface only has HTML and CSS. It is not complicated or difficult, so let's read and understand it! 😄😄😄 Below is the result

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1677427583957/cc869fa4-aa06-4e9f-bb28-96eee39a4b87.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1677427590832/d1ee5e93-9a88-44ad-a2b1-accdf1e4120c.png align="center")

## 3.2. Handling all functions of the game

**caro-home.js**: This is the file that handles all functions and events on the homepage. On the homepage, we will handle only events that occur when players click the button **Let's go**. We will add three parameters: game mode, the sum of rows, and the sum of columns.

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

**caro-main.js**: This is the file that handles all the main events of the game. At this point, there are a lot of events and functions that we will develop. Let's develop the game with me! 😁😁😁

Of course! The first time, the game will init variables, events, music, and images,... **function init** will retrieve the homepage's value for the game mode, the sum of rows, and the sum of columns. Based on that information, we will create a game matrix with the *sum of rows* x the *sum of columns*. The game matrix will save the game's state and draw it by table tag.

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

Handling the checking of the draw and win states in the game is pretty simple:

* Draw state game: We will loop through all of the elements in the game matrix (rows x columns). If all elements are denoted by "" then this location does not have the turn to play. And all elements! = "" =&gt; The game is draw.
    
* Win state game: We will check the horizontal, vertical, right diagonal, and left diagonal. If it has more than 5 in the same turn, the player or computer will win the game.
    

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

## 3.3. Game mode human and human

We handled all game functions in 3.2, including init games, checking draws, and winning the game. In 3.3, we will handle the event when a player or computer plays the turn on the game matrix.

* If the function **processClick** returns the value "win" then we will notify a message with the content "X/O is the winner" and initiate the game again.
    
* If the function **processClick** returns the value "draw " hen we will notify a message with the content "Draw" and initiate the game again.
    

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

**function processClick**: Handling the event when the player or computer plays the turn in the "human vs. human" game mode. We will follow the below steps:

* Step 1: If this location in the game matrix had a value of "X" or "O" then we will return void.
    
* Step 2: If the player is X then set the value of the location in the game matrix to "x" and draw X on the game interface `html document.getElementById(id).innerHTML = XText;`
    
* Step 3: If the player is O then set the value of the location in the game matrix to "o" and draw X on the game interface `html document.getElementById(id).innerHTML = OText;`
    
* Step 4: Checking whether the game is a draw or a win. If it is draw, then return "draw" and if it is win then return "win".
    
* Step 5: Swap the turn `javascript player = player === X ? O : X;`
    

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

## 3.4. Game mode human and computer

There are pretty many algorithms: minimax, hill climbing,... They are used to choose some of the optimal turns. Example with the minimax algorithm:

*Minimax is a decision rule used in artificial intelligence, decision theory, game theory, statistics, and philosophy for minimizing the possible loss for a worst-case (maximum loss) scenario. When dealing with gains, it is referred to as "maximin" – to maximize the minimum gain. Originally formulated for several-player zero-sum game theory, covering both the cases where players take alternate moves and those where they make simultaneous moves, it has also been extended to more complex games and to general decision-making in the presence of uncertainty.*

![](https://images.viblo.asia/4db4c04a-890f-405c-9399-6e1dd0448a18.png align="center")

Here, we will simplify the game and, based on the player's previous experience with the game, determine the player's strategy to win.

Reference paper:

See this paper: [L. Victor Allis, H. J. van den Herik, M. P. H. Huntjens, 1993. Go-Moku and Threat-Space Search](https://web.archive.org/web/20140411074912/http://chalmersgomoku.googlecode.com/files/allis1994.pdf)

Let's go 😘😘😘

We will create 2 constants: *MAP\_SCORE\_COMPUTER*, *MAP\_POINT\_HUMAN*

* *MAP\_SCORE\_COMPUTER*: We will check if it has the 5 in the same turn =&gt; win =&gt; The score of this turn is Infinity. If it has the 4 in the same turn =&gt; the score = is 2000 points. If it has the 3 in the same turn =&gt; the score = is 500 points. If it has the 2 in the same turn =&gt; the score = 300 points. If it has the 1 in the same turn =&gt; the score = 100 points. This score represents the *attack*.
    
* *MAP\_POINT\_HUMAN*: If it has the 4 in the same turn =&gt; score = 999999 points. If it has the 3 in the same turn =&gt; the score = 1000 points. If it has the 2 in the same turn =&gt; the score = 400 points. If it has the 1 in the same turn =&gt; the score = 10 points. If it has the zero in the same turn =&gt; the score = 0 points. This score represents the *defense*.
    

```javascript
const MAP_SCORE_COMPUTER = new Map([
    [5, Infinity], [4, 2000], [3, 500], [2, 300], [1, 100]
])
const MAP_POINT_HUMAN = new Map([
    [4, 999999], [3, 1000], [2, 400], [1, 10], [0, 0]
])
```

**function getPointsComputer**: The function will obtain the location, which is the computer's turn.

* Step 1: *maxScore* is the highest possible score that the computer can achieve. *listScorePoint* is an array of tickable locations for the computer, *pointsComputer* is an array of the computer's highest possible score.
    
* Step 2: We will loop through the game matrix and check it. If the value of the location is "" then we will get the highest the same turn following horizontal, vertical, right diagonal, and left diagonal. The score of the turn = **MAP\_SCORE\_COMPUTER\[số nước liền kề lớn nhất\] + MAP\_POINT\_HUMAN\[the highest the same turn\]**
    
* Step 3: We will loop through the game matrix that has score equal maxScore =&gt; push element into *pointsComputer*
    
* Step 4: Because each game state will have numerous turns with the same maxScore: To make it more equitable, we will assign points at random.
    

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

## 3.5. Game mode computer and computer

The game mode is fancy 😄😄😄 In the game mode, we will replace the human's turn with another computer. We still use the function getPointsComputer to get the turn location.

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

We will check if the game mode is "computer and computer" in the load event, and then call the function "computer and computer" to have two computers play with them.

Note : *let sumPoints = matrixGame.length x matrixGame\[0\].length* is the sum of maximum the turns. If it reaches maximum the turn then the game state is draw.

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

# 4\. Conclusion

Here are some of the images and videos that resulted. ^^

<center><iframe width="560" height="315" src="https://www.youtube.com/embed/jm5ieVDNo8g"></iframe></center>

* Website game: https://nguyenvantuan2391996.github.io/game-development/games/caro/home.html
    
* Source code: https://github.com/nguyenvantuan2391996/game-development/tree/master/games/caro
    

Reference: https://tuannguyenhust.hashnode.dev/