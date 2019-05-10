---
title: 在 ASP.NET Core 中支持的数据保护计算机范围的策略
author: rick-anderson
description: 了解有关设置默认计算机范围策略对于使用 ASP.NET Core 数据保护的所有应用程序的支持。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: 70aaca7afcd3df22cebb4466fbd9845a2277688c
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897264"
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a>在 ASP.NET Core 中支持的数据保护计算机范围的策略

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

Windows 上运行时，数据保护系统具有有限的支持设置默认计算机范围的所有应用程序使用 ASP.NET Core 数据保护策略。 一般理念是管理员可能想要更改默认设置，如使用的算法或密钥生存期，而无需手动更新每个应用程序，在计算机上。

> [!WARNING]
> 系统管理员可以设置默认策略，但它们不能强制使用它。 应用程序开发人员始终可以重写其中一个其自己选择的任何值。 默认策略仅影响应用程序开发人员未指定显式值的设置的位置。

## <a name="setting-default-policy"></a>设置默认策略

若要设置默认策略，管理员可以设置以下注册表项下在系统注册表中的已知的值：

**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**

如果您是 64 位操作系统上，并想要产生效果的 32 位应用程序的行为，请记住配置 Wow6432Node 等效于上面的项。

支持的值如下所示。

| 值              | 类型   | 描述 |
| ------------------ | :----: | ----------- |
| EncryptionType     | string | 指定的算法都应使用的数据保护。 值必须为 CNG CBC、 CNG GCM 或托管和下面更详细地介绍。 |
| DefaultKeyLifetime | DWORD  | 指定新生成的键的生存期。 此值天为单位指定，并且必须是 > = 7。 |
| KeyEscrowSinks     | string | 指定用于密钥托管的类型。 值是以分号分隔的密钥托管接收器，其中在列表中的每个元素都实现的类型的程序集限定名称列表[IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink)。 |

## <a name="encryption-types"></a>加密类型

如果 EncryptionType 为 CNG CBC，系统配置为使用 CBC 模式下对称块密码的保密性和 HMAC 的真实性与 Windows CNG 提供的服务 (请参阅[指定自定义 Windows CNG 算法](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms)为更多详细信息）。 支持以下其他值，其中每个对应于 CngCbcAuthenticatedEncryptionSettings 类型上的属性。

| 值                       | 类型   | 描述 |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | string | 理解 CNG 的对称块加密算法的名称。 此算法是在 CBC 模式下打开。 |
| EncryptionAlgorithmProvider | string | 可以生成 EncryptionAlgorithm 的算法的 CNG 提供程序实现的名称。 |
| EncryptionAlgorithmKeySize  | DWORD  | 要导出的对称块加密算法的密钥长度 （以位为单位）。 |
| HashAlgorithm               | string | 理解的 CNG 的哈希算法的名称。 此算法是在 HMAC 模式下打开。 |
| HashAlgorithmProvider       | string | 可以生成算法的 HashAlgorithm CNG 提供程序实现的名称。 |

如果 EncryptionType 为 CNG GCM，系统配置为使用的保密性和真实性 Galois/计数器模式对称块密码，与 Windows CNG 提供的服务 (请参阅[指定自定义 Windows CNG 算法](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms)有关详细信息）。 支持以下其他值，其中每个对应于 CngGcmAuthenticatedEncryptionSettings 类型上的属性。

| 值                       | 类型   | 描述 |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | string | 理解 CNG 的对称块加密算法的名称。 此算法是在 Galois/计数器模式中打开。 |
| EncryptionAlgorithmProvider | string | 可以生成 EncryptionAlgorithm 的算法的 CNG 提供程序实现的名称。 |
| EncryptionAlgorithmKeySize  | DWORD  | 要导出的对称块加密算法的密钥长度 （以位为单位）。 |

如果管理 EncryptionType，系统配置为用于托管的 SymmetricAlgorithm 保密性和 KeyedHashAlgorithm 的真实性 (请参阅[指定自定义托管算法](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms)的更多详细信息)。 支持以下其他值，其中每个对应于 ManagedAuthenticatedEncryptionSettings 类型上的属性。

| 值                      | 类型   | 描述 |
| -------------------------- | :----: | ----------- |
| EncryptionAlgorithmType    | string | 实现 SymmetricAlgorithm 的类型的程序集限定名称。 |
| EncryptionAlgorithmKeySize | DWORD  | 要导出的对称加密算法的密钥长度 （以位为单位）。 |
| ValidationAlgorithmType    | string | 实现 KeyedHashAlgorithm 的类型的程序集限定名称。 |

如果 EncryptionType 具有任何其他值不是 null 或为空时，数据保护系统在启动时引发异常。

> [!WARNING]
> 在配置时涉及的类型名称 （EncryptionAlgorithmType，ValidationAlgorithmType，KeyEscrowSinks） 的默认策略设置，类型必须可供该应用程序。 这意味着，对于在桌面 CLR 上运行的应用程序，包含这些类型的程序集应在全局程序集缓存 (GAC) 中存在。 对于在.NET Core 上运行的 ASP.NET Core 应用程序，应安装包含这些类型的包。
