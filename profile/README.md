<div align="center">

# spot-probe

[monitor-probe](https://github.com/monitor-probe) 探针的二次开发，现已独立走自己的版本线。

Rust 写的轻量服务器探针：agent 经 WebSocket / JSON-RPC 2.0 上报，hub 用 axum + SQLite 收下并出图。

**[文档站 →](https://spot-probe-docs.hualala.workers.dev/)**

</div>

---

## 这里有什么

| 仓库 | 说明 | 语言 |
|:--|:--|:--|
| **[monitor](https://github.com/spot-probe/monitor)** | hub：后台、API、公开页宿主（axum + SQLite） | ![language](https://img.shields.io/github/languages/top/spot-probe/monitor?style=flat-square) |
| **[agent](https://github.com/spot-probe/agent)** | Linux 采集端 | ![language](https://img.shields.io/github/languages/top/spot-probe/agent?style=flat-square) |
| **[monitor-theme-default](https://github.com/spot-probe/monitor-theme-default)** | 公开状态页主题（Vite + React + TS） | ![language](https://img.shields.io/github/languages/top/spot-probe/monitor-theme-default?style=flat-square) |
| **[monitor-document](https://github.com/spot-probe/monitor-document)** | 文档站（Vite + React SSR，构建时预渲染） | ![language](https://img.shields.io/github/languages/top/spot-probe/monitor-document?style=flat-square) |

## 与上游的关系

四个仓库均 fork 自 [monitor-probe](https://github.com/monitor-probe)，**fork 关系保留**以便随时对照上游。

但**已不再跟随上游**：上游不再接受功能合入，这里走自己的 `main`。因此
`git merge --ff-only upstream/main` **已不适用** —— 仓库不再是上游的纯镜像。

## 与上游的差别

- **公开状态页主题**：浅色语义配色、明暗两套、全部过 WCAG AA；分组页签、筛选与搜索、列表视图；
  图表调色板重建（排除与状态色冲突的绿 / 琥珀 / 红）；卡片与详情页重构；
  国家旗与发行版图标、在线时长徽章
- **可用率与故障历史**：`uptime{d7,d30}` 与 `series=availability` 接口，公开页出时间轴与故障列表
- **品牌**：产品名 **Spot Monitor**，自带 favicon 与 `og:image`；文档站配色与主题同源
- **文档**：全站按本 fork 的**实际行为**重写（hub 的添加节点判定、agent 的真实参数、
  可用率的「无数据 ≠ 离线」语义等）

## 发布产物

| 仓库 | 最新发布 | 产物 |
|:--|:--|:--|
| monitor | [v1.5.3](https://github.com/spot-probe/monitor/releases/tag/v1.5.3) | 两个 musl 架构的 hub 二进制 + `sha256sums.txt` |
| agent | [v1.1.0](https://github.com/spot-probe/agent/releases/tag/v1.1.0) | 两个 musl 架构的 agent 二进制 + `sha256sums.txt` |
| monitor-theme-default | [v1.5.1](https://github.com/spot-probe/monitor-theme-default/releases/tag/v1.5.1) | `theme.tar.gz` + `theme.tar.gz.sha256` |
| monitor-document | — | 无 release，由 Cloudflare Workers 构建发布 |

hub 内置的主题版本由 `monitor` 仓库里的 `web-theme.pin` 钉住（含 sha256），升级要 pin 与版本号一起改。

## 怎么协作

- **`main` 就是发布线**：自己的改动走分支，经 PR 合入 `main`（CI 在 PR 上跑构建与检查）
- 上游仍在开发，但不再接受功能合入；`./dev.sh sync` **默认只报告差异**，不加 `--merge` 不会动 `main`

## 相关链接

- 文档站：<https://spot-probe-docs.hualala.workers.dev/>
- 上游组织：[monitor-probe](https://github.com/monitor-probe)
- 上游文档站（对照用）：<https://monitor-document.pages.dev/>
- 维护者：[@jacob-bytes](https://github.com/jacob-bytes)

---

<div align="center">
<sub>本页内容手写维护。</sub>
</div>
