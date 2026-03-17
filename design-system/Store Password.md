## Summary
- using hash + salt to store password
- use hash because it is one way direction, we cannot revert back to original value -> cannot using encrypt/decrypt in this case
- Rainbow algo -> hack to try all possible values that can hash to the value in db -> prevent = add salt to the password
	- so event 2 passwords with same value -> store 2 diff hash value in db


Interview và câu chuyện làm sao để lưu trữ password trong database hợp lý
1.Tình huống
Trong tình huống này, mình xin phép note inteverviwer là ông A và bạn phỏng vấn là B nha mn.
B: Vâng. Em đã áp dụng để thiết kế module user trong hệ thống, lưu trữ password vào trong database và encrypt trước khi lưu trữ.
A: Bạn vừa mới dự án sử dụng encrypt mà không phải hasing để lưu trữ ?
B: Dạ
A: Vậy bạn lưu trữ key ở đâu ?
B: Key nào ạ?
A: Key mà bạn dùng để encrypt password ấy, và nhờ bạn nói rõ asymmetric hay symmetric encryption trong trường hợp này, và key đó là one per user hay là shared key
  

Symmetric là thuật toán sử dụng 1 key cho việc encryption password plaintext ra ciphertext và ngược lại decryption ciphertext thành plaintext.
Asymmetric là thuật toán sử dụng 2 cặp key: public và private key. Public key dùng để encrypt data, public key dùng để encrypt data và private key dùng để decrypt nó. Public key có thể công khai nhưng private key bắt buộc phải bảo mật vì người khác có thể dùng để giải mã mật khảu của bạn

B: Em không dùng key như trên, vậy cho em xin nói lại hasing
Hasing là thuật toán mã hóa one-way (1 chiều) và bạn không thể lấy lại mật khẩu mà bạn đã hash
Encryption thì ngược lại bạn có thể decrypt password của mình
A: Ok không vấn đề. Vậy bạn có thể giải thích tại sao bạn hash password mà không lưu thẳng nó vào db ?
B: Dạ để đảm bảo tính bảo mật khi hacker trà trộn và lấy thông tin người dùng, họ không thể biết được mật khẩu là gì
A: Nhưng mà hacker vẫn có thể sử dụng rainbow table để lấy dữ liệu thông qua hash collision mà nhỉ, bạn có biết về salt ?
B: Dạ em có sử dụng để append thêm chuỗi random hash vào ạ.
A: Vậy thì nó hoạt động ra sao ?
B: Ví dụ trường hợp hacker lấy được password trong db và sau đó anh ta sử dụng Rainbow table để tìm password và sui rủi thì hash collision xảy ra và anh ta nhận được mật khẩu gốc được trộn với một chuỗi ngẫu nhiên, vì vậy theo một cách nào đó, chúng ta có thể ngăn chặn việc mật khẩu gốc bị lộ.

Rainbow table là một precomputed table dùng để reverse các chuỗi mã hóa (cryptographic) về ngược lại giá trị ban đầu (plaintext). Các chuỗi trong bảng được tạo bằng cách lặp đi lặp lại việc áp dụng hashing cho các mật khẩu gốc, sau đó sử dụng reduction function và tiếp tục hash. Lặp lại đến khi chúng ta có cặp (password, hash) - hash ở đây là chuỗi cuối cùng vòng lặp.

"Salt đảm bảo rằng ngay cả khi hai người dùng có cùng mật khẩu (ví dụ: '123456'), thì chuỗi hash lưu trong DB của họ vẫn sẽ hoàn toàn khác nhau. Điều này khiến Rainbow Table trở nên vô dụng.
A: Vậy thì bạn sử dụng thuật toán hashing nào?
B: Theo tôi được biết là md5 không an toàn, vì vậy tôi sử dụng bcrypt, SHA-256, sha3, ...
Md5 đã được proven là weak theo báo cáo vào 2004: https://eprint.iacr.org/2004/199.pdf, trường hơp tương tự cũng được á dụng với sha1 vào 2005. Vì vậy hãy thực sự kĩ càng trong việc áp dụng hash function nhé

2. Cùng nhau bóc tách vấn đề chút nào 🧐
Cuộc phỏng vấn trên đã chạm đến những khái niệm cốt lõi của việc lưu password:
2.1. Lỗi sai "chết người": Encrypt vs. Hash
Đây là lỗi mà bạn B mắc phải.
• Encryption (Mã hóa 2 chiều): Là thuật toán có thể đảo ngược (decrypt). Bạn dùng 1 cái key (chìa khóa) để mã hóa (encrypt) và cũng dùng key đó (hoặc 1 key khác) để giải mã (decrypt) về dữ liệu gốc.
	◦ Symmetric (Đối xứng): Dùng 1 key cho cả 2 chiều.
	◦ Asymmetric (Bất đối xứng): Dùng 2 key (Public/Private).
• Hashing (Băm 1 chiều): Là thuật toán KHÔNG thể đảo ngược. Bạn không bao giờ có thể "decrypt" một chuỗi hash để lấy lại mật khẩu gốc.

➡️ TUYỆT ĐỐI KHÔNG DÙNG ENCRYPT để lưu password. Nếu hacker lấy được DB và cả cái "key" (mà A hỏi), chúng sẽ giải mã được toàn bộ mật khẩu. Chúng ta phải dùng HASHING.
2.2. Tại sao Hash không (chưa) đủ? - Rainbow Table Attack
• Rainbow Table: Là một bảng "từ điển" khổng lồ được hacker tạo sẵn, chứa hàng tỷ cặp (plaintext, hash). Ví dụ: (123456, e10adc3949ba59abbe56e057f20f883e - nếu dùng MD5).
