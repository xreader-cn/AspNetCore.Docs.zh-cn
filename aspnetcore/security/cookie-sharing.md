---
title: :::no-loc(cookie):::在 ASP.NET 应用之间共享身份验证
author: rick-anderson
description: '了解如何 :::no-loc(cookie)::: 在 ASP.NET 4.x 和 ASP.NET Core 应用之间共享身份验证。'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/:::no-loc(cookie):::-sharing
ms.openlocfilehash: 8f54f2e4894328f8471d5f80c8184839ce47add6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059684"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a><span data-ttu-id="eed2c-103">:::no-loc(cookie):::在 ASP.NET 应用之间共享身份验证</span><span class="sxs-lookup"><span data-stu-id="eed2c-103">Share authentication :::no-loc(cookie):::s among ASP.NET apps</span></span>

<span data-ttu-id="eed2c-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="eed2c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="eed2c-105">网站通常由单独的 web 应用组成。</span><span class="sxs-lookup"><span data-stu-id="eed2c-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="eed2c-106">若要 (SSO) 体验提供单一登录，站点内的 web 应用必须共享身份验证 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="eed2c-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication :::no-loc(cookie):::s.</span></span> <span data-ttu-id="eed2c-107">为了支持此方案，数据保护堆栈允许共享 Katana :::no-loc(cookie)::: authentication 和 ASP.NET Core :::no-loc(cookie)::: 身份验证票证。</span><span class="sxs-lookup"><span data-stu-id="eed2c-107">To support this scenario, the data protection stack allows sharing Katana :::no-loc(cookie)::: authentication and ASP.NET Core :::no-loc(cookie)::: authentication tickets.</span></span>

<span data-ttu-id="eed2c-108">在下面的示例中：</span><span class="sxs-lookup"><span data-stu-id="eed2c-108">In the examples that follow:</span></span>

