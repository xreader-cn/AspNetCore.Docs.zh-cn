---
title: ASP.NET Core 的密钥管理可扩展性
author: rick-anderson
description: 了解有关 ASP.NET Core 数据保护密钥管理扩展性。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 28932cbef1cc797338980f3e0de8b09caee324c0
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896904"
---
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="c589a-103">ASP.NET Core 的密钥管理可扩展性</span><span class="sxs-lookup"><span data-stu-id="c589a-103">Key management extensibility in ASP.NET Core</span></span>

> [!TIP]
> <span data-ttu-id="c589a-104">读取[密钥管理](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)然后再阅读本部分中，因为它介绍了这些 Api 的基本概念的某些部分。</span><span class="sxs-lookup"><span data-stu-id="c589a-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

> [!WARNING]
> <span data-ttu-id="c589a-105">实现以下接口的任何类型应该是线程安全的多个调用方。</span><span class="sxs-lookup"><span data-stu-id="c589a-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="c589a-106">键</span><span class="sxs-lookup"><span data-stu-id="c589a-106">Key</span></span>

<span data-ttu-id="c589a-107">`IKey`接口是加密系统中的键的基本表示形式。</span><span class="sxs-lookup"><span data-stu-id="c589a-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="c589a-108">在抽象意义上，"加密密钥材料"字面意义上不在此处使用术语该键。</span><span class="sxs-lookup"><span data-stu-id="c589a-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="c589a-109">一个键具有以下属性：</span><span class="sxs-lookup"><span data-stu-id="c589a-109">A key has the following properties:</span></span>

* <span data-ttu-id="c589a-110">激活、 创建和过期日期</span><span class="sxs-lookup"><span data-stu-id="c589a-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="c589a-111">吊销状态</span><span class="sxs-lookup"><span data-stu-id="c589a-111">Revocation status</span></span>

* <span data-ttu-id="c589a-112">密钥标识符 (GUID)</span><span class="sxs-lookup"><span data-stu-id="c589a-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="c589a-113">此外，`IKey`公开`CreateEncryptor`方法用于创建[IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)实例绑定到此密钥。</span><span class="sxs-lookup"><span data-stu-id="c589a-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="c589a-114">此外，`IKey`公开`CreateEncryptorInstance`方法用于创建[IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)实例绑定到此密钥。</span><span class="sxs-lookup"><span data-stu-id="c589a-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="c589a-115">没有 API 来检索从原始的加密材料`IKey`实例。</span><span class="sxs-lookup"><span data-stu-id="c589a-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="c589a-116">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="c589a-116">IKeyManager</span></span>

<span data-ttu-id="c589a-117">`IKeyManager`接口表示负责常规密钥存储、 检索和操作的对象。</span><span class="sxs-lookup"><span data-stu-id="c589a-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="c589a-118">它公开三个高级操作：</span><span class="sxs-lookup"><span data-stu-id="c589a-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="c589a-119">创建新的密钥，并将其保存到存储。</span><span class="sxs-lookup"><span data-stu-id="c589a-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="c589a-120">从存储中获取所有键。</span><span class="sxs-lookup"><span data-stu-id="c589a-120">Get all keys from storage.</span></span>

