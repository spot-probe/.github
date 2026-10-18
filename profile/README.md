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

其中 **agent 也会随本 fork 演进**，不只是 hub：ICMP 探测、IP 质量许可、流媒体解锁探测、
远程升级的开关上报，都需要 agent 侧配合。所以**升级 hub 不一定够** —— 有些版本会要求 agent 先升。
面板在保存任务**之前**就会提示哪些节点的 agent 太旧；agent 有自己签名过的发布（见下表），
也可以从面板批量下发升级。注意顺序：新开关会写进服务的启动参数，老 agent 不认识它就会**启动失败**，
所以先升 agent、再开这些开关。

## 与上游的差别

- **公开状态页主题**：浅色语义配色、明暗两套、全部过 WCAG AA；分组页签、筛选与搜索、列表视图；
  图表调色板重建（排除与状态色冲突的绿 / 琥珀 / 红），资源图表有悬停卡片与十字线；
  卡片与详情页重构；国家旗与发行版图标、在线时长徽章；速率图标出「均值到峰值」的带；
  深色模式下浏览器原生控件也用深色；货币一律显示在数字前；续费周期支持任意值（5 年、18 个月）；
  **速率图用对数纵轴**——速率跨度是数量级（0.2 KB/s 的空闲到 70 MB/s 的突发），线性轴会把平时的
  流量压成贴底的一条线，对数轴让两者在同一张图里都看得清；档位由保留期生成（保留期本身也算一档）；
  详情页另有「连接与进程」图，在线状态是一周的日历格；探测的五条线除了颜色还带**线型**
  （实线 / 长虚 / 点线 / 点划 / 短虚）——颜色不该是唯一的区分手段，对色觉障碍的读者几条线才不至于
  看起来一样；延迟页的时间范围也放开到了 hub 的保留期
- **主题可视化配置**：主题在 `theme.json` 里声明配置项，面板据此渲染表单，不用改 hub
  （内置主题目前八项：汇总开关、默认视图、分组页签常驻、四张卡片各一项、速率峰值）；
  主题还可以**直接填 GitHub 仓库地址安装**
- **后台重做**：新增「**总览**」页并作为默认落地页 —— 一行 KPI（在线 / 离线 / 总数 / 待升级 /
  30 天内到期 / 已过期）、一张**续费日历**（每天挂「N 台」，集中到期日高亮，点某天看那天的节点）、
  近期事项与 Agent 版本一览；全队趋势图（流量 / 带宽 / 资源，资源图带「最热那台」的 P95 分布带）。
  延迟页能展开看**哪台节点在拖后腿**（分桶直方图 + 百分位区间带），并有一张**延迟分布热力图**
  （区间边界由当前窗口的分位数算，配一行丢包条；分位图按缺口分段，所以「这段彻底不通」在图上看得见，
  窗口可选 1 小时 / 6 小时 / 24 小时 / 7 天），节点表有「只看待升级」开关；
  节点还可以加一条**私有备注**——只在管理后台出现，公开页任何情况下都不显示；「30 天内到期」那张
  KPI 卡点开就是一行一台，行尾「我已续费」二次确认后续一期（也支持按分组续），
  **由人说「我续了」、hub 只执行**——而不是 hub 自己猜「你没续费」，那会把真正的到期警报盖掉
- **成本视图**：新增汇率模块与 `GET /api/fx`，各币种折成 CNY（缓存 + 手动覆盖），成本页给出
  未到期当月开销与剩余价值。有一条硬约束：**绝不回退成 1:1**——取不到就用上一次的值，并在页面上
  标明它有多旧；悄悄按 1:1 算的话 `$50` 会被当成 `¥50`，总额少算七倍而页面看起来完全正常
- **延迟监控支持 ICMP**：每条任务可选探测方式，**默认仍是 TCP**，现有任务不受影响。
  ICMP 只写裸主机或 IP（`1.1.1.1`、`example.com`、`2606:4700:4700::1111`），带端口会被拒绝并提示正确写法，
  IPv4 与 IPv6 都支持；`monitor-agent --ping <host>` 可以在目标机器上先自检能不能发 ICMP、
  不能的话是权限还是路由；**探测失败的原因显示在面板上**，权限不足不再表现为一条没有解释的 100% 丢包；
  agent 太旧时也**明确说出来**（ICMP 需要 agent ≥ 1.1.1，面板在保存任务**之前**就会提示哪些节点太旧）
- **流媒体解锁探测**：agent 每 6 小时（**按节点错开**、重启不触发风暴）探一次
  Netflix / ChatGPT / YouTube / Amazon Prime Video / Reddit / TikTok，结果上报后面板有「节点实测」列
  与探测历史点阵；判据照 xykt 的 IPQuality 实现。`monitor-agent --probe-once` 能让 agent 自己说出
  它到底读到了什么（URL / 状态码 / 正文长度 / 各标记出现次数），不用靠猜。
  与 IP 质量共用同一个许可开关
- **历史保留 7 → 90 天**（可设 1–365）：分钟数据折叠进小时汇总表，长窗口跨水位线拼接读取；
  `/api/me` 报 `history_days`，公开页的档位阶梯跟着它走，不再写死
- **可用率与故障历史**：`uptime{d7,d30}` 与 `series=availability` 接口，公开页出时间轴与故障列表
- **升级路径（手动 / 批量）**：agent 重跑一次安装命令即升级（带备份与失败回滚），文档给了把命令发到每台机器的几种做法；
  hub 的安装器可以自我更新，发布产物里带 `install-hub.sh`；**升级前自动备份数据库**到
  `数据目录/backups/`（留最近三份），回滚时数据库一并恢复，健康检查还会再问一句端口**是否真的在应答**
  ——`is-active` 只说明进程活着，起来了但没在服务的 hub 从那一句看不出来
