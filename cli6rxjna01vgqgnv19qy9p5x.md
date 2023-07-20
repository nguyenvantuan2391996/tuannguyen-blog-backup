---
title: "Xây dựng ứng dụng nghe nhạc trực tuyến với JavaScript"
seoTitle: "Xây dựng ứng dụng nghe nhạc trực tuyến youtube với JavaScript"
seoDescription: "Ứng dụng nghe nhạc - nơi sẽ dẫn bạn vào một thế giới âm nhạc tuyệt vời và đa dạng. Với sự phát triển không ngừng của công nghệ, nghe nhạc đã trở nên dễ dàng"
datePublished: Sun May 28 2023 02:00:39 GMT+0000 (Coordinated Universal Time)
cuid: cli6rxjna01vgqgnv19qy9p5x
slug: xay-dung-ung-dung-nghe-nhac-truc-tuyen-voi-javascript
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1685692826598/cef57441-7c72-4fd4-90d0-fd64267c617c.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1685692841081/67061eb2-9be4-4e38-82d6-6cfd4dc5a2b7.jpeg
tags: js, javascript, musicplayer

---

# I. Giới thiệu về ứng dụng nghe nhạc

Ứng dụng nghe nhạc - nơi sẽ dẫn bạn vào một thế giới âm nhạc tuyệt vời và đa dạng. Với sự phát triển không ngừng của công nghệ, nghe nhạc đã trở nên dễ dàng và tiện lợi hơn bao giờ hết. Những ứng dụng nghe nhạc phổ biến và tuyệt vời dưới đây.

1. **Spotify**: Với hơn một triệu bài hát và nhiều thể loại âm nhạc khác nhau, Spotify là một trong những ứng dụng nghe nhạc hàng đầu trên thị trường. Bạn có thể tìm kiếm, nghe và chia sẻ nhạc yêu thích của mình một cách dễ dàng.
    
2. **Apple Music**: Đối với người dùng hệ sinh thái Apple, Apple Music là lựa chọn hoàn hảo. Với kho nhạc phong phú, các bài hát và album mới nhất, bạn có thể truy cập và nghe nhạc trên nhiều thiết bị Apple khác nhau.
    
3. **YouTube Music**: Với YouTube Music, bạn có thể trải nghiệm âm nhạc từ hàng triệu bài hát và video âm nhạc trên YouTube. Ứng dụng này cung cấp các danh sách phát cá nhân, gợi ý dựa trên sở thích và nhu cầu nghe nhạc của bạn.
    
4. **SoundCloud**: SoundCloud là một nền tảng nghe nhạc trực tuyến cho phép người dùng tìm kiếm và phát các bài hát từ các nghệ sĩ độc lập và nhạc sĩ mới nổi. Bạn có thể tìm thấy các bản remix, bài hát chưa phát hành và nhiều nội dung độc đáo khác trên SoundCloud.
    
5. **Deezer**: Deezer cung cấp thư viện âm nhạc rộng lớn với hàng triệu bài hát từ nhiều thể loại khác nhau. Bạn có thể tạo danh sách phát cá nhân, khám phá bản sao chất lượng cao (lossless) và tận hưởng trải nghiệm nghe nhạc không giới hạn.
    
    ![Top phần mềm nghe phổ biến hiện nay - Kênh Tool](https://kenhtool.com/wp-content/uploads/2021/12/phan-mem-nghe.jpg align="center")
    

Vậy tại sao chúng ta không tự tạo cho riêng mình một ứng dụng nghe nhạc trực tuyến :D Hôm nay cùng mình tạo ứng dụng nghe nhạc với JavaScript. Nguồn nhạc mình sẽ lấy từ trang youtube.

# II. Cấu trúc mã nguồn

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1684686907773/e1de8c55-ed9a-43ad-82d0-a775439eca72.png align="center")

* css: Thư mục lưu các file css để căn chỉnh, làm đẹp giao diện.
    
* js: Thư mục chứa các file JavaScript để xử lý các chức năng của game.
    
* home.html: Giao diện ứng dụng nghe nhạc.
    

# III. Xây dựng ứng dụng

## 1\. Giao diện

