# Bảng giải thích các giao thức trong Streaming Data

| Giao thức   | Lớp OSI     | Chức năng chính                            | Mô tả ngắn gọn                                                                 | Ví dụ thực tế dễ hiểu                                          | Ngữ cảnh sử dụng phổ biến                          | Giao thức liên quan            |
|-------------|--------------|---------------------------------------------|--------------------------------------------------------------------------------|------------------------------------------------------------------|----------------------------------------------------|-------------------------------|
| **RTP**     | Transport    | Truyền dữ liệu media thời gian thực        | Gửi audio/video với thông tin định thời. Không đảm bảo delivery hoàn toàn.     | Gọi video qua Messenger: hình/tiếng truyền song song.           | VoIP, video conference (Zoom, Meet, WebRTC)        | RTCP, SDP, SIP                 |
| **RTCP**    | Transport    | Quản lý và giám sát truyền RTP             | Gửi thông tin phản hồi (delay, jitter, lost) giúp điều chỉnh RTP.              | Mạng lag khi họp online, RTCP báo cho sender để điều chỉnh.     | Cùng RTP trong media streaming                      | RTP                           |
| **RTSP**    | Application  | Điều khiển stream (play, pause,...)        | Điều khiển truyền media qua TCP, thường kết hợp RTP cho data.                  | Xem lại camera: Play, Pause, Seek giống như điều khiển từ xa.   | IP camera, media server (VLC, Wowza)               | RTP, SDP                      |
| **RTMP**    | Application  | Truyền media độ trễ thấp                   | Giao thức truyền cũ của Adobe, độ trễ thấp nhưng không bảo mật cao.            | Stream game lên Facebook Live/OBS.                              | Live stream (YouTube, Twitch, Facebook trước đây)  | HLS (chuyển dần từ RTMP)      |
| **HLS**     | Application  | Adaptive Streaming                         | Chia video thành đoạn nhỏ, client chọn chất lượng phù hợp.                     | Xem video YouTube: đang xem 480p → WiFi mạnh chuyển lên 1080p.  | Video on demand, live streaming (Apple ecosystem)  | MPEG-DASH                     |
| **WebRTC**  | Application  | Truyền media peer-to-peer thời gian thực   | Kết nối trực tiếp giữa browser, hỗ trợ NAT traversal, mã hóa mặc định.         | Chat video trên Google Meet/PiP camera không dùng plugin.        | Trò chuyện video P2P, video call embedded web       | RTP, STUN, TURN, SDP          |
| **MPEG-DASH**| Application | Adaptive Streaming                         | Chuẩn quốc tế tương tự HLS, mở và linh hoạt hơn.                              | Netflix tự động giảm chất lượng video khi mạng yếu.             | Video streaming (Netflix, YouTube)                 | HLS                            |
| **SDP**     | Presentation | Mô tả session truyền media                 | Dạng text định nghĩa loại codec, IP, port để thương lượng giữa hai bên.        | Gọi video: client gửi “tôi hỗ trợ VP8, port 5004”, server chọn. | Trong SIP, WebRTC, RTSP để mô tả session           | SIP, RTP, RTSP                |
| **SRT**     | Transport    | Truyền media bảo mật, đáng tin cậy         | UDP-based, nhưng có cơ chế phát hiện và sửa lỗi, bảo mật end-to-end.           | Truyền live stream giữa hai studio broadcast qua Internet.      | Live stream độ trễ thấp, studio truyền hình        | FFmpeg                         |
| **RSVP**    | Network      | Đặt trước tài nguyên mạng (QoS)           | Cho phép đặt trước băng thông để truyền media ổn định hơn.                     | Họp online ưu tiên băng thông, không bị lag dù nhà đông người. | Mạng yêu cầu QoS, VoIP trong doanh nghiệp lớn      | RTP, SIP                      |
| **SIP**     | Application  | Khởi tạo/Quản lý phiên VoIP/video          | Gửi lời mời, thiết lập kết nối media, kết thúc phiên.                         | Gọi điện qua Zalo/Skype: client gửi lời mời kết nối.            | VoIP, gọi video, tổng đài IP (FreePBX, Asterisk)   | RTP, SDP                      |
| **IAX**     | Application  | Truyền thoại IP cho Asterisk               | Giao thức tối ưu cho Asterisk, truyền qua 1 cổng (UDP/4569).                  | Tổng đài doanh nghiệp dùng FreePBX/Asterisk để gọi nội bộ.      | Tổng đài IP trong doanh nghiệp                     | SIP (cạnh tranh với IAX)      |

---

### ✅ Gợi ý trình bày hoặc demo theo nhóm giao thức:

- **VoIP (gọi điện/voice):** `SIP + SDP + RTP + RTCP`
- **Live Streaming (một chiều):** `RTMP input → HLS/MPEG-DASH output`
- **Gọi video peer-to-peer:** `WebRTC + SDP + RTP`
- **Mạng doanh nghiệp cần QoS:** `RSVP + RTP + SIP`
- **Truyền media qua Internet bất ổn:** `SRT`

---


