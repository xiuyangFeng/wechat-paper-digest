---
name: wechat-paper-digest
description: Use when the user wants to turn a research paper PDF, DOI, or paper link into WeChat-publication-ready Chinese content, including downloading the paper, recording processed articles, and producing a short cover title, translated title, translated abstract, expanded research background, and 3 figure/table-backed core results.
---

# WeChat Paper Digest

## Overview

Use this skill when the user gives you a paper PDF, DOI, or paper URL and wants公众号内容素材 rather than a generic paper summary.

This skill produces a fixed output package for a single paper:
- downloaded PDF when the input is a DOI or paper link and a PDF is available
- extracted text file for local reading/search when PDF text extraction works
- processed-article registry update to avoid duplicate future work
- cover title
- translated paper title
- translated abstract
- 500-700 character research background
- 3 core results, each tied to a specific figure or table

## When To Use

Trigger this skill when the user asks for any of the following:
- read a paper PDF and generate公众号内容
- process a DOI or paper link into Chinese summary materials
- extract title, abstract, background, and result highlights from a paper
- create a cover title for a paper in the style of `XX大学XX等人...`

Do not use this skill for:
- multi-paper literature reviews
- full academic translation of the entire paper
- peer review, technical critique, or reproducibility analysis unless the user separately asks for those

## Inputs

This skill supports two entry modes:

### 1. PDF input

If the user provides a PDF file:
- read the paper carefully before writing anything
- use the existing `pdf` skill workflow to inspect the paper content
- identify the DOI from the paper and return the DOI link in the final output

### 2. DOI or paper-link input

If the user provides a DOI, DOI URL, publisher URL, arXiv URL, or other paper link:
- resolve the paper first
- obtain and save the paper PDF where possible
- extract and save searchable text from the PDF where possible
- read the paper before generating output

## Local File And Registry Workflow

Before drafting, maintain a local paper package in the current working directory unless the user gives a different target folder.

### 1. Duplicate check

Before downloading or drafting:
- If `processed_articles.md` exists, search it for the DOI, arXiv ID, source URL, PDF filename, and normalized title keywords.
- If the paper is already marked `已完成`, tell the user which local material exists and do not regenerate unless they explicitly ask to redo it.
- If it is only `待确认`, `已处理-历史记录`, or missing a generated Markdown path, continue processing and update the row after completion.

### 2. Download and text extraction

For DOI or paper-link input:
- Save the PDF in the working directory using a stable identifier:
  - arXiv: `paper_<arxiv-id>.pdf`, for example `paper_2501.09046.pdf`
  - DOI: `paper_<doi-suffix-or-safe-title>.pdf`
  - publisher URL without DOI: `paper_<safe-short-title>.pdf`
- Extract text beside the PDF as `paper_<same-id>.txt` when possible.
- Prefer `pdftotext -layout <pdf> <txt>` for arXiv and other text PDFs; if unavailable or extraction is poor, use another reliable PDF text workflow and note the limitation.
- Do not draft from the abstract page alone when the PDF can be downloaded and read.

For PDF input:
- Keep or copy the PDF in the working directory only when needed for a stable local package; otherwise use the provided path.
- Still create a text extraction file next to the PDF when practical, because later figure/table searches depend on it.

### 3. Output file naming

When writing files, save the final公众号素材 as a Markdown file in the working directory:
- arXiv: `<short-topic>_<arxiv-id>_公众号素材.md`
- DOI/publisher paper: `<short-topic>_<safe-doi-or-title>_公众号素材.md`

Use short ASCII names where possible for the machine-readable identifier portion. Chinese is acceptable for the human-readable topic portion if it helps the user identify the paper.

### 4. Processed article registry

After generating the Markdown, create or update `processed_articles.md` in the working directory.

The registry should include:
- status
- de-duplication key, preferably `doi:<doi>; arxiv:<id>` when both exist
- English paper title
- source link
- local PDF/TXT/Markdown material paths
- processing date
- first author and first-author affiliation when known
- notes for missing DOI, poor text extraction, or incomplete historical metadata

