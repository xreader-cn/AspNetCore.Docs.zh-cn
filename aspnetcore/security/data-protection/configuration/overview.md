---
title: 配置 ASP.NET Core 数据保护
author: rick-anderson
description: 了解如何在 ASP.NET Core 中配置数据保护。
ms.author: riande
ms.custom: mvc
ms.date: 03/08/2019
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 36a06246513215ec29891df02688d113db11f914
ms.sourcegitcommit: 32bc00435767189fa3ae5fb8a91a307bf889de9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2019
ms.locfileid: "57733492"
---
# <a name="configure-aspnet-core-data-protection"></a><span data-ttu-id="aaa17-103">配置 ASP.NET Core 数据保护</span><span class="sxs-lookup"><span data-stu-id="aaa17-103">Configure ASP.NET Core Data Protection</span></span>

<span data-ttu-id="aaa17-104">初始化数据保护系统时，它适用[默认设置](xref:security/data-protection/configuration/default-settings)基于操作环境。</span><span class="sxs-lookup"><span data-stu-id="aaa17-104">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) based on the operational environment.</span></span> <span data-ttu-id="aaa17-105">这些设置通常适用于一台计算机上运行的应用。</span><span class="sxs-lookup"><span data-stu-id="aaa17-105">These settings are generally appropriate for apps running on a single machine.</span></span> <span data-ttu-id="aaa17-106">有一名开发人员可能想要更改默认设置的情况：</span><span class="sxs-lookup"><span data-stu-id="aaa17-106">There are cases where a developer may want to change the default settings:</span></span>

* <span data-ttu-id="aaa17-107">应用程序分布在多台计算机。</span><span class="sxs-lookup"><span data-stu-id="aaa17-107">The app is spread across multiple machines.</span></span>
* <span data-ttu-id="aaa17-108">出于合规性原因。</span><span class="sxs-lookup"><span data-stu-id="aaa17-108">For compliance reasons.</span></span>

<span data-ttu-id="aaa17-109">对于这些情况下，数据保护系统提供了丰富的配置 API。</span><span class="sxs-lookup"><span data-stu-id="aaa17-109">For these scenarios, the Data Protection system offers a rich configuration API.</span></span>

