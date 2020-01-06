---
title: 配置 ASP.NET Core数据保护
author: rick-anderson
description: 了解如何在 ASP.NET Core 中配置数据保护。
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: security/data-protection/configuration/overview
ms.openlocfilehash: cda510d0f8211641e3544b53ded79878d717cc58
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358405"
---
# <a name="configure-aspnet-core-data-protection"></a>配置 ASP.NET Core数据保护

当数据保护系统已初始化时，它将基于操作环境应用[默认设置](xref:security/data-protection/configuration/default-settings)。 这些设置通常适用于在一台计算机上运行的应用程序。 在某些情况下，开发人员可能想要更改默认设置：

* 应用程序分布在多台计算机上。
* 出于合规性原因。

在这些情况下，数据保护系统提供了丰富的配置 API。

> [!WARNING]
> 与配置文件类似，应使用适当的权限保护数据保护密钥环。 你可以选择对静态密钥加密，但这不会阻止攻击者创建新密钥。 因此，应用的安全将受到影响。 使用数据保护配置的存储位置应该将其访问权限限制为应用本身，这与保护配置文件的方式类似。 例如，如果选择将密钥环存储在磁盘上，请使用文件系统权限。 确保你的 web 应用在其下运行的标识具有对该目录的读取、写入和创建访问权限。 如果使用 Azure Blob 存储，则只有 web 应用应能够在 Blob 存储中读取、写入或创建新条目等。
>
> 扩展方法[AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection)返回[IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder)。 `IDataProtectionBuilder` 公开扩展方法，你可以将这些方法链接在一起来配置数据保护选项。

::: moniker range=">= aspnetcore-3.0"

本文中使用的数据保护扩展插件需要以下 NuGet 包：

* [AspNetCore. DataProtection. AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/)
* [AspNetCore. DataProtection. AzureKeyVault](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault/)

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a>ProtectKeysWithAzureKeyVault

若要在[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)中存储密钥，请在 `Startup` 类中配置[ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault)的系统：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

