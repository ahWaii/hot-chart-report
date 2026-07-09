# 音乐热歌榜隐性规律探索报告

基于中国传媒大学新闻网数据的全流程分析：数据清洗 → AI 情感分析 → 词频挖掘 → 主题分类 → LLM 特征增强 → 可视化 → 图文报告。

👉 **[查看在线报告](https://weixinyu0.github.io/hot-chart-report/)**

## 技术栈

| 环节 | 工具 |
|------|------|
| 数据分析 | Python 3.12 + pandas 3.0 |
| 情感分析 | SnowNLP 0.12 |
| 中文分词 | jieba 0.42 |
| 可视化 | Matplotlib 3.11 (SVG) |
| LLM 增强 | DeepSeek Chat / 规则引擎 |
| 部署 | GitHub Pages |

## 报告截图

![报告预览](https://weixinyu0.github.io/hot-chart-report/charts/word_frequency_full.svg)

## 输出文件

- `charts/` — 12 张 SVG 可视化图表
- `index.html` — 图文并茂的在线报告
- `report.md` — Markdown 原始版报告