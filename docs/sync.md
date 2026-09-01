# 一键同步上游（Sync fork 操作手册）

本仓库是 [wenzetan/dsh-quota-panel](https://github.com/wenzetan/dsh-quota-panel) 的 **GitHub 真 fork**：

- **`main` 分支 = 纯净上游**（与上游逐字节一致），在 GitHub 上有一键 **Sync fork** 按钮；
- **`sidebar` 分支 = 本 sidebar 定制产品**（开发与发布都在这条分支）。

## 当上游发布新版本时

1. **GitHub 网页端一键同步**：打开 <https://github.com/yuzhounh/dsh-quota-panel-sidebar> → 点击 **Sync fork** → **Update branch**。
   `main` 即刻拿到上游全部新提交（因为 main 从不偏离上游，同步永远干净、无冲突）。

2. **本地把新上游合进 sidebar**：

   ```sh
   git switch sidebar        # 到产品分支
   git fetch origin          # 拉到新同步的 main
   git merge origin/main     # 上游新代码 -> sidebar
   ```

   冲突几乎只出现在两处（改动都在 `src/`，合并规则见下）：
   - `src/client.ts`：CSS 数组、`sidebar.settings.extras` 挂载、rail 分支（上游若改动挂载/样式会撞这里）；
   - `src/index.ts` / `package.json` / `cordis.patch.yml`：名字与排序代码。

3. **重新构建产物并提交**（CI 要求 `lib/` 是最新构建）：

   ```sh
   npm install               # 依赖有变时
   npm run build             # tsc: src/*.ts -> lib/
   node --check lib/index.js && node --check lib/client.js
   git add -A && git commit -m "sync: merge upstream <版本>"
   git push origin sidebar
   ```

## 合并冲突的固定规则

| 文件 | 保留 |
| --- | --- |
| `cordis.patch.yml` | `name: 'dsh-quota-panel-sidebar'`（其余随上游） |
| `package.json` | `name` / `author` / `homepage` / `repository` = sidebar；`version` 维持/升到自己的版本（不要与上游并列同号，避免与 main 的 prerelease 自动打标撞 tag） |
| `src/client.ts` | 上游逻辑 + sidebar 挂载（`sidebar.settings.extras`、`props.wide`、rail、`sortRowsByLabel`、点外收起） |
| `src/index.ts` | 上游逻辑 + host 端 `sortedRowSpecs` |

## 分支约定

- **永远不要**把 sidebar 定制提交到 `main`——main 必须保持与上游一致，否则 Sync fork 会开始冲突。
- 发布时在 **sidebar** 分支打 `v*` tag（如 `v0.9.2`）：`.github/workflows/release.yml` 自动构建校验并建 Release（可选同步 npm）。
- 上游 `main` 的 CI（`.github/workflows/ci.yml`）会在 fork 的 main 上照常运行；`-rc` 版本会按上游逻辑自动打 prerelease tag。如果嫌噪音，可在仓库 Settings → Actions 里关掉，或删掉该 workflow（代价：与上游的 `main` 产生文件差异，同步时会有冲突）。

## 还想要更省事？

- 上游的 CI 已带 `check`（构建 + `lib/` 一致性 + 双端测试）——每次合并后跑一次 `npm run build` 并在本地 `git diff --exit-code -- lib/` 验证即可。
- 若改用 GitHub 的 "Sync fork + 自动 PR"（有些工具如 Renovate / Forksync bot）可以免手动点按钮，但基础流程都是：main 同步 → merge 进 sidebar → build → 提交。