> [!WARNING]
> <span data-ttu-id="aaa17-110">类似于配置文件，数据保护密钥环应受到保护使用适当的权限。</span><span class="sxs-lookup"><span data-stu-id="aaa17-110">Similar to configuration files, the data protection key ring should be protected using appropriate permissions.</span></span> <span data-ttu-id="aaa17-111">可以选择加密密钥的静态，但这不会阻止攻击者创建新的密钥。</span><span class="sxs-lookup"><span data-stu-id="aaa17-111">You can choose to encrypt keys at rest, but this doesn't prevent attackers from creating new keys.</span></span> <span data-ttu-id="aaa17-112">因此，应用程序的安全将受到影响。</span><span class="sxs-lookup"><span data-stu-id="aaa17-112">Consequently, your app's security is impacted.</span></span> <span data-ttu-id="aaa17-113">使用数据保护配置的存储位置应具有其访问仅限于应用本身会保护配置文件的方式类似。</span><span class="sxs-lookup"><span data-stu-id="aaa17-113">The storage location configured with Data Protection should have its access limited to the app itself, similar to the way you would protect configuration files.</span></span> <span data-ttu-id="aaa17-114">例如，如果您选择将密钥环存储在磁盘上，使用文件系统权限。</span><span class="sxs-lookup"><span data-stu-id="aaa17-114">For example, if you choose to store your key ring on disk, use file system permissions.</span></span> <span data-ttu-id="aaa17-115">请确保仅下的标识该 web 应用将在运行具有读取、 写入和创建权限访问此目录。</span><span class="sxs-lookup"><span data-stu-id="aaa17-115">Ensure only the identity under which your web app runs has read, write, and create access to that directory.</span></span> <span data-ttu-id="aaa17-116">如果使用 Azure 表存储，只有 web 应用程序应能够读取、 写入或创建新的条目中的表存储，等等。</span><span class="sxs-lookup"><span data-stu-id="aaa17-116">If you use Azure Table Storage, only the web app should have the ability to read, write, or create new entries in the table store, etc.</span></span>
>
> <span data-ttu-id="aaa17-117">扩展方法[AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection)返回[IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder)。</span><span class="sxs-lookup"><span data-stu-id="aaa17-117">The extension method [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) returns an [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span></span> <span data-ttu-id="aaa17-118">`IDataProtectionBuilder` 显示扩展方法，您可以链接在一起以配置数据保护选项。</span><span class="sxs-lookup"><span data-stu-id="aaa17-118">`IDataProtectionBuilder` exposes extension methods that you can chain together to configure Data Protection options.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a><span data-ttu-id="aaa17-119">ProtectKeysWithAzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="aaa17-119">ProtectKeysWithAzureKeyVault</span></span>

<span data-ttu-id="aaa17-120">若要将密钥存储在[Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)，配置与系统[ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault)中`Startup`类：</span><span class="sxs-lookup"><span data-stu-id="aaa17-120">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="aaa17-121">设置密钥环存储位置 (例如， [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage))。</span><span class="sxs-lookup"><span data-stu-id="aaa17-121">Set the key ring storage location (for example, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span></span> <span data-ttu-id="aaa17-122">必须设置位置，因为在调用`ProtectKeysWithAzureKeyVault`实现[IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)禁用自动数据保护设置，包括密钥环存储位置。</span><span class="sxs-lookup"><span data-stu-id="aaa17-122">The location must be set because calling `ProtectKeysWithAzureKeyVault` implements an [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) that disables automatic data protection settings, including the key ring storage location.</span></span> <span data-ttu-id="aaa17-123">前面的示例中使用 Azure Blob 存储来持久保存密钥环。</span><span class="sxs-lookup"><span data-stu-id="aaa17-123">The preceding example uses Azure Blob Storage to persist the key ring.</span></span> <span data-ttu-id="aaa17-124">有关详细信息，请参阅[密钥存储提供程序：Azure 和 Redis](xref:security/data-protection/implementation/key-storage-providers#azure-and-redis)。</span><span class="sxs-lookup"><span data-stu-id="aaa17-124">For more information, see [Key storage providers: Azure and Redis](xref:security/data-protection/implementation/key-storage-providers#azure-and-redis).</span></span> <span data-ttu-id="aaa17-125">您还可以保留使用本地密钥环[PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system)。</span><span class="sxs-lookup"><span data-stu-id="aaa17-125">You can also persist the key ring locally with [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span></span>

<span data-ttu-id="aaa17-126">`keyIdentifier`是用于密钥加密的密钥保管库密钥标识符 (例如， `https://contosokeyvault.vault.azure.net/keys/dataprotection/`)。</span><span class="sxs-lookup"><span data-stu-id="aaa17-126">The `keyIdentifier` is the key vault key identifier used for key encryption (for example, `https://contosokeyvault.vault.azure.net/keys/dataprotection/`).</span></span>

<span data-ttu-id="aaa17-127">`ProtectKeysWithAzureKeyVault` 重载：</span><span class="sxs-lookup"><span data-stu-id="aaa17-127">`ProtectKeysWithAzureKeyVault` overloads:</span></span>

* <span data-ttu-id="aaa17-128">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder，KeyVaultClient，String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_)能够[KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)以启用数据保护系统，以使用密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="aaa17-128">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) permits the use of a [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="aaa17-129">[（IDataProtectionBuilder、 字符串、 字符串、 X509Certificate2） ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_)能够`ClientId`和[X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)以启用数据保护系统，以使用密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="aaa17-129">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) permits the use of a `ClientId` and [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="aaa17-130">[（IDataProtectionBuilder、 字符串、 字符串、 字符串） 的 ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_)能够`ClientId`和`ClientSecret`以启用数据保护系统，以使用密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="aaa17-130">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) permits the use of a `ClientId` and `ClientSecret` to enable the data protection system to use the key vault.</span></span>

::: moniker-end

## <a name="persistkeystofilesystem"></a><span data-ttu-id="aaa17-131">PersistKeysToFileSystem</span><span class="sxs-lookup"><span data-stu-id="aaa17-131">PersistKeysToFileSystem</span></span>

<span data-ttu-id="aaa17-132">若要将密钥存储在 UNC 共享而不是在 *%LOCALAPPDATA%* 的默认位置，配置与系统[PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span><span class="sxs-lookup"><span data-stu-id="aaa17-132">To store keys on a UNC share instead of at the *%LOCALAPPDATA%* default location, configure the system with [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> <span data-ttu-id="aaa17-133">如果更改密钥持久性位置时，系统将不再会自动加密静态情况下，密钥由于它不知道是否 DPAPI 是一种合适的加密机制。</span><span class="sxs-lookup"><span data-stu-id="aaa17-133">If you change the key persistence location, the system no longer automatically encrypts keys at rest, since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

## <a name="protectkeyswith"></a><span data-ttu-id="aaa17-134">ProtectKeysWith\*</span><span class="sxs-lookup"><span data-stu-id="aaa17-134">ProtectKeysWith\*</span></span>

<span data-ttu-id="aaa17-135">您可以将系统配置为通过调用的任何保护静态密钥[ProtectKeysWith\* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions)配置 Api。</span><span class="sxs-lookup"><span data-stu-id="aaa17-135">You can configure the system to protect keys at rest by calling any of the [ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) configuration APIs.</span></span> <span data-ttu-id="aaa17-136">请考虑下面的示例，它将密钥存储的 UNC 共享上并对这些密钥在使用特定的 X.509 证书的其余部分进行加密：</span><span class="sxs-lookup"><span data-stu-id="aaa17-136">Consider the example below, which stores keys on a UNC share and encrypts those keys at rest with a specific X.509 certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="aaa17-137">在 ASP.NET Core 2.1 或更高版本，可以提供[X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)到[ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)，例如从文件加载证书：</span><span class="sxs-lookup"><span data-stu-id="aaa17-137">In ASP.NET Core 2.1 or later, you can provide an [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), such as a certificate loaded from a file:</span></span>

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

<span data-ttu-id="aaa17-138">请参阅[密钥静态加密](xref:security/data-protection/implementation/key-encryption-at-rest)的更多示例和讨论的内置密钥加密机制。</span><span class="sxs-lookup"><span data-stu-id="aaa17-138">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more examples and discussion on the built-in key encryption mechanisms.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a><span data-ttu-id="aaa17-139">UnprotectKeysWithAnyCertificate</span><span class="sxs-lookup"><span data-stu-id="aaa17-139">UnprotectKeysWithAnyCertificate</span></span>

<span data-ttu-id="aaa17-140">在 ASP.NET Core 2.1 或更高版本，可以轮换证书和解密密钥使用一个数组静止[X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)证书与[UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span><span class="sxs-lookup"><span data-stu-id="aaa17-140">In ASP.NET Core 2.1 or later, you can rotate certificates and decrypt keys at rest using an array of [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certificates with [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span></span>

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

## <a name="setdefaultkeylifetime"></a><span data-ttu-id="aaa17-141">SetDefaultKeyLifetime</span><span class="sxs-lookup"><span data-stu-id="aaa17-141">SetDefaultKeyLifetime</span></span>

<span data-ttu-id="aaa17-142">若要配置系统而不是默认值 90 天使用的密钥生存期为 14 天，请使用[SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span><span class="sxs-lookup"><span data-stu-id="aaa17-142">To configure the system to use a key lifetime of 14 days instead of the default 90 days, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a><span data-ttu-id="aaa17-143">SetApplicationName</span><span class="sxs-lookup"><span data-stu-id="aaa17-143">SetApplicationName</span></span>

<span data-ttu-id="aaa17-144">默认情况下，数据保护系统隔离应用中的另一个基于其内容根路径，即使它们共享同一个物理密钥存储库。</span><span class="sxs-lookup"><span data-stu-id="aaa17-144">By default, the Data Protection system isolates apps from one another based on their content root paths, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="aaa17-145">这可以防止应用程序了解彼此的受保护的负载。</span><span class="sxs-lookup"><span data-stu-id="aaa17-145">This prevents the apps from understanding each other's protected payloads.</span></span>

<span data-ttu-id="aaa17-146">若要共享受保护的应用之间的有效负载：</span><span class="sxs-lookup"><span data-stu-id="aaa17-146">To share protected payloads among apps:</span></span>

* <span data-ttu-id="aaa17-147">配置<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>具有相同值的每个应用中。</span><span class="sxs-lookup"><span data-stu-id="aaa17-147">Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in each app with the same value.</span></span>
* <span data-ttu-id="aaa17-148">应用程序之间使用相同版本的数据保护 API 堆栈。</span><span class="sxs-lookup"><span data-stu-id="aaa17-148">Use the same version of the Data Protection API stack across the apps.</span></span> <span data-ttu-id="aaa17-149">执行**任一**应用的项目文件中的以下：</span><span class="sxs-lookup"><span data-stu-id="aaa17-149">Perform **either** of the following in the apps' project files:</span></span>
  * <span data-ttu-id="aaa17-150">引用通过相同的共享的框架版本[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="aaa17-150">Reference the same shared framework version via the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="aaa17-151">引用相同[数据保护包](xref:security/data-protection/introduction#package-layout)版本。</span><span class="sxs-lookup"><span data-stu-id="aaa17-151">Reference the same [Data Protection package](xref:security/data-protection/introduction#package-layout) version.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a><span data-ttu-id="aaa17-152">DisableAutomaticKeyGeneration</span><span class="sxs-lookup"><span data-stu-id="aaa17-152">DisableAutomaticKeyGeneration</span></span>

<span data-ttu-id="aaa17-153">您可能不希望应用程序以自动滚动更新的密钥 （创建新的密钥），因为它们接近过期的方案。</span><span class="sxs-lookup"><span data-stu-id="aaa17-153">You may have a scenario where you don't want an app to automatically roll keys (create new keys) as they approach expiration.</span></span> <span data-ttu-id="aaa17-154">这一个示例可能是应用设置中的主/辅助关系，其中只有主应用程序负责密钥管理问题，然后辅助应用程序只需将密钥环的只读视图。</span><span class="sxs-lookup"><span data-stu-id="aaa17-154">One example of this might be apps set up in a primary/secondary relationship, where only the primary app is responsible for key management concerns and secondary apps simply have a read-only view of the key ring.</span></span> <span data-ttu-id="aaa17-155">可以将辅助应用程序配置为将视为只读密钥环，通过配置系统[DisableAutomaticKeyGeneration](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.disableautomatickeygeneration):</span><span class="sxs-lookup"><span data-stu-id="aaa17-155">The secondary apps can be configured to treat the key ring as read-only by configuring the system with [DisableAutomaticKeyGeneration](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.disableautomatickeygeneration):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a><span data-ttu-id="aaa17-156">每个应用程序隔离</span><span class="sxs-lookup"><span data-stu-id="aaa17-156">Per-application isolation</span></span>

<span data-ttu-id="aaa17-157">数据保护系统提供了 ASP.NET Core 主机，但它自动隔离了从另一个，应用程序，即使这些应用在相同的工作进程帐户下运行，并且使用相同的主密钥材料。</span><span class="sxs-lookup"><span data-stu-id="aaa17-157">When the Data Protection system is provided by an ASP.NET Core host, it automatically isolates apps from one another, even if those apps are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="aaa17-158">这是某种程度上类似于 IsolateApps 修饰符将来自 System.Web 的 **\<machineKey >** 元素。</span><span class="sxs-lookup"><span data-stu-id="aaa17-158">This is somewhat similar to the IsolateApps modifier from System.Web's **\<machineKey>** element.</span></span>

<span data-ttu-id="aaa17-159">隔离机制的工作原理的考虑在本地计算机上的每个应用使用作为唯一的租户，因此[IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector)取得 root 权限的任何给定的应用会自动包括用作鉴别器的应用程序 ID。</span><span class="sxs-lookup"><span data-stu-id="aaa17-159">The isolation mechanism works by considering each app on the local machine as a unique tenant, thus the [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) rooted for any given app automatically includes the app ID as a discriminator.</span></span> <span data-ttu-id="aaa17-160">应用程序的唯一 ID 来自两个位置之一：</span><span class="sxs-lookup"><span data-stu-id="aaa17-160">The app's unique ID comes from one of two places:</span></span>

1. <span data-ttu-id="aaa17-161">如果应用托管在 IIS 中，唯一的标识符是应用程序的配置路径。</span><span class="sxs-lookup"><span data-stu-id="aaa17-161">If the app is hosted in IIS, the unique identifier is the app's configuration path.</span></span> <span data-ttu-id="aaa17-162">如果在 web 场环境中部署应用，此值应为稳定，假定 web 场中的所有计算机上的 IIS 环境配置方式都类似。</span><span class="sxs-lookup"><span data-stu-id="aaa17-162">If an app is deployed in a web farm environment, this value should be stable assuming that the IIS environments are configured similarly across all machines in the web farm.</span></span>

2. <span data-ttu-id="aaa17-163">如果应用程序不承载于 IIS 中，唯一的标识符是应用程序的物理路径。</span><span class="sxs-lookup"><span data-stu-id="aaa17-163">If the app isn't hosted in IIS, the unique identifier is the physical path of the app.</span></span>

<span data-ttu-id="aaa17-164">唯一标识符设计可以经受住重置&mdash;的单个应用程序和计算机本身。</span><span class="sxs-lookup"><span data-stu-id="aaa17-164">The unique identifier is designed to survive resets &mdash; both of the individual app and of the machine itself.</span></span>

<span data-ttu-id="aaa17-165">此隔离机制假设应用程序不是恶意。</span><span class="sxs-lookup"><span data-stu-id="aaa17-165">This isolation mechanism assumes that the apps are not malicious.</span></span> <span data-ttu-id="aaa17-166">恶意应用程序始终会影响在相同的工作进程帐户下运行的任何其他应用。</span><span class="sxs-lookup"><span data-stu-id="aaa17-166">A malicious app can always impact any other app running under the same worker process account.</span></span> <span data-ttu-id="aaa17-167">在共享宿主环境中应用是相互不受信任，托管提供商应采取措施来确保应用程序，包括分离应用的基础密钥存储库之间的 OS 级别隔离。</span><span class="sxs-lookup"><span data-stu-id="aaa17-167">In a shared hosting environment where apps are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between apps, including separating the apps' underlying key repositories.</span></span>

<span data-ttu-id="aaa17-168">如果由 ASP.NET Core 主机未提供数据保护系统 (例如，如果通过其实例化`DataProtectionProvider`具体类型) 应用程序隔离在默认情况下处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="aaa17-168">If the Data Protection system isn't provided by an ASP.NET Core host (for example, if you instantiate it via the `DataProtectionProvider` concrete type) app isolation is disabled by default.</span></span> <span data-ttu-id="aaa17-169">只要它们提供相应由相同的密钥材料提供支持的所有应用程序时禁用应用程序隔离，可以共享负载[目的](xref:security/data-protection/consumer-apis/purpose-strings)。</span><span class="sxs-lookup"><span data-stu-id="aaa17-169">When app isolation is disabled, all apps backed by the same keying material can share payloads as long as they provide the appropriate [purposes](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="aaa17-170">若要提供应用隔离在此环境中的，调用[SetApplicationName](#setapplicationname)方法的配置对象，并提供每个应用的唯一名称。</span><span class="sxs-lookup"><span data-stu-id="aaa17-170">To provide app isolation in this environment, call the [SetApplicationName](#setapplicationname) method on the configuration object and provide a unique name for each app.</span></span>

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a><span data-ttu-id="aaa17-171">更改算法与 UseCryptographicAlgorithms</span><span class="sxs-lookup"><span data-stu-id="aaa17-171">Changing algorithms with UseCryptographicAlgorithms</span></span>

<span data-ttu-id="aaa17-172">数据保护堆栈可以更改使用新生成的键的默认算法。</span><span class="sxs-lookup"><span data-stu-id="aaa17-172">The Data Protection stack allows you to change the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="aaa17-173">若要执行此操作的最简单方法是调用[UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms)从配置回调：</span><span class="sxs-lookup"><span data-stu-id="aaa17-173">The simplest way to do this is to call [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) from the configuration callback:</span></span>

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

<span data-ttu-id="aaa17-174">默认的 EncryptionAlgorithm AES-256-CBC，且默认 ValidationAlgorithm 是 HMACSHA256。</span><span class="sxs-lookup"><span data-stu-id="aaa17-174">The default EncryptionAlgorithm is AES-256-CBC, and the default ValidationAlgorithm is HMACSHA256.</span></span> <span data-ttu-id="aaa17-175">可以由系统管理员通过设置默认策略[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)，但显式调用`UseCryptographicAlgorithms`重写默认策略。</span><span class="sxs-lookup"><span data-stu-id="aaa17-175">The default policy can be set by a system administrator via a [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy), but an explicit call to `UseCryptographicAlgorithms` overrides the default policy.</span></span>

<span data-ttu-id="aaa17-176">调用`UseCryptographicAlgorithms`，可指定预定义的内置列表中的所需的算法。</span><span class="sxs-lookup"><span data-stu-id="aaa17-176">Calling `UseCryptographicAlgorithms` allows you to specify the desired algorithm from a predefined built-in list.</span></span> <span data-ttu-id="aaa17-177">无需担心有关算法的实现。</span><span class="sxs-lookup"><span data-stu-id="aaa17-177">You don't need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="aaa17-178">在上述方案中，数据保护系统尝试使用 AES 的 CNG 实现，如果在 Windows 上运行。</span><span class="sxs-lookup"><span data-stu-id="aaa17-178">In the scenario above, the Data Protection system attempts to use the CNG implementation of AES if running on Windows.</span></span> <span data-ttu-id="aaa17-179">否则，它将回退到托管[System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes)类。</span><span class="sxs-lookup"><span data-stu-id="aaa17-179">Otherwise, it falls back to the managed [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) class.</span></span>

<span data-ttu-id="aaa17-180">您可以手动指定通过调用实现[UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms)。</span><span class="sxs-lookup"><span data-stu-id="aaa17-180">You can manually specify an implementation via a call to [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span></span>

> [!TIP]
> <span data-ttu-id="aaa17-181">更改算法不会影响密钥环中的现有密钥。</span><span class="sxs-lookup"><span data-stu-id="aaa17-181">Changing algorithms doesn't affect existing keys in the key ring.</span></span> <span data-ttu-id="aaa17-182">它只影响新生成的键。</span><span class="sxs-lookup"><span data-stu-id="aaa17-182">It only affects newly-generated keys.</span></span>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="aaa17-183">指定托管的自定义算法</span><span class="sxs-lookup"><span data-stu-id="aaa17-183">Specifying custom managed algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="aaa17-184">若要指定托管的自定义算法，创建[ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration)指向实现类型的实例：</span><span class="sxs-lookup"><span data-stu-id="aaa17-184">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instance that points to the implementation types:</span></span>

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

<span data-ttu-id="aaa17-185">若要指定托管的自定义算法，创建[ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings)指向实现类型的实例：</span><span class="sxs-lookup"><span data-stu-id="aaa17-185">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instance that points to the implementation types:</span></span>

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

<span data-ttu-id="aaa17-186">通常\*类型属性必须指向具体，可实例化 （通过公共的无参数构造函数） 的实现[SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm)并[KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm)，但系统特殊的情况下等某些值`typeof(Aes)`为方便起见。</span><span class="sxs-lookup"><span data-stu-id="aaa17-186">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) and [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), though the system special-cases some values like `typeof(Aes)` for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="aaa17-187">SymmetricAlgorithm 必须 ≥ 128 位的密钥长度和 ≥ 64 位为单位的块大小，并且它必须支持使用 PKCS #7 填充 CBC 模式加密。</span><span class="sxs-lookup"><span data-stu-id="aaa17-187">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="aaa17-188">KeyedHashAlgorithm 必须具有的摘要的大小 > = 128 位，并且它必须支持密钥长度等于哈希算法的摘要的长度。</span><span class="sxs-lookup"><span data-stu-id="aaa17-188">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="aaa17-189">KeyedHashAlgorithm 并非是严格要求，为 HMAC。</span><span class="sxs-lookup"><span data-stu-id="aaa17-189">The KeyedHashAlgorithm isn't strictly required to be HMAC.</span></span>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="aaa17-190">指定自定义 Windows CNG 算法</span><span class="sxs-lookup"><span data-stu-id="aaa17-190">Specifying custom Windows CNG algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="aaa17-191">若要指定自定义 Windows CNG 算法的 HMAC 验证使用 CBC 模式加密，创建[CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration)实例，它包含的算法的信息：</span><span class="sxs-lookup"><span data-stu-id="aaa17-191">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

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

<span data-ttu-id="aaa17-192">若要指定自定义 Windows CNG 算法的 HMAC 验证使用 CBC 模式加密，创建[CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings)实例，它包含的算法的信息：</span><span class="sxs-lookup"><span data-stu-id="aaa17-192">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

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
> <span data-ttu-id="aaa17-193">对称块加密算法的密钥长度必须 > = 128 位的块大小 > = 64 位，并且它必须支持使用 PKCS #7 填充 CBC 模式加密。</span><span class="sxs-lookup"><span data-stu-id="aaa17-193">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of >= 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="aaa17-194">哈希算法必须具有的摘要的大小 > = 128 位并且必须支持正在打开与 BCRYPT\_ALG\_处理\_HMAC\_标志的标志。</span><span class="sxs-lookup"><span data-stu-id="aaa17-194">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT\_ALG\_HANDLE\_HMAC\_FLAG flag.</span></span> <span data-ttu-id="aaa17-195">\*提供程序属性可以设置为 null 以使用指定的算法的默认提供程序。</span><span class="sxs-lookup"><span data-stu-id="aaa17-195">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="aaa17-196">请参阅[BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx)文档了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="aaa17-196">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="aaa17-197">若要指定自定义 Windows CNG 算法使用 Galois/计数器模式加密进行验证，创建[CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration)实例，它包含的算法的信息：</span><span class="sxs-lookup"><span data-stu-id="aaa17-197">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

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

<span data-ttu-id="aaa17-198">若要指定自定义 Windows CNG 算法使用 Galois/计数器模式加密进行验证，创建[CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings)实例，它包含的算法的信息：</span><span class="sxs-lookup"><span data-stu-id="aaa17-198">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

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
> <span data-ttu-id="aaa17-199">对称块加密算法的密钥长度必须 > = 128 位的块大小为完全 128 位，并且它必须支持 GCM 加密。</span><span class="sxs-lookup"><span data-stu-id="aaa17-199">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="aaa17-200">可以设置[EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider)属性设置为 null 以使用指定的算法的默认提供程序。</span><span class="sxs-lookup"><span data-stu-id="aaa17-200">You can set the [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) property to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="aaa17-201">请参阅[BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx)文档了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="aaa17-201">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="aaa17-202">指定其他自定义算法</span><span class="sxs-lookup"><span data-stu-id="aaa17-202">Specifying other custom algorithms</span></span>

<span data-ttu-id="aaa17-203">尽管不公开为第一类 API，数据保护系统是算法的可扩展，足以允许指定几乎任何类型。</span><span class="sxs-lookup"><span data-stu-id="aaa17-203">Though not exposed as a first-class API, the Data Protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="aaa17-204">例如，就可以使包含硬件安全模块 (HSM) 中的所有密钥并提供加密和解密例程的核心的自定义实现。</span><span class="sxs-lookup"><span data-stu-id="aaa17-204">For example, it's possible to keep all keys contained within a Hardware Security Module (HSM) and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="aaa17-205">请参阅[IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor)中[核心加密扩展性](xref:security/data-protection/extensibility/core-crypto)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="aaa17-205">See [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core cryptography extensibility](xref:security/data-protection/extensibility/core-crypto) for more information.</span></span>

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a><span data-ttu-id="aaa17-206">保存密钥托管在 Docker 容器中时</span><span class="sxs-lookup"><span data-stu-id="aaa17-206">Persisting keys when hosting in a Docker container</span></span>

<span data-ttu-id="aaa17-207">在中承载时[Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/)容器，应在维护密钥：</span><span class="sxs-lookup"><span data-stu-id="aaa17-207">When hosting in a [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) container, keys should be maintained in either:</span></span>

* <span data-ttu-id="aaa17-208">是一个 Docker 卷仍然存在超出容器的生存期，如共享的卷或主机装入的卷，一个文件夹。</span><span class="sxs-lookup"><span data-stu-id="aaa17-208">A folder that's a Docker volume that persists beyond the container's lifetime, such as a shared volume or a host-mounted volume.</span></span>
* <span data-ttu-id="aaa17-209">外部提供程序，如[Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)或[Redis](https://redis.io/)。</span><span class="sxs-lookup"><span data-stu-id="aaa17-209">An external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aaa17-210">其他资源</span><span class="sxs-lookup"><span data-stu-id="aaa17-210">Additional resources</span></span>

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