* home.html: Giao diện ứng dụng nghe nhạc.
    

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta name="viewport" content="width=device-width, initial-scale=1.0" charset="UTF-8">
    <title>Music Player Manager By Tuan Nguyen</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.2/css/all.min.css" integrity="sha512-HK5fgLBL+xu6dm/Ii3z4xhlSUyZgTT9tuc/hSrtw6uzJOvgRr2a9jyxxT1ely+B+xFAmJKVSTbpM/CuL7qxO8w==" crossorigin="anonymous" />
    <link rel="preconnect" href="https://fonts.gstatic.com">
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600&display=swap" rel="stylesheet">
    <link href="css/music.css" rel="stylesheet">
    <script src="https://cdn.jsdelivr.net/npm/sweetalert2@11"></script>
</head>
    <body>
        <div class="wrapper">
            <div class="player__container">
                <div class="player__body">
                    <div class="body__cover">
                        <ul class="list list--cover">
                            <li>
                                <a class="list__link" href=""><i class="fa fa-navicon"></i></a>
                            </li>
                            <li>
                                <a class="list__link" href=""></a>
                            </li>
                            <li>
                                <a id="btn-search" type="button" class="list__link" href=""><i class="fa fa-search"></i></a>
                            </li>
                        </ul>

                        <img id="album-cover" src="http://ecx.images-amazon.com/images/I/51XSHShbPiL.jpg" alt="Album cover" />

                        <div class="range"></div>
                    </div>

                    <div class="body__info">
                        <div id="song-name" class="info__song"></div>
                        <div id="song-artist" class="info__artist"></div>
                    </div>

                    <div class="body__buttons">
                        <ul class="list list--buttons">
                            <li><a id="btn-backward" type="button" class="list__link"><i class="fa fa-step-backward"></i></a></li>
                            <li><a id="btn-play-song" type="button" class="list__link"><i id="icon-play-pause" class="fa fa-play"></i></a></li>
                            <li><a id="btn-forward" type="button" class="list__link"><i class="fa fa-step-forward"></i></a></li>
                        </ul>
                    </div>
                </div>

                <div class="player__footer">
                    <ul class="list list--footer">
                        <li><a href="#" class="list__link"><i class="fa fa-heart"></i></a></li>
                        <li><a href="#" class="list__link"><i class="fa fa-random"></i></a></li>
                        <li><a href="#" class="list__link"><i class="fa fa-undo"></i></a></li>
                        <li><a href="#" class="list__link"><i class="fa fa-ellipsis-h"></i></a></li>
                    </ul>
                </div>
            </div>
        </div>
        <iframe hidden id="song" src="" frameborder="0" allowfullscreen playsinline allow="autoplay; encrypted-media"></iframe>
    </body>
</html>

