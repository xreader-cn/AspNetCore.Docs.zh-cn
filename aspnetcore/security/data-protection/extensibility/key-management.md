---
title: ASP.NET Core 中的密钥管理扩展性
author: rick-anderson
description: 了解 ASP.NET Core 的数据保护密钥管理扩展性。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: db718b8d4c305b75ad52054efde6b2d03f6825ed
ms.sourcegitcommit: 4cce99cbd44372fd4575e8da8c0f4345949f4d9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89153527"
---
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="b6107-103">ASP.NET Core 中的密钥管理扩展性</span><span class="sxs-lookup"><span data-stu-id="b6107-103">Key management extensibility in ASP.NET Core</span></span>

<span data-ttu-id="b6107-104">阅读本部分之前，请阅读 [密钥管理](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) 部分，因为它说明了这些 api 背后的一些基本概念。</span><span class="sxs-lookup"><span data-stu-id="b6107-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

<span data-ttu-id="b6107-105">**警告**：对于多个调用方，实现以下任何接口的类型应该是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="b6107-105">**Warning**: Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="b6107-106">键</span><span class="sxs-lookup"><span data-stu-id="b6107-106">Key</span></span>

<span data-ttu-id="b6107-107">`IKey`接口是 cryptosystem 中密钥的基本表示形式。</span><span class="sxs-lookup"><span data-stu-id="b6107-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="b6107-108">此处的术语关键字在抽象意义上使用，而不是在 "加密密钥材料" 的文字意义上使用。</span><span class="sxs-lookup"><span data-stu-id="b6107-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="b6107-109">密钥具有以下属性：</span><span class="sxs-lookup"><span data-stu-id="b6107-109">A key has the following properties:</span></span>

* <span data-ttu-id="b6107-110">激活、创建和到期日期</span><span class="sxs-lookup"><span data-stu-id="b6107-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="b6107-111">吊销状态</span><span class="sxs-lookup"><span data-stu-id="b6107-111">Revocation status</span></span>

