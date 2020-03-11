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
# <a name="key-management-extensibility-in-aspnet-core"></a>ASP.NET Core 的密钥管理可扩展性

> [!TIP]
> 阅读本部分之前，请阅读[密钥管理](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)部分，因为它说明了这些 api 背后的一些基本概念。

> [!WARNING]
> 实现以下接口的任何类型应该是线程安全的多个调用方。

## <a name="key"></a>密钥

`IKey` 接口是 cryptosystem 中密钥的基本表示形式。 在抽象意义上，"加密密钥材料"字面意义上不在此处使用术语该键。 一个键具有以下属性：

* 激活、 创建和过期日期

* 吊销状态

* 密钥标识符 (GUID)

::: moniker range=">= aspnetcore-2.0"

此外，`IKey` 公开了可用于创建绑定到此密钥的[IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)实例的 `CreateEncryptor` 方法。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

此外，`IKey` 公开了可用于创建绑定到此密钥的[IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)实例的 `CreateEncryptorInstance` 方法。

::: moniker-end

> [!NOTE]
> 没有用于从 `IKey` 实例检索原始加密材料的 API。

## <a name="ikeymanager"></a>IKeyManager

`IKeyManager` 接口表示负责常规密钥存储、检索和操作的对象。 它公开三个高级操作：

* 创建新的密钥，并将其保存到存储。

* 从存储中获取所有键。

* 撤消一个或多个密钥并保存到存储吊销信息。

