---
title: 配置 ASP.NET Core 数据保护
author: rick-anderson
description: 了解如何在 ASP.NET Core 中配置数据保护。
ms.author: riande
ms.custom: mvc
ms.date: 04/11/2019
uid: security/data-protection/configuration/overview
ms.openlocfilehash: ee43427fa1e82a365d49df50567b4ca7afb5a5d3
ms.sourcegitcommit: 9b7fcb4ce00a3a32e153a080ebfaae4ef417aafa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/12/2019
ms.locfileid: "59516243"
---
# <a name="configure-aspnet-core-data-protection"></a>配置 ASP.NET Core 数据保护

初始化数据保护系统时，它适用[默认设置](xref:security/data-protection/configuration/default-settings)基于操作环境。 这些设置通常适用于一台计算机上运行的应用。 有一名开发人员可能想要更改默认设置的情况：

* 应用程序分布在多台计算机。
* 出于合规性原因。

对于这些情况下，数据保护系统提供了丰富的配置 API。

> [!WARNING]
> 类似于配置文件，数据保护密钥环应受到保护使用适当的权限。 可以选择加密密钥的静态，但这不会阻止攻击者创建新的密钥。 因此，应用程序的安全将受到影响。 使用数据保护配置的存储位置应具有其访问仅限于应用本身会保护配置文件的方式类似。 例如，如果您选择将密钥环存储在磁盘上，使用文件系统权限。 请确保仅下的标识该 web 应用将在运行具有读取、 写入和创建权限访问此目录。 如果使用 Azure 表存储，只有 web 应用程序应能够读取、 写入或创建新的条目中的表存储，等等。
>
> 扩展方法[AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection)返回[IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder)。 `IDataProtectionBuilder` 显示扩展方法，您可以链接在一起以配置数据保护选项。

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a>ProtectKeysWithAzureKeyVault

若要将密钥存储在[Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)，配置与系统[ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault)中`Startup`类：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

设置密钥环存储位置 (例如， [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage))。 必须设置位置，因为在调用`ProtectKeysWithAzureKeyVault`实现[IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)禁用自动数据保护设置，包括密钥环存储位置。 前面的示例中使用 Azure Blob 存储来持久保存密钥环。 有关详细信息，请参阅[密钥存储提供程序：Azure 和 Redis](xref:security/data-protection/implementation/key-storage-providers#azure-and-redis)。 您还可以保留使用本地密钥环[PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system)。

`keyIdentifier`是用于密钥加密的密钥保管库密钥标识符。 例如，名为 key vault 中创建的密钥`dataprotection`中`contosokeyvault`具有密钥标识符`https://contosokeyvault.vault.azure.net/keys/dataprotection/`。 提供应用程序与**Unwrap Key**并**Wrap Key**对密钥保管库的权限。

`ProtectKeysWithAzureKeyVault` 重载：

* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder，KeyVaultClient，String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_)能够[KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)以启用数据保护系统，以使用密钥保管库。
* [（IDataProtectionBuilder、 字符串、 字符串、 X509Certificate2） ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_)能够`ClientId`和[X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)以启用数据保护系统，以使用密钥保管库。
* [（IDataProtectionBuilder、 字符串、 字符串、 字符串） 的 ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_)能够`ClientId`和`ClientSecret`以启用数据保护系统，以使用密钥保管库。

::: moniker-end

## <a name="persistkeystofilesystem"></a>PersistKeysToFileSystem

若要将密钥存储在 UNC 共享而不是在 *%LOCALAPPDATA%* 的默认位置，配置与系统[PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> 如果更改密钥持久性位置时，系统将不再会自动加密静态情况下，密钥由于它不知道是否 DPAPI 是一种合适的加密机制。

## <a name="protectkeyswith"></a>ProtectKeysWith\*

您可以将系统配置为通过调用的任何保护静态密钥[ProtectKeysWith\* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions)配置 Api。 请考虑下面的示例，它将密钥存储的 UNC 共享上并对这些密钥在使用特定的 X.509 证书的其余部分进行加密：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

在 ASP.NET Core 2.1 或更高版本，可以提供[X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)到[ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)，例如从文件加载证书：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
}
```

::: moniker-end

请参阅[密钥静态加密](xref:security/data-protection/implementation/key-encryption-at-rest)的更多示例和讨论的内置密钥加密机制。

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a>UnprotectKeysWithAnyCertificate

在 ASP.NET Core 2.1 或更高版本，可以轮换证书和解密密钥使用一个数组静止[X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)证书与[UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
        .UnprotectKeysWithAnyCertificate(
            new X509Certificate2("certificate_old_1.pfx", "password_1"),
            new X509Certificate2("certificate_old_2.pfx", "password_2"));
}
```

