# Báo cáo Day 13 — Lê Đức Thanh, 2A202603328

## 1. Kết luận trước
Mã số `2A202603328`, task `D13-2A202603328`, 10 frame: s01f23, s01f47, s02f16, s02f51, s03f02, s03f47, s04f22, s04f44, s05f22, s05f37. Windows 11 Home, trình duyệt Google Chrome. CVAT Server 2.83.0, UI 2.83.0. MSSV bạn ngồi cạnh: 2A202603087.

Tổng 56 cuboid dạng Shape trên 10 frame: 49 Car, 3 Bus, 2 Truck, 2 Cyclist; 26 cuboid bật occluded. Frame đầu s01f23 có 8 cuboid; frame ít nhất là s03f47 với 4 cuboid, do có quá ít xe nhìn thấy rõ, hoặc chỉ thấy một phần, đủ cơ sở để gán nhãn. `selfcheck` lần chạy cuối cờ 0/56 cuboid (lần chạy đầu cờ 12/56, chủ yếu xe con rộng hơn 2,3 m). Phần mình tin nhất là đáy và hướng: z đáy của mọi cuboid từ −2,15 đến −1,61 m, `ground_gap_m` từ −0,18 đến +0,27 m; xe cùng chiều phần lớn có yaw 266–274°, xe ngược chiều 88,6–92,1°. Phần yếu nhất là kích thước và class: 33/49 hộp Car rộng hơn 2,10 m (mức trên của bảng 4.3), và class của xe lớn ở s05f37 chưa chắc. Việc đọc guideline chưa kỹ, hoặc guideline chưa rõ ở một số ca, ảnh hưởng đến việc chọn class và phỏng đoán phần bị che; khi guideline đầy đủ và có đủ thông tin cơ sở thì có thể gán nhãn chính xác hơn.

