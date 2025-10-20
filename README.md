# 本地名单抽奖（老虎机动画）— 双语使用说明 / Bilingual Guide

> 版本 / Version：**V3.0.0**（紫色主题｜注释与文案增强） · 更新日期 / Updated: **2025-10-20**
> 文件 / File：`本地名单抽奖.html`（单文件、离线运行 / single-file, offline）

---

## 目录 / Table of Contents

* [项目简介 / Project Overview](#项目简介--project-overview)
* [核心功能 / Key Features](#核心功能--key-features)
* [快速开始 / Quick Start](#快速开始--quick-start)
* [数据格式与导入 / Data Formats & Import](#数据格式与导入--data-formats--import)
* [编码识别策略 / Encoding Strategy](#编码识别策略--encoding-strategy)
* [列映射与样例 / Column Mapping & Examples](#列映射与样例--column-mapping--examples)
* [抽取范围设置 / Draw Range Setup](#抽取范围设置--draw-range-setup)
* [抽取与可复现性 / Drawing & Reproducibility](#抽取与可复现性--drawing--reproducibility)
* [可视化与一致性 / Visualization & Consistency](#可视化与一致性--visualization--consistency)
* [导出中奖记录 / Export Results](#导出中奖记录--export-results)
* [可访问性与交互 / Accessibility & UX](#可访问性与交互--accessibility--ux)
* [浏览器兼容性 / Browser Compatibility](#浏览器兼容性--browser-compatibility)
* [性能建议 / Performance Tips](#性能建议--performance-tips)
* [常见问题（FAQ）/ FAQ](#常见问题faq--faq)
* [配置速查（JS/CSS） / Config Cheatsheet](#配置速查jscss--config-cheatsheet)
* [二次开发建议 / Dev Notes](#二次开发建议--dev-notes)
* [安全与隐私 / Security & Privacy](#安全与隐私--security--privacy)
* [版本记录与致谢 / Changelog & Credits](#版本记录与致谢--changelog--credits)
* [许可 / License](#许可--license)

---

## 项目简介 / Project Overview

* **中文**：本地离线抽奖工具。导入 TXT/CSV/Excel 名单，设置抽取范围，点击开始即可。界面采用「老虎机滚动动画」，并保证**所见即所得**：**中央可见项**即为**最终结果**。支持随机种子（可复现）、同轮不重复、历史导出。
* **EN**: A local, offline raffle tool. Import a list from TXT/CSV/Excel, set the draw range, and hit **Start**. Uses a slot-machine animation with a **WYSIWYG** guarantee: the **center row** is **exactly the final winner**. Supports seeded randomness (reproducible runs), no-repeat per round, and CSV export.

> **单文件即用 / Single File**：所有逻辑与样式在同一 HTML 中（通过 CDN 加载 `xlsx`）。无需后端、无需构建。参见 SheetJS 的浏览器独立脚本说明。([docs.sheetjs.com][1])

---

## 核心功能 / Key Features

* **多格式导入 / Multi-format import**：TXT / CSV / XLS / XLSX（读取首个工作表 / reads the first sheet）。基于 SheetJS（`xlsx.full.min.js`）。([docs.sheetjs.com][2])
* **智能编码识别 / Smart encoding detection**：优先识别 BOM（UTF-8/UTF-16），无 BOM 时通过 `TextDecoder` 在候选编码中打分选择。([MDN Web Docs][3])
* **列映射 / Column mapping**：检测表头后可指定“姓名/学号”列。
* **双模式范围 / Two range modes**：
  – 按学号区间 / by ID interval（仅在已导入学号内抽）
  – 按姓名勾选 / by name selection（含搜索、全选/全不选）
* **排除名单 / Exclusion list**：姓名或学号，空格/逗号分隔。
* **可复现抽取 / Reproducible draws**：可选随机种子；同轮不重复。
* **动画一致性 / Visual-logic consistency**：**中央=结果**，无“重影/错位”。
* **历史导出 / Export history**：CSV 含时间/序号/姓名/学号/显示标签。

---

## 快速开始 / Quick Start

1. 打开 `本地名单抽奖.html`（Chrome/Edge/Firefox 均可）。
2. 选择或拖拽 TXT/CSV/XLS/XLSX 名单。
3. 若出现“列映射”，确认姓名/学号列。
4. 选择抽取范围（学号区间或姓名勾选），必要时填写排除名单。
5. 设置连抽次数、是否同轮不重复、（可选）随机种子。
6. 点击 **开始抽取**：中央与结果屏同步显示。
7. 需要记录时，点击 **导出中奖记录** 生成 CSV。

> **提示 / Tip**：用“最小/最大填充”按钮可自动填好学号上下界。

---

## 数据格式与导入 / Data Formats & Import

**TXT/CSV**（每行一条 / one per line）：

```
张三,10001
10002,李四
王五
```

* 可用中文逗号、制表符或空白分隔；空行会被忽略。

**Excel（XLS/XLSX）**：读取第一个工作表；支持：

* 带表头：`学号 | 姓名 | …`（自动识别关键词）
* 无表头但两列成组：`序号 | 姓名 | 序号 | 姓名 | …`

> **显示标签 / Label**：`姓名（学号）`；若缺失则显示现有字段。
> **说明 / Note**：表格解析依赖 SheetJS `xlsx`。([docs.sheetjs.com][2])

---

## 编码识别策略 / Encoding Strategy

* **先看 BOM**：UTF-8 / UTF-16LE / UTF-16BE。
* **无 BOM**：使用浏览器 `TextDecoder` 逐个候选尝试，按替代符（�）更少、控制字符更少、CJK 更多进行打分，UTF-8 略加权；必要时回退默认解码。([MDN Web Docs][3])
* **建议**：若文本解码失败，改存 UTF-8 或直接用 Excel 导入。

---

## 列映射与样例 / Column Mapping & Examples

* 检测到表头后显示映射区；从下拉选择“姓名/学号”列，点击“应用映射”。
* 为简洁，请按新映射**重新导入一次**文件以完全生效。

**示例 / Example**

```
学号,姓名,班级
10001,张三,A1
10002,李四,A1
```

---

## 抽取范围设置 / Draw Range Setup

* **按学号 / By ID**：输入起始/结束（含边界）；仅在**已导入**学号内抽。
* **按姓名 / By Name**：勾选参与者；搜索框即时过滤（不改勾选状态）。
* **排除名单 / Exclusion**：姓名或学号；空格/逗号/中文逗号分隔。

> 两种模式互斥；切换后自动重算候选并刷新滚动条。

---

## 抽取与可复现性 / Drawing & Reproducibility

* **连抽次数 / Consecutive count**：一次点击多次抽取；每次结果单独入历史。
* **同轮不重复 / No-repeat per run**：同一轮不会重复同一人。
* **随机种子 / Seed**：任意字符串 → `mulberry32` PRNG；同数据 + 同设置 → 完全一致的结果（可复现实验过程）。

---

## 可视化与一致性 / Visualization & Consistency

* 动画采用 `requestAnimationFrame` + `transform: translateY`，并使用 `easeOutCubic` 减速。
* 抽取前对候选集**拍快照**并**重排滚动条**，确保**中央=结果**；动画结束后以高亮浮现层（overlay）呈现，避免“重影/错位”。

---

## 导出中奖记录 / Export Results

* 生成 `winners.csv`（UTF-8），包含：**时间 / 序号 / 姓名 / 学号 / 显示标签**。
* 若 Excel 打开乱码，建议用“数据 → 自文本/CSV → UTF-8”导入。

---

## 可访问性与交互 / Accessibility & UX

* 关键控件带 `title` 与 `aria-*`；结果屏使用 `aria-live="polite"`。
* 拖拽高亮、聚焦态样式清晰；按钮具备悬停与按压反馈。

---

## 浏览器兼容性 / Browser Compatibility

* 推荐 Chrome/Edge（近两年）或 Firefox ESR+；移动端可用，桌面体验更佳。
* 个别编码标签的可用性取决于浏览器对 `TextDecoder` 的支持。([MDN Web Docs][3])

---

## 性能建议 / Performance Tips

* 滚动条 DOM 上限：`MAX_N_DOM = 400`，避免超长名单导致重排卡顿。
* 大名单建议：缩小候选范围、利用搜索、适度降低 `--cell-h`、关闭其他重资源页面。

---

## 常见问题（FAQ） / FAQ

**Q1：导入后乱码？ / Garbled text?**
A：优先 Excel 导入或另存为 UTF-8；避免混用制表符与空格。`TextDecoder` 出错时会用替代符（�）代替异常字节。([MDN Web Docs][4])

**Q2：未解析到有效数据？ / No valid rows?**
A：确保每行至少有姓名或学号；空白行会被忽略。

**Q3：候选=0？ / Candidates=0?**
A：检查范围模式、学号上下界、排除名单、姓名勾选。

**Q4：动画与结果为何一致？ / Why does UI match result?**
A：抽取前依据目标索引重排 strip，停止时中央即结果；结果屏仅作同步显示。

**Q5：如何复现实验过程？ / How to reproduce runs?**
A：使用**相同种子**、**相同数据**与**相同设置**（模式、次数、排除…）。

---

## 配置速查（JS/CSS） / Config Cheatsheet

**JS**：`VISIBLE_ROWS=3`；`CENTER_INDEX=1`；`COPIES=3`；`MAX_N_DOM=400`。
**CSS**（`:root`）：主题色 `--accent-*`；单格高度 `--cell-h`（默认 56px）；机台宽度 `--reel-w`；姓名网格列数 `--name-cols`（默认 3，窄屏降为 2/1）。

---

## 二次开发建议 / Dev Notes

* **文案与多语言 / Copy & i18n**：可将 UI 文案集中到对象，便于后续切换语言。
* **主题切换 / Theme switch**：基于 CSS 变量 + `data-theme` 实现深浅色或品牌色切换。
* **动画替换 / Animation swap**：保留“快照→目标索引→重排 strip”的核心流程，可替换翻牌/粒子等表现。
* **依赖 / Dependency**：使用 SheetJS 解析 Excel；浏览器侧脚本 `xlsx.full.min.js` 适合单文件直挂。([docs.sheetjs.com][1])

---

## 安全与隐私 / Security & Privacy

* 全程本地运行，不上传任何数据。
* 导出的 CSV 含中奖记录，请妥善保管。

---

## 版本记录与致谢 / Changelog & Credits

* **V3.0.0（2025-10-20）**

  * 紫色主题与老虎机机台视觉；中央 marker 聚焦与渐隐遮罩。
  * `TextDecoder` 驱动的**智能编码识别**提示；失败引导更清晰。([MDN Web Docs][3])
  * 表头映射、两列成组/单列解析更稳健；姓名网格 3/2/1 列响应式。
  * **中央=结果** 的滚动一致性方案；连抽进度与结果浮现层更友好。
  * CSV 导出时间/序号/姓名/学号/显示标签，便于复盘与对账。
  * 文案与可访问性增强（`title` / `aria-*` / `aria-live`）。

> 本项目的版本记录建议遵循 **Keep a Changelog**，并采用 **语义化版本（SemVer）**。([keepachangelog.com][5])
> 依赖感谢：**SheetJS (xlsx)**。([docs.sheetjs.com][2])

---

## 许可 / License

* 未显式开源许可，默认**仅限个人/内部使用**。
* 若需开源分发，建议添加 **MIT** 或 **Apache-2.0** 并在 README 中声明。
* 版本号建议遵循 **SemVer**（如 `MAJOR.MINOR.PATCH`）。([Semantic Versioning][6])

[5]: https://keepachangelog.com/en/1.1.0/?utm_source=chatgpt.com "Keep a Changelog"
[6]: https://semver.org/?utm_source=chatgpt.com "Semantic Versioning 2.0.0 | Semantic Versioning"
