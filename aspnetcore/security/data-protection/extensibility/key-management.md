---
title: ASP.NET Core 的密钥管理可扩展性
author: rick-anderson
description: 了解有关 ASP.NET Core 数据保护密钥管理扩展性。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 28932cbef1cc797338980f3e0de8b09caee324c0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654258"
---
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="e94cb-103">ASP.NET Core 的密钥管理可扩展性</span><span class="sxs-lookup"><span data-stu-id="e94cb-103">Key management extensibility in ASP.NET Core</span></span>

> [!TIP]
> <span data-ttu-id="e94cb-104">阅读本部分之前，请阅读[密钥管理](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)部分，因为它说明了这些 api 背后的一些基本概念。</span><span class="sxs-lookup"><span data-stu-id="e94cb-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

> [!WARNING]
> <span data-ttu-id="e94cb-105">实现以下接口的任何类型应该是线程安全的多个调用方。</span><span class="sxs-lookup"><span data-stu-id="e94cb-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="e94cb-106">密钥</span><span class="sxs-lookup"><span data-stu-id="e94cb-106">Key</span></span>

<span data-ttu-id="e94cb-107">`IKey` 接口是 cryptosystem 中密钥的基本表示形式。</span><span class="sxs-lookup"><span data-stu-id="e94cb-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="e94cb-108">在抽象意义上，"加密密钥材料"字面意义上不在此处使用术语该键。</span><span class="sxs-lookup"><span data-stu-id="e94cb-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="e94cb-109">一个键具有以下属性：</span><span class="sxs-lookup"><span data-stu-id="e94cb-109">A key has the following properties:</span></span>

* <span data-ttu-id="e94cb-110">激活、 创建和过期日期</span><span class="sxs-lookup"><span data-stu-id="e94cb-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="e94cb-111">吊销状态</span><span class="sxs-lookup"><span data-stu-id="e94cb-111">Revocation status</span></span>

* <span data-ttu-id="e94cb-112">密钥标识符 (GUID)</span><span class="sxs-lookup"><span data-stu-id="e94cb-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e94cb-113">此外，`IKey` 公开了可用于创建绑定到此密钥的[IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)实例的 `CreateEncryptor` 方法。</span><span class="sxs-lookup"><span data-stu-id="e94cb-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="e94cb-114">此外，`IKey` 公开了可用于创建绑定到此密钥的[IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)实例的 `CreateEncryptorInstance` 方法。</span><span class="sxs-lookup"><span data-stu-id="e94cb-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="e94cb-115">没有用于从 `IKey` 实例检索原始加密材料的 API。</span><span class="sxs-lookup"><span data-stu-id="e94cb-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="e94cb-116">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="e94cb-116">IKeyManager</span></span>

<span data-ttu-id="e94cb-117">`IKeyManager` 接口表示负责常规密钥存储、检索和操作的对象。</span><span class="sxs-lookup"><span data-stu-id="e94cb-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="e94cb-118">它公开三个高级操作：</span><span class="sxs-lookup"><span data-stu-id="e94cb-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="e94cb-119">创建新的密钥，并将其保存到存储。</span><span class="sxs-lookup"><span data-stu-id="e94cb-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="e94cb-120">从存储中获取所有键。</span><span class="sxs-lookup"><span data-stu-id="e94cb-120">Get all keys from storage.</span></span>

