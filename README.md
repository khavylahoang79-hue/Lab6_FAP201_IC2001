# Lab6_FAP201_IC2001
# Lab6_FAP201 – Memory Game with 74HC595 Display

## Thông tin chung
- **Môn học:** FAP201 - FPGA Programming
- **Bài thực hành:** Lab06 - Memory Game (FSM, LFSR Pseudo-Random Generator, Key Debounce & 74HC595 7-Segment Controller) (CLO1 & CLO2)[cite: 20, 22]
- **Tác giả:** La Hoàng Khả Vy (CE210538)
- **Mục tiêu:** Thiết kế, kiểm chứng và hiện thực trò chơi ghi nhớ chuỗi chớp đèn (Memory Game) 7 vòng chơi trên bo mạch FPGA[cite: 22]. Hệ thống tích hợp mạch lọc nhiễu nút nhấn, bộ sinh chuỗi ngẫu nhiên LFSR, FSM điều khiển luồng game và giao tiếp 3 dây nối tiếp với IC đệm 74HC595 để hiển thị điểm số/trạng thái trên LED 7 đoạn[cite: 20, 22].

## Môi trường và Phần cứng
- **Công cụ phát triển:** Gowin EDA[cite: 20]
- **Bo mạch (Board):** Xiaomeige ACG525[cite: 20]
- **Chip FPGA (Target Device):** GW5A-LV25UG324C2/I1[cite: 20]
- **Ngôn ngữ:** VHDL (IEEE 1164 STD_LOGIC, NUMERIC_STD)[cite: 22]

## Nguyên lý hoạt động & Phân tích Architecture
- **Kiến trúc các khối chức năng (Sub-modules Architecture):**
  1. **Lọc nhiễu & Đồng bộ nút nhấn (`key_conditioner`):** Đồng bộ hóa tín hiệu qua Flip-Flop và đếm chống dội (`G_STABLE_CYCLES = 1,000,000` chu kỳ ~ 20ms)[cite: 22]. Chỉ tạo duy nhất 1 xung tích cực (`press_pulse_o`) dài 1 chu kỳ clock đúng thời điểm người dùng **nhấn** nút, loại bỏ hoàn toàn nhiễu dội và sự kiện lặp khi giữ nút[cite: 22, 23].
  2. **Máy trạng thái FSM điều khiển Trò chơi (`lab06_game`):**
     * Quản lý 6 trạng thái: `IDLE`, `PATTERN_OFF`, `PATTERN_SHOW`, `WAIT_PLAYER`, `WIN`, `LOSE`[cite: 22].
     * **Bộ sinh ngẫu nhiên LFSR 8-bit (`lfsr_step`):** Sinh mẫu chớp đèn ngẫu nhiên 7 ký tự (mỗi ký tự dạng One-Hot 3-bit tương ứng với LED0, LED1 hoặc LED2)[cite: 22].
     * **Luồng chơi:** Người chơi phải ghi nhớ và bấm lại chính xác chuỗi LED hiển thị từ vòng 1 đến vòng 7[cite: 22]. Thắng cả 7 vòng $\rightarrow$ trạng thái `WIN`[cite: 22]. Bấm sai nút hoặc bấm đồng thời nhiều nút $\rightarrow$ trạng thái `LOSE`[cite: 22, 23].
  3. **Bộ điều khiển LED 7 đoạn qua 74HC595 (`sevenseg_74hc595`):**
     * Truyền dữ liệu nối tiếp 16-bit (8-bit mã segment + 8-bit chọn LED) qua 3 dây: Dữ liệu (`seg_ds_o`), Xung dịch (`seg_shcp_o`), Xung chốt (`seg_stcp_o`)
