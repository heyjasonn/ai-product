# Full SDD: UI/UX trang Job Details + Modal nộp CV

**Nguồn:** [quick-sdd-job-details-ui-ux.md](./quick-sdd-job-details-ui-ux.md)  
**Phiên bản tài liệu:** 1.0  
**Trạng thái:** Draft implementation-ready  

---

## 1. Overview

Tính năng cung cấp **trang chi tiết tin tuyển dụng (Job Details)** tối ưu cho ứng viên: dễ scan, tăng **trust**, **hook** rõ, một **primary CTA Apply** luôn thấy. Khi Apply, người dùng **không rời trang** — hệ thống mở **modal nộp CV** ngay trên trang details. Khối **Công ty** cho phép **banner** employer; toàn bộ vùng **nội dung job** giữ **neutral** theo design system. **Không** có so sánh job hay match score với profile trong phạm vi này.

---

## 2. Problem Statement

| Vấn đề | Tác động |
|--------|----------|
| Quá tải thông tin, thiếu hierarchy | Bounce cao, thời gian on-page thấp |
| Trust yếu (employer, lương, freshness) | Giảm conversion Apply |
| CTA / “lý do apply” không nổi bật | Mất momentum từ listing |

---

## 3. Business Goal

- Tăng **tỷ lệ Apply có ý định** (qualified apply) và giảm bounce trong 30s đầu.
- Tăng **niềm tin** qua employer + minh bạch meta job + trust chỉ hiển thị khi có dữ liệu/backend hỗ trợ.
- Giữ người dùng trong **một context** (details + modal) cho đến khi submit hoặc hủy.

**Giả định (Assumption):** KPI cụ thể (Apply rate, scroll depth) do PM gắn sau; tài liệu này định nghĩa hành vi & acceptance.

---

## 4. Scope

### In Scope

- Layout & component Job Details (mobile + desktop).
- Hero job: title, meta chính, trust strip, primary/secondary CTA, optional “Why join” bullets.
- Body: tabs hoặc anchor sections (Mô tả, Yêu cầu, Phúc lợi, Quy trình — subset tùy dữ liệu).
- Khối **Employer**: logo, tên, badge verified (nếu có), blurb, link careers, **banner** optional.
- **Modal Apply CV** mở từ primary CTA: upload CV, trường tối thiểu, submit, success/error trong modal hoặc kết hợp toast.
- States: loading, empty field, job closed, đã apply, chưa login (ưu tiên trong modal).
- A11y: heading order, dialog pattern, focus trap, keyboard.
- Analytics events (danh mục dưới đây).

### Out of Scope

- **Compare jobs**, **match score** với profile.
- Admin tạo/sửa tin (CMS) — chỉ tiêu thụ dữ liệu job/employer đã có.
- ATS đầy đủ, scheduling phỏng vấn, chat nhà tuyển.
- **Chi tiết business logic screening CV** (AI score, v.v.) — trừ khi API đã có; mặc định chỉ upload + metadata application.

---

## 5. User Personas

| Persona | Mô tả ngắn | Nhu cầu chính |
|---------|------------|----------------|
| **P1 — Ứ viên tích cực** | Scan nhiều tin/ngày | So sánh nhanh meta, Apply nhanh, trust |
| **P2 — Ứ viên thận trọng** | Đọc kỹ mô tả, công ty | Employer rõ, lương/work mode, ít cảm giác “quảng cáo” |
| **P3 — Mobile-first** | Chủ yếu điện thoại | Sticky CTA, modal full-screen/sheet, không che field |

---

## 6. User Stories

| ID | User story | Priority |
|----|------------|----------|
| US-1 | Là ứng viên, tôi muốn **thấy ngay** title, location, work mode, type, level để quyết định có đọc tiếp không. | Must |
| US-2 | Là ứng viên, tôi muốn **Apply bằng một CTA rõ** mà không rời trang details. | Must |
| US-3 | Là ứng viên, tôi muốn **nộp CV trong modal** (upload + xác nhận) và biết khi thành công/thất bại. | Must |
| US-4 | Là ứng viên, tôi muốn **Save/Share** không cạnh tranh với Apply. | Should |
| US-5 | Là ứng viên, tôi muốn **nhìn thấy employer + banner** (nếu có) tách biệt với nội dung job neutral. | Must |
| US-6 | Là ứng viên đã apply, tôi muốn **không apply trùng** và thấy trạng thái rõ. | Must |
| US-7 | Là ứng viên chưa login, tôi muốn **đăng nhập trong luồng modal** (khi có thể) để không mất job context. | Should |

---

## 7. Functional Requirements

### 7.1 Trang Job Details