Use this table shape when creating a new registry:

```markdown
| 状态 | 去重主键 | 论文标题 | 来源链接 | 本地材料 | 处理日期 | 备注 |
|---|---|---|---|---|---|---|
```

Use `已完成` only after the PDF/TXT, generated Markdown, and registry row have all been verified. Use `待确认` or `已处理-历史记录` when evidence is incomplete.

## Required Reading Process

Before generating the公众号内容, do all of the following:

1. Complete the duplicate check, download/text extraction, and local file setup described above.
2. Identify the paper title, authors, first author, first-author affiliation, abstract, introduction, conclusion, figures, and tables.
3. Confirm the first author's affiliation and translate the affiliation into Chinese.
4. Confirm the DOI if present.
5. Read enough of the paper to understand:
   - the research problem
   - the core method or intervention
   - the main findings
   - which figures or tables best support the core results

Do not generate the final answer from title plus abstract alone when the PDF or full paper is available.

## Output Contract

Always return content in exactly this structure:

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
   图/表标题：...
2. ...
   图/表标题：...
3. ...
   图/表标题：...

# 结论与意义
...
```

If the input is DOI/link and no DOI needs to be separately surfaced, you may still keep the `# DOI链接` section and fill it with the DOI URL when available.

## Section Rules

### 1. 封面标题

The cover title must obey all of these rules:
- format it as `XX大学` or `XX研究所` + `XX` + `等人` + `研究/探讨/开发/...` + a highly condensed research content summary
- use the first author's affiliation only
- translate the affiliation into Chinese
- use the first author only; all remaining authors become `等人`
- if the author's name is English, you may use only the surname or only the given name to control length
- keep the final title within 30 visible characters
- the title must reflect the full paper's core contribution, not just a vague topic label

Examples of style:
- `清华大学王等人探讨了肿瘤免疫机制`
- `麻省理工学院Li等人开发了柔性传感器`

### 2. 论文标题翻译

Translate the paper title into fluent, accurate Chinese.

If the source title is already Chinese or not English, provide a polished Chinese表述.

### 3. 摘要翻译

Translate the abstract into Chinese with these constraints:
- preserve the original meaning faithfully
- replace phrases such as `本研究` `我们` `本项目` with a concrete subject in the form `XX大学或XX研究所XX等人...`
- that concrete subject should appear only once in the abstract translation
- after the first use, continue naturally without repeating the full subject

### 4. 研究背景

Write a 500-700 Chinese-character background section based on the paper introduction.

Requirements:
- this is an expanded rewrite of the introduction for a WeChat article, not a general-pop summary
- use 2-3 natural paragraphs rather than a single dense paragraph
- explain the field problem and why it matters in practical, clinical, scientific, or application terms
- summarize the key prior progress, dominant methods, or known mechanisms that the introduction uses to frame the paper
- identify the unresolved gap, bottleneck, controversy, or technical challenge that motivated the study
- end by explaining what question or objective the paper set out to address, without previewing detailed results
- do not drift into detailed result reporting
- do not exceed 700 Chinese characters

### 5. 结果要点

Return exactly 3 core result bullets.

Each result bullet must:
- start with a short result title of 6-9 Chinese characters for display use
- describe one important conclusion from the paper
- explicitly cite a figure number or table number from the paper
- explain what that figure or table demonstrates
- include enough context for a公众号正文段落, not just a one-line conclusion
- be approximately 250 Chinese characters so the explanation is substantive
- mention the relevant metric, comparison target, dataset split, or experimental condition when the paper provides it
- explain why this result matters for the paper's main claim or application value
- translate the original figure caption, table title, or figure/table label into Chinese when citing it
- include that translated caption or title inline so the user can map the Chinese explanation back to the paper artifact
- present the translated figure/table caption or title as a separate lead-in line before the explanatory paragraph
- if one result bullet cites more than one artifact, provide a separate Chinese title for every cited artifact
- format multiple artifact titles explicitly, for example:
  - `图6标题：患者特异数据集中 CFD 真值与代表模型推断的 vFFR 图`
  - `表7标题：患者特异数据集中各模型对狭窄区域 vFFR 的诊断性能`
