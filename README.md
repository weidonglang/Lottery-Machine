# 本地名单抽奖（老虎机动画）— 双语使用说明 / Bilingual Guide

> 版本 / Version：V3（注释与文案增强版） · 更新日期 / Updated: 2025‑10‑19
> 文件 / File：`点我运行_修复_注释与文案优化版.html`（单文件离线运行 / single‑file, offline）

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

* **中文**：一个本地离线运行的抽奖工具。导入 TXT/CSV/Excel 名单，设置抽取范围，点击开始即可。界面采用「老虎机滚动动画」，并保证**所见即所得**：**中央可见项**即为**最终结果**。支持随机种子（可复现）、同轮不重复、历史导出。
* **EN**: A local, offline raffle tool. Import a list from TXT/CSV/Excel, set the draw range, and hit Start. Uses a slot‑machine style animation with **WYSIWYG** guarantee: the **center row** is **exactly the final winner**. Supports seeded randomness (reproducible runs), no‑repeat per round, and exportable history.

> **单文件即用 / Single File**：所有逻辑与样式在一个 HTML 中（通过 CDN 加载 `xlsx`）。No backend, no build.

---

## 核心功能 / Key Features

* **多格式导入 / Multi‑format import**：TXT / CSV / XLS / XLSX（Excel 取首表 / first sheet).
* **智能编码识别 / Smart encoding detection**：UTF‑8/UTF‑16/GBK/GB18030/Big5/Shift_JIS…
* **列映射 / Column mapping**：识别表头后指定“姓名/学号”列 / pick name & id after header detection.
* **双模式范围 / Two range modes**：

  * 按学号区间 / by ID interval（仅在已导入学号内抽 / only existing IDs).
  * 按姓名勾选 / by name selection（搜索、全选/全不选 / search, select all/none).
* **排除名单 / Exclusion list**：姓名或学号，空格/逗号分隔 / names or IDs, space/comma separated.
* **可复现抽取 / Reproducible draws**：可选随机种子；同轮不重复 / optional seed; no‑repeat per run.
* **动画一致性 / Visual‑logic consistency**：中央=结果 / center equals result.
* **历史导出 / Export history**：CSV with timestamp/name/id/label.
* **可访问性 / Accessibility**：`title` & `aria-*` hints; live region for result.

---

## 快速开始 / Quick Start

1. 打开 `点我运行_修复_注释与文案优化版.html`。/ Open the HTML file in a modern browser.
2. 选择或拖拽 TXT/CSV/XLS/XLSX。/ Choose or drag‑drop a file.
3. 若出现“列映射”，确认姓名/学号列。/ If mapping appears, confirm name/id columns.
4. 选择抽取范围（学号区间或姓名勾选），必要时填写排除名单。/ Choose range (ID or Name), fill exclusion if needed.
5. 设置连抽次数、是否同轮不重复、（可选）随机种子。/ Set count, no‑repeat, optional seed.
6. 点击“开始抽取”。中央与结果屏同步显示。/ Click Start; center row matches the result screen.
7. 需要记录时，点击“导出中奖记录”。/ Export CSV when needed.

> 提示 / Tip：用“用最小/最大填充”快速填好学号上下界。 / Use the Min/Max button to auto‑fill ID bounds.

---

## 数据格式与导入 / Data Formats & Import

**TXT/CSV**（每行一条 / one per line）：

```
张三,10001
10002,李四
王五
```

* 可用中文逗号、制表符或空白分隔；空行会被忽略。/ Chinese comma, tabs or spaces are fine; blank lines ignored.

**Excel（XLS/XLSX）**：读取第一个工作表 / first sheet; 支持：

* 带表头：`学号 | 姓名 | …`（自动识别关键词）。/ With headers (`ID | Name | …`).
* 无表头但两列成组：`序号 | 姓名 | 序号 | 姓名 | …` / No header but (index|name) pairs.

> **显示标签 / Label**：`姓名（学号）`；若缺失则显示现有字段。/ `Name (ID)`; fallback to existing field.

---

## 编码识别策略 / Encoding Strategy

* 优先识别 BOM：UTF‑8 / UTF‑16LE / UTF‑16BE。/ Prefer BOMs first.
* 否则在候选集中打分选择：少 `�`、少控制符、多 CJK、UTF‑8 略加权。/ Score candidates by replacements/controls/CJK presence; favor UTF‑8.
* 全部失败则回退默认解码；建议改存 UTF‑8 或用 Excel 导入。/ Fallback to default; try saving as UTF‑8 or import via Excel.

> 基于浏览器 `TextDecoder`；个别编码标签可用性与浏览器有关。/ Depends on browser `TextDecoder` support.

---

## 列映射与样例 / Column Mapping & Examples

* 检测到表头后显示映射区；从下拉选择“姓名/学号”列，点击“应用映射”。/ Mapping UI appears after header detection; select name/id, click Apply.
* 为简洁，请按新映射**重新导入一次**文件以完全生效。/ Re‑import once to fully apply mapping.

**示例 / Example**：

```
学号,姓名,班级
10001,张三,A1
10002,李四,A1
```

---

## 抽取范围设置 / Draw Range Setup

* **按学号 / By ID**：输入起始/结束（含边界）；仅在**已导入**学号内抽。/ Enter min/max (inclusive); draws from **existing** IDs only.
* **按姓名 / By Name**：勾选参与者；搜索框即时过滤（不改勾选状态）。/ Check names; search filters visually.
* **排除名单 / Exclusion**：姓名或学号；空格/逗号/中文逗号分隔。/ Names or IDs; separated by spaces/commas.

> 两种模式互斥；切换后自动重算候选并刷新滚动条。/ Modes are exclusive; switching recomputes candidates and refreshes the strip.

---

