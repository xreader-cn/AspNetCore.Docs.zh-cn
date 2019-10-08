---
title: 配置 ASP.NET Core 数据保护
author: rick-anderson
description: 了解如何在 ASP.NET Core 中配置数据保护。
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 380293f650c9548c286f98c0447c7ed08b918f2a
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007374"
---
# <a name="configure-aspnet-core-data-protection"></a><span data-ttu-id="32d1b-103">配置 ASP.NET Core 数据保护</span><span class="sxs-lookup"><span data-stu-id="32d1b-103">Configure ASP.NET Core Data Protection</span></span>

<span data-ttu-id="32d1b-104">当数据保护系统已初始化时，它将基于操作环境应用[默认设置](xref:security/data-protection/configuration/default-settings)。</span><span class="sxs-lookup"><span data-stu-id="32d1b-104">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) based on the operational environment.</span></span> <span data-ttu-id="32d1b-105">这些设置通常适用于在一台计算机上运行的应用程序。</span><span class="sxs-lookup"><span data-stu-id="32d1b-105">These settings are generally appropriate for apps running on a single machine.</span></span> <span data-ttu-id="32d1b-106">在某些情况下，开发人员可能想要更改默认设置：</span><span class="sxs-lookup"><span data-stu-id="32d1b-106">There are cases where a developer may want to change the default settings:</span></span>

* <span data-ttu-id="32d1b-107">应用程序分布在多台计算机上。</span><span class="sxs-lookup"><span data-stu-id="32d1b-107">The app is spread across multiple machines.</span></span>
* <span data-ttu-id="32d1b-108">出于合规性原因。</span><span class="sxs-lookup"><span data-stu-id="32d1b-108">For compliance reasons.</span></span>

<span data-ttu-id="32d1b-109">在这些情况下，数据保护系统提供了丰富的配置 API。</span><span class="sxs-lookup"><span data-stu-id="32d1b-109">For these scenarios, the Data Protection system offers a rich configuration API.</span></span>

