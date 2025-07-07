---
title: Google chrome ssl自签证书临时例外生命周期
date: 2025-07-07 16:35:13
categories:
- Chrome
tags: 
    - [Chrome, ssl证书]
---


结论：7天 (非人为等因素影响情况下，如清理缓存，或企业自定义，证书指纹变更等)，是Chromium安全设计团队给的底层设计策略，最终目的：在允许开发/测试灵活性的同时，将自签名证书的安全风险严格约束在 用户主动参与且短暂的时间窗口内，防止单次决策引发系统性风险。对于需长期信任的场景（如内部服务），正确路径是将自签名证书安装至操作系统信任库，而非削弱浏览器策略。

# Preferences文件
Preferences文件中记录着临时例外生命周期，Chrome 的 Preferences 文件中的 ssl_cert_decisions 字段存储了用户对特定网站 SSL/TLS 证书做出的例外决定，位置：C:\Users\xiedandan\AppData\Local\Google\Chrome\User Data\Default\Preferences

## 主要作用和含义：
记录手动信任决策：当你访问一个使用自签名证书、过期证书、域名不匹配证书或其他非标准/不受信任证书的 HTTPS 网站时，Chrome 会显示严重的警告页面（例如“您的连接不是私密连接”）。如果你选择 “继续前往网站（不安全）” 或类似的选项（此选项名称在不同版本中可能有细微差别），你就手动忽略了这个证书警告。Chrome 会记录这个决定，将这个网站（主机名）和该特定证书（或其指纹/哈希值）的对应关系存储在 ssl_cert_decisions 中，过期后访问网站时触发警告，并自动清理 Preferences 中的记录。

## 目的：避免重复警告：
该记录的主要目的是让你下次访问同一个网站（使用同一个证书）时，Chrome 不再弹出相同的警告页面，而是直接允许连接（虽然地址栏可能仍会显示“不安全”的提示，如红色划掉的锁图标）。它保存的是你明确确认过的“例外”规则。

## 数据结构：
ssl_cert_decisions 是一个 JSON 格式的字典对象。
键 (Key)： 通常是网站的主机名 (hostname) 或 IP 地址。
值 (Value)： 一个包含证书信息（如证书的哈希值或指纹）以及决策状态（表示已批准）的对象。

## 位置：
Preferences 文件通常位于你的 Chrome 用户数据目录（User Data Directory）中。路径因操作系统而异：
- Windows: %LOCALAPPDATA%\Google\Chrome\User Data\Default\Preferences
- macOS: ~/Library/Application Support/Google/Chrome/Default/Preferences
- Linux: ~/.config/google-chrome/Default/Preferences

## ssl_cert_decisions：
![1.png](1.png)
## last_modified
含义：记录该条决策的最后修改时间戳，即点击“继续前往”的时间。
## decision_expiration_time
含义：决策的过期时间戳。到达此时间后，该决策自动失效。
关联机制：当 decision_expiration_time 到达时，系统可能自动将状态标记为“过期”，并触发重新验证流程。last_modified 在每次决策更新（包括调整有效期）时都会刷新。
## 时间戳计算
（1） 时间基准
Chrome 时间戳从 1601-01-01 00:00:00 UTC 开始计算，单位是微秒（μs）。
（2） 关键偏移量
Unix 时间戳从 1970-01-01 00:00:00 UTC 开始，需要先计算基准差：1601 到 1970 的秒数 = (369 年) × 365 天 + 89 个闰年 = 11644473600 秒
（3） 转换公式
Unix 时间戳 = (Chrome 时间戳 / 1,000,000) - 11644473600
（4） 创建Date对象
	const date = new Date(unixTimestamp * 1000);

浏览器控制台输入计算公式为：
new Date( (chrome 时间戳 /1000000 - 11644473600)*1000)
# 结论验证
以hbm 115环境为例，Chrome 91.0.4471.77（正式版本，64 位） 138.0.7204.50（正式版本，64 位）两个版本验证：
2025.06.25 09:58:30  115环境访问https://10.*.*.*:8877/cm 出现如下提示 ，选择了“继续前往”后，Preferences文件中会记录下115决策时间戳last_modified：13395290310886351，失效时间戳 decision_expiration_time ：13395895110886323, 经计算预计会在2025.07.02 09:58:30 决策失效

![2.png](2.png)
![3.png](3.png)
现象：2025.06.25 09:58:30 临时例外生效，有效期7天，2025.07.02 09:58:30经验证，7月2日 09:58:30之前有效，2025.07.02 09:58:30之后策略失效，登录时相关接口报错，和预计结论保持一致：
![4.png](4.png)

# 快速验证方法：
访问自签名站点 → 跳过警告 → 修改系统时间至7天后 → 重新访问观察警告是否重现
![5.png](5.png)

# Chromium源码中定义了临时例外生命周期
![6.png](6.png)
Chrome >81版本定义文件路径
chromium/components/security_interstitials/content/stateful_ssl_host_state_delegate.cc 如: https://github.com/chromium/chromium/blob/82.0.4085.28/components/security_interstitials/content/stateful_ssl_host_state_delegate.cc
Chrome <=81 版本定义文件路径chromium/chrome/browser/ssl/chrome_ssl_host_state_delegate.cc 如:
https://github.com/chromium/chromium/blob/76.0.3809.146/chrome/browser/ssl/chrome_ssl_host_state_delegate.cc

# 自定义
1. 按 Win + R → 输入 regedit → 回车。
2. 导航至路径：
HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome
3. 右键新建 → DWORD (32 位)值 → 名称：AllowedCertificateOverrideDuration → 双击设置值：30（十进制）。
重启 Chrome。






