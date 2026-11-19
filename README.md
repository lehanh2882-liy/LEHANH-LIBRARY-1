# Write & Shine 4: cách sinh bài hàng loạt

Bộ tài liệu được tách thành **template cố định** và **dữ liệu riêng cho từng unit**. Mỗi unit mới không phải viết lại HTML bằng tay.

```
template/   phần cố định, chép từ demo Unit 1 và Unit 6
  style.css     toàn bộ CSS (màu, font, giấy kẻ dòng, dark mode, bản in)
  page.html     khung trang (Jinja), giữ nguyên id s1…s6, sW, s7…s9 và các class
  core.js       engine gốc: kid(), owl(), medal(), speak(), register(), markItem(), diagnose(), …
  artlib.js     thư viện tranh dùng chung cho mọi unit: icon (IC), đồng hồ, lịch thứ/tháng ghi tiếng Việt,
                sách môn học, người, pic(spec), heroFrame()
  builders.js   dựng 10 phần bài tập từ dữ liệu, kèm bộ bắt lỗi chung
                (viết hoa, dấu câu, số viết bằng chữ, o'clock, forty-five, chữ I, …)
  tail.js       sao, huy chương, pháo giấy, ⚙️ Voice, nút 📄 PDF
units/u02/
  data.json     toàn bộ chữ và đáp án của unit (tương ứng các cột Excel ở mục 5)
  art.js        phần riêng của unit: heroArt(), modelArt() và unitAnalyse()
                (máy chấm 3 tiêu chí riêng của bài viết đoạn); icon mới thì thêm vào template/artlib.js
  tests.json    các câu sai mẫu và gợi ý phải hiện ra, dùng cho kiểm thử
tools/
  build.py       ghép template với dữ liệu thành HTML (có tùy chọn nhúng PDF)
  test_unit.py   kiểm thử tự động
  make_pdf.py    xuất phiếu bài tập và bản có đáp án (A4, nhúng font Andika và Patrick Hand)
  make_audio.py  xuất 4 file MP3 bằng Piper (Amy en-US dẫn, Southern English Female, Alan en-GB)
  make_excel.py  xuất bảng biến theo unit từ các file data.json
```

## Làm một unit mới (ví dụ Unit 3)
1. Chép `units/u02` thành `units/u03`, rồi sửa `data.json`, `art.js` và `tests.json`.
2. `python3 tools/build.py u03`
3. `python3 tools/test_unit.py "out/Write & Shine 4 – Unit 3_ ….html" units/u03/tests.json`. Kết quả phải là ALL PASS.
4. `python3 tools/make_pdf.py "out/….html" units/u03/data.json out/pdf`
5. `python3 tools/build.py u03 --pdf out/pdf/…worksheet.pdf out/pdf/…with-answer-key.pdf` để nhúng PDF vào trang.
6. `python3 tools/make_audio.py units/u03/data.json <thư mục giọng Piper> out/audio`
7. `python3 tools/make_excel.py out/bang.xlsx u02 u03 …`

Kiểm thử tự động kiểm tra các điểm sau:
- không có lỗi JS;
- số sao tối đa của từng buổi đúng;
- làm đúng hết thì đủ sao, mọi phần được đóng dấu Done và hiện huy chương (kể cả khi gõ dấu ' thường);
- mỗi câu sai mẫu hiện đúng gợi ý;
- các lần sai 1, 2, 3 cho đúng mức gợi ý và nút xem đáp án (lần 3 thì câu đó 0 sao);
- luật xem lại của Word Power;
- bảng kiểm bài viết đoạn chấm đúng;
- không bị tràn ngang ở khổ 375 và 390 px.

Giọng Piper (bản low) tải từ GitHub: `rhasspy/piper` releases v0.0.2, các file `voice-en-us-amy-low`, `voice-en-gb-southern_english_female-low`, `voice-en-gb-alan-low`.

## Các unit đã làm
Đã làm đủ Unit 1–10 (tập một), tất cả dựng từ cùng một template. Unit 1 và Unit 6 được chuyển từ 2 file demo gốc sang dữ liệu, giữ nguyên nội dung demo.
Unit 2–10 có 40 sao ở Part 1 và 39 sao ở Part 2, khoảng 31% số chỗ trả lời là viết cả câu hoặc đoạn và khoảng 12% là bấm chọn.
Unit 1 (38/39 sao) và Unit 6 (40/39 sao) giữ đúng như demo, tỉ lệ viết câu khoảng 25–26% (xem sheet Ti_le trong file Excel).

Kiểu tranh dùng trong data.json (trường "pic"):
- `ic:<icon>`: một icon
- `act:<icon>|<badge>`: cảnh có huy hiệu; badge có thể là giờ "6:15 AM", một icon (mon, may, tick, cross…) hoặc chữ
- `two:<icon>+<icon>`: hai icon cạnh nhau
- `digi:6:15 AM` / `ana:6:15`: đồng hồ số / đồng hồ kim
- `place:city` / `school:2|mountains`: cảnh nơi chốn / ngôi trường (Unit 6)
- `{"kid": {...}}`: vẽ một bạn nhỏ (Unit 1)