<script type="text/javascript" src="js/constants.js"></script>
<script type="text/javascript" src="js/music.js"></script>
<script type="text/javascript" src="js/utils.js"></script>
<script type="text/javascript" src="js/youtubeAPI.js"></script>
```

music.css: Thêm style vào các phần tử HTML đó như đổi bố cục, màu sắc trang, đổi màu chữ cho giao diện ứng dụng.

```css
html {
    font-size: 16px;
}
body {
    font-family: 'Roboto', Arial, Verdana, sans-serif;
    background: #e4f2fb;
}
a {
    text-decoration: none;
}
.player__container {
    margin-top: 2rem;
    margin-right: auto;
    margin-left: auto;
    max-width: 30rem;
    height: 50rem;
    background: #fff;
    border-radius: 0.25rem;
    box-shadow: 0 10px 20px -5px rgba(0, 0, 0, .19), 0 6px 6px -10px rgba(0, 0, 0, .23);
}
.body__cover {
    position: relative;
}
.body__cover img {
    max-width: 100%;
    border-radius: 0.25rem;
}
.list {
    display: flex;
    margin: 0;
    padding: 0;
    list-style-type: none;
}
.body__buttons, .body__info, .player__footer {
    padding-right: 2rem;
    padding-left: 2rem;
}
.list--cover, .list--footer {
    justify-content: space-between;
}
.list--header .list__link, .list--footer .list__link {
    color: #888;
}
.list--cover {
    position: absolute;
    top: 0.5rem;
    width: 100%;
}
.list--cover li:first-of-type {
    margin-left: 0.75rem;
}
.list--cover li:last-of-type {
    margin-right: 0.75rem;
}
.list--cover a {
    font-size: 1.15rem;
    color: #fff;
}
.range {
    position: relative;
    top: -1.5rem;
    right: 0;
    left: 0;
    margin: auto;
    background: rgba(255, 255, 255, .95);
    width: 80%;
    height: 0.125rem;
    border-radius: 0.25rem;
    cursor: pointer;
}
.range:before, .range:after {
    content: "";
    position: absolute;
    cursor: pointer;
}
.range:before {
    width: 3rem;
    height: 100%;
    background: linear-gradient(to right, rgba(211, 3, 32, .5), rgba(211, 3, 32, .85));
    border-radius: 0.25rem;
    overflow: hidden;
}
.range:after {
    top: -0.375rem;
    left: 3rem;
    z-index: 3;
    width: 0.875rem;
    height: 0.875rem;
    background: #fff;
    border-radius: 50%;
    box-shadow: 0 0 3px rgba(0, 0, 0, .15), 0 2px 4px rgba(0, 0, 0, .15);
    transition: all 0.25s cubic-bezier(0.4, 0, 1, 1);
}
.range:focus:after, .range:hover:after {
    background: rgba(211, 3, 32, .95);
}
.body__info {
    padding-top: 1.5rem;
    padding-bottom: 1.25rem;
    text-align: center;
}
.info__album, .info__song {
    margin-bottom: 0.5rem;
}
.info__artist, .info__album {
    font-size: 0.75rem;
    font-weight: 300;
    color: #666;
}
.info__song {
    font-size: 1.15rem;
    font-weight: 400;
    color: #d30320;
}
.body__buttons {
    padding-bottom: 2rem;
}
.body__buttons {
    padding-top: 1rem;
}
.list--buttons {
    align-items: center;
    justify-content: center;
}
.list--buttons li:nth-of-type(n+2) {
    margin-left: 1.25rem;
}
.list--buttons a {
    padding: 0.45rem 0.75rem;
    font-size: 1rem;
    border-radius: 50%;
    box-shadow: 0 3px 6px rgba(33, 33, 33, .1), 0 3px 12px rgba(33, 33, 33, .15);
}
.list--buttons a:focus, .list--buttons a:hover {
    color: rgba(171, 2, 26, .95);
    opacity: 1;
    box-shadow: 0 6px 9px rgba(33, 33, 33, .1), 0 6px 16px rgba(33, 33, 33, .15);
}
.list--buttons li:nth-of-type(2) a {
    padding: 0.82rem 1rem 0.82rem 1.19rem;
    margin-left: 0.5rem;
    font-size: 1.25rem;
    color: rgba(211, 3, 32, .95);
}
.list--buttons li:first-of-type a, .list--buttons li:last-of-type a {
    font-size: 0.95rem;
    color: #212121;
    opacity: 0.5;
}
.list--buttons li:first-of-type a:focus, .list--buttons li:last-of-type a:focus, .list--buttons li:first-of-type a:hover, .list--buttons li:last-of-type a:hover {
    color: #d30320;
    opacity: 0.75;
}
.list__link {
    transition: all 0.25s cubic-bezier(0.4, 0, 1, 1);
}
.list__link:focus, .list__link:hover {
    color: #d30320;
}
.player__footer {
    padding-top: 1rem;
    padding-bottom: 2rem;
}
.list--footer a {
    opacity: 0.5;
}
.list--footer a:focus, .list--footer a:hover {
    opacity: 0.9;
}
```

Mình có tham khảo mã nguồn giao diện ở đây. Thanks bro nào đó đã share source code nhé [Music player UI design](https://codepen.io/alexdevero/pen/aZjLNw)

Vì phần giao diện html và css cũng không có gì quá phức tạp nên các bạn có thể tự đọc hiểu nó nhé! 😄😄😄 Sau khi html và css 1 chút chúng ta sẽ được giao diện như thế này.

Giao diện ứng dụng trên desktop

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1684687318189/dfeffaf5-3f9d-41da-b3c5-700298e8c0f4.png align="center")

Giao diện ứng dụng trên mobile

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1684687433420/448e5c20-cec4-4b53-9e78-6a79ba139afa.jpeg align="center")

## 2\. Tạo API key Youtube

Trước khi vào các chức năng chi tiết. Do nguồn nhạc từ youtube nên mọi người generate một api key để lấy thông tin và phát nhạc của youtube. Các bước lấy api key như sau:

* Vào trang [Google Developers Console](https://console.developers.google.com/).
    
* Tạo 1 project mới hoặc chọn một project đã có sẵn.
    
* Bật tính năng YouTube Data API cho project.
    
* Generate một API key cho project.
    

## 3\. Xử lý các API Youtube

Xử lý tìm danh sách bài hát theo từ khoá tìm kiếm. Ở đây mình lấy 10 bài hát gần nhất với từ khoá theo search của Youtube

```JavaScript
async function searchKey(keyword) {
  return await searchYouTube(keyword, API_KEY);
}