## 抽取与可复现性 / Drawing & Reproducibility

* **连抽次数 / Consecutive count**：一次点击多次抽取；历史逐条记录。/ Multiple draws per run; each recorded.
* **同轮不重复 / No‑repeat per run**：同一轮不会重复同一人。/ No duplicate winners in the same run.
* **随机种子 / Seed**：任意字符串 → `mulberry32` PRNG → 同数据+同设置得到相同结果。/ Any string → deterministic PRNG → same results given same data & settings.

---

## 可视化与一致性 / Visualization & Consistency

* 动画基于 `requestAnimationFrame` + `transform: translateY`。/ Animation via rAF + CSS transform.
* 抽取前对候选集**拍快照**并**重排滚动条**，确保**中央=结果**。/ Snapshot candidates and reorder the strip so **center equals result**.
* 采用 `easeOutCubic` 减速，预定圈数与路程，避免浮点累积误差。/ Uses ease‑out cubic and precomputed distance to avoid drift.

---

## 导出中奖记录 / Export Results

* 生成 `winners.csv`，包含：**时间/序号/姓名/学号/显示标签**。/ Exports `winners.csv` with time/index/name/id/label.
* 编码 UTF‑8；Excel 乱码请用“数据 → 自文本/CSV → UTF‑8”。/ If garbled in Excel, import as UTF‑8.

---

## 可访问性与交互 / Accessibility & UX

* 关键控件带 `title` 与 `aria-*`；结果屏使用 `aria-live="polite"`。/ `title` & `aria-*`; result area is a live region.
* 拖拽高亮、聚焦态样式清晰。/ Drag‑over highlight and clear focus states.

---

## 浏览器兼容性 / Browser Compatibility

* 推荐 Chrome/Edge（近两年）或 Firefox ESR+。/ Chrome/Edge (recent) or Firefox ESR+ recommended.
* 移动端可用，桌面更佳。/ Mobile works; desktop preferred.
* 个别编码在旧浏览器可能不可用。/ Some encodings may be unsupported on older browsers.

---

## 性能建议 / Performance Tips

* 滚动条 DOM 上限：`MAX_N_DOM = 400`。/ Strip DOM cap to avoid memory/layout spikes.
* 大名单建议：缩小候选范围、利用搜索、降低 `--cell-h`、关闭其他重资源页面。/ Narrow candidates, use search, reduce row height, close heavy tabs.

---

## 常见问题（FAQ） / FAQ

**Q1：导入后乱码？ / Garbled text?**
A：优先 Excel 导入或另存为 UTF‑8；避免混用制表符与空格。/ Prefer Excel or save as UTF‑8; avoid mixed separators.

**Q2：未解析到有效数据？ / No valid rows?**
A：确保每行至少有姓名或学号；空白行会被忽略。/ Ensure each line has name or ID; blanks ignored.

**Q3：候选=0？ / Candidates=0?**
A：检查范围模式、学号上下界、排除名单、姓名勾选。/ Check mode, ID bounds, exclusions, selections.

**Q4：动画与结果为何一致？ / Why does UI match result?**
A：抽取前依据目标索引重排 strip，停止时中央即结果；下方结果屏只是同步显示。/ Strip is reordered to land the winner at center; result screen is synced.

**Q5：导出无响应？ / Export not working?**
A：允许浏览器下载；移动端可能拦截，推荐桌面浏览器。/ Allow downloads; mobile may block.

**Q6：如何复现实验过程？ / How to reproduce runs?**
A：使用相同种子、相同数据与设置（模式、次数、排除…）。/ Same seed, data and settings.

---

## 配置速查（JS/CSS） / Config Cheatsheet

**JS**：`VISIBLE_ROWS=3`；`CENTER_INDEX=1`；`COPIES=3`；`MAX_N_DOM=400`。
**CSS**（`:root`）：主题色 `--accent-*`；行高 `--cell-h`（默认 56px）；宽度 `--reel-w`；姓名网格列数 `--name-cols`。

---

## 二次开发建议 / Dev Notes

* **文案/i18n 外置 / Externalize copy & i18n**：汇总到 `messages/i18n` 对象以便多语言切换。
* **主题切换 / Theme switch**：基于 CSS 变量与 `data-theme`。
* **视图替换 / View swap**：保留“快照→目标索引→重排 strip”流程；可替换为翻牌/粒子等动画。
* **调试开关 / Debug toggle**：在导入→映射→候选→抽取→导出各阶段统一日志。

---

## 安全与隐私 / Security & Privacy

* 全程本地运行，不上传任何数据。/ Everything runs locally; no uploads.
* 导出的 CSV 含中奖记录，请妥善保管。/ Keep exported results private as needed.

---

## 版本记录与致谢 / Changelog & Credits

* **V3（2025‑10‑19）**：增强注释与文案、可访问性提示、失败引导语；微调动画与交互（不改逻辑）。
* 依赖 / Dependency：`xlsx`（SheetJS，CDN）。感谢开源社区。/ Thanks to open‑source.

---

## 许可 / License

* 未显式开源许可，默认**仅限个人/内部使用**。/ No explicit license; default personal/internal use.
* 若需开源分发，建议添加 **MIT** 或 **Apache‑2.0** 并在 README 中声明。/ For open distribution, add MIT/Apache‑2.0 and state it here.

---

**联系 / Work with us**
需要中英对照更学术/更活泼版本，或把文案外置、加入多语言切换、增加“全局不重复/权重抽样/分组名额”等功能，请告知具体偏好；在**零破坏现有功能**前提下迭代优化。 / Want a more formal/fun bilingual tone, externalized copy, multi‑language toggle, or features like global no‑repeat/weighted sampling/group quotas? Tell me your preferences and we’ll enhance it **without breaking current features**.
