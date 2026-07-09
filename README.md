# 🎵 音乐热歌榜隐性规律探索报告

> 基于中国传媒大学新闻网 674 条数据的全流程分析：数据清洗 → AI 情感分析 → 词频挖掘 → 主题分类 → LLM 特征增强 → 可视化 → 图文报告 → GitHub Pages 部署

**在线报告**：[https://ahwaii.github.io/hot-chart-report/](https://ahwaii.github.io/hot-chart-report/)

---

## 技术栈

| 环节 | 工具 | 用途 |
|------|------|------|
| 数据分析 | Python 3.12 + pandas 3.0 | 全量处理 674 条 |
| 情感分析 | SnowNLP 0.12 | 正文情感打分（无监督） |
| 中文分词 | jieba 0.42 | 高频词统计 |
| 可视化 | Matplotlib 3.11 | 12 张 SVG 矢量图 |
| LLM 增强 | DeepSeek Chat / 规则引擎 | 30 条采样 × 3 并发 |
| 报告生成 | Markdown + HTML/CSS | 图文嵌入式报告 |
| 部署 | GitHub Pages | ahWaii/hot-chart-report |

## 选用的 Skill 说明

本项目在开发过程中加载并使用了以下 AI Agent Skill 技能包，以辅助关键环节的开发：

### 1. `frontend-design` — 前端设计 Skill

**Skill 核心指导原则：**
- 强调在设计实现前选择明确的审美方向（aesthetic direction），拒绝"AI 通用的平庸设计"
- 围绕版式、色彩系统（CSS 变量）、动效设计、空间构成、质感细节五大支柱构建界面
- 避免过度使用的字体、陈旧的配色方案和可预测的布局

**在本项目中的具体应用：**

| 应用环节 | Skill 指导 | 实现效果 |
|---------|-----------|---------|
| 审美方向选择 | 要求在编码前 commit to a BOLD aesthetic direction | 选择了 **Editorial/Magazine meets Data-Journalism** 方向：杂志风格排印、数据新闻式的信息层级、深色编辑器式英雄区 |
| 排版系统 | 强调 typography 为设计核心 | 使用 serif/sans 双字体系统（Georgia + PingFang SC），首字母标号排版章节，引用块使用斜体 |
| 色彩主题 | 使用 CSS 变量构建统一的色彩系统 | 定义了 `--black`、`--near-black`、`--gold`、`--accent` 等 12 个 CSS 变量，全局引用 |
| 空间构成 | 强调 generous whitespace 与 spatial composition | 章节之间 48px 纵向间距，pull-quote 使用 40px 左内边距 + 4px 红色竖线 |
| 差异化记忆点 | 追求 "What makes this UNFORGETTABLE?" | 暗色调英雄区 + 放射渐变的伪元素背景 + 金色题注 + 引用块大引号装饰 |
| 避免 AI 通用风格 | 明确要求 avoid clichéd color schemes and predictable layouts | 使用深红（`#c0392b`）+ 金色（`#d4a84b`）+ 米白背景（`#f7f6f3`）代替蓝色+白色的 AI 通用方案 |

**使用结果：** 最终生成的 `index.html` 在视觉上完全区别于自动化工具生成的"默认报告页面"，具有独立的编辑风格识别度和一致的视觉语言。

### 2. `customize-opencode`（未使用）

该 Skill 专用于 opencode 自身的配置（opencode.json、agents、skills、MCP servers 等），与本次数据分析报告项目不相关，故未加载使用。

## Skill 加载机制说明

在 opencode 开发环境中，通过以下方式加载 Skill：

```python
# Agent 内部等价操作
# 调用 skill 工具，传递 skill 名称
# opencode 会注入 skill 的完整指令和资源文件到对话上下文
```

加载后，Skill 的指令会作为对话上下文的系统级约束，指导 Agent 在生成代码时遵循特定的方法论和设计原则。

## 项目结构

```
📁 charts/
├── analysis.py                  # Python 全量分析脚本
├── data_quality.svg           # 数据质量问题
├── monthly_trend.svg           # 月度发布趋势
├── source_distribution.svg     # 来源分布
├── topic_distribution.svg      # 主题分布
├── pageview_distribution.svg    # 浏览量分布
├── title_length_views.svg      # 标题字数 vs 浏览量
├── scatter_title_views.svg    # 散点图
├── sentiment_distribution.svg  # 情感得分分布
├── topic_sentiment.svg         # 主题情感得分
├── sentiment_extremes.svg      # 情感极端值 Top 5
├── word_frequency.svg          # 高频词 Top 15
└── word_frequency_full.svg     # 高频词 Top 30

📁 1.11/
├── news.csv                    # 原始数据集（685 条）
├── news_enriched.csv          # LLM 增强数据集（30 条 × 12 字段）
├── enrich_batch.js            # 批量 LLM 增强脚本
└── api_key.txt                # API 密钥

📄 index.html                   # GitHub Pages 在线报告
📄 report.md                    # Markdown 原始报告
📄 README.md                    # 本文件
```

## 核心发现

| # | 发现 | 数据 |
|---|------|------|
| 1 | 浏览量集中 200-500（46.4%） | 长尾效应显著 |
| 2 | **16-25 字标题**浏览量最高 | 521.3 vs 465/479 |
| 3 | 情感得分均值 0.994 | 正面报道偏向极显著 |
| 4 | 来源集中于新闻网（92.9%） | 信源偏倚 |
| 5 | "发展""学院""教育"为语义核心 | 四大词簇 |
| 6 | 年发布量增长约 80% | 暑期不减反增 |
| 7 | 艺术创作情感最高（0.82） | 通知类最低（0.55） |
| 8 | 微博最适配（93.3%） | 短文本天然适配 |

---

*报告生成 2026-07-09 · AI Agent (opencode) + Python 3.12 + Node.js 24*