# 中传要闻分析

> **一个从数据到部署的全流程 AI 协作实验**  
> 基于中国传媒大学新闻网 674 条数据，综合运用 AI 编程助手 + 数据科学工具链 + LLM API，完成数据清洗、情感分析、词频挖掘、主题分类、可视化、图文报告和 GitHub Pages 部署的全链路交付。

**在线报告**：[https://ahwaii.github.io/hot-chart-report/](https://ahwaii.github.io/hot-chart-report/)  
**仓库地址**：[https://github.com/ahWaii/hot-chart-report](https://github.com/ahWaii/hot-chart-report)

---

## 一、创意构思

### 1.1 选题动机

互联网音乐平台的热歌榜每天更新，数据量巨大但信息密度极低——榜单背后的"隐性规律"（如标题长度对浏览量的影响、情感基调与传播力的关联、发布时间与受众触达的关系）很少被系统性挖掘。本项目以**中国传媒大学新闻网**为数据切口，探索以下问题：

> **高校新闻传播中是否存在可量化的"隐形规律"？标题多长最有效？什么主题情感最高？什么平台最适配？**

这些问题在商业媒体领域已被广泛研究，但在高校新媒体场景下缺乏公开的数据驱动分析。

### 1.2 设计理念

项目遵循 **Data Journalism** 的核心理念：**用数据讲一个结构化的故事**。不是简单地罗列图表，而是构建一条从"数据现状"到"可执行建议"的叙事弧线：

```
数据概况 → 质量诊断 → 信源分析 → 时间规律 → 浏览量解密
→ 内容语义 → 情感倾向 → 主题交叉 → LLM 增强 → 结论建议
```

### 1.3 视觉呈现

报告前端采用 **Editorial/Magazine meets Data-Journalism** 审美方向，区别于常规 AI 生成的"蓝色+白色+卡片"模板式报告，通过以下手段建立独立视觉身份：

- 深色编辑式英雄区 + 放射渐变背景
- Serif/Sans 双字体排印系统（Georgia + PingFang SC）
- 深红（`#c0392b`）+ 金色（`#d4a84b`）+ 米白（`#f7f6f3`）色彩体系
- Pull-quote 引用块、章节徽章编号、斑马纹表格
- 微交互动效（hover 阴影过渡）

---

## 二、技术方案

### 2.1 系统架构

```
┌─────────────────────────────────────────────────────────────────────┐
│                        数据管道 (Pipeline)                          │
│                                                                     │
│  news.csv  ──→  pandas 清洗  ──→  SnowNLP 情感分析                 │
│  (685条)        去重/补缺失         (0~1 分)                        │
│                     │                                                │
│                     ├──→ jieba 分词 → 词频统计 (Top 15/30)          │
│                     ├──→ 规则主题分类 (11 类)                        │
│                     ├──→ Matplotlib 可视化 (12 张 SVG)               │
│                     └──→ enrich_batch.js 批量 LLM 增强              │
│                            (30条 × 3并发)                          │
│                               │                                       │
│                               └──→ 图文报告 (report.md + index.html)        │
│                                      │                                 │
│                                      └──→ GitHub Pages 部署          │
└─────────────────────────────────────────────────────────────────────┘
```

### 2.2 技术栈明细

| 层级 | 技术 | 版本 | 用途 |
|------|------|------|------|
| 运行环境 | Python | 3.12.10 | 数据分析与可视化 |
| 运行环境 | Node.js | 24.14.1 | LLM 批处理脚本 |
| 数据处理 | pandas | 3.0.3 | 数据清洗、聚合统计 |
| 情感分析 | SnowNLP | 0.12.3 | 无监督正文情感打分 |
| 中文分词 | jieba | 0.42.1 | 高频词统计、词频分析 |
| 可视化 | Matplotlib | 3.11.0 | 12 张 SVG 矢量图 |
| LLM 批次 | DeepSeek Chat API | — | 30 条数据特征增强 |
| LLM 回退 | 规则引擎 (mock) | — | API 不可用时的备用方案 |
| 源码控制 | Git | 2.54.0 | 版本管理 |
| 部署平台 | GitHub Pages | — | 静态站点托管 |
| 报告生成 | Markdown + HTML/CSS | — | 图文嵌入式报告 |

### 2.3 数据流说明

**输入数据：** `news.csv` — 685 条爬取自中国传媒大学新闻网，字段包括标题、链接、日期、来源、浏览次数、正文。

**清洗策略：**
- BOM 处理 → 删除缺失正文 → 去重（基于标题） → 异常日期丢弃
- 清洗损耗仅 **1.6%**（11 条重复标题），最终有效记录 **674 条**

**增强字段（LLM 批次或规则引擎）：**
- 摘要 · 标题党版本 · 小红书版本 · 适用场景 · 目标读者 · 传播力评分

**输出产物：**
- 12 张 SVG 图表、`report.md`、`index.html`、`news_enriched.csv`

### 2.4 费用控制策略

LLM API 批次环节采用以下措施控制成本：
- **分层采样**：30 条（约 4.4%），按来源分层 + 浏览量补足
- **并发控制**：3 路并发，减少等待时间
- **重试机制**：指数退避（2s → 4s），最多 3 次
- **进度持久化**：每成功一条即写入 `enrichment_progress.json`，中断可续跑
- **回退模式**：`USE_MOCK=true` 时使用规则引擎模拟，零 API 费用
- **费用预估**：运行前打印估算 token 和 USD（30 条约 $0.025）

---

## 三、所用 Skills 及使用方式

本项目在 AI Agent 协作过程中加载了以下 opencode 内置 Skill 技能包：

### 3.1 `frontend-design` — 前端设计 Skill

**Skill 来源：** opencode 内置 Skill（`/Users/yoyow/.agents/skills/frontend-design/`）

**核心指导原则：**

| 原则 | 说明 |
|------|------|
| 审美方向选择 | 实现前必须 commit to a BOLD aesthetic direction |
| 五大支柱 | Typography · Color System · Motion · Spatial Composition · Texture |
| 差异化 | 追求 "What makes this UNFORGETTABLE?" |
| 避免 AI 通用 | 拒绝 overused fonts, clichéd color schemes, predictable layouts |

**在本项目中的具体应用方式：**

1. **加载 Skill** — Agent 调用 `skill("frontend-design")`，Skill 指令注入对话上下文
2. **方向决策** — 在编码 `index.html` 前，基于 Skill 指导选择了 **Editorial/Magazine meets Data-Journalism** 方向
3. **CSS 设计系统** — 依照 Skill 的 Color Theming 要求，用 CSS 变量构建了 12 个设计 token
4. **排印系统** — 依照 Typography 支柱，设计了 Serif/Sans 双字体系统 + 章编号排版
5. **Space Composition** — Pull-quote 使用 40px 缩进 + 4px 红色竖线的杂志风格
6. **UI Polish** — Hover 阴影过渡动画、斑马纹表格、引用块圆角
7. **持续迭代** — 每次 `index.html` 修改都对照 Skill 原则检查：是否避免了 AI 通用风格

**未加载的 Skill：**
- `customize-opencode` — 专用于 opencode 自身配置（agent 配置、MCP servers、permission rules 等），与数据分析报告项目不相关

### 3.2 使用 Skill 的收益

| 维度 | 无 Skill 的默认输出 | 使用 Skill 后的输出 |
|------|---------------------|-------------------|
| 视觉风格 | 蓝色+白色卡片，线性布局 | 深红+金+米白，杂志排印 |
| 排版 | 系统默认字体 | Serif/Sans 双字体系统 |
| 色彩系统 | 硬编码颜色值 | 12 个 CSS 设计 token |
| 差异化 | 可预测的 AI 通用模板 | Pull-quote、章节编号、渐变 hero |
| 记忆点 | 无 | 暗色 hero + 金色题注 + 大引号 |

---

## 四、AI 协作过程概述

### 4.1 协作模式

本项目全程采用 **Human-Agent 共创作** 模式：

```
Human (需求提出 + 决策确认 + GitHub 操作)
    │
    ▼
Agent (代码生成 + 调试修复 + 部署执行)
    │
    ▼
Human (验收反馈 + 授权关键操作)
    │
    ▼
Agent (迭代优化 + 文档生成)
```

### 4.2 阶段时间线

| 阶段 | 交互次数 | 关键 Human 决策点 | Agent 交付物 |
|------|---------|-----------------|-------------|
| **1. 数据探索** | 3 轮 | 确认使用新闻网数据集 | 数据概况、字段列表 |
| **2. 清洗分析** | 5 轮 | 修复 3 处代码 bug | `analysis.py` + 12 张图表 |
| **3. 报告撰写** | 2 轮 | 确认报告结构和图表顺序 | `report.md`（图文版） |
| **4. LLM 扩展** | 4 轮 | 配置 API Key（失效导致回退USE_MOCK） | `enrich_batch.js` + 30 条增强数据 |
| **5. 部署 ** | 6 轮 | 1) 提供仓库名 2) 确认用户名 3) 手动创建仓库 4) 手动启用 Pages | `index.html` + Git 推送 |
| **6. Skill 应用** | 3 轮 | 确认审美方向（Editorial/Magazine） | 重构 `index.html` + 更新 README |
| **7. 文档导出** | 1 轮 | — | `prompt-log.md` |

