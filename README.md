# Kernel Linux Tùy Chỉnh cho Azure (az-kernel-full)

Kho chứa này cung cấp các gói cài đặt (`.deb`) và công cụ cho phiên bản Kernel Linux `6.8.12` được biên dịch tùy chỉnh cho môi trường Azure.

**Mục tiêu chính của phiên bản này:**
*   Kích hoạt đầy đủ các tính năng theo dõi và gỡ lỗi nâng cao của eBPF.
*   Bật `PSI tracepoints` để theo dõi áp lực tài nguyên.
*   Bật `MSR probes` để giám sát thanh ghi CPU.
*   Cung cấp một môi trường kernel mạnh mẽ cho các tác vụ phân tích hiệu năng và observability.

---

## Cách Cài Đặt

Có hai phương pháp để cài đặt kernel này. Phương pháp sử dụng kho APT được khuyến khích vì tính tiện lợi và khả năng cập nhật sau này.

### **Phương Pháp 1: Sử Dụng Kho Chứa APT (Khuyến khích)**

Phương pháp này cho phép bạn cài đặt và quản lý kernel thông qua các lệnh `apt-get` tiêu chuẩn của Ubuntu.

**Áp dụng cho:** Ubuntu 22.04 (Jammy Jellyfish)

Chạy các lệnh sau trên máy chủ đích:

```bash
# 1. Tải và thêm khóa GPG của kho chứa để xác thực gói tin
wget -qO - https://wollfoo.github.io/az-kernel-full/wollfoo.gpg | sudo gpg --dearmor -o /usr/share/keyrings/wollfoo-archive-keyring.gpg

# 2. Thêm kho APT của chúng tôi vào danh sách nguồn của bạn
echo "deb [signed-by=/usr/share/keyrings/wollfoo-archive-keyring.gpg] https://wollfoo.github.io/az-kernel-full jammy main" | sudo tee /etc/apt/sources.list.d/wollfoo.list > /dev/null

# 3. Cập nhật danh sách gói và cài đặt gói kernel-image
sudo apt-get update
sudo apt-get install linux-image-6.8.12-azure-full+

# 4. (Tùy chọn) Cài đặt gói headers nếu bạn cần biên dịch modules bên ngoài
sudo apt-get install linux-headers-6.8.12-azure-full+
```

### **Phương Pháp 2: Tải Trực Tiếp Từ GitHub Releases**

Phương pháp này phù hợp nếu bạn muốn cài đặt thủ công hoặc không muốn thêm kho APT vào hệ thống.

1.  **Tải các gói `.deb`:**
    Truy cập trang [**Releases**](https://github.com/wollfoo/az-kernel-full/releases/tag/v6.8.12-full) và tải về các tệp sau:
    *   `linux-image-6.8.12-azure-full+..._amd64.deb`
    *   `linux-headers-6.8.12-azure-full+..._amd64.deb` (Tùy chọn)

2.  **Cài đặt các gói đã tải về:**
    Mở terminal tại thư mục chứa các tệp đã tải và chạy lệnh:
    ```bash
    sudo dpkg -i linux-image-6.8.12-azure-full+*.deb linux-headers-6.8.12-azure-full+*.deb
    ```

---

## Cài Đặt Công Cụ `perf`

Công cụ `perf` đã được biên dịch sẵn nhưng không được đóng gói. Bạn có thể tải nó từ [**Releases**](https://github.com/wollfoo/az-kernel-full/releases/tag/v6.8.12-full) và đặt nó vào đường dẫn thực thi của hệ thống.

```bash
# Tải về perf
wget https://github.com/wollfoo/az-kernel-full/releases/download/v6.8.12-full/perf

# Cấp quyền thực thi và di chuyển vào /usr/local/bin
chmod +x perf
sudo mv perf /usr/local/bin/
```

## Xác Minh Sau Khi Cài Đặt

Sau khi cài đặt xong bằng một trong các phương pháp trên, hãy khởi động lại máy chủ của bạn.
```bash
sudo reboot
```

Khi máy chủ khởi động lại, hãy đăng nhập và kiểm tra phiên bản kernel:
```bash
uname -r
```

Kết quả trả về phải là: `6.8.12-azure-full+`
