Hi chị, sau khi xem bài của chị, em có một số nhận xét như sau:

# Yêu cầu: Hoàn thành bài tập sử dụng kiến thức về HTML và CSS đã học

## 1. Tạo trang hiển thị một bài thơ

#### Điểm làm tốt:

- Bài thơ có 3 khổ, mỗi khổ được trình bày bằng thẻ `<p>`, các câu trong khổ được ngắt dòng bằng thẻ `<br>` đúng theo yêu cầu.
- Thẻ `<h1>` được dùng cho tên bài thơ.

#### Điểm cần cải thiện:

- Phần thông tin liên hệ đang dùng 3 thẻ `<p>` riêng biệt cho tác giả, email và số điện thoại. Nên gộp cả ba thành 1 thẻ `<p>` duy nhất, dùng `<br>` để xuống dòng giữa các mục:

```html
<p>
  Tác giả: Đức Trung<br />
  Email liên hệ: <a href="mailto:hoamai06@gmail.com">hoamai06@gmail.com</a
  ><br />
  Số điện thoại: <a href="tel:0965288335">0965288335</a>
</p>
```

- Giá trị `href` của thẻ `<a>` cho email đang là `" mailto: hoamai06@gmail.com"` (có khoảng trắng thừa trước và sau dấu `:`). Nên dùng `"mailto:hoamai06@gmail.com"` (không có khoảng trắng). Tương tự, `"tel: 0965288335"` nên sửa thành `"tel:0965288335"`.

## 2. Tạo một bài viết nói về bài thơ trên

#### Điểm làm tốt:

- Thẻ `<h1>` hiển thị tiêu đề giới thiệu.
- Bài viết có đủ 10 thẻ `<h2>` và các thẻ `<h3>` tương ứng, nội dung đủ dài để hiện thanh cuộn.
- Mục lục được xây dựng bằng `<ul>`, `<li>`, `<a>` với đầy đủ các mục `<h2>` và `<h3>`.
- Tất cả `<h2>` và `<h3>` đều có thuộc tính `id`, các liên kết trong mục lục trỏ đúng đến heading tương ứng.

#### Điểm cần cải thiện:

- Trong mục lục, các thẻ `<ul>` chứa mục `<h3>` đang nằm ngoài thẻ `<li>` cha, không đúng cấu trúc HTML. Nên đặt `<ul>` con vào bên trong `<li>` cha tương ứng:

```html
<!-- Hiện tại (chưa đúng cấu trúc) -->
<li>
  <a href="#the-tho-luc-bat-trong-bai-mien-que"
    >Thể thơ lục bát trong bài "Miền Quê"</a
  >
</li>
<ul>
  <li>
    <a href="#Dac-diem-cua-the-tho-luc-bat">Đặc điểm của thể thơ lục bát</a>
  </li>
  <li>
    <a href="#cach-su-dung-luc-bat-trong-bai-tho"
      >Cách sử dụng lục bát trong bài thơ</a
    >
  </li>
</ul>

<!-- Nên sửa thành -->
<li>
  <a href="#the-tho-luc-bat-trong-bai-mien-que"
    >Thể thơ lục bát trong bài "Miền Quê"</a
  >
  <ul>
    <li>
      <a href="#dac-diem-cua-the-tho-luc-bat">Đặc điểm của thể thơ lục bát</a>
    </li>
    <li>
      <a href="#cach-su-dung-luc-bat-trong-bai-tho"
        >Cách sử dụng lục bát trong bài thơ</a
      >
    </li>
  </ul>
</li>
```

- Bài làm chưa bổ sung thẻ `<a>` bên trong mỗi `<h2>` và `<h3>` để tự liên kết đến chính heading đó. Nên thêm thẻ `<a>` bao quanh nội dung chữ của từng heading, với `href` trỏ về `id` của heading cha:

```html
<h2 id="tong-quan-ve-bai-tho-mien-que">
  <a href="#tong-quan-ve-bai-tho-mien-que">Tổng quan về bài thơ Miền Quê</a>
</h2>

<h3 id="dac-diem-cua-the-tho-luc-bat">
  <a href="#dac-diem-cua-the-tho-luc-bat">Đặc điểm của thể thơ lục bát</a>
</h3>
```