### 4.3 关键修复记录

| 问题 | 发现时机 | 修复策略 |
|------|---------|---------|
| CSV 编码 BOM 导致 Python 读取出错 | 阶段 1 | `encoding='utf-8-sig'` 加载 |
| `stopwords` 变量名写为 `stop_words` | 阶段 2 | 统一变量名 |
| `range(len(words))` 使用了 jieba 返回的 518787 个元素而非 top 15 | 阶段 2 | 改为 `len(words_top)` |
| API key 401 认证失败 | 阶段 4 | 添加 `USE_MOCK=true` 规则引擎回退 |
| `processNews` 中 `client` 不在局部作用域 | 阶段 4 | 全局 `_client` + `setClient()` |
| `results.push(null)` 导致顺序错乱 | 阶段 4 | 改为 `resultMap = new Map()` |
| GitHub 仓库名与用户名初始猜测错误 | 阶段 5 | 经 2 次询问确认实际用户名和仓库名 |

### 4.4 人机分工

| 任务 | 负责人 | 理由 |
|------|--------|------|
| 选择分析方向 | Human | 需要领域知识和价值判断 |
| 编写分析脚本 | Agent | 高效生成结构代码 |
| 调试语法错误 | Agent | 快速识别并修复 |
| 修复逻辑 bug | Agent + Human | Agent 定位，Human 确认修复方案 |
| 选择视觉方向 | Human | 审美偏好是人类的决策 |
| 实现 CSS 设计系统 | Agent | Skill 指导下的精确实现 |
| 创建 GitHub 仓库 | Human | GitHub 需登录认证（Agent 无法操作） |
| 启用 GitHub Pages | Human | GitHub UI 操作需人工 |
| 生成文档 | Agent | 自动汇总全流程信息 |
| 验收成果 | Human | 终验确认 |


