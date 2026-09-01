<p align="center">
  <img src="docs/assets/money-bag-icon.png" alt="DSH Quota Panel Sidebar" width="128" height="128">
</p>

<h1 align="center">dsh-quota-panel-sidebar</h1>

<p align="center"><strong>DeepSeek Harness 侧栏额度与余额面板</strong></p>

<p align="center">
  <a href="https://github.com/yuzhounh/dsh-quota-panel-sidebar/releases/tag/v0.9.2"><img src="https://img.shields.io/badge/version-v0.9.2-0969da" alt="version v0.9.2"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-d4a900" alt="MIT License"></a>
  <img src="https://img.shields.io/badge/sync-true%20fork%20of%20dsh--quota--panel-2da44e" alt="true fork of dsh-quota-panel">
</p>

---

`dsh-quota-panel-sidebar` 是一个 DSH Web 插件，将模型供应商的额度、余额与用量面板挂载在侧栏底部的“设置”按钮旁。面板随侧栏折叠，在展开态与“新会话”和“设置”按钮保持一致的左右边界，并支持点击外部自动收起。

本项目以 **GitHub 真 fork** 方式跟踪 [wenzetan/dsh-quota-panel](https://github.com/wenzetan/dsh-quota-panel)（MIT）：仓库的 `main` 分支逐字节镜像上游（可在 GitHub 上 **Sync fork 一键同步**），本 sidebar 定制产品位于 **`sidebar` 分支**，基于上游工具链从 `src/` 构建产物。

## 仓库模型（真 fork + 一键同步）

```
wenzetan/dsh-quota-panel (上游)
        │  GitHub "Sync fork"（一键）
        ▼
yuzhounh/dsh-quota-panel-sidebar
        ├── main    = 纯净上游（永远与上游一致，一键同步零冲突）
        └── sidebar = sidebar 定制产品（本 README 描述的就是它）
```

- 上游更新 → 在你的 fork 页点 **Sync fork** → `main` 拿到新代码。
- 把 `main` 合并进 `sidebar`（`git merge main`，冲突通常只出现在 `src/client.ts` / `src/index.ts` 的 CSS 与挂载点上），再 `npm run build` 重编产物即可。
- 本分支代码版本独立（当前 `0.9.2`），与上游的 `-rc` 自动打标互不干扰。
- 完整操作手册见 [docs/sync.md](docs/sync.md)。

## 与上游的差异（sidebar 分支 v0.9.2）

| 改动 | 说明 |
| --- | --- |
| **挂载位置** | 从右下角悬浮（`shell.overlay`）改为侧边栏底部 `sidebar.settings.extras` 槽位，位于"设置"按钮右侧 |
| **设置按钮宽度** | 保持与“新会话”按钮等宽；额度圆钮叠放在右端，不再挤窄设置按钮 |
| **折叠协同** | 侧栏折叠成 56px 栏轨时，额度按钮变为圆点小图标，与设置齿轮纵向堆叠，一起折叠/展开 |
| **卡片对位** | 展开卡片、下方“设置”按钮和上方“新会话”按钮使用相同左右边界 |
| **卡片宽度** | 展开侧栏时占满标准内容宽度；折叠栏轨时保持 `248px` 浮层宽度 |
| **点击外部收起** | 展开后点击卡片外部任意位置自动收起（原版只能点右上角 ▾） |
| **收起箭头** | 卡片右上角收起按钮三角形方向朝下（▾） |
| **字母序排序** | 供应商行始终按显示名（label）英文字母/拼音排序——host 端 `sortedRowSpecs()` + 客户端 `sortRowsByLabel()` 双侧保证，新增供应商自动归位 |

其余能力（供应商自动发现、设置面板、行级代理、ChatGPT 登录、zai 积分套餐 `CREDIT_LIMIT`、视口钳制 + 吸顶头）均随上游同步。

## 安装

```sh
# 锁定发布 tag（推荐）
dsh plugin --profile web add "github:yuzhounh/dsh-quota-panel-sidebar#v0.9.2"

# 或跟随 sidebar 分支（不推荐，未经发布门禁）
dsh plugin --profile web add "github:yuzhounh/dsh-quota-panel-sidebar#sidebar"
```

安装后**重启 `dsh web`**（host 半的 bundle patch 与 client 模块图在启动时合成），然后刷新 `http://127.0.0.1:3080`。

> ⚠️ 依赖侧栏改动（`dsh-client-ui-sidebar` 的 `sidebar.settings.extras` 槽位 + foot 行布局）。
> 如果你的 DSH 版本与开发的 0.1.1-rc.2 差异较大，可能需要对侧栏包另行打补丁。
> 补丁说明见 [docs/sidebar-patch.md](docs/sidebar-patch.md)。

## 支持的能力（继承上游）

- 供应商自动发现：凭据可解析即上板（DeepSeek / OpenRouter / SiliconFlow / Moonshot / MiniMax / StepFun / xAI / 智谱 GLM / GLM Coding / Kimi Coding / OpenCode Go / ChatGPT 订阅 / one-api 聚合站等）
- 展开卡片：逐供应商余额/用量行、进度条、用量窗口、重置倒计时
- ⚙ 设置面板：显示开关、刷新间隔、预警阈值、按行 HTTP(S) 代理（localStorage，本地生效）
- **架构即安全**：API Key 只存在于宿主侧，浏览器仅经回环 RPC 接收归一化视图

## 发布说明（sidebar 分支）

本 fork 的定制是**源码驱动**的：改动直接落在 `src/index.ts` / `src/client.ts`，
用上游工具链构建产物（`npm run build` = `tsc` + vendored runtime 拷贝），`lib/` 是编译产物并提交入库。

- 版本：SemVer，独立于上游版本号（当前 `0.9.2`），tag 形式 `vX.Y.Z`（如 `v0.9.2`）
- 通道：push `v*` tag → `.github/workflows/release.yml` 自动建 GitHub Release（先构建并校验 `lib/` 为最新）；配置 `NPM_TOKEN` secret 后同步发布 npm
- 同步清单：上游更新 → fork 页 Sync fork → `git merge main`（在 `sidebar` 分支）→ `npm run build` → 提交

## License

MIT — 与上游一致。见 [LICENSE](LICENSE)。