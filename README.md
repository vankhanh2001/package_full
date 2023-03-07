# Máy chủ OpenWrt OPKG cá nhân
Cài đặt gói nâng cấp và ứng dụng sửa đổi OpenWrt (có thể là: OpenClash, Passwall, ShadowSocksR+ Plus, Wegare STL, Tiny File Manager, Xderm Mini, v2rayA, Modeminfo, dll) sau đó.

Ưu điểm của việc cài đặt và cập nhật bằng máy chủ tùy chỉnh như thế này là:
1. Không cần bận tâm đến việc sử dụng wget và curl rất dài và phức tạp.
2. Cài đặt gói Ipk có thể sử dụng `opkg install package-name`.
3. Cài đặt gói IPK cũng có thể sử dụng tính năng **System - Software** trên LuCI OpenWrt.

Danh sách các nội dung:
- [danh sách kiến ​​trúc](#kiến trúc danh sách)
- [Cách thêm kho lưu trữ vào bản cập nhật phần mềm OpenWrt](#cách thêm kho lưu trữ vào phần mềm cập nhật openwrt)
- [Cách cài đặt và cập nhật gói](#cách cài đặt và cập nhật gói)
- [Cách kiểm tra gói đã được cài đặt hay chưa](#cách kiểm tra gói đã cài đặt hay chưa)
- [Tín dụng](#Tín dụng)

## danh sách kiến ​​trúc
Kho lưu trữ này hỗ trợ các kiến ​​trúc sau:

```
aarch64_cortex-a53
aarch64_cortex-a72
aarch64_generic
arm_arm1176jzf-s_vfp
arm_cortex-a7_neon-vfpv4
i386_pentium4
mips_24kc
mipsel_24kc
x86_64
```

## Cách thêm kho lưu trữ vào bản cập nhật phần mềm OpenWrt
Để thêm kho lưu trữ này vào phần sụn, bạn có thể sử dụng 2 cách, cụ thể là:
- [Sử dụng LuCI](#sử dụng-luci)
- [Sử dụng thiết bị đầu cuối](#thiết bị đầu cuối sử dụng) hoặc JuiceSSH/Termius/Termux


### Sử dụng LuCI

  1. Nhập LuCI IP (ví dụ: 192.168.1.1), Đăng nhập, Mở **System -> Software -> Configuration**
  
  2. Thêm dấu # (hedge) trước dòng ```option check_signature```, ví dụ bên dưới
  
      thay đổi văn bản dưới đây
      
      ```
      option check_signature
      ```
      
      được như thế này
      
      ```
      # option check_signature
      ```

  3. Trong phần nguồn cấp dữ liệu tùy chỉnh, hãy thêm danh sách bên dưới
      ```
      src/gz custom_generic https://github.com/vankhanh2001/package_full/tree/main/generic
      src/gz custom_arch https://github.com/vankhanh2001/package_full/tree/main/x86_64
      ```

     thay đổi **X86** và điều chỉnh kiến ​​trúc CPU bộ định tuyến OpenWrt của bạn

 
### Sử dụng thiết bị đầu cuối
  1. Sử dụng một trong các đề xuất ứng dụng Terminal bên dưới
      - Terminal TTYD (Paket OpenWrt)
      - JuiceSSH
      - Termius
      - Termux
      
      > Lưu ý: Người dùng có thể sử dụng các ứng dụng đầu cuối khác ngoài những ứng dụng được đề cập ở trên
  
  2. Sao chép dán bên dưới vào thiết bị đầu cuối, nó sẽ tự động điều chỉnh theo loại kiến ​​​​trúc cpu của bộ định tuyến
      
      ```
      sed -i 's/option check_signature/# option check_signature/g' /etc/opkg.conf
      echo "src/gz custom_generic https://github.com/vankhanh2001/package_full/tree/main/generic" >> /etc/opkg/customfeeds.conf
      echo "src/gz custom_arch https://github.com/vankhanh2001/package_full/tree/main/$(grep "OPENWRT_ARCH" /etc/os-release | awk -F '"' '{print $2}')" >> /etc/opkg/customfeeds.conf
      ```

      > Lưu ý: đối với phần sụn OpenWrt 19.07, vẫn có những thứ cần được cài đặt thủ công, chẳng hạn như `kcptun-client`, `xray-core` và `libcap-bin`.
    
    

## Cách cài đặt và cập nhật gói
Cách cài đặt kho lưu trữ này, bạn có thể sử dụng 2 cách cụ thể là
- [Sử dụng LuCI](#install-and-update-package-using-luci)
- [Sử dụng Terminal](#install-and-update-packages-using-terminal) như JuiceSSH/Termius/Termux
### Cài đặt và cập nhật các gói bằng LuCI
  1. Nhập IP của LuCI (ví dụ: 192.168.1.1), Đăng nhập, Đi tới **System -> Software -> Configuration**
  2. Nhấn nút **Update Lists**.
  3. Tìm tên gói (chẳng hạn như: `luci-app-passwall`) trong trường **Filter**.
  4. Nhấn nút **Tìm gói hàng**.
  5. Cuộn một chút, xem bên dưới có các tab **Gói đã cài đặt** và **Gói có sẵn**:
      - Các gói đã cài đặt: các gói trong danh sách đã được cài đặt.
      - Các gói khả dụng: các gói trong danh sách chưa được cài đặt.
  6. Nhấp vào **Các gói có sẵn**, sau đó tìm tên gói được ghi trong bộ lọc trước đó.
  7. Nhấp vào **Cài đặt** trên dòng trong tên gói, sau đó đợi quá trình cài đặt gói kết thúc.
 
### Cài đặt và cập nhật các gói bằng Terminal
  1. Mở ứng dụng đầu cuối ưa thích của bạn
  2. Chạy lệnh bên dưới để cập nhật danh sách các gói có sẵn trên máy chủ
      ```
      opkg update
      ```
  
  3. Chạy lệnh `opkg install package-name`, thay thế `package-name` thành tên gói hiện có (ví dụ này sẽ sử dụng gói `luci-app-passwall`).
      
      ```
      opkg install luci-app-passwall
      ```

## Cách kiểm tra gói đã được cài đặt hay chưa
Cách cài đặt kho lưu trữ này, bạn có thể sử dụng 2 cách cụ thể là
- [Sử dụng LuCI](#cách-kiểm-tra-trạng-thái-bưu-kiện-bằng-luci)
- [Sử dụng thiết bị đầu cuối](#cách-kiểm-tra-trạng-thái-bưu-kiện-với-thiết-bị-thiết-bị) giống JuiceSSH/Termius/Termux

### Cách kiểm tra trạng thái gói hàng với LuCI
  1. Nhập IP LuCI (ví dụ: 192.168.1.1), sau đó Đăng nhập.
      - Nếu bạn cài đặt một gói có chứa từ `luci-app`, nó sẽ thường xuất hiện trong System/Services/NAS/VPN/Modem/Network của LuCI và những thứ khác.
      - Nếu bạn cài đặt một gói chứa từ `luci-proto`, nó sẽ thường xuất hiện trong **Mạng -> Chọn giao diện -> Cài đặt chung -> Giao thức**.
      - Nếu bạn cài đặt một gói có chứa từ `luci-theme`, nó sẽ thường xuất hiện trong **System -> System Properties -> Language and Style -> Design**.
      - Nếu gói đã cài đặt không chứa từ luci, thì gói đó sẽ không hiển thị bất kỳ thứ gì trong LuCI.

### Cách kiểm tra trạng thái gói hàng bằng Terminal
  1. Mở một thiết bị đầu cuối
  2. Chạy lệnh `opkg list-install package-nama`, thay thế `package-name` thành tên gói hiện có (trong ví dụ này, chúng tôi sẽ sử dụng gói `luci-app-passwall`).
      
      ```
      opkg list-installed luci-app-passwall
      ```
      
      Nếu trong thiết bị đầu cuối xuất hiện `luci-app-passwall - 4.43-2` thì gói ứng dụng đã được cài đặt, nếu không có thì gói chưa được cài đặt. Số `4.43-2` trong terminal là phiên bản của gói ứng dụng đã cài đặt.
      
