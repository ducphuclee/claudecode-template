---
description: Đọc lại context từ session trước và tiếp tục công việc đang dở
allowed-tools: Read, Bash(git *)
---

## Resume — Tiếp tục công việc

Đọc lại toàn bộ project state và tiếp tục từ nơi đã dừng.

### Bước thực hiện

1. **Đọc song song** tất cả context cần thiết:
   - `.project-manager/sessions/latest.md` — session trước dừng ở đâu
   - `.project-manager/status.md` — trạng thái tổng thể
   - `.project-manager/tasks/in-progress.md` — tasks đang dở
   - `.project-manager/knowledge/blockers.md` — blockers
   - `.project-info/patterns.md` — patterns đã học
   - `git status` — uncommitted changes

2. **Tổng hợp và báo cáo** cho user:

```
## Project Status

**Đang làm:** [task name] — [X%]
**Dừng ở:** [mô tả cụ thể]
**Blockers:** [nếu có]

## Uncommitted changes
[danh sách files nếu có]

## Tôi đề xuất tiếp tục:
> [Next action cụ thể]

Bạn có muốn tiếp tục không?
```

3. **Chờ xác nhận** từ user trước khi bắt đầu làm.

4. Khi user xác nhận → tiếp tục từ "Next action" đã đề xuất.

---

> Tip: Gõ `/resume` ngay khi mở session mới để không mất thời gian tìm lại context.
