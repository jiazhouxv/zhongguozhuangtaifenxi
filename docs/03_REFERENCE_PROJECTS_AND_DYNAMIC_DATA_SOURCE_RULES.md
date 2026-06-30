# 03｜GitHub 参考项目与动态数据源引用规则

> 本文档整理当前 GitHub 上接近本项目的公开项目，并说明哪些可以作为参考、哪些可以作为数据源、哪些不能直接动态引用。核心原则：参考项目可以借鉴思想和接口方式，但不能默认把别人的仓库当作稳定数据 API。

---

## 1. 当前结论

目前没有发现一个完全同主题的公开 GitHub 项目：

```text
专门针对中国状态，整合官方宏观数据、民间商业数据、外资流动、就业消费、地方债、房地产、社会压力、政策反应，并做动态可视化监测。
```

GitHub 上更常见的是以下几类：

1. 中国官方数据抓取工具。
2. 中国金融市场数据接口。
3. 房价或房地产局部数据项目。
4. FRED / World Bank / 全球经济 dashboard。
5. 量化金融数据平台。
6. 单一主题研究项目。

本项目的定位不是复制某个项目，而是做：

```text
中国状态分析 OS
= 多源数据支撑
+ 历史危机案例库
+ 动态数据采集
+ 风险评分
+ 可视化 HTML dashboard
+ 可隐藏/显示/扩展的数据支撑层
```

---

## 2. 参考项目分类

### 2.1 中国官方宏观数据类

| 项目 | 用途 | 可借鉴内容 | 局限 |
|---|---|---|---|
| `kinghchan/china-nbs-scraper` | 中国国家统计局数据抓取 | 官方宏观数据抓取方式 | 只覆盖官方统计局，不能代表真实状态 |
| `alan-chen-dongsheng/china-macro-quant` | 中国宏观量化方向 | 宏观指标组织方式 | 需要进一步核验内容和活跃度 |
| `changao1/70-China-cities-housing-index-data-by-national-bureau-of-statistics` | 70 城房价指数 | 房地产官方房价数据 | 仍是官方 NBS 口径，不能替代挂牌、成交、法拍 |
| `Zenia-Huang/china-housing-scraper` | 中国住房数据抓取 | 房产抓取思路 | 仓库很小，需要核验可用性 |

适合放入：

```text
第一道数据支撑：官方宏观数据
房地产官方指标层
```

不适合直接替代：

```text
民间房产真实流动性数据
法拍数据
贝壳/中介成交周期
银行抵押物处置
```

---

### 2.2 中国金融市场数据工具类

| 项目 | 用途 | 可借鉴内容 | 局限 |
|---|---|---|---|
| `akfamily/akshare` | 金融、宏观、商品、股票等数据接口 | 很值得优先研究，覆盖范围宽 | 数据源稳定性、接口变化和使用条款需要核验 |
| `waditu/tushare` | 股票、基金、财务、市场数据 | A 股、财务、行业数据 | 部分数据需要 token 或权限 |
| `shimencaiji/baostock` | A 股行情和财务数据 | 免费 A 股基础数据 | 覆盖面相对窄 |
| `microsoft/qlib` | AI 量化投资平台 | 数据管道、因子、回测、模型结构 | 偏量化交易，不是宏观状态分析 |

适合用于：

- 银行股压力监测。
- 地产股压力监测。
- 行业估值和利润预期。
- A 股行业景气。
- 商品价格。
- 债券、利率、市场情绪。
- 外资持股和市场风险偏好。

注意：金融市场数据只能作为风险信号，不等于实体经济事实。

---

### 2.3 通用经济 dashboard 类

| 项目 | 用途 | 可借鉴内容 | 局限 |
|---|---|---|---|
| `fred-economic-dashboard` 系列 | FRED 经济数据展示 | 时间序列图表、指标卡、宏观 dashboard | 多数偏美国，不针对中国 |
| `Global-Economic-Indicators-Dashboard` | 全球经济指标展示 | 多国指标结构和 UI 组织 | 不做中国风险链条 |
| `g20-economic-dashboard` | G20 指标 dashboard | 跨国比较 | 不做中国深度分析 |
| `Data-Driven-Economic-Research-Tool` | 数据驱动经济研究工具 | 研究工具结构 | 不针对中国 |

适合借鉴：

- 指标卡片。
- 趋势图。
- 多指标筛选。
- 国家比较。
- 时间序列可视化。
- 数据源说明。

不适合照搬：