Cách dùng CVAT:
- Góc nhìn: kéo chuột trái để xoay khung 3D tới khi nhìn thẳng từ trên xuống, kéo chuột phải để dời, lăn con lăn để phóng to, thu nhỏ; đổi camera bằng biểu tượng bánh răng trên khung ảnh.
- Vẽ và chỉnh: vẽ cuboid bằng **Shape**, nháy đúp để đặt hộp; các hộp sau cùng label chỉ cần bấm `N`. Chỉnh hộp ở ba khung Top, Side, Front, ưu tiên khung dễ nhìn trước rồi tới khung tiếp theo; ở Side, Front chỉ chỉnh đáy và chiều cao, không xoay. Cần đọc kỹ guideline trước khi gán (tên class và hướng xe).
- Hướng và ảnh camera: bật **Appearance → Cuboid orientation**, căn vạch đỏ `+X` về đầu xe theo ảnh camera. CVAT 2.83 không chiếu hộp lên ảnh camera, nên xem cuboid trên 7 camera bằng `python scripts/lab.py preview` (ảnh trong `work/xem/`).

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ. Ảnh: `t1-s01f23-3d.png`, `t1-s01f23-top.png`, `t1-s01f47-3d.png`, `t1-s01f47-top.png`.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f23 | Phía trước (−y) ở cạnh trên; +x chỉ sang cạnh trái (bên trái xe), +y chỉ xuống cạnh dưới (ra sau xe) | Phẳng; thấp hơn LiDAR khoảng 1,7–1,8 m (z đáy các xe: −1,68 đến −1,78 m) | ~19,7 m (xe con phía sau, cuboid #6) | Xe con, xe buýt, xe hai bánh có người | Sau-trái: xe buýt ~13,9 m; sau-phải: xe con ~6,7 m; phía sau: xe con ~19,7 m; bên trái: xe con ~6,7 m | Một cụm ít điểm ở xa, không đủ để biết là xe, người hay vật bên đường |
| s01f47 | Phía trước (−y) ở cạnh trên; +x chỉ sang cạnh trái (bên trái xe), +y chỉ xuống cạnh dưới (ra sau xe) | Phẳng gần xe (z đáy −1,76 đến −1,82 m); phía sau ~30 m thấp hơn khoảng 0,2 m | ~30,5 m (xe con phía sau, cuboid #5) | Xe con, xe tải | Phía sau: xe con ~7,3 m và ~30,5 m | Xe tải phía trước bị 3 xe con che trên cam phía trước, chỉ thấy một phần |

Chỉ camera cho biết: màu và kiểu thân xe, để tách xe buýt với xe tải (xe buýt sau-trái ở s01f23, xe tải phía trước-phải ở s01f47); xe hai bánh có người đang lái hay chỉ dựng bên đường; đầu hay đuôi xe (đèn trước, đèn hậu) để đặt hướng. Chỉ LiDAR cho biết: khoảng cách thật tới từng xe (xe buýt ở s01f23 cách ~13,9 m); độ cao mặt đường so với cảm biến (~−1,7 m); các xe phía sau và hai bên vẫn có điểm đủ 360°, kể cả khi bị che một phần trên camera như xe tải ở s01f47.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| s02f16 | Cạnh trên | Không có |
| s02f51 | Cạnh trên | Không có |
| s03f02 | Cạnh trên | Không có |
| s03f47 | Cạnh trên | Không có |
| s04f22 | Cạnh trên | Không có |
| s04f44 | Cạnh trên | Không có |
| s05f22 | Cạnh trên | Không có |
| s05f37 | Cạnh trên | Không có |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | s01f23, #1 | Car | yaw 270,8°: cùng chiều xe thu dữ liệu | Có: z đáy −1,78 m, `ground_gap_m` 0,01 m | Có: Top, Side, Front và ảnh camera | Dài 4,89 m, cao 1,56 m trong khoảng sedan; rộng 2,16 m vượt mức 2,10 m của SUV |
| (b) xe buýt hoặc xe tải | s01f23, #4 | Bus | yaw 269,6°: cùng chiều | Có: z đáy −1,72 m, `ground_gap_m` 0,03 m | Có: Top, Side, Front và ảnh camera | Dài 12,91 m (trên 12,5 m), rộng 3,08 m (trên 2,60 m), cao 3,58 m (trên 3,50 m): cả ba chiều vượt bảng 4.3, rộng vượt nhiều nhất |
| (c) người đi bộ hoặc xe hai bánh | s01f23, #7 | Cyclist | yaw 27,8° | Có: z đáy −1,66 m, `ground_gap_m` 0,12 m | Có: Top, Side, Front và ảnh camera | 2,00 × 0,92 × 1,64 m: trong khoảng xe máy có người |

Vòng hiệu chuẩn: mình không tham gia vòng hiệu chuẩn trong task chung `D13-HC485087`, nên không có số liệu so với các bạn. Vật bỏ qua vì không đủ cơ sở: không có.

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | s02f00 trong task chung `D13-HC485087`; `t4-1-bong-che.png` | Xe buýt đỗ sát bên trái chắn tia laser, nên tia không tới được vùng phía sau nó | Sau xe buýt đỏ bên trái (~4 m) gần như không có điểm, trong khi hai bên vùng đó vẫn dày điểm. Vùng trống là chưa quan sát được, không phải đường trống | Bật occluded cho vật bị che một phần, cuboid bao phần suy luận được; không kết luận vùng trống là không có vật |
| 2. Thưa ở xa | s04f44, Car #4 (~35,5 m, phía sau); `t4-2-thua-o-xa.png` | LiDAR 40 tia tỏa ra theo góc, nên càng xa khoảng cách giữa các tia càng lớn và số điểm rơi trên vật càng ít | Hộp chỉ có 107 điểm, so với 3367 điểm ở xe con cách 3,6 m (s05f22 #3) | Vẽ nếu thấy khoảng 15–20% thể tích hoặc suy luận được; không thì bỏ qua, ghi LOW_VISIBILITY |
| 3. Phản xạ thấp / ít điểm bất thường | s03f02, Car #1 (~3,8 m, phía trước-trái, không bị che); `t4-3-phan-xa-thap.png` | Hai giả thuyết: thân xe tối màu hoặc nhiều kính nên ít tia phản hồi về cảm biến; hoặc xe quá gần nên một phần thân nằm ngoài góc quét dọc của LiDAR | Hộp chỉ có 2197 điểm, trong khi xe con ở tầm tương tự có 3367 điểm (s05f22 #3, 3,6 m) và 3106 điểm (s01f23 #1, 4,3 m) | Ghi giả thuyết, không khẳng định nguyên nhân; hộp vẫn vẽ theo hình dạng xe nhận ra trên point cloud và ảnh camera |
| 4. Điểm lạ | s02f35 trong task chung `D13-HC485087`; `t4-4-diem-la.png` | Tia phản xạ nhiều lần (ví dụ qua kính hoặc mặt kim loại) làm cảm biến tính sai khoảng cách | Cụm khoảng 46 điểm phía sau bên trái, cách ~29 m, ở z ≈ −13,5 m, tức thấp hơn mặt đường quanh đó ~11 m; phải xoay nghiêng khung 3D mới thấy | Không gán thành vật |

## 5. Số từ cuboid của tôi (Bước 5)
Lấy từ `submission/D13-2A202603328-bangA.csv`; số điểm lấy từ cột `points_in` của `-tukiem.csv`. Mặt đường khoảng −1,7 m: z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| s01f23, #1, Car | −1,00 m | 1,56 m | −1,78 m | 4,34 m | 4,46 m | 270,8° | Chạm đường (`ground_gap_m` 0,01 m) |
| s05f22, #1, Car | −1,27 m | 1,70 m | −2,12 m | 31,62 m | 31,64 m | 89,3° | Thấp hơn −1,7 m khoảng 0,42 m, nhưng `ground_gap_m` chỉ 0,04 m: mặt đường quanh xe ở xa thấp hơn, hộp vẫn chạm đường |
| s01f23, #8, Cyclist | −0,98 m | 1,98 m | −1,97 m | 16,88 m | 16,91 m | 2,1° | Chìm nhẹ: đáy thấp hơn mặt đường quanh hộp 0,18 m |

(a) Cuboid gần nhất: s05f22 #3 (Car), `dist_m` 3,64 m, r 3,75 m. Cuboid xa nhất: s05f37 #5 (Bus), `dist_m` 37,47 m, r 37,48 m. (b) Xe hai bánh s01f23 #7: 2,00 × 0,92 × 1,64 m, cả ba chiều trong khoảng xe máy có người. Xe hai bánh s01f23 #8: 1,46 × 1,08 × 1,98 m, dài ngắn hơn, rộng và cao hơn khoảng xe máy, có thể do hướng hộp lệch (yaw 2,1° trong khi #7 cạnh đó là 27,8°) hoặc hộp bao cả người. Xe con s01f23 #1: 4,89 × 2,16 × 1,56 m, dài và cao trong khoảng sedan, rộng vượt 2,10 m, có thể do bao cả gương hoặc còn dư hai bên. (c) Xe cùng chiều phía trước s01f47 #2 (`az_deg` 0,4°): yaw 270,0°, đúng gần 270°. Xe ngược chiều phía trước-trái s02f16 #5 (`az_deg` 19,4°): yaw 89,7°, đúng gần 90°.

Mật độ theo khoảng cách (4 cuboid ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| s05f22, #3, Car | 3,64 m | 3367 |
| s02f16, #3, Car | 12,58 m | 1016 |
| s02f51, #4, Car | 22,97 m | 254 |
| s04f44, #4, Car | 35,52 m | 107 |

Xa gấp khoảng 1,8 lần (12,6 → 23,0 m) thì số điểm còn khoảng 1/4 (1016 → 254), gần quy luật số điểm giảm theo bình phương khoảng cách. So với đường cong ở slide (xe con, trung vị 1968 / 718 / 214 / 72 điểm ở 0–10 / 10–20 / 20–30 / 30–40 m), bốn hộp của mình cùng xu hướng và cùng bậc (3367 / 1016 / 254 / 107); hộp gần cao hơn trung vị vì xe chỉ cách 3,6 m.

Tự kiểm: lần `sync` đầu cờ 12/56 cuboid. Đã sửa trên CVAT: s03f02 #8 đổi class Car → Truck (rộng 2,53 m, cao 2,41 m khớp xe tải hộp trong bảng 4.3); s05f37 #5 (Bus) kéo dài từ 5,13 m lên 9,73 m và nâng nóc từ 2,27 m lên 2,37 m để bao phần xe bị che suy luận được, đang bật occluded; s01f47 #5 (Car) rút chiều dài từ 6,32 m xuống 4,78 m; 9 xe con thu hẹp chiều rộng xuống dưới 2,3 m (s02f16 #6, s02f51 #3, #5, s04f22 #4, s04f44 #1, #3, #4, s05f22 #3, s05f37 #2). Lý do sửa: ở khung Top, hộp rộng hơn cụm điểm, hai cạnh dọc còn khoảng trống so với điểm của xe nên kéo vào sát; với s01f47 #5, hai đầu hộp dư so với cụm điểm nên rút ngắn lại. Lần `sync` cuối không còn cờ nào, nên không có cảnh báo giữ nguyên.

Đổi kiểm với 2A202603087: bạn ấy đối chiếu `dist_m` và z đáy của một cuboid trong bảng mục 5 với bảng A mở trên màn hình máy mình; cả hai con số khớp, không phải sửa.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
(a) Model 3D dùng nhãn để học vị trí và kích thước vật trong không gian thật quanh xe, phục vụ đo khoảng cách, tránh va chạm và dự đoán đường đi, nên nhãn phải đo bằng mét trong hệ tọa độ của xe; kích thước theo pixel đổi theo khoảng cách và theo từng camera, cùng một xe ở gần và ở xa có kích thước pixel rất khác nhau. Vật thưa điểm chỉ còn vài chục điểm (xe ở 35,5 m chỉ có 107 điểm), model không học được hình dạng từ một cụm mơ hồ; nếu không có quy tắc chung (vẽ khi thấy khoảng 15–20% thể tích, không thì bỏ qua và ghi LOW_VISIBILITY) thì mỗi người gán một kiểu, nhãn không nhất quán và model học cả những hộp đoán.

(b) Dùng AI (giảng viên cho phép dùng AI điền báo cáo; mình review lại toàn bộ):
- Công cụ: Claude Code (Claude Opus 5.5) chạy trên máy.
- Câu hỏi chính: cài `.venv` và thư viện; nâng CVAT local từ 2.74.1 lên 2.83.0 và gỡ lỗi OPA health check; cách chạy `lab.py`, `check_submission.py`; ý nghĩa các cờ `selfcheck` và bảng 4.3. AI sửa tiêu đề README, đổi tên báo cáo.
- Dùng kết quả thế nào: mình tự vẽ và sửa mọi cuboid trên CVAT, tự ghi các quan sát trực tiếp (mặt đường, vật phía sau, cụm bị che ở s01f47, điều lạ ở các frame lướt, ảnh nhìn từ trên xuống, vòng hiệu chuẩn, đổi kiểm) và đọc lại để sửa phần AI viết. AI không xem ảnh camera, ảnh chụp màn hình, `work/xem/` hay dữ liệu trong `data/data-VF/`.

(c) Điều còn chưa chắc: 33/49 hộp xe con rộng hơn 2,10 m, có thể còn bao dư hai bên dù `selfcheck` không còn cờ; s05f37 #1 (Bus, dài 8,63 m) và #5 (Bus, cao 2,37 m) ngắn hoặc thấp hơn xe buýt thường trong bảng 4.3, có thể là minibus hoặc class khác (UNCERTAIN_CLASS); s01f23 #3 có yaw 243,6°, lệch khoảng 26° so với các xe cùng chiều; s01f23 #8 (Cyclist) có kích thước ngoài bảng 4.3.
