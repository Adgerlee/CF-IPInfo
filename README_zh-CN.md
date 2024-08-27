# ✔ CloudFlare IP Info Dashboard

一个强大、无服务器且免费的 IP 信息仪表板解决方案，由 Cloudflare Workers 驱动，具有用户友好的界面和多语言支持。

## README

- [English](README.md) - [繁体中文](README_zh-TW.md) - [简体中文](README_zh-CN.md) - [日本语](README_ja.md) - [한국어](README_ko.md)

## 📱 示范网址

- https://xip.im
- https://xip.im/api
- ```curl xip.im```

## ⭐ 特点

- 开源、易于部署（10分钟内完成，无需本地工具）且完全免费
- IP 信息显示
  - 显示访问者的 IP 地址
  - 提供地理位置信息，包括国家、城市和地区
  - 显示时区信息
  - 显示网络服务提供商 (ISP) 信息
  - 显示自治系统号码 (ASN)
- 多语言支持
  - 支持英语、繁体中文、简体中文、日语和韩语
  - 易于扩展，可添加更多语言
- 用户界面
  - 响应式设计，适应不同设备尺寸
  - 现代化的卡片式佈局
  - 动画效果增强用户体验
  - 暗黑模式支持
- 地图功能
  - 显示用户大致位置的互动地图
  - 可切换显示/隐藏
- API 支持
  - 提供 JSON 格式的 IP 信息 API
- 可定制性
  - 可以轻松添加或修改显示的 IP 信息
  - 支持自定义样式和佈局

## 👀 演示

![](https://raw.githubusercontent.com/KKKKKCAT/CF-IPInfo/main/img/CF-IPInfo-1.webp)
![](https://raw.githubusercontent.com/KKKKKCAT/CF-IPInfo/main/img/CF-IPInfo-2.webp)

## ⚡ 快速开始

1. 登录到您的 Cloudflare 帐户
2. 进入 Workers & Pages 部分
3. 创建一个新的 Worker
4. 将提供的代码複製并粘贴到 Worker 编辑器中
5. 保存并部署

对于代码，请参考
- [普通版本](https://github.com/KKKKKCAT/CF-IPInfo/blob/main/CF-IPInfo.js)
- [表情符号旗版本](https://github.com/KKKKKCAT/CF-IPInfo/blob/main/CF-IPInfo-emoji.js)

## 📄 API 文档

访问 `/api` 端点以获取 JSON 格式的 IP 信息。

示例响应：

```json
{
  "ip": "203.0.113.195",
  "ipVersion": "IPv4",
  "country": "United States",
  "city": "Los Angeles",
  "region": "California",
  "timezone": "America/Los_Angeles",
  "isp": "Example ISP",
  "asn": "AS12345",
  "latitude": 34.0522,
  "longitude": -118.2437
}
```

## 未来特性 (待办事项)

- [ ] 添加更多语言支持
- [x] 实现暗黑模式切换
- [x] 添加 IP 地理位置地图显示
- [x] 支持 IPv6 和 IPv4 地址格式检测
- [ ] 添加 IP 信息历史记录功能
- [x] 实现 API 端点，允许其他服务查询 IP 信息
- [ ] 添加更多 IP 相关的技术信息（如 网络类型等）
- [ ] 支持自定义域名

## 贡献

欢迎提交 Pull Requests 来改进这个项目。对于重大更改，请先开 issue 讨论您想要改变的内容。

## 👤 作者

**KKCAT**

- [![Telegram](https://img.shields.io/badge/-Telegram-2CA5E0?style=flat-square&logo=telegram&logoColor=white)](https://t.me/kkkkkcat)
- [![Twitter](https://img.shields.io/badge/Twitter-Follow-1DA1F2?style=flat&logo=twitter)](https://x.com/kcat88888)





Cloudflare Worker 获取 IP 和 IP 信息的原理

获取客户端 IP（CF-Connecting-IP）:

当请求到达 Cloudflare 的边缘节点时，Cloudflare 会记录发起请求的客户端 IP 地址。
Cloudflare 将此 IP 添加到一个特殊的 HTTP 头部 CF-Connecting-IP 中。
在 Worker 中，我们可以通过 request.headers.get('CF-Connecting-IP') 来获取这个值。
IP 版本检测（detectIPVersion）:

这是一个自定义函数，不是 Cloudflare 提供的。
通常通过正则表达式来判断 IP 地址的格式，从而确定是 IPv4 还是 IPv6。
例如：
function detectIPVersion(ip) {
  return /^(\d{1,3}\.){3}\d{1,3}$/.test(ip) ? 'IPv4' : 'IPv6';
}
request.cf 对象:

这是 Cloudflare Workers 特有的对象，包含了丰富的请求相关信息。
当请求到达 Cloudflare 网络时，Cloudflare 会快速查询其内部数据库，获取与该 IP 相关的各种信息。
这个过程在请求到达 Worker 之前就已完成，所以在 Worker 中访问这些信息几乎没有延迟。
地理位置信息（country, city, region, timezone, latitude, longitude）:

Cloudflare 维护着一个全球 IP 地理位置数据库。
当请求到来时，Cloudflare 根据客户端 IP 快速查询这个数据库。
这些信息被填充到 request.cf 对象中。
网络信息（asOrganization, asn）:

AS（Autonomous System）信息来自 Cloudflare 的网络数据库。
asOrganization 通常是 IP 地址所属的互联网服务提供商（ISP）。
asn 是自治系统号码，用于 BGP（边界网关协议）路由。
数据更新机制:

Cloudflare 持续更新其 IP 数据库，以确保信息的准确性。
这个更新过程对 Worker 开发者来说是透明的。
性能考虑:

由于这些信息是预先计算并缓存的，访问 request.cf 对象的各个属性几乎不会增加处理时间。
错误处理:

代码中使用 || 'Unknown' 或 || 0 是一个好习惯，可以处理某些信息不可用的情况。
总的来说，Cloudflare 利用其全球网络和强大的数据处理能力，在请求到达 Worker 之前就准备好了这些信息。这使得开发者可以轻松且高效地访问丰富的 IP 相关数据，而无需额外的 API 调用或複杂的后端逻辑。
