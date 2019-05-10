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
# <a name="key-management-extensibility-in-aspnet-core"></a>ASP.NET Core 的密钥管理可扩展性

> [!TIP]
> 读取[密钥管理](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)然后再阅读本部分中，因为它介绍了这些 Api 的基本概念的某些部分。

> [!WARNING]
> 实现以下接口的任何类型应该是线程安全的多个调用方。

## <a name="key"></a>键

`IKey`接口是加密系统中的键的基本表示形式。 在抽象意义上，"加密密钥材料"字面意义上不在此处使用术语该键。 一个键具有以下属性：

* 激活、 创建和过期日期

* 吊销状态

* 密钥标识符 (GUID)

::: moniker range=">= aspnetcore-2.0"

此外，`IKey`公开`CreateEncryptor`方法用于创建[IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)实例绑定到此密钥。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

此外，`IKey`公开`CreateEncryptorInstance`方法用于创建[IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)实例绑定到此密钥。

::: moniker-end

> [!NOTE]
> 没有 API 来检索从原始的加密材料`IKey`实例。

## <a name="ikeymanager"></a>IKeyManager

`IKeyManager`接口表示负责常规密钥存储、 检索和操作的对象。 它公开三个高级操作：

* 创建新的密钥，并将其保存到存储。

* 从存储中获取所有键。

* 撤消一个或多个密钥并保存到存储吊销信息。

