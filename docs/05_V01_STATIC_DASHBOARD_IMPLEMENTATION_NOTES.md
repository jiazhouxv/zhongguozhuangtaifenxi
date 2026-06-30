# 05｜V0.1 静态 Dashboard 实现说明

> 本文档记录第一版可预览 HTML Dashboard 的实现范围、文件结构、使用方式和后续扩展方向。

---

## 1. 本轮完成内容

V0.1 已完成一个可预览的静态 HTML 原型：

```text
public/index.html
```

页面读取：

```text
public/data/sources.json
public/data/risk_scores.json
public/data/historical_cases.json
public/data/dashboard_config.json
```

并实现：

1. 总风险评分动态计算。
2. 12 个风险模块展示。
3. 数据源启用/禁用。
4. 数据源显示/隐藏。
5. 数据源是否纳入评分切换。
6. 数据源权重滑块。
7. 模块分数随数据源变化重新计算。
8. 历史危机相似度展示。
9. 数据源参与情况表。
10. 根目录 `index.html` 自动跳转到 `public/index.html`。

---

## 2. 当前文件

```text
index.html
public/index.html
public/data/sources.json
public/data/risk_scores.json
public/data/historical_cases.json
public/data/dashboard_config.json
data_catalog/sources.yaml
data_catalog/indicators.yaml
data_catalog/risk_models.yaml
```

---

## 3. 当前评分逻辑

模块分数：

```text
module_score = sum(signal_score * signal_weight * source_weight * source_reliability)
               / sum(signal_weight * source_weight * source_reliability)
```

总分：

```text
total_score = sum(module_score * module_weight) / sum(module_weight)
```

解释：

- `signal_score`：某数据源对某模块给出的风险信号。
- `signal_weight`：该信号在模块内的重要性。
- `source_weight`：用户可调整的数据源权重。
- `source_reliability`：数据源可信度。
- `module_weight`：模块在总风险中的权重。

---

## 4. 当前限制

当前版本是产品原型，不是正式研究报告。

限制：

1. 使用样例数据，不是实时真实数据。
2. 没有接入真实 API。
3. 没有 GitHub Actions 自动抓取。
4. 没有历史数据回放。
5. 没有数据源合法性审查。
6. 没有独立测试脚本。
7. 没有复杂图表库，当前使用纯 HTML/CSS/JS。
8. 历史案例相似度为人工样例评分。

---

## 5. 预览方式

### 5.1 GitHub 页面内查看

可以打开仓库中的：

```text
public/index.html
```

但 GitHub 文件页面不会执行完整静态站点逻辑。

### 5.2 GitHub Pages 预览

建议在 GitHub 仓库设置中启用 Pages：

```text
Settings → Pages → Deploy from a branch → main → /root
```

启用后根目录 `index.html` 会跳转到：

```text
public/index.html
```

### 5.3 本地预览

如果本地 clone 仓库，可以运行：

```bash
python -m http.server 8000
```

然后打开：

```text
http://localhost:8000/public/index.html
```

不要直接双击 HTML 文件，因为浏览器可能阻止 `fetch('./data/*.json')`。

---

## 6. 下一步建议

### V0.1.1

- 增加 README 中的预览说明。
- 增加截图占位说明。
- 增加 `docs/06_V011_CODEX_TASK_PROMPT.md`，给 Codex 做后续工程任务。

### V0.2

- 建立 Python 数据处理脚本。
- 从 `data_catalog/*.yaml` 生成 `public/data/*.json`。
- 加入 schema 校验。
- 加入 GitHub Actions 手动触发构建。

### V0.3

- 接入第一个真实数据源，建议优先：World Bank 或 IMF。
- 不建议第一批就爬国内商业平台。

### V0.4

- 加入历史案例独立 JSON。
- 做中国当前状态与历史案例的向量相似度计算。

### V0.5

- 加入月度报告自动生成。
- Dashboard 支持导出 Markdown 报告。

---

## 7. 工程纪律建议

后续编码任务应遵循：

1. 先读 `README.md` 和 `docs/04_DYNAMIC_HTML_DASHBOARD_PRODUCT_ARCHITECTURE.md`。
2. 只改必要文件。
3. 不引入复杂框架，除非明确需要。
4. 不接入存在法律或反爬风险的数据源。
5. 所有数据源必须先进入 `data_catalog/sources.yaml`。
6. 所有前端数据必须来自 `public/data/*.json`。
7. 不在前端暴露 API key。
8. 所有评分逻辑必须可解释。
