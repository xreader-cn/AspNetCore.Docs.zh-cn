---
title: 在 ASP.NET 应用程序之间共享身份验证 cookie
author: rick-anderson
description: 了解如何共享身份验证 cookie 之间 ASP.NET 4.x 和 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2019
uid: security/cookie-sharing
ms.openlocfilehash: b2f906ac97fe79b2a66a5ab709bcbcb03ab8cc39
ms.sourcegitcommit: 1bf80f4acd62151ff8cce517f03f6fa891136409
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "68223914"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a><span data-ttu-id="40a4f-103">在 ASP.NET 应用程序之间共享身份验证 cookie</span><span class="sxs-lookup"><span data-stu-id="40a4f-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="40a4f-104">通过[Rick Anderson](https://twitter.com/RickAndMSFT)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="40a4f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="40a4f-105">网站通常包含单独的 web 应用程序协同工作。</span><span class="sxs-lookup"><span data-stu-id="40a4f-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="40a4f-106">若要提供单一登录 (SSO) 体验，站点内的 web 应用必须共享身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="40a4f-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="40a4f-107">若要支持这种情况下，数据保护堆栈允许在共享 Katana cookie 身份验证和 ASP.NET Core cookie 身份验证票证。</span><span class="sxs-lookup"><span data-stu-id="40a4f-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="40a4f-108">在下面的示例：</span><span class="sxs-lookup"><span data-stu-id="40a4f-108">In the examples that follow:</span></span>

