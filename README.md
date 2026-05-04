# WeChat Paper Digest

`wechat-paper-digest` 是一个面向科研公众号写作流程的 Codex Skill。它可以把单篇论文 PDF、DOI 或论文链接整理成公众号推文前期素材，包括封面标题、论文标题翻译、摘要翻译、研究背景、3 个图表支撑的结果要点，以及结论与意义。

这个 skill 的目标不是生成泛泛的论文摘要，而是帮助科研团队快速得到可直接进入公众号编辑流程的中文素材包。

## 功能特点

- 根据第一作者和第一作者单位生成 30 字以内的公众号封面标题
- 翻译论文标题和摘要，并将摘要中的“我们/本研究”替换为具体研究主体
- 基于 Introduction 压缩生成 200-300 字研究背景
- 输出 3 个核心结果，每个结果必须绑定具体 Figure 或 Table
- 为每个结果补充图题或表题的中文翻译，便于人工定位截图
- 输出固定 Markdown 结构，方便后续复制到公众号排版流程

## 适用场景

- 读取论文 PDF，生成公众号内容素材
- 根据 DOI 或论文链接整理中文推文初稿材料
- 为组内论文分享、课题组公众号、科研资讯栏目快速准备内容
- 从论文图表中提炼 3 个适合展开成正文段落的核心结果

不建议用于：

- 多篇论文综述
- 整篇论文逐句翻译
- 学术同行评审、复现实验检查或方法学批判

## 安装方式

将本仓库克隆到 Codex skills 目录：

```bash
mkdir -p ~/.codex/skills
git clone https://github.com/xiuyangFeng/wechat-paper-digest.git ~/.codex/skills/wechat-paper-digest
```

如果已经安装过，可以进入目录后拉取更新：

```bash
cd ~/.codex/skills/wechat-paper-digest
git pull
```

## 使用方式

在 Codex 中给出论文 PDF、DOI 或论文链接，并明确调用该 skill，例如：

```text
使用 $wechat-paper-digest 阅读这篇论文 PDF，生成公众号推文素材。
```

或者：

```text
使用 $wechat-paper-digest 处理这个 DOI：https://doi.org/...
```

## 输出结构

该 skill 会固定输出以下 Markdown 结构：

```markdown
# 封面标题
...

# DOI链接
...

# 论文标题翻译
...

# 摘要翻译
...

# 研究背景
...

# 结果要点
1. ...
   标题：...
2. ...
   标题：...
3. ...
   标题：...

# 结论与意义
...
```

## 质量要求

该 skill 内置了较严格的执行检查，包括：

- 必须确认论文标题、作者、第一作者单位、摘要、引言、结论、图和表
- PDF 可读时，不能只基于标题和摘要生成最终内容
- 封面标题必须使用第一作者单位和第一作者
- 摘要翻译中具体研究主体只出现一次
- 结果要点必须恰好 3 条
- 每条结果都必须引用明确的 Figure 或 Table
- 找不到 DOI 时必须明确写出“未在论文中明确识别到 DOI”

## 文件结构

```text
wechat-paper-digest/
├── SKILL.md
├── README.md
├── LICENSE
└── agents/
    └── openai.yaml
```

## 贡献

欢迎提交 Issue 或 Pull Request 改进：

- 不同学科论文的输出模板
- 更细的图表结果提炼规则
- 更适合公众号排版的标题风格
- PDF 识别质量较差时的降级策略

## 许可证

本项目使用 MIT License。你可以自由使用、修改和分发，但请保留许可证声明。
