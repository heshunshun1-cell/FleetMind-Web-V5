# FleetMind Web V4 - Data Visualization Analytics Version

## 项目简介

FleetMind 是一个我自己一步步开发出来的物流车队数据分析 Web App。这个项目最开始只是一个简单的 Truck 单车分析工具，后来慢慢升级成了一个可以保存车辆数据、查看历史记录、生成 Fleet Dashboard，并进一步进行数据可视化分析的小型物流数据系统。

这个项目的灵感来自我之前接触过的物流和运输业务。我发现真实的物流管理并不只是看一辆车赚了多少钱，还要综合考虑收入、成本、利润率、风险等级、主要成本压力等因素。所以我希望用 Python 和 Flask 做一个简单但有实际意义的工具，把车辆运营数据变得更清楚、更直观。

V4 是在 V3 Dashboard 版本基础上的进一步升级，重点是加入 **Data Visualization 数据可视化功能**。相比 V3 主要展示汇总数字，V4 开始用图表展示车队数据，让数据分析结果更加直观，也更像一个真正的数据分析项目。

---

## 从 V3 到 V4：为什么要升级？

在 V3 版本中，我已经完成了 Fleet Dashboard。Dashboard 可以展示：

* Truck Count
* Total Revenue
* Total Cost
* Total Profit
* Average Profit Margin
* High Risk Trucks
* Best Performing Truck
* Lowest Margin Truck
* Most Common Cost Pressure

这些内容让项目已经不只是单车分析，而是可以从“车队整体”的角度理解数据。

但是我后来发现，只有数字还不够直观。比如：

* 哪辆车收入最高？
* 哪辆车利润最低？
* 有没有亏损车辆？
* 风险等级分布是否集中？
* 主要成本压力到底集中在哪一类？

这些问题如果只看表格或文字，需要用户自己慢慢比较。但如果用图表显示，就会一眼清楚。

所以 V4 的目标就是：
**把 FleetMind 从 Dashboard 数字总结，升级成带有数据可视化能力的 Analytics 工具。**

---

## V4 新增功能

V4 新增了一个独立的数据可视化页面：

```text
/analytics
```

对应页面文件：

```text
templates/analytics.html
```

新增图表保存目录：

```text
static/charts/
```

目前 V4 可以自动生成并显示四张图表：

### 1. Revenue by Truck

每辆车收入柱状图。

这个图可以帮助用户快速比较不同 Truck 的收入表现，判断哪辆车贡献的 revenue 更高。

生成文件：

```text
static/charts/revenue_chart.png
```

---

### 2. Profit by Truck

每辆车利润柱状图。

这个图不仅可以显示盈利车辆，也可以显示亏损车辆。比如如果某辆车 profit 是负数，柱子会向下，并且会直接显示负数数值。

这个功能让我觉得很有意义，因为真实业务中，发现亏损车辆比只看高收入车辆更重要。收入高不代表赚钱，利润和成本控制才是核心。

生成文件：

```text
static/charts/profit_chart.png
```

---

### 3. Risk Level Distribution

风险等级分布图。

风险等级固定显示四类：

```text
Excellent
Normal
Warning
High Risk
```

即使某一类当前车辆数量是 0，也会保留在图表逻辑中。这样做比只显示 CSV 里出现过的风险等级更专业，因为它保证了分析标准的一致性。

生成文件：

```text
static/charts/risk_chart.png
```

---

### 4. Cost Pressure Distribution

主要成本压力分布图。

这个图会统计每辆车的主要成本压力来源，例如：

```text
Fuel Cost
Repair Cost
Toll Cost
Salary Cost
Insurance Cost
```

目前测试数据中主要成本压力集中在 Fuel Cost，所以图表里显示 Fuel Cost 数量最高。以后如果加入更多车辆数据，这张图可以帮助分析车队最常见的成本问题。

生成文件：

```text
static/charts/cost_pressure_chart.png
```

---

## 技术实现思路

V4 没有大改 V3 的结构，而是在原来的项目基础上做增量升级。

整体流程是：

```text
读取 fleet_data.csv
↓
用 Python 处理车辆数据
↓
用 matplotlib 生成图表
↓
保存成 PNG 图片
↓
Flask 在 analytics.html 页面中显示图片
```

这样做的好处是结构比较清楚，也比较适合我目前的学习阶段。前端不需要引入复杂的 JavaScript 图表库，后端也不需要马上升级数据库。先用 CSV + matplotlib 把数据可视化跑通，是一个比较稳妥的进阶路线。

---

## V4 新增核心函数

在 `fleetmind_core.py` 中新增了以下函数：

```python
generate_revenue_chart()
generate_profit_chart()
generate_risk_chart()
generate_cost_pressure_chart()
```

这些函数分别负责读取 `fleet_data.csv`，提取对应字段，然后生成图表图片。

在 `app.py` 中新增了：

```python
@app.route("/analytics")
def analytics():
```

每次访问 `/analytics` 页面时，系统会重新生成最新图表，确保页面显示的是当前 CSV 数据对应的分析结果。

---

## 遇到的问题和解决过程

### 问题 1：CSV 字段名不匹配

一开始写 Revenue Chart 的时候，我本来想用 `truck_name` 作为横坐标，但后来检查 `fleet_data.csv` 后发现真实字段是：

```text
truck_id
```

如果继续使用 `truck_name`，程序会出现：

