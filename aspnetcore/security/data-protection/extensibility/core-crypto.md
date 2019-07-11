---
title: 在 ASP.NET Core 中的核心加密可扩展性
author: rick-anderson
description: 了解有关 IAuthenticatedEncryptor、 IAuthenticatedEncryptorDescriptor、 IAuthenticatedEncryptorDescriptorDeserializer 和顶级工厂。
ms.author: riande
ms.date: 08/11/2017
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: a5f651e3313cc579b995b45905826a5bffcc241c
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814694"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a>在 ASP.NET Core 中的核心加密可扩展性

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> 实现以下接口的任何类型应该是线程安全的多个调用方。

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a>IAuthenticatedEncryptor

**IAuthenticatedEncryptor**接口是加密子系统的基本构建基块。 通常是每个密钥，一个 IAuthenticatedEncryptor 和 IAuthenticatedEncryptor 实例将对所有加密密钥材料和算法执行加密操作所需的信息。

顾名思义，类型为负责提供经过身份验证的加密和解密服务。 它会公开以下两个 Api。

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

加密方法将返回的 blob，包括到加密的纯文本和身份验证的标记。 尽管 AAD 本身不需要是可恢复最后一个有效负载中域的身份验证标记必须包含的附加经过身份验证的数据 (AAD)。 解密方法验证的身份验证标记，并返回被解码的有效负载。 应为 CryptographicException homogenized （除 ArgumentNullException 和类似） 的所有失败。

> [!NOTE]
> 实际上，IAuthenticatedEncryptor 实例本身不需要包含密钥材料。 例如，实现可以将委托到 HSM 中的所有操作。

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a>如何创建 IAuthenticatedEncryptor

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

**IAuthenticatedEncryptorFactory**接口表示一种类型，知道如何创建[IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)实例。 其 API 是按如下所示。

* CreateEncryptorInstance （IKey 密钥）：IAuthenticatedEncryptor

对于任何给定的 IKey 实例，其 CreateEncryptorInstance 方法创建的任何经过身份验证的加密应被视为等效，如下面的代码示例。

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

**IAuthenticatedEncryptorDescriptor**接口表示一种类型，知道如何创建[IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)实例。 其 API 是按如下所示。

* CreateEncryptorInstance():IAuthenticatedEncryptor

* ExportToXml():XmlSerializedDescriptorInfo

如 IAuthenticatedEncryptor，假定的 IAuthenticatedEncryptorDescriptor 实例包装一个特定键。 这意味着，对于任何给定的 IAuthenticatedEncryptorDescriptor 实例，其 CreateEncryptorInstance 方法创建的任何经过身份验证的加密应视为等效的如下面的代码示例。

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

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a>IAuthenticatedEncryptorDescriptor （ASP.NET Core 2.x 仅）

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

**IAuthenticatedEncryptorDescriptor**接口表示知道如何将自身导出到 XML 的类型。 其 API 是按如下所示。

* ExportToXml():XmlSerializedDescriptorInfo

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a>XML 序列化

IAuthenticatedEncryptor 和 IAuthenticatedEncryptorDescriptor 的主要区别是描述符，知道如何创建加密程序并提供有效的参数。 请考虑其实现依赖于 SymmetricAlgorithm 和 KeyedHashAlgorithm IAuthenticatedEncryptor。 加密程序的作业是使用这些类型，但它并不一定知道这些类型原来所在的位置，因此它不能真正写出如何重新创建自身的应用程序重启时的正确描述。 描述符可作为在此基础上更高的级别。 因为描述符知道如何创建加密器实例 （例如，它知道如何创建所需的算法），以便可以重新创建加密程序实例，应用程序重置后，它可以序列化这一知识以 XML 格式。

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

通过其 ExportToXml 例程，描述符可序列化。 此例程返回 XmlSerializedDescriptorInfo 其中包含两个属性： 描述符和表示的类型的 XElement 表示形式[IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer)可以是用于提供相应的 XElement 此描述符继续研究下去。

