# 04｜动态 HTML 可视化产品架构

> 本文档定义本项目未来最终呈现形态：一个可动态更新、可切换数据源、可调整权重、可显示/隐藏数据支撑，并能自动影响风险评分和可视化结果的 HTML 页面。

---

## 1. 产品目标

最终产品不是一篇静态报告，而是一个动态可视化页面：

```text
中国状态分析 HTML Dashboard
```

核心能力：

1. 动态抓取和更新多源数据。
2. 支持官方数据、民间商业数据、外资数据、历史案例数据、论文研究数据等多层支撑。
3. 每个数据源可以新增、禁用、隐藏、显示、调整权重。
4. 数据源状态会直接影响最终图表和风险评分。
5. 用户可以看到每个结论背后的证据链。
6. 支持历史案例对照，例如“中国当前与日本 1990s 的相似度”。
7. 支持周度、月度、季度报告自动生成。

---

## 2. 系统分层

```text
Layer 0: Source Registry 数据源注册表
Layer 1: Raw Data 原始数据层
Layer 2: Processed Data 标准化数据层
Layer 3: Signals 指标信号层
Layer 4: Scores 风险评分层
Layer 5: Narrative 解释文本层
Layer 6: HTML Dashboard 可视化层
Layer 7: Report Export 报告导出层
```

---

## 3. 数据流

```text
官方 API / 商业数据 / GitHub 数据 / 手工数据 / 舆情数据 / 历史案例数据
        ↓
fetchers 采集脚本
        ↓
data/raw 原始数据存档
        ↓
processors 清洗、对齐、标准化
        ↓
data/processed 标准化时间序列
        ↓
signal_engine 指标异常识别
        ↓
risk_score_engine 风险评分
        ↓
public/data/*.json
        ↓
HTML Dashboard 前端读取
        ↓
用户切换数据源、权重、显示隐藏
        ↓
前端重新计算或请求预计算结果
```

---

## 4. 建议目录结构

```text
zhongguozhuangtaifenxi/
  README.md
  docs/
    01_HISTORICAL_CRISIS_CASE_DATABASE_FRAMEWORK.md
    02_RESEARCH_BIBLIOGRAPHY_AND_DATASETS.md
    03_REFERENCE_PROJECTS_AND_DYNAMIC_DATA_SOURCE_RULES.md
    04_DYNAMIC_HTML_DASHBOARD_PRODUCT_ARCHITECTURE.md
    indicators/
    history_cases/
    methodology/
  data_catalog/
    sources.yaml
    indicators.yaml
    risk_models.yaml
    case_templates.yaml
  data/
    raw/
    manual/
    processed/
    signals/
    scores/
  scripts/
    fetchers/
    processors/
    scoring/
    validators/
  public/
    index.html
    data/
      sources.json
      indicators.json
      risk_scores.json
      historical_cases.json
      dashboard_config.json
  reports/
    weekly/
    monthly/
    quarterly/
```

---

## 5. 数据源注册表设计

每个数据源必须先注册，再进入系统。

```yaml
source_id: CN_NBS_REAL_ESTATE_INVESTMENT
name: 国家统计局房地产开发投资数据
category: official_macro
source_type: official_api_or_download
provider: National Bureau of Statistics of China
url:
license:
access_method: fetcher_script
update_frequency: monthly
runtime_usage: backend_fetch_only
allowed_for_frontend_direct_call: false
requires_api_key: false
reliability_score: 0.55
bias_risk: official_smoothing
enabled: true
visible_in_dashboard: true
included_in_score: true
confidence_weight: 0.5
last_successful_fetch:
stale_after_days: 45
related_indicators:
  - real_estate_investment_yoy
  - new_home_sales_area_yoy
  - developer_funding_yoy
notes: 官方口径，必须与民间挂牌、成交、法拍数据交叉验证。
```

---

## 6. 指标注册表设计

每个指标都要结构化。