::: moniker-end

## <a name="setdefaultkeylifetime"></a>SetDefaultKeyLifetime

若要配置系统而不是默认值 90 天使用的密钥生存期为 14 天，请使用[SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a>SetApplicationName

默认情况下，数据保护系统隔离应用中的另一个基于其内容根路径，即使它们共享同一个物理密钥存储库。 这可以防止应用程序了解彼此的受保护的负载。

若要共享受保护的应用之间的有效负载：

* 配置<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>具有相同值的每个应用中。
* 应用程序之间使用相同版本的数据保护 API 堆栈。 执行**任一**应用的项目文件中的以下：
  * 引用通过相同的共享的框架版本[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。
  * 引用相同[数据保护包](xref:security/data-protection/introduction#package-layout)版本。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a>DisableAutomaticKeyGeneration

您可能不希望应用程序以自动滚动更新的密钥 （创建新的密钥），因为它们接近过期的方案。 这一个示例可能是应用设置中的主/辅助关系，其中只有主应用程序负责密钥管理问题，然后辅助应用程序只需将密钥环的只读视图。 可以将辅助应用程序配置为将视为只读密钥环，通过配置具有系统<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a>每个应用程序隔离

数据保护系统提供了 ASP.NET Core 主机，但它自动隔离了从另一个，应用程序，即使这些应用在相同的工作进程帐户下运行，并且使用相同的主密钥材料。 这是某种程度上类似于 IsolateApps 修饰符将来自 System.Web 的`<machineKey>`元素。

隔离机制的工作方式考虑在本地计算机上的每个应用使用作为唯一的租户，因此是<xref:Microsoft.AspNetCore.DataProtection.IDataProtector>取得 root 权限的任何给定的应用会自动包括用作鉴别器的应用程序 ID。 应用程序的唯一 ID 是应用的物理路径：

* 为应用程序中托管[IIS](xref:fundamentals/servers/index#iis-http-server)，唯一的 ID 是应用程序的 IIS 物理路径。 如果在 web 场环境中部署应用，则此值是稳定，假定 web 场中的所有计算机上的 IIS 环境配置方式都类似。
* 对于自承载的应用上运行[Kestrel 服务器](xref:fundamentals/servers/index#kestrel)，唯一的 ID 是磁盘上的应用程序的物理路径。

唯一标识符设计可以经受住重置&mdash;的单个应用程序和计算机本身。

此隔离机制假设应用程序不是恶意。 恶意应用程序始终会影响在相同的工作进程帐户下运行的任何其他应用。 在共享宿主环境中应用是相互不受信任，托管提供商应采取措施来确保应用程序，包括分离应用的基础密钥存储库之间的 OS 级别隔离。

如果由 ASP.NET Core 主机未提供数据保护系统 (例如，如果通过其实例化`DataProtectionProvider`具体类型) 应用程序隔离在默认情况下处于禁用状态。 只要它们提供相应由相同的密钥材料提供支持的所有应用程序时禁用应用程序隔离，可以共享负载[目的](xref:security/data-protection/consumer-apis/purpose-strings)。 若要提供应用隔离在此环境中的，调用[SetApplicationName](#setapplicationname)方法的配置对象，并提供每个应用的唯一名称。

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a>更改算法与 UseCryptographicAlgorithms

数据保护堆栈可以更改使用新生成的键的默认算法。 若要执行此操作的最简单方法是调用[UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms)从配置回调：

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

默认的 EncryptionAlgorithm AES-256-CBC，且默认 ValidationAlgorithm 是 HMACSHA256。 可以由系统管理员通过设置默认策略[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)，但显式调用`UseCryptographicAlgorithms`重写默认策略。

调用`UseCryptographicAlgorithms`，可指定预定义的内置列表中的所需的算法。 无需担心有关算法的实现。 在上述方案中，数据保护系统尝试使用 AES 的 CNG 实现，如果在 Windows 上运行。 否则，它将回退到托管[System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes)类。

您可以手动指定通过调用实现[UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms)。

> [!TIP]
> 更改算法不会影响密钥环中的现有密钥。 它只影响新生成的键。

### <a name="specifying-custom-managed-algorithms"></a>指定托管的自定义算法

::: moniker range=">= aspnetcore-2.0"

若要指定托管的自定义算法，创建[ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration)指向实现类型的实例：

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptorConfiguration()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

若要指定托管的自定义算法，创建[ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings)指向实现类型的实例：

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptionSettings()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

通常\*类型属性必须指向具体，可实例化 （通过公共的无参数构造函数） 的实现[SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm)并[KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm)，但系统特殊的情况下等某些值`typeof(Aes)`为方便起见。

> [!NOTE]
> SymmetricAlgorithm 必须 ≥ 128 位的密钥长度和 ≥ 64 位为单位的块大小，并且它必须支持使用 PKCS #7 填充 CBC 模式加密。 KeyedHashAlgorithm 必须具有的摘要的大小 > = 128 位，并且它必须支持密钥长度等于哈希算法的摘要的长度。 KeyedHashAlgorithm 并非是严格要求，为 HMAC。

### <a name="specifying-custom-windows-cng-algorithms"></a>指定自定义 Windows CNG 算法

::: moniker range=">= aspnetcore-2.0"

若要指定自定义 Windows CNG 算法的 HMAC 验证使用 CBC 模式加密，创建[CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration)实例，它包含的算法的信息：

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

若要指定自定义 Windows CNG 算法的 HMAC 验证使用 CBC 模式加密，创建[CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings)实例，它包含的算法的信息：

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

> [!NOTE]
> 对称块加密算法的密钥长度必须 > = 128 位的块大小 > = 64 位，并且它必须支持使用 PKCS #7 填充 CBC 模式加密。 哈希算法必须具有的摘要的大小 > = 128 位并且必须支持正在打开与 BCRYPT\_ALG\_处理\_HMAC\_标志的标志。 \*提供程序属性可以设置为 null 以使用指定的算法的默认提供程序。 请参阅[BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx)文档了解详细信息。

::: moniker range=">= aspnetcore-2.0"

若要指定自定义 Windows CNG 算法使用 Galois/计数器模式加密进行验证，创建[CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration)实例，它包含的算法的信息：

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

若要指定自定义 Windows CNG 算法使用 Galois/计数器模式加密进行验证，创建[CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings)实例，它包含的算法的信息：

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

> [!NOTE]
> 对称块加密算法的密钥长度必须 > = 128 位的块大小为完全 128 位，并且它必须支持 GCM 加密。 可以设置[EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider)属性设置为 null 以使用指定的算法的默认提供程序。 请参阅[BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx)文档了解详细信息。

### <a name="specifying-other-custom-algorithms"></a>指定其他自定义算法

尽管不公开为第一类 API，数据保护系统是算法的可扩展，足以允许指定几乎任何类型。 例如，就可以使包含硬件安全模块 (HSM) 中的所有密钥并提供加密和解密例程的核心的自定义实现。 请参阅[IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor)中[核心加密扩展性](xref:security/data-protection/extensibility/core-crypto)有关详细信息。

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a>保存密钥托管在 Docker 容器中时

在中承载时[Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/)容器，应在维护密钥：

* 是一个 Docker 卷仍然存在超出容器的生存期，如共享的卷或主机装入的卷，一个文件夹。
* 外部提供程序，如[Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)或[Redis](https://redis.io/)。

## <a name="additional-resources"></a>其他资源

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
