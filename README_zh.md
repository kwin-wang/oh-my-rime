# oh-my-rime Fork 自动同步的 GitHub Actions 分支

此分支（`gh-actions`）是专门用于管理 GitHub Actions 工作流的独立分支，主要用于自动同步此 fork 与上游仓库。

## 用途说明

此分支作为**默认分支**，使 GitHub Actions 工作流在 Actions 选项卡中可见并可手动触发。它仅包含必要的工作流文件和文档，与主代码库保持分离。

## 架构设计

```
仓库结构：
├── gh-actions（默认分支）← 您在这里
│   └── .github/workflows/
│       └── sync-upstream.yaml  ← 自动同步工作流
└── main（代码分支）
    └── [实际项目文件]  ← 接收来自上游的更新
```

## 工作原理

### 1. 分支策略
- **`gh-actions`**：默认分支，仅包含 GitHub Actions 工作流
- **`main`**：主代码分支，镜像上游仓库
- `gh-actions` 中的工作流自动将上游的更改同步到 `main`

### 2. 同步工作流

`sync-upstream.yaml` 工作流：
- **自动运行**：每天 UTC 时间凌晨 2:00
- **可手动触发**：从 Actions 选项卡触发
- **同步更改**：从 `Mintimate/oh-my-rime`（上游）同步到此 fork 的 `main` 分支
- **使用 Action**：`aormsby/Fork-Sync-With-Upstream-action@v3.4.1`

### 3. 工作流配置

```yaml
name: 'Upstream Sync'

on:
  schedule:
    - cron: '0 2 * * *'  # 每天 UTC 2:00
  workflow_dispatch:     # 手动触发
    inputs:
      sync_test_mode:
        description: 'Fork Sync Test Mode'
        type: boolean
        default: false
```

主要特性：
- **自动调度**：使用 cron 语法进行每日执行
- **手动触发**：包含 `workflow_dispatch` 用于按需同步
- **测试模式**：可选的测试模式，用于调试而不进行实际更改
- **权限设置**：需要 `contents: write` 权限来更新仓库

## 设置说明

如果您想为自己的 fork 复制此设置：

### 步骤 1：创建 gh-actions 分支
```bash
git checkout --orphan gh-actions
git rm -rf .
```

### 步骤 2：添加工作流文件
创建 `.github/workflows/sync-upstream.yaml`，使用上述配置，并更新：
- `upstream_sync_repo`：设置为您的上游仓库

### 步骤 3：提交并推送
```bash
git add .
git commit -m "Add upstream sync workflow"
git push -u origin gh-actions
```

### 步骤 4：设置为默认分支
1. 前往 GitHub 仓库的 Settings → Branches
2. 将默认分支从 `main` 更改为 `gh-actions`
3. 确认更改

### 步骤 5：验证
1. 导航到仓库的 Actions 选项卡
2. 您应该看到工作流列表中的 "Upstream Sync"
3. 点击 "Run workflow" 测试手动触发

## 维护指南

### 检查同步状态
- 访问 [Actions 选项卡](../../actions) 查看工作流运行情况
- 每次运行都会显示是否发现并同步了新提交
- 失败的运行表示可能存在需要手动解决的合并冲突

### 修改同步计划
编辑 `sync-upstream.yaml` 中的 cron 表达式：
- `'0 2 * * *'` - 每天 UTC 2:00
- `'0 */6 * * *'` - 每 6 小时
- `'0 0 * * 0'` - 每周日午夜

### 处理冲突
如果自动同步因冲突而失败：
1. 切换到 `main` 分支
2. 手动与上游合并或变基
3. 推送解决后的更改
4. 下次自动同步将正常继续

## 优势

1. **自动更新**：无需手动干预即可与上游保持同步
2. **清晰分离**：GitHub Actions 配置与代码隔离
3. **完全可见**：工作流出现在 Actions 选项卡中并可手动触发
4. **最小占用**：gh-actions 分支仅包含必要文件

## 故障排除

### 工作流不可见
- 确保 `gh-actions` 已设置为默认分支
- 检查工作流文件的 YAML 语法是否正确

### 同步失败
- 检查 `main` 分支中的合并冲突
- 验证上游仓库名称是否正确
- 确保 GitHub token 具有适当的权限

### 手动同步
要在工作流之外手动同步：
```bash
git checkout main
git fetch upstream
git merge upstream/main
git push origin main
```

## 参考资料

- [Fork-Sync-With-Upstream-action](https://github.com/aormsby/Fork-Sync-With-Upstream-action)
- [GitHub Actions 文档](https://docs.github.com/cn/actions)
- [原始实现文章](https://ronaldln.github.io/MyPamphlet-Blog/2025/03/31/github-actionfork/)

---

*此分支由自动化维护。如需贡献代码，请使用 `main` 分支。*