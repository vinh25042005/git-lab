# S01 — "Đã commit nhầm secret"

## Trạng thái repo hiện tại

```bash
git log --oneline
# 90a38db (HEAD -> main) Add project files and pre-commit config
# ea63bbc cherry-pick: docs:hotfix
# 64c6ae5 Initial commit
```

**Giả sử push nhầm api key**
```bash
echo "API_KEY" > secret.env
git add secret.env
git commit -m 'Add secret config (NHẦM!)'
git remote add origin git@github.com:user/git-lab.git
git push origin main
```
**Trạng thái sau thiết lập:**
```bash
git log --oneline
# f0b306c (HEAD -> main, origin/main) Add secret config (NHẦM!)
# 90a38db Add project files and pre-commit config
# ea63bbc cherry-pick: docs:hotfix
# 64c6ae5 Initial commit
```

# Cách giải quyết

**Bước 1: Xoá file khỏi tracking & thêm vào .gitignore**
```bash
git rm --cached secret.env
echo "secret.env" >> .gitignore
git add .gitignore
git commit -m 'Remove secret.env from tracking'
```
**Bước 2: Xoá file khỏi TOÀN BỘ history**
```bash
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch secret.env" \
  --prune-empty --tag-name-filter cat -- --all
```
**Bước 3: Force push lên remote**
```bash
git push origin --force --all
git push origin --force --tags
```
**Bước 4: Dọn dẹp local**
```bash
git reflog expire --expire=now --all
git gc --prune=now --aggressive
```
**Bước 5: Đảm bảo team clone về không thấy file**
```bash
# Mỗi người clone mới (không pull):
git clone git@github.com:user/git-lab.git

# HOẶC nếu đã có local clone:
git fetch origin
git reset --hard origin/main
git clean -fd
```

## Rủi ro còn lại
Token đã bị lộ 
Fork đã tồn tại
CI/CD logs 
Local clone của team