- 风险逻辑。
- 中国本地数据源。
- 民间舆情和商业数据支撑。
- 历史危机相似度模型。

---

## 3. 能否把 GitHub 项目作为动态数据源？

### 3.1 结论

可以分三种情况：

| 类型 | 是否可动态引用 | 推荐方式 |
|---|---|---|
| 数据 API 工具库 | 可以间接引用 | 在本项目后端/脚本中调用其包或接口 |
| 静态数据仓库 | 可以引用但要缓存 | 下载/同步到本项目 data/raw 或 data/vendor |
| 代码示例/仪表盘项目 | 不建议动态引用 | 只作为架构参考，不作为运行依赖 |

不能做的事：

```text
不要让最终 HTML 页面直接依赖某个陌生 GitHub raw 文件作为实时数据源。
不要把第三方项目当成稳定生产 API。
不要绕过对方网站或数据源的服务条款。
不要在前端暴露需要密钥的 API token。
不要把不确定版权的数据直接内置发布。
```

---

## 4. 动态数据源接入分级

### Level 0：只作为文档参考

适用：

- 研究论文。
- 其他 dashboard 项目。
- 数据抓取代码示例。
- 可视化布局参考。

处理方式：

```text
在 docs 中记录链接、用途、可借鉴点。
不进入运行时依赖。
```

### Level 1：手动下载/人工更新

适用：

- 历史危机数据。
- 学术数据集。
- PDF 报告中的表格。
- 暂无稳定 API 的数据。

处理方式：

```text
data/manual/
  source_name/YYYY-MM-DD/file.csv
```

优点：可靠、可审计。
缺点：不实时。

### Level 2：定期同步到本仓库

适用：

- GitHub 上公开 CSV/JSON 数据。
- 官方开放数据。
- 可合法下载的公开数据。

处理方式：

```text
scripts/fetchers/fetch_xxx.py
→ data/raw/xxx/YYYY-MM-DD.csv
→ data/processed/xxx.parquet
```

优点：可追溯、可复现。
缺点：需要维护抓取脚本。

### Level 3：运行时 API 动态调用

适用：

- World Bank API。
- IMF API。
- FRED API。
- OECD API。
- 合法的金融数据 API。
- 自建后端缓存 API。

处理方式：

```text
后端或构建脚本调用 API
→ 缓存到 data/cache
→ 前端读取本项目生成的 JSON
```

注意：最终 HTML 页面最好读取本项目自己的 JSON，而不是直接请求一堆第三方 API。

### Level 4：实时商业数据接入

适用：

- 付费数据源。
- 招聘平台数据。
- 房产平台数据。
- 法拍数据。
- 舆情平台数据。
- 金融终端数据。

处理方式：

```text
合法授权
+ 后端采集
+ 缓存
+ 数据质量校验
+ 前端只读聚合结果
```

---

## 5. 推荐数据源配置格式

未来每个数据源应登记到：

```text
data_catalog/sources.yaml
```

示例：

```yaml
source_id: AKSHARE_CN_STOCK_MARKET
name: AkShare
category: financial_market
source_type: python_package
repository: akfamily/akshare
license: TBD
access_method: package_api
update_frequency: daily
runtime_usage: backend_fetch_only
allowed_for_frontend_direct_call: false
requires_api_key: false
legal_review_status: pending
reliability_score: 3
coverage:
  geography: China
  topics:
    - stock_market
    - commodity
    - macro
    - bond
risk:
  - upstream_interface_change
  - source_terms_unclear
  - data_quality_varies
notes: 适合作为金融市场和部分宏观数据的接口工具，但需要逐项确认数据源合法性和稳定性。
```

---

## 6. 数据源状态字段

每个数据源都要有状态，支持未来 HTML 页面隐藏或显示。

```yaml
status:
  enabled: true
  visible_in_dashboard: true
  included_in_score: true
  confidence_weight: 0.6
  last_successful_fetch: 2026-06-30
  last_error:
  stale_after_days: 7
```

含义：

| 字段 | 作用 |
|---|---|
| enabled | 是否启用数据源 |
| visible_in_dashboard | 是否在前端显示 |
| included_in_score | 是否参与风险评分 |
| confidence_weight | 数据源权重 |
| last_successful_fetch | 最近一次成功更新 |
| stale_after_days | 超过多少天视为过期 |

---

## 7. 动态引用架构建议

最终 HTML 页面不要直接依赖外部站点，而应采用如下结构：

