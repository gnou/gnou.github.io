---
layout: post
title:  "Certificates, Identifiers & Profiles"
date:   2015-05-30
categories: certificate, identifier, profile, provisioning profile
---

## CSR(Certificate Request)
这是一个公钥文件，会被用于换取Certificates。生成过程其实是在本地基于RSA加密得到配对的密钥，私钥存在keychain access中，公钥就是这个文件。   
生成方法有如下两种：   

* **Keychain Access**   
  * *Certificate Assistant* -> *Request a Certificate From a Certificate Authority*   
  * 填写*User Email Address*&*Common Name*(key的名字，最好取一个特殊的名字，方便日后(在Keychain Access中)查找)，留空”CA Email”，”Save to disk”，会在本地生成后缀名为`.certSigningRequest`的CSR文件      
- **Command Line**   
  * `openssl genrsa -out private.key 2048`   
  * `openssl req -new -sha256 -key private.key -out my.certSigningRequest`  

来自Apple的解释：

> When your CSR file is created, a public and private key pair is automatically generated. Your private key is stored on your computer. On a Mac, it is stored in the login Keychain by default and can be viewed in the Keychain Access app under the "Keys" category. Your requested certificate is the public half of your key pair.

## Certificates  

用于代码签名，分Development/Distribution两种类型，前者用于开发，后者用于分发/发布。  
生成步骤如下：  
1. 生成CSR(Certificate request)文件  
2. 在 **Apple Developer** -> **Certificates** 新建Certificate，有的证书可能需要选择App ID，比如push notification，有的不需要，之后上传上一步生成的CSR文件，证书生成。  
3. 下载证书，并双击添加到 *Keychain Access*，  

> 注意：证书生成的过程中需要上传public key，使用的过程中会去验证它对应的private key，如果生成时所用的Mac并不是我们要用的Mac，需要在private key所在的机器上将其导出。只要在*Keychain Access*中找到对应的**证书**(证书和private key在一起)，导出为`.p12`文件，传到另一台Mac，这样另一台Mac就同时拥有了Certificate和private key。

## App ID
App ID 用于标识来自一个开发团队的一个或多个Apps。App ID 由 Team ID 和 Bundle ID search string 组成，如下所示：  
![App ID]({{site.url}}/assets/media/2015-05-30-AppID.png)  
App ID 的前缀是 Team ID，它是由Apple提供的。后缀是 Bundle ID search string ，是由我们自己设置，Bundle ID search string 可以是独一无二、用于表示某个特定的App，也可以是有通配符的、表示一系列的App。

## Provisioning Profile
Provisioning profile同样分为Development/Distribution两种。Development provisioning profile 用于把App安装到指定的设备上，distribution provisioning profile 则用于发布App。  
Provisioning profile 包括了 App ID, certificates, devices(Development provisioning profile，决定了可以把App安装到哪些设备上) 这几部分信息。  
Provisioning profile 都有一个长度一般为一年的有效期，过期之后需要重新验证，不是重新生成，只是重新验证。  
下载 provisioning profile 到本地，这是一个后缀名为`.mobileprovision`的文件。