序列化的描述符可能包含敏感信息，如加密密钥材料。 数据保护系统必须对其具有永久保存到存储之前加密信息的内置支持。 若要这样做的优点，描述符应将标记包含敏感信息的属性名称"requiresEncryption"的元素 (xmlns"<http://schemas.asp.net/2015/03/dataProtection>")，值"true"。

>[!TIP]
> 没有用于将此属性设置的帮助器 API。 调用 XElement.MarkAsRequiresEncryption() 位于命名空间 Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel 中的扩展方法。

也可以是其中的序列化的描述符不包含敏感信息的情况。 再次考虑在 HSM 中存储的加密密钥的大小写。 序列化本身由于 HSM 就不会暴露的材料以纯文本形式时，描述符不能写出的密钥材料。 相反，描述符可能写出的版本密钥包装的密钥 （如果 HSM 允许以这种方式导出） 或密钥的 HSM 的唯一标识符。

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a>IAuthenticatedEncryptorDescriptorDeserializer

**IAuthenticatedEncryptorDescriptorDeserializer**接口表示知道如何从 XElement IAuthenticatedEncryptorDescriptor 实例反序列化的类型。 它公开了一个方法：

* ImportFromXml （XElement 元素）：IAuthenticatedEncryptorDescriptor

ImportFromXml 方法将返回的 XElement [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml)和创建的原始 IAuthenticatedEncryptorDescriptor 等效。

类型实现 IAuthenticatedEncryptorDescriptorDeserializer 应具有以下两个公共构造函数之一：

* .ctor(IServiceProvider)

* .ctor()

> [!NOTE]
> 传递给构造函数的 IServiceProvider 可能为 null。

## <a name="the-top-level-factory"></a>顶级工厂

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

**AlgorithmConfiguration**类表示的类型： 它知道如何创建[IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor)实例。 它公开一个 API。

* CreateNewDescriptor():IAuthenticatedEncryptorDescriptor

AlgorithmConfiguration 视为顶级工厂。 配置可作为模板。 它包装算法的信息 （例如，此配置生成使用 AES-128-GCM 主密钥描述符），但它尚不支持与特定键相关联。

当调用 CreateNewDescriptor、 仅用于此调用中，创建新的密钥材料，并且生成新 IAuthenticatedEncryptorDescriptor 其包装此密钥材料和所需使用材料的算法信息。 无法在软件中创建 （和保存在内存中） 的密钥材料，它可以创建并保留在 HSM 中，依次类推。 关键点是到 CreateNewDescriptor 任何两个调用应永远不会创建等效 IAuthenticatedEncryptorDescriptor 实例。

AlgorithmConfiguration 类型如充当密钥创建例程的入口点[自动密钥滚动](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)。 若要更改所有将来密钥的实现，设置在 KeyManagementOptions AuthenticatedEncryptorConfiguration 属性。

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

**IAuthenticatedEncryptorConfiguration**接口表示的类型： 它知道如何创建[IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor)实例。 它公开一个 API。

* CreateNewDescriptor():IAuthenticatedEncryptorDescriptor

IAuthenticatedEncryptorConfiguration 视为顶级工厂。 配置可作为模板。 它包装算法的信息 （例如，此配置生成使用 AES-128-GCM 主密钥描述符），但它尚不支持与特定键相关联。

当调用 CreateNewDescriptor、 仅用于此调用中，创建新的密钥材料，并且生成新 IAuthenticatedEncryptorDescriptor 其包装此密钥材料和所需使用材料的算法信息。 无法在软件中创建 （和保存在内存中） 的密钥材料，它可以创建并保留在 HSM 中，依次类推。 关键点是到 CreateNewDescriptor 任何两个调用应永远不会创建等效 IAuthenticatedEncryptorDescriptor 实例。

IAuthenticatedEncryptorConfiguration 类型如充当密钥创建例程的入口点[自动密钥滚动](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)。 若要更改所有将来密钥的实现，请在服务容器中注册的单一实例 IAuthenticatedEncryptorConfiguration。

---
