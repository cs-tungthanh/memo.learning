**Sharding**: split data in multiple instances
	- solve issue about data storage when 1 instance cannot hold all data
**Partition**: split data in multiple table in the same instance
	- solve issue about query

Partitioning vs Sharding: một nhầm lẫn rất nhiều team từng trả giá bằng… downtime
Mình gặp không ít team, khi hệ thống bắt đầu chậm, câu hỏi đầu tiên bật ra là:
“Hay mình shard database đi?”
Chúng giải quyết hai vấn đề khác nhau.
* Partitioning là khi bạn dọn lại nhà
- Bạn vẫn sống trong một căn nhà.
Chỉ là bạn:
- Chia phòng theo chức năng
- Đồ năm 2024 để một chỗ, 2025 để chỗ khác
- Khi cần lấy đồ 2025 thì không phải lục tung cả nhà
Database cũng vậy:
- Query nhanh hơn
- Dễ dọn dẹp
- Ít rủi ro
- Gần như không đổi kiến trúc
Partitioning giúp hệ thống gọn gàng và chạy mượt hơn,
nhưng ngôi nhà vẫn chỉ có một tầng.
* Sharding là khi bạn mua thêm nhà
Không còn là dọn đồ nữa.
Bạn chia người ra ở nhiều căn nhà khác nhau.
- User nhóm A ở nhà A
- User nhóm B ở nhà B
Hỏng một nhà, các nhà khác vẫn sống
Nghe rất đã.
Nhưng đổi lại:
- Muốn gặp nhau thì phải đi đường xa
- Join dữ liệu không còn đơn giản
- Backup, migration, schema change… đều đau đầu hơn trước rất nhiều
Sharding là quyết định kiến trúc, không phải tuning.
* Sai lầm phổ biến nhất mình thấy
- DB chậm → shard
- Query lâu → shard
- Data nhiều → shard
Trong khi:
- Index chưa tối ưu
- Partition chưa làm
- Query chưa hiểu rõ access pattern
- Load thật sự chưa chạm trần máy
Shard sớm quá thường dẫn đến: “Hệ thống phức tạp lên gấp 5, nhưng performance chỉ tăng 20%”
* Thực tế ngoài đời (không màu hồng)
- Hệ thống tài chính
+ Transaction partition theo thời gian
+ Customer shard khi scale lớn
- E-commerce
+ Order partition để report
+ User shard để chịu tải
- Social / Messaging
+ User shard
+ Data trong shard lại tiếp tục partition
Hai khái niệm này thường đi cùng nhau, nhưng không bao giờ thay thế nhau.
Một câu để nhớ lâu: 
Partitioning giúp hệ thống chạy tốt hơn.
Sharding giúp hệ thống chạy to hơn.
Và câu hỏi đúng không phải là: “Dùng cái nào?”
Mà là: “Chúng ta đang bị giới hạn bởi query, hay bởi kiến trúc?”

  


