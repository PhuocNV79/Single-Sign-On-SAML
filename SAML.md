# Giải thích cách hoạt động của SAML
## 1. SAML là gì?
Nếu bạn đã từng nghe OAuth hay OAuth2 thì SAML chính là một chuẩn khác để giải quyết bài toán tương tự với OAuth và OAuth2.

Bài toán mà các chuẩn trên giải quyết tên là SSO (Single Sign On). 

SSO nảy sinh từ vấn đề khi các nhà cung cấp dịch vụ muốn người dùng có thể sử dụng các dịch vụ khác nhau mà chỉ cần đăng nhập vào một chỗ duy nhất, giúp cho việc sử dụng được thuận tiện cũng như giúp cho người dùng quản lý các username và password được giảm đi tối thiểu, thì đòi hỏi cần có một cách nào đó có thể xác thực và ủy quyền thông tin người dùng mà không cần bắt họ tạo tài khoản khác nhau trên những dịch vụ khác nhau.

Chú ý: SAML tự động giải quyết cả 2 vấn đề xác thực (authentication) và ủy quyền (authorization) nhưng OAuth và OAuth2 chỉ tự động giải quyết được vấn đề uỷ quyền (link tham khảo tại đây).

SAML (Security Assertion Markup Language) là một "chuẩn mở" cho phép nhà cung cấp thực thể (Identity Provider - IdP) xác thực người dùng và ủy quyền cho người dùng sử dụng một dịch vụ nào đó của nhà cung cấp dịch vụ (Service Provider - SP) mà không bắt buộc người dùng phải tạo tài khoản đăng nhập vào dịch vụ đó.

Định nghĩa như trên đọc sẽ khó hiểu, nhưng nếu bạn biết nút "Đăng nhập bằng Facebook" ở một số website thì mục đích của cái nút đó chính là mục đích của SAML.

 

##2. Cách hoạt động của SAML.
 



Ở hình trên thì: SP là nhà cung cấp dịch vụ (là ứng dụng có nút "Đăng nhập bằng Facebook" ấy), IdP là nhà cung cấp các thực thể (tài khoản người dùng - IdP ở đây là Facebook đấy).

Bước 1: User sẽ click vào nút "Đăng nhập bằng tài khoản của cái gì đó" từ browser, request này sẽ được gửi tới SP.
Bước 2: Phía SP sẽ tạo ra một SAML Request để gửi tới IdP, SAML Request này sẽ được chính SP ký điện tử (sign) bằng chữ ký của SP (chữ ký của SP ở đây chính là khóa bí mật của SP). 
Bước 3: Phía IdP khi nhận được SAML Request từ SP sẽ phải xác thực chữ ký có đúng là của SP hay không bằng cách dùng khóa công khai của SP để xác thực:
 Khóa công khai của SP này IdP lấy từ đâu?

Trước khi thực hiện giao dịch, SP và IdP phải bằng cách nào đó trao đổi được khóa công khai với nhau trước (không phải khóa bí  mật nhé). Thông thường, mỗi bên SP và IdP sẽ có một public url chứa metadata, metadata này chứa các thông tin công khai như là khóa công khai, ID thực thể và URL để điều hướng khi có request đến. Phía IdP sẽ biết được metadata url của SP để từ đó lấy ra khóa công khai của SP để xác thực chữ ký của SP. Trong trường hợp hệ thống của Idp đã có sẵn và không lấy public key của SP thông qua metadata url được thì SP phải gửi khóa công khai của mình cho Idp cài đặt trước khi thực hiện giao dịch.

Bước 4: Vẫn đang ở IdP, sau khi xác thực được chữ ký của SP rồi, IdP sẽ làm những thứ sau:
Lấy ra thông tin người dùng đang sử dụng browser (nếu người dùng đang đăng nhập vào IdP, còn nếu người dùng đang không đăng nhập thì bắt người dùng đăng nhập trước) để redirect (http post) về cho SP sử dụng (kết quả trả về này mình gọi là SAML Response). Trước khi gửi về cho SP thì IdP sẽ ký điện tử (sign) vào SAML Response bằng khóa bí mật của IdP.
Không những IdP ký vào SAML Response mà IdP cũng sẽ mã hóa các kết quả dữ liệu (SAML Assertions) có trong SAML Response bằng khóa công khai của SP.
Bước 5: Khi SP nhận được SAML Response, nó sẽ thực hiện những việc sau:
Dùng khóa công khai của IdP để xác thực xem có đúng là kết quả được gửi từ IdP hay không (đây chính là phần xác thực mà OAuth và OAuth2 không có). Khóa công khai của IdP cũng giống như nói ở trên, có thể lấy thông qua metadata url của IdP hoặc có thể được trao đổi trước.
Nếu xác thực đúng chữ ký, SP sẽ tiếp tục dùng khóa công khai của chính mình để giải mãi SAML Assertions đã được mã hóa từ phía IdP.
Lấy các thông tin dữ liệu người dùng trong SAML Assertions để đăng nhập người dùng vào hệ thống của chính mình, và trả về cho người dùng thông báo thành công (hay điều hướng người dùng tới các tài nguyên mong muốn).
