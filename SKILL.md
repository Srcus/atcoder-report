---
name: atcoder-report
description: 给 AtCoder Beginner Contest（ABC）的选手生成中文代码诊断报告（docx）：自动抓取某场比赛里该选手的全部提交与源码，逐题给结果、代码、卡点和改法。用户说"给某某出个 AtCoder 报告""看看这几个人 ABC 打得怎么样""分析一下这场比赛"时使用。
whenToUse: 用户给出 AtCoder handle（可多个）和一场 ABC（或让你选最近的），要求出代码分析/诊断报告。
---

# AtCoder ABC 代码诊断报告

一条流水线：**抓提交 → 读代码 → 写分析 → 渲染 docx**。抓取和渲染是脚本，中间的分析由你（agent）完成。

## 一、抓取

```bash
python <本技能目录>/scripts/fetch_submissions.py --contest abc484 --users alice bob --out ./cache
```

- 数据来源：AtCoder Problems 的公开 API（提交记录）+ AtCoder 提交详情页（源码，公开可访问）。
- 会把**每一次**提交都存下来：`cache/<contest>/<user>/attempts/<pid>_<序号>_<结果>.cpp`，并生成 `index.json`（每题尝试数、最终结果、得分、用时）。
- 输出示例：

```
abc484_a  4 次  WA/WA/WA/AC  → AC (100)
abc484_b  3 次  WA/WA/AC     → AC (200)
```

- 脚本每次请求间隔 1.2 秒，失败会重试；一场比赛几十人大概几分钟。
- **先跑这一步**，拿到每人的 `index.json`，再决定报告要写哪几题。

## 二、读代码、写分析

对每个选手：

1. 看 `index.json`，确定题号、尝试次数、最终结果、用时。
2. 读 `attempts/` 里的源码。**有多次提交的，对比第一次 WA 和最后一次 AC 的差异**——报告里最有价值的就是"他为什么卡住、改了什么才过"。
3. 需要验证结论时，本地编译跑一下（g++ 在 `w64devkit` 或系统 `g++`），必要时用 n≤6 的暴力对拍找反例。
4. 按 `examples/abc484_red_apple.json` 的格式写一份分析 JSON。

字段说明：

| 字段 | 说明 |
|---|---|
| `user` / `contest` / `contest_title` | handle、比赛 id、比赛全名 |
| `score` / `full` | 得分、满分（可省略 full） |
| `language` | 主要语言，写进封面 |
| `summary` | 一句话总评，写在成绩表下面 |
| `problems[]` | 每题：`pid` `letter` `title` `points` `result` `attempts` `ms` `file` `statement` `rows` `verdict` `advice` |
| `file` | 相对 `--cache` 的路径，如 `attempts/abc484_a_04_AC.cpp`（指向**最终**那次提交） |
| `rows` | 分析表，`[["要点","说明"], ...]`，3–5 行 |
| `closing[]` | 总结段落，2–3 段 |

## 三、渲染

```bash
python <本技能目录>/scripts/render_report.py --data data/alice.json data/bob.json --cache ./cache --out ./out
```

产出 `out/<user>_<contest>_report.docx`，排版和 CSP 那套一致（微软雅黑 + Consolas、A4、代码块带行号、表格统一宽度）。

## 四、写作口径

- 中文，老师的口气。不写"值得注意的是""综上所述""让我们"。
- 讲具体行号和数字，不讲感悟：说"第 21 行把 30 写成 8"，不说"读题不够仔细"。
- 不贴能力标签。卡住就写卡在哪；能过就写清楚为什么对。
- 分数低时先找"改一处就能拿分"的地方，报告的最后一段要给下一步动作。
- 对 AC 的题也要写 2–3 行"为什么这样写是对的"，别只夸一句"很好"。

## 五、注意

- 只支持公开可见的提交；选手设了私密提交的抓不到。
- 不要在一场比赛里请求过快，脚本已经限速，别改成并发。
- 报告里的题面请用自己的话概括，不要逐字翻译原题。
- 需要提交记录之外的练习数据（比如某题在别处的提交），让用户提供截图或文件。