| ID | Yêu cầu | Ghi chú |
|----|---------|---------|
| FR-D1 | Hiển thị đầy đủ field job theo **Data Requirements**; ẩn section nếu không có dữ liệu (không để heading rỗng). | |
| FR-D2 | Một **H1** duy nhất = job title. | SEO + a11y |
| FR-D3 | **Primary CTA** “Apply” / tương đương locale; **Secondary** Save, Share. | Weight visual |
| FR-D4 | Mobile: **sticky bar** hoặc FAB cho primary CTA; không che H1 hoặc meta chính (padding bottom content). | |
| FR-D5 | **Tabs hoặc anchor nav** giữa các khối nội dung dài; deep link optional tới `#requirements` (Assumption). | |
| FR-D6 | **Skeleton** khi loading; **error boundary** hoặc message khi fetch job fail. | |
| FR-D7 | Job **closed/expired**: disable Apply, hiển thị message + block gợi ý job tương tự (Assumption: có API recommendations). | |

### 7.2 Khối Employer

| ID | Yêu cầu | Ghi chú |
|----|---------|---------|
| FR-E1 | Hiển thị logo, tên, link “Xem công ty” / careers URL nếu có. | |
| FR-E2 | **Banner**: hiển thị trong container employer; object-fit cover/contain theo design; lazy load. | |
| FR-E3 | Badge “Verified” **chỉ** khi `employer.verified === true` (hoặc tương đương backend). | |
| FR-E4 | Không áp dụng màu brand employer lên card mô tả job / typography job body. | Quyết định sản phẩm |

### 7.3 Modal Apply CV

| ID | Yêu cầu | Ghi chú |
|----|---------|---------|
| FR-M1 | Mở từ primary CTA; đóng: nút X, Esc (khi không dirty hoặc confirm discard — xem **Business Rules**). | |
| FR-M2 | Tiêu đề modal: ví dụ “Ứng tuyển: {JobTitle}” + employer name optional. | |
| FR-M3 | **Upload CV**: chấp nhận định dạng/size theo **Validation**; hiển thị tên file đã chọn. | |
| FR-M4 | Submit gọi API tạo application; **loading** trên nút submit; **success** → màn success trong modal hoặc step 2 ngắn. | |
| FR-M5 | Nếu chưa login: hiển thị bước auth trong modal **hoặc** redirect có `returnUrl` nếu policy bắt buộc (Out of modal chỉ khi bắt buộc). | |
| FR-M6 | Sau success: CTA “Xem đơn ứng tuyển” / “Đóng”; optional link similar jobs trên trang nền. | |

### 7.4 Analytics (minimum)

| Event | Payload tối thiểu |
|-------|------------------|
| `job_details_view` | `job_id`, `employer_id`, source |
| `job_apply_click` | `job_id` |
| `job_apply_modal_open` | `job_id` |
| `job_apply_submit` | `job_id`, `success` boolean |
| `job_save_click` / `job_share_click` | `job_id` |

---

## 8. Business Rules

| ID | Rule |
|----|------|
| BR-1 | **Một primary CTA** Apply trên above-the-fold desktop; mobile có thể duplicate trong sticky nhưng cùng một action. |
| BR-2 | **Trust copy** không được khẳng định nếu không có cờ/dữ liệu backend (ví dụ không ghi “Đã xác minh” nếu không verified). |
| BR-3 | **Salary**: nếu ẩn theo locale/policy → hiển thị copy chuẩn (“Mức lương: nhà tuyển không công khai”) không để blank. |
| BR-4 | User **đã apply** cho `job_id`: primary CTA = disabled hoặc “Đã nộp”; không mở modal apply lại trừ khi product cho phép withdraw (Out of scope mặc định). |
| BR-5 | Đóng modal khi form **dirty**: cần confirm (“Hủy bỏ thay đổi?”) — Assumption UX chuẩn. |
| BR-6 | Banner employer: nếu URL lỗi → fallback ẩn banner hoặc placeholder neutral, không broken layout. |

---

## 9. Main Flow

```txt
1. User vào Job Details (listing click / deep link / SEO).
2. Client fetch job + employer + user context (saved, applied, auth).
3. Render hero (H1, meta, trust strip, CTA) + body sections + employer block (banner optional).
4. User đọc / scroll; có thể dùng anchor/tabs.
5. User bấm Apply:
   a. Nếu job closed → không mở modal; toast hoặc inline message.
   b. Nếu đã apply → không mở modal apply; CTA đã reflect.
   c. Nếu chưa login → modal bước auth HOẶC redirect returnUrl (policy).
   d. Ngược lại → mở Apply modal (focus vào dialog, lock scroll body).
6. User chọn file CV, điền field bắt buộc, Submit.
7. API success → success state trong modal; đóng modal hoặc CTA “Xong”; trang details refresh context `applied=true`.
8. API error → inline error modal, cho phép retry.
```

---

## 10. Alternative Flows