* <span data-ttu-id="c589a-121">撤消一个或多个密钥并保存到存储吊销信息。</span><span class="sxs-lookup"><span data-stu-id="c589a-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="c589a-122">编写`IKeyManager`是一个非常高级的任务，和大多数开发人员不应尝试。</span><span class="sxs-lookup"><span data-stu-id="c589a-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="c589a-123">相反，大多数开发人员应充分利用所提供的功能[XmlKeyManager](#xmlkeymanager)类。</span><span class="sxs-lookup"><span data-stu-id="c589a-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="c589a-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="c589a-124">XmlKeyManager</span></span>

<span data-ttu-id="c589a-125">`XmlKeyManager`类型是内置具体实现`IKeyManager`。</span><span class="sxs-lookup"><span data-stu-id="c589a-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="c589a-126">它提供了几个有用的功能，包括密钥托管和静态密钥加密。</span><span class="sxs-lookup"><span data-stu-id="c589a-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="c589a-127">在此系统中的密钥都表示为 XML 元素 (具体而言， [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview))。</span><span class="sxs-lookup"><span data-stu-id="c589a-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="c589a-128">`XmlKeyManager` 取决于在完成其任务的过程中的其他几个组件：</span><span class="sxs-lookup"><span data-stu-id="c589a-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="c589a-129">`AlgorithmConfiguration`这决定了使用新的密钥的算法。</span><span class="sxs-lookup"><span data-stu-id="c589a-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="c589a-130">`IXmlRepository`其中将密钥保存在存储中的控件。</span><span class="sxs-lookup"><span data-stu-id="c589a-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="c589a-131">`IXmlEncryptor` [可选]，它允许加密静态密钥。</span><span class="sxs-lookup"><span data-stu-id="c589a-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="c589a-132">`IKeyEscrowSink` [可选] 提供密钥托管服务。</span><span class="sxs-lookup"><span data-stu-id="c589a-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="c589a-133">`IXmlRepository`其中将密钥保存在存储中的控件。</span><span class="sxs-lookup"><span data-stu-id="c589a-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="c589a-134">`IXmlEncryptor` [可选]，它允许加密静态密钥。</span><span class="sxs-lookup"><span data-stu-id="c589a-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="c589a-135">`IKeyEscrowSink` [可选] 提供密钥托管服务。</span><span class="sxs-lookup"><span data-stu-id="c589a-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="c589a-136">下面是高级的关系图，指示如何这些组件连接在一起内`XmlKeyManager`。</span><span class="sxs-lookup"><span data-stu-id="c589a-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![创建密钥](key-management/_static/keycreation2.png)

<span data-ttu-id="c589a-138">*密钥创建 / CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="c589a-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="c589a-139">在实现中的`CreateNewKey`，则`AlgorithmConfiguration`组件用于创建唯一`IAuthenticatedEncryptorDescriptor`，然后序列化为 XML。</span><span class="sxs-lookup"><span data-stu-id="c589a-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="c589a-140">如果存在密钥托管接收器，则原始 （未加密） 的 XML 提供到接收器进行长期存储。</span><span class="sxs-lookup"><span data-stu-id="c589a-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="c589a-141">未加密的 XML 然后通过运行`IXmlEncryptor`（如果需要） 来生成加密的 XML 文档。</span><span class="sxs-lookup"><span data-stu-id="c589a-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="c589a-142">此加密的文档保存到长期存储通过`IXmlRepository`。</span><span class="sxs-lookup"><span data-stu-id="c589a-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="c589a-143">(如果没有`IXmlEncryptor`是配置，未加密的文档保存在`IXmlRepository`。)</span><span class="sxs-lookup"><span data-stu-id="c589a-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![密钥检索](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![创建密钥](key-management/_static/keycreation1.png)

<span data-ttu-id="c589a-146">*密钥创建 / CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="c589a-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="c589a-147">在实现中的`CreateNewKey`，则`IAuthenticatedEncryptorConfiguration`组件用于创建唯一`IAuthenticatedEncryptorDescriptor`，然后序列化为 XML。</span><span class="sxs-lookup"><span data-stu-id="c589a-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="c589a-148">如果存在密钥托管接收器，则原始 （未加密） 的 XML 提供到接收器进行长期存储。</span><span class="sxs-lookup"><span data-stu-id="c589a-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="c589a-149">未加密的 XML 然后通过运行`IXmlEncryptor`（如果需要） 来生成加密的 XML 文档。</span><span class="sxs-lookup"><span data-stu-id="c589a-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="c589a-150">此加密的文档保存到长期存储通过`IXmlRepository`。</span><span class="sxs-lookup"><span data-stu-id="c589a-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="c589a-151">(如果没有`IXmlEncryptor`是配置，未加密的文档保存在`IXmlRepository`。)</span><span class="sxs-lookup"><span data-stu-id="c589a-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![密钥检索](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="c589a-153">*密钥检索 / GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="c589a-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="c589a-154">在实现中的`GetAllKeys`、 XML 文档表示密钥和吊销读取从基础`IXmlRepository`。</span><span class="sxs-lookup"><span data-stu-id="c589a-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="c589a-155">如果这些文档进行加密，系统将自动解密机密。</span><span class="sxs-lookup"><span data-stu-id="c589a-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="c589a-156">`XmlKeyManager` 创建适当`IAuthenticatedEncryptorDescriptorDeserializer`实例进行反序列化文档回`IAuthenticatedEncryptorDescriptor`实例，然后将封装在单个`IKey`实例。</span><span class="sxs-lookup"><span data-stu-id="c589a-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="c589a-157">此集合的`IKey`实例返回给调用方。</span><span class="sxs-lookup"><span data-stu-id="c589a-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="c589a-158">可在特定的 XML 元素的详细信息[密钥存储格式的文档](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format)。</span><span class="sxs-lookup"><span data-stu-id="c589a-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="c589a-159">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="c589a-159">IXmlRepository</span></span>

<span data-ttu-id="c589a-160">`IXmlRepository`接口表示可以持久保存到 XML 并从后备存储中检索 XML 的类型。</span><span class="sxs-lookup"><span data-stu-id="c589a-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="c589a-161">它公开两个 Api:</span><span class="sxs-lookup"><span data-stu-id="c589a-161">It exposes two APIs:</span></span>

* <span data-ttu-id="c589a-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="c589a-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="c589a-163">实现`IXmlRepository`无需分析通过其传递的 XML。</span><span class="sxs-lookup"><span data-stu-id="c589a-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="c589a-164">它们应视为不透明的 XML 文档，让较高的层担心如何生成和分析文档。</span><span class="sxs-lookup"><span data-stu-id="c589a-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="c589a-165">有四种内置的具体类型实现`IXmlRepository`:</span><span class="sxs-lookup"><span data-stu-id="c589a-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="c589a-166">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="c589a-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="c589a-167">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="c589a-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="c589a-168">AzureStorage.AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="c589a-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="c589a-169">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="c589a-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="c589a-170">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="c589a-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="c589a-171">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="c589a-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="c589a-172">AzureStorage.AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="c589a-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="c589a-173">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="c589a-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="c589a-174">请参阅[密钥存储提供程序文档](xref:security/data-protection/implementation/key-storage-providers)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="c589a-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="c589a-175">注册的自定义`IXmlRepository`适合使用不同的后备存储 （例如，Azure 表存储） 时。</span><span class="sxs-lookup"><span data-stu-id="c589a-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="c589a-176">若要更改默认存储库应用程序范围，请注册自定义`IXmlRepository`实例：</span><span class="sxs-lookup"><span data-stu-id="c589a-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

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

## <a name="ixmlencryptor"></a><span data-ttu-id="c589a-177">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="c589a-177">IXmlEncryptor</span></span>

<span data-ttu-id="c589a-178">`IXmlEncryptor`接口表示可以加密纯文本 XML 元素的类型。</span><span class="sxs-lookup"><span data-stu-id="c589a-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="c589a-179">它公开一个 API:</span><span class="sxs-lookup"><span data-stu-id="c589a-179">It exposes a single API:</span></span>

* <span data-ttu-id="c589a-180">Encrypt(XElement plaintextElement):EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="c589a-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="c589a-181">如果一个序列化`IAuthenticatedEncryptorDescriptor`包含任何元素标记为"需要加密"，然后`XmlKeyManager`将通过已配置运行这些元素`IXmlEncryptor`的`Encrypt`方法，并将保存到加密的元素而不是为纯文本元素`IXmlRepository`。</span><span class="sxs-lookup"><span data-stu-id="c589a-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="c589a-182">输出`Encrypt`方法是`EncryptedXmlInfo`对象。</span><span class="sxs-lookup"><span data-stu-id="c589a-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="c589a-183">此对象是包含两个结果到加密的包装`XElement`表示的类型和`IXmlDecryptor`这可用于解密的相应元素。</span><span class="sxs-lookup"><span data-stu-id="c589a-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="c589a-184">有四种内置的具体类型实现`IXmlEncryptor`:</span><span class="sxs-lookup"><span data-stu-id="c589a-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="c589a-185">CertificateXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="c589a-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="c589a-186">DpapiNGXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="c589a-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="c589a-187">DpapiXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="c589a-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="c589a-188">NullXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="c589a-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="c589a-189">请参阅[rest 文档在密钥加密](xref:security/data-protection/implementation/key-encryption-at-rest)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="c589a-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="c589a-190">若要更改默认在静态密钥加密机制整个应用程序范围，请注册自定义`IXmlEncryptor`实例：</span><span class="sxs-lookup"><span data-stu-id="c589a-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

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

## <a name="ixmldecryptor"></a><span data-ttu-id="c589a-191">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="c589a-191">IXmlDecryptor</span></span>

<span data-ttu-id="c589a-192">`IXmlDecryptor`接口表示一种类型，知道如何解密`XElement`的已加密通过`IXmlEncryptor`。</span><span class="sxs-lookup"><span data-stu-id="c589a-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="c589a-193">它公开一个 API:</span><span class="sxs-lookup"><span data-stu-id="c589a-193">It exposes a single API:</span></span>

* <span data-ttu-id="c589a-194">Decrypt(XElement encryptedElement) :XElement</span><span class="sxs-lookup"><span data-stu-id="c589a-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="c589a-195">`Decrypt`方法撤消由执行的加密`IXmlEncryptor.Encrypt`。</span><span class="sxs-lookup"><span data-stu-id="c589a-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="c589a-196">通常情况下，每个具体`IXmlEncryptor`实现都将具有相应的具体`IXmlDecryptor`实现。</span><span class="sxs-lookup"><span data-stu-id="c589a-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="c589a-197">类型实现`IXmlDecryptor`应具有以下两个公共构造函数之一：</span><span class="sxs-lookup"><span data-stu-id="c589a-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="c589a-198">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="c589a-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="c589a-199">.ctor()</span><span class="sxs-lookup"><span data-stu-id="c589a-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="c589a-200">`IServiceProvider`传递给构造函数可能为 null。</span><span class="sxs-lookup"><span data-stu-id="c589a-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="c589a-201">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="c589a-201">IKeyEscrowSink</span></span>

<span data-ttu-id="c589a-202">`IKeyEscrowSink`接口表示可执行托管的敏感信息的类型。</span><span class="sxs-lookup"><span data-stu-id="c589a-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="c589a-203">回想一下，序列化的描述符可能包含敏感信息 （如加密材料），这是什么导致了引入[IXmlEncryptor](#ixmlencryptor)键入第一个位置中。</span><span class="sxs-lookup"><span data-stu-id="c589a-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="c589a-204">但是，意外的发生，并且密钥环可以删除或损坏。</span><span class="sxs-lookup"><span data-stu-id="c589a-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="c589a-205">托管接口提供了允许访问原始序列化的 XML，通过配置的任何转换前一个紧急应急[IXmlEncryptor](#ixmlencryptor)。</span><span class="sxs-lookup"><span data-stu-id="c589a-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="c589a-206">该接口公开单个 API:</span><span class="sxs-lookup"><span data-stu-id="c589a-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="c589a-207">应用商店 （Guid keyId、 XElement 元素）</span><span class="sxs-lookup"><span data-stu-id="c589a-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="c589a-208">这就需要通过`IKeyEscrowSink`实现来处理提供的元素以安全的方式与业务策略保持一致。</span><span class="sxs-lookup"><span data-stu-id="c589a-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="c589a-209">一个可能的实现可能是托管接收器使用的是已知的公司 X.509 证书的 XML 元素进行加密的证书的私钥已托管;`CertificateXmlEncryptor`可以帮助解决这个问题类型。</span><span class="sxs-lookup"><span data-stu-id="c589a-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="c589a-210">`IKeyEscrowSink`实现程序还负责适当地保留提供的元素。</span><span class="sxs-lookup"><span data-stu-id="c589a-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="c589a-211">默认情况下没有托管启用了机制，但服务器管理员可以[全局配置此](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="c589a-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="c589a-212">它还可以配置以编程方式通过`IDataProtectionBuilder.AddKeyEscrowSink`方法，如下面的示例中所示。</span><span class="sxs-lookup"><span data-stu-id="c589a-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="c589a-213">`AddKeyEscrowSink`方法重载镜像`IServiceCollection.AddSingleton`并`IServiceCollection.AddInstance`重载，为`IKeyEscrowSink`实例都应是单一实例。</span><span class="sxs-lookup"><span data-stu-id="c589a-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="c589a-214">如果多个`IKeyEscrowSink`注册实例，每个将调用在密钥生成过程，因此密钥可同时托管多个机制。</span><span class="sxs-lookup"><span data-stu-id="c589a-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="c589a-215">没有 API 中读取数据从`IKeyEscrowSink`实例。</span><span class="sxs-lookup"><span data-stu-id="c589a-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="c589a-216">这与托管机制的设计从理论上讲是一致： 它可用于使密钥材料的受信任的颁发机构，并且由于应用程序本身不是受信任的颁发机构，它不应该有权访问其自身托管的材料。</span><span class="sxs-lookup"><span data-stu-id="c589a-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="c589a-217">下面的示例代码演示如何创建并注册`IKeyEscrowSink`密钥托管，以便只有"CONTOSODomain 管理员"的成员可以恢复它们。</span><span class="sxs-lookup"><span data-stu-id="c589a-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="c589a-218">若要运行此示例，必须是到已加入域的 Windows 8 / Windows Server 2012 计算机和域控制器必须是 Windows Server 2012 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="c589a-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
