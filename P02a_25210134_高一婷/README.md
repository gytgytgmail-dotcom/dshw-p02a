# P02a: 金融数据获取、管理与分析

## 项目说明

本项目为金融数据科学课程作业 P02a，构建可复现的金融数据分析流水线。

## 目录结构

```
hw/
├── data/
│   ├── raw/               # 原始数据（不提交GitHub，见.gitignore）
│   │   ├── stocks/        # 10只A股日度OHLCV（akshare前复权）
│   │   ├── index/         # 沪深300、上证指数
│   │   ├── macro/         # CPI月度、M2货币供应量
│   │   ├── financial/     # 个股财务指标（近5年）
│   │   └── download_log.csv  # 下载日志（成功/失败记录）
│   ├── interim/           # 中间计算结果
│   └── processed/         # 清洗后数据（CSV + SQLite）
│       ├── stocks_long.csv
│       ├── stocks_wide.csv
│       ├── hs300.csv
│       ├── cpi.csv
│       ├── m2.csv
│       ├── findata.db          # SQLite数据库
│       ├── descriptive_stats.csv
│       └── capm_results.csv
├── notebooks/
│   ├── 01_data_acquisition.ipynb   # Part1-2：数据获取
│   ├── 02_cleaning_storage.ipynb   # Part3：清洗与存储
│   └── 03_analysis_capm.ipynb      # Part4-6：分析、可视化、CAPM
├── reports/
│   ├── figures/           # 图表输出（PNG）
│   └── P02a_report.html   # 独立HTML报告
└── README.md
```

## 股票列表

| 代码   | 名称     | 行业 |
|--------|----------|------|
| 600036 | 招商银行 | 银行 |
| 601398 | 工商银行 | 银行 |
| 600519 | 贵州茅台 | 白酒 |
| 000858 | 五粮液   | 白酒 |
| 600276 | 恒瑞医药 | 医药 |
| 000625 | 长安汽车 | 汽车 |
| 600887 | 伊利股份 | 食品 |
| 601088 | 中国神华 | 能源 |
| 600028 | 中国石化 | 能源 |
| 600941 | 中国移动 | 电信 |

**基准指数**：沪深300（000300）、上证指数（000001）

## 数据来源

| 数据类型 | 数据源 | 接口 |
|----------|--------|------|
| 股票日度OHLCV | AKShare | `stock_zh_a_hist` (前复权) |
| 市场指数 | AKShare | `stock_zh_index_daily` |
| CPI月度 | AKShare | `macro_china_cpi_monthly` |
| M2货币供应量 | AKShare | `macro_china_money_supply` |
| 财务指标 | AKShare | `stock_financial_analysis_indicator` |

**数据时间范围**：2020-01-01 至 2024-12-31（5年）

## 运行方式

```bash
# 安装依赖
pip install akshare pandas numpy matplotlib seaborn statsmodels scipy

# 顺序运行三个Notebook
jupyter notebook notebooks/01_data_acquisition.ipynb
jupyter notebook notebooks/02_cleaning_storage.ipynb
jupyter notebook notebooks/03_analysis_capm.ipynb

# 生成HTML报告
jupyter nbconvert --to html notebooks/03_analysis_capm.ipynb --output reports/P02a_report.html
```

## 数据清洗方法

1. **缺失值处理**：日期缺失行删除，价格序列前向填充
2. **日期标准化**：统一转为 `pd.Timestamp`，格式 `YYYY-MM-DD`
3. **数据类型转换**：OHLCV 强制转为 `float64`，无法转换的设为 NaN
4. **去重**：按日期去重，保留最后一条记录
5. **极端收益率标记**：日涨跌幅超±20% 的记录添加 `is_extreme=True` 标记
6. **宽长转换**：长格式（`stocks_long.csv`）与宽格式（`stocks_wide.csv`）双格式存储

## 存储格式对比

| 方法 | 文件 | 优点 | 适用场景 |
|------|------|------|----------|
| CSV（方法A） | `*.csv` | 直接查看、通用性强 | 小数据集、共享 |
| SQLite（方法C） | `findata.db` | 支持SQL查询、多表关联 | 多表查询、结构化分析 |


## GitHub仓库地址
https://github.com/gytgytgmail-dotcom/dshw-p01
---
*数据仅用于学术研究，不构成投资建议。*
