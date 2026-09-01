# CHANGELOG

本 fork 基于上游 [dsh-quota-panel](https://github.com/wenzetan/dsh-quota-panel)（MIT）定制，
现在以 GitHub 真 fork 方式跟踪上游：`main` 镜像上游（一键 Sync fork），定制产品在 `sidebar` 分支。

## [0.9.2] - 2026-09-01

- **重构为 GitHub 真 fork**：仓库重建为 `wenzetan/dsh-quota-panel` 的真 fork（同名 URL 不变），`main` = 纯净上游，可在 GitHub 一键 Sync fork；sidebar 定制迁移到 `sidebar` 分支。
- **同步到上游 0.9.1-rc.3**：并入 zai 积分套餐（`CREDIT_LIMIT`）、设置面板视口钳制 + 吸顶头、ChatGPT 订阅登录、行级代理等全部上游新特性。
- **恢复上游工具链**：收回 `scripts/`、`tsconfig.json`、`package-lock.json`、`README.zh.md`、`docs/`，改为源码驱动构建（`src/` → `lib/`，`npm run build`），CI 校验 `lib/` 为最新构建。
- **sidebar 定制全部重放**：`sidebar.settings.extras` 挂载、rail 折叠圆点、卡片对位、点击外部收起、双侧字母序排序（host `sortedRowSpecs()` + client `sortRowsByLabel()`）。

## [0.9.1] - 2026-08-25

- 设置按钮不再被额度圆钮挤窄，保持与“新会话”按钮相同的完整内容宽度。
- 展开的额度卡片改用与“新会话”和“设置”相同的 `2px` 左右边距。
- 修正 bundle 清单仍引用上游包名的问题，使 sidebar fork 能由 DSH 正常导入。
- 统一客户端 ModuleLoader 注册 ID 与 sidebar 包名，避免页面报 “loaded without registering” 并停止加载。

## [0.9.0] - 2026-08-22

初始发布（fork 定制版）。相对上游 v0.8.0 的改动：

- 挂载点：`shell.overlay` 悬浮 → 侧栏底部 `sidebar.settings.extras` 槽位（设置按钮右侧）。
- 折叠协同：56px 栏轨下额度按钮变圆点，与设置齿轮纵向堆叠。
- 字母序排序：host + client 双侧按 label 稳定排序。

## 上游变更（v0.9.0 及之前）

见上游仓库 [CHANGELOG](https://github.com/wenzetan/dsh-quota-panel/blob/main/docs/development/CHANGELOG.md)。