# Use bind mounts
***
Tất nhiên, dưới đây là bảng so sánh giữa volume mounts và bind mounts:

| Ưu điểm                            | Volume có tên               | Bind mount                  |
|-----------------------------------|-----------------------------|-----------------------------|
| Vị trí trên máy chủ                 | Docker chọn                 | Bạn quyết định             |
| Ví dụ về việc mount (sử dụng --mount) | type=volume,src=my-volume,target=/usr/local/data | type=bind,src=/path/to/data,target=/usr/local/data |
| Lấp đầy volume mới bằng nội dung của container | Có                          | Không                       |
| Hỗ trợ Volume Drivers              | Có                          | Không                       |

Như vậy, bảng này sẽ giúp bạn dễ dàng so sánh những khác biệt quan trọng giữa volume mounts và bind mounts trong Docker.

