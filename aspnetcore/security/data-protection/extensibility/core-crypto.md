---
title: ASP.NET Core 中的核心加密扩展性
author: rick-anderson
description: 了解 IAuthenticatedEncryptor、IAuthenticatedEncryptorDescriptor、IAuthenticatedEncryptorDescriptorDeserializer 和顶层工厂。
ms.author: riande
ms.date: 08/11/2017
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: de34968f21eec28cf375ee9f75d3cb8b212c7e70
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404270"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a><span data-ttu-id="1c4f2-103">ASP.NET Core 中的核心加密扩展性</span><span class="sxs-lookup"><span data-stu-id="1c4f2-103">Core cryptography extensibility in ASP.NET Core</span></span>

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> <span data-ttu-id="1c4f2-104">实现以下任何接口的类型对于多个调用方应是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="1c4f2-105">IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="1c4f2-105">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="1c4f2-106">**IAuthenticatedEncryptor**接口是加密子系统的基本构建基块。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-106">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="1c4f2-107">通常，每个密钥有一个 IAuthenticatedEncryptor，IAuthenticatedEncryptor 实例包装执行加密操作所需的所有加密密钥材料和算法信息。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-107">There's generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="1c4f2-108">顾名思义，类型负责提供经过身份验证的加密和解密服务。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-108">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="1c4f2-109">它公开了以下两个 Api。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-109">It exposes the following two APIs.</span></span>

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

<span data-ttu-id="1c4f2-110">加密方法返回包含到加密纯文本和身份验证标记的 blob。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-110">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="1c4f2-111">身份验证标记必须包含其他经过身份验证的数据（AAD），尽管 AAD 本身无需从最终有效负载中恢复。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-111">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="1c4f2-112">解密方法验证身份验证标记并返回已解码的有效负载。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-112">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="1c4f2-113">所有失败（除了 System.argumentnullexception 和类似）都应该 homogenized 为 System.security.cryptography.cryptographicexception。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-113">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="1c4f2-114">IAuthenticatedEncryptor 实例本身实际上不需要包含密钥材料。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-114">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="1c4f2-115">例如，实现可能会委托给所有操作的 HSM。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-115">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="1c4f2-116">如何创建 IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="1c4f2-116">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="1c4f2-117">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1c4f2-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="1c4f2-118">**IAuthenticatedEncryptorFactory**接口表示知道如何创建[IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)实例的类型。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-118">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="1c4f2-119">其 API 如下所示。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-119">Its API is as follows.</span></span>

* <span data-ttu-id="1c4f2-120">CreateEncryptorInstance （IKey 键）： IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="1c4f2-120">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="1c4f2-121">对于任何给定的 IKey 实例，由其 CreateEncryptorInstance 方法创建的任何经过身份验证的加密程序都应视为等效，如以下代码示例所示。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-121">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="1c4f2-122">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1c4f2-122">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="1c4f2-123">**IAuthenticatedEncryptorDescriptor**接口表示知道如何创建[IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)实例的类型。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-123">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="1c4f2-124">其 API 如下所示。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-124">Its API is as follows.</span></span>

* <span data-ttu-id="1c4f2-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="1c4f2-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="1c4f2-126">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="1c4f2-126">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="1c4f2-127">与 IAuthenticatedEncryptor 一样，假定 IAuthenticatedEncryptorDescriptor 的实例包装一个特定键。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-127">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="1c4f2-128">这意味着，对于任何给定的 IAuthenticatedEncryptorDescriptor 实例，由其 CreateEncryptorInstance 方法创建的任何经过身份验证的加密程序都应视为等效，如以下代码示例中所示。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-128">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="1c4f2-129">IAuthenticatedEncryptorDescriptor （仅 ASP.NET Core 2.x）</span><span class="sxs-lookup"><span data-stu-id="1c4f2-129">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="1c4f2-130">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1c4f2-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="1c4f2-131">**IAuthenticatedEncryptorDescriptor**接口表示一种类型，该类型知道如何将自身导出到 XML。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-131">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="1c4f2-132">其 API 如下所示。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-132">Its API is as follows.</span></span>