async function searchYouTube(keyword, apiKey) {
  // Perform keyword search using YouTube Data API v3
  const searchResponse = await fetch(
    `https://www.googleapis.com/youtube/v3/search?part=snippet&maxResults=10&q=${encodeURIComponent(
      keyword
    )}&key=${apiKey}`
  );
  const searchResult = await searchResponse.json();

  return searchResult.items.map((item) => item.id.videoId);
}
```

Lấy thông tin của bài hát theo id: tiêu đề, kênh phát video, thumbnail của video,...

```JavaScript
async function getInfoSong(id) {
  let res = {
    title: NOT_FOUND,
    artist: NOT_FOUND,
    thumbnail: NOT_FOUND,
  };

  await fetch(
    `https://www.googleapis.com/youtube/v3/videos?id=` +
      id +
      `&part=snippet&key=` +
      API_KEY
  )
    .then((response) => response.json())
    .then((data) => {
      const video = data.items[0];
      res.artist = video.snippet.channelTitle;
      res.title = video.snippet.title;
      res.thumbnail = video.snippet.thumbnails.standard
        ? video.snippet.thumbnails.standard.url
        : video.snippet.thumbnails.high.url;
    })
    .catch((error) => {
      console.error("Error:", error);
    });

  return res;
}
```

Hai function này chỉ là gọi API của Youtube theo từ khoá tìm kiếm. Các bạn đọc document của Youtube là okela. Không vấn đề gì về phần này cả.

## 4\. Chức năng tìm kiếm bài hát

Ở phần này mình có dùng thư viện sweet alert để cho giao diện đẹp hơn. Các bước cũng đơn giản. Đầu tiên chúng ta sẽ có hàm **getInfoTheSong** hàm này có chức năng gọi hàm xử lý lấy thông tin bài hát ở phía trên và hiển thị các giá trị lên giao diện màn hình.

```JavaScript
async function getInfoTheSong(id) {
  const songInfo = await getInfoSong(id);
  document.getElementById("song-name").textContent = songInfo.title;
  document.getElementById("song-artist").textContent = songInfo.artist;
  if (songInfo.thumbnail || songInfo.thumbnail !== NOT_FOUND) {
    document.getElementById("album-cover").src = songInfo.thumbnail;
  }
}
```

Ở bước xử lý tìm kiếm ta sẽ xử lý từng bước như sau:

* Nhận được đầu vào input key search của người dùng.
    
* Gọi hàm **searchKey** phía trên để lấy thông tin danh sách bài hát.
    
* Lưu danh sách bài hát và default selected bài hát đầu tiên.
    
* Phát nhạc.
    

```JavaScript
document.getElementById("btn-search").addEventListener("click", (event) => {
  event.preventDefault();
  Swal.fire({
    title: "Điền tên bài hát",
    input: "text",
    inputAttributes: {
      autocapitalize: "off",
    },
    showCancelButton: true,
    confirmButtonText: "Search",
    showLoaderOnConfirm: true,
    preConfirm: async (songName) => {
      const ids = await searchKey(songName);
      if (ids.length === 0) {
        AlertError(NOT_FOUND_SONG, "");
        return;
      }

      localStorage.setItem(VIDEO_YOUTUBE_ID, ids);
      localStorage.setItem(SELECTED_VIDEO_ID, ids[0]);
      document.getElementById("song").src =
        "https://www.youtube.com/embed/" +
        ids[0] +
        "?rel=0&amp;controls=0&amp;showinfo=0&amp;autoplay=1";
      document.getElementById("icon-play-pause").className = "fa fa-pause";

      // get the song's information
      await getInfoTheSong(ids[0]);
    },
    allowOutsideClick: () => !Swal.isLoading(),
  }).then((result) => {});
});
```

## 5\. Chức năng play/pause

Ở bước xử lý này ta kiểm tra nếu trạng thái và bật tắt nhạc tương ứng với trạng thái hiện tại

```JavaScript
document.getElementById("btn-play-song").addEventListener("click", (event) => {
  document.getElementById("icon-play-pause").className =
    document.getElementById("icon-play-pause").className === "fa fa-play"
      ? "fa fa-pause"
      : "fa fa-play";
  const isAuto =
    document.getElementById("icon-play-pause").className === "fa fa-play"
      ? "0"
      : "1";
  document.getElementById("song").src =
    "https://www.youtube.com/embed/" +
    localStorage.getItem(SELECTED_VIDEO_ID) +
    "?rel=0&amp;controls=0&amp;showinfo=0&amp;autoplay=" +
    isAuto;
})
```

## 6\. Chức năng chọn bài trước và bài kế tiếp

Trước khi tới chi tiết từng chức năng. Ta có hàm lấy vị trí mới của bài hát

* Nếu người dùng next bài =&gt; bài hát kế tiếp =&gt; vị trí bài hát tăng thêm 1. Tuy nhiên nếu vị trí mới vượt quá số lượng bài hát trong list =&gt; quay lại bài hát đầu tiên =&gt; vị trí mới là 0.
    
* Nếu người dùng previous bài =&gt; bài hát trước =&gt; vị trí bài hát trừ thêm 1. Tuy nhiên nếu vị trí mới nhỏ hơn 0 =&gt; quay lại bài hát cuối cùng =&gt; vị trí mới là length danh sách - 1.
    

```JavaScript
function getNewIndex(type) {
  const ids = localStorage.getItem(VIDEO_YOUTUBE_ID).split(",");
  const selectedID = localStorage.getItem(SELECTED_VIDEO_ID);
  let newIndex = 0;
  for (let i = 0; i < ids.length; i++) {
    if (ids[i] === selectedID && type === "backward") {
      newIndex = i - 1;
      return newIndex < 0 ? ids.length - 1 : newIndex;
    }

    if (ids[i] === selectedID && type === "forward") {
      newIndex = i + 1;
      return newIndex > ids.length - 1 ? 0 : newIndex;
    }
  }

  return newIndex;
}
```

**Chức năng chọn bài kế tiếp**

```JavaScript
document
  .getElementById("btn-forward")
  .addEventListener("click", async (event) => {
    const newForwardIndex = getNewIndex("forward");
    const ids = localStorage.getItem(VIDEO_YOUTUBE_ID).split(",");
    localStorage.setItem(SELECTED_VIDEO_ID, ids[newForwardIndex]);

    document.getElementById("song").src =
      "https://www.youtube.com/embed/" +
      ids[newForwardIndex] +
      "?rel=0&amp;controls=0&amp;showinfo=0&amp;autoplay=1";
    await getInfoTheSong(ids[newForwardIndex]);
  });
```

**Chức năng chọn bài trước**

```JavaScript
document
  .getElementById("btn-backward")
  .addEventListener("click", async (event) => {
    const newBackwardIndex = getNewIndex("backward");
    const ids = localStorage.getItem(VIDEO_YOUTUBE_ID).split(",");
    localStorage.setItem(SELECTED_VIDEO_ID, ids[newBackwardIndex]);

    document.getElementById("song").src =
      "https://www.youtube.com/embed/" +
      ids[newBackwardIndex] +
      "?rel=0&amp;controls=0&amp;showinfo=0&amp;autoplay=1";
    await getInfoTheSong(ids[newBackwardIndex]);
  });
```

# IV. Kết luận

Đây là 1 số hình ảnh và video kết quả sau khi build code xong nhé mọi người ^^

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1684689165180/ebd1d763-ba1f-4b0e-9af5-dc4f4151a2ab.png align="center")

<center><iframe width="560" height="315" src="https://www.youtube.com/embed/Jn5PETnoRYI"></iframe></center>

* Website ứng dụng: https://nguyenvantuan2391996.github.io/game-development/applications/music-player/home.html
    
* Source code: https://github.com/nguyenvantuan2391996/game-development/tree/master/applications/music-player