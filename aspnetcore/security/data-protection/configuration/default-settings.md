---
title: 数据保护密钥管理和 ASP.NET Core 中的生存期
author: rick-anderson
description: 了解有关数据保护密钥管理和 ASP.NET Core 中的生存期。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: 2f022a4c7519485fe629ce47c27d214c8c27d5bc
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897274"
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a>数据保护密钥管理和 ASP.NET Core 中的生存期

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="key-management"></a>密钥管理

是应用程序尝试检测其操作环境和处理自己的密钥配置。

1. 如果应用托管在[Azure 应用](https://azure.microsoft.com/services/app-service/)，密钥保存到 *%HOME%\ASP.NET\DataProtection-Keys*文件夹。 此文件夹由网络存储提供支持，并跨托管应用的所有计算机同步。
   * 密钥不是静态保护的。
   * *DataProtection 密钥*文件夹提供密钥环在单个部署槽位中应用的所有实例。
   * 各部署槽位（例如过渡槽和生成槽）不共享密钥环。 在交换之间部署槽，如交换到生产环境的过渡环境或使用 A / B 测试，使用数据保护的任何应用将无法解密使用之前槽位中的密钥环存储的数据。 这会导致用户正在注销的应用程序使用标准的 ASP.NET Core cookie 身份验证，因为它使用数据保护来保护其 cookie。 如果你需要独立于槽位的密钥环，则使用外部密钥环提供程序，例如 Azure Blob 存储、 Azure 密钥保管库，SQL 存储区中，或 Redis 缓存。

1. 如果用户配置文件不可用，将密钥保存到 *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys*文件夹。 如果操作系统是 Windows，静态使用 DPAPI 加密密钥。

   同时还必须启用应用池的 [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。 `setProfileEnvironment` 的默认值为 `true`。 在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。 如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：

   1. 导航到 %windir%/system32/inetsrv/config 文件夹。
   1. 打开 applicationHost.config 文件。
   1. 查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。
   1. 确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。

1. 如果应用托管在 IIS 中，密钥保留到 HKLM 注册表中特殊的注册表项的列仅对工作进程帐户。 使用 DPAPI 对密钥静态加密。

1. 如果没有这些条件匹配，不保留当前进程之外的密钥。 进程关闭时，生成所有密钥都都将丢失。

开发人员始终完全控制，并且可重写方式和存储密钥的位置。 上面的前三个选项应提供合理的默认值对于大多数应用程序类似于如何在 ASP.NET  **\<machineKey >** 过去的工作自动生成例程。 最终的回退选项是要求开发人员指定的唯一情形[配置](xref:security/data-protection/configuration/overview)前期如果他们想要密钥持久性，但在极少数情况下才会出现以上后备机制。

在托管在 Docker 容器中，应是 （共享的卷或仍然存在超出容器的生存期的主机装入的卷） 的 Docker 卷的文件夹中保存密钥或在外部提供程序，如[Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)或[Redis](https://redis.io/)。 如果应用无法访问共享的网络卷，外部提供程序也是可在 web 场方案中 (请参阅[PersistKeysToFileSystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem)有关详细信息)。

> [!WARNING]
> 如果开发人员重写上面所述的规则和点处为特定的密钥存储库的数据保护系统，则禁用自动加密静态密钥。 静态保护，可以通过重新启用[配置](xref:security/data-protection/configuration/overview)。

## <a name="key-lifetime"></a>密钥生存期

默认情况下，密钥的 90 天生存期。 密钥过期后，应用将自动生成新的密钥，并将新的密钥设置为活动密钥。 只要已停用的密钥保留在系统中，您的应用程序可以解密保护与他们的任何数据。 请参阅[密钥管理](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)有关详细信息。

## <a name="default-algorithms"></a>默认算法

使用的默认负载保护算法是 AES-256-CBC 保密性和 HMACSHA256 的真实性。 512 位主密钥，每隔 90 天更改一次用于派生两个用于在每个有效负载的基础上这些算法的子键。 请参阅[子项派生](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation)有关详细信息。

## <a name="additional-resources"></a>其他资源

* <xref:security/data-protection/extensibility/key-management>
* <xref:host-and-deploy/web-farm>
