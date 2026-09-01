# 侧栏包补丁说明（sidebar-patch.md）

本 fork 依赖 DSH 侧栏提供 `sidebar.settings.extras` 子槽，并把它和"设置"放在同一行。
该槽位在开发版本 `0.1.1-rc.2` 中由以下补丁加入，补丁打在服务器实际加载的包树上：

## 补丁文件

`node_modules/@deepseek-ai/dsh-client-ui-sidebar/lib/client.js`
（版本目录：`…\DeepSeekHarnessTray\dsh\versions\<版本>\node_modules\…`）

### 1. 声明新子槽

在 "sidebar" 槽位注册的 `children` map 中新增：

```js
"sidebar.settings": { kind: "single", scope: "root" },
"sidebar.settings.extras": { kind: "list", scope: "root" },
```

### 2. foot 区布局：设置与 extras 并排

`SidebarRoot` 的 `footArea` 子节点改为：`footerActions`（原样，Cordis 插件入口）在前，
其后一个 `.dsh-sb-settings-row` 行内并列渲染 `settingsArea` 与新增的 extras 容器：

```jsx
<div className={footArea}>
  <div className={footerActions}>{renderSlot("sidebar.footer.action", { wide })}</div>
  <div className="dsh-sb-settings-row">
    <div className={settingsArea}>{renderSlot("sidebar.settings", { wide })}</div>
    <div className="dsh-sb-extras">{renderSlot("sidebar.settings.extras", { wide })}</div>
  </div>
</div>
```

配套样式由插件在浏览器侧注入（见 `lib/client.js` 的 CSS 数组），核心规则：

```css
.dsh-sb-settings-row { display: flex; align-items: center; gap: 0; width: calc(100% - 4px); min-width: 0; margin-inline: 2px; }
.dsh-sb-settings-row > .hHd-Xa_settingsArea { flex: 0 0 100%; width: 100%; min-width: 0; }
.dsh-sb-extras { display: flex; flex: 0 0 0; width: 0; overflow: visible; }
.hHd-Xa_footArea { position: relative; }
.hHd-Xa_collapsed .dsh-sb-settings-row { flex-direction: column; justify-content: center; gap: 8px; width: auto; margin-inline: 0; }
```

展开态下 extras 不占横向布局宽度，额度圆钮向左叠放在设置按钮的右端空白区；因此设置按钮不会被挤窄。
设置行和额度卡片都使用与“新会话”按钮相同的 `2px` 左右边距，三者左右边缘一致。

## 为什么这样做

- `sidebar.footer.action` 是**列表槽**（已容纳 Cordis 面板），不复用，另开 `sidebar.settings.extras` 避免抢占；
- extras 是列表槽，未来其他插件也能注册进同一位置；
- 折叠态改纵向堆叠，符合 56px 栏轨的图标列惯例，避免横向溢出。

## 换 DSH 版本后如何重打

1. 用 `node --check` 校验目标版本对应文件路径相同；
2. 按上文三处（children map、JSX 结构、样式注入）重放即可，改动很小；
3. 或用仓库 `docs/backup/` 下保留的原始文件做差异对照。
