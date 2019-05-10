---
title: 在 ASP.NET Core 中存放的密钥加密
author: rick-anderson
description: 了解 ASP.NET Core 数据保护密钥的加密对静止的实现详细信息。
ms.author: riande
ms.date: 07/16/2018
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: 52c3137dbe467096364b42430c92aecc7c15e313
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892304"
---
# <a name="key-encryption-at-rest-in-aspnet-core"></a><span data-ttu-id="b1827-103">在 ASP.NET Core 中存放的密钥加密</span><span class="sxs-lookup"><span data-stu-id="b1827-103">Key encryption at rest in ASP.NET Core</span></span>

<span data-ttu-id="b1827-104">数据保护系统[默认情况下使用的发现机制](xref:security/data-protection/configuration/default-settings)来确定如何加密密钥应静态加密。</span><span class="sxs-lookup"><span data-stu-id="b1827-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine how cryptographic keys should be encrypted at rest.</span></span> <span data-ttu-id="b1827-105">开发人员可以重写的发现机制，并手动指定如何静态加密密钥。</span><span class="sxs-lookup"><span data-stu-id="b1827-105">The developer can override the discovery mechanism and manually specify how keys should be encrypted at rest.</span></span>

> [!WARNING]
> <span data-ttu-id="b1827-106">如果指定的显式[密钥暂留位置](xref:security/data-protection/implementation/key-storage-providers)，数据保护系统注销 rest 机制在默认密钥加密。</span><span class="sxs-lookup"><span data-stu-id="b1827-106">If you specify an explicit [key persistence location](xref:security/data-protection/implementation/key-storage-providers), the data protection system deregisters the default key encryption at rest mechanism.</span></span> <span data-ttu-id="b1827-107">因此，无法再进行静态加密密钥。</span><span class="sxs-lookup"><span data-stu-id="b1827-107">Consequently, keys are no longer encrypted at rest.</span></span> <span data-ttu-id="b1827-108">我们建议您[指定一个显式的密钥加密机制](xref:security/data-protection/implementation/key-encryption-at-rest)对于生产部署。</span><span class="sxs-lookup"><span data-stu-id="b1827-108">We recommend that you [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span> <span data-ttu-id="b1827-109">本主题中介绍的静态加密机制选项。</span><span class="sxs-lookup"><span data-stu-id="b1827-109">The encryption-at-rest mechanism options are described in this topic.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a><span data-ttu-id="b1827-110">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b1827-110">Azure Key Vault</span></span>

<span data-ttu-id="b1827-111">若要将密钥存储在[Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)，配置与系统[ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault)中`Startup`类：</span><span class="sxs-lookup"><span data-stu-id="b1827-111">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="b1827-112">有关详细信息，请参阅[配置 ASP.NET Core 数据保护：ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault)。</span><span class="sxs-lookup"><span data-stu-id="b1827-112">For more information, see [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span></span>

::: moniker-end

## <a name="windows-dpapi"></a><span data-ttu-id="b1827-113">Windows DPAPI</span><span class="sxs-lookup"><span data-stu-id="b1827-113">Windows DPAPI</span></span>

<span data-ttu-id="b1827-114">**仅适用于 Windows 的部署。**</span><span class="sxs-lookup"><span data-stu-id="b1827-114">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="b1827-115">使用 Windows DPAPI 时，使用加密密钥材料[CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata)之前保存到存储。</span><span class="sxs-lookup"><span data-stu-id="b1827-115">When Windows DPAPI is used, key material is encrypted with [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) before being persisted to storage.</span></span> <span data-ttu-id="b1827-116">DPAPI 是永远不会读取当前计算机之外的数据的适当的加密机制 (不过可以备份到 Active Directory 这些密钥; 请参阅[DPAPI 和漫游配置文件](https://support.microsoft.com/kb/309408/#6))。</span><span class="sxs-lookup"><span data-stu-id="b1827-116">DPAPI is an appropriate encryption mechanism for data that's never read outside of the current machine (though it's possible to back these keys up to Active Directory; see [DPAPI and Roaming Profiles](https://support.microsoft.com/kb/309408/#6)).</span></span> <span data-ttu-id="b1827-117">若要配置 DPAPI 密钥静态加密，请调用之一[ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi)扩展方法：</span><span class="sxs-lookup"><span data-stu-id="b1827-117">To configure DPAPI key-at-rest encryption, call one of the [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) extension methods:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

<span data-ttu-id="b1827-118">如果`ProtectKeysWithDpapi`称为不带任何参数，仅在当前 Windows 用户帐户，才能解密持久化的密钥环。</span><span class="sxs-lookup"><span data-stu-id="b1827-118">If `ProtectKeysWithDpapi` is called with no parameters, only the current Windows user account can decipher the persisted key ring.</span></span> <span data-ttu-id="b1827-119">您可以选择指定的计算机 （而不仅仅是当前用户帐户） 上的任何用户帐户能够解密密钥环：</span><span class="sxs-lookup"><span data-stu-id="b1827-119">You can optionally specify that any user account on the machine (not just the current user account) be able to decipher the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a><span data-ttu-id="b1827-120">X.509 证书</span><span class="sxs-lookup"><span data-stu-id="b1827-120">X.509 certificate</span></span>

<span data-ttu-id="b1827-121">如果应用程序分布在多台计算机，它可能是可以方便地在计算机间分发共享的 X.509 证书和配置托管的应用，以对密钥静态加密使用的证书：</span><span class="sxs-lookup"><span data-stu-id="b1827-121">If the app is spread across multiple machines, it may be convenient to distribute a shared X.509 certificate across the machines and configure the hosted apps to use the certificate for encryption of keys at rest:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

<span data-ttu-id="b1827-122">由于.NET Framework 的限制，支持仅使用 CAPI 私钥的证书。</span><span class="sxs-lookup"><span data-stu-id="b1827-122">Due to .NET Framework limitations, only certificates with CAPI private keys are supported.</span></span> <span data-ttu-id="b1827-123">请参阅以下内容的可能的解决方法，这些限制。</span><span class="sxs-lookup"><span data-stu-id="b1827-123">See the content below for possible workarounds to these limitations.</span></span>

::: moniker-end

## <a name="windows-dpapi-ng"></a><span data-ttu-id="b1827-124">Windows DPAPI-NG</span><span class="sxs-lookup"><span data-stu-id="b1827-124">Windows DPAPI-NG</span></span>

<span data-ttu-id="b1827-125">**此机制是仅在 Windows 8/Windows Server 2012 或更高版本上可用。**</span><span class="sxs-lookup"><span data-stu-id="b1827-125">**This mechanism is available only on Windows 8/Windows Server 2012 or later.**</span></span>

<span data-ttu-id="b1827-126">从 Windows 8 开始，Windows OS 支持 DPAPI NG （也称为 CNG DPAPI）。</span><span class="sxs-lookup"><span data-stu-id="b1827-126">Beginning with Windows 8, Windows OS supports DPAPI-NG (also called CNG DPAPI).</span></span> <span data-ttu-id="b1827-127">有关详细信息，请参阅[有关 CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi)。</span><span class="sxs-lookup"><span data-stu-id="b1827-127">For more information, see [About CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span></span>

<span data-ttu-id="b1827-128">主体被编码为保护描述符规则。</span><span class="sxs-lookup"><span data-stu-id="b1827-128">The principal is encoded as a protection descriptor rule.</span></span> <span data-ttu-id="b1827-129">在下面的示例调用[ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping)，只有指定的 SID 的已加入域的用户可以解密密钥环：</span><span class="sxs-lookup"><span data-stu-id="b1827-129">In the following example that calls [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), only the domain-joined user with the specified SID can decrypt the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="b1827-130">此外，还有的无参数重载`ProtectKeysWithDpapiNG`。</span><span class="sxs-lookup"><span data-stu-id="b1827-130">There's also a parameterless overload of `ProtectKeysWithDpapiNG`.</span></span> <span data-ttu-id="b1827-131">使用此便捷方法来指定规则"SID = {CURRENT_ACCOUNT_SID}"，其中*CURRENT_ACCOUNT_SID*是当前 Windows 用户帐户的 SID:</span><span class="sxs-lookup"><span data-stu-id="b1827-131">Use this convenience method to specify the rule "SID={CURRENT_ACCOUNT_SID}", where *CURRENT_ACCOUNT_SID* is the SID of the current Windows user account:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

<span data-ttu-id="b1827-132">在此方案中，AD 域控制器负责分发 DPAPI NG 操作所用的加密密钥。</span><span class="sxs-lookup"><span data-stu-id="b1827-132">In this scenario, the AD domain controller is responsible for distributing the encryption keys used by the DPAPI-NG operations.</span></span> <span data-ttu-id="b1827-133">目标用户可以解密加密的负载从任何加入域的计算机 （前提是该进程的标识来运行）。</span><span class="sxs-lookup"><span data-stu-id="b1827-133">The target user can decipher the encrypted payload from any domain-joined machine (provided that the process is running under their identity).</span></span>

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a><span data-ttu-id="b1827-134">基于证书的加密使用 Windows DPAPI NG</span><span class="sxs-lookup"><span data-stu-id="b1827-134">Certificate-based encryption with Windows DPAPI-NG</span></span>

<span data-ttu-id="b1827-135">如果应用程序运行在 Windows 8.1 / Windows Server 2012 R2 或更高版本，可以使用 Windows DPAPI NG 进行基于证书的加密。</span><span class="sxs-lookup"><span data-stu-id="b1827-135">If the app is running on Windows 8.1/Windows Server 2012 R2 or later, you can use Windows DPAPI-NG to perform certificate-based encryption.</span></span> <span data-ttu-id="b1827-136">使用规则描述符字符串"证书 = HashId:THUMBPRINT"，其中*指纹*是十六进制编码的 SHA1 指纹的证书：</span><span class="sxs-lookup"><span data-stu-id="b1827-136">Use the rule descriptor string "CERTIFICATE=HashId:THUMBPRINT", where *THUMBPRINT* is the hex-encoded SHA1 thumbprint of the certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="b1827-137">指向此存储库的任何应用程序必须运行 Windows 8.1 / Windows Server 2012 R2 或更高版本才能解开密钥。</span><span class="sxs-lookup"><span data-stu-id="b1827-137">Any app pointed at this repository must be running on Windows 8.1/Windows Server 2012 R2 or later to decipher the keys.</span></span>

## <a name="custom-key-encryption"></a><span data-ttu-id="b1827-138">自定义密钥加密</span><span class="sxs-lookup"><span data-stu-id="b1827-138">Custom key encryption</span></span>

<span data-ttu-id="b1827-139">如果不适当的内置机制，开发人员可以通过提供自定义指定自己的密钥加密机制[IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)。</span><span class="sxs-lookup"><span data-stu-id="b1827-139">If the in-box mechanisms aren't appropriate, the developer can specify their own key encryption mechanism by providing a custom [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).</span></span>
