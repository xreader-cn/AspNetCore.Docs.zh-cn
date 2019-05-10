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
# <a name="key-encryption-at-rest-in-aspnet-core"></a>在 ASP.NET Core 中存放的密钥加密

数据保护系统[默认情况下使用的发现机制](xref:security/data-protection/configuration/default-settings)来确定如何加密密钥应静态加密。 开发人员可以重写的发现机制，并手动指定如何静态加密密钥。

> [!WARNING]
> 如果指定的显式[密钥暂留位置](xref:security/data-protection/implementation/key-storage-providers)，数据保护系统注销 rest 机制在默认密钥加密。 因此，无法再进行静态加密密钥。 我们建议您[指定一个显式的密钥加密机制](xref:security/data-protection/implementation/key-encryption-at-rest)对于生产部署。 本主题中介绍的静态加密机制选项。

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a>Azure Key Vault

若要将密钥存储在[Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)，配置与系统[ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault)中`Startup`类：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

有关详细信息，请参阅[配置 ASP.NET Core 数据保护：ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault)。

::: moniker-end

## <a name="windows-dpapi"></a>Windows DPAPI

**仅适用于 Windows 的部署。**

使用 Windows DPAPI 时，使用加密密钥材料[CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata)之前保存到存储。 DPAPI 是永远不会读取当前计算机之外的数据的适当的加密机制 (不过可以备份到 Active Directory 这些密钥; 请参阅[DPAPI 和漫游配置文件](https://support.microsoft.com/kb/309408/#6))。 若要配置 DPAPI 密钥静态加密，请调用之一[ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi)扩展方法：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

如果`ProtectKeysWithDpapi`称为不带任何参数，仅在当前 Windows 用户帐户，才能解密持久化的密钥环。 您可以选择指定的计算机 （而不仅仅是当前用户帐户） 上的任何用户帐户能够解密密钥环：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a>X.509 证书

如果应用程序分布在多台计算机，它可能是可以方便地在计算机间分发共享的 X.509 证书和配置托管的应用，以对密钥静态加密使用的证书：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

由于.NET Framework 的限制，支持仅使用 CAPI 私钥的证书。 请参阅以下内容的可能的解决方法，这些限制。

::: moniker-end

## <a name="windows-dpapi-ng"></a>Windows DPAPI-NG

**此机制是仅在 Windows 8/Windows Server 2012 或更高版本上可用。**

从 Windows 8 开始，Windows OS 支持 DPAPI NG （也称为 CNG DPAPI）。 有关详细信息，请参阅[有关 CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi)。

主体被编码为保护描述符规则。 在下面的示例调用[ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping)，只有指定的 SID 的已加入域的用户可以解密密钥环：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

此外，还有的无参数重载`ProtectKeysWithDpapiNG`。 使用此便捷方法来指定规则"SID = {CURRENT_ACCOUNT_SID}"，其中*CURRENT_ACCOUNT_SID*是当前 Windows 用户帐户的 SID:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

在此方案中，AD 域控制器负责分发 DPAPI NG 操作所用的加密密钥。 目标用户可以解密加密的负载从任何加入域的计算机 （前提是该进程的标识来运行）。

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a>基于证书的加密使用 Windows DPAPI NG

如果应用程序运行在 Windows 8.1 / Windows Server 2012 R2 或更高版本，可以使用 Windows DPAPI NG 进行基于证书的加密。 使用规则描述符字符串"证书 = HashId:THUMBPRINT"，其中*指纹*是十六进制编码的 SHA1 指纹的证书：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

指向此存储库的任何应用程序必须运行 Windows 8.1 / Windows Server 2012 R2 或更高版本才能解开密钥。

## <a name="custom-key-encryption"></a>自定义密钥加密

如果不适当的内置机制，开发人员可以通过提供自定义指定自己的密钥加密机制[IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)。
