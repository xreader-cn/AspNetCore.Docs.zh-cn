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
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="2d0bc-103">在 ASP.NET Core 中的密钥存储格式</span><span class="sxs-lookup"><span data-stu-id="2d0bc-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="2d0bc-104">对象的 XML 表示形式中的静态存储。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="2d0bc-105">密钥存储的默认目录为 %localappdata%\asp.net\dataprotection-keys\。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-105">The default directory for key storage is %LOCALAPPDATA%\ASP.NET\DataProtection-Keys\.</span></span>

## <a name="the-key-element"></a><span data-ttu-id="2d0bc-106">\<密钥 > 元素</span><span class="sxs-lookup"><span data-stu-id="2d0bc-106">The \<key> element</span></span>

<span data-ttu-id="2d0bc-107">注册表项存在作为密钥存储库中的顶级对象。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-107">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="2d0bc-108">按照约定键具有文件名**密钥-{guid}.xml**，其中 {guid} 是密钥的 id。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-108">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="2d0bc-109">每个此类文件包含单个密钥。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-109">Each such file contains a single key.</span></span> <span data-ttu-id="2d0bc-110">文件的格式如下所示。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-110">The format of the file is as follows.</span></span>

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

<span data-ttu-id="2d0bc-111">\<密钥 > 元素包含以下属性和子元素：</span><span class="sxs-lookup"><span data-stu-id="2d0bc-111">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="2d0bc-112">密钥 id。此值将被处理为授权，;文件名是只需以方便人们阅读过去。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-112">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="2d0bc-113">版本的\<密钥 > 元素中，当前固定为 1。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-113">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="2d0bc-114">密钥的创建、 激活和到期日期。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-114">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="2d0bc-115">一个\<描述符 > 元素，它包含有关该注册表项中包含的已验证的加密实现的信息。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-115">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="2d0bc-116">在上述示例中，密钥的 id 是 {80732141-ec8f-4b80-af9c-c4d2d1ff8901}，它已创建并激活 2015 年 3 月 19 日，并且有 90 天的生存期。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-116">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="2d0bc-117">（有时会激活日期可能略微之前如此示例所示的创建日期。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-117">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="2d0bc-118">这是因为中的 Api 如何工作和不会造成损害在实践中指出。）</span><span class="sxs-lookup"><span data-stu-id="2d0bc-118">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="2d0bc-119">\<描述符 > 元素</span><span class="sxs-lookup"><span data-stu-id="2d0bc-119">The \<descriptor> element</span></span>

<span data-ttu-id="2d0bc-120">外部\<描述符 > 元素包含属性 deserializerType，这是实现 IAuthenticatedEncryptorDescriptorDeserializer 的类型的程序集限定名称。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-120">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="2d0bc-121">此类型负责读取内部\<描述符 > 元素和用于分析中包含的信息。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-121">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="2d0bc-122">特定的格式\<描述符 > 元素取决于经过身份验证的加密程序实现封装的密钥，以及每个反序列化程序类型为此需要稍微不同的格式。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-122">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="2d0bc-123">一般情况下，不过，此元素将包含算法的信息 (名称、 类型、 Oid，或类似) 和机密密钥材料。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-123">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="2d0bc-124">在上述示例中，该描述符指定此密钥包装 AES-256-CBC 加密 + HMACSHA256 验证。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-124">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="2d0bc-125">\<EncryptedSecret > 元素</span><span class="sxs-lookup"><span data-stu-id="2d0bc-125">The \<encryptedSecret> element</span></span>

<span data-ttu-id="2d0bc-126">**&lt;EncryptedSecret&gt;** 元素，其中包含的密钥材料的加密的形式可能会显示如果[启用加密的机密的静态](xref:security/data-protection/implementation/key-encryption-at-rest)。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-126">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="2d0bc-127">该属性`decryptorType`实现的类型的程序集限定名称[IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-127">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="2d0bc-128">此类型负责读取内部 **&lt;encryptedKey&gt;** 元素，并对其恢复原始的纯文本进行解密。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-128">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="2d0bc-129">如同`<descriptor>`，特定的格式`<encryptedSecret>`元素取决于正在使用的静态加密机制。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-129">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="2d0bc-130">在上述示例中，每个注释使用 Windows DPAPI 对主密钥进行加密。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-130">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="2d0bc-131">\<吊销 > 元素</span><span class="sxs-lookup"><span data-stu-id="2d0bc-131">The \<revocation> element</span></span>

<span data-ttu-id="2d0bc-132">吊销作为密钥存储库中的顶级对象存在。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-132">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="2d0bc-133">根据约定吊销使用文件名**吊销-{timestamp}.xml** （适用于特定日期前撤消所有密钥） 或**吊销-{guid}.xml** （适用于都撤消特定键）。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-133">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="2d0bc-134">每个文件将包含一个\<吊销 > 元素。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-134">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="2d0bc-135">对于各个键的吊销情况，该文件的内容将如下所示。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-135">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="2d0bc-136">在这种情况下，撤消指定的键。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-136">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="2d0bc-137">如果密钥 id 为"\*"，但是，如以下示例中，会撤销其创建日期是在指定的吊销日期之前的所有密钥。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-137">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="2d0bc-138">\<原因 > 元素永远不会读取系统。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-138">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="2d0bc-139">它是只需很方便地存储吊销一个用户可读的理由。</span><span class="sxs-lookup"><span data-stu-id="2d0bc-139">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