>[!WARNING]
> 编写 `IKeyManager` 是一种非常高级的任务，大多数开发人员都不应尝试。 相反，大多数开发人员应充分利用[XmlKeyManager](#xmlkeymanager)类提供的功能。

## <a name="xmlkeymanager"></a>XmlKeyManager

`XmlKeyManager` 类型是 `IKeyManager`的内置具体实现。 它提供了几个有用的功能，包括密钥托管和静态密钥加密。 此系统中的键表示为 XML 元素（特别是[system.xml.linq.xelement>](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)）。

`XmlKeyManager` 依赖于完成其任务的过程中的多个其他组件：

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`，用于指示新密钥使用的算法。

* `IXmlRepository`，控制在存储中保留密钥的位置。

* `IXmlEncryptor` [可选]，这允许静态加密密钥。

* `IKeyEscrowSink` [可选]，它提供密钥委托服务。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`，控制在存储中保留密钥的位置。

* `IXmlEncryptor` [可选]，这允许静态加密密钥。

* `IKeyEscrowSink` [可选]，它提供密钥委托服务。

::: moniker-end

下面是高级关系图，这些关系图指示如何在 `XmlKeyManager`中将这些组件连接在一起。

::: moniker range=">= aspnetcore-2.0"

![创建密钥](key-management/_static/keycreation2.png)

*密钥创建/CreateNewKey*

在 `CreateNewKey`的实现中，`AlgorithmConfiguration` 组件用于创建唯一 `IAuthenticatedEncryptorDescriptor`，然后将其序列化为 XML。 如果存在密钥托管接收器，则原始 （未加密） 的 XML 提供到接收器进行长期存储。 然后，将通过 `IXmlEncryptor` （如果需要）运行未加密的 XML，以生成加密的 XML 文档。 此加密文档通过 `IXmlRepository`持久保存到长期存储。 （如果未配置任何 `IXmlEncryptor`，则会将未加密的文档保留在 `IXmlRepository`中。）

![密钥检索](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![创建密钥](key-management/_static/keycreation1.png)

*密钥创建/CreateNewKey*

在 `CreateNewKey`的实现中，`IAuthenticatedEncryptorConfiguration` 组件用于创建唯一 `IAuthenticatedEncryptorDescriptor`，然后将其序列化为 XML。 如果存在密钥托管接收器，则原始 （未加密） 的 XML 提供到接收器进行长期存储。 然后，将通过 `IXmlEncryptor` （如果需要）运行未加密的 XML，以生成加密的 XML 文档。 此加密文档通过 `IXmlRepository`持久保存到长期存储。 （如果未配置任何 `IXmlEncryptor`，则会将未加密的文档保留在 `IXmlRepository`中。）

![密钥检索](key-management/_static/keyretrieval1.png)

::: moniker-end

*密钥检索/GetAllKeys*

在 `GetAllKeys`的实现中，将从基础 `IXmlRepository`读取表示键和吊销的 XML 文档。 如果这些文档进行加密，系统将自动解密机密。 `XmlKeyManager` 将创建适当的 `IAuthenticatedEncryptorDescriptorDeserializer` 实例，将文档反序列化为 `IAuthenticatedEncryptorDescriptor` 实例，然后将其包装在单独的 `IKey` 实例中。 此 `IKey` 实例的集合将返回到调用方。

有关特定 XML 元素的详细信息，请参阅[密钥存储格式文档](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format)。

## <a name="ixmlrepository"></a>IXmlRepository

`IXmlRepository` 接口表示可将 XML 保存到后备存储并从中检索 XML 的类型。 它公开两个 Api:

* `GetAllElements`：`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

`IXmlRepository` 的实现不需要分析通过它们传递的 XML。 它们应视为不透明的 XML 文档，让较高的层担心如何生成和分析文档。

有四种内置的具体类型可实现 `IXmlRepository`：

::: moniker range=">= aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

有关详细信息，请参阅[密钥存储提供程序文档](xref:security/data-protection/implementation/key-storage-providers)。

使用其他后备存储（例如 Azure 表存储）时，注册自定义 `IXmlRepository` 是合适的。

若要更改应用程序范围内的默认存储库，请注册自定义 `IXmlRepository` 实例：

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

## <a name="ixmlencryptor"></a>IXmlEncryptor

`IXmlEncryptor` 接口表示可加密纯文本 XML 元素的类型。 它公开一个 API:

* 加密 (XElement plaintextElement): EncryptedXmlInfo

如果序列化的 `IAuthenticatedEncryptorDescriptor` 包含任何标记为 "需要加密" 的元素，则 `XmlKeyManager` 将通过配置的 `IXmlEncryptor`的 `Encrypt` 方法来运行这些元素，并将到加密元素而不是纯文本元素保存到 `IXmlRepository`。 `Encrypt` 方法的输出是 `EncryptedXmlInfo` 对象。 此对象是包含所生成的到加密 `XElement` 的包装，该类型表示可用于破译相应元素的 `IXmlDecryptor`。

有四种内置的具体类型可实现 `IXmlEncryptor`：

* [CertificateXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [DpapiNGXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [DpapiXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [NullXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

有关详细信息，请参阅[静态密钥加密文档](xref:security/data-protection/implementation/key-encryption-at-rest)。

若要更改应用程序范围内默认的密钥加密机制，请注册自定义 `IXmlEncryptor` 实例：

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

## <a name="ixmldecryptor"></a>IXmlDecryptor

`IXmlDecryptor` 接口表示一种类型，该类型知道如何解密通过 `IXmlEncryptor`到加密的 `XElement`。 它公开一个 API:

* 解密 (XElement encryptedElement): XElement

`Decrypt` 方法撤销 `IXmlEncryptor.Encrypt`所执行的加密。 通常，每个具体 `IXmlEncryptor` 实现都有相应的具体 `IXmlDecryptor` 实现。

实现 `IXmlDecryptor` 的类型应该具有以下两个公共构造函数之一：

* .ctor(IServiceProvider)
* .ctor()

> [!NOTE]
> 传递给构造函数的 `IServiceProvider` 可能为 null。

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

`IKeyEscrowSink` 接口表示可以执行敏感信息的委托的类型。 请记住，序列化描述符可能包含敏感信息（如加密材料），这是第一个位置引入[IXmlEncryptor](#ixmlencryptor)类型的结果。 但是，意外的发生，并且密钥环可以删除或损坏。

托管接口提供了紧急转义影线，允许在任何已配置的[IXmlEncryptor](#ixmlencryptor)转换之前访问原始序列化的 XML。 该接口公开单个 API:

* 应用商店 （Guid keyId、 XElement 元素）

这取决于 `IKeyEscrowSink` 实现，以安全的方式处理所提供的元素，与业务策略一致。 一个可能的实现可能是，托管接收器使用已知的公司 x.509 证书（其中证书的私钥已托管）对 XML 元素进行加密;`CertificateXmlEncryptor` 类型可帮助进行此类处理。 `IKeyEscrowSink` 实现还负责正确保存提供的元素。

默认情况下，不启用任何托管机制，尽管服务器管理员可对[此进行全局配置](xref:security/data-protection/configuration/machine-wide-policy)。 它还可以通过 `IDataProtectionBuilder.AddKeyEscrowSink` 方法以编程方式进行配置，如下面的示例中所示。 `AddKeyEscrowSink` 方法重载会镜像 `IServiceCollection.AddSingleton` 并 `IServiceCollection.AddInstance` 重载，因为 `IKeyEscrowSink` 实例将被单一实例。 如果注册了多个 `IKeyEscrowSink` 实例，则会在密钥生成过程中调用每个实例，因此密钥可同时托管多个机制。

没有用于从 `IKeyEscrowSink` 实例读取材料的 API。 这与托管机制的设计从理论上讲是一致： 它可用于使密钥材料的受信任的颁发机构，并且由于应用程序本身不是受信任的颁发机构，它不应该有权访问其自身托管的材料。

下面的示例代码演示如何创建和注册 `IKeyEscrowSink`，其中密钥是托管的，这样只有 "CONTOSODomain Admins" 的成员才能恢复它们。

> [!NOTE]
> 若要运行此示例，必须是到已加入域的 Windows 8 / Windows Server 2012 计算机和域控制器必须是 Windows Server 2012 或更高版本。

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
