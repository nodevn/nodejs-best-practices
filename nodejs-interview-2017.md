Đây không phải là một cách tốt nhất để đánh giá một người bằng những câu hỏi dạng như thế này, nhưng **những điều này cho bạn có thể thấy được tổng quan kinh nghiệm Node.js của người đó**

# Các câu hỏi interview năm 2017

* error-first callback là gì ?
* Bạn có thể tránh callback-hell bằng cách nào ?
* Promise là gì ?
* Các công cụ nào được sử dụng để đảm bảo nhất quán phong cách lập trình ? Tại sao nó lại quan trọng ?
* Khi nào thì sử dụng `npm`, khi nào sử dụng `yarn` ?
* *TestPyramid* là gì ? Cho 1 ví dụ!
* Framework `http` nào bạn ưa thích sử dụng nhất và tại sao ?
* Làm thế nào bạn có thể chống lại tấn công **XSS**
* Làm thế nào bạn có thể chắc chắn các thư viện phụ thuộc của bạn là an toàn ?

Và sau đây là các đáp án tham khảo

# Tham số lỗi đầu tiên của hàm callback

Tham số lỗi đầu tiên của hàm callback được sử dụng để truyền thông tin lỗi và dữ liệu kèm theo. Như vậy, trước khi thực hiện các nghiệp vụ logic hàm gọi lại, nó phải được kiểm tra có lỗi hay không. Tham số tiếp theo được truyền là dữ liệu kết quả.

```js
fs.readFile(filePath, function(err, data) {  
  if (err) {
    // xử lý lỗi, dừng lại bằng cách gọi lệnh return
    return console.log(err)
  }
  // nếu không có lỗi thì sử dụng data, trả về cho hàm callback
  console.log(data)
})
```

# Bạn có thể tránh callback-hell bằng cách nào ?

Có rất nhiều cách để giải quyết vấn đề callback hells, như:

* **Mô-đun hóa**: chia nhỏ các hàm callback thành các function độc lập
* Sử dụng một thư viện **control flow**, như [async](https://www.npmjs.com/package/async)
* Sử dụng **generator** kết hợp với **Promise**
* Sử dụng **async/await** (chú ý, tính năng này mới có trong node v7)

# Promise là gì ?

**Promise** là một `cấu trúc điều khiển lập trình`, được thiết kế cho các xử lý bất đồng bộ. Promise được mô tả lần đầu vào những năm 80. Và bây giờ, nó trở thành một phần của hầu hết các ngôn ngữ lập trình hiện đại, làm cho cuộc sống của bạn trở nên dễ dàng hơn. 

Một ví dụ sẽ in ra giao diện console dòng thông điệp `Hello Promise!`. Chú ý, khối lệnh `catch` được sử dụng để xử lý lỗi/ngoại lệ, và khối lệnh `then` có thể được gọi liên tiếp nhau.

```js
new Promise((resolve, reject) => {  
  setTimeout(() => {
    resolve('result')
  }, 100)
})
  .then(console.log)
  .catch(console.error)
```

Một ví dụ khác về hiệu năng sử dụng Promise:

```js
function foo() {
  return new Promise(function(resolve){
    setTimeout(function(){
      resolve();
    }, 3000);
  });
}

function bar() {
  return new Promise(function(resolve){
    setTimeout(function(){
      resolve();
    }, 3000);
  });
}


var start = Date.now();

Promise.all([
  foo(),
  bar()
])
.then(function() {
  console.log('Promise.all', Date.now() - start);
});


start = Date.now();
foo()
.then(bar)
.then(function(){
  console.log('chain .then', Date.now() - start);
});
```

Tìm hiểu thêm Promise ở đây: [Promise – Async thì sao nào?](https://nodejs.vn/topic/15/promise-async-thì-sao-nào), [Promise là khỉ gì?](https://kipalog.com/posts/Promise-la-khi-gi-)

# Công cụ nào đảm bảo được phong cách nhất quán khi lập trình ? Tại sao nó lại quan trọng ?

Khi làm việc trong 1 team, phong cách lập trình nhất quán rất quan trọng, vì thế các thành viên đều có thể sửa đổi code dự án dễ dàng, mà không phải sử dụng một phong cách mới mỗi lần sửa mã.

Ngoài ra, nó có thể giúp loại bỏ các vấn đề lập trình sử dụng phân tích tĩnh.

Một số công cụ có thể hỗ trợ:

* [ESLint](http://eslint.org/)
* [Standard](http://standardjs.com/)

Nếu bạn muốn tự tin hơn, hãy vọc và nắm bắt các nguyên tắc [Làm sạch mã JavaScript](https://blog.risingstack.com/javascript-clean-coding-best-practices-node-js-at-scale/)



# Liên kết tham khảo

* [Node.js Interview Questions and Answers (2017 Edition)](https://blog.risingstack.com/node-js-interview-questions-and-answers-2017/)