设置密钥环存储位置（例如， [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)）。 必须设置位置，因为调用 `ProtectKeysWithAzureKeyVault` 实现了禁用自动数据保护设置的[IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) ，包括密钥环存储位置。 前面的示例使用 Azure Blob 存储来持久保存密钥环。 有关详细信息，请参阅[密钥存储提供程序： Azure 存储](xref:security/data-protection/implementation/key-storage-providers#azure-storage)。 还可以通过[PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system)将密钥环保存到本地。

`keyIdentifier` 是用于密钥加密的密钥保管库密钥标识符。 例如，在 `contosokeyvault` 中名为 `dataprotection` 的密钥保管库中创建的密钥具有密钥标识符 `https://contosokeyvault.vault.azure.net/keys/dataprotection/`。 向应用提供对密钥保管库的**解包密钥**和**包装密钥**权限。

`ProtectKeysWithAzureKeyVault` 重载：

* [ProtectKeysWithAzureKeyVault （IDataProtectionBuilder，KeyVaultClient，String）](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_)允许使用[KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)使数据保护系统能够使用密钥保管库。
* [ProtectKeysWithAzureKeyVault （IDataProtectionBuilder，string，string，X509Certificate2）](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_)允许使用 `ClientId` 和[X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)使数据保护系统能够使用密钥保管库。
* [ProtectKeysWithAzureKeyVault （IDataProtectionBuilder，string，string，string）](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_)允许使用 `ClientId` 和 `ClientSecret` 使数据保护系统能够使用密钥保管库。

::: moniker-end

## <a name="persistkeystofilesystem"></a>PersistKeysToFileSystem

若要将密钥存储在 UNC 共享上，而不是存储在 *% LOCALAPPDATA%* 默认位置，请使用[PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)配置系统：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> 如果更改密钥持久性位置，系统将不再自动加密静态密钥，因为它不知道 DPAPI 是否为适当的加密机制。

## <a name="protectkeyswith"></a>ProtectKeysWith\*

可以通过调用[ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions)配置 api，将系统配置为保护静态密钥。 请考虑以下示例，该示例将密钥存储在 UNC 共享上，并使用特定的 x.509 证书对静态密钥进行加密：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

在 ASP.NET Core 2.1 或更高版本中，你可以提供[ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)的[X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) ，例如从文件中加载的证书：

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

有关内置密钥加密机制的更多示例和讨论，请参阅[静态密钥加密](xref:security/data-protection/implementation/key-encryption-at-rest)。

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a>UnprotectKeysWithAnyCertificate

在 ASP.NET Core 2.1 或更高版本中，你可以使用[UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate)的[X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)

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

若要将系统配置为使用14天的密钥生存期而不是默认的90天，请使用[SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime)：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a>SetApplicationName

默认情况下，数据保护系统基于其[内容根](xref:fundamentals/index#content-root)路径将应用彼此隔离，即使它们共享相同的物理密钥存储库。 这可防止应用了解彼此的受保护负载。

在应用之间共享受保护的负载：

* 用相同的值在每个应用中配置 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>。
* 在应用程序中使用相同版本的数据保护 API 堆栈。 在应用的项目文件中执行以下**任一**操作：
  * 通过[AspNetCore 元包](xref:fundamentals/metapackage-app)引用相同的共享框架版本。
  * 引用相同的[数据保护包](xref:security/data-protection/introduction#package-layout)版本。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a>DisableAutomaticKeyGeneration

您可能会遇到这样的情况：不希望应用程序在接近过期时自动滚动更新密钥（创建新的密钥）。 这种情况的一个示例可能是在主/辅助关系中设置的应用，其中只有主应用负责密钥管理问题，辅助应用只是具有密钥环的只读视图。 可以通过使用 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>配置系统，将辅助应用配置为将密钥环视为只读：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a>每应用程序隔离

数据保护系统提供了 ASP.NET Core 主机，但它自动隔离了从另一个，应用程序，即使这些应用在相同的工作进程帐户下运行，并且使用相同的主密钥材料。 这有点类似于 IsolateApps 的 `<machineKey>` 元素中的修饰符。

隔离机制的工作原理是将本地计算机上的每个应用视为唯一的租户，因此，任何给定应用的根 <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> 会自动将应用 ID 包含为鉴别器。 应用的唯一 ID 是应用的物理路径：

* 对于在 IIS 中托管的应用，唯一 ID 是应用的 IIS 物理路径。 如果在 web 场环境中部署了应用，则此值是稳定的，假定在 web 场中的所有计算机上配置了类似的 IIS 环境。
* 对于在[Kestrel 服务器](xref:fundamentals/servers/index#kestrel)上运行的自承载应用程序，唯一 ID 是指向磁盘上的应用程序的物理路径。

唯一标识符的设计目的是在每个应用程序和计算机本身&mdash;重置。

此隔离机制假定应用不是恶意的。 恶意应用始终会影响在同一工作进程帐户下运行的任何其他应用。 在应用不受信任的共享主机环境中，托管提供商应采取措施来确保应用之间的操作系统级隔离，包括分离应用程序的底层密钥存储库。

如果由 ASP.NET Core 主机未提供数据保护系统 (例如，如果通过其实例化`DataProtectionProvider`具体类型) 应用程序隔离在默认情况下处于禁用状态。 禁用应用隔离后，只要提供相应的[用途](xref:security/data-protection/consumer-apis/purpose-strings)，同一密钥材料支持的所有应用就可以共享有效负载。 若要在此环境中提供应用隔离，请对配置对象调用[SetApplicationName](#setapplicationname)方法，并为每个应用提供唯一的名称。

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a>用 UseCryptographicAlgorithms 更改算法

数据保护堆栈允许您更改新生成的密钥使用的默认算法。 执行此操作的最简单方法是从配置回调调用[UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) ：

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

默认 EncryptionAlgorithm 为 AES-256-CBC，默认 ValidationAlgorithm 为 HMACSHA256。 系统管理员可以通过[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)设置默认策略，但对 `UseCryptographicAlgorithms` 的显式调用会替代默认策略。

通过调用 `UseCryptographicAlgorithms`，可以从预定义的内置列表中指定所需的算法。 您无需担心算法的实现。 在上述方案中，如果在 Windows 上运行，数据保护系统将尝试使用 AES 的 CNG 实现。 否则，它会回退到托管的[系统。](/dotnet/api/system.security.cryptography.aes)

可以通过调用[UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms)手动指定实现。

> [!TIP]
> 更改算法不会影响密钥环中的现有密钥。 它仅影响新生成的键。

### <a name="specifying-custom-managed-algorithms"></a>指定自定义托管算法

::: moniker range=">= aspnetcore-2.0"

若要指定自定义托管算法，请创建一个指向实现类型的[ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration)实例：

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

若要指定自定义托管算法，请创建一个指向实现类型的[ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings)实例：

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

通常，\*类型属性必须指向[system.security.cryptography.symmetricalgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm)和[KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm)的具体实例实例（通过公共无参数的 ctor）实现，尽管系统特别适用于一些值（如 `typeof(Aes)`）以方便使用。

> [!NOTE]
> System.security.cryptography.symmetricalgorithm 必须具有≥128位的密钥长度和≥64位的块大小，并且必须支持 PKCS #7 填充的 CBC 模式加密。 KeyedHashAlgorithm 的摘要大小必须为 > = 128 位，并且它必须支持长度等于哈希算法摘要长度的键。 KeyedHashAlgorithm 不一定是 HMAC。

### <a name="specifying-custom-windows-cng-algorithms"></a>指定自定义 Windows CNG 算法

::: moniker range=">= aspnetcore-2.0"

若要通过 HMAC 验证使用 CBC 模式加密指定自定义 Windows CNG 算法，请创建包含算法信息的[CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration)实例：

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

若要通过 HMAC 验证使用 CBC 模式加密指定自定义 Windows CNG 算法，请创建包含算法信息的[CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings)实例：

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
> 对称块加密算法的密钥长度必须为 > = 128 位，块大小为 > = 64 位，并且它必须支持 PKCS #7 填充的 CBC 模式加密。 哈希算法的摘要大小必须为 > = 128 位，并且必须支持使用 BCRYPT\_ALG\_处理\_HMAC\_标志标志打开。 \*提供程序属性可以设置为 null，以将默认提供程序用于指定的算法。 有关详细信息，请参阅[BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx)文档。

::: moniker range=">= aspnetcore-2.0"

若要使用 Galois/Counter 模式加密和验证来指定自定义 Windows CNG 算法，请创建包含算法信息的[CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration)实例：

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

若要使用 Galois/Counter 模式加密和验证来指定自定义 Windows CNG 算法，请创建包含算法信息的[CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings)实例：

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
> 对称块密码算法的密钥长度必须为 > = 128 位，块大小正好为128位，并且必须支持 GCM 加密。 可以将[EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider)属性设置为 null，以将默认提供程序用于指定的算法。 有关详细信息，请参阅[BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx)文档。

### <a name="specifying-other-custom-algorithms"></a>指定其他自定义算法

尽管不是作为第一类 API 公开的，但数据保护系统的扩展能力足以允许指定几乎任何类型的算法。 例如，可以保留硬件安全模块（HSM）中包含的所有密钥，并提供核心加密和解密例程的自定义实现。 有关详细信息，请参阅[核心加密扩展性](xref:security/data-protection/extensibility/core-crypto)中的[IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) 。

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a>在 Docker 容器中托管时保持密钥

在[Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/)容器中托管时，应在以下任一项中维护密钥：

* 一个文件夹，它是在容器的生存期之外保留的 Docker 卷，如共享卷或主机装入的卷。
* 外部提供程序，如[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)或[Redis](https://redis.io/)。

## <a name="persisting-keys-with-redis"></a>通过 Redis 保持密钥

只应使用支持[Redis 数据暂留](/azure/azure-cache-for-redis/cache-how-to-premium-persistence)的 Redis 版本来存储密钥。 [Azure Blob 存储](/azure/storage/blobs/storage-blobs-introduction)是持久性的，可用于存储密钥。 有关详细信息，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore/issues/13476)。

## <a name="additional-resources"></a>其他资源

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