```text
KeyError
```

所以后来改成使用：

```python
row["truck_id"]
```

这让我意识到，做数据项目时不能凭感觉写字段名，必须先确认真实数据结构。

---

### 问题 2：为什么变量名要用复数

在写图表函数时，我使用了：

```python
truck_ids = []
revenues = []
profits = []
```

这是因为这些变量不是一个值，而是一组数据。比如：

```python
truck_ids = ["Truck 013", "Truck 014", "Truck 015"]
revenues = [260000, 240000, 38000]
```

这让我对 list 数据结构的理解更清楚了：
单个值用单数，一组值用复数，代码可读性会更好。

---

### 问题 3：matplotlib 导致 Flask 崩溃

V4 开发中遇到的最大问题是 matplotlib 在 Flask 中运行时，Mac 出现了类似下面的报错：

```text
NSWindow should only be instantiated on the main thread
```

这个问题的原因是 matplotlib 默认可能会尝试打开 GUI 图形窗口，但 Flask Web App 运行时不应该弹出图形窗口。

解决方法是在图表函数中加入：

```python
import matplotlib
matplotlib.use("Agg")
```

这样 matplotlib 就不会打开窗口，而是只在后台生成 PNG 图片。

这个问题让我学到一个很重要的点：
在 Web 项目里使用 matplotlib 时，要注意后端渲染模式，不能让它像本地画图程序一样打开窗口。

---

### 问题 4：亏损车辆不够明显

Profit Chart 一开始虽然可以显示负利润，但亏损车辆只是柱子向下，不够直观。

后来我在柱状图上加入了数值标签，让亏损车辆可以直接显示负数。例如：

```text
-5000
```

这样用户可以更明显地看到哪辆车在亏损。

这个小改动虽然代码不复杂，但我觉得很实用，因为真实的数据分析不只是把图画出来，还要让别人看得懂。

---

### 问题 5：Risk Chart 应该固定四个等级

一开始 Risk Chart 是根据 CSV 里出现的风险等级自动生成。如果某个等级没有出现，比如 `Warning`，图表就不会显示它。

后来我觉得这样不够规范，所以改成固定四个风险等级：

```python
risk_counts = {
    "Excellent": 0,
    "Normal": 0,
    "Warning": 0,
    "High Risk": 0
}
```

这样即使某个等级当前数量是 0，也能保持分析标准完整。

这让我意识到：
数据分析不仅要看现有数据，还要设计好分类标准和分析框架。

---

## 当前项目结构

```text
FleetMind-Web-V4

app.py
fleetmind_core.py
fleet_data.csv
fleet_history.txt
README.md

templates/
├── index.html
├── result.html
├── records.html
├── samples.html
├── compare.html
├── assistant.html
├── history.html
├── new_truck.html
├── dashboard.html
└── analytics.html

static/
├── style.css
└── charts/
    ├── revenue_chart.png
    ├── profit_chart.png
    ├── risk_chart.png
    └── cost_pressure_chart.png
```

---

## 当前技术栈

本项目目前使用：

* Python
* Flask
* HTML
* CSS
* CSV
* Jinja2
* matplotlib
* Git
* GitHub

---

## 我觉得 V4 的进步

从 V3 到 V4，我感觉这个项目明显更像一个真正的数据分析项目了。

V3 主要是把数据总结出来，告诉用户整体情况。
V4 则开始把数据可视化，让用户可以从图表中快速发现问题。

比如：

* Revenue Chart 可以看收入差异
* Profit Chart 可以看盈利和亏损
* Risk Chart 可以看车队风险结构
* Cost Pressure Chart 可以看主要成本压力集中在哪里

这说明 FleetMind 已经不只是一个普通 Flask 小网页，而是一个具备基本数据处理、数据分析和数据可视化能力的小型系统。

虽然这个项目目前还没有使用数据库、React、Docker 或云部署，但它已经完整体现了一个数据项目的核心流程：

```text
数据输入
↓
数据存储
↓
数据计算
↓
数据汇总
↓
数据可视化
↓
业务解释
```

对我来说，这个 V4 版本是一个很重要的进阶点。它让我把 Python、Flask、CSV、matplotlib 和前端页面真正连接到了一起，也让我更理解数据分析项目不是只写代码，而是要让数据服务于真实问题。

---

## Future Improvements

未来我希望继续把 FleetMind 升级到更完整的版本，比如：

* 使用 SQLite 或 PostgreSQL 替代 CSV
* 加入更多交互式图表
* 使用 JavaScript Chart.js 或 Plotly
* 加入车辆筛选功能
* 加入时间维度分析
* 加入真实车队运营数据
* 部署到云端，让别人也可以在线访问
* 未来甚至可以接入 AI API，生成自动化分析报告

---

## 总结

FleetMind V4 是我从基础 Web App 走向数据可视化项目的一次重要升级。

这个版本让我更清楚地理解了：

* 如何读取和处理 CSV 数据
* 如何用 matplotlib 生成图表
* 如何把图表接入 Flask 页面
* 如何通过数据可视化发现业务问题
* 如何一步步 debug 并解决真实开发问题

这个项目虽然还不是一个大型商业系统，但它已经有了比较完整的数据分析逻辑。对我来说，FleetMind V4 不只是一次代码升级，更是一次从“会写功能”到“会做数据产品”的进步。