### 5.2 核心发现速览

| # | 发现 | 数据支撑 |
|---|------|---------|
| 1 | **16-25 字标题**浏览量最高（521.3） | 分组图 |
| 2 | 情感得分均值 **0.994** | 正面偏向极显著 |
| 3 | 浏览量集中 200-500（46.4%） | 长尾效应 |
| 4 | 来源集中于新闻网（92.9%） | 信源偏倚 |
| 5 | "发展""学院""教育"为语义核心 | 四大词簇 |
| 6 | 年发布量增长约 **80%** | 月度趋势 |
| 7 | **艺术创作**情感最高（0.82），通知类最低（0.55） | 主题情绪图 |
| 8 | **微博**最适配（93.3%） | LLM 增强分析 |

---

## 六、项目结构

```
📦 hot-chart-report/
├── charts/
│   ├── analysis.py                  # Python 全量分析脚本
│   ├── prompt-log.md                # 完整 Prompt 交互记录
│   ├── data_quality.svg           # 数据质量问题
│   ├── monthly_trend.svg           # 月度发布趋势
│   ├── source_distribution.svg     # 来源分布
│   ├── topic_distribution.svg      # 主题分布
│   ├── pageview_distribution.svg    # 浏览量分布
│   ├── title_length_views.svg      # 标题字数 vs 浏览量
│   ├── scatter_title_views.svg    # 散点图（字数×浏览量×情感）
│   ├── sentiment_distribution.svg  # 情感得分分布
│   ├── topic_sentiment.svg         # 主题情感得分
│   ├── sentiment_extremes.svg      # 情感极端值 Top 5
│   ├── word_frequency.svg          # 高频词 Top 15
│   └── word_frequency_full.svg     # 高频词 Top 30
├── 1.11/
│   ├── news.csv                    # 原始数据集（685 条）
│   ├── news_enriched.csv          # LLM 增强数据集（30 条 × 12 字段）
│   ├── enrich_batch.js            # 批量 LLM 增强脚本
│   ├── api_key.txt                 # API 密钥
│   └── enrichment_progress.json   # LLM 执行进度文件
├── index.html                      # GitHub Pages 在线报告
├── report.md                       # Markdown 原始报告
├── README.md                       # 本文件
└── .git                            # Git 仓库
```

## 七、部署

- **在线报告**: [https://ahwaii.github.io/hot-chart-report/](https://ahwaii.github.io/hot-chart-report/)
- **部署方式**: GitHub Pages（main 分支，/ root 目录）
- **启用方式**: 在 GitHub 仓库 Settings → Pages 中手动选择 main 分支启用

---

## 八、许可证

本项目采用 MIT License。

---

*报告生成 2026-07-09 · AI Agent (opencode) + Python 3.12 + Node.js 24 · 部署于 GitHub Pages*