```yaml
indicator_id: REAL_ESTATE_LIQUIDITY_PRESSURE
name: 房地产真实流动性压力
category: real_estate
frequency: weekly/monthly
unit: score_0_100
inputs:
  - second_hand_listing_count
  - transaction_cycle_days
  - price_cut_ratio
  - foreclosure_count
  - failed_auction_ratio
  - mortgage_loan_yoy
calculation_method: weighted_composite
higher_is_worse: true
risk_thresholds:
  low: 20
  moderate: 40
  high: 60
  severe: 80
related_history_cases:
  - JAPAN_1990_BALANCE_SHEET_RECESSION
  - USA_2008_SUBPRIME_CRISIS
  - SPAIN_2008_PROPERTY_BANKING_CRISIS
visible_in_dashboard: true
included_in_total_score: true
```

---

## 7. 风险评分模型

### 7.1 模块评分

每个模块单独评分：

| 模块 | 分数范围 | 说明 |
|---|---:|---|
| 房地产流动性 | 0–100 | 越高说明卖不掉、跌价、法拍压力越强 |
| 地方财政 | 0–100 | 越高说明土地财政和城投压力越强 |
| 银行资产质量 | 0–100 | 越高说明坏账和利润压力越强 |
| 居民资产负债表 | 0–100 | 越高说明居民防御性越强 |
| 就业质量 | 0–100 | 越高说明岗位和工资压力越强 |
| 消费降级 | 0–100 | 越高说明低价化、防御性消费越强 |
| 企业利润现金流 | 0–100 | 越高说明企业经营压力越强 |
| 产能过剩 | 0–100 | 越高说明价格战和库存压力越强 |
| 外资信心 | 0–100 | 越高说明外资撤退或降配越明显 |
| 国际贸易竞争力 | 0–100 | 越高说明出口质量和外需压力越强 |
| 社会压力外溢 | 0–100 | 越高说明经济压力向社会层面传导越明显 |
| 政策反应强度 | 0–100 | 越高说明政策托底强度越大，也可能说明压力越大 |

### 7.2 总分

```text
Total Risk Score = Σ(模块分数 × 模块权重 × 数据源可信度修正)
```

建议初始权重：

| 模块 | 初始权重 |
|---|---:|
| 房地产流动性 | 0.14 |
| 地方财政 | 0.12 |
| 银行资产质量 | 0.12 |
| 居民资产负债表 | 0.10 |
| 就业质量 | 0.10 |
| 消费降级 | 0.08 |
| 企业利润现金流 | 0.08 |
| 产能过剩 | 0.07 |
| 外资信心 | 0.08 |
| 国际贸易竞争力 | 0.05 |
| 社会压力外溢 | 0.03 |
| 政策反应强度 | 0.03 |

权重未来必须可调整。

---

## 8. 数据源显示/隐藏机制

前端需要支持：

```json
{
  "source_id": "CN_NBS_REAL_ESTATE_INVESTMENT",
  "enabled": true,
  "visible": true,
  "included_in_score": true,
  "weight": 0.5
}
```

用户动作：

| 用户操作 | 系统反应 |
|---|---|
| 隐藏某个数据源 | 图表不显示该数据源，但可继续参与评分 |
| 禁用某个数据源 | 数据源不显示，也不参与评分 |
| 调低权重 | 风险评分中该数据影响下降 |
| 新增数据源 | 数据源进入注册表，经过校验后进入指标 |
| 数据源过期 | 页面显示 stale 状态，默认降低权重或剔除 |

---

## 9. HTML Dashboard 页面模块

### 9.1 总览页

- 总风险评分。
- 本月变化。
- 最高风险模块。
- 近 12 个月风险趋势。
- 三大核心结论。
- 数据源健康状态。

### 9.2 模块页

每个模块一页：

