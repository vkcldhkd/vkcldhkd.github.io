---
title: Commit Message Guide
date: 2025-11-04 22:31:43
category:
- Git
tags:
- Commit Message Guide
- Git
---

# Commit Message Guide:
커밋하는 스타일을 개선해보기 위해 OpenAI 팀에서 사용한다는 커밋 스타일을 보고 학습하기위해 작성!

---

## Commit Types

| Type | Description |
|------|--------------|
| **feat** | Add a new feature |
| **fix** | Fix a bug |
| **docs** | Documentation changes only |
| **style** | Code style/formatting changes (no logic impact) |
| **refactor** | Refactor code without changing behavior |
| **perf** | Performance improvement |
| **test** | Add or update tests |
| **build** | Build system or dependency changes |
| **ci** | Continuous integration configuration changes |
| **chore** | Maintenance tasks (no code change) |
| **revert** | Revert a previous commit |

> These follow the [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) standard.

---

## Format

```
<type>(<scope>): <short summary>

[optional body]

[optional footer]
```

### Example
```
feat(api): add retry mechanism for failed requests

- Implements exponential backoff for network calls
- Adds configuration flag `maxRetryCount`
```

---

## Examples from Real OpenAI-style Commits

### Feature
```
feat(eval): add MMLU-lite benchmark for smaller models
```
→ Adds a new evaluation suite for lightweight models.

### Fix
```
fix(api): correct error handling for streaming completions
```
→ Corrects bug in API streaming pipeline.

### Refactor
```
refactor(trainer): simplify gradient accumulation logic
```
→ Cleans up internal training loop logic.

### Performance
```
perf(model): optimize KV-cache reuse during decoding
```
→ Improves performance in GPT decoding.

### Documentation
```
docs(readme): update setup instructions and directory structure
```
→ Updates documentation without code changes.

### Test
```
test(tokenizer): add regression tests for unicode normalization
```
→ Adds new tests for tokenizer edge cases.

### Build / CI
```
build: bump numpy dependency to 1.26.3
ci(workflow): enable scheduled benchmark runs
```

### Maintenance
```
chore: remove legacy scripts and clean up project root
```

### Revert
```
revert: feat(ui): add new onboarding flow
```
→ Reverts an unwanted feature addition.

---

## Tips for Great Commit Messages

1. **Use imperative tone** (e.g., “Add”, “Fix”, “Refactor”)  
2. **Keep title ≤ 50 characters**
3. **Use blank line** between title and body
4. **Wrap body at 72 characters**
5. **Focus on WHY the change was made**, not just what changed

---

## Quick Reference Table

| Type | Meaning |
|------|----------|
| `feat` | Add new functionality |
| `fix` | Resolve a bug |
| `docs` | Documentation updates |
| `style` | Code formatting/style changes |
| `refactor` | Structural code improvement |
| `perf` | Performance optimization |
| `test` | Add/modify tests |
| `build` | Build or dependency changes |
| `ci` | CI/CD configuration updates |
| `chore` | Maintenance / cleanup |
| `revert` | Undo a previous commit |

---

## Summary
> OpenAI and many large AI teams adopt this commit style for clarity, automation, and collaboration.  
> Using a consistent pattern like `feat(animation): improve genie-lamp spawn transition` helps generate changelogs, track intent, and maintain clean project history.

---

© 2025 — OpenAI-style Commit Reference (Community Documentation)
