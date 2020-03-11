---
title: 在 ASP.NET Core 中存放的密钥加密
author: rick-anderson
description: 了解 ASP.NET Core 数据保护密钥的加密对静止的实现详细信息。
ms.author: riande
ms.date: 07/16/2018
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: 52c3137dbe467096364b42430c92aecc7c15e313
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651630"
---
# <a name="key-encryption-at-rest-in-aspnet-core"></a><span data-ttu-id="cecf0-103">在 ASP.NET Core 中存放的密钥加密</span><span class="sxs-lookup"><span data-stu-id="cecf0-103">Key encryption at rest in ASP.NET Core</span></span>

<span data-ttu-id="cecf0-104">默认情况下，数据保护系统[使用发现机制](xref:security/data-protection/configuration/default-settings)来确定应如何对加密密钥进行静态加密。</span><span class="sxs-lookup"><span data-stu-id="cecf0-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine how cryptographic keys should be encrypted at rest.</span></span> <span data-ttu-id="cecf0-105">开发人员可以重写发现机制，并手动指定密钥的加密方式。</span><span class="sxs-lookup"><span data-stu-id="cecf0-105">The developer can override the discovery mechanism and manually specify how keys should be encrypted at rest.</span></span>

> [!WARNING]
> <span data-ttu-id="cecf0-106">如果指定显式[密钥持久性位置](xref:security/data-protection/implementation/key-storage-providers)，数据保护系统将注销静态密钥加密机制。</span><span class="sxs-lookup"><span data-stu-id="cecf0-106">If you specify an explicit [key persistence location](xref:security/data-protection/implementation/key-storage-providers), the data protection system deregisters the default key encryption at rest mechanism.</span></span> <span data-ttu-id="cecf0-107">因此，不再静态加密密钥。</span><span class="sxs-lookup"><span data-stu-id="cecf0-107">Consequently, keys are no longer encrypted at rest.</span></span> <span data-ttu-id="cecf0-108">建议为生产部署[指定显式密钥加密机制](xref:security/data-protection/implementation/key-encryption-at-rest)。</span><span class="sxs-lookup"><span data-stu-id="cecf0-108">We recommend that you [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span> <span data-ttu-id="cecf0-109">本主题介绍了静态加密机制选项。</span><span class="sxs-lookup"><span data-stu-id="cecf0-109">The encryption-at-rest mechanism options are described in this topic.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a><span data-ttu-id="cecf0-110">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="cecf0-110">Azure Key Vault</span></span>

<span data-ttu-id="cecf0-111">若要在[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)中存储密钥，请在 `Startup` 类中配置[ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault)的系统：</span><span class="sxs-lookup"><span data-stu-id="cecf0-111">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="cecf0-112">有关详细信息，请参阅[Configure ASP.NET Core Data Protection： ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault)。</span><span class="sxs-lookup"><span data-stu-id="cecf0-112">For more information, see [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span></span>

::: moniker-end

## <a name="windows-dpapi"></a><span data-ttu-id="cecf0-113">Windows DPAPI</span><span class="sxs-lookup"><span data-stu-id="cecf0-113">Windows DPAPI</span></span>

<span data-ttu-id="cecf0-114">**仅适用于 Windows 部署。**</span><span class="sxs-lookup"><span data-stu-id="cecf0-114">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="cecf0-115">使用 Windows DPAPI 时，将使用[CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata)对密钥材料进行加密，然后将其保存到存储中。</span><span class="sxs-lookup"><span data-stu-id="cecf0-115">When Windows DPAPI is used, key material is encrypted with [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) before being persisted to storage.</span></span> <span data-ttu-id="cecf0-116">DPAPI 是一种适用于从不在当前计算机之外读取的数据的适当加密机制（尽管可以将这些密钥备份到 Active Directory; 请参阅[DPAPI 和漫游配置文件](https://support.microsoft.com/kb/309408/#6)）。</span><span class="sxs-lookup"><span data-stu-id="cecf0-116">DPAPI is an appropriate encryption mechanism for data that's never read outside of the current machine (though it's possible to back these keys up to Active Directory; see [DPAPI and Roaming Profiles](https://support.microsoft.com/kb/309408/#6)).</span></span> <span data-ttu-id="cecf0-117">若要配置 DPAPI 静态密钥加密，请调用[ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi)扩展方法之一：</span><span class="sxs-lookup"><span data-stu-id="cecf0-117">To configure DPAPI key-at-rest encryption, call one of the [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) extension methods:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

<span data-ttu-id="cecf0-118">如果在没有参数的情况下调用 `ProtectKeysWithDpapi`，则只有当前的 Windows 用户帐户才能解密持久的密钥环。</span><span class="sxs-lookup"><span data-stu-id="cecf0-118">If `ProtectKeysWithDpapi` is called with no parameters, only the current Windows user account can decipher the persisted key ring.</span></span> <span data-ttu-id="cecf0-119">您可以选择指定计算机上的任何用户帐户（而不只是当前用户帐户）能够破译密钥环：</span><span class="sxs-lookup"><span data-stu-id="cecf0-119">You can optionally specify that any user account on the machine (not just the current user account) be able to decipher the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a><span data-ttu-id="cecf0-120">X.509 证书</span><span class="sxs-lookup"><span data-stu-id="cecf0-120">X.509 certificate</span></span>

<span data-ttu-id="cecf0-121">如果应用分布在多台计算机上，则在计算机上分发共享的 x.509 证书，并将托管应用配置为使用证书进行静态密钥加密可能会很方便。</span><span class="sxs-lookup"><span data-stu-id="cecf0-121">If the app is spread across multiple machines, it may be convenient to distribute a shared X.509 certificate across the machines and configure the hosted apps to use the certificate for encryption of keys at rest:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

<span data-ttu-id="cecf0-122">由于 .NET Framework 限制，仅支持具有 CAPI 私钥的证书。</span><span class="sxs-lookup"><span data-stu-id="cecf0-122">Due to .NET Framework limitations, only certificates with CAPI private keys are supported.</span></span> <span data-ttu-id="cecf0-123">请参阅下面的内容，了解这些限制的可能解决方法。</span><span class="sxs-lookup"><span data-stu-id="cecf0-123">See the content below for possible workarounds to these limitations.</span></span>

::: moniker-end

## <a name="windows-dpapi-ng"></a><span data-ttu-id="cecf0-124">Windows DPAPI-NG</span><span class="sxs-lookup"><span data-stu-id="cecf0-124">Windows DPAPI-NG</span></span>

<span data-ttu-id="cecf0-125">**此机制仅在 Windows 8/Windows Server 2012 或更高版本上可用。**</span><span class="sxs-lookup"><span data-stu-id="cecf0-125">**This mechanism is available only on Windows 8/Windows Server 2012 or later.**</span></span>

<span data-ttu-id="cecf0-126">从 Windows 8 开始，Windows OS 支持 DPAPI-NG （也称为 CNG DPAPI）。</span><span class="sxs-lookup"><span data-stu-id="cecf0-126">Beginning with Windows 8, Windows OS supports DPAPI-NG (also called CNG DPAPI).</span></span> <span data-ttu-id="cecf0-127">有关详细信息，请参阅[关于 CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi)。</span><span class="sxs-lookup"><span data-stu-id="cecf0-127">For more information, see [About CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span></span>

<span data-ttu-id="cecf0-128">主体编码为保护描述符规则。</span><span class="sxs-lookup"><span data-stu-id="cecf0-128">The principal is encoded as a protection descriptor rule.</span></span> <span data-ttu-id="cecf0-129">在以下调用[ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping)的示例中，只有具有指定 SID 的已加入域的用户才能解密密钥环：</span><span class="sxs-lookup"><span data-stu-id="cecf0-129">In the following example that calls [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), only the domain-joined user with the specified SID can decrypt the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="cecf0-130">还有 `ProtectKeysWithDpapiNG`的无参数重载。</span><span class="sxs-lookup"><span data-stu-id="cecf0-130">There's also a parameterless overload of `ProtectKeysWithDpapiNG`.</span></span> <span data-ttu-id="cecf0-131">使用此简便方法指定规则 "SID = {CURRENT_ACCOUNT_SID}"，其中*CURRENT_ACCOUNT_SID*是当前 Windows 用户帐户的 SID：</span><span class="sxs-lookup"><span data-stu-id="cecf0-131">Use this convenience method to specify the rule "SID={CURRENT_ACCOUNT_SID}", where *CURRENT_ACCOUNT_SID* is the SID of the current Windows user account:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

<span data-ttu-id="cecf0-132">在此方案中，AD 域控制器负责分发由 DPAPI-NG 操作使用的加密密钥。</span><span class="sxs-lookup"><span data-stu-id="cecf0-132">In this scenario, the AD domain controller is responsible for distributing the encryption keys used by the DPAPI-NG operations.</span></span> <span data-ttu-id="cecf0-133">目标用户可以从任何已加入域的计算机（前提是进程在其标识下运行）解密已加密的有效负载。</span><span class="sxs-lookup"><span data-stu-id="cecf0-133">The target user can decipher the encrypted payload from any domain-joined machine (provided that the process is running under their identity).</span></span>

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a><span data-ttu-id="cecf0-134">基于证书的加密和 Windows DPAPI-NG</span><span class="sxs-lookup"><span data-stu-id="cecf0-134">Certificate-based encryption with Windows DPAPI-NG</span></span>

<span data-ttu-id="cecf0-135">如果应用在 Windows 8.1/Windows Server 2012 R2 或更高版本上运行，则可以使用 Windows DPAPI-NG 执行基于证书的加密。</span><span class="sxs-lookup"><span data-stu-id="cecf0-135">If the app is running on Windows 8.1/Windows Server 2012 R2 or later, you can use Windows DPAPI-NG to perform certificate-based encryption.</span></span> <span data-ttu-id="cecf0-136">使用规则描述符字符串 "CERTIFICATE = HashId： THUMBPRINT"，其中*THUMBPRINT*是证书的十六进制编码的 SHA1 指纹：</span><span class="sxs-lookup"><span data-stu-id="cecf0-136">Use the rule descriptor string "CERTIFICATE=HashId:THUMBPRINT", where *THUMBPRINT* is the hex-encoded SHA1 thumbprint of the certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="cecf0-137">指向此存储库的任何应用都必须在 Windows 8.1/Windows Server 2012 R2 或更高版本上运行，才能解密密钥。</span><span class="sxs-lookup"><span data-stu-id="cecf0-137">Any app pointed at this repository must be running on Windows 8.1/Windows Server 2012 R2 or later to decipher the keys.</span></span>

## <a name="custom-key-encryption"></a><span data-ttu-id="cecf0-138">自定义密钥加密</span><span class="sxs-lookup"><span data-stu-id="cecf0-138">Custom key encryption</span></span>

<span data-ttu-id="cecf0-139">如果不适合使用机箱内机制，开发人员可以通过提供自定义[IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)来指定其自己的密钥加密机制。</span><span class="sxs-lookup"><span data-stu-id="cecf0-139">If the in-box mechanisms aren't appropriate, the developer can specify their own key encryption mechanism by providing a custom [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).</span></span>