* <span data-ttu-id="b6107-112">GUID)  (的密钥标识符</span><span class="sxs-lookup"><span data-stu-id="b6107-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b6107-113">此外，还 `IKey` 公开了一个 `CreateEncryptor` 方法，该方法可用于创建绑定到此密钥的 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 实例。</span><span class="sxs-lookup"><span data-stu-id="b6107-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b6107-114">此外，还 `IKey` 公开了一个 `CreateEncryptorInstance` 方法，该方法可用于创建绑定到此密钥的 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 实例。</span><span class="sxs-lookup"><span data-stu-id="b6107-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="b6107-115">没有用于从实例中检索原始加密材料的 API `IKey` 。</span><span class="sxs-lookup"><span data-stu-id="b6107-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="b6107-116">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="b6107-116">IKeyManager</span></span>

<span data-ttu-id="b6107-117">`IKeyManager`接口表示负责常规密钥存储、检索和操作的对象。</span><span class="sxs-lookup"><span data-stu-id="b6107-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="b6107-118">它公开了三个高级操作：</span><span class="sxs-lookup"><span data-stu-id="b6107-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="b6107-119">创建新密钥并将其保存到存储中。</span><span class="sxs-lookup"><span data-stu-id="b6107-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="b6107-120">从存储获取所有密钥。</span><span class="sxs-lookup"><span data-stu-id="b6107-120">Get all keys from storage.</span></span>

* <span data-ttu-id="b6107-121">撤消一个或多个密钥并将吊销信息保存到存储中。</span><span class="sxs-lookup"><span data-stu-id="b6107-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="b6107-122">编写 `IKeyManager` 是一种非常高级的任务，大多数开发人员都不应尝试。</span><span class="sxs-lookup"><span data-stu-id="b6107-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="b6107-123">相反，大多数开发人员应充分利用 [XmlKeyManager](#xmlkeymanager) 类提供的功能。</span><span class="sxs-lookup"><span data-stu-id="b6107-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="b6107-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="b6107-124">XmlKeyManager</span></span>

<span data-ttu-id="b6107-125">`XmlKeyManager`类型是的机箱内的具体实现 `IKeyManager` 。</span><span class="sxs-lookup"><span data-stu-id="b6107-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="b6107-126">它提供若干有用的功能，包括静态密钥的密钥委托和加密。</span><span class="sxs-lookup"><span data-stu-id="b6107-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="b6107-127">此系统中的键表示为 XML 元素 (具体说来就是 [system.xml.linq.xelement>](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)) 。</span><span class="sxs-lookup"><span data-stu-id="b6107-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="b6107-128">`XmlKeyManager` 依赖于完成其任务的过程中的多个其他组件：</span><span class="sxs-lookup"><span data-stu-id="b6107-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="b6107-129">`AlgorithmConfiguration`，它指示新密钥使用的算法。</span><span class="sxs-lookup"><span data-stu-id="b6107-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="b6107-130">`IXmlRepository`，控制在存储中保留密钥的位置。</span><span class="sxs-lookup"><span data-stu-id="b6107-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="b6107-131">`IXmlEncryptor` [可选]，这允许静态加密密钥。</span><span class="sxs-lookup"><span data-stu-id="b6107-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="b6107-132">`IKeyEscrowSink` [可选]，它提供密钥委托服务。</span><span class="sxs-lookup"><span data-stu-id="b6107-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="b6107-133">`IXmlRepository`，控制在存储中保留密钥的位置。</span><span class="sxs-lookup"><span data-stu-id="b6107-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="b6107-134">`IXmlEncryptor` [可选]，这允许静态加密密钥。</span><span class="sxs-lookup"><span data-stu-id="b6107-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="b6107-135">`IKeyEscrowSink` [可选]，它提供密钥委托服务。</span><span class="sxs-lookup"><span data-stu-id="b6107-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="b6107-136">下面是高级关系图，这些关系图指示如何在中将这些组件连接在一起 `XmlKeyManager` 。</span><span class="sxs-lookup"><span data-stu-id="b6107-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![密钥创建](key-management/_static/keycreation2.png)

<span data-ttu-id="b6107-138">*密钥创建/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="b6107-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="b6107-139">在的实现中 `CreateNewKey` ， `AlgorithmConfiguration` 组件用于创建一个唯一的 `IAuthenticatedEncryptorDescriptor` ，然后将其序列化为 XML。</span><span class="sxs-lookup"><span data-stu-id="b6107-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="b6107-140">如果存在密钥托管接收器，则会提供原始 (未加密的) XML，用于长期存储的接收器。</span><span class="sxs-lookup"><span data-stu-id="b6107-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="b6107-141">`IXmlEncryptor`如果需要) 生成加密的 xml 文档，则会通过 (运行未加密的 xml。</span><span class="sxs-lookup"><span data-stu-id="b6107-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="b6107-142">此加密文档通过将持久保存到长期存储 `IXmlRepository` 。</span><span class="sxs-lookup"><span data-stu-id="b6107-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="b6107-143"> (如果未 `IXmlEncryptor` 配置，则会将未加密的文档保留在中 `IXmlRepository` 。 ) </span><span class="sxs-lookup"><span data-stu-id="b6107-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![密钥检索](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![密钥创建](key-management/_static/keycreation1.png)

<span data-ttu-id="b6107-146">*密钥创建/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="b6107-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="b6107-147">在的实现中 `CreateNewKey` ， `IAuthenticatedEncryptorConfiguration` 组件用于创建一个唯一的 `IAuthenticatedEncryptorDescriptor` ，然后将其序列化为 XML。</span><span class="sxs-lookup"><span data-stu-id="b6107-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="b6107-148">如果存在密钥托管接收器，则会提供原始 (未加密的) XML，用于长期存储的接收器。</span><span class="sxs-lookup"><span data-stu-id="b6107-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="b6107-149">`IXmlEncryptor`如果需要) 生成加密的 xml 文档，则会通过 (运行未加密的 xml。</span><span class="sxs-lookup"><span data-stu-id="b6107-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="b6107-150">此加密文档通过将持久保存到长期存储 `IXmlRepository` 。</span><span class="sxs-lookup"><span data-stu-id="b6107-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="b6107-151"> (如果未 `IXmlEncryptor` 配置，则会将未加密的文档保留在中 `IXmlRepository` 。 ) </span><span class="sxs-lookup"><span data-stu-id="b6107-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![密钥检索](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="b6107-153">*密钥检索/GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="b6107-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="b6107-154">在的实现中 `GetAllKeys` ，将从基础读取表示键和吊销的 XML 文档 `IXmlRepository` 。</span><span class="sxs-lookup"><span data-stu-id="b6107-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="b6107-155">如果这些文档已加密，系统会自动将其解密。</span><span class="sxs-lookup"><span data-stu-id="b6107-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="b6107-156">`XmlKeyManager` 创建相应的 `IAuthenticatedEncryptorDescriptorDeserializer` 实例，以将文档反序列化回 `IAuthenticatedEncryptorDescriptor` 实例，然后将其包装在单个 `IKey` 实例中。</span><span class="sxs-lookup"><span data-stu-id="b6107-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="b6107-157">此实例集合 `IKey` 将返回到调用方。</span><span class="sxs-lookup"><span data-stu-id="b6107-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="b6107-158">有关特定 XML 元素的详细信息，请参阅 [密钥存储格式文档](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format)。</span><span class="sxs-lookup"><span data-stu-id="b6107-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="b6107-159">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="b6107-159">IXmlRepository</span></span>

<span data-ttu-id="b6107-160">`IXmlRepository`接口表示可将 xml 持久保存到后备存储并从中检索 xml 的类型。</span><span class="sxs-lookup"><span data-stu-id="b6107-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="b6107-161">它公开两个 Api：</span><span class="sxs-lookup"><span data-stu-id="b6107-161">It exposes two APIs:</span></span>

* <span data-ttu-id="b6107-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="b6107-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="b6107-163">的实现 `IXmlRepository` 不需要分析通过它们传递的 XML。</span><span class="sxs-lookup"><span data-stu-id="b6107-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="b6107-164">它们应将 XML 文档视为不透明，并使更高的层担心生成和分析文档。</span><span class="sxs-lookup"><span data-stu-id="b6107-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="b6107-165">有四种内置的具体类型可实现 `IXmlRepository` ：</span><span class="sxs-lookup"><span data-stu-id="b6107-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="b6107-166">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="b6107-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="b6107-167">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="b6107-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="b6107-168">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="b6107-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="b6107-169">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="b6107-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="b6107-170">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="b6107-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="b6107-171">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="b6107-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="b6107-172">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="b6107-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="b6107-173">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="b6107-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="b6107-174">有关详细信息，请参阅 [密钥存储提供程序文档](xref:security/data-protection/implementation/key-storage-providers) 。</span><span class="sxs-lookup"><span data-stu-id="b6107-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="b6107-175">`IXmlRepository`当使用不同的后备存储 (例如，Azure 表存储) 时，注册自定义适用。</span><span class="sxs-lookup"><span data-stu-id="b6107-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="b6107-176">若要更改应用程序范围内的默认存储库，请注册自定义 `IXmlRepository` 实例：</span><span class="sxs-lookup"><span data-stu-id="b6107-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlRepository = new MyCustomXmlRepository());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlRepository>(new MyCustomXmlRepository());
```

::: moniker-end

## <a name="ixmlencryptor"></a><span data-ttu-id="b6107-177">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="b6107-177">IXmlEncryptor</span></span>

<span data-ttu-id="b6107-178">`IXmlEncryptor`接口表示可加密纯文本 XML 元素的类型。</span><span class="sxs-lookup"><span data-stu-id="b6107-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="b6107-179">它公开一个 API：</span><span class="sxs-lookup"><span data-stu-id="b6107-179">It exposes a single API:</span></span>

* <span data-ttu-id="b6107-180">加密 (System.xml.linq.xelement> plaintextElement) ： EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="b6107-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="b6107-181">如果序列化的 `IAuthenticatedEncryptorDescriptor` 包含任何标记为 "需要加密" 的元素，则 `XmlKeyManager` 将通过配置 `IXmlEncryptor` 的的方法运行这些元素 `Encrypt` ，并将到加密元素而不是纯文本元素保存到 `IXmlRepository` 。</span><span class="sxs-lookup"><span data-stu-id="b6107-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="b6107-182">此方法的输出 `Encrypt` 是一个 `EncryptedXmlInfo` 对象。</span><span class="sxs-lookup"><span data-stu-id="b6107-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="b6107-183">此对象是一个包装，其中包含结果到加密 `XElement` 和类型，该类型表示 `IXmlDecryptor` 可用于对相应元素进行解密的。</span><span class="sxs-lookup"><span data-stu-id="b6107-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="b6107-184">有四种内置的具体类型可实现 `IXmlEncryptor` ：</span><span class="sxs-lookup"><span data-stu-id="b6107-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="b6107-185">CertificateXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="b6107-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="b6107-186">DpapiNGXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="b6107-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="b6107-187">DpapiXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="b6107-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="b6107-188">NullXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="b6107-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="b6107-189">有关详细信息，请参阅 [静态密钥加密文档](xref:security/data-protection/implementation/key-encryption-at-rest) 。</span><span class="sxs-lookup"><span data-stu-id="b6107-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="b6107-190">若要在应用程序范围内更改默认的密钥加密机制，请注册自定义 `IXmlEncryptor` 实例：</span><span class="sxs-lookup"><span data-stu-id="b6107-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlEncryptor = new MyCustomXmlEncryptor());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlEncryptor>(new MyCustomXmlEncryptor());
```

::: moniker-end

## <a name="ixmldecryptor"></a><span data-ttu-id="b6107-191">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="b6107-191">IXmlDecryptor</span></span>

<span data-ttu-id="b6107-192">`IXmlDecryptor`接口表示一种类型，该类型知道如何对 `XElement` 通过进行到加密的进行解密 `IXmlEncryptor` 。</span><span class="sxs-lookup"><span data-stu-id="b6107-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="b6107-193">它公开一个 API：</span><span class="sxs-lookup"><span data-stu-id="b6107-193">It exposes a single API:</span></span>

* <span data-ttu-id="b6107-194">解密 (System.xml.linq.xelement> encryptedElement) ： System.xml.linq.xelement></span><span class="sxs-lookup"><span data-stu-id="b6107-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="b6107-195">`Decrypt`方法撤消执行的加密 `IXmlEncryptor.Encrypt` 。</span><span class="sxs-lookup"><span data-stu-id="b6107-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="b6107-196">通常，每个具体 `IXmlEncryptor` 的实现都具有相应的具体 `IXmlDecryptor` 实现。</span><span class="sxs-lookup"><span data-stu-id="b6107-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="b6107-197">实现的类型 `IXmlDecryptor` 应具有以下两个公共构造函数之一：</span><span class="sxs-lookup"><span data-stu-id="b6107-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="b6107-198">.ctor (IServiceProvider) </span><span class="sxs-lookup"><span data-stu-id="b6107-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="b6107-199">.ctor ( # A1</span><span class="sxs-lookup"><span data-stu-id="b6107-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="b6107-200">`IServiceProvider`传递给构造函数的不能为 null。</span><span class="sxs-lookup"><span data-stu-id="b6107-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="b6107-201">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="b6107-201">IKeyEscrowSink</span></span>

<span data-ttu-id="b6107-202">`IKeyEscrowSink`接口表示可以执行敏感信息的委托的类型。</span><span class="sxs-lookup"><span data-stu-id="b6107-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="b6107-203">请记住，序列化描述符可能包含敏感信息 (如加密材料) ，这就是第一处引入 [IXmlEncryptor](#ixmlencryptor) 类型的结果。</span><span class="sxs-lookup"><span data-stu-id="b6107-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="b6107-204">但是，会发生事故，关键环可能会被删除或损坏。</span><span class="sxs-lookup"><span data-stu-id="b6107-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="b6107-205">托管接口提供了紧急转义影线，允许在任何已配置的 [IXmlEncryptor](#ixmlencryptor)转换之前访问原始序列化的 XML。</span><span class="sxs-lookup"><span data-stu-id="b6107-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="b6107-206">接口公开单个 API：</span><span class="sxs-lookup"><span data-stu-id="b6107-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="b6107-207">存储 (Guid keyId，System.xml.linq.xelement> 元素) </span><span class="sxs-lookup"><span data-stu-id="b6107-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="b6107-208">这是由 `IKeyEscrowSink` 实现来处理所提供的元素，其安全方式与业务策略一致。</span><span class="sxs-lookup"><span data-stu-id="b6107-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="b6107-209">一个可能的实现可能是，托管接收器使用已知的公司 x.509 证书（其中证书的私钥已托管）对 XML 元素进行加密; `CertificateXmlEncryptor` 类型可帮助此。</span><span class="sxs-lookup"><span data-stu-id="b6107-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="b6107-210">`IKeyEscrowSink`实现还负责正确保存提供的元素。</span><span class="sxs-lookup"><span data-stu-id="b6107-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="b6107-211">默认情况下，不启用任何托管机制，尽管服务器管理员可对 [此进行全局配置](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="b6107-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="b6107-212">它还可通过方法以编程方式进行配置 `IDataProtectionBuilder.AddKeyEscrowSink` ，如下面的示例中所示。</span><span class="sxs-lookup"><span data-stu-id="b6107-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="b6107-213">`AddKeyEscrowSink`方法重载镜像 `IServiceCollection.AddSingleton` 和 `IServiceCollection.AddInstance` 重载，因为实例要 `IKeyEscrowSink` 单一实例。</span><span class="sxs-lookup"><span data-stu-id="b6107-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="b6107-214">如果 `IKeyEscrowSink` 注册了多个实例，则会在密钥生成过程中调用每个实例，以便可以将密钥同时托管到多个机制。</span><span class="sxs-lookup"><span data-stu-id="b6107-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="b6107-215">没有用于从实例读取材料的 API `IKeyEscrowSink` 。</span><span class="sxs-lookup"><span data-stu-id="b6107-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="b6107-216">这与托管机制的设计理论一致：它旨在使密钥材料可供受信任的颁发机构访问，因为该应用程序本身不是受信任的颁发机构，所以它不应访问其自己的托管材料。</span><span class="sxs-lookup"><span data-stu-id="b6107-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="b6107-217">下面的示例代码演示如何创建和注册 `IKeyEscrowSink` where 密钥的托管，以便只有 "CONTOSODomain Admins" 的成员才能恢复它们。</span><span class="sxs-lookup"><span data-stu-id="b6107-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="b6107-218">若要运行此示例，你必须在已加入域的 Windows 8/Windows Server 2012 计算机上，并且域控制器必须为 Windows Server 2012 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="b6107-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
