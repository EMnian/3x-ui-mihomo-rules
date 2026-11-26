# 3X-UI 的 Mihomo 分流规则

用 3X-UI 管理节点，用这份模板管理客户端分流。面板把每位用户的节点、策略组和规则合成一份 Mihomo 订阅，供 Clash Verge Rev、FlClash 等客户端使用。模板包含 16 个可独立选节点的场景组，以及「PROXY」和「自动测速」两个公共组。18 个组的图标都内嵌在配置中。

## 接入 3X-UI

1. 使用 **3X-UI v3.7.0 或更高版本**。在「面板设置 → 订阅 → Clash / Mihomo」中开启 Clash/Mihomo 订阅和路由。
2. 将下面的 **Raw 地址**粘贴到「全局路由规则」，保存面板设置。

   ```text
   https://raw.githubusercontent.com/EMnian/3x-ui-mihomo-rules/main/routing.yaml
   ```

3. 在客户端导入 **3X-UI 为用户生成的 Mihomo 订阅链接**，并启用「规则」模式。已导入过该订阅的设备直接刷新即可。
4. 在客户端「代理」页打开 `PROXY`，选择「自动测速」。各场景默认跟随 `PROXY`；如果客户端记住了以前选的节点，这一步可以切回自动选择。

3X-UI 负责读取上面的模板，客户端负责读取面板生成的个人订阅。节点凭据和个人订阅链接请保存在面板里。[3X-UI 订阅文档](https://docs.sanaei.dev/zh/docs/config/subscription/) · [远程规则支持的版本](https://github.com/MHSanaei/3x-ui/releases/tag/v3.7.0)

## 常用分流

Mihomo 从上往下匹配，执行第一条命中的规则。比如 Gemini 先进入「AI 服务」，YouTube 先进入「流媒体」，两者都排在 Google 通用规则之前。

| 流量 | 默认处理 |
| --- | --- |
| 本地网络、国内抖音、命中国内名单的流量 | 直连。已知广告域名优先于应用规则拦截 |
| TikTok | 独立策略组，主站和上游收录的视频 CDN 可一起固定到同一节点 |
| AI、流媒体 | ChatGPT、Claude、Gemini 等进入「AI 服务」；YouTube、Netflix、Disney+ 等进入「流媒体」 |
| 金融 | 银行、证券券商、跨境支付、加密资产和其他金融分别选节点。例如汇丰中国直连，PayPal、Wise 进入「跨境支付」 |
| 平台与开发 | Apple 与 iCloud、Google、微软、Telegram、开发平台和游戏平台分别选节点；GitHub、GitLab、Docker 归入「开发平台」 |
| 其他流量 | 已收录的通用海外域名进入「海外应用」，最终未命中的流量进入「未分类流量」 |

要让某项服务固定出口，直接在它的场景组中选择具体节点。例如 TikTok 选一个固定节点，流媒体选 `PROXY`，两者就可以分别使用固定和自动选择的出口。完整优先级见 [routing.yaml](./routing.yaml)。

## 测速和规则更新

| 项目 | 周期 | 执行方式 |
| --- | --- | --- |
| 节点测速 | 每 60 秒 | 客户端通过订阅节点请求 `https://www.gstatic.com/generate_204`，按响应延迟自动择优。单次超时 5 秒 |
| 上游规则集 | 每 1 小时 | 客户端检查 47 个 MetaCubeX MRS 域名或 IP 规则集，成功拉取后使用新名单 |
| 本仓库模板 | 随 3X-UI 远程模板和客户端订阅刷新 | 修改策略组、匹配顺序或时间间隔后，等待面板拉取新模板，再刷新客户端里的 3X-UI 订阅 |

测速反映到探测地址的响应延迟。需要固定登录地区的服务可手动选节点。上游新增一份独立规则集时，需要在模板的 `rule-providers` 中声明，并在 `rules` 中引用。[MetaCubeX 规则库](https://github.com/MetaCubeX/meta-rules-dat) · [Mihomo 自动测速](https://wiki.metacubex.one/config/proxy-groups/url-test/) · [Mihomo 规则集](https://wiki.metacubex.one/config/rule-providers/)
