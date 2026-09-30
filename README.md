# SuperTable — 目标检测改进实验数据智能看板

单文件 HTML 数据看板，专为 YOLO 系列目标检测改进实验设计。上传 xlsx/csv 汇总表后自动以基线为锚点，逐格对比着色、计算差值、支持行列筛选与快照导出。

## 当前版本：V6

---

## 版本迭代记录

### V1（基础版）

- 侧边栏导航：首页（上传）+ 项目列表（可折叠）+ 设置
- 支持 xlsx / xls / csv 上传（SheetJS 解析）
- 自动识别基线行（按关键字 "baseline"）
- 自动识别数值列、信息列（Epoch/Type 不参与对比）、「越小越好」列（Parameters/GFLOPS）
- 以基线为锚点着色：优于基线 → 绿色，劣于 → 红色，基线行 → 黑色中性
- 每格显示与基线的差值（换行、带箭头和正负号）
- 默认按 mAP50 降序排列，可切换排序列和方向
- 行文本筛选
- 设置页：排序列、基线关键字、精度、逐格着色、方向规则、主题
- Notion 简约风格 UI，Inter + JetBrains Mono + Instrument Serif 字体
- 深色/浅色主题切换
- 侧边栏收缩（仅图标）+ 拖拽调整宽度（180~420px）
- 行复选框勾选 → 创建快照（子项目）
- 快照导出为独立 CSV 文件
- 眼镜 SVG favicon
- 数值居中对齐

### V2（增强版）

在 V1 基础上新增：

- **冻结首列**：checkbox 列 + Model 列横向滚动时保持固定（`position:sticky`）
- **列筛选**：工具栏「列筛选」下拉面板，可隐藏/显示任意列
- **快照继承列状态**：创建快照时自动保存当前列可见性，CSV 导出仅含可见列
- **数据持久化**（localStorage）：所有项目、快照、设置自动保存，刷新页面后恢复
- 设置页新增「本地存储状态」面板和「清除全部数据」按钮

### V3（小步迭代）

在 V2 基础上新增：

- **同名文件增量更新**：拖入与已有项目同名的 xlsx/csv 文件时，自动覆盖旧数据（重新检测列类型、重新识别基线），**已有快照不受影响**（快照是独立数据副本）。侧栏标记「已更新」徽章，显示更新时间戳。
- **全量数据导出**：设置页或首页一键导出 JSON 备份文件（含所有项目 + 快照 + 设置），文件名自动带日期。
- **跨设备导入**：在新设备上打开 SuperTable_V3.html → 导入之前导出的 JSON → 一键恢复全部数据资产。
- 导入时二次确认防误操作
- Toast 消息分级（成功绿 / 错误红 / 普通黑）

### V4

在 V3 基础上做视觉全面重做（功能与数据格式不变），采用 [Cursor-Light-Warmth](UI/Cursor-Light-Warmth-设计说明.md) 设计语言：

- **暖纸白三层底色**：`#F7F7F4` 主背景 → `#F2F1ED` 侧栏/底板 → `#E6E5E0` 凹陷面，分区靠色阶而非边框；所有"黑色"统一为暖墨色 `#26251E`
- **胶囊 / 4px 双圆角体系**：按钮、输入框、导航项、开关、徽章一律 `9999px` 胶囊；卡片、表格、窗口模型、舞台一律 `4px` 小圆角，无中间态
- **产品窗口模型**：首页上传区与项目表格都装进白色窗口（红绿灯标题栏 + 等宽文件名 + `0 0 16px` 无偏移弥散阴影）；首页以油画质感媒体舞台托起两扇错位叠放的窗口
- **双色标题法**：主标题两行同字号同字重（400、负字距），第二行降为 55% 透明度墨色 + EB Garamond 衬斜体，用色彩分层替代字号层级
- **单点朱砂**：`#C8451D` 仅出现在文字链接（首页「了解对比与备份流程 →」），每屏至多一处；diff 语义色改用低饱和绿 `#3F7A46` / 红 `#B0413E`
- **新 LOGO 与 Favicon**：V 形字母 + 纯色底的双版本——浅色模式为彩虹渐变 V + 浅卡其底（`UI/logo-light.svg`），深色模式为高级紫渐变 V + 黑底（`UI/logo-dark.svg`）；左上角 LOGO 与浏览器 Favicon 均随主题自动切换
- **字体系统**：Inter / Instrument Serif → Instrument Sans（人文无衬线）+ EB Garamond Italic（衬斜体点缀）+ JetBrains Mono（代码 / diff / 徽章）+ Noto Sans SC
- **动效收敛**：过渡统一 `0.14s cubic-bezier(.25,1,.5,1)`，入场 `fadeSlideUp`（2px 位移），`prefers-reduced-motion` 时降级
- **深色模式**：三层纸底整体换为 `#1A1915` 系暖黑，朱砂提亮至 `#E2673F` 以保证对比度
- **数据延续**：功能与 localStorage key `supertable_v3_state` 均与 V3 一致，V3 数据可直接在 V4 中继续使用

