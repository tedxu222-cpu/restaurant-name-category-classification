# 店家名称补全与描述类别分类

本项目以 `restaurants.csv` 和 `ratings.csv` 为基础，完成两项工作：

1. 利用评论补全部分缺失的店家名称；
2. 根据店名和评论内容，为每个店家新增 `描述类别`。

最终交付文件为 [data/final/restaurants_final.csv](data/final/restaurants_final.csv)，包含 `restId`、`name`、`描述类别` 三列。

## 结果概览

| 项目 | 结果 |
| --- | --- |
| 店家总数 | 243,247 |
| 初始缺失名称的店家 | 34,115 |
| 成功补全名称 | 1,267 |
| 类别数量 | 14 类（含“不确定”） |
| 最终“不确定”类别 | 8,534 |

名称补全先按候选名称的评论票数筛选；恰好两票的候选再使用大语言模型复核。类别分类先使用关键词规则；仅对规则无法判定的商家使用大语言模型复核。

## 目录说明

```text
店家/
├─ data/
│  ├─ raw/                 原始 restaurants.csv、ratings.csv
│  ├─ derived/             名称补全与规则分类产生的过程数据
│  ├─ final/               最终结果 restaurants_final.csv
│  └─ splits/rating_split/ 为便于查看而拆分的 ratings.csv
├─ notebooks/              四个数据处理 Notebook
├─ outputs/
│  ├─ name_completion/     名称候选、票数、补名证据及模型筛选结果
│  └─ category/            类别汇总、模型任务及模型分类结果
├─ report/                 实验报告
└─ README.md
```

为保护原始评论并避免上传大体量文件，GitHub 仓库只包含最终结果、必要的补名证据、类别汇总、Notebook 和实验报告；`data/raw/`、`data/derived/`、`data/splits/` 以及含评论文本的模型过程文件仅保留在本地。

## Notebook 运行顺序

1. `notebooks/restaurants_name_completion.ipynb`：提取名称候选、完成规则清洗，并生成补名草稿。
2. `notebooks/llm_two_vote_candidate_screening.ipynb`：可选。使用 DeepSeek 对两票名称候选进行语义筛选。
3. `notebooks/restaurants_category_classification.ipynb`：根据名称和评论执行关键词规则分类。
4. `notebooks/llm_category_classification.ipynb`：可选。使用 DeepSeek 对规则分类为“不确定”的商家进行复核，并生成最终文件。

两个大语言模型 Notebook 均需要自行提供有效的 DeepSeek API Key；密钥只在当前 Notebook 会话中输入，不应保存到项目文件。两份模型结果已在本地导出，但因包含评论文本，未上传至公开仓库：

- `outputs/name_completion/name_two_vote_llm_screened.csv`
- `outputs/category/category_llm_screened.csv`

若只需查看或提交最终结果，无需重新运行大语言模型 Notebook。

## 主要文件

| 文件 | 用途 |
| --- | --- |
| `data/raw/restaurants.csv` | 原始店家表 |
| `data/raw/ratings.csv` | 原始评分与评论表 |
| `data/final/restaurants_final.csv` | 最终补名并完成类别分类的店家表 |
| `outputs/name_completion/name_completion_fill_evidence.csv` | 每个补名结果的候选与票数证据 |
| `outputs/category/category_llm_screened.csv` | 大语言模型类别复核结果（仅本地保留） |
| `report/店家数据补全与类别分类实验报告.docx` | 实验报告 |

## 注意事项

- `ratings.csv` 约 1.8 GB，建议使用 Notebook 或代码读取；Excel 不适合直接打开全量文件。
- 四个 Notebook 的第一个代码单元格集中配置了项目路径。若移动整个项目目录，只需要更新其中的 `PROJECT_DIR`。
- `data/splits/rating_split/` 仅用于分段查看评论，不是处理流程的唯一数据来源。