- 房地产。
- 地方财政与城投。
- 银行风险。
- 居民资产负债表。
- 就业。
- 消费。
- 企业利润。
- 产能过剩。
- 外资与资本流动。
- 国际贸易。
- 社会压力。
- 政策反应。

### 9.3 数据源页

- 数据源列表。
- 类型。
- 启用状态。
- 更新时间。
- 可信度。
- 偏差风险。
- 是否参与评分。
- 是否显示。

### 9.4 历史案例页

- 历史危机案例列表。
- 危机类型筛选。
- 国家/地区筛选。
- 与中国当前相似度。
- 关键指标对照。
- 案例详情。

### 9.5 证据链页

每个结论必须能展开证据：

```text
结论：房地产流动性压力上升
  ├─ 官方数据：房地产投资、销售面积、新开工
  ├─ 民间数据：挂牌量、降价比例、成交周期
  ├─ 法拍数据：法拍数量、流拍率
  ├─ 银行数据：抵押物处置、按揭贷款
  └─ 历史对照：日本 1990s、美国 2008、西班牙 2008
```

---

## 10. 推荐前端技术路线

### 10.1 最小版本

```text
纯 HTML + JavaScript + JSON + Chart.js 或 ECharts
```

优点：

- 简单。
- 可直接 GitHub Pages 部署。
- 不需要后端。
- 适合第一版验证。

### 10.2 中期版本

```text
Vite + React + ECharts/Recharts + JSON 数据
```

优点：

- 组件化。
- 支持复杂筛选和交互。
- 适合长期维护。

### 10.3 数据更新

```text
GitHub Actions 定时运行
→ Python fetchers 抓取数据
→ Python processors 清洗
→ 生成 public/data/*.json
→ GitHub Pages 自动更新
```

### 10.4 暂不建议

第一阶段不建议直接上：

- 复杂数据库。
- 用户登录。
- 大型后端。
- 实时流处理。
- 复杂机器学习模型。

原因：当前最重要的是框架、数据结构、可解释性和可验证性。

---

## 11. 数据文件建议

### 11.1 `public/data/sources.json`

```json
[
  {
    "source_id": "AKSHARE_CN_STOCK_MARKET",
    "name": "AkShare",
    "category": "financial_market",
    "enabled": true,
    "visible": true,
    "included_in_score": true,
    "weight": 0.6,
    "last_updated": "2026-06-30",
    "status": "ok"
  }
]
```

### 11.2 `public/data/risk_scores.json`

```json
{
  "as_of": "2026-06-30",
  "total_score": 67,
  "modules": [
    {
      "module_id": "real_estate_liquidity",
      "name": "房地产流动性",
      "score": 82,
      "trend": "up",
      "evidence_count": 12
    }
  ]
}
```

### 11.3 `public/data/historical_cases.json`

```json
[
  {
    "case_id": "JAPAN_1990_BALANCE_SHEET_RECESSION",
    "name": "日本 1990s 资产负债表衰退",
    "crisis_type": ["real_estate", "banking", "balance_sheet_recession"],
    "china_similarity_score": 0.82,
    "key_similarities": [
      "房地产泡沫破裂",
      "居民和企业降杠杆",
      "银行坏账延后确认",
      "低通胀压力"
    ]
  }
]
```

### 11.4 `public/data/dashboard_config.json`

```json
{
  "modules": {
    "real_estate_liquidity": {
      "visible": true,
      "weight": 0.14
    },
    "foreign_capital": {
      "visible": true,
      "weight": 0.08
    }
  },
  "sources": {
    "CN_NBS_REAL_ESTATE_INVESTMENT": {
      "enabled": true,
      "visible": true,
      "included_in_score": true,
      "weight": 0.5
    }
  }
}
```

---

## 12. 历史案例相似度模型

初期可以用人工评分，后续再自动化。

### 12.1 人工评分版