* <span data-ttu-id="40a4f-109">身份验证 cookie 名称设置为公用值的`.AspNet.SharedCookie`。</span><span class="sxs-lookup"><span data-stu-id="40a4f-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="40a4f-110">`AuthenticationType`设置为`Identity.Application`显式或默认情况下。</span><span class="sxs-lookup"><span data-stu-id="40a4f-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="40a4f-111">常见的应用名称用于启用数据保护系统共享数据保护密钥 (`SharedCookieApp`)。</span><span class="sxs-lookup"><span data-stu-id="40a4f-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="40a4f-112">`Identity.Application` 使用作为身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="40a4f-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="40a4f-113">使用任何方案，必须一致地使用它*内部和跨*共享的 cookie 应用作为默认方案或通过显式设置。</span><span class="sxs-lookup"><span data-stu-id="40a4f-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="40a4f-114">加密和解密 cookie，因此，必须在应用之间使用一致的方案时，使用方案。</span><span class="sxs-lookup"><span data-stu-id="40a4f-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="40a4f-115">一种常见[数据保护密钥](xref:security/data-protection/implementation/key-management)使用存储位置。</span><span class="sxs-lookup"><span data-stu-id="40a4f-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="40a4f-116">在 ASP.NET Core 应用中，<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*>用于设置密钥存储位置。</span><span class="sxs-lookup"><span data-stu-id="40a4f-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="40a4f-117">在.NET Framework 应用中，Cookie 身份验证中间件使用实现<xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>。</span><span class="sxs-lookup"><span data-stu-id="40a4f-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="40a4f-118">`DataProtectionProvider` 提供对身份验证 cookie 有效负载数据进行加密和解密的数据保护服务。</span><span class="sxs-lookup"><span data-stu-id="40a4f-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="40a4f-119">`DataProtectionProvider`实例都独立于应用的其他部分使用的数据保护系统。</span><span class="sxs-lookup"><span data-stu-id="40a4f-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="40a4f-120">[DataProtectionProvider.Create (System.IO.DirectoryInfo、 操作\<IDataProtectionBuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*)接受<xref:System.IO.DirectoryInfo>指定用于数据保护密钥存储的位置。</span><span class="sxs-lookup"><span data-stu-id="40a4f-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="40a4f-121">`DataProtectionProvider` 需要[Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="40a4f-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="40a4f-122">在 ASP.NET Core 2.x 应用中，引用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="40a4f-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="40a4f-123">在.NET Framework 应用程序添加到的包引用[Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)。</span><span class="sxs-lookup"><span data-stu-id="40a4f-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="40a4f-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 设置的常见应用程序。</span><span class="sxs-lookup"><span data-stu-id="40a4f-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a><span data-ttu-id="40a4f-125">共享 ASP.NET Core 应用之间的身份验证 cookie</span><span class="sxs-lookup"><span data-stu-id="40a4f-125">Share authentication cookies among ASP.NET Core apps</span></span>

<span data-ttu-id="40a4f-126">当使用 ASP.NET Core标识：</span><span class="sxs-lookup"><span data-stu-id="40a4f-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="40a4f-127">数据保护密钥和应用名称必须在应用之间共享。</span><span class="sxs-lookup"><span data-stu-id="40a4f-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="40a4f-128">常见的密钥存储位置提供给<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*>下面的示例中的方法。</span><span class="sxs-lookup"><span data-stu-id="40a4f-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="40a4f-129">使用<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>来配置常见的共享的应用名称 (`SharedCookieApp`下面的示例中)。</span><span class="sxs-lookup"><span data-stu-id="40a4f-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="40a4f-130">有关详细信息，请参阅 <xref:security/data-protection/configuration/overview> 。</span><span class="sxs-lookup"><span data-stu-id="40a4f-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="40a4f-131">使用<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*>扩展方法，以设置 cookie 的数据保护服务。</span><span class="sxs-lookup"><span data-stu-id="40a4f-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="40a4f-132">在以下示例中，身份验证类型设置为`Identity.Application`默认情况下。</span><span class="sxs-lookup"><span data-stu-id="40a4f-132">In the following example, the authentication type is set to `Identity.Application` by default.</span></span>

<span data-ttu-id="40a4f-133">在 `Startup.ConfigureServices`中：</span><span class="sxs-lookup"><span data-stu-id="40a4f-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

<span data-ttu-id="40a4f-134">当使用 ASP.NET Core 标识不直接的 cookie，配置数据保护和身份验证中的`Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="40a4f-134">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="40a4f-135">在以下示例中，身份验证类型设置为`Identity.Application`:</span><span class="sxs-lookup"><span data-stu-id="40a4f-135">In the following example, the authentication type is set to `Identity.Application`:</span></span>

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

<span data-ttu-id="40a4f-136">托管在子域之间共享 cookie 的应用，指定在一个公共域[Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain)属性。</span><span class="sxs-lookup"><span data-stu-id="40a4f-136">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="40a4f-137">若要在应用之间共享 cookie `contoso.com`，如`first_subdomain.contoso.com`并`second_subdomain.contoso.com`，指定`Cookie.Domain`作为`.contoso.com`:</span><span class="sxs-lookup"><span data-stu-id="40a4f-137">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="40a4f-138">加密静态数据保护密钥</span><span class="sxs-lookup"><span data-stu-id="40a4f-138">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="40a4f-139">对于生产部署，配置`DataProtectionProvider`来加密静态使用 DPAPI 或 x509 证书的密钥。</span><span class="sxs-lookup"><span data-stu-id="40a4f-139">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="40a4f-140">有关详细信息，请参阅 <xref:security/data-protection/implementation/key-encryption-at-rest> 。</span><span class="sxs-lookup"><span data-stu-id="40a4f-140">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="40a4f-141">在以下示例中，证书指纹提供给<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span><span class="sxs-lookup"><span data-stu-id="40a4f-141">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="40a4f-142">身份验证之间共享 cookie ASP.NET 4.x 和 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="40a4f-142">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="40a4f-143">使用 Katana Cookie 身份验证中间件的 ASP.NET 4.x 应用程序可以配置为生成 ASP.NET Core Cookie 身份验证中间件与兼容的身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="40a4f-143">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="40a4f-144">这样，在几个步骤中的大型站点的单个应用升级同时在站点之间提供顺畅的 SSO 体验。</span><span class="sxs-lookup"><span data-stu-id="40a4f-144">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="40a4f-145">当应用使用 Katana Cookie 身份验证中间件时，它将调用`UseCookieAuthentication`在项目的*Startup.Auth.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="40a4f-145">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="40a4f-146">ASP.NET 4.x web 应用程序项目创建与 Visual Studio 2013 和更高版本默认情况下使用 Katana Cookie 身份验证中间件。</span><span class="sxs-lookup"><span data-stu-id="40a4f-146">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="40a4f-147">尽管`UseCookieAuthentication`已过时或不受支持的 ASP.NET Core 应用程序，调用`UseCookieAuthentication`ASP.NET 中使用 Katana Cookie 身份验证中间件的 4.x 应用程序是否有效。</span><span class="sxs-lookup"><span data-stu-id="40a4f-147">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="40a4f-148">ASP.NET 4.x 应用程序必须面向.NET Framework 4.5.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="40a4f-148">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="40a4f-149">否则，无法安装所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="40a4f-149">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="40a4f-150">若要共享 ASP.NET 4.x 应用程序和 ASP.NET Core 应用之间的身份验证 cookie，请先配置 ASP.NET Core 应用，如中所述[共享在 ASP.NET Core 应用程序之间的身份验证 cookie](#share-authentication-cookies-among-aspnet-core-apps)部分，然后配置 ASP.NET 4.x 应用作为如下所示。</span><span class="sxs-lookup"><span data-stu-id="40a4f-150">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-among-aspnet-core-apps) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="40a4f-151">确认应用的包已更新到最新版本。</span><span class="sxs-lookup"><span data-stu-id="40a4f-151">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="40a4f-152">安装[Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/)到每个 ASP.NET 4.x 应用程序的包。</span><span class="sxs-lookup"><span data-stu-id="40a4f-152">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="40a4f-153">找到并修改对调用`UseCookieAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="40a4f-153">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="40a4f-154">更改要使用的 ASP.NET Core Cookie 身份验证中间件的名称匹配的 cookie 名称 (`.AspNet.SharedCookie`在示例中)。</span><span class="sxs-lookup"><span data-stu-id="40a4f-154">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="40a4f-155">在以下示例中，身份验证类型设置为`Identity.Application`。</span><span class="sxs-lookup"><span data-stu-id="40a4f-155">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="40a4f-156">提供的一个实例`DataProtectionProvider`初始化为常见的数据保护密钥存储位置。</span><span class="sxs-lookup"><span data-stu-id="40a4f-156">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="40a4f-157">确认应用程序名称已设置为使用共享身份验证 cookie 的所有应用的常见应用程序名称 (`SharedCookieApp`在示例中)。</span><span class="sxs-lookup"><span data-stu-id="40a4f-157">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="40a4f-158">如果未设置`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`并`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`，将<xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier>到区别开来的唯一用户的声明。</span><span class="sxs-lookup"><span data-stu-id="40a4f-158">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="40a4f-159">*App_Start/Startup.Auth.cs*:</span><span class="sxs-lookup"><span data-stu-id="40a4f-159">*App_Start/Startup.Auth.cs*:</span></span>

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

<span data-ttu-id="40a4f-160">当生成用户标识，身份验证类型时 (`Identity.Application`) 中定义的类型必须匹配`AuthenticationType`集`UseCookieAuthentication`中*App_Start/Startup.Auth.cs*。</span><span class="sxs-lookup"><span data-stu-id="40a4f-160">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="40a4f-161">*Models/IdentityModels.cs*:</span><span class="sxs-lookup"><span data-stu-id="40a4f-161">*Models/IdentityModels.cs*:</span></span>

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

## <a name="use-a-common-user-database"></a><span data-ttu-id="40a4f-162">使用常见的用户数据库</span><span class="sxs-lookup"><span data-stu-id="40a4f-162">Use a common user database</span></span>

<span data-ttu-id="40a4f-163">当应用使用相同的标识架构 （标识的相同版本），确认每个应用的标识系统指向同一个用户数据库。</span><span class="sxs-lookup"><span data-stu-id="40a4f-163">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="40a4f-164">否则，标识系统将生成在运行时失败时它将尝试匹配对其数据库中的信息的身份验证 cookie 中的信息。</span><span class="sxs-lookup"><span data-stu-id="40a4f-164">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="40a4f-165">在应用之间不同标识架构时，通常因为应用程序正在使用不同的标识版本，共享通用基于标识的最新版本的数据库不可能而无需重新映射和其他应用程序的标识架构中添加列。</span><span class="sxs-lookup"><span data-stu-id="40a4f-165">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="40a4f-166">通常会更有效地升级其他应用程序以使用最新的标识版本，以便可以由应用程序共享通用的数据库。</span><span class="sxs-lookup"><span data-stu-id="40a4f-166">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="40a4f-167">其他资源</span><span class="sxs-lookup"><span data-stu-id="40a4f-167">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