* <span data-ttu-id="eed2c-109">身份验证 :::no-loc(cookie)::: 名称设置为的公共值 `.AspNet.Shared:::no-loc(Cookie):::` 。</span><span class="sxs-lookup"><span data-stu-id="eed2c-109">The authentication :::no-loc(cookie)::: name is set to a common value of `.AspNet.Shared:::no-loc(Cookie):::`.</span></span>
* <span data-ttu-id="eed2c-110">`AuthenticationType`设置为 `:::no-loc(Identity):::.Application` 显式或默认设置为。</span><span class="sxs-lookup"><span data-stu-id="eed2c-110">The `AuthenticationType` is set to `:::no-loc(Identity):::.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="eed2c-111">常见的应用名称用于使数据保护系统 () 共享数据保护密钥 `Shared:::no-loc(Cookie):::App` 。</span><span class="sxs-lookup"><span data-stu-id="eed2c-111">A common app name is used to enable the data protection system to share data protection keys (`Shared:::no-loc(Cookie):::App`).</span></span>
* <span data-ttu-id="eed2c-112">`:::no-loc(Identity):::.Application` 用作身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="eed2c-112">`:::no-loc(Identity):::.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="eed2c-113">无论使用哪种方案，都必须在共享应用 *内和* 共享应用中以一致的方式使用它， :::no-loc(cookie)::: 或者通过显式设置。</span><span class="sxs-lookup"><span data-stu-id="eed2c-113">Whatever scheme is used, it must be used consistently *within and across* the shared :::no-loc(cookie)::: apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="eed2c-114">加密和解密时将使用该方案 :::no-loc(cookie)::: ，因此必须在应用间使用一致的方案。</span><span class="sxs-lookup"><span data-stu-id="eed2c-114">The scheme is used when encrypting and decrypting :::no-loc(cookie):::s, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="eed2c-115">使用通用的 [数据保护密钥](xref:security/data-protection/implementation/key-management) 存储位置。</span><span class="sxs-lookup"><span data-stu-id="eed2c-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="eed2c-116">在 ASP.NET Core 应用中， <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 用于设置密钥存储位置。</span><span class="sxs-lookup"><span data-stu-id="eed2c-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="eed2c-117">在 .NET Framework 应用中， :::no-loc(Cookie)::: 身份验证中间件使用的实现 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> 。</span><span class="sxs-lookup"><span data-stu-id="eed2c-117">In .NET Framework apps, :::no-loc(Cookie)::: Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="eed2c-118">`DataProtectionProvider` 为身份验证负载数据的加密和解密提供数据保护服务 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="eed2c-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication :::no-loc(cookie)::: payload data.</span></span> <span data-ttu-id="eed2c-119">该 `DataProtectionProvider` 实例与应用程序的其他部分所使用的数据保护系统隔离。</span><span class="sxs-lookup"><span data-stu-id="eed2c-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="eed2c-120">[DataProtectionProvider (DirectoryInfo，Action \<IDataProtectionBuilder>) ](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) 接受 <xref:System.IO.DirectoryInfo> 来指定数据保护密钥存储的位置。</span><span class="sxs-lookup"><span data-stu-id="eed2c-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="eed2c-121">`DataProtectionProvider` 需要 DataProtection NuGet 包 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) ：</span><span class="sxs-lookup"><span data-stu-id="eed2c-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="eed2c-122">在 ASP.NET Core 1.x 应用程序中，请参阅 [AspNetCore 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="eed2c-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="eed2c-123">在 .NET Framework 应用中，将包引用添加到 [AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)。</span><span class="sxs-lookup"><span data-stu-id="eed2c-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="eed2c-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 设置常见的应用名称。</span><span class="sxs-lookup"><span data-stu-id="eed2c-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a><span data-ttu-id="eed2c-125">与共享身份验证 :::no-loc(cookie)::::::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="eed2c-125">Share authentication :::no-loc(cookie):::s with :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="eed2c-126">使用 :::no-loc(ASP.NET Core Identity)::: 时：</span><span class="sxs-lookup"><span data-stu-id="eed2c-126">When using :::no-loc(ASP.NET Core Identity)::::</span></span>

* <span data-ttu-id="eed2c-127">数据保护密钥和应用程序名称必须在应用之间共享。</span><span class="sxs-lookup"><span data-stu-id="eed2c-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="eed2c-128">在以下示例中，将向方法提供一个公共密钥存储位置 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 。</span><span class="sxs-lookup"><span data-stu-id="eed2c-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="eed2c-129"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>在以下示例中，使用配置公共共享应用名称 (`Shared:::no-loc(Cookie):::App`) 。</span><span class="sxs-lookup"><span data-stu-id="eed2c-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`Shared:::no-loc(Cookie):::App` in the following examples).</span></span> <span data-ttu-id="eed2c-130">有关详细信息，请参阅 <xref:security/data-protection/configuration/overview>。</span><span class="sxs-lookup"><span data-stu-id="eed2c-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="eed2c-131">使用 <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.ConfigureApplication:::no-loc(Cookie):::*> 扩展方法为设置数据保护服务 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="eed2c-131">Use the <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.ConfigureApplication:::no-loc(Cookie):::*> extension method to set up the data protection service for :::no-loc(cookie):::s.</span></span>
* <span data-ttu-id="eed2c-132">默认的身份验证类型为 `:::no-loc(Identity):::.Application` 。</span><span class="sxs-lookup"><span data-stu-id="eed2c-132">The default authentication type is `:::no-loc(Identity):::.Application`.</span></span>

<span data-ttu-id="eed2c-133">在 `Startup.ConfigureServices`中：</span><span class="sxs-lookup"><span data-stu-id="eed2c-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("Shared:::no-loc(Cookie):::App");

services.ConfigureApplication:::no-loc(Cookie):::(options => {
    options.:::no-loc(Cookie):::.Name = ".AspNet.Shared:::no-loc(Cookie):::";
});
```

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a><span data-ttu-id="eed2c-134">共享身份 :::no-loc(cookie)::: 验证 :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="eed2c-134">Share authentication :::no-loc(cookie):::s without :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="eed2c-135">如果 :::no-loc(cookie)::: 直接使用，则 :::no-loc(ASP.NET Core Identity)::: 在中配置数据保护和身份验证 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="eed2c-135">When using :::no-loc(cookie):::s directly without :::no-loc(ASP.NET Core Identity):::, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eed2c-136">在下面的示例中，"身份验证类型" 设置为 `:::no-loc(Identity):::.Application` ：</span><span class="sxs-lookup"><span data-stu-id="eed2c-136">In the following example, the authentication type is set to `:::no-loc(Identity):::.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("Shared:::no-loc(Cookie):::App");

services.AddAuthentication(":::no-loc(Identity):::.Application")
    .Add:::no-loc(Cookie):::(":::no-loc(Identity):::.Application", options =>
    {
        options.:::no-loc(Cookie):::.Name = ".AspNet.Shared:::no-loc(Cookie):::";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a><span data-ttu-id="eed2c-137">:::no-loc(cookie):::跨不同的基路径共享</span><span class="sxs-lookup"><span data-stu-id="eed2c-137">Share :::no-loc(cookie):::s across different base paths</span></span>

<span data-ttu-id="eed2c-138">身份验证 :::no-loc(cookie)::: 使用[HttpRequest](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)作为其默认值[ :::no-loc(Cookie)::: 。路径](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Path)。</span><span class="sxs-lookup"><span data-stu-id="eed2c-138">An authentication :::no-loc(cookie)::: uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [:::no-loc(Cookie):::.Path](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Path).</span></span> <span data-ttu-id="eed2c-139">如果应用的 :::no-loc(cookie)::: 必须在不同的基路径间共享，则 `Path` 必须重写：</span><span class="sxs-lookup"><span data-stu-id="eed2c-139">If the app's :::no-loc(cookie)::: must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("Shared:::no-loc(Cookie):::App");

services.ConfigureApplication:::no-loc(Cookie):::(options => {
    options.:::no-loc(Cookie):::.Name = ".AspNet.Shared:::no-loc(Cookie):::";
    options.:::no-loc(Cookie):::.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a><span data-ttu-id="eed2c-140">:::no-loc(cookie):::跨子域共享</span><span class="sxs-lookup"><span data-stu-id="eed2c-140">Share :::no-loc(cookie):::s across subdomains</span></span>

<span data-ttu-id="eed2c-141">承载跨子域共享的应用时 :::no-loc(cookie)::: ，请在中指定一个公共域[ :::no-loc(Cookie)::: 。域](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Domain)属性。</span><span class="sxs-lookup"><span data-stu-id="eed2c-141">When hosting apps that share :::no-loc(cookie):::s across subdomains, specify a common domain in the [:::no-loc(Cookie):::.Domain](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Domain) property.</span></span> <span data-ttu-id="eed2c-142">若要 :::no-loc(cookie)::: 在上跨应用共享 `contoso.com` ，例如 `first_subdomain.contoso.com` 和 `second_subdomain.contoso.com` ，请将 `:::no-loc(Cookie):::.Domain` 指定 `.contoso.com` 为：</span><span class="sxs-lookup"><span data-stu-id="eed2c-142">To share :::no-loc(cookie):::s across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `:::no-loc(Cookie):::.Domain` as `.contoso.com`:</span></span>

```csharp
options.:::no-loc(Cookie):::.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="eed2c-143">加密静态数据保护密钥</span><span class="sxs-lookup"><span data-stu-id="eed2c-143">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="eed2c-144">对于生产部署，请将配置 `DataProtectionProvider` 为使用 DPAPI 或 X509Certificate 加密静态密钥。</span><span class="sxs-lookup"><span data-stu-id="eed2c-144">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="eed2c-145">有关详细信息，请参阅 <xref:security/data-protection/implementation/key-encryption-at-rest>。</span><span class="sxs-lookup"><span data-stu-id="eed2c-145">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="eed2c-146">在下面的示例中，提供了一个证书指纹来 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> 执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="eed2c-146">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="eed2c-147">:::no-loc(cookie):::在 ASP.NET 4.x 和 ASP.NET Core 应用之间共享身份验证</span><span class="sxs-lookup"><span data-stu-id="eed2c-147">Share authentication :::no-loc(cookie):::s between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="eed2c-148">使用 Katana Authentication 中间件的 ASP.NET 4.x 应用程序 :::no-loc(Cookie)::: 可以配置为生成 :::no-loc(cookie)::: 与 ASP.NET Core :::no-loc(Cookie)::: authentication 中间件兼容的身份验证。</span><span class="sxs-lookup"><span data-stu-id="eed2c-148">ASP.NET 4.x apps that use Katana :::no-loc(Cookie)::: Authentication Middleware can be configured to generate authentication :::no-loc(cookie):::s that are compatible with the ASP.NET Core :::no-loc(Cookie)::: Authentication Middleware.</span></span> <span data-ttu-id="eed2c-149">这允许在多个步骤中升级大型站点的单个应用，同时跨站点提供平稳的 SSO 体验。</span><span class="sxs-lookup"><span data-stu-id="eed2c-149">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="eed2c-150">当应用使用 Katana :::no-loc(Cookie)::: Authentication 中间件时，它会调用 `Use:::no-loc(Cookie):::Authentication` 项目的 *Startup.Auth.cs* 文件。</span><span class="sxs-lookup"><span data-stu-id="eed2c-150">When an app uses Katana :::no-loc(Cookie)::: Authentication Middleware, it calls `Use:::no-loc(Cookie):::Authentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="eed2c-151">使用 Visual Studio 2013 和更高版本创建的 ASP.NET 4.x web 应用项目在 :::no-loc(Cookie)::: 默认情况下使用 Katana Authentication 中间件。</span><span class="sxs-lookup"><span data-stu-id="eed2c-151">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana :::no-loc(Cookie)::: Authentication Middleware by default.</span></span> <span data-ttu-id="eed2c-152">尽管 `Use:::no-loc(Cookie):::Authentication` ASP.NET Core 应用已过时且不受支持，但 `Use:::no-loc(Cookie):::Authentication` 在使用 Katana Authentication 中间件的 ASP.NET 4.x 应用中调用 :::no-loc(Cookie)::: 是有效的。</span><span class="sxs-lookup"><span data-stu-id="eed2c-152">Although `Use:::no-loc(Cookie):::Authentication` is obsolete and unsupported for ASP.NET Core apps, calling `Use:::no-loc(Cookie):::Authentication` in an ASP.NET 4.x app that uses Katana :::no-loc(Cookie)::: Authentication Middleware is valid.</span></span>

<span data-ttu-id="eed2c-153">ASP.NET 4.x 应用必须面向 .NET Framework 4.5.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="eed2c-153">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="eed2c-154">否则，无法安装所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="eed2c-154">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="eed2c-155">若要 :::no-loc(cookie)::: 在 ASP.NET 4.x 应用和 ASP.NET Core 应用之间共享身份验证，请按在 [ :::no-loc(cookie)::: ASP.NET Core 应用间共享身份验证](#share-authentication-:::no-loc(cookie):::s-with-aspnet-core-identity) 中的说明配置 ASP.NET Core 应用，并按如下所示配置 ASP.NET 4.x 应用。</span><span class="sxs-lookup"><span data-stu-id="eed2c-155">To share authentication :::no-loc(cookie):::s between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication :::no-loc(cookie):::s among ASP.NET Core apps](#share-authentication-:::no-loc(cookie):::s-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="eed2c-156">确认应用的包已更新到最新版本。</span><span class="sxs-lookup"><span data-stu-id="eed2c-156">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="eed2c-157">将 [Owin](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) 包安装到每个 ASP.NET 1.x 应用程序中。</span><span class="sxs-lookup"><span data-stu-id="eed2c-157">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="eed2c-158">查找并修改对的调用 `Use:::no-loc(Cookie):::Authentication` ：</span><span class="sxs-lookup"><span data-stu-id="eed2c-158">Locate and modify the call to `Use:::no-loc(Cookie):::Authentication`:</span></span>

* <span data-ttu-id="eed2c-159">更改该 :::no-loc(cookie)::: 名称以匹配该 :::no-loc(Cookie)::: 示例) 中 ASP.NET Core Authentication 中间件 (使用的名称 `.AspNet.Shared:::no-loc(Cookie):::` 。</span><span class="sxs-lookup"><span data-stu-id="eed2c-159">Change the :::no-loc(cookie)::: name to match the name used by the ASP.NET Core :::no-loc(Cookie)::: Authentication Middleware (`.AspNet.Shared:::no-loc(Cookie):::` in the example).</span></span>
* <span data-ttu-id="eed2c-160">在下面的示例中，"身份验证类型" 设置为 `:::no-loc(Identity):::.Application` 。</span><span class="sxs-lookup"><span data-stu-id="eed2c-160">In the following example, the authentication type is set to `:::no-loc(Identity):::.Application`.</span></span>
* <span data-ttu-id="eed2c-161">向 `DataProtectionProvider` 通用数据保护密钥存储位置提供已初始化的实例。</span><span class="sxs-lookup"><span data-stu-id="eed2c-161">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="eed2c-162">确认应用名称设置为在 :::no-loc(cookie)::: 示例) 中共享身份验证 (的所有应用使用的通用应用名称 `Shared:::no-loc(Cookie):::App` 。</span><span class="sxs-lookup"><span data-stu-id="eed2c-162">Confirm that the app name is set to the common app name used by all apps that share authentication :::no-loc(cookie):::s (`Shared:::no-loc(Cookie):::App` in the example).</span></span>

<span data-ttu-id="eed2c-163">如果未设置 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` 和 `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` ，则将设置 <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> 为区分唯一用户的声明。</span><span class="sxs-lookup"><span data-stu-id="eed2c-163">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="eed2c-164">*App_Start/startup.auth.cs* ：</span><span class="sxs-lookup"><span data-stu-id="eed2c-164">*App_Start/Startup.Auth.cs* :</span></span>

```csharp
app.Use:::no-loc(Cookie):::Authentication(new :::no-loc(Cookie):::AuthenticationOptions
{
    AuthenticationType = ":::no-loc(Identity):::.Application",
    :::no-loc(Cookie):::Name = ".AspNet.Shared:::no-loc(Cookie):::",
    LoginPath = new PathString("/Account/Login"),
    Provider = new :::no-loc(Cookie):::AuthenticationProvider
    {
        OnValidate:::no-loc(Identity)::: =
            SecurityStampValidator
                .OnValidate:::no-loc(Identity):::<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerate:::no-loc(Identity):::: (manager, user) =>
                        user.GenerateUser:::no-loc(Identity):::Async(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("Shared:::no-loc(Cookie):::App"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s." +
                    ":::no-loc(Cookie):::AuthenticationMiddleware",
                ":::no-loc(Identity):::.Application",
                "v2"))),
    :::no-loc(Cookie):::Manager = new Chunking:::no-loc(Cookie):::Manager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

<span data-ttu-id="eed2c-165">生成用户标识时， () 的身份验证类型 `:::no-loc(Identity):::.Application` 必须与 `AuthenticationType` `Use:::no-loc(Cookie):::Authentication` *App_Start/startup.auth.cs* 中的 "设置" 中定义的类型匹配。</span><span class="sxs-lookup"><span data-stu-id="eed2c-165">When generating a user identity, the authentication type (`:::no-loc(Identity):::.Application`) must match the type defined in `AuthenticationType` set with `Use:::no-loc(Cookie):::Authentication` in *App_Start/Startup.Auth.cs* .</span></span>

<span data-ttu-id="eed2c-166">*模型/ :::no-loc(Identity):::Models.cs* ：</span><span class="sxs-lookup"><span data-stu-id="eed2c-166">*Models/:::no-loc(Identity):::Models.cs* :</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public async Task<Claims:::no-loc(Identity):::> GenerateUser:::no-loc(Identity):::Async(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // :::no-loc(Cookie):::AuthenticationOptions.AuthenticationType
        var user:::no-loc(Identity)::: = 
            await manager.Create:::no-loc(Identity):::Async(this, ":::no-loc(Identity):::.Application");

        // Add custom user claims here

        return user:::no-loc(Identity):::;
    }
}
```

## <a name="use-a-common-user-database"></a><span data-ttu-id="eed2c-167">使用公共用户数据库</span><span class="sxs-lookup"><span data-stu-id="eed2c-167">Use a common user database</span></span>

<span data-ttu-id="eed2c-168">如果应用使用相同 :::no-loc(Identity)::: 的架构 (相同的 :::no-loc(Identity):::) 版本，请确认 :::no-loc(Identity)::: 每个应用的系统指向同一用户数据库。</span><span class="sxs-lookup"><span data-stu-id="eed2c-168">When apps use the same :::no-loc(Identity)::: schema (same version of :::no-loc(Identity):::), confirm that the :::no-loc(Identity)::: system for each app is pointed at the same user database.</span></span> <span data-ttu-id="eed2c-169">否则，当标识系统尝试将身份验证中的信息与 :::no-loc(cookie)::: 数据库中的信息进行匹配时，它将在运行时生成故障。</span><span class="sxs-lookup"><span data-stu-id="eed2c-169">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication :::no-loc(cookie)::: against the information in its database.</span></span>

<span data-ttu-id="eed2c-170">如果 :::no-loc(Identity)::: 架构在应用中不同，通常是因为应用使用的是不同 :::no-loc(Identity)::: 的版本，则在 :::no-loc(Identity)::: 不重新映射和添加其他应用的架构中的列的情况下，不能使用来共享基于最新版本的公共数据库 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="eed2c-170">When the :::no-loc(Identity)::: schema is different among apps, usually because apps are using different :::no-loc(Identity)::: versions, sharing a common database based on the latest version of :::no-loc(Identity)::: isn't possible without remapping and adding columns in other app's :::no-loc(Identity)::: schemas.</span></span> <span data-ttu-id="eed2c-171">将其他应用程序升级到使用最新版本通常更为有效， :::no-loc(Identity)::: 以便应用可以共享公共数据库。</span><span class="sxs-lookup"><span data-stu-id="eed2c-171">It's often more efficient to upgrade the other apps to use the latest :::no-loc(Identity)::: version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eed2c-172">其他资源</span><span class="sxs-lookup"><span data-stu-id="eed2c-172">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