- **可选的远程升级（签名验证）**：agent 把**发布公钥编译进二进制**（不是配置项，**hub 也改不了**），
  只依赖 `minisign-verify` 验签；私钥从不进仓库——CI 只构建并发出**草稿**，签名在本机离线做、
  之后转正，而 `releases/latest` 只解析已发布的版本，所以**未签名的草稿产物任何节点都取不到**。
  节点侧用 `--allow-remote-upgrade` 显式开启（**默认不勾**），开关状态在握手里如实上报；开启后可在
  面板上对单台节点点「远程升级」（两步确认），hub 用**同一条已认证的 WebSocket** 把公告与二进制推过去，
  与 `/agent/{arch}` 中继同源。验签通过才替换自己，升级后自检、失败回滚，结果回报到面板；
  下发过程**有进度**（取发布物 / 推送 N% / 等待应用 / 等待重连），不会只显示一个「正在升级」；
  也能**批量下发**（逐台进行、失败即停），成功回执保留 30 分钟、失败留到下一次尝试
- **安装路径也验签**：重跑安装命令时，`install.sh` 会先向 hub 取一份签名（节点常常连不上 GitHub，
  所以由 hub 转），再用**机器上已经装着的那个旧 agent** 去验新包——脚本自己不懂 Ed25519，而旧 agent
  里编着公钥。**不匹配就拒绝安装**，取不到签名则打一条明确的警告。首次安装暂时做不到这一点，
  那时会直说「本机还没有可信校验器」（这是个引导问题：验签器本身也得可信地装进来）。
  另外给 unit 加了重启上限（5 分钟内崩 5 次就停），一个「启动即崩」的坏版本不会无限重启下去
- **装完自检**：agent 安装器收尾不再只说一句「去看日志」，而是带超时去日志里等第一条 `connected to`
  （服务 `active` 不等于在报数——token 或地址错了会一直重连而服务始终是 active），再打印实测事实
  `monitor-agent is running (pid N)` 与 `connected to <hub>`；连不上就大声警告但不让安装失败，
  并给出**真实路径**的 `${BIN} --ping 1.1.1.1` 自检命令与 `net.ipv4.ping_group_range` 的前提检查
- **通知**：agent 与 hub 有新版本时提醒（搭在每日那次检查上，不另开轮询，每个版本只说一次）；
  另有**流量报告**——日报 / 周报 / 月报 / 半年报 / 年报，按天快照求和，报文里写明「本期覆盖 N 天」
  （半年报与年报的诚实底线），明细限 Top 20（Telegram 单条 4096 字符的上限）。
  **Telegram 还能问**：`/status` `/nodes` `/node <名字>` `/traffic` `/probe` 这几个只读命令——
  走长轮询（自建 hub 常常只有出网），只认设置里的那个会话，投递结果也落库、在面板里看得见
- **节点地址与国家**：按节点自身地址查，也可以手填
- **IP 质量与风险**：hub 自己下 IP 库（城市 / ASN / Tor 三个镜像可配、URL 支持日期占位符、
  免费库的压缩包直接解，边收边写盘所以内存与库大小脱钩），**离线也能给出质量**；在线风险库则把
  多个来源**逐源并列、绝不合成**成一个分数，并有每日额度与节流。两条路都**按节点许可**：
  `--allow-ip-quality`（**默认关**）没开的节点，本地查询一次都不做、在线接口一个请求都不发——
  后者才是这个开关最大的理由（否则每台节点的 IP 都会发给第三方）。面板在节点里分别给出 IP 质量、
  在线风险（带来源名）与本地 Tor 清单，总览页有「IP 质量」视图可横向比较。查询**失败一次不会抹掉
  上一轮的结论**——每条结论带着自己的时间戳，面板据实显示它有多旧
- **品牌**：产品名 **Spot Monitor**，自带 favicon 与 `og:image`；文档站配色与主题同源；
  标签页图标 / iOS 主屏图标 / 社交预览图**三处各自独立**，可以在设置页里各换一份
- **文档**：全站按本 fork 的**实际行为**重写（hub 的添加节点判定、agent 的真实参数、
  ICMP 的权限与自检、保留天数的上限、可用率的「无数据 ≠ 离线」语义等）

## 发布产物

| 仓库 | 最新发布 | 产物 |
|:--|:--|:--|
| monitor | [v1.12.25](https://github.com/spot-probe/monitor/releases/tag/v1.12.25) | 两个 musl 架构的 hub 二进制 + `install-hub.sh` + `sha256sums.txt` |
| agent | [v1.1.17](https://github.com/spot-probe/agent/releases/tag/v1.1.17) | 两个 musl 架构的 agent 二进制（**各带一份 `.minisig` 签名**）+ `sha256sums.txt` |
| monitor-theme-default | [v1.8.21](https://github.com/spot-probe/monitor-theme-default/releases/tag/v1.8.21) | `theme.tar.gz` + `theme.tar.gz.sha256` |
| monitor-document | — | 无 release，由 Cloudflare Workers 构建发布 |

hub 内置的主题版本由 `monitor` 仓库里的 `web-theme.pin` 钉住（含发布物的 sha256，当前指向 `v1.8.21`），
升级要 pin 与版本号一起改。agent 的发布走「CI 出草稿 → 本机离线签名 → 转正」，所以
`releases/latest` 指向的永远是已签名的那一版。

## 怎么协作

- **`main` 就是发布线**：自己的改动走分支，经 PR 合入 `main`，发版打 tag。CI 在 PR 上跑构建与检查，
  另有一套 **Playwright 冒烟**——起一个真实 hub 对着它跑，不是 jsdom
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
