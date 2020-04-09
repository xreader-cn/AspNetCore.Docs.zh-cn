---
title: ASP.NET核心中的关键存储格式
author: rick-anderson
description: 了解ASP.NET核心数据保护密钥存储格式的实现详细信息。
ms.author: riande
ms.date: 04/08/2020
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 3072c673791b589027a910b80eaba52052eb9311
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976932"
---
# <a name="key-storage-format-in-aspnet-core"></a>ASP.NET核心中的关键存储格式

<a name="data-protection-implementation-key-storage-format"></a>

对象以 XML 表示形式保存。 密钥存储的默认目录是：

* 窗口： _%本地应用数据%%\ASP.NET_数据保护-密钥\*
* macOS / Linux： *$HOME/.aspnet/数据保护-密钥*

## <a name="the-key-element"></a>键\<>元素

密钥作为顶级对象存在于密钥存储库中。 根据约定键具有文件名**键-{guid}.xml，** 其中{guid} 是密钥的 ID。 每个此类文件都包含一个键。 文件的格式如下。

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

\<键>元素包含以下属性和子元素：

* 密钥 ID。此值被视为权威值;文件名对于人类的可读性来说简直就是一个不错的。

* \<键>元素的版本，当前固定在 1。

* 密钥的创建、激活和到期日期。

* \<描述符>元素，其中包含有关此密钥中包含的经过身份验证的加密实现的信息。

在上面的示例中，密钥的 ID 是 {80732141-ec8f-4b80-af9c-c4d2d1ff8901}，它于 2015 年 3 月 19 日创建并激活，其生存期为 90 天。 （有时激活日期可能稍微早于创建日期，如本示例所示。 这是由于 API 的工作方式中一点原因，并且在实践中是无害的。

## <a name="the-descriptor-element"></a>\<描述符>元素

外部\<描述符>元素包含一个属性反序列化类型，该类型是实现 IAuthenticatedEncryptor 描述器解序列化的类型的程序集限定名称。 此类型负责读取内部\<描述符>元素并分析 中包含的信息。

\<描述符>元素的特定格式取决于由密钥封装的经过身份验证的加密器实现，并且每种反序列化器类型都期望为此采用略有不同的格式。 但是，通常，此元素将包含算法信息（名称、类型、OID 或类似）和密钥材料。 在上面的示例中，描述符指定此密钥包装 AES-256-CBC 加密 + HMACSHA256 验证。

## <a name="the-encryptedsecret-element"></a>加密\<的机密>元素

如果[启用静态机密加密](xref:security/data-protection/implementation/key-encryption-at-rest)加密，则可能存在包含密钥材料加密形式的**&lt;加密&gt;机密**元素。 该属性`decryptorType`是实现[IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)的类型的程序集限定名称。 此类型负责读取内部**&lt;加密密钥&gt;** 元素并解密它以恢复原始纯文本。

与`<descriptor>`，`<encryptedSecret>`元素的特定格式取决于正在使用的静态加密机制。 在上面的示例中，主密钥根据注释使用 Windows DPAPI 进行加密。

## <a name="the-revocation-element"></a>吊销\<>元素

吊销作为顶级对象存在于密钥存储库中。 根据约定吊销具有文件名**吊销-[时间戳].xml（** 用于在特定日期之前撤消所有密钥）或**吊销-{guid_.xml（** 用于撤销特定密钥）。 每个文件包含一个\<吊销>元素。

对于单个键的吊销，文件内容如下。

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

在这种情况下，仅吊销指定的密钥。 但是，如果密钥 ID 为"*"，则如以下示例所示，创建日期早于指定吊销日期的所有密钥将被吊销。

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

系统\<永远不会读取>元素的原因。 它只是一个方便的地方，存储一个人类可读的理由的撤销。