* <span data-ttu-id="e94cb-121">撤消一个或多个密钥并保存到存储吊销信息。</span><span class="sxs-lookup"><span data-stu-id="e94cb-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="e94cb-122">编写 `IKeyManager` 是一种非常高级的任务，大多数开发人员都不应尝试。</span><span class="sxs-lookup"><span data-stu-id="e94cb-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="e94cb-123">相反，大多数开发人员应充分利用[XmlKeyManager](#xmlkeymanager)类提供的功能。</span><span class="sxs-lookup"><span data-stu-id="e94cb-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="e94cb-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="e94cb-124">XmlKeyManager</span></span>

<span data-ttu-id="e94cb-125">`XmlKeyManager` 类型是 `IKeyManager`的内置具体实现。</span><span class="sxs-lookup"><span data-stu-id="e94cb-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="e94cb-126">它提供了几个有用的功能，包括密钥托管和静态密钥加密。</span><span class="sxs-lookup"><span data-stu-id="e94cb-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="e94cb-127">此系统中的键表示为 XML 元素（特别是[system.xml.linq.xelement>](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)）。</span><span class="sxs-lookup"><span data-stu-id="e94cb-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="e94cb-128">`XmlKeyManager` 依赖于完成其任务的过程中的多个其他组件：</span><span class="sxs-lookup"><span data-stu-id="e94cb-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="e94cb-129">`AlgorithmConfiguration`，用于指示新密钥使用的算法。</span><span class="sxs-lookup"><span data-stu-id="e94cb-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="e94cb-130">`IXmlRepository`，控制在存储中保留密钥的位置。</span><span class="sxs-lookup"><span data-stu-id="e94cb-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="e94cb-131">`IXmlEncryptor` [可选]，这允许静态加密密钥。</span><span class="sxs-lookup"><span data-stu-id="e94cb-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="e94cb-132">`IKeyEscrowSink` [可选]，它提供密钥委托服务。</span><span class="sxs-lookup"><span data-stu-id="e94cb-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="e94cb-133">`IXmlRepository`，控制在存储中保留密钥的位置。</span><span class="sxs-lookup"><span data-stu-id="e94cb-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="e94cb-134">`IXmlEncryptor` [可选]，这允许静态加密密钥。</span><span class="sxs-lookup"><span data-stu-id="e94cb-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="e94cb-135">`IKeyEscrowSink` [可选]，它提供密钥委托服务。</span><span class="sxs-lookup"><span data-stu-id="e94cb-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="e94cb-136">下面是高级关系图，这些关系图指示如何在 `XmlKeyManager`中将这些组件连接在一起。</span><span class="sxs-lookup"><span data-stu-id="e94cb-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![创建密钥](key-management/_static/keycreation2.png)

<span data-ttu-id="e94cb-138">*密钥创建/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="e94cb-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="e94cb-139">在 `CreateNewKey`的实现中，`AlgorithmConfiguration` 组件用于创建唯一 `IAuthenticatedEncryptorDescriptor`，然后将其序列化为 XML。</span><span class="sxs-lookup"><span data-stu-id="e94cb-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="e94cb-140">如果存在密钥托管接收器，则原始 （未加密） 的 XML 提供到接收器进行长期存储。</span><span class="sxs-lookup"><span data-stu-id="e94cb-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="e94cb-141">然后，将通过 `IXmlEncryptor` （如果需要）运行未加密的 XML，以生成加密的 XML 文档。</span><span class="sxs-lookup"><span data-stu-id="e94cb-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="e94cb-142">此加密文档通过 `IXmlRepository`持久保存到长期存储。</span><span class="sxs-lookup"><span data-stu-id="e94cb-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="e94cb-143">（如果未配置任何 `IXmlEncryptor`，则会将未加密的文档保留在 `IXmlRepository`中。）</span><span class="sxs-lookup"><span data-stu-id="e94cb-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![密钥检索](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![创建密钥](key-management/_static/keycreation1.png)

<span data-ttu-id="e94cb-146">*密钥创建/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="e94cb-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="e94cb-147">在 `CreateNewKey`的实现中，`IAuthenticatedEncryptorConfiguration` 组件用于创建唯一 `IAuthenticatedEncryptorDescriptor`，然后将其序列化为 XML。</span><span class="sxs-lookup"><span data-stu-id="e94cb-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="e94cb-148">如果存在密钥托管接收器，则原始 （未加密） 的 XML 提供到接收器进行长期存储。</span><span class="sxs-lookup"><span data-stu-id="e94cb-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="e94cb-149">然后，将通过 `IXmlEncryptor` （如果需要）运行未加密的 XML，以生成加密的 XML 文档。</span><span class="sxs-lookup"><span data-stu-id="e94cb-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="e94cb-150">此加密文档通过 `IXmlRepository`持久保存到长期存储。</span><span class="sxs-lookup"><span data-stu-id="e94cb-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="e94cb-151">（如果未配置任何 `IXmlEncryptor`，则会将未加密的文档保留在 `IXmlRepository`中。）</span><span class="sxs-lookup"><span data-stu-id="e94cb-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![密钥检索](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="e94cb-153">*密钥检索/GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="e94cb-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="e94cb-154">在 `GetAllKeys`的实现中，将从基础 `IXmlRepository`读取表示键和吊销的 XML 文档。</span><span class="sxs-lookup"><span data-stu-id="e94cb-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="e94cb-155">如果这些文档进行加密，系统将自动解密机密。</span><span class="sxs-lookup"><span data-stu-id="e94cb-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="e94cb-156">`XmlKeyManager` 将创建适当的 `IAuthenticatedEncryptorDescriptorDeserializer` 实例，将文档反序列化为 `IAuthenticatedEncryptorDescriptor` 实例，然后将其包装在单独的 `IKey` 实例中。</span><span class="sxs-lookup"><span data-stu-id="e94cb-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="e94cb-157">此 `IKey` 实例的集合将返回到调用方。</span><span class="sxs-lookup"><span data-stu-id="e94cb-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="e94cb-158">有关特定 XML 元素的详细信息，请参阅[密钥存储格式文档](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format)。</span><span class="sxs-lookup"><span data-stu-id="e94cb-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="e94cb-159">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e94cb-159">IXmlRepository</span></span>

<span data-ttu-id="e94cb-160">`IXmlRepository` 接口表示可将 XML 保存到后备存储并从中检索 XML 的类型。</span><span class="sxs-lookup"><span data-stu-id="e94cb-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="e94cb-161">它公开两个 Api:</span><span class="sxs-lookup"><span data-stu-id="e94cb-161">It exposes two APIs:</span></span>

* <span data-ttu-id="e94cb-162">`GetAllElements`：`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="e94cb-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="e94cb-163">`IXmlRepository` 的实现不需要分析通过它们传递的 XML。</span><span class="sxs-lookup"><span data-stu-id="e94cb-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="e94cb-164">它们应视为不透明的 XML 文档，让较高的层担心如何生成和分析文档。</span><span class="sxs-lookup"><span data-stu-id="e94cb-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="e94cb-165">有四种内置的具体类型可实现 `IXmlRepository`：</span><span class="sxs-lookup"><span data-stu-id="e94cb-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="e94cb-166">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e94cb-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="e94cb-167">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e94cb-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="e94cb-168">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e94cb-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="e94cb-169">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e94cb-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="e94cb-170">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e94cb-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="e94cb-171">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e94cb-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="e94cb-172">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e94cb-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="e94cb-173">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e94cb-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="e94cb-174">有关详细信息，请参阅[密钥存储提供程序文档](xref:security/data-protection/implementation/key-storage-providers)。</span><span class="sxs-lookup"><span data-stu-id="e94cb-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="e94cb-175">使用其他后备存储（例如 Azure 表存储）时，注册自定义 `IXmlRepository` 是合适的。</span><span class="sxs-lookup"><span data-stu-id="e94cb-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="e94cb-176">若要更改应用程序范围内的默认存储库，请注册自定义 `IXmlRepository` 实例：</span><span class="sxs-lookup"><span data-stu-id="e94cb-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

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

## <a name="ixmlencryptor"></a><span data-ttu-id="e94cb-177">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="e94cb-177">IXmlEncryptor</span></span>

<span data-ttu-id="e94cb-178">`IXmlEncryptor` 接口表示可加密纯文本 XML 元素的类型。</span><span class="sxs-lookup"><span data-stu-id="e94cb-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="e94cb-179">它公开一个 API:</span><span class="sxs-lookup"><span data-stu-id="e94cb-179">It exposes a single API:</span></span>

* <span data-ttu-id="e94cb-180">加密 (XElement plaintextElement): EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="e94cb-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="e94cb-181">如果序列化的 `IAuthenticatedEncryptorDescriptor` 包含任何标记为 "需要加密" 的元素，则 `XmlKeyManager` 将通过配置的 `IXmlEncryptor`的 `Encrypt` 方法来运行这些元素，并将到加密元素而不是纯文本元素保存到 `IXmlRepository`。</span><span class="sxs-lookup"><span data-stu-id="e94cb-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="e94cb-182">`Encrypt` 方法的输出是 `EncryptedXmlInfo` 对象。</span><span class="sxs-lookup"><span data-stu-id="e94cb-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="e94cb-183">此对象是包含所生成的到加密 `XElement` 的包装，该类型表示可用于破译相应元素的 `IXmlDecryptor`。</span><span class="sxs-lookup"><span data-stu-id="e94cb-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="e94cb-184">有四种内置的具体类型可实现 `IXmlEncryptor`：</span><span class="sxs-lookup"><span data-stu-id="e94cb-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="e94cb-185">CertificateXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="e94cb-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="e94cb-186">DpapiNGXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="e94cb-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="e94cb-187">DpapiXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="e94cb-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="e94cb-188">NullXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="e94cb-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="e94cb-189">有关详细信息，请参阅[静态密钥加密文档](xref:security/data-protection/implementation/key-encryption-at-rest)。</span><span class="sxs-lookup"><span data-stu-id="e94cb-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="e94cb-190">若要更改应用程序范围内默认的密钥加密机制，请注册自定义 `IXmlEncryptor` 实例：</span><span class="sxs-lookup"><span data-stu-id="e94cb-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

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

## <a name="ixmldecryptor"></a><span data-ttu-id="e94cb-191">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="e94cb-191">IXmlDecryptor</span></span>

<span data-ttu-id="e94cb-192">`IXmlDecryptor` 接口表示一种类型，该类型知道如何解密通过 `IXmlEncryptor`到加密的 `XElement`。</span><span class="sxs-lookup"><span data-stu-id="e94cb-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="e94cb-193">它公开一个 API:</span><span class="sxs-lookup"><span data-stu-id="e94cb-193">It exposes a single API:</span></span>

* <span data-ttu-id="e94cb-194">解密 (XElement encryptedElement): XElement</span><span class="sxs-lookup"><span data-stu-id="e94cb-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="e94cb-195">`Decrypt` 方法撤销 `IXmlEncryptor.Encrypt`所执行的加密。</span><span class="sxs-lookup"><span data-stu-id="e94cb-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="e94cb-196">通常，每个具体 `IXmlEncryptor` 实现都有相应的具体 `IXmlDecryptor` 实现。</span><span class="sxs-lookup"><span data-stu-id="e94cb-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="e94cb-197">实现 `IXmlDecryptor` 的类型应该具有以下两个公共构造函数之一：</span><span class="sxs-lookup"><span data-stu-id="e94cb-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="e94cb-198">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="e94cb-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="e94cb-199">.ctor()</span><span class="sxs-lookup"><span data-stu-id="e94cb-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="e94cb-200">传递给构造函数的 `IServiceProvider` 可能为 null。</span><span class="sxs-lookup"><span data-stu-id="e94cb-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="e94cb-201">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="e94cb-201">IKeyEscrowSink</span></span>

<span data-ttu-id="e94cb-202">`IKeyEscrowSink` 接口表示可以执行敏感信息的委托的类型。</span><span class="sxs-lookup"><span data-stu-id="e94cb-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="e94cb-203">请记住，序列化描述符可能包含敏感信息（如加密材料），这是第一个位置引入[IXmlEncryptor](#ixmlencryptor)类型的结果。</span><span class="sxs-lookup"><span data-stu-id="e94cb-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="e94cb-204">但是，意外的发生，并且密钥环可以删除或损坏。</span><span class="sxs-lookup"><span data-stu-id="e94cb-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="e94cb-205">托管接口提供了紧急转义影线，允许在任何已配置的[IXmlEncryptor](#ixmlencryptor)转换之前访问原始序列化的 XML。</span><span class="sxs-lookup"><span data-stu-id="e94cb-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="e94cb-206">该接口公开单个 API:</span><span class="sxs-lookup"><span data-stu-id="e94cb-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="e94cb-207">应用商店 （Guid keyId、 XElement 元素）</span><span class="sxs-lookup"><span data-stu-id="e94cb-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="e94cb-208">这取决于 `IKeyEscrowSink` 实现，以安全的方式处理所提供的元素，与业务策略一致。</span><span class="sxs-lookup"><span data-stu-id="e94cb-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="e94cb-209">一个可能的实现可能是，托管接收器使用已知的公司 x.509 证书（其中证书的私钥已托管）对 XML 元素进行加密;`CertificateXmlEncryptor` 类型可帮助进行此类处理。</span><span class="sxs-lookup"><span data-stu-id="e94cb-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="e94cb-210">`IKeyEscrowSink` 实现还负责正确保存提供的元素。</span><span class="sxs-lookup"><span data-stu-id="e94cb-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="e94cb-211">默认情况下，不启用任何托管机制，尽管服务器管理员可对[此进行全局配置](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="e94cb-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="e94cb-212">它还可以通过 `IDataProtectionBuilder.AddKeyEscrowSink` 方法以编程方式进行配置，如下面的示例中所示。</span><span class="sxs-lookup"><span data-stu-id="e94cb-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="e94cb-213">`AddKeyEscrowSink` 方法重载会镜像 `IServiceCollection.AddSingleton` 并 `IServiceCollection.AddInstance` 重载，因为 `IKeyEscrowSink` 实例将被单一实例。</span><span class="sxs-lookup"><span data-stu-id="e94cb-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="e94cb-214">如果注册了多个 `IKeyEscrowSink` 实例，则会在密钥生成过程中调用每个实例，因此密钥可同时托管多个机制。</span><span class="sxs-lookup"><span data-stu-id="e94cb-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="e94cb-215">没有用于从 `IKeyEscrowSink` 实例读取材料的 API。</span><span class="sxs-lookup"><span data-stu-id="e94cb-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="e94cb-216">这与托管机制的设计从理论上讲是一致： 它可用于使密钥材料的受信任的颁发机构，并且由于应用程序本身不是受信任的颁发机构，它不应该有权访问其自身托管的材料。</span><span class="sxs-lookup"><span data-stu-id="e94cb-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="e94cb-217">下面的示例代码演示如何创建和注册 `IKeyEscrowSink`，其中密钥是托管的，这样只有 "CONTOSODomain Admins" 的成员才能恢复它们。</span><span class="sxs-lookup"><span data-stu-id="e94cb-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="e94cb-218">若要运行此示例，必须是到已加入域的 Windows 8 / Windows Server 2012 计算机和域控制器必须是 Windows Server 2012 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="e94cb-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
