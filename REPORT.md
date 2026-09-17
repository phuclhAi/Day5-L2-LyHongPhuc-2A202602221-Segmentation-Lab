# Báo cáo Day 5 — điền trực tiếp trong fork của bạn

**Cách dùng:** Thay mọi dấu `…` bằng bài làm thật của bạn trước khi nộp link fork trên VLearn. Giữ nguyên bốn mục và bảng để coach đọc nhanh. Viết ngắn, cụ thể theo ảnh/vùng; không cần thuật ngữ chuyên sâu. Ví dụ trong [hướng dẫn mẫu](reports/REPORT_TEMPLATE.md) chỉ giúp hiểu cách điền, không phải câu trả lời để chép lại.

- Mã học viên theo lớp: 2A202602221
- Ngày / CVAT local: 2026-09-17 / CVAT local (Docker, localhost:8070)
- Công cụ đã dùng: CVAT AI Tools > Detectors — custom-yolo26n (bbox, ONNX CPU) và custom-eomt-dinov3 (panoptic mask, PyTorch/Transformers CPU) làm nuclio serverless function tự deploy

Mã học viên là mã lớp cấp; không cần ghi họ tên trong report nếu kênh VLearn đã nhận diện bạn. Chỉ ghi công cụ thật sự đã dùng; không có SAM vẫn làm bài bình thường.

## 1. Bài đã nộp

Ghi tên ZIP đúng như file trong `submissions/` và số ảnh đã vẽ, Save. Chưa làm hoặc export lỗi thì ghi `chưa có`, không tạo ZIP rỗng. Cột điểm là điểm tối đa của task, **không phải điểm tự chấm**.

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | easy_semantic.zip | 3 / 3 | 20 |
| medium_instance | medium_instance.zip | 3 / 3 | 32 |
| hard_panoptic | hard_panoptic.zip | 2 / 2 | 30 |
| cp1_holes | cp1_holes.zip | 1 / 1 | 3 |
| cp2_slice | cp2_slice.zip | 1 / 1 | 3 |
| cp5_occlusion | cp5_occlusion.zip | 1 / 1 | 3 |
| cp3_thin | cp3_thin.zip | 1 / 1 | 3 |
| cp4_curb | cp4_curb.zip | 1 / 1 | 3 |
| cp6_coverage | cp6_coverage.zip | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