| # | Kịch bản | Hành vi |
|---|----------|---------|
| A1 | User bấm **Save** | Toggle bookmark; optimistic UI + rollback nếu fail. |
| A2 | User **Share** | Native share hoặc copy link; không mở modal apply. |
| A3 | User đóng modal **trước khi submit** | Nếu không dirty: đóng ngay; nếu dirty: confirm. |
| A4 | Session hết hạn **giữa chừng** modal | Thông báo lỗi auth; offer re-login trong modal hoặc redirect returnUrl. |
| A5 | Upload quá lớn / sai định dạng | Inline validation; không gọi submit. |

---

## 11. Edge Cases

| Edge case | Hành vi mong đợi |
|-----------|-------------------|
| Job không tồn tại (404) | Trang lỗi thân thiện + link về listing. |
| Job draft / không public | 404 hoặc 403 theo policy API. |
| Employer không có banner | Không render slot banner hoặc render compact card only. |
| Logo/banner load fail | Monogram / placeholder; alt text. |
| Mô tả HTML từ employer | Sanitize XSS; whitelist tags (Assumption: backend strip + FE safe render). |
| Keyboard mobile che submit | Scroll modal content; sticky footer submit trên mobile sheet. |
| Slow network | Skeleton; timeout message cho fetch job. |
| Double click Submit | Idempotency key hoặc disable button đến khi response (Assumption). |

---

## 12. Validation Rules

### 12.1 UI / Content

- H1 = title; các section H2/H3 tuần tự.
- Primary CTA contrast đạt WCAG AA với nền (Assumption token DS).
- Tap target ≥ **44×44** px (mobile).
- Banner employer: max height / aspect ratio theo DS; không tràn sang cột job.

### 12.2 Modal & Form

| Field | Rule |
|-------|------|
| CV file | Định dạng: **PDF, DOC, DOCX** (Assumption — chỉnh theo backend). |
| Size tối đa | Ví dụ **5–10 MB** (đồng bộ API). |
| Tên file | Hiển thị truncate giữa/đuôi nếu quá dài. |
| Required fields | Tối thiểu: file CV; optional: cover letter, phone (theo product). |

### 12.3 A11y (modal)

- `role="dialog"`, `aria-modal="true"`, `aria-labelledby` trỏ tiêu đề.
- Focus vào dialog khi mở; **focus trap**; khi đóng trả focus về nút Apply.
- Esc: đóng hoặc mở confirm nếu dirty.

---

## 13. Data Requirements

### 13.1 Job (client model — ví dụ)

| Field | Type | Bắt buộc UI | Ghi chú |
|-------|------|-------------|---------|
| `id` | string | ✓ | |
| `title` | string | ✓ | H1 |
| `department` | string? | | |
| `location_label` | string? | | Hybrid text |
| `work_mode` | enum | ✓ | remote/hybrid/onsite |
| `employment_type` | enum? | | full_time/part_time/contract |
| `level` | string? | | |
| `salary_display` | string? | | Đã format server hoặc FE theo locale |
| `salary_visibility` | enum? | | public/hidden/confidential |
| `summary_bullets` | string[]? | | “Why join” |
| `description_html` | string | ✓ | Sanitized |
| `requirements_html` | string? | | |
| `benefits_html` | string? | | |
| `process_html` | string? | | |
| `application_deadline` | ISO date? | | |
| `posted_at` / `updated_at` | ISO date | ✓ | Freshness |
| `status` | enum | ✓ | open/closed |

### 13.2 Employer

| Field | Type | Ghi chú |
|-------|------|---------|
| `id`, `name` | string | ✓ |
| `logo_url` | string? | |
| `banner_url` | string? | Chỉ trong employer block |
| `verified` | boolean | Badge |
| `short_description` | string? | |
| `careers_url` | string? | |

### 13.3 User context (session / API)

| Field | Ý nghĩa |
|-------|---------|
| `is_authenticated` | |
| `has_applied_to_job_id` | boolean |
| `has_saved_job_id` | boolean |

---

## 14. API / Integration Requirements

| Endpoint / hành vi | Mô tả | Ghi chú |
|---------------------|--------|---------|
| `GET /jobs/:id` | Chi tiết job + embedded employer hoặc join sẵn | 404/403 |
| `GET /jobs/:id/context` | saved, applied (Assumption: gộp vào GET job nếu đơn giản hơn) | |
| `POST /applications` | multipart: `job_id`, `cv`, optional fields | 201 + `application_id` |
| `POST /auth/login` | Nếu tách flow auth trong modal | Cookie/session policy |
| `POST /bookmarks` | Save job | Idempotent theo design |

**Assumption:** Contract cụ thể (URL, field names) do team backend chốt; FE adapter layer.

---

## 15. UI / UX Requirements

