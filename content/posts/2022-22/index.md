---
title: "公共DNS"
date: 2022-12-03T10:00:00+08:00
draft: false
authors: ["zhyoch"]
tags: [Network]
featuredImagePreview: ""
summary: 国内能够正常使用的DoH与DoT。
---

DNS Privacy Project: https://dnsprivacy.org/public_resolvers/

## 国内DNS

| 官网                                                    | IPV4(53)                 | IPV6(53)                            | DoH(443)                                                                                        | DoT(853)                                  |
| ------------------------------------------------------- | ------------------------ | ----------------------------------- | ----------------------------------------------------------------------------------------------- | ----------------------------------------- |
| [阿里 DNS](https://www.alidns.com/)                     | 223.5.5.5<br />223.6.6.6 | 2400:3200::1<br />2400:3200:baba::1 | https://dns.alidns.com/dns-query                                                                | dns.alidns.com                            |
| [腾讯 DNSPod](https://www.dnspod.cn/Products/publicdns) | 119.29.29.29             | 2402:4e00::                         | https://doh.pub/dns-query<br />https://1.12.12.12/dns-query<br />https://120.53.53.53/dns-query | dot.pub<br />1.12.12.12<br />120.53.53.53 |

## 国外DNS

| 官网                                                                              | IPV4(53)                         | IPV6(53)                                       | DoH(443)                                           | DoT(853)                              |
| --------------------------------------------------------------------------------- | -------------------------------- | ---------------------------------------------- | -------------------------------------------------- | ------------------------------------- |
| [Cloudflare DNS](https://developers.cloudflare.com/1.1.1.1/setup/)                | 1.1.1.1<br />1.0.0.1             | 2606:4700:4700::1111<br />2606:4700:4700::1001 | https://cloudflare-dns.com/dns-query               | cloudflare-dns.com                    |
| [Google DNS](https://developers.google.com/speed/public-dns/docs/using)           | 8.8.8.8<br />8.8.4.4             | 2001:4860:4860::8888<br />2001:4860:4860::8844 | https://dns.google/dns-query                       | dns.google                            |
| [Quad9](https://www.quad9.net/service/service-addresses-and-features/) uncensored | 9.9.9.10<br />149.112.112.10     | 2620:fe::10<br />2620:fe::fe:10                | https://dns10.quad9.net/dns-query                  | dns10.quad9.net                       |
| [CleanBrowsing](https://cleanbrowsing.org/filters/) security-filter               | 185.228.168.9<br />185.228.169.9 | 2a0d:2a00:1::2<br />2a0d:2a00:2::2             | https://doh.cleanbrowsing.org/doh/security-filter/ | security-filter-dns.cleanbrowsing.org |
| [Dnswarden](https://dnswarden.com/index.html)                                     |                                  |                                                | https://dns.dnswarden.com/uncensored               | uncensored.dns.dnswarden.com          |

## DoT测试方法

```bash
echo | openssl s_client -connect dns.alidns.com:853 2>/dev/null | openssl x509 -pubkey -noout | openssl pkey -pubin -outform der | openssl dgst -sha256 -binary | openssl enc -base64
```

