
### 方案一：创建自定义分支（推荐）

**1. 创建并维护你的自定义分支**
```bash
# 从 main 创建你的自定义分支
git checkout main
git checkout -b custom-dev

# 在 custom-dev 分支上做你的修改
# 修改代码...
git add .
git commit -m "Add custom features"
git push origin custom-dev
```

**2. 定期同步官方更新**
```bash
# 切换到 main 分支
git checkout main

# 从官方仓库拉取最新代码（这会覆盖 main，但不影响你的 custom-dev）
git fetch upstream
git reset --hard upstream/main

# 推送更新后的 main 到你的 fork
git push origin main --force

# 切换到你的自定义分支
git checkout custom-dev

# 合并最新的 main 到你的自定义分支
git merge main
```

**3. 处理可能的冲突**
```bash
# 如果有冲突，查看冲突文件
git status

# 手动解决冲突后
git add .
git commit -m "Merge latest upstream changes"
git push origin custom-dev
```

**4. 日常开发使用 custom-dev 分支**
```bash
# 启动项目时使用你的分支
git checkout custom-dev
pnpm dev
```

### 方案二：在 main 分支上保留修改（适合少量修改）

```bash
# 1. 提交你的修改
git add .
git commit -m "My custom changes"

# 2. 拉取官方最新代码
git fetch upstream

# 3. 合并（Git 会自动处理）
git merge upstream/main

# 4. 如果有冲突，解决后提交
git add .
git commit -m "Merge upstream/main"

# 5. 推送到你的 fork
git push origin main
```

### 方案三：使用 Rebase 保持历史整洁

```bash
# 1. 拉取官方最新代码
git fetch upstream

# 2. Rebase 你的提交到最新代码之上
git rebase upstream/main

# 3. 如果有冲突，解决后继续
git add .
git rebase --continue

# 4. 强制推送（因为改写了历史）
git push origin main --force
```

## 最佳实践建议

### 推荐的分支结构

```
main (custom-dev)          ← 你的日常开发分支（包含你的修改）
  ↑ merge
main (origin)              ← 你 fork 的 main，和官方同步
  ↑ sync
main (upstream)            ← 官方 lobehub 仓库
```

### 完整工作流示例

```bash
# === 初始设置（只需做一次）===
git checkout main
git checkout -b custom-dev
git push origin custom-dev

# === 日常开发 ===
git checkout custom-dev
# 做你的修改...
git add .
git commit -m "Add my features"
git push origin custom-dev

# === 每周或每月同步官方更新 ===
# 步骤1: 更新 main 分支
git checkout main
git fetch upstream
git reset --hard upstream/main
git push origin main --force

# 步骤2: 将更新合并到你的开发分支
git checkout custom-dev
git merge main

# 步骤3: 解决冲突（如果有）
# 编辑冲突文件...
git add .
git commit -m "Merge upstream v1.142.9"
git push origin custom-dev

# 步骤4: 继续在 custom-dev 上开发
pnpm dev
```

## 保护重要文件的技巧

如果某些文件经常被覆盖，可以使用 `.gitattributes`：

```bash
# 创建 .gitattributes 文件
echo ".env.local merge=ours" >> .gitattributes
echo "src/config/custom.ts merge=ours" >> .gitattributes

git config merge.ours.driver true
```

## 快速检查

```bash
# 查看当前分支
git branch

# 查看你的自定义提交
git log upstream/main..HEAD --oneline

# 查看哪些文件被修改了
git diff upstream/main
```



这样你既能保持和官方同步，又能保留自己的定制化代码！你倾向于用哪种方案？