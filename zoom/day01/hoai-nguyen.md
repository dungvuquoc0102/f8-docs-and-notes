Hi chị, sau khi xem bài của chị, em có một số nhận xét như sau:

- Vì lần đầu làm, nên em sẽ review chi tiết một chút để chị có thể hiểu rõ hơn về những điểm cần cải thiện. Những lần sau, chị sẽ quen hơn thì sẽ không mắc phải những lỗi này nữa đâu nha.

- Phần font bị lỗi do có ký tự ’ (khác dấu nháy đơn) thuộc font Charter, chị có thể thay thế toàn bộ ký tự ’ bằng ký tự ' (dấu nháy đơn) trong dự án để tránh lỗi font nha.

# Yêu cầu: Hoàn thành các bài tập sử dụng kiến thức đã học về HTML

## 1. Xây dựng trang báo Medium với các thẻ HTML được sử dụng đúng semantic

#### Điểm làm tốt:

- Thẻ `<html>` có thuộc tính `lang="en"`.
- Thẻ `<h1>` có nội dung "How 60 Minutes a Day Can Change Your Life" khớp với tiêu đề bài báo.
- Chỉ có một thẻ `<h1>` trong trang.
- Các thẻ `<h2>` được sử dụng đúng cho các tiêu đề phần.
- Thẻ `<mark>`, `<i>` được dùng ở nhiều vị trí trong bài.
- Danh sách `<ol>` và `<ul>` được sử dụng đúng chỗ.

#### Điểm cần cải thiện:

- Trang thiếu khai báo `<!DOCTYPE html>`. Nên thêm `<!DOCTYPE html>` ở dòng đầu tiên của file để trình duyệt nhận diện đúng chuẩn HTML5.

```html
<!DOCTYPE html>
<html lang="en">
  ...
</html>
```

- Cấu trúc `<head>` và `<body>` chưa đúng: nội dung bài báo (`<h5>`, `<h1>`, `<img>`, `<blockquote>`, v.v.) đang nằm bên trong thẻ `<head>`, và thẻ `<body>` được mở bên trong `<head>`. Nên đặt toàn bộ nội dung hiển thị vào bên trong `<body>`, và chỉ đặt các thẻ `<meta>`, `<title>`, `<link>` trong `<head>`.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>How 60 Minutes a Day Can Change Your Life</title>
  </head>
  <body>
    <!-- Toàn bộ nội dung bài báo đặt ở đây -->
  </body>
</html>
```

- Trang thiếu thẻ `<title>`. Nên thêm `<title>How 60 Minutes a Day Can Change Your Life</title>` vào trong `<head>` để đồng nhất với nội dung của `<h1>`.

- Trang thiếu `<meta charset="UTF-8">` và `<meta name="viewport" content="width=device-width, initial-scale=1.0">`. Nên thêm vào trong `<head>`:

```html
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
```

- Thẻ `<h5>` đang được dùng cho tên tờ báo "The Velvet Guillotine" ở đầu trang, trong khi bên dưới mới có `<h1>`. Điều này phá vỡ thứ tự cấp độ tiêu đề (không dùng `<h5>` trước `<h1>`). Nên dùng thẻ `<p>` thay cho `<h5>` ở vị trí này, vì đây là tên ấn phẩm chứ không phải tiêu đề cấp độ 5.

```html
<p>The Velvet Guillotine</p>
<h1>How 60 Minutes a Day Can Change Your Life</h1>
```

- Thuộc tính `alt` của thẻ `<img>` đang để trống (`alt=""`). Nên đặt giá trị `alt="How 60 Minutes a Day Can Change Your Life"`.

```html
<img
  width="100%"
  src="https://images.pexels.com/photos/3046629/pexels-photo-3046629.jpeg"
  alt="How 60 Minutes a Day Can Change Your Life"
/>
```

- Hình ảnh và phần chú thích ảnh đang được bố cục bằng `<blockquote>`. `<blockquote>` dùng để trích dẫn văn bản, không phải để bọc ảnh. Nên dùng `<figure>` và `<figcaption>` thay thế.

```html
<figure>
  <img
    width="100%"
    src="https://images.pexels.com/photos/3046629/pexels-photo-3046629.jpeg"
    alt="How 60 Minutes a Day Can Change Your Life"
  />
  <figcaption>
    Photo by
    <a
      href="https://www.pexels.com/photo/plants-macro-growth-soil-113335/"
      target="_blank"
      >Markus Spiske</a
    >/Pexels
  </figcaption>