* <span data-ttu-id="1c4f2-133">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="1c4f2-133">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="1c4f2-134">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1c4f2-134">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="1c4f2-135">XML 序列化</span><span class="sxs-lookup"><span data-stu-id="1c4f2-135">XML Serialization</span></span>

<span data-ttu-id="1c4f2-136">IAuthenticatedEncryptor 和 IAuthenticatedEncryptorDescriptor 之间的主要区别在于，描述符知道如何创建加密程序并为其提供有效的参数。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-136">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="1c4f2-137">假设其实现依赖于 System.security.cryptography.symmetricalgorithm 和 KeyedHashAlgorithm 的 IAuthenticatedEncryptor。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-137">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="1c4f2-138">加密程序的工作就是使用这些类型，但不一定知道这些类型来自何处，因此，如果应用程序重启，则无法真正地写出如何重新创建其自身的正确说明。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-138">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="1c4f2-139">描述符在此基础上充当更高的级别。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-139">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="1c4f2-140">由于描述符知道如何创建加密程序实例（例如，它知道如何创建所需的算法），因此它可以将 XML 格式的知识序列化，以便可以在应用程序重置后重新创建加密程序实例。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-140">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

<span data-ttu-id="1c4f2-141">描述符可以通过其 ExportToXml 例程进行序列化。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-141">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="1c4f2-142">此例程返回一个 XmlSerializedDescriptorInfo，其中包含两个属性：描述符的 System.xml.linq.xelement> 表示形式和表示[IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer)的类型，该类型可用于在给定相应 system.xml.linq.xelement> 的情况下恢复此描述符。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-142">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="1c4f2-143">序列化描述符可能包含敏感信息，如加密密钥材料。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-143">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="1c4f2-144">数据保护系统在将信息保存到存储之前，对其进行内置支持。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-144">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="1c4f2-145">若要利用这一点，描述符应标记包含具有属性名称 "requiresEncryption" （xmlns " <http://schemas.asp.net/2015/03/dataProtection> "）、值为 "true" 的敏感信息的元素。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-145">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="1c4f2-146">有一个用于设置此属性的帮助器 API。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-146">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="1c4f2-147">调用位于命名空间 Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel 中的扩展方法 MarkAsRequiresEncryption （）。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-147">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="1c4f2-148">也可能存在序列化描述符不包含敏感信息的情况。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-148">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="1c4f2-149">再次考虑存储在 HSM 中的加密密钥的情况。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-149">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="1c4f2-150">此描述符在序列化时无法写出密钥材料，因为 HSM 不会以纯文本形式公开材料。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-150">The descriptor cannot write out the key material when serializing itself since the HSM won't expose the material in plaintext form.</span></span> <span data-ttu-id="1c4f2-151">相反，描述符可能会写出密钥的密钥包装版本（如果 HSM 允许以这种方式导出）或 HSM 自己的密钥唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-151">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="1c4f2-152">IAuthenticatedEncryptorDescriptorDeserializer</span><span class="sxs-lookup"><span data-stu-id="1c4f2-152">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="1c4f2-153">**IAuthenticatedEncryptorDescriptorDeserializer**接口表示知道如何从 system.xml.linq.xelement> 反序列化 IAuthenticatedEncryptorDescriptor 实例的类型。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-153">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="1c4f2-154">它公开了单一方法：</span><span class="sxs-lookup"><span data-stu-id="1c4f2-154">It exposes a single method:</span></span>

