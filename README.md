# FleetMind Web V5.1

## 项目简介

FleetMind 是我一步一步做出来的一个物流车队运营分析项目。
最开始它只是一个简单的 Python 车辆成本分析程序，后来逐渐升级成 Flask 网页版、CSV 数据分析 Dashboard，到现在的 V5.1，已经变成了一个基于 SQLite 数据库的车队运营分析系统。

这个版本的核心升级是：

> 从 V4 的 CSV-based dashboard，升级成 V5.1 的 SQLite database-driven fleet analytics system。

简单来说，V4 主要是读取 `fleet_data.csv` 来展示数据；
V5.1 则使用 `fleetmind.db` 数据库来保存、读取、分析车辆运营记录。

---

## 从 V4 到 V5.1 的升级

### V4 是什么？

V4 已经可以做到：

* 使用 Flask 搭建网页
* 从 CSV 文件读取车辆数据
* 显示车辆记录
* 生成 Dashboard 指标
* 使用 matplotlib 生成收入、利润、风险、成本压力图表
* 显示亏损车辆和风险车辆

V4 对我来说已经不是一个普通 Python 小作业了，而是一个比较完整的 CSV 数据可视化项目。

但是 V4 也有一个明显限制：

> 数据主要依赖 CSV 文件，整体更像一个文件分析工具，不太像真实业务系统。

---

### V5.1 做了什么？

V5.1 的重点不是乱加功能，而是升级项目底层的数据结构。

我加入了 SQLite 数据库：

```text
fleetmind.db
```

并创建了 `trucks` 表，用来保存车辆运营数据。

现在系统的数据流变成：

```text
用户输入车辆数据
→ 保存到 SQLite 数据库
→ Records 页面读取数据库
→ Dashboard 页面计算数据库数据
→ Analytics 页面生成数据库图表
→ Insights 页面生成运营建议
```

这让 FleetMind 从一个 CSV dashboard 变成了一个更像真实系统的小型数据库项目。

---

## 主要功能

### 1. 新增车辆记录

用户可以在网页中输入一辆车的运营数据，包括：

* Truck ID
* Driver
* Route
* Revenue
* Fuel Cost
* Toll Cost
* Repair Cost
* Salary Cost
* Insurance Cost
* Other Cost

系统会自动计算：

* Total Cost
* Profit
* Profit Margin
* Risk Level
* Main Cost Pressure
* FleetMind Suggestion

并把新车辆保存到 SQLite 数据库中。

---

### 2. Records 页面

`/records` 页面会从 SQLite 数据库读取所有车辆记录。

这个页面可以查看每辆车的：

* 收入
* 总成本
* 利润
* 利润率
* 风险等级
* 主要成本压力

---

### 3. Dashboard 页面

`/dashboard` 页面会从数据库计算车队整体表现，包括：

* Truck Count
* Total Revenue
* Total Cost
* Total Profit
* Average Profit Margin
* High Risk Trucks
* Best Performing Truck
* Lowest Margin Truck
* Most Common Cost Pressure

这个页面可以快速了解整个车队目前的运营情况。

---

### 4. Analytics 页面

`/analytics` 页面会从 SQLite 数据库生成分析结果和图表。

目前包括：

* Revenue by Truck
* Profit by Truck
* Risk Level Distribution
* Cost Pressure Distribution
* Loss-making Trucks

这些图表仍然使用 matplotlib 生成，但是数据来源已经从 CSV 改成 SQLite 数据库。

---

### 5. Operational Insights 页面

这是 V5.1 新增的页面。

`/insights` 页面会根据数据库中的车辆数据，自动生成一些 rule-based operational insights。

例如：

* 哪辆车正在亏损
* 哪辆车是 High Risk
* 哪个成本类别是最常见压力
* 哪辆车表现最好
* 哪辆车应该优先检查

这不是真正的大模型 AI，但它已经是一个简单的 rule-based decision support system。

---

## 技术栈

这个项目目前使用了：

* Python
* Flask
* SQLite
* SQL
* HTML
* CSS
* Jinja2
* matplotlib

---

## 项目结构

```text
FleetMind-Web-V5/
│
├── app.py
├── fleetmind_core.py
├── database.py
├── init_db.py
├── fleetmind.db
├── fleet_data.csv
│
├── templates/
│   ├── index.html
│   ├── new_truck.html
│   ├── result.html
│   ├── records.html
│   ├── dashboard.html
│   ├── analytics.html
│   ├── insights.html
│   └── ...
│
├── static/
│   ├── style.css
│   └── charts/
│
└── README.md
```

---

## 如何运行

先安装 Flask 和 matplotlib：

```bash
pip install flask matplotlib
```

初始化数据库：

```bash
python3 init_db.py
```

运行项目：

```bash
python3 app.py
```

然后在浏览器打开：

```text
http://127.0.0.1:5001
```

---

## 我这次学到了什么

这次从 V4 升级到 V5.1，我最大的收获是理解了：

> 一个项目不只是页面好看，更重要的是底层数据结构要合理。

以前 V4 主要是 CSV 文件分析，数据能展示，但系统感还不够强。
V5.1 加入 SQLite 后，我第一次真正把：

```text
数据录入
数据保存
数据读取
数据分析
运营建议
```

串成了一个完整闭环。

这对我来说是一个很重要的进步，因为它更接近真实业务系统，而不是只停留在课堂练习。

---

## 当前版本总结

FleetMind V5.1 目前已经实现：

* SQLite 数据库支持
* 新车辆直接保存到数据库
* Records 页面数据库化
* Dashboard 页面数据库化
* Analytics 页面数据库化
* Insights 页面规则建议
* 从 V4 CSV dashboard 升级为 V5.1 database-driven analytics system

一句话总结：

> FleetMind V5.1 is a Flask-based fleet analytics dashboard using SQLite to store truck operating records, generate analytics charts, and provide rule-based operational insights.

---

## 下一步计划

后续可以继续升级：

* 添加 trips 表，记录每一次运输任务
* 添加 routes 表，分析不同线路表现
* 增加按 truck、route、month 的筛选功能
* 使用 Chart.js 或 Plotly 做交互式图表
* 加入更高级的 AI assistant
* 尝试做利润预测模型
* 部署到云端

V5.1 只是数据库化的第一步，后面还可以继续升级成更完整的物流数据分析平台。