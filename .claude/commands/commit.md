---
description: Tạo git commit thông minh theo Conventional Commits
allowed-tools: Bash(git *)
---

## Commit Task

Tạo một git commit cho các thay đổi hiện tại.

### Bước thực hiện:

1. Chạy `git status` và `git diff` để xem các thay đổi
2. Chạy `git log --oneline -5` để xem style commit của project
3. Phân tích các thay đổi và xác định:
   - **type**: feat / fix / chore / docs / test / refactor / perf / ci
   - **scope**: phần nào của codebase bị ảnh hưởng (optional)
   - **description**: mô tả ngắn gọn bằng tiếng Anh, viết thường, không dấu chấm cuối
4. Stage các file phù hợp (KHÔNG dùng `git add .` nếu có file nhạy cảm)
5. Tạo commit với message theo format:
   ```
   <type>(<scope>): <description>

   [optional body: giải thích WHY nếu cần]
   ```

### Lưu ý:
- KHÔNG commit file `.env`, credentials, hoặc file nhạy cảm
- KHÔNG dùng `--no-verify`
- Nếu có argument `$ARGUMENTS`, dùng làm gợi ý cho commit message

**Arguments:** $ARGUMENTS
