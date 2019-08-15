---
title: 在 ASP.NET 应用中共享身份验证 cookie
author: rick-anderson
description: 了解如何共享身份验证 cookie 之间 ASP.NET 4.x 和 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: security/cookie-sharing
ms.openlocfilehash: 1650afce5c371d0830bb207618b9c1495f0ce587
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022391"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a><span data-ttu-id="11ca2-103">在 ASP.NET 应用中共享身份验证 cookie</span><span class="sxs-lookup"><span data-stu-id="11ca2-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="11ca2-104">作者: [Rick Anderson](https://twitter.com/RickAndMSFT)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="11ca2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="11ca2-105">网站通常由单独的 web 应用组成。</span><span class="sxs-lookup"><span data-stu-id="11ca2-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="11ca2-106">若要提供单一登录 (SSO) 体验, 站点内的 web 应用必须共享身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="11ca2-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="11ca2-107">若要支持这种情况下，数据保护堆栈允许在共享 Katana cookie 身份验证和 ASP.NET Core cookie 身份验证票证。</span><span class="sxs-lookup"><span data-stu-id="11ca2-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="11ca2-108">在下面的示例中:</span><span class="sxs-lookup"><span data-stu-id="11ca2-108">In the examples that follow:</span></span>

* <span data-ttu-id="11ca2-109">身份验证 cookie 名称设置为的`.AspNet.SharedCookie`公共值。</span><span class="sxs-lookup"><span data-stu-id="11ca2-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="11ca2-110">设置为显式或默认设置为`Identity.Application`。 `AuthenticationType`</span><span class="sxs-lookup"><span data-stu-id="11ca2-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="11ca2-111">常见的应用程序名称用于使数据保护系统共享数据保护密钥 (`SharedCookieApp`)。</span><span class="sxs-lookup"><span data-stu-id="11ca2-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="11ca2-112">`Identity.Application`用作身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="11ca2-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="11ca2-113">无论使用哪种方案, 都必须在共享 cookie 应用*内和中*以一致的方式使用它, 或者通过显式设置。</span><span class="sxs-lookup"><span data-stu-id="11ca2-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="11ca2-114">加密和解密 cookie 时使用该方案, 因此必须在应用间使用一致的方案。</span><span class="sxs-lookup"><span data-stu-id="11ca2-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="11ca2-115">使用通用的[数据保护密钥](xref:security/data-protection/implementation/key-management)存储位置。</span><span class="sxs-lookup"><span data-stu-id="11ca2-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="11ca2-116">在 ASP.NET Core 应用中<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> , 用于设置密钥存储位置。</span><span class="sxs-lookup"><span data-stu-id="11ca2-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="11ca2-117">在 .NET Framework 应用中, Cookie 身份验证中间件使用<xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>的实现。</span><span class="sxs-lookup"><span data-stu-id="11ca2-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="11ca2-118">`DataProtectionProvider`为身份验证 cookie 负载数据的加密和解密提供数据保护服务。</span><span class="sxs-lookup"><span data-stu-id="11ca2-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="11ca2-119">该`DataProtectionProvider`实例与应用程序的其他部分所使用的数据保护系统隔离。</span><span class="sxs-lookup"><span data-stu-id="11ca2-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="11ca2-120">[DataProtectionProvider (DirectoryInfo, Action\<IDataProtectionBuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*)接受<xref:System.IO.DirectoryInfo>来指定数据保护密钥存储的位置。</span><span class="sxs-lookup"><span data-stu-id="11ca2-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="11ca2-121">`DataProtectionProvider`需要 DataProtection NuGet 包[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :</span><span class="sxs-lookup"><span data-stu-id="11ca2-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="11ca2-122">在 ASP.NET Core 1.x 应用程序中, 请参阅[AspNetCore 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="11ca2-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="11ca2-123">在 .NET Framework 应用中, 将包引用添加到[AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)。</span><span class="sxs-lookup"><span data-stu-id="11ca2-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="11ca2-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>设置常见的应用名称。</span><span class="sxs-lookup"><span data-stu-id="11ca2-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a><span data-ttu-id="11ca2-125">共享 ASP.NET Core 应用之间的身份验证 cookie</span><span class="sxs-lookup"><span data-stu-id="11ca2-125">Share authentication cookies among ASP.NET Core apps</span></span>

<span data-ttu-id="11ca2-126">当使用 ASP.NET Core标识：</span><span class="sxs-lookup"><span data-stu-id="11ca2-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="11ca2-127">数据保护密钥和应用程序名称必须在应用之间共享。</span><span class="sxs-lookup"><span data-stu-id="11ca2-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="11ca2-128">在以下示例中, 将向<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*>方法提供一个公共密钥存储位置。</span><span class="sxs-lookup"><span data-stu-id="11ca2-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="11ca2-129">使用<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>配置公共共享应用名称 (`SharedCookieApp`在以下示例中)。</span><span class="sxs-lookup"><span data-stu-id="11ca2-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="11ca2-130">有关详细信息，请参阅 <xref:security/data-protection/configuration/overview>。</span><span class="sxs-lookup"><span data-stu-id="11ca2-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="11ca2-131"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*>使用扩展方法为 cookie 设置数据保护服务。</span><span class="sxs-lookup"><span data-stu-id="11ca2-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="11ca2-132">默认的身份验证类型`Identity.Application`为。</span><span class="sxs-lookup"><span data-stu-id="11ca2-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="11ca2-133">在 `Startup.ConfigureServices`中：</span><span class="sxs-lookup"><span data-stu-id="11ca2-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

<span data-ttu-id="11ca2-134">如果在不 ASP.NET Core 标识的情况下直接使用 cookie, 请在`Startup.ConfigureServices`中配置数据保护和身份验证。</span><span class="sxs-lookup"><span data-stu-id="11ca2-134">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="11ca2-135">在下面的示例中, "身份验证类型" `Identity.Application`设置为:</span><span class="sxs-lookup"><span data-stu-id="11ca2-135">In the following example, the authentication type is set to `Identity.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

<span data-ttu-id="11ca2-136">承载跨子域共享 cookie 的应用时, 请在 " [Cookie](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) " 属性中指定一个公共域。</span><span class="sxs-lookup"><span data-stu-id="11ca2-136">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="11ca2-137">若`contoso.com`要在中的应用之间共享 cookie `first_subdomain.contoso.com` ( `second_subdomain.contoso.com`例如和) `Cookie.Domain` , `.contoso.com`请将指定为:</span><span class="sxs-lookup"><span data-stu-id="11ca2-137">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="11ca2-138">加密静态数据保护密钥</span><span class="sxs-lookup"><span data-stu-id="11ca2-138">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="11ca2-139">对于生产部署, 请将`DataProtectionProvider`配置为使用 DPAPI 或 X509Certificate 加密静态密钥。</span><span class="sxs-lookup"><span data-stu-id="11ca2-139">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="11ca2-140">有关详细信息，请参阅 <xref:security/data-protection/implementation/key-encryption-at-rest> 。</span><span class="sxs-lookup"><span data-stu-id="11ca2-140">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="11ca2-141">在下面的示例中, 提供了一个证书指纹<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>来执行以下操作:</span><span class="sxs-lookup"><span data-stu-id="11ca2-141">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="11ca2-142">在 ASP.NET 4.x 和 ASP.NET Core 应用之间共享身份验证 cookie</span><span class="sxs-lookup"><span data-stu-id="11ca2-142">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="11ca2-143">使用 Katana Cookie 身份验证中间件的 ASP.NET 4.x 应用程序可以配置为生成与 ASP.NET Core Cookie 身份验证中间件兼容的身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="11ca2-143">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="11ca2-144">这允许在多个步骤中升级大型站点的单个应用, 同时跨站点提供平稳的 SSO 体验。</span><span class="sxs-lookup"><span data-stu-id="11ca2-144">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="11ca2-145">当应用使用 Katana Cookie 身份验证中间件时, `UseCookieAuthentication`它会调用项目的*Startup.Auth.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="11ca2-145">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="11ca2-146">使用 Visual Studio 2013 和更高版本创建的 ASP.NET 4.x web 应用项目默认使用 Katana Cookie 身份验证中间件。</span><span class="sxs-lookup"><span data-stu-id="11ca2-146">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="11ca2-147">尽管`UseCookieAuthentication` ASP.NET Core 应用已过时且不受支持, `UseCookieAuthentication`但在使用 Katana Cookie 身份验证中间件的 ASP.NET 4.x 应用中调用是有效的。</span><span class="sxs-lookup"><span data-stu-id="11ca2-147">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="11ca2-148">ASP.NET 4.x 应用必须面向 .NET Framework 4.5.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="11ca2-148">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="11ca2-149">否则, 无法安装所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="11ca2-149">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="11ca2-150">若要在 ASP.NET 4.x 应用和 ASP.NET Core 应用之间共享身份验证 cookie, 请按在[ASP.NET Core 应用之间共享身份验证 cookie](#share-authentication-cookies-among-aspnet-core-apps)部分中所述配置 ASP.NET Core 应用, 然后按如下所示配置 ASP.NET 4.x 应用。</span><span class="sxs-lookup"><span data-stu-id="11ca2-150">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-among-aspnet-core-apps) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="11ca2-151">确认应用的包已更新到最新版本。</span><span class="sxs-lookup"><span data-stu-id="11ca2-151">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="11ca2-152">将[Owin](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/)包安装到每个 ASP.NET 1.x 应用程序中。</span><span class="sxs-lookup"><span data-stu-id="11ca2-152">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="11ca2-153">查找并修改对`UseCookieAuthentication`的调用:</span><span class="sxs-lookup"><span data-stu-id="11ca2-153">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="11ca2-154">更改 cookie 名称, 使其与 ASP.NET Core cookie 身份验证中间件使用的名称`.AspNet.SharedCookie`匹配 (在本示例中)。</span><span class="sxs-lookup"><span data-stu-id="11ca2-154">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="11ca2-155">在下面的示例中, "身份验证类型" `Identity.Application`设置为。</span><span class="sxs-lookup"><span data-stu-id="11ca2-155">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="11ca2-156">向通用数据保护密钥`DataProtectionProvider`存储位置提供已初始化的实例。</span><span class="sxs-lookup"><span data-stu-id="11ca2-156">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="11ca2-157">确认应用名称设置为共享身份验证 cookie 的所有应用使用的通用应用名称 (`SharedCookieApp`在此示例中)。</span><span class="sxs-lookup"><span data-stu-id="11ca2-157">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="11ca2-158">如果未设置`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`和`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, 则<xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier>将设置为区分唯一用户的声明。</span><span class="sxs-lookup"><span data-stu-id="11ca2-158">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="11ca2-159">*App_Start/* authentication:</span><span class="sxs-lookup"><span data-stu-id="11ca2-159">*App_Start/Startup.Auth.cs*:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create({PATH TO COMMON KEY RING FOLDER},
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

<span data-ttu-id="11ca2-160">生成用户标识时,`Identity.Application`身份验证类型 () 必须与在 `AuthenticationType` App_Start/中使用 set `UseCookieAuthentication` with 定义的类型匹配。</span><span class="sxs-lookup"><span data-stu-id="11ca2-160">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="11ca2-161">*模型/IdentityModels*:</span><span class="sxs-lookup"><span data-stu-id="11ca2-161">*Models/IdentityModels.cs*:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a><span data-ttu-id="11ca2-162">使用公共用户数据库</span><span class="sxs-lookup"><span data-stu-id="11ca2-162">Use a common user database</span></span>

<span data-ttu-id="11ca2-163">如果应用使用相同的标识架构 (同一版本标识), 请确认每个应用的标识系统指向同一用户数据库。</span><span class="sxs-lookup"><span data-stu-id="11ca2-163">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="11ca2-164">否则, 当标识系统尝试将身份验证 cookie 中的信息与数据库中的信息进行匹配时, 它将在运行时生成故障。</span><span class="sxs-lookup"><span data-stu-id="11ca2-164">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="11ca2-165">当标识架构在应用中不同时, 通常是因为应用使用的是不同的标识版本, 因此, 在不重新映射和添加其他应用的标识架构中的列的情况下, 不能使用最新版本的标识共享公共数据库。</span><span class="sxs-lookup"><span data-stu-id="11ca2-165">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="11ca2-166">将其他应用程序升级到使用最新的标识版本通常更高效, 以便应用可以共享公共数据库。</span><span class="sxs-lookup"><span data-stu-id="11ca2-166">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="11ca2-167">其他资源</span><span class="sxs-lookup"><span data-stu-id="11ca2-167">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
