# Clash Party 覆写脚本 (clash-party.js)

> 适用于 **Clash Verge Rev** / **Mihomo Party** 的全局扩展覆写脚本
>
> 基于 [dahaha-365/YaNet](https://github.com/dahaha-365/YaNet) 二开，针对中文用户深度优化

---

## 目录

- [项目简介](#项目简介)
- [为什么需要这个项目](#为什么需要这个项目)
- [核心功能](#核心功能)
- [快速开始](#快速开始)
- [配置详解](#配置详解)
- [Tailscale 共存方案](#tailscale-共存方案)
- [Syncthing 共存方案](#syncthing-共存方案)
- [DNS 防泄露机制](#dns-防泄露机制)
- [架构设计](#架构设计)
- [常见问题](#常见问题)
- [致谢](#致谢)

---

## 项目简介

`clash-party.js` 是一个为 Mihomo Party / Clash Verge Rev 设计的全局扩展脚本，通过 JavaScript 覆写机制，在不修改原始配置文件的前提下，实现：

- **智能分流**：国内外流量自动分离
- **节点分组**：按地区自动识别并分组（20+ 地区）
- **DNS 防泄露**：fake-ip + DoH 加密 + 国内 DNS 分流
- **VPN 共存**：Tailscale / Syncthing 等工具正常工作
- **一键配置**：顶部开关控制所有功能

---

## 为什么需要这个项目

### 解决的痛点

| 痛点 | 传统方案 | 本项目方案 |
|------|----------|------------|
| 手动编辑 YAML 配置 | 容易出错，每次更新都需重新配置 | JavaScript 覆写，一键更新 |
| 节点分组混乱 | 手动添加节点到策略组 | 自动识别地区并分组 |
| DNS 泄露风险 | 需要手动配置多个 DNS 项 | 内置 fake-ip + DoH 防泄露 |
| Tailscale 不兼容 | 需要手动添加 bypass 规则 | 内置 Tailscale TUN bypass |
| 国内直连规则缺失 | 手动添加 PROCESS-NAME 规则 | 预置微信/向日葵/AnyDesk 直连 |
| 配置文件被覆盖 | 每次重启丢失修改 | 覆写通过内存注入，不受影响 |

### 适用场景

| 场景 | 推荐度 |
|------|--------|
| 同时使用 Tailscale + Clash Party | ⭐⭐⭐⭐⭐ |
| 同时使用 Syncthing + Clash Party | ⭐⭐⭐⭐⭐ |
| 需要自动分流国内外流量 | ⭐⭐⭐⭐⭐ |
| 需要 DNS 防泄露保护 | ⭐⭐⭐⭐⭐ |
| 需要按地区自动分组节点 | ⭐⭐⭐⭐ |
| 纯手动选择节点 | ⭐⭐⭐ |

---

## 核心功能

### 1. 智能分流策略组

| 策略组 | 说明 | 默认状态 |
|--------|------|----------|
| 苹果服务 | Apple 相关流量 | ✅ 开启 |
| 微软服务 | Microsoft 相关流量 | ✅ 开启 |
| GitHub | GitHub 访问 | ✅ 开启 |
| 谷歌服务 | Google 全家桶 | ✅ 开启 |
| 国外 AI | ChatGPT / Copilot / DeepSeek 等 | ✅ 开启 |
| YouTube | YouTube 流量 | ✅ 开启 |
| Spotify | 音乐流媒体 | ✅ 开启 |
| Pixiv | 图片社区 | ✅ 开启 |
| 游戏专用 | 游戏流量 | ✅ 开启 |
| 广告过滤 | 广告拦截 | ✅ 开启 |
| 跟踪分析 | 隐私追踪拦截 | ✅ 开启 |
| 巴哈姆特 | 动画疯 | ❌ 关闭 |
| Netflix | 网飞 | ❌ 关闭 |
| TikTok | 国际版抖音 | ❌ 关闭 |
| Disney+ | 迪士尼 | ❌ 关闭 |
| HBO | HBO | ❌ 关闭 |
| 哔哩哔哩东南亚 | B站东南亚 | ❌ 关闭 |
| TVB | TVB | ❌ 关闭 |
| Hulu | Hulu | ❌ 关闭 |
| Prime Video | 亚马逊 Prime Video | ❌ 关闭 |
| Telegram | Telegram 通讯 | ❌ 关闭 |
| Line | Line 通讯 | ❌ 关闭 |
| WhatsApp | WhatsApp | ❌ 关闭 |

### 2. 地区自动识别（20 个地区）

| 地区 | 匹配关键词 | 倍率限制 |
|------|------------|----------|
| HK 香港 | 港, 香港, HONG KONG, hk | 5x |
| US 美国 | 美, 🇺🇸, us, USA | 5x |
| JP 日本 | 日, 日本, JP, 🇯🇵 | 5x |
| KR 韩国 | 韩, 韩国, kr, KR, korea | 5x |
| SG 新加坡 | 新, 新加坡, sg, SG, singapore | 5x |
| CN 中国大陆 | 中, cn, CN, 国内 | 5x |
| TW 台湾省 | 台, tw, TW, 台湾 | 5x |
| GB 英国 | 英, uk, UK, gb, GB | 5x |
| DE 德国 | 德, de, DE, germany | 5x |
| MY 马来西亚 | 马来, my, MY, malaysia | 5x |
| TK 土耳其 | 土耳其, tk, TK, tr, TR | 5x |
| CA 加拿大 | 加, 加拿大, ca, CA | 5x |
| FR 法国 | 法, 法国, fr, FR, france | 5x |
| GR 希腊 | 希, 希腊, gr, GR, greece | 5x |
| LT 立陶宛 | 立陶宛, lt, LT, lithuania | 5x |
| MK 北马其顿 | 北马其顿, mk, MK, macedonia | 5x |
| NL 荷兰 | 荷, 荷兰, nl, NL, netherlands | 5x |
| PL 波兰 | 波, 波兰, pl, PL, poland | 5x |
| SE 瑞典 | 瑞典, sweden, stockholm, 🇸🇪 | 5x |
| AR 阿根廷 | 阿根廷, 🇦🇷, argentina | 5x |

**特性**：
- 自动过滤高倍率节点（默认 >5x 排除）
- `autoDetect` 自动识别未匹配节点的国家代码
- 支持 `url-test` 自动选择最低延迟节点

### 3. 自定义规则

#### 直连规则

| 类别 | 规则 | 说明 |
|------|------|------|
| 远程桌面 | SunloginClient.exe, AnyDesk.exe | 国产远程工具 |
| 同步工具 | syncthing.exe | 文件同步 |
| VPN | tailscaled.exe, tailscale-ipn.exe | Tailscale 网卡级绕过 |
| 微信 | WeChat.exe | 微信直连 |
| 内网 | 10.168.1.0/24, 10.168.2.0/24 | 局域网直连 |
| DeepSeek | DeepSeek 官网直连 | AI 服务直连 |
| 端口 | SSH (22), 邮件 SSL (465) | 常用端口直连 |

#### 代理规则

| 类别 | 规则 | 说明 |
|------|------|------|
| Discord | Discord 全站 | 通讯工具 |
| Windsurf | Windsurf IDE | 开发工具 |
| Postman | Postman API 工具 | 开发工具 |

### 4. DNS 配置

| 配置项 | 值 | 说明 |
|--------|-----|------|
| enhanced-mode | fake-ip | 返回假 IP，防 DNS 污染 |
| fake-ip-range | 198.18.0.1/16 | 假 IP 地址段 |
| fake-ip-filter | +.lan, +.local, +.ts.net, time.*.com, ntp.*.com | 不返回假 IP 的域名 |
| nameserver | 1.1.1.1/dns-query, 8.8.8.8/dns-query | 海外 DoH 加密 DNS |
| nameserver-policy | geosite:cn → 国内 DNS | 国内域名使用国内 DNS |
| dohBlockDomains | doh.pub, dns.alidns.com 等 | REJECT 国内 DoH 防泄露 |

---

## 快速开始

### Mihomo Party

1. 打开 Mihomo Party → **覆写**
2. 新建覆写，选择「通过 URL 导入」
3. 粘贴下载 URL：
   ```
   https://raw.githubusercontent.com/godpowerscn/clash-override/master/clash-party.js
   ```
4. 启用该覆写
5. 重启内核

### Clash Verge Rev

1. 打开 Clash Verge → **订阅** → **覆写脚本**
2. 新建脚本，粘贴上方 URL 或直接导入 `clash-party.js` 内容
3. 启用该脚本
4. 重启内核

---

## 配置详解

### 顶部开关

```javascript
// 总开关
const enable = true

// urltest 自动选择（false = 手动选择）
const enableUrltest = false

// DNS 覆写（true = 启用自定义 DNS）
const enableDnsOverride = true
```

### 策略组开关

```javascript
const ruleOptions = {
    apple: true,        // 苹果服务
    microsoft: true,    // 微软服务
    github: true,       // GitHub
    google: true,       // 谷歌服务
    openai: true,       // 国外 AI
    spotify: true,      // Spotify
    youtube: true,      // YouTube
    pixiv: true,        // Pixiv
    games: true,        // 游戏
    tracker: true,      // 跟踪分析
    ads: true,          // 广告过滤
    // ... 更多选项见文件
}
```

### 地区配置

```javascript
const regionOptions = {
    excludeHighPercentage: true,  // 排除高倍率节点
    autoDetect: true,             // 自动识别未匹配节点
    regions: [
        {
            name: 'HK香港',
            regex: /港|香港|HONG KONG/i,
            ratioLimit: 5,        // 倍率 >5x 排除
            icon: '...',
        },
        // ... 更多地区
    ],
}
```

---

## Tailscale 共存方案

### 问题

Clash Party TUN 模式会接管所有网络流量，导致 Tailscale UDP 打洞失败，只能走 DERP 中继（延迟高）。

### 解决方案

脚本内置三层 bypass：

| 层级 | 机制 | 作用 |
|------|------|------|
| 网络接口层 | `tun.exclude-process` | 让 Tailscale 进程流量绕过 TUN 网卡 |
| 路由层 | `tun.route-exclude-address` | 让 100.64.0.0/10 流量绕过 TUN 路由 |
| DNS 层 | `fake-ip-filter` + `nameserver-policy` | Tailscale 域名使用真实 IP + 系统 DNS |

### 验证步骤

```bash
# 1. 检查 Tailscale 状态
tailscale status

# 2. 检查 UDP 连通性
tailscale netcheck
# 期望输出: UDP: true

# 3. 测试直连 ping
tailscale ping kdl-1
# 期望输出: pong via direct in ~11ms
```

---

## Syncthing 共存方案

### 问题

Syncthing 使用 QUIC 协议（UDP）进行文件同步，TUN 模式可能导致连接失败。

### 解决方案

| 配置 | 值 | 说明 |
|------|-----|------|
| tun.exclude-process | syncthing.exe | Syncthing 流量绕过 TUN |
| tun.route-exclude-address | 198.18.0.0/15 | 假 IP 地址段绕过 |
| fake-ip-filter | +.lan, +.local | 局域网发现使用真实 IP |

### 验证步骤

```bash
# 1. 检查 Syncthing 状态
# 打开 http://127.0.0.1:8384/

# 2. 检查设备连接状态
# 应显示 "已连接" 或 "本地发现"
```

---

## DNS 防泄露机制

### 防护层级

| 层级 | 机制 | 说明 |
|------|------|------|
| 第一层 | fake-ip 模式 | 所有 DNS 查询返回假 IP，真实解析在连接时通过代理完成 |
| 第二层 | dns-hijack: any:53 | 拦截所有 53 端口 DNS 查询，强制走 TUN |
| 第三层 | 海外 nameserver | 默认 DNS 使用 Cloudflare/Google DoH，通过代理解析 |
| 第四层 | DoH 阻断列表 | REJECT 国内 DoH 服务器，防止浏览器绕过 TUN |
| 第五层 | nameserver-policy | 国内域名分流到国内 DNS，避免跨境解析 |

### 阻断的 DoH 服务器

| 域名 | 说明 |
|------|------|
| doh.pub | 腾讯 DoH |
| dot.pub | 腾讯 DoT |
| dns.alidns.com | 阿里 DNS |
| doh.alidns.com | 阿里 DoH |
| dnspod.cn | DNSPod |
| doh.dnspod.cn | DNSPod DoH |
| dns.360.cn | 360 DNS |
| doh.360.cn | 360 DoH |

### 验证 DNS 泄露

访问以下网站检查 DNS 泄露：
- https://dnsleaktest.com/
- https://ipleak.net/
- https://browserleaks.com/dns

---

## 架构设计

```
┌─────────────────────────────────────────────────────────────┐
│                      应用程序层                              │
│  (浏览器, 微信, Tailscale, Syncthing, ...)                 │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│                    TUN 虚拟网卡                              │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  exclude-process: tailscaled.exe, tailscale-ipn.exe │   │
│  │                   syncthing.exe                      │   │
│  │  route-exclude: 100.64.0.0/10, 198.18.0.0/15        │   │
│  └─────────────────────────────────────────────────────┘   │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│                    Mihomo 核心                              │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │  DNS 模块    │  │  路由模块    │  │  代理模块    │        │
│  │  fake-ip    │  │  规则匹配    │  │  协议转换    │        │
│  │  DoH 阻断   │  │  分流决策    │  │  节点选择    │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│                    物理网卡                                  │
│  (WLAN / Ethernet)                                         │
└─────────────────────────────────────────────────────────────┘
```

---

## 常见问题

### Q: 覆写更新后配置丢失怎么办？

A: 覆写通过内存注入生效，不会修改原始 `mihomo.yaml`。每次重启内核会重新应用覆写。

### Q: 如何添加新的直连规则？

A: 在 `clash-party.js` 中找到 `customRules` 数组，添加新的规则：

```javascript
{
    name: '我的应用',
    type: 'direct',
    processName: ['my-app.exe'],
}
```

### Q: Tailscale 还是走 DERP 怎么办？

A: 检查以下配置：
1. 确认 `tun.exclude-process` 包含 `tailscaled.exe`
2. 确认 `tun.route-exclude-address` 包含 `100.64.0.0/10`
3. 确认 `fake-ip-filter` 包含 `+.ts.net`
4. 重启 Clash Party 内核

### Q: DNS 泄露测试显示国内 DNS 怎么办？

A: 检查以下配置：
1. 确认 `enableDnsOverride = true`
2. 确认 `dohBlockDomains` 包含国内 DoH 域名
3. 确认浏览器 Secure DNS 已关闭或使用海外 DoH

### Q: 如何查看实际生效的配置？

A: 查看 `work/config.yaml` 文件，这是 mihomo 实际加载的配置。

---

## 文件结构

```
clash-override/
├── README.md              # 本文档
└── clash-party.js         # 覆写脚本主文件
```

### 相关文件路径（Mihomo Party）

| 文件 | 路径 | 说明 |
|------|------|------|
| 覆写脚本（本地） | `C:\Users\{用户名}\AppData\Roaming\mihomo-party\override\*.js` | 本地缓存 |
| 基础配置 | `C:\Users\{用户名}\AppData\Roaming\mihomo-party\mihomo.yaml` | 每次重启覆盖 |
| 运行时配置 | `C:\Users\{用户名}\AppData\Roaming\mihomo-party\work\config.yaml` | 实际生效 |
| YAML 覆写 | `C:\Users\{用户名}\AppData\Roaming\mihomo-party\override\*.yaml` | 可选 |

---

## 致谢

- [dahaha-365/YaNet](https://github.com/dahaha-365/YaNet) - 原始脚本基础
- [MetaCubeX/meta-rules-dat](https://github.com/MetaCubeX/meta-rules-dat) - GeoX 数据源
- [Koolson/Qure](https://github.com/Koolson/Qure) - 策略组图标
- [ghfast.net](https://ghfast.net) - 国内镜像加速

---

## 许可证

MIT License

---

**最后更新**: 2026-09-14