- Một số `id` đang dùng chữ hoa (ví dụ: `Tong-quan-ve-bai-tho-Mien-Que`, `Dac-diem-cua-the-tho-luc-bat`, `Nhung-bieu-tuong-quen-thuoc-cua-lang-que`). Nên dùng toàn bộ chữ thường: `tong-quan-ve-bai-tho-mien-que`, `dac-diem-cua-the-tho-luc-bat`, `nhung-bieu-tuong-quen-thuoc-cua-lang-que`. Phần `href` trong mục lục cũng nên cập nhật theo để đồng nhất.

## 3. Highlight code trong nội dung bài viết

#### Điểm làm tốt:

- Bài viết có cấu trúc rõ ràng, chia theo từng bước với mô tả trước khối code và giải thích sau mỗi khối.
- Sử dụng `<pre><code>` để trình bày các đoạn code trong bài.

#### Điểm cần cải thiện:

- Bài làm chưa tích hợp PrismJS để highlight code. Nên thêm link CSS của PrismJS vào `<head>` và thêm script ở cuối `<body>`:

```html
<head>
  <!-- ... -->
  <link
    rel="stylesheet"
    href="https://cdnjs.cloudflare.com/ajax/libs/prism/1.29.0/themes/prism.min.css"
  />
</head>
<body>
  <!-- nội dung bài viết -->

  <script src="https://cdnjs.cloudflare.com/ajax/libs/prism/1.29.0/prism.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/prism/1.29.0/components/prism-css.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/prism/1.29.0/components/prism-javascript.min.js"></script>
</body>
```

Và mỗi thẻ `<code>` nên có class ngôn ngữ tương ứng để PrismJS highlight đúng:

```html
<pre><code class="language-html">...</code></pre>

<pre><code class="language-css">...</code></pre>

<pre><code class="language-javascript">...</code></pre>
```

- Khối code HTML trong Bước 1 đang chứa các thẻ HTML chưa được escape (ví dụ: `<html>`, `<head>`, `<body>`, `<div>`). Trình duyệt sẽ phân tích các thẻ này thay vì hiển thị chúng dưới dạng văn bản, khiến phần code không hiển thị đúng. Nên thay `<` bằng `&lt;` và `>` bằng `&gt;` cho toàn bộ nội dung HTML bên trong `<pre><code>`:

```html
<pre><code class="language-html">&lt;!DOCTYPE html&gt;
&lt;html lang="vi"&gt;
&lt;head&gt;
  &lt;meta charset="UTF-8"&gt;
  &lt;title&gt;Todo List&lt;/title&gt;
  &lt;link rel="stylesheet" href="style.css"&gt;
&lt;/head&gt;
&lt;body&gt;
  &lt;div class="container"&gt;
    &lt;h1&gt;Todo List&lt;/h1&gt;
    &lt;div class="todo-input"&gt;
      &lt;input type="text" id="taskInput" placeholder="Nhập công việc..."&gt;
      &lt;button onclick="addTask()"&gt;Thêm&lt;/button&gt;
    &lt;/div&gt;
    &lt;ul id="taskList"&gt;&lt;/ul&gt;
  &lt;/div&gt;
  &lt;script src="script.js"&gt;&lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;</code></pre>
```

## 4. Xây dựng trang giới thiệu bản thân

Chưa có phần bài làm.

**Đánh giá tổng quan:**

> Điểm: 7 / 10
>
> Nhận xét: Bài làm khá tốt. Chị đã áp dụng được các kiến thức về cấu trúc HTML cơ bản (thẻ tiêu đề, thẻ đoạn văn, thẻ liên kết, mục lục với `<ul>`, `<li>`, `<a>`, thẻ `<pre><code>` cho khối code) để hoàn thành một phần bài tập, thể hiện sự hiểu bài. Chị bổ sung lại bài theo nhận xét phía trên của em để bài làm hoàn thiện hơn nha.
>
> Em chúc chị học tốt!
