# Lab2_FAP201 - Lighting an LED with Logic Gates

## Thông tin chung
* **Môn học:** FAP201 - FPGA Programming[cite: 15]
* **Bài thực hành:** Lab02 - Lighting an LED with Logic Gates (CLO1 & CLO2)[cite: 15]
* **Mục tiêu:** Thiết kế, kiểm chứng và hiện thực mạch logic sử dụng cổng AND tổ hợp để điều khiển LED thông qua 2 nút bấm/switch trên bo mạch FPGA[cite: 14, 15].

## Môi trường và Phần cứng
* **Công cụ phát triển:** Gowin EDA[cite: 13, 15]
* **Bo mạch (Board):** Xiaomeige ACG525[cite: 13]
* **Chip FPGA (Target Device):** GW5A-LV25UG324C2/I1[cite: 13]
* **Ngôn ngữ:** VHDL (IEEE 1164 STD_LOGIC)[cite: 14, 19]

## Nguyên lý hoạt động & Bảng chân lý
* **Mạch tổ hợp (Combinational Circuit):** Biểu thức logic `o_led <= i_sw1 and i_sw2;` được thực hiện trực tiếp bằng tài nguyên Look-Up Table (LUT) trên FPGA, không sử dụng xung nhịp (clock), thanh ghi (register) hay máy trạng thái (FSM)[cite: 14, 15, 16].
* **Phân tích cực tính phần cứng (Active-Low Hardware):**
  * **Công tắc ngõ vào (`i_sw1`, `i_sw2`):** Mặc định khi thả nút ra là mức logic `'1'` (nhờ cấu hình `PULL_MODE=UP`), khi nhấn nút xuống mang mức logic `'0'`[cite: 13, 14].
  * **Đèn LED ngõ ra (`o_led`):** Đèn TẮT ở mức logic `'1'` và SÁNG ở mức logic `'0'`[cite: 14].
  * **Logic vận hành:** Phép toán `1 AND 1 = 1` (Đèn TẮT khi chưa bấm nút nào)[cite: 14]. Đèn chỉ SÁNG (ngõ ra `'0'`) khi cả hai nút đều được nhấn hoặc có ít nhất một nút bấm kéo ngõ vào về `'0'`[cite: 14].

### Bảng chân lý (4 Test Cases)
| i_sw1 (KEY0) | i_sw2 (KEY1) | o_led (LED0) | Trạng thái vật lý |
| :---: | :---: | :---: | :--- |
| `1` (Thả) | `1` (Thả) | `1` | LED TẮT[cite: 14] |
| `1` (Thả) | `0` (Nhấn) | `0` | LED SÁNG[cite: 14] |
| `0` (Nhấn) | `1` (Thả) | `0` | LED SÁNG[cite: 14] |
| `0` (Nhấn) | `0` (Nhấn) | `0` | LED SÁNG[cite: 14] |

## Bảng ánh xạ chân vật lý (Physical Constraints Mapping)
Tín hiệu được cấu hình trong file `constraints/Lab2.cst`[cite: 13, 15]:

| Tín hiệu VHDL | Chân FPGA | Linh kiện phần cứng | Cấu hình I/O (3.3V Bank VCCIO) |
| :--- | :---: | :--- | :--- |
| `i_sw1` | `B16` | Switch 1 / KEY0 | `PULL_MODE=UP`, `DRIVE=OFF`[cite: 13] |
| `i_sw2` | `A15` | Switch 2 / KEY1 | `PULL_MODE=UP`, `DRIVE=OFF`[cite: 13] |
| `o_led` | `D14` | Output LED / LED0 | `PULL_MODE=NONE`, `DRIVE=8mA`[cite: 13] |

## Cấu trúc thư mục dự án (Project Structure)
Kho lưu trữ được tổ chức theo tiêu chuẩn đánh giá của môn học[cite: 15]:

* `01_Boolean_LUT_Analysis.pdf`: Báo cáo phân tích hàm Boolean $Y = A \cdot B$, bảng chân lý và cấu trúc LUT[cite: 15].
* `src/Lab2.vhd`: Mã nguồn VHDL mô tả mạch tổ hợp[cite: 14, 15].
* `constraints/Lab2.cst`: File gán chân vật lý cho chip Gowin GW5A[cite: 13, 15].
* `sim/lab02_tb.vhd`: File Testbench kiểm thử 4 vector ngõ vào (`00`, `01`, `10`, `11`)[cite: 15, 18, 19].
* `waveform/`: Ảnh chụp hoặc file trace dạng sóng mô phỏng (`.ghw`)[cite: 15, 19].
* `reports/`: Báo cáo quá trình Tổng hợp (Synthesis) và P&R (Place & Route)[cite: 15].
* `evidence/`: Nhật ký Programmer và bằng chứng nghiệm thu 4 trường hợp trên bo mạch phần cứng[cite: 15].
* `README.md`: Tài liệu hướng dẫn dự án[cite: 15].

## Hướng dẫn Build & Kiểm chứng (Execution & Verification)
1. **Mô phỏng (Simulation):** 
   * Mở công cụ mô phỏng (GHDL / Gowin Simulator) và chạy testbench `sim/lab02_tb.vhd`[cite: 18, 19].
   * Đảm bảo cả 4 trường hợp tổ hợp ngõ vào đều vượt qua các câu lệnh `assert` và hiển thị kết quả đúng trên sóng dạng waveform[cite: 18, 19].
2. **Tổng hợp & Nạp phần cứng (Synthesis & Programming):**
   * Mở project trong Gowin EDA với target `GW5A-LV25UG324C2/I1`[cite: 13, 18].
   * Chạy quá trình **Synthesis** và **Place & Route**[cite: 15, 18].
   * Kết nối bo mạch Xiaomeige ACG525, sử dụng Gowin Programmer để nạp file bitstream (`.fs`) xuống chip[cite: 13, 15, 18].
   * Thao tác trực tiếp với KEY0 và KEY1 để đối chiếu kết quả thực tế trên LED0[cite: 13, 15, 18].