| 历史案例 | 房地产 | 银行 | 外债 | 通胀 | 财政 | 居民资产负债表 | 综合相似度 |
|---|---:|---:|---:|---:|---:|---:|---:|
| 日本 1990s | 5 | 4 | 1 | 4 | 3 | 5 | 高 |
| 欧债危机 | 2 | 4 | 2 | 1 | 5 | 3 | 中高 |
| 亚洲金融危机 | 3 | 4 | 5 | 2 | 2 | 2 | 中 |
| 美国 2008 | 5 | 5 | 1 | 1 | 3 | 4 | 中高 |
| 魏玛恶性通胀 | 0 | 1 | 2 | 5 | 5 | 1 | 低 |

### 12.2 自动评分版

未来可以将每个历史案例和中国当前指标映射成向量：

```text
[房地产压力, 银行压力, 外债压力, 汇率压力, 通胀压力, 财政压力, 居民资产负债表压力, 外资压力]
```

然后计算：

- Cosine similarity。
- Weighted distance。
- Rule-based similarity。

---

## 13. 最小可执行版本 V0.1

V0.1 只做静态 JSON + HTML，不接复杂自动抓取。

### V0.1 范围

1. 创建 `public/index.html`。
2. 创建 `public/data/risk_scores.json`。
3. 创建 `public/data/sources.json`。
4. 创建 `public/data/historical_cases.json`。
5. 页面显示：
   - 总风险分。
   - 12 个模块风险分。
   - 数据源列表。
   - 历史案例相似度。
   - 显示/隐藏数据源。
6. 使用静态 JSON 验证交互。

### V0.1 不做

- 不做真实自动抓取。
- 不做登录。
- 不做数据库。
- 不做复杂模型。
- 不做实时新闻抓取。
- 不做商业数据爬取。

---

## 14. V0.2 数据接入路线

优先接入低风险、稳定、合法数据：

1. World Bank WDI。
2. IMF IFS/BOP/IIP。
3. FRED。
4. UNCTAD FDI。
5. UN Comtrade。
6. AkShare 中明确可用的公开金融数据。
7. 手工维护的历史案例 JSON。

后续再考虑：

- 房地产挂牌。
- 法拍。
- 招聘。
- 消费平台。
- 舆情。

这些需要逐项评估合法性、稳定性和成本。

---

## 15. 关键设计原则

```text
1. 先框架，后数据。
2. 先静态 JSON，后动态抓取。
3. 先合法稳定数据，后商业/替代数据。
4. 每个数据源都必须可追溯。
5. 每个结论都必须能展开证据链。
6. 每个数据源都可以隐藏、禁用、调权重。
7. 风险评分不能是黑箱。
8. 历史案例不是装饰，而是相似度比较基准。
9. 前端只读本项目生成的 JSON，不直接依赖不稳定第三方接口。
10. 任何抓取都必须考虑许可证、服务条款、隐私和反爬规则。
```

---

## 16. 后续工程任务建议

如果进入开发，应拆成以下任务：

### Task 1：建立数据目录和 JSON schema

- `data_catalog/sources.yaml`
- `data_catalog/indicators.yaml`
- `data_catalog/risk_models.yaml`
- `public/data/*.json`

### Task 2：创建 HTML 静态可视化 V0.1

- 总览页。
- 模块卡片。
- 数据源开关。
- 历史案例相似度。

### Task 3：建立历史案例 JSON

- 先录入 20 个重点案例。
- 每个案例包含危机类型、关键词、相似度字段。

### Task 4：建立评分引擎原型

- 使用静态 JSON。
- 支持权重调整。
- 支持数据源启用/禁用。

### Task 5：接入第一个真实数据源

推荐从 World Bank 或 IMF 开始，而不是直接爬国内商业平台。

---

## 17. 当前状态

当前为产品架构文档，不含代码实现。下一步建议先创建：

```text
data_catalog/sources.yaml
data_catalog/indicators.yaml
public/data/risk_scores.json
public/data/sources.json
public/data/historical_cases.json
```

然后再做最小 HTML 页面。
