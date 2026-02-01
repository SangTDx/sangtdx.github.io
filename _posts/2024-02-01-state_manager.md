---
layout: post
title:  "AUTOSAR Adaptive State Management Chi Tiết"
summary: "Giải thích chi tiết về AUTOSAR State Management - thành phần điều phối trung tâm trong AUTOSAR Adaptive Platform"
author: sangtdx
date: '2024-02-01 1:35:23 +0530'
category: ['autosar', 'automotive']
tags: autosar, adaptive, state-management
thumbnail: /assets/img/blogs/adaptive_autosar/adaptive-autosar.png
keywords: autosar, adaptive, state management, automotive
usemathjax: false
permalink: /blog/state-manager/
---

# Giải Thích Chi Tiết về AUTOSAR State Management (Quản Lý Trạng Thái)

## Tài liệu gốc
- **Tiêu đề**: Specification of State Management
- **Phiên bản**: AUTOSAR AP R20-11
- **Ngày phát hành**: 2020-11-30
- **Mã tài liệu**: 908

---

## MỤC LỤC
1. [Giới Thiệu Tổng Quan](#1-giới-thiệu-tổng-quan)
2. [Các Khái Niệm Cơ Bản](#2-các-khái-niệm-cơ-bản)
3. [Chức Năng Chính của State Management](#3-chức-năng-chính-của-state-management)
4. [Các Trạng Thái Quan Trọng](#4-các-trạng-thái-quan-trọng)
5. [Tương Tác với Các Module Khác](#5-tương-tác-với-các-module-khác)
6. [Giao Diện Lập Trình (API & Service Interfaces)](#6-giao-diện-lập-trình-api--service-interfaces)
7. [Các Ví Dụ Thực Tế](#7-các-ví-dụ-thực-tế)

---

## 1. GIỚI THIỆU TỔNG QUAN

### State Management là gì?

**State Management (Quản Lý Trạng Thái)** là một thành phần chức năng (Functional Cluster) quan trọng trong nền tảng AUTOSAR Adaptive Platform. Nó giống như một "người điều phối trung tâm" trong hệ thống ô tô, chịu trách nhiệm quản lý và điều khiển các chế độ hoạt động của toàn bộ hệ thống.

### Tại sao cần State Management?

Hãy tưởng tượng một chiếc xe ô tô hiện đại có rất nhiều chức năng:
- Hệ thống giải trí
- Hệ thống điều hướng
- Hệ thống an toàn
- Hệ thống chẩn đoán
- Hệ thống cập nhật phần mềm

Tất cả các hệ thống này cần phải được bật/tắt theo đúng thứ tự và phối hợp với nhau. **State Management** chính là thành phần đảm nhiệm việc này.

### Vị trí trong AUTOSAR Architecture

State Management thuộc **Adaptive Platform Services** (các dịch vụ nền tảng thích ứng), là một phần của AUTOSAR Adaptive Platform - nền tảng dành cho các hệ thống ô tô hiện đại có tính năng phức tạp.

<figure style="text-align: center;">
  <img src="/assets/img/blogs/adaptive_autosar/adaptive-autosar.png" alt="AUTOSAR Adaptive State Management Architecture" style="display: block; margin: 0 auto;">
  <figcaption>AUTOSAR Adaptive State Management Architecture</figcaption>
</figure>

---

## 2. CÁC KHÁI NIỆM CƠ BẢN

### 2.1. Các Thuật Ngữ Quan Trọng

#### **Machine (Máy)**
- Là một đơn vị ECU (Electronic Control Unit - Bộ điều khiển điện tử) hoàn chỉnh
- Có thể là một máy vật lý hoặc máy ảo trong môi trường ảo hóa

#### **Process (Tiến trình)**
- Là một chương trình đang chạy trên hệ điều hành
- Tương đương với khái niệm process trong Linux/Unix

#### **Modelled Process (Tiến trình được Mô hình hóa)**
- Là một instance của một Executable (file thực thi)
- Được định nghĩa trong file cấu hình ARXML/Meta-Model
- Có quan hệ 1:1 với một process đang chạy

#### **Function Group (Nhóm Chức Năng)**
- Là một tập hợp các Modelled Processes có liên quan chức năng với nhau
- Cần được điều khiển một cách nhất quán
- Ví dụ: 
  - "InfotainmentFG" - nhóm các ứng dụng giải trí
  - "NavigationFG" - nhóm các ứng dụng điều hướng
  - "DiagnosticsFG" - nhóm các ứng dụng chẩn đoán

#### **Function Group State (Trạng Thái của Nhóm Chức Năng)**
- Đặc trưng cho tình trạng hiện tại của một Function Group
- Ví dụ: "On" (Bật), "Off" (Tắt), "Standby" (Chờ), "Verify" (Xác thực)

#### **Machine State (Trạng Thái Máy)**
- Là một loại Function Group State đặc biệt
- Tên Function Group là "MachineFG"
- Có các trạng thái được định nghĩa sẵn: **Startup**, **Shutdown**, **Restart**
- Chủ yếu điều khiển vòng đời của máy và các ứng dụng cấp nền tảng

#### **Adaptive Application (Ứng dụng Thích ứng)**
- Là các ứng dụng chạy trên AUTOSAR Adaptive Platform
- Có thể là ứng dụng người dùng hoặc ứng dụng nền tảng

#### **Execution Management (Quản Lý Thực Thi)**
- Là thành phần chịu trách nhiệm khởi động và dừng các processes
- State Management ra lệnh, Execution Management thực hiện

#### **Network Handle (Xử Lý Mạng)**
- Đại diện cho một tập hợp các mạng (hoặc mạng con)
- Được cung cấp bởi Network Management

#### **Communication Groups (Nhóm Giao tiếp)**
- Là một mẫu giao tiếp đặc biệt
- Cho phép một server gửi tin nhắn đến nhiều clients
- Server có thể theo dõi phản hồi từ tất cả các clients

### 2.2. Lịch Sử Phát Triển của Tài Liệu

Tài liệu này đã trải qua nhiều phiên bản:

- **R18-10 (2018-10-31)**: Phiên bản đầu tiên
- **R19-03 (2019-03-29)**: 
  - Loại bỏ một số components
  - Đưa RequestState và ReleaseRequest vào trạng thái deprecated
  - Giới thiệu "Trigger" và "Notifier" fields
- **R19-11 (2019-11-28)**:
  - Thay đổi interface với ExecutionManagement thành StateClient
  - Chuyển trạng thái tài liệu từ Final sang published
- **R20-11 (2020-11-30)** - Phiên bản hiện tại:
  - Cập nhật interface với Update And Configuration Management
  - Cập nhật interface với Adaptive Diagnostics
  - Giới thiệu Diagnostic Reset dựa trên Communication Groups
  - Cập nhật interface với Platform Health Management
  - Chuyển error reactions cho supervised entity failures sang State Management
  - Giới thiệu PowerModes dựa trên Communication Groups
  - Loại bỏ RequestState và ReleaseRequest interface

---

## 3. CHỨC NĂNG CHÍNH CỦA STATE MANAGEMENT

### 3.1. Vai Trò Trung Tâm

State Management là **điểm trung tâm** nơi:
- Nhận tất cả các sự kiện hoạt động có thể ảnh hưởng đến trạng thái nội bộ
- Đánh giá các sự kiện này
- Quyết định dựa trên:
  - **Loại sự kiện** (Event type) - được định nghĩa theo yêu cầu dự án
  - **Độ ưu tiên sự kiện** (Event priority) - được định nghĩa theo yêu cầu dự án
  - **Application identifier** - định danh ứng dụng (đang thảo luận với Identity and Access Management)

### 3.2. Nhiệm Vụ Chính

#### A. Quản Lý Machine State (Trạng Thái Máy)
- Điều khiển vòng đời của toàn bộ máy
- Quản lý các trạng thái: Startup, Shutdown, Restart
- Điều phối các processes cấp nền tảng

#### B. Quản Lý Function Group State (Trạng Thái Nhóm Chức Năng)
- Điều khiển các nhóm ứng dụng có liên quan chức năng
- Cho phép khởi động/dừng các nhóm một cách độc lập
- Hỗ trợ các vòng đời đan xen (interleaving lifecycles)

#### C. Nhận và Xử Lý Yêu Cầu Thay Đổi Trạng Thái
Các yêu cầu có thể đến từ nhiều nguồn:
- **Platform Health Management**: Khi phát hiện lỗi, yêu cầu chuyển sang chế độ fallback
- **Adaptive Diagnostics**: Chuyển hệ thống sang các trạng thái chẩn đoán khác nhau, hoặc yêu cầu reset
- **Update and Config Management**: Chuyển hệ thống sang trạng thái cho phép cập nhật phần mềm
- **Network Management**: Phối hợp chức năng với trạng thái mạng
- **Adaptive Applications**: Ứng dụng tự gửi yêu cầu thông qua ara::com interface

#### D. Cung Cấp Thông Tin Trạng Thái
- Cung cấp các ara::com fields để các ứng dụng khác có thể:
  - **Subscribe** (Đăng ký) để nhận thông báo khi trạng thái thay đổi (qua "Notifier" fields)
  - **Write** (Ghi) để yêu cầu thay đổi trạng thái (qua "Trigger" fields)

#### E. Điều Khiển Mạng
- Bật/tắt các mạng (hoặc mạng con) thông qua Network Management
- Phối hợp trạng thái Function Groups với trạng thái mạng

#### F. Ngăn Chặn Shutdown Không Mong Muốn
- Không cho phép hệ thống tắt khi:
  - Có phiên chẩn đoán (diagnostic session) đang hoạt động
  - Có phiên cập nhật (update session) đang hoạt động
- Giám sát việc ngăn chặn này với timeout theo dự án

### 3.3. Tính Linh Hoạt và Đặc Thù Dự Án

AUTOSAR **không chỉ định cụ thể** logic phân xử (arbitration logic) vì:
- Mỗi dự án có yêu cầu khác nhau
- Cần tính linh hoạt cao

Thay vào đó:
- AUTOSAR chỉ định các **service interfaces cơ bản**
- Logic phân xử được đóng gói trong **code đặc thù dự án** (project-specific code)
- Code này có thể được:
  - Phát triển thủ công
  - Sinh tự động từ các tham số cấu hình chuẩn hóa

---

## 4. CÁC TRẠNG THÁI QUAN TRỌNG

### 4.1. Machine State (Trạng Thái Máy)

Machine State là một loại Function Group State đặc biệt với tên "MachineFG".

#### 4.1.1. Startup (Khởi động)

**Mục đích**: Đưa hệ thống từ trạng thái tắt lên trạng thái hoạt động ban đầu.

**Yêu cầu quan trọng**:
- State Management **PHẢI** được cấu hình chạy trong trạng thái Startup
- Nếu không, hệ thống sẽ bị "kẹt" mãi mãi trong Startup và không thể chuyển sang trạng thái khác

**Quy trình khởi động** (ví dụ):
```
1. Hệ thống bật nguồn
2. Execution Management khởi động State Management trong Machine State "Startup"
3. State Management đánh giá điều kiện
4. State Management yêu cầu Execution Management chuyển sang Machine State "Driving" (hoặc trạng thái hoạt động ban đầu khác)
5. Execution Management:
   - Dừng các processes không cần thiết trong Driving
   - Khởi động các processes cần thiết cho Driving
   - Xác nhận hoàn thành với State Management
```

#### 4.1.2. Shutdown (Tắt máy)

**Mục đích**: Tắt hệ thống một cách an toàn và sạch sẽ.

**Yêu cầu quan trọng**:
- State Management **PHẢI** được cấu hình chạy trong trạng thái Shutdown
- Lý do: Cần có thành phần để báo lỗi nếu quá trình shutdown thất bại

**Lưu ý đặc biệt**:
- Tại một thời điểm nào đó, State Management cũng sẽ bị tắt
- Sau khi State Management tắt, các lỗi sẽ được xử lý theo cách đặc thù implementation

**Quy trình shutdown** (ví dụ):
```
1. Nhận yêu cầu shutdown (từ người dùng, từ logic nội bộ, hoặc từ ứng dụng khác)
2. State Management kiểm tra:
   - Có phiên chẩn đoán đang hoạt động không?
   - Có phiên cập nhật đang hoạt động không?
3. Nếu không có phiên nào đang hoạt động:
   - State Management có thể yêu cầu tắt tất cả Function Groups về "Off"
   - Sau đó yêu cầu Execution Management chuyển Machine State sang "Shutdown"
4. Execution Management thực hiện shutdown các processes theo thứ tự
5. Cuối cùng, một process sẽ khởi tạo OS/HW shutdown
```

#### 4.1.3. Restart (Khởi động lại)

**Mục đích**: Khởi động lại hệ thống.

**Yêu cầu quan trọng**:
- State Management **PHẢI** được cấu hình chạy trong trạng thái Restart
- Lý do tương tự như Shutdown

**Trường hợp sử dụng**:
- Update and Config Management yêu cầu restart sau khi cập nhật
- Adaptive Diagnostics yêu cầu reset loại "hardReset"
- Platform Health Management yêu cầu restart để phục hồi từ lỗi

### 4.2. Function Group State (Trạng Thái Nhóm Chức Năng)

#### 4.2.1. Tại Sao Cần Function Groups?

**Vấn đề**: Machine State không đủ linh hoạt nếu:
- Có nhiều hơn một nhóm ứng dụng có liên quan chức năng trên cùng một máy
- Các nhóm cần được khởi động/dừng với vòng đời đan xen nhau
- Cần điều khiển từng nhóm một cách độc lập

**Giải pháp**: Sử dụng Function Groups và Function Group States

#### 4.2.2. Sự Khác Biệt với Machine State

| Machine State | Other Function Group States |
|--------------|------------------------------|
| Điều khiển vòng đời máy (startup/shutdown/restart) | Điều khiển các nhóm ứng dụng cụ thể |
| Điều khiển processes cấp nền tảng | Điều khiển processes cấp người dùng |
| Tên Function Group: "MachineFG" | Tên tùy ý theo dự án |

#### 4.2.3. Cấu Hình Function Groups

**[SWS_SM_00001]**: State Management phải lấy thông tin về các Function Groups có sẵn và các trạng thái khả dụng từ **Machine Manifest** để thiết lập quản lý trạng thái cho từng Function Group.

**Machine Manifest** là file cấu hình chứa:
- Danh sách các Function Groups
- Các Function Group States của mỗi Function Group
- Cấu hình đặc thù cho máy

#### 4.2.4. Dependencies Giữa Function Groups

**Từ góc độ Execution Management**: Các Function Groups là độc lập, không ảnh hưởng lẫn nhau.

**Từ góc độ State Management**: Có thể có dependencies.

**Ví dụ**: 
```
Khi shutdown máy:
1. Execution Management chỉ cần chuyển Machine State sang "Shutdown"
2. Nhưng State Management phải:
   - Đánh giá xem có hợp lệ để shutdown không?
   - Có thể cần tắt tất cả Function Groups trước
   - Sau đó mới yêu cầu chuyển Machine State sang "Shutdown"
```

**Lưu ý**: Dependencies này là đặc thù implementation và có thể cấu hình bởi integrator.

#### 4.2.5. Hỗ Trợ Calibration Data (Dữ liệu Hiệu chuẩn)

Hệ thống có thể chứa **calibration data** để xử lý biến thể (variant handling):
- Một số Function Groups có thể không được cấu hình để chạy trên biến thể hệ thống cụ thể

**[SWS_SM_00005]**: State Management phải nhận thông tin về các Function Groups bị deactivated từ calibration data.

**[SWS_SM_00006]**: State Management phải từ chối yêu cầu từ các ứng dụng để thay đổi Function Group State của một Function Group không được cấu hình chạy trong biến thể này.

---

## 5. TƯƠNG TÁC VỚI CÁC MODULE KHÁC

### 5.1. Execution Management

**Vai trò**: Thực hiện các thay đổi trạng thái mà State Management yêu cầu.

**Quan hệ**:
- **State Management**: Ra quyết định (Decision maker)
- **Execution Management**: Người thực thi (Executor)

**Quy trình tương tác**:
```
1. State Management quyết định cần thay đổi Function Group State
2. State Management gọi API của Execution Management
3. Execution Management:
   - Dừng các processes không còn cần thiết
   - Khởi động các processes mới cần thiết
   - Trả về kết quả cho State Management
4. State Management đánh giá kết quả:
   - Nếu thành công: Cập nhật trạng thái nội bộ, thông báo qua "Notifier" fields
   - Nếu thất bại: Thực hiện các hành động đặc thù dự án (ví dụ: retry, báo lỗi, fallback)
```

**Yêu cầu**:
- **[SWS_SM_00400]**: State Management phải sử dụng API của Execution Management để thay đổi Machine State hoặc Function Group State.
- **[SWS_SM_00401]**: State Management phải đánh giá kết quả từ Execution Management và thực hiện các hành động đặc thù dự án.
- **[SWS_SM_00402]**: State Management phải cung cấp Function Group States dựa trên kết quả từ Execution Management qua service interface.

**Lưu ý**: Execution Management không được tự ý thực hiện thay đổi Function Group State. Điều này tạo ra yêu cầu cấu hình:
- State Management phải chạy trong **MỌI** Machine State (Startup, Shutdown, Restart)
- Nếu không, hệ thống sẽ bị "kẹt" mãi mãi

### 5.2. Platform Health Management (PHM)

**Vai trò**: Giám sát các thực thể (entities) được cấu hình và thông báo cho State Management khi có lỗi.

**Cách thức giám sát**:
- PHM thực hiện **local supervision** (giám sát cục bộ) các entities
- PHM kiểm tra status của **health channels**
- Lỗi từ local supervisions được tích lũy trong **global supervision**
- Phạm vi của global supervision là một Function Group (hoặc một phần của nó)

**Quy trình tương tác**:
```
1. PHM phát hiện lỗi:
   - Global supervision chuyển sang trạng thái "stopped"
   - Hoặc health channel chứa thông tin quan trọng cho State Management
2. PHM thông báo cho State Management qua C++ API
   - Interface: Class với virtual functions
   - State Management implement các functions này
3. State Management nhận thông báo qua RecoveryHandler()
4. State Management đánh giá thông tin lỗi
5. State Management thực hiện các hành động phục hồi đặc thù dự án:
   - Yêu cầu Execution Management chuyển Function Group sang state khác
   - Yêu cầu restart máy
   - Kích hoạt chức năng fallback
   - ...
```

**Cơ chế timeout**:
- PHM giám sát việc RecoveryHandler() trả về với timeout có thể cấu hình
- Nếu sau một số lần retry được cấu hình mà State Management vẫn không trả về đúng:
  - PHM sẽ thực hiện các biện pháp đối phó riêng
  - Ví dụ: Trigger sai watchdog hoặc ngừng trigger watchdog

### 5.3. Adaptive Diagnostics

**Vai trò**: Chịu trách nhiệm chẩn đoán, cấu hình và reset các Diagnostic Addresses.

**Diagnostic Address**: Có quan hệ với Software Cluster (đặc thù dự án).

**Interface**: C++ API được cung cấp bởi Adaptive Diagnostics
- Class với virtual functions
- State Management implement các functions này

#### 5.3.1. Ngăn Chặn Shutdown Khi Có Phiên Chẩn Đoán

**[SWS_SM_00100]**: State Management không được shutdown hệ thống khi đang xử lý yêu cầu từ Adaptive Diagnostics.

**Lý do**: Cần đảm bảo hoàn thành quá trình chẩn đoán.

#### 5.3.2. Các Loại Reset

Adaptive Diagnostics hỗ trợ nhiều loại reset (theo ISO 14229-1):
- **hardReset**: Reset cứng
- **keyOffOnReset**: Reset giống như tắt nguồn rồi bật lại
- **softReset**: Reset mềm (không cần tắt/bật lại phần cứng)
- **customReset**: Reset tùy chỉnh

**Vấn đề**: Mỗi OEM (nhà sản xuất ô tô) hiểu các loại reset này khác nhau.

**Giải pháp**: State Management ủy quyền một phần chức năng reset cho các Adaptive Applications.

**[SWS_SM_00101]**: State Management phải implement cơ chế nhận yêu cầu reset cho Diagnostic Addresses từ Adaptive Diagnostics và thực hiện các hành động đặc thù dự án cho từng loại reset.

**Ví dụ mapping**:
```
- "keyOffOnReset" → Stop và Start Function Group liên quan đến Diagnostic Address
- "softReset" → Yêu cầu Modelled Processes thực hiện chức năng nội bộ mà không cần terminate và start lại
```

#### 5.3.3. Diagnostic Reset Service

Để hỗ trợ "softReset", State Management cung cấp **service interface** trong phạm vi **CommunicationGroup**:
- Tất cả Modelled Processes muốn hỗ trợ phải sử dụng ara::com methods và fields
- Định nghĩa message và reply message trong section 9.1.2

**Quy trình**:
```
1. Adaptive Diagnostics yêu cầu softReset cho một Diagnostic Address
2. State Management xác định các Modelled Processes liên quan
3. State Management gửi DiagnosticResetMsg qua CommunicationGroup
4. Các Modelled Processes nhận message:
   - Thực hiện reset nội bộ (đặc thù dự án)
   - Gửi DiagnosticResetRespMsg trả lời
5. State Management nhận tất cả replies:
   - Nếu tất cả OK: Báo thành công cho Diagnostics
   - Nếu có lỗi: Retry hoặc thực hiện hành động đặc thù khác
```

#### 5.3.4. Lưu Trữ Reset Cause (Nguyên nhân Reset)

State Management là điểm trung tâm để yêu cầu reset máy, nên phải theo dõi nguyên nhân reset.

**[SWS_SM_00103]**: State Management phải cung cấp chức năng persist (lưu trữ bền vững) loại reset trước khi thực hiện Machine reset.

**[SWS_SM_00104]**: State Management phải đọc nguyên nhân reset được persist gần nhất khi State Management được spawn (khởi động). Nguyên nhân này phải được cung cấp cho Adaptive Diagnostics qua C++ interface.

**[SWS_SM_00105]**: State Management phải reset nguyên nhân reset được persist ngay sau khi đọc giá trị hiện tại.

**Quy trình**:
```
1. Trước khi reset máy:
   - State Management persist reset type vào bộ nhớ không bay hơi (non-volatile)
2. Sau khi máy khởi động lại:
   - State Management đọc reset type đã persist
   - Cung cấp thông tin này cho Diagnostics qua API
   - Ngay lập tức reset giá trị persist về mặc định
```

### 5.4. Update and Config Management (UCM)

**Vai trò**: Chịu trách nhiệm cài đặt, xóa bỏ hoặc cập nhật Software Clusters (đơn vị có thể cập nhật nhỏ nhất).

**Interface**: Service interfaces do State Management cung cấp (xem section 9.2.4)

**Bảo mật**: Integrator phải giới hạn việc sử dụng interface này chỉ cho UCM thông qua Identity and Access Management.

#### 5.4.1. Ngăn Chặn Shutdown Khi Có Phiên Cập Nhật

**[SWS_SM_00200]**: State Management không được shutdown hệ thống khi có phiên cập nhật đang hoạt động.

**[SWS_SM_00201]**: State Management phải giám sát thời lượng phiên cập nhật với timeout đặc thù dự án để hệ thống không chạy mãi mãi.

#### 5.4.2. Kiểm Tra Khả Năng Cập Nhật

**[SWS_SM_00203]**: State Management phải cung cấp interface cho UCM để kiểm tra xem có thể thực hiện cập nhật không.

**Quy trình**:
```
1. UCM gọi StartUpdateSession()
2. State Management đánh giá:
   - Trạng thái hiện tại của máy
   - Trạng thái của toàn bộ xe (nếu có superior State Management)
   - Các điều kiện đặc thù dự án
3. State Management trả về:
   - OK: Cho phép cập nhật
   - Rejected: Không cho phép (do trạng thái không phù hợp)
```

#### 5.4.3. Persist Session Status

**[SWS_SM_00204]**: State Management phải persist thông tin về phiên cập nhật đang diễn ra để có thể đọc lại sau bất kỳ loại Machine reset nào.

**Lý do**: 
- Một số cập nhật yêu cầu restart máy
- Sau restart, UCM cần biết đang ở bước nào để tiếp tục

**Yêu cầu sau restart**:
- Chỉ một vài Function Groups được set về một Function Group State có ý nghĩa (đặc thù dự án)
- Ít nhất UCM phải ở trạng thái running

#### 5.4.4. Yêu Cầu Reset

**[SWS_SM_00202]**: State Management phải implement interface để nhận yêu cầu Machine reset từ UCM.

**Trường hợp sử dụng**:
- Khi các Functional Clusters như State Management, PHM, hoặc Execution Management bị ảnh hưởng bởi cập nhật
- Implementation đơn giản: Yêu cầu Machine State restart từ Execution Management

#### 5.4.5. Kết Thúc Phiên Cập Nhật

**[SWS_SM_00205]**: State Management phải cung cấp interface cho UCM để thông báo rằng phiên cập nhật đã hoàn thành.

**Khi nhận được thông báo**:
- State Management xóa thông tin về ongoing update
- Tiếp tục công việc thường xuyên: Set Function Groups về các Function Group State có ý nghĩa

#### 5.4.6. Ba Bước Cập Nhật

UCM có thể thực hiện tối đa 3 bước, tùy thuộc vào loại cập nhật:

**[SWS_SM_00206] - PrepareUpdate**: 
- UCM gọi để yêu cầu chuẩn bị các Function Groups cho cập nhật
- State Management ít nhất set tất cả Function Groups (được cho làm tham số) về "Off" state

**[SWS_SM_00207] - VerifyUpdate**: 
- UCM gọi sau khi thực hiện cập nhật thực tế
- State Management ít nhất set tất cả Function Groups về "Verify" state
- Nếu bất kỳ Function Group nào không thể set về state yêu cầu → Báo failed

**[SWS_SM_00208] - PrepareRollback**: 
- UCM gọi nếu muốn revert các thay đổi trước đó
- State Management ít nhất set tất cả Function Groups về "Off" state

**Quy trình cập nhật Software Cluster**:
```
1. UCM: StartUpdateSession()
   → State Management cho phép hoặc từ chối

2. UCM: PrepareUpdate(FunctionGroups)
   → State Management set FGs về "Off"

3. UCM thực hiện cập nhật thực tế:
   - Thay thế executable
   - Thay đổi manifests
   - ...

4. UCM: VerifyUpdate(FunctionGroups)
   → State Management set FGs về "Verify"
   → FGs chạy để xác thực cập nhật

5a. Nếu thành công:
    UCM: StopUpdateSession()
    → State Management tiếp tục hoạt động bình thường

5b. Nếu thất bại:
    UCM: PrepareRollback(FunctionGroups)
    → State Management set FGs về "Off"
    → UCM revert các thay đổi
```

**Lưu ý**:
- **Khi cài đặt mới**: PrepareUpdate không được gọi
- **Khi xóa**: VerifyUpdate và PrepareRollback không được gọi

### 5.5. Network Management

**Vai trò**: Quản lý các mạng và mạng con (partial networks).

**Mục tiêu**: Adaptive Applications không cần biết cụ thể mạng nào được sử dụng để portable giữa các ECUs khác nhau.

#### 5.5.1. NetworkHandle

**NetworkHandle** là gì?
- Đại diện cho một tập hợp các (partial) networks
- Được cung cấp bởi Network Management
- Được định nghĩa trong Machine Manifest
- Được gán cho một Function Group State

**[SWS_SM_00300]**: State Management phải nhận thông tin về NetworkHandles và các Function Group States liên quan từ Machine Manifest.

#### 5.5.2. Network State → Function Group State

**Kịch bản**: Mạng được kích hoạt/vô hiệu hóa từ bên ngoài

**Quy trình**:
```
1. Mạng (hoặc partial networks) được kích hoạt/vô hiệu hóa từ bên ngoài
2. Network Management thay đổi giá trị NetworkHandle tương ứng
3. State Management nhận thông báo (vì đã đăng ký field này)
4. State Management set Function Group tương ứng về Function Group State được cấu hình cho NetworkHandle này trong Machine Manifest
```

**[SWS_SM_00301]**: State Management phải đăng ký tất cả NetworkHandles được cung cấp bởi Network Management và có trong Machine Manifest.

**[SWS_SM_00302]**: State Management phải set Function Groups về Function Group State tương ứng (được cấu hình trong Machine Manifest cho NetworkHandle) khi nhận ra sự thay đổi giá trị NetworkHandle.

#### 5.5.3. Function Group State → Network State

**Kịch bản**: Function Group thay đổi state, cần thay đổi mạng tương ứng

**Quy trình**:
```
1. Function Group cần thay đổi Function Group State
2. State Management kiểm tra Machine Manifest: Có NetworkHandle nào liên kết với FG State này không?
3. Nếu có: State Management thay đổi giá trị NetworkHandle
4. Network Management nhận ra thay đổi
5. Network Management thay đổi trạng thái (partial) networks tương ứng
```

**[SWS_SM_00303]**: State Management phải thay đổi giá trị NetworkHandle khi Function Group thay đổi Function Group State và có NetworkHandle liên kết với FG State này trong Machine Manifest.

#### 5.5.4. Afterrun (Chạy tiếp sau)

**Vấn đề**: 
- Function Group có thể cần ở lại trong state lâu hơn sau khi mạng đã tắt
- Hoặc mạng cần available lâu hơn sau khi Function Group đã chuyển sang "Off"

**Giải pháp**: Afterrun

**[SWS_SM_00304]**: State Management phải hỗ trợ cơ chế 'afterrun' để tắt Function Groups hoặc (partial) networks liên quan. Giá trị timeout cho afterrun phải được đọc từ Machine Manifest.

**Ví dụ**:
```
Timeout afterrun = 5 giây

Kịch bản 1: Network tắt trước
1. Mạng bị tắt
2. State Management đợi 5 giây
3. Sau 5 giây, State Management mới set Function Group về "Off"

Kịch bản 2: Function Group tắt trước
1. Function Group chuyển sang "Off"
2. State Management đợi 5 giây
3. Sau 5 giây, State Management mới tắt mạng tương ứng
```

### 5.6. Operating System Interface

State Management **không có** interface trực tiếp với Operating System.

Tất cả các dependencies với OS được abstract hóa bởi Execution Management.

### 5.7. Virtualized/Hierarchical Environment (Môi Trường Ảo Hóa/Phân Cấp)

#### 5.7.1. Tình Huống

Trên một ECU có thể có nhiều máy ảo (virtual machines), mỗi máy ảo chạy AUTOSAR Adaptive Platform riêng:
- Mỗi máy ảo có State Management riêng
- Cần có một máy ảo giám sát toàn bộ trạng thái ECU

Điều này cũng áp dụng cho môi trường phân cấp (không chỉ ảo hóa).

#### 5.7.2. Yêu Cầu

**[SWS_SM_00500]**: State Management phải có khả năng đăng ký các "Trigger" fields của một State Management instance giám sát để nhận thông tin về trạng thái toàn bộ ECU.

**[SWS_SM_00501]**: State Management phải implement cơ chế tính toán trạng thái nội bộ dựa trên thông tin từ State Management instance giám sát.

#### 5.7.3. Kiến Trúc

```
┌─────────────────────────────────────────┐
│   Superior State Management (ECU-level) │
│   - Giám sát toàn bộ ECU                 │
│   - Cung cấp "Trigger" fields            │
└────────────┬────────────────────────────┘
             │
    ┌────────┴────────┬──────────────┐
    │                 │              │
┌───▼────┐      ┌────▼───┐    ┌────▼───┐
│  VM1   │      │  VM2   │    │  VM3   │
│  SM    │      │  SM    │    │  SM    │
└────────┘      └────────┘    └────────┘
```

---

## 6. GIAO DIỆN LẬP TRÌNH (API & SERVICE INTERFACES)

### 6.1. Tổng Quan

State Management **không cung cấp C++ API công khai**. Thay vào đó, tất cả functional interfaces được cung cấp qua **Service Interfaces** (Chapter 9).

Service Interfaces sử dụng **ara::com** - framework giao tiếp của AUTOSAR Adaptive Platform.

### 6.2. Type Definitions (Định Nghĩa Kiểu Dữ Liệu)

#### 6.2.1. PowerMode Types

**PowerModeMsg** [SWS_SM_91011]:
- **Kiểu**: STRING
- **Mô tả**: Message gửi đến tất cả Processes đang chạy để yêu cầu chuyển PowerMode
- **Các giá trị**:
  - **"On"**: Hoạt động bình thường
  - **"Off"**: Chuẩn bị persist data cho shutdown
  - **"Suspend"**: Chuẩn bị cho suspend-to-RAM

**PowerModeRespMsg** [SWS_SM_91012]:
- **Kiểu**: VALUE (số nguyên)
- **Mô tả**: Reply message từ Process nhận được PowerModeMsg
- **Các giá trị**:
  - **Done (0)**: Đã chuyển sang mode yêu cầu thành công
  - **Failed (1)**: Không chuyển được sang mode yêu cầu
  - **Busy (2)**: Không thể xử lý mode yêu cầu (đang có công việc quan trọng)
  - **NotSupported (3)**: Không hỗ trợ mode yêu cầu

#### 6.2.2. DiagnosticReset Types

**DiagnosticResetMsg** [SWS_SM_91013]:
- **Kiểu**: STRING
- **Mô tả**: Message gửi đến tất cả Processes trong một SoftwareCluster để yêu cầu thực hiện Diagnostic SoftReset
- **Các giá trị**:
  - **"SoftReset"**: Thực hiện soft reset

**DiagnosticResetRespMsg** [SWS_SM_91014]:
- **Kiểu**: VALUE (số nguyên)
- **Mô tả**: Reply message từ Process nhận được DiagnosticResetMsg
- **Các giá trị**:
  - **Done (0)**: Reset thực hiện thành công
  - **Failed (1)**: Reset không thành công
  - **Busy (2)**: Không thể thực hiện reset (đang có công việc quan trọng)
  - **NotSupported (3)**: Không hỗ trợ reset

#### 6.2.3. Update And Configuration Management Types

**FunctionGroupList** [SWS_SM_91018]:
- **Kiểu**: VECTOR (mảng)
- **Subelements**: FGNameType
- **Mô tả**: Danh sách các FunctionGroups

**FGNameType** [SWS_SM_91019]:
- **Kiểu**: STRING
- **Mô tả**: Full qualified FunctionGroup shortName

### 6.3. Provided Service Interfaces (Giao Diện Dịch Vụ Được Cung Cấp)

#### 6.3.1. TriggerIn (Trigger Đầu Vào)

**Port** [SWS_SM_91001]:
- **Tên**: TriggerIn_{State}
- **Loại**: ProvidedPort
- **Interface**: TriggerIn
- **Mô tả**: Được sử dụng bởi Adaptive (Platform) Applications để trigger State Management thay đổi trạng thái nội bộ

**Service Interface** [SWS_SM_91007]:
- **Tên**: TriggerIn_{StateGroup}
- **Namespace**: ara::sm
- **Field**: Trigger
  - **Mô tả**: Giá trị được State Management đánh giá theo cách đặc thù dự án
  - **Type**: project_specific (đặc thù dự án)
  - **HasGetter**: false
  - **HasNotifier**: false
  - **HasSetter**: true (Chỉ cho phép ghi)

**Cách sử dụng**:
```cpp
// Adaptive Application muốn trigger State Management
auto trigger_proxy = /* get proxy to TriggerIn service */;
trigger_proxy->Trigger.Set(new_trigger_value);
```

#### 6.3.2. TriggerOut (Trigger Đầu Ra)

**Port** [SWS_SM_91002]:
- **Tên**: TriggerOut_{State}
- **Loại**: ProvidedPort
- **Interface**: TriggerOut
- **Mô tả**: Được sử dụng bởi Adaptive (Platform) Applications để được thông báo khi State Management thay đổi trạng thái nội bộ

**Service Interface** [SWS_SM_91008]:
- **Tên**: TriggerOut_{StateGroup}
- **Namespace**: ara::sm
- **Field**: Notifier
  - **Mô tả**: Được set bởi State Management theo cách đặc thù dự án để thông báo cho Applications về thay đổi
  - **Type**: project_specific
  - **HasGetter**: true (Cho phép đọc giá trị hiện tại)
  - **HasNotifier**: true (Cho phép subscribe để nhận thông báo)
  - **HasSetter**: false

**Cách sử dụng**:
```cpp
// Adaptive Application muốn nhận thông báo
auto notifier_proxy = /* get proxy to TriggerOut service */;

// Đăng ký callback
notifier_proxy->Notifier.Subscribe([](auto value) {
    // Xử lý khi State Management thay đổi trạng thái
    std::cout << "State changed to: " << value << std::endl;
});

// Hoặc đọc giá trị hiện tại
auto current_state = notifier_proxy->Notifier.Get();
```

#### 6.3.3. TriggerInOut (Trigger Cả Hai Chiều)

**Port** [SWS_SM_91003]:
- **Tên**: TriggerInOut_{State}
- **Loại**: ProvidedPort
- **Interface**: TriggerInOut
- **Mô tả**: Được sử dụng bởi Adaptive (Platform) Applications để trigger State Management thay đổi trạng thái nội bộ VÀ để nhận thông tin khi việc thay đổi được thực hiện

**Service Interface** [SWS_SM_91009]:
- **Tên**: TriggerInOut_{StateGroup}
- **Namespace**: ara::sm
- **Field 1**: Trigger
  - **Mô tả**: Giá trị được State Management đánh giá theo cách đặc thù dự án
  - **Type**: project_specific
  - **HasGetter**: false
  - **HasNotifier**: false
  - **HasSetter**: true
- **Field 2**: Notifier
  - **Mô tả**: Được set bởi State Management để thông báo cho Applications
  - **Type**: project_specific
  - **HasGetter**: true
  - **HasNotifier**: true
  - **HasSetter**: false

**Ý nghĩa**: 
- Khi giá trị Trigger thay đổi
- State Management xử lý
- Sau đó Notifier thay đổi để phản ánh kết quả

**Cách sử dụng**:
```cpp
auto service_proxy = /* get proxy to TriggerInOut service */;

// Subscribe để nhận thông báo khi State Management hoàn thành
service_proxy->Notifier.Subscribe([](auto value) {
    std::cout << "State Management completed transition to: " << value << std::endl;
});

// Yêu cầu thay đổi
service_proxy->Trigger.Set(requested_state);
// Sau khi State Management xử lý xong, callback sẽ được gọi
```

#### 6.3.4. UpdateRequest

**Port** [SWS_SM_91016]:
- **Tên**: UpdateRequest
- **Loại**: ProvidedPort
- **Interface**: UpdateRequest
- **Mô tả**: Được sử dụng bởi Update And Configuration Management để yêu cầu State Management thực hiện các bước cập nhật SoftwareClusters

**Service Interface** [SWS_SM_91017]:
- **Tên**: UpdateRequest
- **Namespace**: ara::sm

**Methods (Các phương thức)**:

**1. StartUpdateSession**:
- **Mô tả**: Phải được gọi bởi UCM khi cần bắt đầu tương tác với State Management. State Management có thể từ chối nếu máy không ở trạng thái phù hợp để cập nhật.
- **FireAndForget**: false (Chờ response)
- **Application Errors**: 
  - **kRejected**: Yêu cầu bị từ chối do trạng thái nội bộ

**2. PrepareUpdate**:
- **Mô tả**: Phải được gọi bởi UCM sau khi State Management cho phép cập nhật. State Management sẽ từ chối nếu StartUpdateSession chưa được gọi thành công trước đó.
- **FireAndForget**: false
- **Parameters**:
  - **FunctionGroupList** (IN): Danh sách FunctionGroups trong SoftwareCluster cần chuẩn bị cập nhật
- **Application Errors**:
  - **kRejected**: Yêu cầu bị từ chối
  - **kPrepareFailed**: Bước chuẩn bị thất bại

**3. VerifyUpdate**:
- **Mô tả**: Phải được gọi bởi UCM sau khi State Management cho phép cập nhật và bước chuẩn bị đã hoàn thành. State Management sẽ từ chối nếu PrepareUpdate chưa được gọi thành công.
- **FireAndForget**: false
- **Parameters**:
  - **FunctionGroupList** (IN): Danh sách FunctionGroups trong SoftwareCluster cần verify
- **Application Errors**:
  - **kRejected**: Yêu cầu bị từ chối
  - **kVerifyFailed**: Bước verify thất bại

**4. PrepareRollback**:
- **Mô tả**: Phải được gọi bởi UCM sau khi State Management cho phép cập nhật (khi cần rollback).
- **FireAndForget**: false
- **Parameters**:
  - **FunctionGroupList** (IN): Danh sách FunctionGroups trong SoftwareCluster cần rollback
- **Application Errors**:
  - **kRejected**: Yêu cầu bị từ chối
  - **kRollbackFailed**: Bước rollback thất bại

**5. ResetMachine**:
- **Mô tả**: Yêu cầu reset máy. Trước khi reset, tất cả thông tin trong máy phải được persist. Yêu cầu sẽ bị từ chối nếu StartUpdateSession chưa được gọi thành công.
- **FireAndForget**: false
- **Application Errors**:
  - **kRejected**: Yêu cầu bị từ chối

**6. StopUpdateSession**:
- **Mô tả**: Phải được gọi bởi UCM khi cập nhật hoàn thành để thông báo State Management rằng cập nhật đã xong và máy ở trạng thái ổn định. Yêu cầu sẽ bị từ chối nếu StartUpdateSession chưa được gọi thành công.
- **FireAndForget**: true (Không chờ response)

**Ví dụ sử dụng**:
```cpp
// UCM code
auto update_proxy = /* get proxy to UpdateRequest service */;

// 1. Bắt đầu phiên cập nhật
auto result = update_proxy->StartUpdateSession().Get();
if (result.HasValue()) {
    // 2. Chuẩn bị cập nhật
    FunctionGroupList fgs = {"FG1", "FG2"};
    auto prep_result = update_proxy->PrepareUpdate(fgs).Get();
    
    if (prep_result.HasValue()) {
        // 3. Thực hiện cập nhật thực tế (UCM logic)
        performActualUpdate();
        
        // 4. Verify cập nhật
        auto verify_result = update_proxy->VerifyUpdate(fgs).Get();
        
        if (verify_result.HasValue()) {
            // 5. Hoàn thành
            update_proxy->StopUpdateSession();
        } else {
            // Rollback
            update_proxy->PrepareRollback(fgs).Get();
            revertChanges();
        }
    }
}
```

#### 6.3.5. Application Interaction - PowerMode

**Service Interface** [SWS_SM_91020]:
- **Tên**: PowerMode
- **Namespace**: ara::sm
- **Mô tả**: Được sử dụng bởi mọi Adaptive Application để cho phép State Management đạt được hành vi đồng bộ của tất cả applications

**Methods**:

**1. message**:
- **Mô tả**: Gửi PowerModeMsg (định nghĩa trong 9.1) đến tất cả Processes để yêu cầu chuyển PowerMode
- **Parameters**:
  - **msg** (OUT): Message gửi đến tất cả Processes để yêu cầu vào state này
  - **Type**: PowerModeMsg

**2. event**:
- **Mô tả**: Tất cả Processes nhận được yêu cầu PowerMode gửi message này làm câu trả lời cho State Management
- **Parameters**:
  - **respMsg** (OUT): ResponseMessage từ Process nhận được yêu cầu PowerMode
  - **Type**: PowerModeRespMsg

**Cách sử dụng trong Adaptive Application**:
```cpp
// Application skeleton (nhận yêu cầu từ State Management)
class MyAppPowerMode : public ara::sm::skeleton::PowerMode {
public:
    // Override method để nhận PowerMode request
    void ReceivePowerModeRequest(PowerModeMsg msg) override {
        PowerModeRespMsg response;
        
        if (msg == "Off") {
            // Chuẩn bị cho shutdown
            persistData();
            cleanupResources();
            response = PowerModeRespMsg::Done;
        } else if (msg == "Suspend") {
            // Chuẩn bị cho suspend
            prepareSuspend();
            response = PowerModeRespMsg::Done;
        } else if (msg == "On") {
            // Trở về hoạt động bình thường
            resumeNormalOperation();
            response = PowerModeRespMsg::Done;
        } else {
            response = PowerModeRespMsg::NotSupported;
        }
        
        // Gửi response về State Management
        SendResponse(response);
    }
};
```

#### 6.3.6. Application Interaction - DiagnosticReset

**Service Interface** [SWS_SM_91015]:
- **Tên**: DiagnosticReset
- **Namespace**: ara::sm

**Methods**:

**1. message**:
- **Mô tả**: Gửi DiagnosticResetMsg (định nghĩa trong 9.1) đến tất cả Processes trong một SoftwareCluster
- **Parameters**:
  - **Msg** (OUT): Message gửi đến tất cả Processes để yêu cầu thực hiện softReset
  - **Type**: DiagnosticResetMsg

**2. event**:
- **Mô tả**: Tất cả Processes nhận được yêu cầu DiagnosticReset gửi message này làm câu trả lời
- **Parameters**:
  - **respMsg** (OUT): ResponseMessage từ Process nhận được yêu cầu
  - **Type**: DiagnosticResetRespMsg

**Cách sử dụng trong Adaptive Application**:
```cpp
// Application skeleton
class MyAppDiagReset : public ara::sm::skeleton::DiagnosticReset {
public:
    void ReceiveDiagnosticResetRequest(DiagnosticResetMsg msg) override {
        DiagnosticResetRespMsg response;
        
        if (msg == "SoftReset") {
            // Thực hiện soft reset nội bộ
            bool success = performInternalReset();
            
            if (success) {
                response = DiagnosticResetRespMsg::Done;
            } else {
                response = DiagnosticResetRespMsg::Failed;
            }
        } else {
            response = DiagnosticResetRespMsg::NotSupported;
        }
        
        SendResponse(response);
    }
    
private:
    bool performInternalReset() {
        // Reset internal state
        resetDataStructures();
        reinitializeModules();
        return true;
    }
};
```

### 6.4. Required Service Interfaces (Giao Diện Dịch Vụ Được Yêu Cầu)

#### 6.4.1. Network Management - NetworkState

**Port** [SWS_SM_91004]:
- **Tên**: NetworkState_{NetworkHandle}
- **Loại**: RequiredPort
- **Interface**: NetworkState
- **Mô tả**: Cung cấp thông tin về trạng thái mạng cho mỗi NetworkHandle. Chỉ được sử dụng bởi State Management!
- **Variation**: FOR NetworkHandle : MODEL.filterType("NetworkHandle");

**Ý nghĩa**: 
- State Management cần interface này để đọc trạng thái mạng
- Mỗi NetworkHandle có một port riêng

### 6.5. Application Errors (Lỗi Ứng Dụng)

**Application Error Domain** [SWS_SM_91010]:

| Tên | Code | Mô tả |
|-----|------|-------|
| kRejected | 5 | Yêu cầu bị từ chối do trạng thái nội bộ của State Management/máy |
| kVerifyFailed | 6 | Bước verify của cập nhật thất bại |
| kPrepareFailed | 7 | Bước chuẩn bị của cập nhật thất bại |
| kRollbackFailed | 8 | Bước rollback của cập nhật thất bại |

---

## 7. CÁC VÍ DỤ THỰC TẾ

Phần này giúp bạn hiểu cách State Management hoạt động trong thực tế thông qua các kịch bản cụ thể. Các ví dụ dưới đây được thiết kế để dễ hiểu, chi tiết từng bước.

---

**HẾT TÀI LIỆU GIẢI THÍCH CHI TIẾT VỀ AUTOSAR STATE MANAGEMENT**

---

**Tóm tắt nội dung chính**:

1. **State Management** là thành phần trung tâm điều phối trong AUTOSAR Adaptive Platform
2. Quản lý **Machine State** (Startup, Shutdown, Restart) và **Function Group States**
3. Tương tác với nhiều module khác: Execution Management, PHM, Diagnostics, UCM, Network Management
4. Cung cấp **Service Interfaces** qua ara::com: TriggerIn, TriggerOut, TriggerInOut, UpdateRequest, PowerMode, DiagnosticReset
5. Hỗ trợ **PowerModes** để xử lý late wakeup scenarios
6. Hỗ trợ **Diagnostic Reset** qua CommunicationGroups
7. Ngăn chặn shutdown khi có diagnostic/update sessions
8. Hỗ trợ môi trường ảo hóa/phân cấp

**Tất cả các khái niệm, chức năng, interface, và requirements từ tài liệu gốc đã được giải thích chi tiết bằng tiếng Việt với ví dụ minh họa.**