* <span data-ttu-id="1c4f2-155">ImportFromXml （System.xml.linq.xelement> 元素）： IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="1c4f2-155">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="1c4f2-156">ImportFromXml 方法采用[IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml)返回的 system.xml.linq.xelement>，并创建原始 IAuthenticatedEncryptorDescriptor 的等效项。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-156">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="1c4f2-157">实现 IAuthenticatedEncryptorDescriptorDeserializer 的类型应具有以下两个公共构造函数之一：</span><span class="sxs-lookup"><span data-stu-id="1c4f2-157">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="1c4f2-158">.ctor （IServiceProvider）</span><span class="sxs-lookup"><span data-stu-id="1c4f2-158">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="1c4f2-159">.ctor （）</span><span class="sxs-lookup"><span data-stu-id="1c4f2-159">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="1c4f2-160">传递给构造函数的 IServiceProvider 可能为 null。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-160">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="1c4f2-161">顶级工厂</span><span class="sxs-lookup"><span data-stu-id="1c4f2-161">The top-level factory</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="1c4f2-162">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1c4f2-162">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="1c4f2-163">**AlgorithmConfiguration**类表示一种知道如何创建[IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor)实例的类型。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-163">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="1c4f2-164">它公开一个 API。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-164">It exposes a single API.</span></span>

* <span data-ttu-id="1c4f2-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="1c4f2-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="1c4f2-166">将 AlgorithmConfiguration 看作顶级工厂。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-166">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="1c4f2-167">配置用作模板。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-167">The configuration serves as a template.</span></span> <span data-ttu-id="1c4f2-168">它包装算法信息（例如，此配置使用 AES-128-GCM 主密钥生成描述符），但尚未与特定密钥相关联。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-168">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="1c4f2-169">调用 CreateNewDescriptor 时，将仅为此调用创建新的密钥材料，并生成新的 IAuthenticatedEncryptorDescriptor 来包装此密钥材料以及使用该材料所需的算法信息。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-169">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="1c4f2-170">密钥材料可以在软件中创建（并保存在内存中），可以在 HSM 中创建和保存它。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-170">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="1c4f2-171">关键是，对 CreateNewDescriptor 的任何两次调用都不应创建等效的 IAuthenticatedEncryptorDescriptor 实例。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-171">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="1c4f2-172">AlgorithmConfiguration 类型用作密钥创建例程（如[自动密钥滚动](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)）的入口点。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-172">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="1c4f2-173">若要更改所有将来键的实现，请在 KeyManagementOptions 中设置 AuthenticatedEncryptorConfiguration 属性。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-173">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="1c4f2-174">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1c4f2-174">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="1c4f2-175">**IAuthenticatedEncryptorConfiguration**接口表示知道如何创建[IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor)实例的类型。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-175">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="1c4f2-176">它公开一个 API。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-176">It exposes a single API.</span></span>

* <span data-ttu-id="1c4f2-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="1c4f2-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="1c4f2-178">将 IAuthenticatedEncryptorConfiguration 看作顶级工厂。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-178">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="1c4f2-179">配置用作模板。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-179">The configuration serves as a template.</span></span> <span data-ttu-id="1c4f2-180">它包装算法信息（例如，此配置使用 AES-128-GCM 主密钥生成描述符），但尚未与特定密钥相关联。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-180">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="1c4f2-181">调用 CreateNewDescriptor 时，将仅为此调用创建新的密钥材料，并生成新的 IAuthenticatedEncryptorDescriptor 来包装此密钥材料以及使用该材料所需的算法信息。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-181">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="1c4f2-182">密钥材料可以在软件中创建（并保存在内存中），可以在 HSM 中创建和保存它。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-182">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="1c4f2-183">关键是，对 CreateNewDescriptor 的任何两次调用都不应创建等效的 IAuthenticatedEncryptorDescriptor 实例。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-183">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="1c4f2-184">IAuthenticatedEncryptorConfiguration 类型用作密钥创建例程（如[自动密钥滚动](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)）的入口点。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-184">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="1c4f2-185">若要更改所有将来键的实现，请在服务容器中注册单一实例 IAuthenticatedEncryptorConfiguration。</span><span class="sxs-lookup"><span data-stu-id="1c4f2-185">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---
