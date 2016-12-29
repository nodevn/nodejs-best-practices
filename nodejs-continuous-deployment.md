# Triển khai liên tục ứng dụng Node.js

**Continuous Integration** là một tập quán trong phát triển phần mềm mà ở đó yêu cầu lập trình viên tích hợp source code trên máy local với server chứa source code được chia sẻ với các lập trình viên khác. Việc tích hợp này cần phải diễn ra thường xuyên và càng sớm càng tốt (ASAP). Điều này giúp hạn chế sự khác biệt trong source code trên máy local của các thành viên khác nhau tham gia vào dự án. Đồng thời, nếu như dự án của bạn có sử dụng các công cụ test tự động (automated testing) việc làm này sẽ giúp source code của lập trình viên trở lên đáng tin cậy hơn.

**Continuous Deployment** hay CD là khái niệm có quan hệ rất mật thiết với Continuous Integration. Continuous Deployment giúp việc deploy ứng dụng có thể được diễn ra trong bất cứ thời điểm nào và sau đó thậm chí là tự động release một phiên bản mới trên môi trường test hoặc production nếu như bản build (gồm các mã lệnh được bạn gửi lên server) của bạn vượt qua được tất cả các test case.

Với cách làm như vậy, nếu xảy ra lỗi trong quá trình build (mã lệnh bạn commit không vượt qua các kiểm định về chất lượng hoặc các test case) lập trình viên sẽ có thể dễ dàng xác định được lỗi diễn ra ở đâu và qua đó giúp fix lỗi một cách hiệu quả hơn.

**Continuous Delivery** là tập quán của việc đảm bảo mã lệnh của từng lập trình viên tham gia vào dự án có thể deploy ở bất cứ thời điểm nào. Tuy nhiên điều này không có nghĩa là nó có thể đảm bảo ứng dụng có thể được deploy một cách an toàn trên môi trường production vì thông thường sẽ có sự khác biệt trong cách cấu hình ứng dụng giữa môi trường production và test.



# Liên kết tham khảo

1. [DevOps](http://gurunh.com/2016/03/devops/), [TechMaster](https://techmaster.vn/posts/33895/devops)
2. [Continuous Integration Là Gì?](http://www.codehub.vn/Continuous-Integration-La-Gi)
3. [Continuous Deployment of Node.js Applications](https://blog.risingstack.com/continuous-deployment-of-node-js-applications/)