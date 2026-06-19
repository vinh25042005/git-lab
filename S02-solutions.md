# S02 — "Merge nhầm vào main"

## Trạng thái repo hiện tại

```bash
git log --oneline
# 2a0d53a (HEAD -> main, origin/main) Remove secret.env from tracking
# 90a38db Add project files and pre-commit config
# ea63bbc docs:hotfix
# 64c6ae5 Initial commit
```

**Giả sử merge nhầm branch `feature-b` vào main**
```bash
git merge feature-b -m "Merge branch 'feature-b' into main (NHAM!)"
git push origin main
```
**Trạng thái sau thiết lập:**
```bash
git log --oneline --graph
# *   c7fe85e (HEAD -> main, origin/main) Merge branch 'feature-b' into main (NHAM!)
# |\
# | * a44287f (feature-b) docs: feature-b v2
# | * 8b92bc2 docs: feature-b v1
# | * 2efe409 docs:file3
# | * 496fc6c docs:file2
# | * 4bd3175 docs:file1
# * | 2a0d53a Remove secret.env from tracking
# * | 90a38db Add project files and pre-commit config
# * | ea63bbc docs:hotfix
# |/
# * 64c6ae5 Initial commit
```

---

# Cách giải quyết

## Cách 1: Revert merge commit

```bash
git revert -m 1 HEAD
git push origin main
```

**Ưu điểm:** An toàn, không force push, team chỉ cần `git pull`.
**Nhược điểm:** Không merge lại được branch đó — cần revert cái revert trước (`git revert <revert-commit>`).

## Cách 2: Reset hard

```bash
git reset --hard 2a0d53a
git push origin main --force
```

**Ưu điểm:** Lịch sử sạch, merge lại branch bình thường.
**Nhược điểm:** Rewrite history, team phải clone lại, cần force push.

---

