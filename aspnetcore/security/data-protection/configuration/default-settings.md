---
title: ASP.NET Core 中的数据保护密钥管理和生存期信息
author: rick-anderson
description: 了解 ASP.NET Core 中的数据保护密钥管理和生存期。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: 1303c5c2c993f1d20383457666aebfa2a583e938
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053002"
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a><span data-ttu-id="1e1c2-103">ASP.NET Core 中的数据保护密钥管理和生存期信息</span><span class="sxs-lookup"><span data-stu-id="1e1c2-103">Data Protection key management and lifetime in ASP.NET Core</span></span>

<span data-ttu-id="1e1c2-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1e1c2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="key-management"></a><span data-ttu-id="1e1c2-105">密钥管理</span><span class="sxs-lookup"><span data-stu-id="1e1c2-105">Key management</span></span>

<span data-ttu-id="1e1c2-106">应用会尝试检测其操作环境并自行处理密钥配置。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-106">The app attempts to detect its operational environment and handle key configuration on its own.</span></span>

1. <span data-ttu-id="1e1c2-107">如果应用托管在 [Azure 应用](https://azure.microsoft.com/services/app-service/)中，则密钥将保留在 *%HOME%\ASP.NET\DataProtection-Keys* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-107">If the app is hosted in [Azure Apps](https://azure.microsoft.com/services/app-service/), keys are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="1e1c2-108">此文件夹由网络存储提供支持，并跨托管应用的所有计算机同步。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-108">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span>
   * <span data-ttu-id="1e1c2-109">密钥不是静态保护的。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-109">Keys aren't protected at rest.</span></span>
   * <span data-ttu-id="1e1c2-110">*DataProtection* 文件夹在单个部署槽位中向应用的所有实例提供密钥环。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-110">The *DataProtection-Keys* folder supplies the key ring to all instances of an app in a single deployment slot.</span></span>
   * <span data-ttu-id="1e1c2-111">各部署槽位（例如过渡槽和生成槽）不共享密钥环。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-111">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span> <span data-ttu-id="1e1c2-112">当你在部署槽之间进行交换时（例如，将暂存交换到生产环境或使用 A/B 测试），任何使用数据保护的应用都将无法使用上一个槽内的密钥环来解密存储的数据。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-112">When you swap between deployment slots, for example swapping Staging to Production or using A/B testing, any app using Data Protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="1e1c2-113">这会导致用户注销使用标准 ASP.NET Core cookie 身份验证的应用，因为它使用数据保护来保护它 cookie 。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-113">This leads to users being logged out of an app that uses the standard ASP.NET Core cookie authentication, as it uses Data Protection to protect its cookies.</span></span> <span data-ttu-id="1e1c2-114">如果需要与槽无关的密钥环，请使用外部密钥环形提供程序，例如 Azure Blob 存储、Azure Key Vault、SQL 存储或 Redis 缓存。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-114">If you desire slot-independent key rings, use an external key ring provider, such as Azure Blob Storage, Azure Key Vault, a SQL store, or Redis cache.</span></span>

1. <span data-ttu-id="1e1c2-115">如果用户配置文件可用，则密钥将保留在 *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-115">If the user profile is available, keys are persisted to the *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="1e1c2-116">如果操作系统为 Windows，则使用 DPAPI 对密钥进行静态加密。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-116">If the operating system is Windows, the keys are encrypted at rest using DPAPI.</span></span>

   <span data-ttu-id="1e1c2-117">同时还必须启用应用池的 [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-117">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="1e1c2-118">`setProfileEnvironment` 的默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-118">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="1e1c2-119">在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-119">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="1e1c2-120">如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1e1c2-120">If keys aren't stored in the user profile directory as expected:</span></span>

   1. <span data-ttu-id="1e1c2-121">导航到 %windir%/system32/inetsrv/config 文件夹。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-121">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
   1. <span data-ttu-id="1e1c2-122">打开 applicationHost.config 文件。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-122">Open the *applicationHost.config* file.</span></span>
   1. <span data-ttu-id="1e1c2-123">查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-123">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
   1. <span data-ttu-id="1e1c2-124">确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-124">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

1. <span data-ttu-id="1e1c2-125">如果应用托管在 IIS 中，则密钥将保留在仅列入工作进程帐户的特殊注册表项中的 HKLM 注册表中。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-125">If the app is hosted in IIS, keys are persisted to the HKLM registry in a special registry key that's ACLed only to the worker process account.</span></span> <span data-ttu-id="1e1c2-126">使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-126">Keys are encrypted at rest using DPAPI.</span></span>

1. <span data-ttu-id="1e1c2-127">如果这些条件都不匹配，则不会在当前进程的外部保留键。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-127">If none of these conditions match, keys aren't persisted outside of the current process.</span></span> <span data-ttu-id="1e1c2-128">当进程关闭时，所有生成的密钥都将丢失。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-128">When the process shuts down, all generated keys are lost.</span></span>

<span data-ttu-id="1e1c2-129">开发人员始终处于完全控制下，并且可以重写存储键的方式和位置。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-129">The developer is always in full control and can override how and where keys are stored.</span></span> <span data-ttu-id="1e1c2-130">上述前三个选项应为大多数应用提供良好的默认设置，这与 ASP.NET **\<machineKey>** 自动生成例程过去的工作方式类似。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-130">The first three options above should provide good defaults for most apps similar to how the ASP.NET **\<machineKey>** auto-generation routines worked in the past.</span></span> <span data-ttu-id="1e1c2-131">最终的回退选项是要求开发人员在需要进行密钥持久性的情况下预先指定 [配置](xref:security/data-protection/configuration/overview) 的唯一方案，但只有在极少数情况下才会发生此回退。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-131">The final, fallback option is the only scenario that requires the developer to specify [configuration](xref:security/data-protection/configuration/overview) upfront if they want key persistence, but this fallback only occurs in rare situations.</span></span>

<span data-ttu-id="1e1c2-132">在 Docker 容器中托管时，密钥应保留在一个文件夹中，该文件夹 (共享卷或主机装载的卷，该卷会超出容器生存期) 或外部提供程序（如 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 或 [Redis](https://redis.io/)）。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-132">When hosting in a Docker container, keys should be persisted in a folder that's a Docker volume (a shared volume or a host-mounted volume that persists beyond the container's lifetime) or in an external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span> <span data-ttu-id="1e1c2-133">如果应用无法访问共享的网络卷，则外部提供程序也可用于 web 场方案 (请参阅 [PersistKeysToFileSystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) 了解详细信息) 。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-133">An external provider is also useful in web farm scenarios if apps can't access a shared network volume (see [PersistKeysToFileSystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) for more information).</span></span>

> [!WARNING]
> <span data-ttu-id="1e1c2-134">如果开发人员覆盖上述规则，并在特定的密钥存储库中指向数据保护系统，将禁用静态密钥的自动加密。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-134">If the developer overrides the rules outlined above and points the Data Protection system at a specific key repository, automatic encryption of keys at rest is disabled.</span></span> <span data-ttu-id="1e1c2-135">可以通过 [配置](xref:security/data-protection/configuration/overview)重新启用静态保护。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-135">At-rest protection can be re-enabled via [configuration](xref:security/data-protection/configuration/overview).</span></span>

## <a name="key-lifetime"></a><span data-ttu-id="1e1c2-136">密钥生存期</span><span class="sxs-lookup"><span data-stu-id="1e1c2-136">Key lifetime</span></span>

<span data-ttu-id="1e1c2-137">默认情况下，密钥的生存期为90天。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-137">Keys have a 90-day lifetime by default.</span></span> <span data-ttu-id="1e1c2-138">密钥过期后，应用会自动生成新密钥，并将新密钥设置为活动密钥。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-138">When a key expires, the app automatically generates a new key and sets the new key as the active key.</span></span> <span data-ttu-id="1e1c2-139">只要已停用的密钥仍保留在系统中，你的应用就可以对任何受其保护的数据进行解密。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-139">As long as retired keys remain on the system, your app can decrypt any data protected with them.</span></span> <span data-ttu-id="1e1c2-140">有关详细信息，请参阅 [密钥管理](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) 。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-140">See [key management](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) for more information.</span></span>

## <a name="default-algorithms"></a><span data-ttu-id="1e1c2-141">默认算法</span><span class="sxs-lookup"><span data-stu-id="1e1c2-141">Default algorithms</span></span>

<span data-ttu-id="1e1c2-142">使用的默认负载保护算法为 AES-256-CBC，以实现保密性，使用 HMACSHA256 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-142">The default payload protection algorithm used is AES-256-CBC for confidentiality and HMACSHA256 for authenticity.</span></span> <span data-ttu-id="1e1c2-143">512位主密钥（每90天更改一次）用于根据每个有效负载派生用于这些算法的两个子项。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-143">A 512-bit master key, changed every 90 days, is used to derive the two sub-keys used for these algorithms on a per-payload basis.</span></span> <span data-ttu-id="1e1c2-144">有关详细信息，请参阅 [子项派生](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) 。</span><span class="sxs-lookup"><span data-stu-id="1e1c2-144">See [subkey derivation](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1e1c2-145">其他资源</span><span class="sxs-lookup"><span data-stu-id="1e1c2-145">Additional resources</span></span>

* <xref:security/data-protection/extensibility/key-management>
* <xref:host-and-deploy/web-farm>
