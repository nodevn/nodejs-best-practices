# nodejs-best-practices

Cách làm tốt nhất khi lập trình với Node.js, được tham khảo tại [RisingStack](https://blog.risingstack.com/node-js-best-practices/). 

# Quy ước sử dụng hàm callback

Tất cả các module chìa ra một hàm `callback` với tham số lỗi `err` ở vị trí đầu tiên, để báo hàm gọi lại có lỗi khi thực thi hay không?

Nó nên được viết như thế này:

```js
module.exports = function (heroName, callback) {  
  // khởi tạo một anh hùng
  var hero = createHero(heroName);

  // tham số đầu tiên của hàm gọi lại là giá trị lỗi
  // cụ thể ở đây truyền vào giá trị null, tức là không có lỗi!
  // nhưng nếu có lỗi xảy ra, hãy trả về một new Error hoặc một string để mô tả lỗi
  // tham số thứ hai là dữ liệu trả về (nếu có)
  return callback(null, hero);
}
```

# Luôn kiểm lỗi khi xử lý hàm callback

Việc làm này tránh gặp phải các lỗi có thể xảy ra trong kết quả trả về, hãy làm một hành động gì đó ?

```js
// Ví dụ một chương trình đọc file, nhưng không kiểm tra kết quả đọc file có bị lỗi hay không.
var fs = require('fs');

function readJSON(filePath, callback) {  
  fs.readFile(filePath, function(err, data) {
    // Hồn nhiên như cô tiên, trả về dữ liệu!  
    callback(JSON.parse(data));
  });
}

readJSON('./package.json', function (err, pkg) { ... });
```

Vấn đề ở đây, là hàm `readJSON` không quan tâm có lỗi xảy ra trong quá trình thực thi hay không. Mặc nhiên, nó trả về dữ liệu cho hàm `callback`.

Chương trình, nên được cải tiến `v0.1.1` lại như thế này:

```js
// Phiên bản cải tiến v0.1.1
function readJSON(filePath, callback) {  
  fs.readFile(filePath, function(err, data) {
    // kiểm tra nếu có lỗi ?
    if (err) {
      // trả lỗi bằng cách gọi hàm callback
      // hãy nhớ lại: err là tham số đầu tiên của hàm callback
      callback(err);
    }

    // nếu không lỗi, truyền null tham số đầu tiên và dữ liệu JSON.
    callback(null, JSON.parse(data));
  });
}
```

# Trả về chính hàm callback

Nếu bạn để ý sẽ thấy phiên bản v0.1.1 vẫn tồn tại một vấn đề, đó là nếu có lỗi xảy ra, chương trình vẫn tiếp tục thực thi lệnh sau khi gọi hàm `callback(err)`. Điều này dẫn tới những kết quả bất ngờ, không trông chờ mà đến. Vậy, hãy luôn trả về hàm callback để dừng xử lý.


```js
// Phiên bản cải tiến v0.1.2
function readJSON(filePath, callback) {  
  fs.readFile(filePath, function(err, data) {
    // kiểm tra nếu có lỗi ?
    if (err) {
      // trả lỗi bằng cách gọi hàm callback
      // hãy nhớ lại: err là tham số đầu tiên của hàm callback
      return callback(err);
    }

    // nếu không lỗi, truyền null tham số đầu tiên và dữ liệu JSON.
    return callback(null, JSON.parse(data));
  });
}
```

# Chỉ sử dụng try-catch trong các đoạn mã đồng bộ

Chương trình ở trên có còn lỗi tiềm tàng nào hay không ? Ồ có đấy! Chúng ta cần phải quan tâm đến hàm `JSON.parse(string)`. Bởi vì hàm này có thể ném ra lỗi nếu chuỗi `string` truyền vào không đúng định dạng của một chuỗi `JSON` hợp lệ.

Như vậy chúng ta cần một, bộ giám sát và bắt lỗi `try-catch`. Bạn chú ý là chỉ sử dụng try-catch ở các đoạn mã đồng bộ nhé! Rất may hàm JSON.parse xử lý đồng bộ, và bộ tóm lỗi try-catch của chúng ta sẽ hoạt động hiệu quả trong trường hợp này.

```js
// Phiên bản cải tiến xịn nhất v0.1.3
function readJSON(filePath, callback) {  
  fs.readFile(filePath, function(err, data) {
    var parsedJson;

    // Có lỗi đọc file không ?
    if (err) {
       return callback(err);
    }

    // Chuyển đổi data về kiểu dữ liệu JSON.
    try {
      parsedJson = JSON.parse(data);
    } catch (exception) {
      return callback(exception);
    }

    // Đến đây thì mọi thứ ok rồi !
    return callback(null, parsedJson);
  });
}
``` 

# Tạo ra các mô-đun nhỏ

Hãy làm theo phong-cách-unix:

> Các nhà phát triển nên xây dựng một chương trình bằng cách ghép nỗi các bộ phẩn đơn giản theo các giao diện interfaces có thiết kế tốt, như vậy các vấn đề là cục bộ, và tất cả các thành phần của chương trình có thể được thay thế bở một phiên bản tương lai tốt hơn, hỗ trợ các tính năng mới!

Hãy làm cho nó đơn giản: một mô-đun chỉ nên làm một việc và làm tốt việc đó!

# Sử dụng một thư viện xử lý không đồng bộ tốt nhất

Hãy chọn [async](https://github.com/caolan/async).

# Xử lý lỗi

Các lỗi xảy ra có thể được chia thành 2 loại chính: Lỗi hệ thống và lỗi lập trình

## Lỗi hệ thống

Lỗi hệ thống có thể xảy ra trong các ứng dụng đang hoạt động trơn chu, do nó không phải `bug`, nhưng vấn đề gây ra bởi hệ thống / lời gọi dịch vụ từ xa, ví dụ như:

* Request timeout: Hết thời gian yêu cầu
* Connect timeout: Lỗi không thể kết nối tới dịch vụ từ xa
* Out of memory: Hệ thống hết bộ nhớ

### Xử lý lỗi hệ thống

Phụ thuộc vào loại lỗi hệ thống cụ thể, mà bạn có thể làm các công việc sau để khắc phục:

* Cố gắng sửa lỗi - Nếu 1 file không tồn tại, bạn cần tạo nó trước
* Thử lại công việc - trường hợp không kết nối được mạng, hãy thử kết nối lại lần thứ 2 hoặc thêm lần thứ 3.
* Cố gắng thông báo cho khách hàng biết có điều gì đó không ổn, và khách hàng có thể điều chỉnh lại các dữ liệu nhập vào cho đúng.
* Tự thoát tiến trình, khi điều kiện chạy ứng dụng không ổn định, ví dụ việc không thể đọc được file cấu hình, thông tin kết nối.

Vì vậy, cố gắng ghi log mọi thứ ra `màn hình`, `file`!

## Lỗi lập trình

Rõ ràng lỗi lập trình là `bug`. Đây là điều mà bạn cần tránh gặp phải, ví dụ như:

* Gọi một hàm không đồng bộ không xử lý hàm `callback`. Cái này nhiều newbie dính nha @@
* Cannot read property of `undefined`. Lỗi muôn thuở của các ứng dụng javascript.

### Xử lý lỗi lập trình

Với các `bug` lỗi lập trình, bạn sẽ không biết được trạng thái ứng dụng của bạn là như thế nào, và nó có thể tự thoát `crash` ngay lập tức khi xảy ra. Và bạn cần có một chương trình giám sát và tự khởi động lại, như: [pm2](http://pm2.keymetrics.io/), [forever](https://github.com/foreverjs/forever), [supervisord](http://supervisord.org/) hoặc [monit](http://mmonit.com/monit/).

# Quy trình làm việc Workflow

## Khởi tạo một dự án mới với `npm init`

Lệnh `init` giúp bạn tạo ra một file `package.json` của ứng dụng. Nó khởi tạo mặc định và có thể sửa đổi sau này.

Bạn có thể tạo một ứng dụng mới bằng các lệnh đơn giản như thế này:

```bash
mkdir my-awesome-new-project
cd my-awesome-new-project
npm init
```

## Kịch bản khởi động và kịch bản kiểm thử

Trong file `package.json` bạn có thể cài đặt các kịch bản trong phần `scripts`. Mặc định, `npm` sẽ tạo ra 2 kịch bản `start` và `test`. Bạn có thể sửa đổi nội dung của nó hoặc thêm vào các kịch bản khác. Sau đó bạn có thể chạy với lệnh

* Khởi động ứng dụng: `npm start`
* Kiểm thử ứng dụng: `npm test`
* Chạy một kịch bản khác: `npm run dev`, `npm run configw`, ...

Bạn thấy ngay được là `npm start` và `npm test` là 2 lối tắt của lệnh đầy đủ `npm run start` và `npm run test`.

Chú ý: npm sẽ thiết lập biến `$PATH` để tìm chương trình thực thi bên trong thư mục `node_modules/.bin` cùng cấp với file `package.json`. Điều này giúp tránh xung đột với các module toàn cục khác của NPM.

## Biến môi trường

## ĐỪNG CỐ PHÁT MINH LẠI CÁI BÁNH XE

Luôn luôn tìm kiếm các giải pháp có sẵn trước. [NPM](https://npmjs.org) có rất rất nhiều packages được cung cấp miễn phí và bạn có thể dễ dàng tìm thấy nó.

## Sử dụng quy ước lập trình

Sẽ trở nên dễ dàng hơn để hiểu một hệ thống lớn, khi tất cả các đoạn mã được viết theo một phong cách nhất quán. Nó có thể bao gồm quy tắc thụt đầu dòng, quy ước đặt tên biến, cách làm tốt nhất, và nhiều quy định thống nhất khác nữa.

Tham khảo một ví dụ thực tế của [RisingStack](http://risingstack.com/) [Node.js style guide](https://github.com/RisingStack/node-style-guide).



