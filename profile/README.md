<div align="center">

# spot-probe

[monitor-probe](https://github.com/monitor-probe) 探针的二次开发组织

Rust 写的轻量服务器探针：agent 经 WebSocket / JSON-RPC 2.0 上报，hub 用 axum + SQLite 收下并出图。

</div>

---

## 这里有什么

| 仓库 | 说明 | 语言 |
|:--|:--|:--|
| **[monitor](https://github.com/spot-probe/monitor)** | hub：后台、API、公开页宿主（axum + SQLite） | ![language](https://img.shields.io/github/languages/top/spot-probe/monitor?style=flat-square) |
| **[agent](https://github.com/spot-probe/agent)** | Linux 采集端 | ![language](https://img.shields.io/github/languages/top/spot-probe/agent?style=flat-square) |
| **[monitor-theme-default](https://github.com/spot-probe/monitor-theme-default)** | 公开状态页主题（Vite + React + TS） | ![language](https://img.shields.io/github/languages/top/spot-probe/monitor-theme-default?style=flat-square) |
| **[monitor-document](https://github.com/spot-probe/monitor-document)** | 探针文档（MDX） | ![language](https://img.shields.io/github/languages/top/spot-probe/monitor-document?style=flat-square) |

四个仓库均 fork 自 [monitor-probe](https://github.com/monitor-probe)，**fork 关系保留**，可以随时对照上游。

## 与上游的差别

改动集中在**公开状态页主题**：

- 浅色语义配色，明暗两套，全部过 WCAG AA
- 分组页签、筛选与搜索、列表视图
- 图表调色板重建（排除与状态色冲突的绿 / 琥珀 / 红）
- 卡片与详情页重构，内含价格与到期信息
- favicon 上色，并复用为站名标记

已发布 **[v1.3.0](https://github.com/spot-probe/monitor-theme-default/releases/tag/v1.3.0)**；hub 的 `v1.1.0` 已内置该主题。

## 发布产物

| 仓库 | 最新发布 | 产物 |
|:--|:--|:--|
| monitor | [v1.1.0](https://github.com/spot-probe/monitor/releases/tag/v1.1.0) | 两个 musl 架构的 hub 二进制 + `sha256sums.txt` |
| agent | [v1.0.0](https://github.com/spot-probe/agent/releases/tag/v1.0.0) | 两个 musl 架构的 agent 二进制 + `sha256sums.txt` |
| monitor-theme-default | [v1.3.0](https://github.com/spot-probe/monitor-theme-default/releases/tag/v1.3.0) | `theme.tar.gz` + 校验文件 |

## 怎么协作

- **`main` 保持上游镜像**：`git merge --ff-only upstream/main` 永远不冲突，方便持续跟进上游的修复与新特性
- **自己的改动走分支**：通过指向 `main` 的 PR 触发 CI（`pull_request` 事件），合入与否由改动性质决定
- 长期分支用 rebase 跟随上游，而不是把上游 merge 进来，历史保持线性

## 相关链接

- 上游组织：[monitor-probe](https://github.com/monitor-probe)
- 上游文档站：<https://monitor-document.pages.dev/>
- 维护者：[@jacob-bytes](https://github.com/jacob-bytes)

---

<div align="center">
<sub>本页内容手写维护。</sub>
</div>
