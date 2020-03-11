---
title: 在 ASP.NET Core 中的密钥存储格式
author: rick-anderson
description: 了解实现的 ASP.NET Core 数据保护密钥的存储格式的详细信息。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 81df124f3dd0cadf8fd895ab55f66eec6415705f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654996"
---
# <a name="key-storage-format-in-aspnet-core"></a>在 ASP.NET Core 中的密钥存储格式

<a name="data-protection-implementation-key-storage-format"></a>

对象静态存储在 XML 表示形式中。 密钥存储的默认目录为%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\。

## <a name="the-key-element"></a>\<键 > 元素

键作为顶级对象存在于密钥存储库中。 按约定键具有文件名**key {guid} .xml**，其中 {guid} 是密钥的 id。 每个这样的文件都包含一个键。 文件的格式如下所示。

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

\<键 > 元素包含以下属性和子元素：

* 密钥 id。此值被视为权威值;文件名只是一种 nicety 的可读性。

* \<键 > 元素的版本，当前已固定为1。

* 密钥的创建、激活和到期日期。

* \<描述符 > 元素，其中包含有关此密钥中包含的经过身份验证的加密实现的信息。

在上面的示例中，密钥 id 是 {80732141-ec8f-4b80-af9c-c4d2d1ff8901}，它是在年3月 2015 19 日创建和激活的，它的生存期为90天。 （有时激活日期可能会略微早于创建日期，如本示例中所示。 这是因为 Api 的工作方式吹毛求疵，在实践中是无害的。）

## <a name="the-descriptor-element"></a>\<说明符 > 元素

外部 \<描述符 > 元素包含 deserializerType 属性，该属性是实现 IAuthenticatedEncryptorDescriptorDeserializer 的类型的程序集限定名称。 此类型负责读取内部 \<描述符 > 元素和分析中包含的信息。

\<描述符 > 元素的特定格式依赖于通过密钥封装的经过身份验证的加密器实现，并且每个反序列化程序类型都需要为此设置略有不同的格式。 但一般情况下，此元素将包含算法信息（名称、类型、Oid 或类似）和密钥材料。 在上面的示例中，描述符指定此密钥包装 AES-256-CBC encryption + HMACSHA256 验证。

## <a name="the-encryptedsecret-element"></a>\<encryptedSecret > 元素

如果[启用静态加密机密，](xref:security/data-protection/implementation/key-encryption-at-rest)则包含加密形式的密钥材料的 **&lt;encryptedSecret&gt;** 元素可能存在。 特性 `decryptorType` 是实现[IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)的类型的程序集限定名称。 此类型负责读取内部 **&lt;encryptedKey&gt;** 元素，并对其进行解密以恢复原始纯文本。

与 `<descriptor>`一样，`<encryptedSecret>` 元素的特定格式依赖于正在使用中的静态加密机制。 在上面的示例中，使用 Windows DPAPI 按注释对主密钥进行加密。

## <a name="the-revocation-element"></a>\<吊销 > 元素

吊销作为顶级对象存在于密钥存储库中。 按照约定吊销具有文件名**吊销-{timestamp} .xml** （用于在特定日期前撤销所有密钥）或**吊销-{guid} .xml** （用于吊销特定密钥）。 每个文件都包含单个 \<吊销 > 元素。

对于单个密钥的吊销，文件内容将如下所示。

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

在这种情况下，仅吊销指定的密钥。 不过，如果密钥 id 为 "*"，如以下示例中所示，创建日期早于指定吊销日期的所有密钥都将被吊销。

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

系统永远不会读取 \<原因 > 元素。 这只是一个方便的位置，用于存储可供用户阅读的吊销原因。
