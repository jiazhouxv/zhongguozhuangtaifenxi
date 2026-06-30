# 00｜Dashboard 预览快速说明

## 当前可预览入口

V0.1 页面：

```text
public/index.html
```

根目录入口：

```text
index.html
```

根目录 `index.html` 会自动跳转到 `public/index.html`。

---

## GitHub Pages 预览方式

在 GitHub 仓库中开启：

```text
Settings → Pages → Deploy from a branch → main → /root
```

开启后访问仓库对应的 GitHub Pages 地址即可。

如果 GitHub Pages 地址为：

```text
https://jiazhouxv.github.io/zhongguozhuangtaifenxi/
```

页面会自动跳转到：

```text
https://jiazhouxv.github.io/zhongguozhuangtaifenxi/public/index.html
```

---

## 本地预览方式

不要直接双击 HTML 文件，因为浏览器可能阻止读取 `public/data/*.json`。

本地 clone 后，在仓库根目录运行：

```bash
python -m http.server 8000
```

然后打开：

```text
http://localhost:8000/public/index.html
```

---

## 当前页面已支持

1. 总风险评分展示。
2. 12 个风险模块。
3. 数据源启用 / 禁用。
4. 数据源显示 / 隐藏。
5. 数据源是否纳入评分切换。
6. 数据源权重滑块。
7. 风险分随数据源状态重新计算。
8. 历史危机相似度展示。
9. 数据源参与情况表。

---

## 当前不是正式研究结论

V0.1 使用样例数据，目的是验证产品形态和交互逻辑。

正式版需要继续接入：

- 官方宏观数据抓取。
- 外资和国际收支数据。
- 企业利润和现金流数据。
- 债务与信用市场数据。
- 历史危机案例结构化数据。
- 合法合规的民间商业替代数据。
