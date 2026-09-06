# ptrade-knowledge-skill

**恒生 PTrade 量化交易平台 — AI Agent 智能知识技能包**

[![Version](https://img.shields.io/badge/version-0.1.0-blue.svg)]()
[![License](https://img.shields.io/badge/license-MIT-green.svg)](./LICENSE)

为 AI Agent 提供结构化的 PTrade 官方文档知识库，实现 API 查询、策略代码生成、事件生命周期约束、交易/行情函数用法、对象与数据字典查档、常见问题排查等能力。**本 Skill 仅基于本地知识库作答，不进行任何外部网络抓取**。

姊妹项目：[qmt-knowledge-skill](https://github.com/miSu7Ultra)（迅投 QMT 版），两者结构一致，可搭配使用。

---

## ✨ 功能特性

本 Skill **仅在用户主动提出 PTrade 相关需求时激活**，不主动介入非 PTrade 上下文。适用场景如下：

| 场景 | 示例问题 |
|---|---|
| 📖 **新手入门** | 「PTrade 怎么跑第一个策略？」「回测和交易有什么区别？」 |
| 💻 **代码生成** | 「帮我写一个双均线策略」「写一个盘后逆回购策略」 |
| 🔎 **API 查询** | 「order 的参数有哪些？」「get_history 怎么用？」 |
| ⏱️ **事件生命周期** | 「get_index_stocks 为什么不能放 initialize？」「tick_data 和 handle_data 区别？」 |
| 🧱 **对象与枚举** | 「Context 对象有哪些字段？」「委托状态 9 是什么意思？」 |
| ⚠️ **错误排查** | 「为什么下单是废单？」「实盘为什么没有回测按钮？」「取数据为空怎么办？」 |
| 📊 **内置指标** | 「get_MACD 怎么引用？技术指标能直接取吗？」 |
| 🛠️ **环境配置** | 「PTrade 支持哪些三方库？」「能用 f-string 吗？」 |

---

## 📁 项目结构

```
ptrade-knowledge-skill/
├── SKILL.md              # ⭐ Skill 主入口（Agent 行为规范 + 索引 + 约束）
├── README.md             # 本文件
├── LICENSE               # MIT
└── knowledge/            # PTrade 官方文档知识库（四大类共 22 份文档）
    ├── 01-入门/
    │   ├── 快速开始.md                 # 新建策略 / 回测 / 交易三步上手
    │   ├── 策略运行周期与时间.md        # 运行周期 / 策略运行时间 / 委托下单时间
    │   ├── 支持的业务类型.md            # 回测与交易支持的证券业务类型
    │   ├── 开始写策略.md               # 完整小策略 → 实用策略 → 模拟盘实盘注意事项
    │   ├── 策略引擎简介.md             # ⭐ 事件驱动框架 + 各事件可调用 API 矩阵
    │   └── 常见问题解答.md             # 环境与使用 FAQ
    ├── 02-API/
    │   ├── 设置函数.md                 # ⭐ set_universe / set_benchmark / 手续费滑点
    │   ├── 定时周期性函数.md            # run_daily / run_interval / tick_data
    │   ├── 获取信息函数.md             # ⭐ get_history / get_snapshot / 交易日期
    │   ├── 获取股票信息.md             # 股票列表 / 状态 / 除权除息 / 指数成分
    │   ├── 获取其他信息.md             # 对账文件 / 底仓设置
    │   ├── 股票交易函数.md             # ⭐ order 系列 / get_positions
    │   ├── 公共交易函数.md             # 持仓 / 委托 / 成交 / 资金查询
    │   ├── 融资融券函数.md             # 两融交易 + 查询
    │   ├── 期货专用函数.md             # 期货交易 / 查询 / 设置
    │   ├── 技术指标计算函数.md          # get_MACD / get_KDJ / get_RSI / get_CCI
    │   └── 其他函数.md                 # log / is_trade 等
    ├── 03-数据与枚举/
    │   ├── 对象与数据字典.md            # ⚠️ g / Context / SecurityUnitData + 委托状态字典
    │   └── 支持的三方库.md             # 内置三方库及版本
    ├── 04-示例与FAQ/
    │   ├── 完整示例.md                 # 双均线 / MACD / tick均线 / 逆回购 / 打新 / 可转债 模板
    │   ├── 常见问题QA.md               # 群友高频问题排查
    │   └── 接口版本变动.md             # 历史版本接口差异
    └── assets/                          # 文档配图
```

---

## ⚡ 核心约束速览（详见 SKILL.md）

1. **Python 3.5 兼容**：禁 f-string / 海象运算符 / `import os`
2. **最小结构**：`initialize(context)` + `handle_data(context, data)` 必选；`g.` 全局对象；`set_universe` 设股票池
3. **事件与 API 匹配**：`get_index_stocks` 不放 `initialize`；回测禁用 `get_snapshot`；`get_price` 的 `start_date` 与 `count` 互斥
4. **内置函数直调**：禁止 `import ptrade`
5. **实盘防护**：下单前查 `get_open_orders`；慎用 `order_target`（持仓同步延迟 → 重复下单）
6. **强制风险提示**：所有回答附带风险提醒与免责声明

---

## 🚀 使用方式

### 在 Claude Code / Claude Agent Skill 中使用

将本目录放入你的 skills 目录（或直接把 `SKILL.md` + `knowledge/` 交给 Agent）：

```bash
git clone https://github.com/miSu7Ultra/ptrade-knowledge-skill.git
```

Agent 读取 `SKILL.md` 后即获得完整的 PTrade 知识行为规范；知识库按需读取 `knowledge/` 下对应文档。

### 在自有 LLM 应用中使用

把 `knowledge/` 目录作为 RAG 知识库切片入库，`SKILL.md` 作为系统提示词的约束部分即可。

---

## 📚 知识来源

- PTrade 官方帮助文档（券商客户端内置文档整理）
- 社区高频 QA 整理

> 本知识库为社区非官方整理，与恒生电子无隶属关系。PTrade 为恒生电子商标。若与官方文档冲突，以官方文档为准。

---

## 📄 License

[MIT](./LICENSE)
