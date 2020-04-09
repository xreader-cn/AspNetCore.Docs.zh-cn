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
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="a350d-103">ASP.NET核心中的关键存储格式</span><span class="sxs-lookup"><span data-stu-id="a350d-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="a350d-104">对象以 XML 表示形式保存。</span><span class="sxs-lookup"><span data-stu-id="a350d-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="a350d-105">密钥存储的默认目录是：</span><span class="sxs-lookup"><span data-stu-id="a350d-105">The default directory for key storage is:</span></span>

* <span data-ttu-id="a350d-106">窗口： _%本地应用数据%%\ASP.NET_数据保护-密钥\*</span><span class="sxs-lookup"><span data-stu-id="a350d-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span></span>
* <span data-ttu-id="a350d-107">macOS / Linux： *$HOME/.aspnet/数据保护-密钥*</span><span class="sxs-lookup"><span data-stu-id="a350d-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span></span>

## <a name="the-key-element"></a><span data-ttu-id="a350d-108">键\<>元素</span><span class="sxs-lookup"><span data-stu-id="a350d-108">The \<key> element</span></span>

<span data-ttu-id="a350d-109">密钥作为顶级对象存在于密钥存储库中。</span><span class="sxs-lookup"><span data-stu-id="a350d-109">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="a350d-110">根据约定键具有文件名**键-{guid}.xml，** 其中{guid} 是密钥的 ID。</span><span class="sxs-lookup"><span data-stu-id="a350d-110">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="a350d-111">每个此类文件都包含一个键。</span><span class="sxs-lookup"><span data-stu-id="a350d-111">Each such file contains a single key.</span></span> <span data-ttu-id="a350d-112">文件的格式如下。</span><span class="sxs-lookup"><span data-stu-id="a350d-112">The format of the file is as follows.</span></span>

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

<span data-ttu-id="a350d-113">\<键>元素包含以下属性和子元素：</span><span class="sxs-lookup"><span data-stu-id="a350d-113">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="a350d-114">密钥 ID。此值被视为权威值;文件名对于人类的可读性来说简直就是一个不错的。</span><span class="sxs-lookup"><span data-stu-id="a350d-114">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="a350d-115">\<键>元素的版本，当前固定在 1。</span><span class="sxs-lookup"><span data-stu-id="a350d-115">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="a350d-116">密钥的创建、激活和到期日期。</span><span class="sxs-lookup"><span data-stu-id="a350d-116">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="a350d-117">\<描述符>元素，其中包含有关此密钥中包含的经过身份验证的加密实现的信息。</span><span class="sxs-lookup"><span data-stu-id="a350d-117">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="a350d-118">在上面的示例中，密钥的 ID 是 {80732141-ec8f-4b80-af9c-c4d2d1ff8901}，它于 2015 年 3 月 19 日创建并激活，其生存期为 90 天。</span><span class="sxs-lookup"><span data-stu-id="a350d-118">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="a350d-119">（有时激活日期可能稍微早于创建日期，如本示例所示。</span><span class="sxs-lookup"><span data-stu-id="a350d-119">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="a350d-120">这是由于 API 的工作方式中一点原因，并且在实践中是无害的。</span><span class="sxs-lookup"><span data-stu-id="a350d-120">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="a350d-121">\<描述符>元素</span><span class="sxs-lookup"><span data-stu-id="a350d-121">The \<descriptor> element</span></span>

<span data-ttu-id="a350d-122">外部\<描述符>元素包含一个属性反序列化类型，该类型是实现 IAuthenticatedEncryptor 描述器解序列化的类型的程序集限定名称。</span><span class="sxs-lookup"><span data-stu-id="a350d-122">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="a350d-123">此类型负责读取内部\<描述符>元素并分析 中包含的信息。</span><span class="sxs-lookup"><span data-stu-id="a350d-123">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="a350d-124">\<描述符>元素的特定格式取决于由密钥封装的经过身份验证的加密器实现，并且每种反序列化器类型都期望为此采用略有不同的格式。</span><span class="sxs-lookup"><span data-stu-id="a350d-124">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="a350d-125">但是，通常，此元素将包含算法信息（名称、类型、OID 或类似）和密钥材料。</span><span class="sxs-lookup"><span data-stu-id="a350d-125">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="a350d-126">在上面的示例中，描述符指定此密钥包装 AES-256-CBC 加密 + HMACSHA256 验证。</span><span class="sxs-lookup"><span data-stu-id="a350d-126">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="a350d-127">加密\<的机密>元素</span><span class="sxs-lookup"><span data-stu-id="a350d-127">The \<encryptedSecret> element</span></span>

<span data-ttu-id="a350d-128">如果[启用静态机密加密](xref:security/data-protection/implementation/key-encryption-at-rest)加密，则可能存在包含密钥材料加密形式的**&lt;加密&gt;机密**元素。</span><span class="sxs-lookup"><span data-stu-id="a350d-128">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="a350d-129">该属性`decryptorType`是实现[IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)的类型的程序集限定名称。</span><span class="sxs-lookup"><span data-stu-id="a350d-129">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="a350d-130">此类型负责读取内部**&lt;加密密钥&gt;** 元素并解密它以恢复原始纯文本。</span><span class="sxs-lookup"><span data-stu-id="a350d-130">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="a350d-131">与`<descriptor>`，`<encryptedSecret>`元素的特定格式取决于正在使用的静态加密机制。</span><span class="sxs-lookup"><span data-stu-id="a350d-131">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="a350d-132">在上面的示例中，主密钥根据注释使用 Windows DPAPI 进行加密。</span><span class="sxs-lookup"><span data-stu-id="a350d-132">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="a350d-133">吊销\<>元素</span><span class="sxs-lookup"><span data-stu-id="a350d-133">The \<revocation> element</span></span>

<span data-ttu-id="a350d-134">吊销作为顶级对象存在于密钥存储库中。</span><span class="sxs-lookup"><span data-stu-id="a350d-134">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="a350d-135">根据约定吊销具有文件名**吊销-[时间戳].xml（** 用于在特定日期之前撤消所有密钥）或**吊销-{guid_.xml（** 用于撤销特定密钥）。</span><span class="sxs-lookup"><span data-stu-id="a350d-135">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="a350d-136">每个文件包含一个\<吊销>元素。</span><span class="sxs-lookup"><span data-stu-id="a350d-136">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="a350d-137">对于单个键的吊销，文件内容如下。</span><span class="sxs-lookup"><span data-stu-id="a350d-137">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="a350d-138">在这种情况下，仅吊销指定的密钥。</span><span class="sxs-lookup"><span data-stu-id="a350d-138">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="a350d-139">但是，如果密钥 ID 为"\*"，则如以下示例所示，创建日期早于指定吊销日期的所有密钥将被吊销。</span><span class="sxs-lookup"><span data-stu-id="a350d-139">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="a350d-140">系统\<永远不会读取>元素的原因。</span><span class="sxs-lookup"><span data-stu-id="a350d-140">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="a350d-141">它只是一个方便的地方，存储一个人类可读的理由的撤销。</span><span class="sxs-lookup"><span data-stu-id="a350d-141">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
