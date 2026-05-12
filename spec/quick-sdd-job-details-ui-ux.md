# Quick SDD: UI/UX trang Job Details

## 1. Problem

- Người dùng (ứng viên) thường scan nhiều tin; trang chi tiết job dễ bị quá tải thông tin, khó phân biệt “điều quan trọng” vs “phụ lục”.
- Thiếu tín hiệu **trust** (employer, minh bạch lương/benefit, thời gian đăng, xác minh) làm giảm tỷ lệ apply và tăng bounce.
- Hook yếu: CTA và “lý do nên apply” không nổi bật → mất momentum sau khi click từ listing.

## 2. Goal

- **Dễ đọc**: hierarchy rõ (tiêu đề → meta → tóm tắt → chi tiết), đoạn ngắn, scan được trong ~30 giây.
- **Dễ hiểu**: ngôn ngữ đơn giản; thuật ngữ job được giải thích hoặc nhóm gọn (requirements vs nice-to-have).
- **Focus**: một “hero insight” (vì sao job này đáng xem) + một primary CTA luôn trong tầm mắt (sticky hoặc lặp lại hợp lý).
- **Trust**: employer card, badge xác minh (nếu có), minh bạch compensation/work mode/location, freshness.
- **Hook mạnh**: social proof ngắn, highlight 2–3 USP, urgency có căn cứ (deadline, số slot) — không clickbait.

## 3. Input / Output

### Input

- **Job entity**: title, department/team (optional), location/hybrid/remote, employment type, level, salary range (hoặc “competitive” + policy), mô tả, requirements, benefits, application deadline, posted/updated dates.
- **Employer entity**: name, logo, industry/size (optional), verified flag, careers URL, company blurb ngắn; **banner công ty** (ảnh/URL, optional) — chỉ hiển thị trong vùng “Công ty / Employer”, không dùng làm nền toàn trang job.
- **Context người dùng**: đã login hay chưa, đã apply hay chưa, saved hay chưa, locale/timezone.
- **Analytics hooks** (cho triển khai): impression job id, scroll depth milestones, CTA clicks.

### Output

- **UI spec**: layout breakpoints (mobile/desktop), thứ tự section, component list, states (loading/empty/error), accessibility notes (heading order, contrast).
- **UX copy framework**: labels cho CTA secondary, empty states, trust microcopy.
- **Acceptance criteria** gắn với từng vùng màn hình (hero, body, sidebar/sticky bar).

## 4. Main Flow

```txt
Landing từ listing / deep link
  ↓
Above the fold: title + key facts + trust strip + primary CTA
  ↓
Scan: tóm tắt 3–5 bullet “Why join” + salary/work mode
  ↓
Đọc sâu: tabs hoặc anchor (Mô tả | Yêu cầu | Phúc lợi | Quy trình)
  ↓
Quyết định: Apply / Save / Share — sticky CTA trên mobile
  ↓
Apply: mở **modal nộp CV** ngay trên trang job details (không chuyển trang); hoàn tất hoặc hủy trong modal
  ↓
Post-apply: confirmation trong modal hoặc toast + next steps (profile, similar jobs)
```

## 5. Orchestration Mode

**Pipeline**

**Lý do:** bước 1 discovery + IA/wireframe, bước 2 visual + component spec, bước 3 copy + accessibility + edge cases — output tuần tự, dễ review. Multi-agent chỉ cần nếu tách song song research đối thủ vs design system audit.

## 6. Command

Suggested command name:

`/design-job-details`  
(hoặc tái sử dụng `/quick-sdd` khi chỉ cần bản rút gọn; phiên bản đầy đủ dùng `/full-sdd` hoặc `/refine-sdd` sau khi có wireframe.)

## 7. Validation Rules

- **Một primary CTA** trên viewport đầu tiên (Apply / Đăng ký tuyển); secondary (Save, Share) không cạnh tranh visual weight.
- **Heading hierarchy**: một H1 (job title); các section dùng H2/H3 đúng thứ tự; không nhảy cấp.
- **Salary/location/work mode**: hiển thị theo policy sản phẩm — nếu thiếu dữ liệu, hiển thị placeholder có giải thích (“Employer chưa công khai”) thay vì để trống gây nghi ngờ.
- **Trust claims có nguồn**: badge “Verified” chỉ khi có rule backend; không dùng trust language giả định.
- **Performance UX**: skeleton cho hero + body; không layout shift lớn khi load logo/ảnh.
- **Mobile**: CTA sticky không che nội dung quan trọng; safe area; tap target ≥ 44px.
- **Branding tách bạch**: phần **thông tin job** (title, meta, mô tả, yêu cầu, phúc lợi, CTA job) dùng **neutral** (màu/chữ theo design system sản phẩm). **Banner / brand employer** chỉ trong khối công ty; không “nhuộm” typography hay card nội dung job bằng màu thương hiệu nhà tuyển.
- **Modal apply CV**: mở từ primary CTA; `role="dialog"`, `aria-modal="true"`, tiêu đề modal gắn với tên job; focus trap; đóng bằng Esc và control đóng rõ; scroll body phía sau bị khóa khi modal mở; chiều cao modal thích ứng (mobile: full-screen sheet hoặc gần full viewport).

## 8. Edge Cases

- **Job hết hạn / đã đóng**: grey state, CTA disabled, gợi ý job tương tự.
- **User đã apply**: đổi CTA “Đã nộp” + link xem status; tránh double submit.
- **Chưa login khi Apply**: ưu tiên **luồng trong modal** (bước đăng nhập / đăng ký ngắn hoặc embed) để giữ context job; chỉ redirect full-page nếu policy bảo mật bắt buộc, kèm return URL về details.
- **Modal trên màn nhỏ / bàn phím**: tránh che field upload; đẩy nội dung modal khi keyboard mở; nút submit luôn reachable.
- **Thiếu logo employer / ảnh lỗi**: fallback monogram + alt text.
- **Mô tả quá dài**: “Expand” cho block dài; giữ anchor navigation.
- **Salary confidential theo locale**: copy và component variant theo market compliance.
- **SEO/traffic**: SSR hoặc meta chuẩn — không làm hero trống khi JS chậm (progressive enhancement).

## 9. Quyết định sản phẩm (đã xác nhận)

1. **So sánh job / match score**: không triển khai; không thiết kế sidebar compare hay điểm khớp profile.
2. **Employer branding**: **có banner** trong phần công ty; **neutral** cho toàn bộ vùng hiển thị thông tin job (xem Validation — branding tách bạch).
3. **Apply**: bấm primary CTA → **modal nộp CV** trên chính trang job details (không rời trang để bắt đầu apply); chi tiết bước trong modal (upload CV, trường bắt buộc, xác nhận) do spec modal / form riêng bổ sung.