### V5

在 V4 基础上新增**本地数据持久化同步**（功能与视觉延续 V4）：

- **选择本地同步文件夹**：设置页「本地文件夹同步」→「选择文件夹…」，用浏览器 **File System Access API**（`showDirectoryPicker`）选取一个本地文件夹
- **每次操作后自动同步**：导入、排序、筛选、快照、改设置等任何操作（统一经 `persist()`）触发后，自动把完整数据写入所选文件夹的 `SuperTable_Sync.json`（与「导出 JSON」同格式），约 450ms 去抖合并连续操作
- **文件夹句柄持久化**：目录句柄存入 **IndexedDB**（`supertable_sync`），刷新 / 重开浏览器后仍记住所选文件夹，无需重新选择
- **权限续接**：重开后自动查询并申请 `readwrite` 权限；若浏览器要求，点「立即同步」即可重新授权写入
- **从文件夹恢复**：「从文件夹恢复」读取 `SuperTable_Sync.json` 一键还原全部项目（与 JSON 导入一致，覆盖前二次确认），可用于换设备 / 误删后找回
- **手动同步 / 取消链接**：「立即同步」随时强制写入；「取消链接」解除文件夹绑定（不删除已同步文件）
- **优雅降级**：不支持 File System Access 的浏览器（Firefox / Safari）自动禁用该功能并提示改用 JSON 导出 / 导入；Chromium 内核（Chrome / Edge）完整支持
- **数据延续**：localStorage key 与数据格式仍为 `supertable_v3_state`，V4 / V3 数据可直接在 V5 中继续使用

### V6（当前版本）

在 V5 基础上新增**仓库入口**（功能与数据格式不变）：

- **右上角 GitHub 图标**：顶栏最右侧新增 GitHub 标志性 Octocat 图标，点击新标签页打开仓库主页 <https://github.com/luoqianshi/supertable>
- 图标沿用顶栏胶囊图标按钮样式（`currentColor` 着色），浅色 / 深色主题下自动适配，悬停轻微放大
- **数据延续**：localStorage key 与数据格式仍为 `supertable_v3_state`，V5 / V4 / V3 数据可直接在 V6 中继续使用

---

## 功能概述

| 功能 | 说明 |
|------|------|
| 文件导入 | 拖拽或点击上传 .xlsx / .xls / .csv，支持批量 |
| 增量更新 | 同名文件再次拖入 → 覆盖数据，快照保留 |
| 基线对比 | 自动识别 baseline 行，逐格计算差值并着色 |
| 方向感知 | Parameters/GFLOPS 越小越好（绿色），其余越大越好 |
| 行筛选 | 文本搜索框实时过滤 |
| 列筛选 | 下拉面板控制列可见性 |
| 快照 | 勾选行 + 当前列状态 → 创建子项目 |
| CSV 导出 | 快照可独立导出为 CSV（仅含可见列） |
| 数据持久化 | localStorage 自动保存，刷新不丢失 |
| 全量备份 | 导出/导入 JSON，跨设备迁移 |
| 文件夹同步 | 选择本地文件夹，每次操作自动写入 SuperTable_Sync.json（V5） |
| 排序 | 点击表头切换排序列和方向 |
| 主题 | 浅色/深色切换 |
| 侧边栏 | 收缩至图标模式 / 拖拽调整宽度 |
| GitHub 入口 | 顶栏右上角 GitHub 图标，新标签页打开仓库（V6） |

