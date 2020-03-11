---
title: 在 ASP.NET Core 中支持的数据保护计算机范围的策略
author: rick-anderson
description: 了解有关设置默认计算机范围策略对于使用 ASP.NET Core 数据保护的所有应用程序的支持。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: 70aaca7afcd3df22cebb4466fbd9845a2277688c
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655062"
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a>在 ASP.NET Core 中支持的数据保护计算机范围的策略

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

Windows 上运行时，数据保护系统具有有限的支持设置默认计算机范围的所有应用程序使用 ASP.NET Core 数据保护策略。 通常，管理员可能希望更改默认设置（例如使用的算法或密钥生存期），而无需手动更新计算机上的每个应用。

> [!WARNING]
> 系统管理员可以设置默认策略，但不能强制实施它。 应用开发人员始终可以通过自己选择的任意值覆盖任何值。 默认策略仅影响开发人员未指定设置的显式值的应用。

## <a name="setting-default-policy"></a>设置默认策略

若要设置默认策略，管理员可在以下注册表项下的系统注册表中设置已知值：

**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**

如果你使用的是64位的操作系统，并且想要影响32位应用的行为，请记住配置 Wow6432Node 的等效项。

下面显示了支持的值。

| 值              | 类型   | 说明 |
| ------------------ | :----: | ----------- |
| EncryptionType     | 字符串 | 指定应对哪些算法使用数据保护。 该值必须是 CNG-CBC、CNG-GCM 或托管的，下面将对此进行更详细的介绍。 |
| DefaultKeyLifetime | DWORD  | 指定新生成的密钥的生存期。 值以天为单位指定，并且必须 > = 7。 |
| KeyEscrowSinks     | 字符串 | 指定用于密钥委托的类型。 该值是一个以分号分隔的密钥托管接收器列表，其中，列表中的每个元素都是实现[IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink)的类型的程序集限定名称。 |

## <a name="encryption-types"></a>加密类型

如果 EncryptionType 是 CNG-CBC，系统会将系统配置为使用 CBC 模式的对称块密码来实现保密性，使用 Windows CNG 提供的服务进行真品验证（有关详细信息，请参阅[指定自定义 WINDOWS cng 算法](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms)）。 支持以下附加值，其中每个值对应于 CngCbcAuthenticatedEncryptionSettings 类型上的一个属性。

| 值                       | 类型   | 说明 |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | 字符串 | CNG 理解的对称块加密算法的名称。 此算法在 CBC 模式下打开。 |
| EncryptionAlgorithmProvider | 字符串 | 可生成算法 EncryptionAlgorithm 的 CNG 提供程序实现的名称。 |
| EncryptionAlgorithmKeySize  | DWORD  | 用于派生对称块加密算法的密钥长度（以位为单位）。 |
| HashAlgorithm               | 字符串 | CNG 理解的哈希算法的名称。 此算法在 HMAC 模式下打开。 |
| HashAlgorithmProvider       | 字符串 | 可生成算法 HashAlgorithm 的 CNG 提供程序实现的名称。 |

如果 EncryptionType 是 CNG-GCM，系统会将系统配置为使用 Galois/Counter 模式对称块密码，以获得 Windows CNG 提供的服务的机密性和真实性（有关详细信息，请参阅[指定自定义 WINDOWS cng 算法](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms)）。 支持以下附加值，其中每个值对应于 CngGcmAuthenticatedEncryptionSettings 类型上的一个属性。

| 值                       | 类型   | 说明 |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | 字符串 | CNG 理解的对称块加密算法的名称。 此算法在 Galois/Counter 模式下打开。 |
| EncryptionAlgorithmProvider | 字符串 | 可生成算法 EncryptionAlgorithm 的 CNG 提供程序实现的名称。 |
| EncryptionAlgorithmKeySize  | DWORD  | 用于派生对称块加密算法的密钥长度（以位为单位）。 |

如果 EncryptionType 是托管的，则系统会将系统配置为使用托管的 System.security.cryptography.symmetricalgorithm 作为保密性，并使用 KeyedHashAlgorithm 作为真实性（有关详细信息，请参阅[指定自定义托管算法](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms)）。 支持以下附加值，其中每个值对应于 ManagedAuthenticatedEncryptionSettings 类型上的一个属性。

| 值                      | 类型   | 说明 |
| -------------------------- | :----: | ----------- |
| EncryptionAlgorithmType    | 字符串 | 实现 System.security.cryptography.symmetricalgorithm 的类型的程序集限定名称。 |
| EncryptionAlgorithmKeySize | DWORD  | 要为对称加密算法派生的密钥的长度（位）。 |
| ValidationAlgorithmType    | 字符串 | 实现 KeyedHashAlgorithm 的类型的程序集限定名称。 |

如果 EncryptionType 的其他任何值均不为 null 或为空，则数据保护系统将在启动时引发异常。

> [!WARNING]
> 配置涉及类型名称（EncryptionAlgorithmType、ValidationAlgorithmType、KeyEscrowSinks）的默认策略设置时，这些类型必须可用于应用。 这意味着，对于在桌面 CLR 上运行的应用程序，包含这些类型的程序集应存在于全局程序集缓存（GAC）中。 对于在.NET Core 上运行的 ASP.NET Core 应用程序，应安装包含这些类型的包。