> [!WARNING]
> <span data-ttu-id="32d1b-110">与配置文件类似，应使用适当的权限保护数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="32d1b-110">Similar to configuration files, the data protection key ring should be protected using appropriate permissions.</span></span> <span data-ttu-id="32d1b-111">你可以选择对静态密钥加密，但这不会阻止攻击者创建新密钥。</span><span class="sxs-lookup"><span data-stu-id="32d1b-111">You can choose to encrypt keys at rest, but this doesn't prevent attackers from creating new keys.</span></span> <span data-ttu-id="32d1b-112">因此，应用的安全将受到影响。</span><span class="sxs-lookup"><span data-stu-id="32d1b-112">Consequently, your app's security is impacted.</span></span> <span data-ttu-id="32d1b-113">使用数据保护配置的存储位置应该将其访问权限限制为应用本身，这与保护配置文件的方式类似。</span><span class="sxs-lookup"><span data-stu-id="32d1b-113">The storage location configured with Data Protection should have its access limited to the app itself, similar to the way you would protect configuration files.</span></span> <span data-ttu-id="32d1b-114">例如，如果选择将密钥环存储在磁盘上，请使用文件系统权限。</span><span class="sxs-lookup"><span data-stu-id="32d1b-114">For example, if you choose to store your key ring on disk, use file system permissions.</span></span> <span data-ttu-id="32d1b-115">确保你的 web 应用在其下运行的标识具有对该目录的读取、写入和创建访问权限。</span><span class="sxs-lookup"><span data-stu-id="32d1b-115">Ensure only the identity under which your web app runs has read, write, and create access to that directory.</span></span> <span data-ttu-id="32d1b-116">如果使用 Azure Blob 存储，则只有 web 应用应能够在 Blob 存储中读取、写入或创建新条目等。</span><span class="sxs-lookup"><span data-stu-id="32d1b-116">If you use Azure Blob Storage, only the web app should have the ability to read, write, or create new entries in the blob store, etc.</span></span>
>
> <span data-ttu-id="32d1b-117">扩展方法[AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection)返回[IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder)。</span><span class="sxs-lookup"><span data-stu-id="32d1b-117">The extension method [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) returns an [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span></span> <span data-ttu-id="32d1b-118">`IDataProtectionBuilder` 公开扩展方法，你可以将这些方法链接在一起来配置数据保护选项。</span><span class="sxs-lookup"><span data-stu-id="32d1b-118">`IDataProtectionBuilder` exposes extension methods that you can chain together to configure Data Protection options.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a><span data-ttu-id="32d1b-119">ProtectKeysWithAzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="32d1b-119">ProtectKeysWithAzureKeyVault</span></span>

<span data-ttu-id="32d1b-120">若要在[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)中存储密钥，请在 @no__t 2 类中配置[ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault)的系统：</span><span class="sxs-lookup"><span data-stu-id="32d1b-120">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="32d1b-121">设置密钥环存储位置（例如， [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)）。</span><span class="sxs-lookup"><span data-stu-id="32d1b-121">Set the key ring storage location (for example, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span></span> <span data-ttu-id="32d1b-122">必须设置位置，因为调用 `ProtectKeysWithAzureKeyVault` 实现了禁用自动数据保护设置的[IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) ，包括密钥环存储位置。</span><span class="sxs-lookup"><span data-stu-id="32d1b-122">The location must be set because calling `ProtectKeysWithAzureKeyVault` implements an [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) that disables automatic data protection settings, including the key ring storage location.</span></span> <span data-ttu-id="32d1b-123">前面的示例使用 Azure Blob 存储来持久保存密钥环。</span><span class="sxs-lookup"><span data-stu-id="32d1b-123">The preceding example uses Azure Blob Storage to persist the key ring.</span></span> <span data-ttu-id="32d1b-124">有关详细信息，请参阅 @no__t 0Key 存储提供程序：Azure 存储](xref:security/data-protection/implementation/key-storage-providers#azure-storage)。</span><span class="sxs-lookup"><span data-stu-id="32d1b-124">For more information, see [Key storage providers: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span></span> <span data-ttu-id="32d1b-125">还可以通过[PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system)将密钥环保存到本地。</span><span class="sxs-lookup"><span data-stu-id="32d1b-125">You can also persist the key ring locally with [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span></span>

<span data-ttu-id="32d1b-126">@No__t-0 是用于密钥加密的密钥保管库密钥标识符。</span><span class="sxs-lookup"><span data-stu-id="32d1b-126">The `keyIdentifier` is the key vault key identifier used for key encryption.</span></span> <span data-ttu-id="32d1b-127">例如，在 `contosokeyvault` 中名为 `dataprotection` 的密钥保管库中创建的密钥具有密钥标识符 `https://contosokeyvault.vault.azure.net/keys/dataprotection/`。</span><span class="sxs-lookup"><span data-stu-id="32d1b-127">For example, a key created in key vault named `dataprotection` in the `contosokeyvault` has the key identifier `https://contosokeyvault.vault.azure.net/keys/dataprotection/`.</span></span> <span data-ttu-id="32d1b-128">向应用提供对密钥保管库的**解包密钥**和**包装密钥**权限。</span><span class="sxs-lookup"><span data-stu-id="32d1b-128">Provide the app with **Unwrap Key** and **Wrap Key** permissions to the key vault.</span></span>

<span data-ttu-id="32d1b-129">`ProtectKeysWithAzureKeyVault` 重载：</span><span class="sxs-lookup"><span data-stu-id="32d1b-129">`ProtectKeysWithAzureKeyVault` overloads:</span></span>

* <span data-ttu-id="32d1b-130">[ProtectKeysWithAzureKeyVault （IDataProtectionBuilder，KeyVaultClient，String）](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_)允许使用[KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)使数据保护系统能够使用密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="32d1b-130">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) permits the use of a [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="32d1b-131">[ProtectKeysWithAzureKeyVault （IDataProtectionBuilder，string，string，X509Certificate2）](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_)允许使用 `ClientId` 和[X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) ，使数据保护系统能够使用密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="32d1b-131">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) permits the use of a `ClientId` and [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="32d1b-132">[ProtectKeysWithAzureKeyVault （IDataProtectionBuilder，string，string，string）](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_)允许使用 `ClientId` 和 `ClientSecret` 来使数据保护系统使用密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="32d1b-132">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) permits the use of a `ClientId` and `ClientSecret` to enable the data protection system to use the key vault.</span></span>

::: moniker-end

## <a name="persistkeystofilesystem"></a><span data-ttu-id="32d1b-133">PersistKeysToFileSystem</span><span class="sxs-lookup"><span data-stu-id="32d1b-133">PersistKeysToFileSystem</span></span>

<span data-ttu-id="32d1b-134">若要将密钥存储在 UNC 共享上，而不是存储在 *% LOCALAPPDATA%* 默认位置，请使用[PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)配置系统：</span><span class="sxs-lookup"><span data-stu-id="32d1b-134">To store keys on a UNC share instead of at the *%LOCALAPPDATA%* default location, configure the system with [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> <span data-ttu-id="32d1b-135">如果更改密钥持久性位置，系统将不再自动加密静态密钥，因为它不知道 DPAPI 是否为适当的加密机制。</span><span class="sxs-lookup"><span data-stu-id="32d1b-135">If you change the key persistence location, the system no longer automatically encrypts keys at rest, since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

## <a name="protectkeyswith"></a><span data-ttu-id="32d1b-136">ProtectKeysWith\*</span><span class="sxs-lookup"><span data-stu-id="32d1b-136">ProtectKeysWith\*</span></span>

<span data-ttu-id="32d1b-137">可以通过调用[ProtectKeysWith @ no__t](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions)配置 api，将系统配置为保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="32d1b-137">You can configure the system to protect keys at rest by calling any of the [ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) configuration APIs.</span></span> <span data-ttu-id="32d1b-138">请考虑以下示例，该示例将密钥存储在 UNC 共享上，并使用特定的 x.509 证书对静态密钥进行加密：</span><span class="sxs-lookup"><span data-stu-id="32d1b-138">Consider the example below, which stores keys on a UNC share and encrypts those keys at rest with a specific X.509 certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="32d1b-139">在 ASP.NET Core 2.1 或更高版本中，你可以提供[ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)的[X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) ，例如从文件中加载的证书：</span><span class="sxs-lookup"><span data-stu-id="32d1b-139">In ASP.NET Core 2.1 or later, you can provide an [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), such as a certificate loaded from a file:</span></span>

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

<span data-ttu-id="32d1b-140">有关内置密钥加密机制的更多示例和讨论，请参阅[静态密钥加密](xref:security/data-protection/implementation/key-encryption-at-rest)。</span><span class="sxs-lookup"><span data-stu-id="32d1b-140">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more examples and discussion on the built-in key encryption mechanisms.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a><span data-ttu-id="32d1b-141">UnprotectKeysWithAnyCertificate</span><span class="sxs-lookup"><span data-stu-id="32d1b-141">UnprotectKeysWithAnyCertificate</span></span>

<span data-ttu-id="32d1b-142">在 ASP.NET Core 2.1 或更高版本中，你可以使用[UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate)的[X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)</span><span class="sxs-lookup"><span data-stu-id="32d1b-142">In ASP.NET Core 2.1 or later, you can rotate certificates and decrypt keys at rest using an array of [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certificates with [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span></span>

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

## <a name="setdefaultkeylifetime"></a><span data-ttu-id="32d1b-143">SetDefaultKeyLifetime</span><span class="sxs-lookup"><span data-stu-id="32d1b-143">SetDefaultKeyLifetime</span></span>

<span data-ttu-id="32d1b-144">若要将系统配置为使用14天的密钥生存期而不是默认的90天，请使用[SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime)：</span><span class="sxs-lookup"><span data-stu-id="32d1b-144">To configure the system to use a key lifetime of 14 days instead of the default 90 days, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a><span data-ttu-id="32d1b-145">SetApplicationName</span><span class="sxs-lookup"><span data-stu-id="32d1b-145">SetApplicationName</span></span>

<span data-ttu-id="32d1b-146">默认情况下，数据保护系统基于其[内容根](xref:fundamentals/index#content-root)路径将应用彼此隔离，即使它们共享相同的物理密钥存储库。</span><span class="sxs-lookup"><span data-stu-id="32d1b-146">By default, the Data Protection system isolates apps from one another based on their [content root](xref:fundamentals/index#content-root) paths, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="32d1b-147">这可防止应用了解彼此的受保护负载。</span><span class="sxs-lookup"><span data-stu-id="32d1b-147">This prevents the apps from understanding each other's protected payloads.</span></span>

<span data-ttu-id="32d1b-148">在应用之间共享受保护的负载：</span><span class="sxs-lookup"><span data-stu-id="32d1b-148">To share protected payloads among apps:</span></span>

* <span data-ttu-id="32d1b-149">用相同的值在每个应用中配置 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>。</span><span class="sxs-lookup"><span data-stu-id="32d1b-149">Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in each app with the same value.</span></span>
* <span data-ttu-id="32d1b-150">在应用程序中使用相同版本的数据保护 API 堆栈。</span><span class="sxs-lookup"><span data-stu-id="32d1b-150">Use the same version of the Data Protection API stack across the apps.</span></span> <span data-ttu-id="32d1b-151">在应用的项目文件中执行以下**任一**操作：</span><span class="sxs-lookup"><span data-stu-id="32d1b-151">Perform **either** of the following in the apps' project files:</span></span>
  * <span data-ttu-id="32d1b-152">通过[AspNetCore 元包](xref:fundamentals/metapackage-app)引用相同的共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="32d1b-152">Reference the same shared framework version via the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="32d1b-153">引用相同的[数据保护包](xref:security/data-protection/introduction#package-layout)版本。</span><span class="sxs-lookup"><span data-stu-id="32d1b-153">Reference the same [Data Protection package](xref:security/data-protection/introduction#package-layout) version.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a><span data-ttu-id="32d1b-154">DisableAutomaticKeyGeneration</span><span class="sxs-lookup"><span data-stu-id="32d1b-154">DisableAutomaticKeyGeneration</span></span>

<span data-ttu-id="32d1b-155">您可能会遇到这样的情况：不希望应用程序在接近过期时自动滚动更新密钥（创建新的密钥）。</span><span class="sxs-lookup"><span data-stu-id="32d1b-155">You may have a scenario where you don't want an app to automatically roll keys (create new keys) as they approach expiration.</span></span> <span data-ttu-id="32d1b-156">这种情况的一个示例可能是在主/辅助关系中设置的应用，其中只有主应用负责密钥管理问题，辅助应用只是具有密钥环的只读视图。</span><span class="sxs-lookup"><span data-stu-id="32d1b-156">One example of this might be apps set up in a primary/secondary relationship, where only the primary app is responsible for key management concerns and secondary apps simply have a read-only view of the key ring.</span></span> <span data-ttu-id="32d1b-157">可以通过将系统配置为 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>，将辅助应用配置为将密钥环视为只读：</span><span class="sxs-lookup"><span data-stu-id="32d1b-157">The secondary apps can be configured to treat the key ring as read-only by configuring the system with <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a><span data-ttu-id="32d1b-158">每应用程序隔离</span><span class="sxs-lookup"><span data-stu-id="32d1b-158">Per-application isolation</span></span>

<span data-ttu-id="32d1b-159">数据保护系统提供了 ASP.NET Core 主机，但它自动隔离了从另一个，应用程序，即使这些应用在相同的工作进程帐户下运行，并且使用相同的主密钥材料。</span><span class="sxs-lookup"><span data-stu-id="32d1b-159">When the Data Protection system is provided by an ASP.NET Core host, it automatically isolates apps from one another, even if those apps are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="32d1b-160">这有点类似于 IsolateApps @no__t 中的修饰符。</span><span class="sxs-lookup"><span data-stu-id="32d1b-160">This is somewhat similar to the IsolateApps modifier from System.Web's `<machineKey>` element.</span></span>

<span data-ttu-id="32d1b-161">隔离机制的工作原理是将本地计算机上的每个应用视为唯一的租户，因此，任何给定应用的 @no__t 0 会自动将应用 ID 包含为鉴别器。</span><span class="sxs-lookup"><span data-stu-id="32d1b-161">The isolation mechanism works by considering each app on the local machine as a unique tenant, thus the <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> rooted for any given app automatically includes the app ID as a discriminator.</span></span> <span data-ttu-id="32d1b-162">应用的唯一 ID 是应用的物理路径：</span><span class="sxs-lookup"><span data-stu-id="32d1b-162">The app's unique ID is the app's physical path:</span></span>

* <span data-ttu-id="32d1b-163">对于在 IIS 中托管的应用，唯一 ID 是应用的 IIS 物理路径。</span><span class="sxs-lookup"><span data-stu-id="32d1b-163">For apps hosted in IIS, the unique ID is the IIS physical path of the app.</span></span> <span data-ttu-id="32d1b-164">如果在 web 场环境中部署了应用，则此值是稳定的，假定在 web 场中的所有计算机上配置了类似的 IIS 环境。</span><span class="sxs-lookup"><span data-stu-id="32d1b-164">If an app is deployed in a web farm environment, this value is stable assuming that the IIS environments are configured similarly across all machines in the web farm.</span></span>
* <span data-ttu-id="32d1b-165">对于在[Kestrel 服务器](xref:fundamentals/servers/index#kestrel)上运行的自承载应用程序，唯一 ID 是指向磁盘上的应用程序的物理路径。</span><span class="sxs-lookup"><span data-stu-id="32d1b-165">For self-hosted apps running on the [Kestrel server](xref:fundamentals/servers/index#kestrel), the unique ID is the physical path to the app on disk.</span></span>

<span data-ttu-id="32d1b-166">唯一标识符旨在重置单个应用和计算机本身的 @ no__t-0both。</span><span class="sxs-lookup"><span data-stu-id="32d1b-166">The unique identifier is designed to survive resets&mdash;both of the individual app and of the machine itself.</span></span>

<span data-ttu-id="32d1b-167">此隔离机制假定应用不是恶意的。</span><span class="sxs-lookup"><span data-stu-id="32d1b-167">This isolation mechanism assumes that the apps are not malicious.</span></span> <span data-ttu-id="32d1b-168">恶意应用始终会影响在同一工作进程帐户下运行的任何其他应用。</span><span class="sxs-lookup"><span data-stu-id="32d1b-168">A malicious app can always impact any other app running under the same worker process account.</span></span> <span data-ttu-id="32d1b-169">在应用不受信任的共享主机环境中，托管提供商应采取措施来确保应用之间的操作系统级隔离，包括分离应用程序的底层密钥存储库。</span><span class="sxs-lookup"><span data-stu-id="32d1b-169">In a shared hosting environment where apps are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between apps, including separating the apps' underlying key repositories.</span></span>

<span data-ttu-id="32d1b-170">如果由 ASP.NET Core 主机未提供数据保护系统 (例如，如果通过其实例化`DataProtectionProvider`具体类型) 应用程序隔离在默认情况下处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="32d1b-170">If the Data Protection system isn't provided by an ASP.NET Core host (for example, if you instantiate it via the `DataProtectionProvider` concrete type) app isolation is disabled by default.</span></span> <span data-ttu-id="32d1b-171">禁用应用隔离后，只要提供相应的[用途](xref:security/data-protection/consumer-apis/purpose-strings)，同一密钥材料支持的所有应用就可以共享有效负载。</span><span class="sxs-lookup"><span data-stu-id="32d1b-171">When app isolation is disabled, all apps backed by the same keying material can share payloads as long as they provide the appropriate [purposes](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="32d1b-172">若要在此环境中提供应用隔离，请对配置对象调用[SetApplicationName](#setapplicationname)方法，并为每个应用提供唯一的名称。</span><span class="sxs-lookup"><span data-stu-id="32d1b-172">To provide app isolation in this environment, call the [SetApplicationName](#setapplicationname) method on the configuration object and provide a unique name for each app.</span></span>

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a><span data-ttu-id="32d1b-173">用 UseCryptographicAlgorithms 更改算法</span><span class="sxs-lookup"><span data-stu-id="32d1b-173">Changing algorithms with UseCryptographicAlgorithms</span></span>

<span data-ttu-id="32d1b-174">数据保护堆栈允许您更改新生成的密钥使用的默认算法。</span><span class="sxs-lookup"><span data-stu-id="32d1b-174">The Data Protection stack allows you to change the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="32d1b-175">执行此操作的最简单方法是从配置回调调用[UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) ：</span><span class="sxs-lookup"><span data-stu-id="32d1b-175">The simplest way to do this is to call [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) from the configuration callback:</span></span>

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

<span data-ttu-id="32d1b-176">默认 EncryptionAlgorithm 为 AES-256-CBC，默认 ValidationAlgorithm 为 HMACSHA256。</span><span class="sxs-lookup"><span data-stu-id="32d1b-176">The default EncryptionAlgorithm is AES-256-CBC, and the default ValidationAlgorithm is HMACSHA256.</span></span> <span data-ttu-id="32d1b-177">系统管理员可以通过[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)设置默认策略，但对 @no__t 的显式调用会替代默认策略。</span><span class="sxs-lookup"><span data-stu-id="32d1b-177">The default policy can be set by a system administrator via a [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy), but an explicit call to `UseCryptographicAlgorithms` overrides the default policy.</span></span>

<span data-ttu-id="32d1b-178">调用 `UseCryptographicAlgorithms` 使你可以从预定义的内置列表中指定所需的算法。</span><span class="sxs-lookup"><span data-stu-id="32d1b-178">Calling `UseCryptographicAlgorithms` allows you to specify the desired algorithm from a predefined built-in list.</span></span> <span data-ttu-id="32d1b-179">您无需担心算法的实现。</span><span class="sxs-lookup"><span data-stu-id="32d1b-179">You don't need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="32d1b-180">在上述方案中，如果在 Windows 上运行，数据保护系统将尝试使用 AES 的 CNG 实现。</span><span class="sxs-lookup"><span data-stu-id="32d1b-180">In the scenario above, the Data Protection system attempts to use the CNG implementation of AES if running on Windows.</span></span> <span data-ttu-id="32d1b-181">否则，它会回退到托管的[系统。](/dotnet/api/system.security.cryptography.aes)</span><span class="sxs-lookup"><span data-stu-id="32d1b-181">Otherwise, it falls back to the managed [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) class.</span></span>

<span data-ttu-id="32d1b-182">可以通过调用[UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms)手动指定实现。</span><span class="sxs-lookup"><span data-stu-id="32d1b-182">You can manually specify an implementation via a call to [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span></span>

> [!TIP]
> <span data-ttu-id="32d1b-183">更改算法不会影响密钥环中的现有密钥。</span><span class="sxs-lookup"><span data-stu-id="32d1b-183">Changing algorithms doesn't affect existing keys in the key ring.</span></span> <span data-ttu-id="32d1b-184">它仅影响新生成的键。</span><span class="sxs-lookup"><span data-stu-id="32d1b-184">It only affects newly-generated keys.</span></span>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="32d1b-185">指定自定义托管算法</span><span class="sxs-lookup"><span data-stu-id="32d1b-185">Specifying custom managed algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="32d1b-186">若要指定自定义托管算法，请创建一个指向实现类型的[ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration)实例：</span><span class="sxs-lookup"><span data-stu-id="32d1b-186">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instance that points to the implementation types:</span></span>

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

<span data-ttu-id="32d1b-187">若要指定自定义托管算法，请创建一个指向实现类型的[ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings)实例：</span><span class="sxs-lookup"><span data-stu-id="32d1b-187">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instance that points to the implementation types:</span></span>

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

<span data-ttu-id="32d1b-188">通常，@no__t 0Type 属性必须指向[system.security.cryptography.symmetricalgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm)和[KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm)的具体实例实例（通过公共无参数的 ctor）实现，尽管系统 @no__t 特别适用于方便性.</span><span class="sxs-lookup"><span data-stu-id="32d1b-188">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) and [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), though the system special-cases some values like `typeof(Aes)` for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="32d1b-189">System.security.cryptography.symmetricalgorithm 必须具有≥128位的密钥长度和≥64位的块大小，并且必须支持 PKCS #7 填充的 CBC 模式加密。</span><span class="sxs-lookup"><span data-stu-id="32d1b-189">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="32d1b-190">KeyedHashAlgorithm 的摘要大小必须为 > = 128 位，并且它必须支持长度等于哈希算法摘要长度的键。</span><span class="sxs-lookup"><span data-stu-id="32d1b-190">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="32d1b-191">KeyedHashAlgorithm 不一定是 HMAC。</span><span class="sxs-lookup"><span data-stu-id="32d1b-191">The KeyedHashAlgorithm isn't strictly required to be HMAC.</span></span>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="32d1b-192">指定自定义 Windows CNG 算法</span><span class="sxs-lookup"><span data-stu-id="32d1b-192">Specifying custom Windows CNG algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="32d1b-193">若要通过 HMAC 验证使用 CBC 模式加密指定自定义 Windows CNG 算法，请创建包含算法信息的[CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration)实例：</span><span class="sxs-lookup"><span data-stu-id="32d1b-193">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

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

<span data-ttu-id="32d1b-194">若要通过 HMAC 验证使用 CBC 模式加密指定自定义 Windows CNG 算法，请创建包含算法信息的[CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings)实例：</span><span class="sxs-lookup"><span data-stu-id="32d1b-194">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

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
> <span data-ttu-id="32d1b-195">对称块加密算法的密钥长度必须为 > = 128 位，块大小为 > = 64 位，并且它必须支持 PKCS #7 填充的 CBC 模式加密。</span><span class="sxs-lookup"><span data-stu-id="32d1b-195">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of >= 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="32d1b-196">哈希算法的摘要大小必须为 > = 128 位，并且必须支持使用 BCRYPT @ no__t-0ALG @ no__t-1HANDLE @ no__t-2HMAC @ no__t-3FLAG 标志打开。</span><span class="sxs-lookup"><span data-stu-id="32d1b-196">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT\_ALG\_HANDLE\_HMAC\_FLAG flag.</span></span> <span data-ttu-id="32d1b-197">可以将 @no__t 0Provider 属性设置为 null，以将默认提供程序用于指定的算法。</span><span class="sxs-lookup"><span data-stu-id="32d1b-197">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="32d1b-198">有关详细信息，请参阅[BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx)文档。</span><span class="sxs-lookup"><span data-stu-id="32d1b-198">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="32d1b-199">若要使用 Galois/Counter 模式加密和验证来指定自定义 Windows CNG 算法，请创建包含算法信息的[CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration)实例：</span><span class="sxs-lookup"><span data-stu-id="32d1b-199">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

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

<span data-ttu-id="32d1b-200">若要使用 Galois/Counter 模式加密和验证来指定自定义 Windows CNG 算法，请创建包含算法信息的[CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings)实例：</span><span class="sxs-lookup"><span data-stu-id="32d1b-200">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

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
> <span data-ttu-id="32d1b-201">对称块密码算法的密钥长度必须为 > = 128 位，块大小正好为128位，并且必须支持 GCM 加密。</span><span class="sxs-lookup"><span data-stu-id="32d1b-201">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="32d1b-202">可以将[EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider)属性设置为 null，以将默认提供程序用于指定的算法。</span><span class="sxs-lookup"><span data-stu-id="32d1b-202">You can set the [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) property to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="32d1b-203">有关详细信息，请参阅[BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx)文档。</span><span class="sxs-lookup"><span data-stu-id="32d1b-203">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="32d1b-204">指定其他自定义算法</span><span class="sxs-lookup"><span data-stu-id="32d1b-204">Specifying other custom algorithms</span></span>

<span data-ttu-id="32d1b-205">尽管不是作为第一类 API 公开的，但数据保护系统的扩展能力足以允许指定几乎任何类型的算法。</span><span class="sxs-lookup"><span data-stu-id="32d1b-205">Though not exposed as a first-class API, the Data Protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="32d1b-206">例如，可以保留硬件安全模块（HSM）中包含的所有密钥，并提供核心加密和解密例程的自定义实现。</span><span class="sxs-lookup"><span data-stu-id="32d1b-206">For example, it's possible to keep all keys contained within a Hardware Security Module (HSM) and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="32d1b-207">有关详细信息，请参阅[核心加密扩展性](xref:security/data-protection/extensibility/core-crypto)中的[IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) 。</span><span class="sxs-lookup"><span data-stu-id="32d1b-207">See [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core cryptography extensibility](xref:security/data-protection/extensibility/core-crypto) for more information.</span></span>

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a><span data-ttu-id="32d1b-208">在 Docker 容器中托管时保持密钥</span><span class="sxs-lookup"><span data-stu-id="32d1b-208">Persisting keys when hosting in a Docker container</span></span>

<span data-ttu-id="32d1b-209">在[Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/)容器中托管时，应在以下任一项中维护密钥：</span><span class="sxs-lookup"><span data-stu-id="32d1b-209">When hosting in a [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) container, keys should be maintained in either:</span></span>

* <span data-ttu-id="32d1b-210">一个文件夹，它是在容器的生存期之外保留的 Docker 卷，如共享卷或主机装入的卷。</span><span class="sxs-lookup"><span data-stu-id="32d1b-210">A folder that's a Docker volume that persists beyond the container's lifetime, such as a shared volume or a host-mounted volume.</span></span>
* <span data-ttu-id="32d1b-211">外部提供程序，如[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)或[Redis](https://redis.io/)。</span><span class="sxs-lookup"><span data-stu-id="32d1b-211">An external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span>

## <a name="persisting-keys-with-redis"></a><span data-ttu-id="32d1b-212">通过 Redis 保持密钥</span><span class="sxs-lookup"><span data-stu-id="32d1b-212">Persisting keys with Redis</span></span>

<span data-ttu-id="32d1b-213">只应使用支持[Redis 数据暂留](/azure/azure-cache-for-redis/cache-how-to-premium-persistence)的 Redis 版本来存储密钥。</span><span class="sxs-lookup"><span data-stu-id="32d1b-213">Only Redis versions supporting [Redis Data Persistence](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) should be used to store keys.</span></span> <span data-ttu-id="32d1b-214">[Azure Blob 存储](/azure/storage/blobs/storage-blobs-introduction)是持久性的，可用于存储密钥。</span><span class="sxs-lookup"><span data-stu-id="32d1b-214">[Azure Blob storage](/azure/storage/blobs/storage-blobs-introduction) is persistent and can be used to store keys.</span></span> <span data-ttu-id="32d1b-215">有关详细信息，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore/issues/13476)。</span><span class="sxs-lookup"><span data-stu-id="32d1b-215">For more information, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/13476).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="32d1b-216">其他资源</span><span class="sxs-lookup"><span data-stu-id="32d1b-216">Additional resources</span></span>

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