### 15.1 Desktop (≥1024px ví dụ)

- **Layout:** 2 cột — cột chính (job content), cột phụ (employer card + optional meta lặp CTA) **hoặc** single column nếu DS không có sidebar (Assumption: 2 cột nếu có chỗ).
- **Above the fold:** H1, chips meta (location, work mode, type), salary line, primary + secondary, trust strip (posted, verified).
- **Employer:** Card dưới hero hoặc cột phải; banner trên cùng card employer.

### 15.2 Mobile

- Single column; **sticky bottom** primary CTA + safe-area.
- Modal: **bottom sheet / full-screen** theo DS; scroll nội dung riêng; footer submit sticky trong modal.

### 15.3 Visual system

- **Job zone:** neutral colors, DS typography — không gradient brand employer trên body job.
- **Employer zone:** banner + logo; có thể bo góc/bóng theo DS nhưng không “pull” màu vào job typography.

### 15.4 Content design

- “Why join” tối đa **5 bullets**, mỗi bullet một dòng ý chính.
- Long description: **Read more** collapse default ~6–8 dòng (token cụ thể theo DS).

---

## 16. Permission / Access Control

| Actor | Quyền |
|-------|--------|
| Anonymous | Xem job public; Apply mở modal → yêu cầu auth trước submit (hoặc guest apply — **Out of scope** trừ khi BR mở). |
| Authenticated | Apply, Save đầy đủ theo API. |
| Owner job / admin | Không thuộc scope trang candidate. |

**Assumption:** Apply yêu cầu đăng nhập trước khi submit thành công.

---

## 17. Error Handling

| Tình huống | UI |
|------------|-----|
| Fetch job fail | Full-page hoặc inline error + Retry |
| 404 job | Message + CTA về listing |
| Upload fail (network) | Inline trong modal + Retry |
| 413 payload | Message kích thước file |
| 401 submit | Prompt đăng nhập lại |
| 409 duplicate apply | Message + disable submit |

---

## 18. Test Cases

| ID | Tiền điều kiện | Bước | Kết quả mong đợi |
|----|----------------|------|------------------|
| TC-1 | Job open, user chưa apply | Mở trang | H1 đúng; Apply enabled |
| TC-2 | | Bấm Apply | Modal mở; focus trong dialog |
| TC-3 | | Esc khi form trống | Modal đóng; focus về Apply |
| TC-4 | | Chọn PDF hợp lệ, Submit | 201 → success state |
| TC-5 | User đã apply | Mở trang | CTA “Đã nộp”; không mở apply |
| TC-6 | Job closed | Mở trang | Apply disabled |
| TC-7 | Banner URL lỗi | Mở trang | Fallback; layout không vỡ |
| TC-8 | Mobile viewport | Scroll cuối | Sticky CTA không che nội dung quan trọng (check padding) |
| TC-9 | Screen reader | Mở modal | announced as dialog; heading |

---

## 19. Risks & Assumptions

| Mục | Rủi ro / Giả định | Giảm thiểu |
|-----|-------------------|------------|
| R1 | Modal + auth phức tạp trên mobile | Sheet full-height, bước ngắn |
| R2 | HTML mô tả XSS | Sanitize server + CSP |
| R3 | SEO vs CSR-only | SSR/SSG cho public job slug |
| A1 | API applications multipart đã sẵn sàng | Spike nếu chưa |
| A2 | Không có recommendations job | Ẩn block “Tương tự” hoặc mock sau |

---

## 20. Open Questions

1. **Định dạng & giới hạn file CV** chính xác theo backend (chỉ PDF hay DOCX)?  
2. **Guest apply** có được phép không, hay bắt buộc login trước submit?  
3. **Deep link** section (`#requirements`) có cần trong v1 không?  
4. **Cover letter / câu hỏi tùy chỉnh** theo employer có trong v1 không?  
5. **i18n**: chỉ tiếng Việt hay đa ngôn ngữ cho copy CTA/modal?

---

## 21. Implementation Notes

- Tách **page container** (data fetch, SEO) khỏi **presentational sections** (Hero, BodyTabs, EmployerCard, ApplyModal).
- Modal nên là **portal** ra `document.body` để tránh z-index/stacking context.
- Dùng **React Query / SWR** hoặc tương đương: cache `jobId`, invalidate sau apply success.
- **Idempotency:** `POST /applications` nên nhận `client_request_id` nếu backend hỗ trợ (tránh double submit).
- Feature flag: `employer_banner` ẩn nếu region không cho branded banner (future).
- Liên kết ngược tới Quick SDD khi iterate: cập nhật cả hai nếu thay đổi quyết định sản phẩm §9 Quick SDD.

---

**Tài liệu liên quan:** [quick-sdd-job-details-ui-ux.md](./quick-sdd-job-details-ui-ux.md)