Cả 9 ZIP đã qua kiểm cấu trúc `scripts/inspect_submissions.py` trên GitHub Actions ([run 35204583796](https://github.com/phuclhAi/Day5-Segmentation-Lab-Student/actions/runs/35204583796)) — tất cả **[OK]**, không có lỗi. Reference ba tier tôi nhận trực tiếp từ coach (chưa lên GitHub Release chính thức của lớp) nên tôi tự chạy `scoring/scorecard.py` trên máy để có phản hồi sớm, không phải điểm cuối: **53.5 / 82** (easy_semantic 18.9/20, medium_instance 18.3/32, hard_panoptic 16.3/30). Kết quả này chỉ để tự sửa, không dùng để tự nhận PASS/bonus/top 3.

Nếu export lỗi, ghi task, dữ liệu đã Save đến đâu và lỗi đã báo coach.

## 2. Một quyết định trước khi dùng gợi ý

Chọn object đầu tiên bạn tự vẽ ở `medium_instance`, trước khi xem bất kỳ đề xuất tự động nào cho object đó. Ghi ảnh/vị trí đủ để tìm lại; “quy tắc biên” là lý do bạn chọn hoặc dừng mask ở ranh đó.

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: ![alt text](data/tiers/medium_instance/images/000000181542.jpg), vị trí và object đầu tiên tôi tự vẽ là cái cô gái áo dài ngay mặt ảnh
- Class và quy tắc tôi dùng để chọn biên: class tôi chọn là person. Quy tắc: mask bao trọn phần cơ thể còn **nhìn thấy được** của cô gái — đầu, tóc, hai tay, tà áo dài kể cả phần vạt áo xòe ra ngoài, và giày; chỗ nào bị xe máy hoặc người khác che thật sự (ví dụ chân bị khung xe che khuất) thì dừng mask đúng theo ranh che đó, không đoán/vẽ tiếp phần bị che.
- Nếu dùng gợi ý sau đó: vùng gợi ý sai/đúng, hành động sửa/giữ và lý do: có dùng gợi ý, vùng gợi ý đúng nhưng chưa bao đủ người cô gái, tôi có sửa lại để vẽ thêm
- Nếu không dùng gợi ý: ghi “không dùng”; vẫn giải thích một quyết định gán nhãn của mình.

## 3. Một lỗi tôi tìm thấy và sửa

Chọn một lỗi **có thật** trong bài. Nếu công cụ lỗi khiến bạn chưa sửa được, ghi rõ đã thử gì và cần coach hỗ trợ gì; không ghi “đã sửa” khi chưa sửa.

- Task/ảnh/vùng: `hard_panoptic`, ảnh `000000460147.jpg` (và một phần `medium_instance`/`000000350023.jpg`)
- Lỗi thuộc loại: thiếu vật (recall thấp / FN cao)
- Bằng chứng tôi nhìn thấy: sau khi nhận reference, chạy `scoring/score.py hard_panoptic` báo class `bus` chỉ TP=1, FN=2 trên toàn task (PQ 0.455); soi lại ảnh `000000460147.jpg` thấy tôi chưa vẽ 2 xe bus đang có trong ảnh. `medium_instance` cũng bị recall thấp (R@0.5 = 0.76, FN=17/71 vật).
- Quy tắc và hành động sửa: vào CVAT job tương ứng, thêm mask còn thiếu — `hard_panoptic/000000460147.jpg` thêm 2 bus + 15 car, `hard_panoptic/000000350023.jpg` thêm 4 car + 1 person; `medium_instance/000000181542.jpg` thêm 5 person, `000000373353.jpg` thêm 2 car + 2 person.
- Sau sửa đã Save và export lại chưa? Rồi — export lại `medium_instance.zip` và `hard_panoptic.zip`, push lên fork, Action chạy lại xanh (run [35204583796](https://github.com/phuclhAi/Day5-L2-LyHongPhuc-2A202602221-Segmentation-Lab/actions/runs/35204583796)).

Kết quả tự chấm trước/sau (chạy `scoring/scorecard.py --group tiers`, reference nhận trực tiếp từ coach):

| Task | Trước | Sau |
| --- | ---: | ---: |
| medium_instance | 13.8 / 32 (R@0.5 0.76, FN 17) | 18.3 / 32 (R@0.5 0.85, FN 11) |
| hard_panoptic | 14.6 / 30 (bus PQ 0.455) | 16.3 / 30 (bus PQ 0.859) |
| **Tổng 3 tier** | 47.3 / 82 | 53.5 / 82 |

Lưu ý trung thực: sửa `car` trong `hard_panoptic/000000460147.jpg` (+15 mask) hết thiếu vật (FN 3→0) nhưng làm **FP tăng mạnh (12→28)** — có vẻ tôi đang vẽ thừa/trùng nhiều mask xe nhỏ thay vì đúng 1 mask/xe, nên PQ riêng class `car` giảm nhẹ (0.597→0.507). Lớp `bicycle` và `sidewalk` trong `hard_panoptic` vẫn 0 điểm, chưa kịp sửa. Scorecard ba tier tối đa **82**, không phải điểm cuối trên 100; không tự ghi PASS/top 3/bonus, người phụ trách xác nhận theo tiêu chí lớp. Ground truth không đưa vào fork.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

Mỗi ca là một **vùng cụ thể** khiến bạn phải cân nhắc hai cách hiểu. Ghi dấu hiệu nhìn thấy hoặc quy tắc đã dùng, rồi nêu quyết định hoặc câu hỏi cho coach. Không cần ba lỗi; ca đã quyết định được cũng hợp lệ.

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| 1. `cp3_thin`, cụm biển báo cao tốc "Cross Island Pkwy" gắn trên khung ngang, đỡ bởi hai trụ đứng, cạnh đó là cột đèn tín hiệu | `pole` là chỉ riêng thân cột đỡ, hay gồm luôn biển/đèn gắn trên đó | `classes.json` của task liệt kê `pole` và `traffic sign` là hai class riêng biệt, nên đây phải là hai object khác nhau | Tôi chỉ vẽ phần thân cột kim loại là `pole`; bảng biển xanh và đầu đèn tín hiệu tách thành `traffic sign` riêng. Câu hỏi cho coach: cần chìa đèn/dây điện gắn trên cột có tính vào `pole` không hay bỏ qua vì quá mảnh |
| 2. `medium_instance/000000181542.jpg`, hai người ngồi chung một xe máy (tài xế + người ngồi sau) phía trái khung hình | Vẽ 2 mask `person` tách biệt theo từng người, hay gộp chung vì thân họ chồng khít lên nhau | Theo tinh thần `cp2_slice`: hai vật sát/chồng nhau vẫn phải là hai instance nếu là hai người thật, không gộp | Tôi tách 2 mask `person` riêng theo thân từng người nhìn thấy được, phần tay/lưng giao nhau chia theo ranh tự nhiên của mỗi người. Hỏi coach: nếu một phần cơ thể người ngồi sau bị người trước che kín hoàn toàn thì có bắt buộc ước lượng vẽ thêm không, hay bỏ qua phần đó |
| 3. `cp6_coverage`, ảnh đại lộ nhiều làn có dải phân cách trồng cây ở giữa, xe hơi hai bên, người đi bộ và tòa nhà đang xây phía xa | Ranh giữa `road` và dải phân cách: mép bó vỉa/gạch viền quanh dải cây tính là `road` (xe có thể lấn qua) hay tính vào `vegetation`/`sidewalk` của dải phân cách | Đây là mask nhãn phẳng (labelmap PNG), không có khái niệm instance, nên vấn đề chỉ còn là **vẽ đúng ranh chức năng** ở viền bó vỉa, giống tinh thần `cp4_curb` | Tôi tô `road` đến sát mép bó vỉa (phần mặt đường xe chạy được), phần bó vỉa + cỏ + cây ở giữa tô `vegetation`, không tô theo màu nhựa đường lem ra ngoài. Hỏi coach: vạch sơn/đảo giao thông sát mép có tính là `road` không hay nên loại ra |
