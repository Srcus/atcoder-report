# 发布到 GitHub

这个目录本身就是一个可以直接上传的仓库：

```
atcoder-report/
├── SKILL.md
├── README.md
├── LICENSE
├── requirements.txt
├── scripts/
├── references/
└── examples/
```

## 方式 A：网页上传（不用装任何工具）

1. 打开 https://github.com/new ，仓库名填 `atcoder-report`，Public。
2. 建好后点 **uploading an existing file**。
3. 把 `atcoder-report/` 里的**内容**（不是目录本身）拖进去，提交。

> 注意：GitHub 网页上传不支持空文件夹，`examples/cache/...` 这种深层目录拖拽一般没问题，但如果漏了文件，用 `git` 更稳。

## 方式 B：命令行（推荐）

需要先装 Git：

```powershell
winget install --id Git.Git -e --source winget
```

然后：

```powershell
cd atcoder-report
git init
git add .
git commit -m "feat: AtCoder ABC 代码诊断报告 skill"
gh auth login          # 首次需要
gh repo create atcoder-report --public --source . --push
```

如果已经有仓库：

```powershell
git remote add origin https://github.com/<你的账号>/atcoder-report.git
git branch -M main
git push -u origin main
```

## 方式 C：不装 Git，用 GitHub API

拿到一个带 `repo` 权限的 token 后，可以用脚本把每个文件 PUT 上去（适合服务器环境）。需要的话可以再补一个 `publish_api.py`。

## 建议的仓库信息

- 描述：`给 AtCoder Beginner Contest 选手生成中文代码诊断报告（DSH skill + CLI）`
- Topics：`atcoder`、`competitive-programming`、`dsh-skill`、`docx`、`education`
- Release：把 `atcoder-report-kit.zip` 作为 v1.0.0 的附件

## 验证发布

```powershell
git clone https://github.com/<你的账号>/atcoder-report.git test-clone
cd test-clone
python scripts/render_report.py --data examples/abc484_red_apple.json --cache examples/cache --out out
```

能生成 `out/red_apple_abc484_report.docx` 就说明仓库完整。