>[!WARNING]
> 编写`IKeyManager`是一个非常高级的任务，和大多数开发人员不应尝试。 相反，大多数开发人员应充分利用所提供的功能[XmlKeyManager](#xmlkeymanager)类。

## <a name="xmlkeymanager"></a>XmlKeyManager

`XmlKeyManager`类型是内置具体实现`IKeyManager`。 它提供了几个有用的功能，包括密钥托管和静态密钥加密。 在此系统中的密钥都表示为 XML 元素 (具体而言， [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview))。

`XmlKeyManager` 取决于在完成其任务的过程中的其他几个组件：

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`这决定了使用新的密钥的算法。

* `IXmlRepository`其中将密钥保存在存储中的控件。

* `IXmlEncryptor` [可选]，它允许加密静态密钥。

* `IKeyEscrowSink` [可选] 提供密钥托管服务。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`其中将密钥保存在存储中的控件。

* `IXmlEncryptor` [可选]，它允许加密静态密钥。

* `IKeyEscrowSink` [可选] 提供密钥托管服务。

::: moniker-end

下面是高级的关系图，指示如何这些组件连接在一起内`XmlKeyManager`。

::: moniker range=">= aspnetcore-2.0"

![创建密钥](key-management/_static/keycreation2.png)

*密钥创建 / CreateNewKey*

在实现中的`CreateNewKey`，则`AlgorithmConfiguration`组件用于创建唯一`IAuthenticatedEncryptorDescriptor`，然后序列化为 XML。 如果存在密钥托管接收器，则原始 （未加密） 的 XML 提供到接收器进行长期存储。 未加密的 XML 然后通过运行`IXmlEncryptor`（如果需要） 来生成加密的 XML 文档。 此加密的文档保存到长期存储通过`IXmlRepository`。 (如果没有`IXmlEncryptor`是配置，未加密的文档保存在`IXmlRepository`。)

![密钥检索](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![创建密钥](key-management/_static/keycreation1.png)

*密钥创建 / CreateNewKey*

在实现中的`CreateNewKey`，则`IAuthenticatedEncryptorConfiguration`组件用于创建唯一`IAuthenticatedEncryptorDescriptor`，然后序列化为 XML。 如果存在密钥托管接收器，则原始 （未加密） 的 XML 提供到接收器进行长期存储。 未加密的 XML 然后通过运行`IXmlEncryptor`（如果需要） 来生成加密的 XML 文档。 此加密的文档保存到长期存储通过`IXmlRepository`。 (如果没有`IXmlEncryptor`是配置，未加密的文档保存在`IXmlRepository`。)

![密钥检索](key-management/_static/keyretrieval1.png)

::: moniker-end

*密钥检索 / GetAllKeys*

在实现中的`GetAllKeys`、 XML 文档表示密钥和吊销读取从基础`IXmlRepository`。 如果这些文档进行加密，系统将自动解密机密。 `XmlKeyManager` 创建适当`IAuthenticatedEncryptorDescriptorDeserializer`实例进行反序列化文档回`IAuthenticatedEncryptorDescriptor`实例，然后将封装在单个`IKey`实例。 此集合的`IKey`实例返回给调用方。

可在特定的 XML 元素的详细信息[密钥存储格式的文档](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format)。

## <a name="ixmlrepository"></a>IXmlRepository

`IXmlRepository`接口表示可以持久保存到 XML 并从后备存储中检索 XML 的类型。 它公开两个 Api:

* `GetAllElements` :`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

实现`IXmlRepository`无需分析通过其传递的 XML。 它们应视为不透明的 XML 文档，让较高的层担心如何生成和分析文档。

有四种内置的具体类型实现`IXmlRepository`:

::: moniker range=">= aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage.AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage.AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

请参阅[密钥存储提供程序文档](xref:security/data-protection/implementation/key-storage-providers)有关详细信息。

注册的自定义`IXmlRepository`适合使用不同的后备存储 （例如，Azure 表存储） 时。

若要更改默认存储库应用程序范围，请注册自定义`IXmlRepository`实例：

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

`IXmlEncryptor`接口表示可以加密纯文本 XML 元素的类型。 它公开一个 API:

* Encrypt(XElement plaintextElement):EncryptedXmlInfo

如果一个序列化`IAuthenticatedEncryptorDescriptor`包含任何元素标记为"需要加密"，然后`XmlKeyManager`将通过已配置运行这些元素`IXmlEncryptor`的`Encrypt`方法，并将保存到加密的元素而不是为纯文本元素`IXmlRepository`。 输出`Encrypt`方法是`EncryptedXmlInfo`对象。 此对象是包含两个结果到加密的包装`XElement`表示的类型和`IXmlDecryptor`这可用于解密的相应元素。

有四种内置的具体类型实现`IXmlEncryptor`:

* [CertificateXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [DpapiNGXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [DpapiXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [NullXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

请参阅[rest 文档在密钥加密](xref:security/data-protection/implementation/key-encryption-at-rest)有关详细信息。

若要更改默认在静态密钥加密机制整个应用程序范围，请注册自定义`IXmlEncryptor`实例：

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

`IXmlDecryptor`接口表示一种类型，知道如何解密`XElement`的已加密通过`IXmlEncryptor`。 它公开一个 API:

* Decrypt(XElement encryptedElement) :XElement

`Decrypt`方法撤消由执行的加密`IXmlEncryptor.Encrypt`。 通常情况下，每个具体`IXmlEncryptor`实现都将具有相应的具体`IXmlDecryptor`实现。

类型实现`IXmlDecryptor`应具有以下两个公共构造函数之一：

* .ctor(IServiceProvider)
* .ctor()

> [!NOTE]
> `IServiceProvider`传递给构造函数可能为 null。

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

`IKeyEscrowSink`接口表示可执行托管的敏感信息的类型。 回想一下，序列化的描述符可能包含敏感信息 （如加密材料），这是什么导致了引入[IXmlEncryptor](#ixmlencryptor)键入第一个位置中。 但是，意外的发生，并且密钥环可以删除或损坏。

托管接口提供了允许访问原始序列化的 XML，通过配置的任何转换前一个紧急应急[IXmlEncryptor](#ixmlencryptor)。 该接口公开单个 API:

* 应用商店 （Guid keyId、 XElement 元素）

这就需要通过`IKeyEscrowSink`实现来处理提供的元素以安全的方式与业务策略保持一致。 一个可能的实现可能是托管接收器使用的是已知的公司 X.509 证书的 XML 元素进行加密的证书的私钥已托管;`CertificateXmlEncryptor`可以帮助解决这个问题类型。 `IKeyEscrowSink`实现程序还负责适当地保留提供的元素。

默认情况下没有托管启用了机制，但服务器管理员可以[全局配置此](xref:security/data-protection/configuration/machine-wide-policy)。 它还可以配置以编程方式通过`IDataProtectionBuilder.AddKeyEscrowSink`方法，如下面的示例中所示。 `AddKeyEscrowSink`方法重载镜像`IServiceCollection.AddSingleton`并`IServiceCollection.AddInstance`重载，为`IKeyEscrowSink`实例都应是单一实例。 如果多个`IKeyEscrowSink`注册实例，每个将调用在密钥生成过程，因此密钥可同时托管多个机制。

没有 API 中读取数据从`IKeyEscrowSink`实例。 这与托管机制的设计从理论上讲是一致： 它可用于使密钥材料的受信任的颁发机构，并且由于应用程序本身不是受信任的颁发机构，它不应该有权访问其自身托管的材料。

下面的示例代码演示如何创建并注册`IKeyEscrowSink`密钥托管，以便只有"CONTOSODomain 管理员"的成员可以恢复它们。

> [!NOTE]
> 若要运行此示例，必须是到已加入域的 Windows 8 / Windows Server 2012 计算机和域控制器必须是 Windows Server 2012 或更高版本。

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
