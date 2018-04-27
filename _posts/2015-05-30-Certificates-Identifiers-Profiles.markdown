---
layout: post
title:  "Bundle IDs, Certificates && Provisioning Profiles"
date:   2015-05-30
categories: certificate, identifier, profile, provisioning profile
---

在以 iOS 为代表的 Apple 平台上开发、发布应用需要遵循严格的限制，比如必须要注册开发者账号，开发中的应用不能安装到任意机器上，等等。这里我就解释一下常用的几个名词，方便大家理解。

## Bundle ID
每个应用都应该有一个独一无二的 ID ，系统可以根据这个 ID 来区分不同的应用。跟大多平台一样，这个 ID 字符串一般是 [reverse-DNS](https://littlebitesofcocoa.com/317-crafting-great-reverse-dns-identifiers) 格式，例如 `com.myOrganization.myFirstApp`，要注意的是，这里区分大小写，`myFirstApp`和`myfistapp`并不相同。

#### App ID
我们常说的 App ID 其实是由 Team ID 和 Bundle ID 两部分组成的。Team ID 是由 Apple 生成的，不受我们控制。Apple ID 一般格式如下：
<br><br>
![App ID Structure]({{ "/assets/2015-05-30-Certificates-Identifiers-Profiles-AppID.png" }})
<br><br>

## Signing Certificates/签名证书
开发发布过程中我们会遇到几种不同的证书，最常见的当然就是用于应用签名的证书，用于 Apple Push Notification Service SSL (APNS) 的证书也很常用。下面列出几种常见的证书类型和作用

* **iOS App Development** : 开发过程中为 app 签名。
* **Apple Push Notification service SSL (APNS)** :  用于和 Apple 的 APNS 服务通信使用，分 _Development_ 和 _Production_ 两种。
* **App Store and Ad Hoc** ： 分发 app 时给 app 签名用。

## Provisioning Profiles
上面提到我们的 app 不能安装到任意机器上，在 iOS 平台上可以安装的只能是经过 Apple 签名的应用，但是我们在开发的时候当然不希望每次真机安装都要先把应用提交 Apple，这时候 Provisioning Profile 就扮演了开发者和机器直接连接的桥梁。
Provisioning Profile 其实是一个大合集，里面包含了很多东西，具体如下：

![Provisioning Profile]({{ "/assets/2015-05-30-Certificates-Identifiers-Profiles-Provisioning Profiles-structure.png" }})
<br><br>

* **App ID** : 决定了能安装什么应用。
* **Development Certificate** :  用于给应用签名，证明要安装的这个应用是这个开发者提供的。
* **Device ID**：反映到 iOS 设备上就是 UUID ，这是用于识别机器的一串字符串，每个机器都不同。

