# Kernel Linux Tùy Chỉnh cho Azure (az-kernel-full)

Kho chứa này cung cấp các gói cài đặt (`.deb`) và công cụ cho phiên bản Kernel Linux `6.8.12` được biên dịch tùy chỉnh cho môi trường Azure.

**Mục tiêu chính của phiên bản này:**
*   Kích hoạt đầy đủ các tính năng theo dõi và gỡ lỗi nâng cao của eBPF.
*   Bật `PSI tracepoints` để theo dõi áp lực tài nguyên.
*   Bật `MSR probes` để giám sát thanh ghi CPU.
*   Cung cấp một môi trường kernel mạnh mẽ cho các tác vụ phân tích hiệu năng và observability.

---

## Cách Cài Đặt

Có hai phương pháp để cài đặt kernel này.

### **Phương Pháp 1: Cài Đặt Đầy Đủ qua APT và GitHub Releases (Khuyến khích)**

Phương pháp này là cách tiếp cận toàn diện, cho phép bạn cài đặt kernel qua `apt` và sau đó bổ sung công cụ `perf` tương ứng.

**Áp dụng cho:** Ubuntu 22.04 (Jammy Jellyfish)

#### **Bước 1.1: Cài Đặt Kernel và Headers qua APT**

```bash
# Tải và thêm khóa GPG của kho chứa để xác thực gói tin
wget -qO - https://wollfoo.github.io/az-kernel-full/wollfoo.gpg | sudo gpg --dearmor -o /usr/share/keyrings/wollfoo-archive-keyring.gpg

# Thêm kho APT của chúng tôi vào danh sách nguồn của bạn
echo "deb [signed-by=/usr/share/keyrings/wollfoo-archive-keyring.gpg] https://wollfoo.github.io/az-kernel-full jammy main" | sudo tee /etc/apt/sources.list.d/wollfoo.list > /dev/null

# Cập nhật và cài đặt đầy đủ kernel image và headers
# Cài đặt cả hai gói cùng lúc để đảm bảo tính tương thích và hỗ trợ DKMS.
sudo apt-get update
sudo apt-get install linux-image-6.8.12-azure-full+ linux-headers-6.8.12-azure-full+
```

#### **Bước 1.2: Cài Đặt Công Cụ `perf` (Thủ công)**

> **Lưu ý quan trọng:** Công cụ `perf` phải có phiên bản khớp chính xác với kernel. Do đó, nó không thể được phân phối qua kho APT và phải được cài đặt thủ công từ GitHub Releases để đảm bảo tương thích.

```bash
# Tải về tệp thực thi perf đã được biên dịch sẵn
wget https://github.com/wollfoo/az-kernel-full/releases/download/v6.8.12-full/perf

# Cấp quyền thực thi và di chuyển vào đường dẫn hệ thống
chmod +x perf
sudo mv perf /usr/local/bin/

# Kiểm tra phiên bản để xác nhận
perf --version
```

### **Phương Pháp 2: Cài Đặt Thủ Công Toàn Bộ từ GitHub Releases**

Phương pháp này phù hợp nếu bạn muốn cài đặt thủ công hoàn toàn.

1.  **Tải các tệp cần thiết:**
    Truy cập trang [**Releases**](https://github.com/wollfoo/az-kernel-full/releases/tag/v6.8.12-full) và tải về các tệp:
    *   `linux-image-6.8.12-azure-full+..._amd64.deb`
    *   `linux-headers-6.8.12-azure-full+..._amd64.deb` (Tùy chọn nhưng khuyến khích)
    *   `perf`

2.  **Cài đặt các gói `.deb`:**
    ```bash
    sudo dpkg -i linux-image-6.8.12-azure-full+*.deb linux-headers-6.8.12-azure-full+*.deb
    ```

3.  **Cài đặt công cụ `perf`:**
     ```bash
    chmod +x perf
    sudo mv perf /usr/local/bin/
    ```
---

## Xác Minh Sau Khi Cài Đặt

Sau khi cài đặt xong bằng một trong các phương pháp trên, hãy khởi động lại máy chủ của bạn.
```