---

## 使用方法

1. 用浏览器打开 `index.html`（需联网加载 SheetJS 和字体 CDN），或直接访问 GitHub Pages 在线地址
2. 拖入实验汇总表格（首行为列名，其余每行一个模型）
3. 自动识别 baseline → 绿红着色 + 差值
4. 勾选感兴趣的行 → 「快照」→ 子项目独立存在
5. 快照页面 → 「导出 CSV」→ 用于论文表格
6. 实验有新结果时，拖入同名文件即可覆盖更新，快照保持不变
7. 换设备时：设置 → 「导出 JSON」→ 在新设备「导入」
8. （可选，V5）设置 → 「本地文件夹同步」→「选择文件夹…」，之后每次操作自动把数据同步为该文件夹下的 `SuperTable_Sync.json`；换设备或误删后可「从文件夹恢复」

---

## 技术细节

- **单文件架构**：全部 HTML + CSS + JS 内联，无构建步骤
- **外部依赖**：SheetJS 0.18.5（CDN）、Google Fonts（Instrument Sans / EB Garamond Italic / JetBrains Mono / Noto Sans SC）
- **存储**：localStorage（key: `supertable_v3_state`），JSON 序列化，Set↔Array 互转
- **文件夹同步（V5）**：File System Access API（`showDirectoryPicker`，Chromium 内核）选取本地文件夹，目录句柄存于 IndexedDB（`supertable_sync`）；每次 `persist()` 后去抖写入 `SuperTable_Sync.json`
- **兼容性**：Chrome / Edge / Firefox / Safari 现代版本（文件夹同步需 Chrome / Edge，其余浏览器自动降级为 JSON 导出 / 导入）
- **数据不出本机**：纯前端处理，无服务端通信

---

## 部署（GitHub Pages）

推送到 `main` / `master` 分支后，`.github/workflows/deploy-pages.yml` 会自动把当前页面部署到 GitHub Pages：

1. 仓库 **Settings → Pages → Build and deployment → Source** 选择 **GitHub Actions**（首次需开启）
2. 推送代码，或在 **Actions** 页手动触发 `Deploy to GitHub Pages`
3. 构建产物为 `index.html` + `UI/*.svg`，纯静态、无构建步骤；部署前会校验 `index.html` 是否存在

---

## 工程规约

每次版本迭代之后必须完成两件事，详见 [AGENTS.md](AGENTS.md)：

1. 更新 `README.md`（版本号、迭代记录、文件清单）
2. 备份本次版本 HTML 到 `Versions/`（`SuperTable_Vx.html`）

---

## 文件清单

```
index.html               ← 当前版本（V6，推荐使用；GitHub Pages 部署的就是它）
Versions/                ← 历史版本备份目录
  SuperTable_V6.html     ← V6 备份
  SuperTable_V5.html     ← V5 备份
  SuperTable_V4.html     ← V4 备份
  SuperTable_V3.html     ← V3 备份
  SuperTable_V2.html     ← V2 备份
  SuperTable_V1.html     ← V1 备份
UI/
  logo-light.svg         ← 浅色模式 LOGO / Favicon（彩虹渐变 V + 浅卡其底）
  logo-dark.svg          ← 深色模式 LOGO / Favicon（紫渐变 V + 黑底）
  Cursor-Light-Warmth-设计说明.md  ← V4 采用的设计风格说明
.github/workflows/deploy-pages.yml  ← GitHub Pages 部署工作流
AGENTS.md                ← 协作规约（版本迭代后必做事项）
.gitignore               ← 忽略 Exp_Data/、_site/ 等
.gitattributes           ← 换行符规范（避免 Actions 脚本吃到 \r）
README.md                ← 本文件
Exp_Data/                ← 实验数据（已被 git 忽略，不入库）
```