</figure>
```

- Thẻ `<a>` liên kết đến Pexels (liên kết ngoài) thiếu thuộc tính `target="_blank"`. Thẻ `<a>` ở cuối bài có `target="_self"` thay vì `target="_blank"`. Nên thêm hoặc sửa `target="_blank"` cho tất cả các liên kết ngoài.

```html
<a
  href="https://www.pexels.com/photo/plants-macro-growth-soil-113335/"
  target="_blank"
  >Markus Spiske</a
>

<a href="https://www.amazon.com/dp/B0F1DC76YK" target="_blank"
  ><strong>Check it out here</strong></a
>
```

- Thẻ `<ul>` đang được đặt bên trong thẻ `<p>` ở mục "The Simple Math of Transformation". `<ul>` là thẻ block, không thể lồng bên trong `<p>`. Nên đóng thẻ `<p>` trước `<ul>`.

```html
<p>
  <i>One hour</i> per day may seem small, but over time, it compounds into
  something significant:
</p>
<ul>
  <li>
    <strong>7 hours a week</strong> — that's nearly a full workday of learning
    something new.
  </li>
  <li>
    <strong>30 hours a month</strong> — that's the equivalent of completing an
    entire module in a university class.
  </li>
  <li>
    <strong>365 hours in a year</strong> — over 15 full days spent purely on
    growth.
  </li>
</ul>
```

- Đoạn văn đầu tiên `<b>One Hour. 60 Minutes. 3,600 Seconds.</b> We all have the same...` chưa được bọc trong thẻ `<p>`. Nên đặt toàn bộ đoạn này vào `<p>`.

```html
<p>
  <strong>One Hour. 60 Minutes. 3,600 Seconds.</strong> We all have the same 24
  hours in a day, but somehow, the idea of carving out just
  <strong>one hour</strong> to learn something new feels monumental for most
  people.
</p>
```

- Các mục trong danh sách `<ol>` ở phần "Why Don't More People Do This?" chưa áp dụng `<strong>` cho phần tên mục (ví dụ: "People Underestimate Small Steps:", "The Comfort of the Familiar:"). Nên bọc phần tên mục bằng `<strong>`.

```html
<ol>
  <li>
    <strong>People Underestimate Small Steps:</strong> most of us are obsessed
    with instant results...
  </li>
  <li>
    <strong>The Comfort of the Familiar:</strong>
    <mark>spending an hour learning something new feels uncomfortable...</mark>
  </li>
  <li>
    <strong>We're Afraid of Failing:</strong> starting something new means not
    being good at it...
  </li>
  <li>
    <strong>It Feels Like We Don't Have Time:</strong> this is the most
    <strong>common</strong> excuse...
  </li>
</ol>
```

- Phần "How I Taught Myself to Code" thiếu thẻ `<i>` cho các từ kỹ thuật `loops, arrays` và `syntax errors`. Nên bổ sung.

```html
Terms like <i>loops, arrays</i>, and <i>syntax errors</i> swam around in my
head...
```

- Phần "It Feels Like We Don't Have Time" thiếu `<em>` cho từ `do` trong câu "you do have time". Nên sửa thành `you <em>do</chị> have time`.

- Phần "Imagine Your Life After a Year of Learning" thiếu `<em>` cho từ `way` trong câu "look way better". Nên sửa thành `look <em>way</chị> better`.

- Bài làm chưa có đoạn mã HTML nhúng trong bài báo. Nên thêm một khối `<pre><code>` ở vị trí phù hợp (ví dụ cuối bài).

```html
<pre>
<code>
  &lt;!DOCTYPE html&gt;
  &lt;html lang="en"&gt;
    &lt;head&gt;
      &lt;meta charset="UTF-8" /&gt;
      &lt;meta name="viewport" content="width=device-width, initial-scale=1.0" /&gt;
      &lt;title&gt;Document&lt;/title&gt;
    &lt;/head&gt;
    &lt;body&gt;&lt;/body&gt;
  &lt;/html&gt;
</code>
</pre>
```

- Phần "Edited 14/3/2025" bị lỗi đánh máy: `<b>dited 14/3/2025:</b>` đang thiếu chữ "E". Nên sửa thành `<strong>Edited 14/3/2025:</strong>`.

**Đánh giá tổng quan:**

> Điểm: 7 / 10
>
> Nhận xét: Bài làm khá tốt. Chị đã áp dụng được một số kiến thức về các thẻ HTML semantic, cấu trúc nội dung và các thẻ định dạng nội tuyến để hoàn thành bài tập, thể hiện sự hiểu bài. Chị bổ sung lại bài theo nhận xét phía trên của em để bài làm hoàn thiện hơn nha.
>
> Em chúc chị học tốt!