```text
第三方数据源 / GitHub 项目 / 官方 API / 商业数据
        ↓
fetcher 脚本或后端采集器
        ↓
data/raw 原始数据存档
        ↓
data/processed 标准化数据
        ↓
data/signals 指标信号
        ↓
data/scores 风险评分
        ↓
public/data/*.json
        ↓
HTML 可视化页面读取本项目 JSON
```

优势：

1. 前端稳定，不受外部接口临时失败影响。
2. 每次数据更新可追溯。
3. 可以回放历史版本。
4. 可以隐藏某个数据源而不破坏页面。
5. 可以调整权重后重新生成评分。
6. 可以避免在前端暴露 API token。

---

## 8. 参考项目如何进入系统

### 8.1 可以直接使用的情况

满足以下条件时，可以作为依赖或数据接口：

- 开源许可证允许使用。
- 数据源服务条款允许抓取或调用。
- 接口稳定。
- 不需要非法绕过反爬。
- 不涉及个人隐私。
- 数据可以缓存和审计。
- 能记录原始来源和更新时间。

### 8.2 只能参考，不能直接使用的情况

以下情况只能作为参考：

- 项目没有明确许可证。
- 项目长期不维护。
- 抓取方式可能违反目标网站条款。
- 数据源需要登录或付费但没有授权。
- 数据包含个人信息。
- 原始数据来源不清楚。
- 代码只是课程作业或 demo。

---

## 9. 对当前发现项目的初步处理建议

| 项目 | 建议状态 | 说明 |
|---|---|---|
| `akfamily/akshare` | 候选数据接口 | 优先研究，适合金融市场和部分宏观数据 |
| `waditu/tushare` | 候选数据接口 | 需要 token 和权限，适合 A 股财务数据 |
| `shimencaiji/baostock` | 候选数据接口 | 免费 A 股数据，可作为补充 |
| `microsoft/qlib` | 架构参考 | 可参考数据管道和因子框架，不作为宏观数据源 |
| `kinghchan/china-nbs-scraper` | 参考/候选抓取器 | 可研究 NBS 抓取方式，但要优先使用官方 API 或稳定下载 |
| `changao1/70-China-cities-housing-index-data-by-national-bureau-of-statistics` | 候选静态数据 | 可作为官方房价历史数据备份 |
| FRED dashboard 类项目 | UI/结构参考 | 不作为中国数据源 |
| World Bank dashboard 类项目 | UI/结构参考 | 可借鉴 API 调用方式 |

---

## 10. 与最终 HTML 可视化的关系

最终页面应支持：

1. 数据源列表。
2. 每个数据源的启用/禁用。
3. 每个数据源的显示/隐藏。
4. 每个数据源是否纳入风险评分。
5. 每个数据源的权重。
6. 每个数据源最近更新时间。
7. 每个数据源的数据质量状态。
8. 每个指标背后的来源说明。
9. 风险评分随数据源调整自动变化。
10. 历史案例相似度随数据支撑变化自动变化。

示例：

```text
用户隐藏官方 GDP 数据，但保留房地产挂牌、法拍、招聘、外资流动数据
→ 页面重新计算“房地产压力”“居民资产负债表压力”“外资信心”模块
→ 总风险评分变化
```

---

## 11. 后续工程建议

先不要直接做大型系统。建议按最小可执行路径：

### 阶段 1：静态框架

- 完成 docs。
- 建立数据源目录。
- 建立历史案例目录。
- 建立指标目录。

### 阶段 2：手动数据 JSON

- 先用手工维护的 JSON 支撑 HTML 页面。
- 验证页面交互、隐藏/显示、权重、评分逻辑。

### 阶段 3：接入低风险 API

优先接入：

- World Bank。
- IMF。
- FRED。
- UNCTAD。
- UN Comtrade。
- AkShare 中可稳定调用且合法的数据。

### 阶段 4：接入中国本地替代数据

- 房地产挂牌。
- 法拍。
- 招聘。
- 消费。
- 舆情。

这些必须先做合法性和稳定性评估。

### 阶段 5：自动化更新和可视化发布

- GitHub Actions 定时抓取。
- 数据处理脚本生成 `public/data/*.json`。
- HTML 页面读取 JSON。
- 支持指标显示/隐藏、权重调整、风险评分。

---

## 12. 核心原则

```text
第三方 GitHub 项目可以借鉴；
第三方 GitHub 数据可以在许可证允许时缓存；
第三方抓取工具可以研究，但不能默认合法稳定；
最终前端不要直接依赖外部不稳定接口；
所有数据必须有来源、更新时间、权重、可信度和启用状态。
```