- do not collapse multiple cited artifacts into one generic title; a paragraph that mentions both `图6` and `表7` must include both `图6标题` and `表7标题`
- do not count the translated figure/table caption or title toward the approximately 250 Chinese characters of the result explanation itself
- be suitable for the user to locate and screenshot manually

Allowed style:
- `图2显示，该材料在...条件下的响应速度明显提升，说明...`
- `表1比较了...，结果表明...`

Not allowed:
- vague references such as `某图` `相关结果图`
- conclusions without a figure/table anchor
- result titles that are too long, too generic, or simply repeat the figure/table number
- more than 3 result bullets unless the user explicitly asks for more

### 6. 结论与意义

Write a separate conclusion-and-significance section after the 3 result bullets.

Requirements:
- be approximately 200 Chinese characters
- summarize the paper's most important method, result, and practical significance
- focus on the paper's single most important takeaway rather than repeating all result bullets
- explain why the work matters for the field, clinic, application, or future research direction
- read like the closing summary paragraph of a公众号 article
- avoid introducing brand-new claims that are not supported by the paper

## DOI Rules

When a DOI is found, return it as a DOI link, preferably in the form:

`https://doi.org/...`

If the input is a PDF and no DOI can be identified, explicitly write:

`未在论文中明确识别到 DOI`

## Failure And Degradation Rules

### Missing DOI

If no DOI is found:
- do not invent one
- state `未在论文中明确识别到 DOI`

### Low-quality scanned PDF

If the PDF is scanned or text extraction is poor:
- use the identifiable content as far as possible
- still try to determine the paper's core contribution and figure/table-backed results
- explicitly warn that some content may need manual review:
  `部分内容因识别质量受限，结论需人工复核`

### Non-English paper

If the paper is not in English:
- keep the same output template
- translate or normalize the title into polished Chinese as needed

### Figure/Table support

If result claims cannot be confidently tied to a specific figure or table:
- keep reading until you can identify 3 supported result points
- do not use ambiguous placeholders
- if the paper genuinely lacks enough identifiable figure/table anchors, say so clearly instead of fabricating evidence

## Writing Standard

The final output should be:
- concise
- publication-ready for a research公众号 workflow
- accurate to the paper
- written in clean Chinese
- with result bullets that are fuller and more explanatory than terse captions

Avoid:
- exaggerated media language
- unsupported inference
- direct copying of long paper sentences
- generic filler such as `具有重要意义`

## Execution Checklist

Before finishing, verify all of the following:
- duplicate check was performed against `processed_articles.md` when it exists
- DOI/link inputs have a downloaded PDF saved locally when available
- a searchable TXT extraction exists locally, or a clear extraction limitation is reported
- the final公众号素材 Markdown was saved in the working directory
- `processed_articles.md` was created or updated with the DOI/arXiv/source key and local PDF/TXT/Markdown paths
- the paper has actually been read, not just skimmed
- the first author and first-author affiliation are correct
- the affiliation is translated into Chinese
- the cover title is 30 characters or fewer
- the abstract translation uses the explicit subject only once
- the research background is 500-700 Chinese characters
- the research background uses 2-3 natural paragraphs and covers problem, prior context, gap, and study motivation
- there are exactly 3 result bullets
- each result bullet has a 6-9 Chinese-character title
- each result bullet is approximately 250 Chinese characters
- every result bullet points to a specific figure or table
- every cited figure or table includes its own original caption or title translated into Chinese
- when a result bullet cites multiple figures/tables, every cited artifact has a separate `图X标题` or `表X标题` line
- the translated figure/table caption or title is separated from the main result explanation and not counted toward the result word target
- the conclusion-and-significance section exists and is approximately 200 Chinese characters
- the DOI link is returned, or the missing-DOI fallback is shown
