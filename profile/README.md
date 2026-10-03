<div align="center">

# spot-probe

[monitor-probe](https://github.com/monitor-probe) 探针的二次开发，现已独立走自己的版本线。

Rust 写的轻量服务器探针：agent 经 WebSocket / JSON-RPC 2.0 上报，hub 用 axum + SQLite 收下并出图。
延迟监控支持 **TCP** 与 **ICMP** 两种探测。

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
  图表调色板重建（排除与状态色冲突的绿 / 琥珀 / 红），资源图表有悬停卡片与十字线；
  卡片与详情页重构；国家旗与发行版图标、在线时长徽章；速率图标出「均值到峰值」的带；
  深色模式下浏览器原生控件也用深色；货币一律显示在数字前；续费周期支持任意值（5 年、18 个月）
- **主题可视化配置**：主题在 `theme.json` 里声明配置项，面板据此渲染表单，不用改 hub
  （内置主题目前八项：汇总开关、默认视图、分组页签常驻、四张卡片各一项、速率峰值）；
  主题还可以**直接填 GitHub 仓库地址安装**
- **延迟监控支持 ICMP**：每条任务可选探测方式，**默认仍是 TCP**，现有任务不受影响。
  ICMP 只写裸主机或 IP（`1.1.1.1`、`example.com`、`2606:4700:4700::1111`），带端口会被拒绝并提示正确写法，
  IPv4 与 IPv6 都支持；`monitor-agent --ping <host>` 可以在目标机器上先自检能不能发 ICMP、
  不能的话是权限还是路由；**探测失败的原因显示在面板上**，权限不足不再表现为一条没有解释的 100% 丢包
- **历史保留 7 → 90 天**（可设 1–365）：分钟数据折叠进小时汇总表，长窗口跨水位线拼接读取；
  `/api/me` 报 `history_days`，公开页的档位阶梯跟着它走，不再写死
- **可用率与故障历史**：`uptime{d7,d30}` 与 `series=availability` 接口，公开页出时间轴与故障列表
- **升级路径**：agent 重跑一次安装命令即升级（带备份与失败回滚），文档给了把命令发到每台机器的几种做法；
  hub 的安装器可以自我更新，发布产物里带 `install-hub.sh`
- **装完自检**：agent 安装器收尾不再只说一句「去看日志」，而是带超时去日志里等第一条 `connected to`
  （服务 `active` 不等于在报数——token 或地址错了会一直重连而服务始终是 active），再打印实测事实
  `monitor-agent is running (pid N)` 与 `connected to <hub>`；连不上就大声警告但不让安装失败，
  并给出**真实路径**的 `${BIN} --ping 1.1.1.1` 自检命令与 `net.ipv4.ping_group_range` 的前提检查
- **节点地址与国家**：按节点自身地址查，也可以手填
- **品牌**：产品名 **Spot Monitor**，自带 favicon 与 `og:image`；文档站配色与主题同源
- **文档**：全站按本 fork 的**实际行为**重写（hub 的添加节点判定、agent 的真实参数、
  ICMP 的权限与自检、保留天数的上限、可用率的「无数据 ≠ 离线」语义等）

## 发布产物

| 仓库 | 最新发布 | 产物 |
|:--|:--|:--|
| monitor | [v1.8.9](https://github.com/spot-probe/monitor/releases/tag/v1.8.9) | 两个 musl 架构的 hub 二进制 + `install-hub.sh` + `sha256sums.txt` |
| agent | [v1.1.2](https://github.com/spot-probe/agent/releases/tag/v1.1.2) | 两个 musl 架构的 agent 二进制 + `sha256sums.txt` |
| monitor-theme-default | [v1.8.5](https://github.com/spot-probe/monitor-theme-default/releases/tag/v1.8.5) | `theme.tar.gz` + `theme.tar.gz.sha256` |
| monitor-document | — | 无 release，由 Cloudflare Workers 构建发布 |

hub 内置的主题版本由 `monitor` 仓库里的 `web-theme.pin` 钉住（含 sha256，当前指向 `v1.8.5`），
升级要 pin 与版本号一起改。

## 怎么协作

- **`main` 就是发布线**：自己的改动走分支，经 PR 合入 `main`（CI 在 PR 上跑构建与检查），发版打 tag
- **版本号必须一起改**：hub 的 `Cargo.toml` 与 `Cargo.lock`、主题的 `theme.json` / `package.json` /
  `package-lock.json` 各自必须同值，CI 与发版 tag 都会校验 —— 只改一处会在发布流水线上才暴露
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
