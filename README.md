# Clash Party 覆写规则

基于 [Adsryen/clash-override](https://github.com/Adsryen/clash-override) 二开，适用于 **Clash Verge Rev** / **Mihomo Party** 的全局扩展覆写脚本。

## 下载

```
https://raw.githubusercontent.com/godpowerscn/clash-override/master/clash-party.js
```

在 Mihomo Party 的「覆写」中粘贴此 URL 即可使用，支持随时更新。

## 功能特性

### 自动分流策略组

| 策略组 | 说明 | 默认 |
|--------|------|------|
| 苹果服务 | Apple 相关流量 | 开 |
| 微软服务 | Microsoft 相关流量 | 开 |
| GitHub | GitHub 访问 | 开 |
| 谷歌服务 | Google 全家桶 | 开 |
| 国外 AI | ChatGPT / Copilot 等 | 开 |
| YouTube | YouTube 流量 | 开 |
| Spotify | 音乐流媒体 | 开 |
| Pixiv | 图片社区 | 开 |
| 游戏专用 | 游戏流量 | 开 |
| 广告过滤 | 广告拦截 | 开 |
| 跟踪分析 | 隐私追踪拦截 | 开 |

### 地区自动识别（20 个地区）

节点名称自动匹配地区并分组：香港、美国、日本、韩国、新加坡、中国大陆、台湾、英国、德国、马来西亚、土耳其、加拿大、法国、希腊、立陶宛、北马其顿、荷兰、波兰、瑞典、阿根廷。

支持 `autoDetect` 自动识别未匹配节点的国家代码。

### 自定义规则（直连 / 代理）

**直连规则：**
- 远程桌面：SunloginClient、AnyDesk
- 同步工具：Syncthing
- VPN：Tailscale（含 DERP 流量）
- 微信：WeChat.exe
- 内网：10.168.1.0/24、10.168.2.0/24
- DeepSeek 直连

**代理规则：**
- Discord
- Windsurf
- Postman

### 端口直连

- SSH（端口 22）
- 邮件 SSL（端口 465）

### DNS 配置

内置国内/国外 DNS 分流（需手动开启 `enableDnsOverride`）：
- 国内：Ali DNS、Tencent DNSPod、China Telecom
- 国外：DoH 加密 DNS

### 其他特性

- sniffer 域名嗅探（强制 Google/Facebook 等域名解析）
- GeoX 数据源使用 ghfast.net 镜像（国内友好）
- NTP 时间同步
- 内存优化加载器（memconservative）

## 快速开始

### Mihomo Party

1. 打开 Mihomo Party → 覆写
2. 新建覆写，选择「通过 URL 导入」
3. 粘贴上方下载 URL
4. 启用该覆写

### Clash Verge Rev

1. 打开 Clash Verge → 订阅 → 覆写脚本
2. 新建脚本，粘贴上方 URL 或直接导入 `clash-party.js` 内容
3. 启用该脚本

## 配置自定义

编辑 `clash-party.js` 顶部的配置项：

```js
// 总开关
const enable = true

// 自动选择最低延迟节点（false = 手动选择）
const enableUrltest = false

// DNS 覆写（true = 启用自定义 DNS）
const enableDnsOverride = false

// 启用/禁用策略组
const ruleOptions = {
    openai: true,    // 国外 AI
    spotify: true,   // Spotify
    youtube: true,   // YouTube
    // ... 更多选项见文件
}
```

## 致谢

- [Adsryen/clash-override](https://github.com/Adsryen/clash-override) 原始脚本
- [dahaha-365/YaNet](https://github.com/dahaha-365/YaNet) AI 规则集
- [MetaCubeX/meta-rules-dat](https://github.com/MetaCubeX/meta-rules-dat) GeoX 数据
- [Koolson/Qure](https://github.com/Koolson/Qure) 策略组图标
