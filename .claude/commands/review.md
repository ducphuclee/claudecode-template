---
description: Review code hiện tại hoặc một PR URL
allowed-tools: Bash(git *), Bash(gh *)
---

## Code Review Task

Thực hiện code review kỹ lưỡng.

### Nếu có PR URL trong arguments:
```
gh pr view $ARGUMENTS
gh pr diff $ARGUMENTS
```

### Nếu không có arguments - review changes hiện tại:
```
git diff HEAD
git diff --cached
```

### Checklist review:

**Correctness**
- [ ] Logic có đúng không?
- [ ] Edge cases được handle chưa?
- [ ] Error handling đầy đủ chưa?

**Security**
- [ ] Có SQL injection / XSS / command injection không?
- [ ] Input validation đầy đủ chưa?
- [ ] Không expose sensitive data?

**Performance**
- [ ] Có N+1 query không?
- [ ] Có vòng lặp không cần thiết không?
- [ ] Memory leaks?

**Code Quality**
- [ ] Tuân thủ conventions của project?
- [ ] Code có dễ đọc/maintain không?
- [ ] Có duplicate code cần refactor không?
- [ ] Tests đầy đủ chưa?

**Dependencies**
- [ ] Có dependency mới nào không cần thiết không?
- [ ] Có breaking changes không?

### Output format:
Đưa ra nhận xét theo format:
- **MUST FIX**: Lỗi nghiêm trọng cần sửa trước khi merge
- **SHOULD FIX**: Vấn đề quan trọng nên sửa
- **SUGGESTION**: Gợi ý cải thiện (optional)
- **PRAISE**: Điểm tốt cần ghi nhận

**Target:** $ARGUMENTS
