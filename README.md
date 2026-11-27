# AtCoder ABC 代码诊断报告

给 AtCoder Beginner Contest（ABC）的选手出一份中文代码诊断报告：自动抓取某场比赛的全部提交与源码，逐题给出结果、代码、卡在哪、怎么改，最后渲染成 Word。

这套东西原本是给 CSP-J 做代码诊断用的，改成 AtCoder 之后只保留了两件事：**把提交抓下来**、**把报告排好版**。中间的分析由 AI agent 读代码完成，这也正是它区别于普通"提交记录导出"的地方。

## 它会输出什么

每人一份 `docx`，结构是：

1. **成绩概览**：每题的分值、结果、用时、提交次数
2. **逐题分析**：题意 → 完整代码（模板折叠）→ 要点表 → 问题所在 / 为什么这样写是对的 → 建议
3. **总结**：这一场的失分结构 + 下一步动作

样例见 [`examples/`](examples/)。

## 快速开始

```bash
pip install python-docx

# 1. 抓提交（一场比赛、多个 handle）
python scripts/fetch_submissions.py --contest abc484 --users alice bob --out ./cache

# 2. 写分析数据（格式见 examples/abc484_red_apple.json）
#    这一步由 AI agent 读代码完成，也可以自己写

# 3. 渲染
python scripts/render_report.py --data data/alice.json --cache ./cache --out ./out
```

`fetch_submissions.py` 会把**每一次**提交都下载下来，不只是最后一次：

```
cache/abc484/alice/
├── attempts/
│   ├── abc484_a_01_WA.cpp
│   ├── abc484_a_02_AC.cpp
│   └── ...
└── index.json          # 每题尝试数、最终结果、得分、用时
```

这对写报告很关键：能对比"第一次为什么 WA、改了什么才过"，比只看最终 AC 代码有用得多。

## 作为 DSH skill 使用

把整个目录放到 `~/.dsh/skills/atcoder-report/`（或任意技能目录），之后直接说：

> 给 alice、bob 出一下 abc484 的报告

agent 会按 `SKILL.md` 里的流程走：抓取 → 读代码 → 写分析 JSON → 渲染。

## 数据来源与边界

- 提交记录：AtCoder Problems 公开 API（`kenkoooo.com`）
- 源代码：AtCoder 提交详情页（公开可访问，无需登录）
- 只抓得到公开提交；选手设了私密提交就抓不到
- 脚本限速 1.2 秒一次请求，请勿改成并发

## 目录

```
SKILL.md                     DSH 技能入口（工作流说明）
scripts/fetch_submissions.py 抓提交 + 源码
scripts/render_report.py     渲染 docx
scripts/docx_style.py        排版基础函数
references/writing-style.md  报告口径（中文、去 AI 腔）
examples/                    一份完整的数据样例
```

## License

MIT
