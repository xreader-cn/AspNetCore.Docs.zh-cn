---
title: 在 ASP.NET Core 中的密钥存储格式
author: rick-anderson
description: 了解实现的 ASP.NET Core 数据保护密钥的存储格式的详细信息。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 81df124f3dd0cadf8fd895ab55f66eec6415705f
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208013"
---
# <a name="key-storage-format-in-aspnet-core"></a>在 ASP.NET Core 中的密钥存储格式

<a name="data-protection-implementation-key-storage-format"></a>

对象的 XML 表示形式中的静态存储。 密钥存储的默认目录为 %localappdata%\asp.net\dataprotection-keys\。

## <a name="the-key-element"></a>\<密钥 > 元素

注册表项存在作为密钥存储库中的顶级对象。 按照约定键具有文件名**密钥-{guid}.xml**，其中 {guid} 是密钥的 id。 每个此类文件包含单个密钥。 文件的格式如下所示。

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

\<密钥 > 元素包含以下属性和子元素：

* 密钥 id。此值将被处理为授权，;文件名是只需以方便人们阅读过去。

* 版本的\<密钥 > 元素中，当前固定为 1。

* 密钥的创建、 激活和到期日期。

* 一个\<描述符 > 元素，它包含有关该注册表项中包含的已验证的加密实现的信息。

在上述示例中，密钥的 id 是 {80732141-ec8f-4b80-af9c-c4d2d1ff8901}，它已创建并激活 2015 年 3 月 19 日，并且有 90 天的生存期。 （有时会激活日期可能略微之前如此示例所示的创建日期。 这是因为中的 Api 如何工作和不会造成损害在实践中指出。）

## <a name="the-descriptor-element"></a>\<描述符 > 元素

外部\<描述符 > 元素包含属性 deserializerType，这是实现 IAuthenticatedEncryptorDescriptorDeserializer 的类型的程序集限定名称。 此类型负责读取内部\<描述符 > 元素和用于分析中包含的信息。

特定的格式\<描述符 > 元素取决于经过身份验证的加密程序实现封装的密钥，以及每个反序列化程序类型为此需要稍微不同的格式。 一般情况下，不过，此元素将包含算法的信息 (名称、 类型、 Oid，或类似) 和机密密钥材料。 在上述示例中，该描述符指定此密钥包装 AES-256-CBC 加密 + HMACSHA256 验证。

## <a name="the-encryptedsecret-element"></a>\<EncryptedSecret > 元素

**&lt;EncryptedSecret&gt;** 元素，其中包含的密钥材料的加密的形式可能会显示如果[启用加密的机密的静态](xref:security/data-protection/implementation/key-encryption-at-rest)。 该属性`decryptorType`实现的类型的程序集限定名称[IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)。 此类型负责读取内部 **&lt;encryptedKey&gt;** 元素，并对其恢复原始的纯文本进行解密。

如同`<descriptor>`，特定的格式`<encryptedSecret>`元素取决于正在使用的静态加密机制。 在上述示例中，每个注释使用 Windows DPAPI 对主密钥进行加密。

## <a name="the-revocation-element"></a>\<吊销 > 元素

吊销作为密钥存储库中的顶级对象存在。 根据约定吊销使用文件名**吊销-{timestamp}.xml** （适用于特定日期前撤消所有密钥） 或**吊销-{guid}.xml** （适用于都撤消特定键）。 每个文件将包含一个\<吊销 > 元素。

对于各个键的吊销情况，该文件的内容将如下所示。

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

在这种情况下，撤消指定的键。 如果密钥 id 为"*"，但是，如以下示例中，会撤销其创建日期是在指定的吊销日期之前的所有密钥。

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

\<原因 > 元素永远不会读取系统。 它是只需很方便地存储吊销一个用户可读的理由。
