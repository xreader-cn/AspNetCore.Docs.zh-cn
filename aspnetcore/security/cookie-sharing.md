---
title: 在 ASP.NET 应用中共享身份验证 cookie
author: rick-anderson
description: 了解如何在 ASP.NET 4.x 和 ASP.NET Core 应用之间共享身份验证 cookie。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cookie-sharing
ms.openlocfilehash: b8de5ea1da47c7813aac0137719989959d4c8ce4
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407026"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a>在 ASP.NET 应用中共享身份验证 cookie

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

网站通常由单独的 web 应用组成。 若要提供单一登录（SSO）体验，站点内的 web 应用必须共享身份验证 cookie。 为了支持此方案，数据保护堆栈允许共享 Katana cookie 身份验证和 ASP.NET Core cookie 身份验证票证。

在下面的示例中：

* 身份验证 cookie 名称设置为的公共值 `.AspNet.SharedCookie` 。
* `AuthenticationType`设置为 `Identity.Application` 显式或默认设置为。
* 常见的应用程序名称用于使数据保护系统共享数据保护密钥（ `SharedCookieApp` ）。
* `Identity.Application`用作身份验证方案。 无论使用哪种方案，都必须在共享 cookie 应用*内和中*以一致的方式使用它，或者通过显式设置。 加密和解密 cookie 时使用该方案，因此必须在应用间使用一致的方案。
* 使用通用的[数据保护密钥](xref:security/data-protection/implementation/key-management)存储位置。
  * 在 ASP.NET Core 应用中， <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 用于设置密钥存储位置。
  * 在 .NET Framework 应用中，Cookie 身份验证中间件使用的实现 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> 。 `DataProtectionProvider`为身份验证 cookie 负载数据的加密和解密提供数据保护服务。 该 `DataProtectionProvider` 实例与应用程序的其他部分所使用的数据保护系统隔离。 [DataProtectionProvider （DirectoryInfo，Action \<IDataProtectionBuilder> ）](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*)接受 <xref:System.IO.DirectoryInfo> 来指定数据保护密钥存储的位置。
* `DataProtectionProvider`需要 DataProtection NuGet 包[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) ：
  * 在 ASP.NET Core 1.x 应用程序中，请参阅[AspNetCore 元包](xref:fundamentals/metapackage-app)。
  * 在 .NET Framework 应用中，将包引用添加到[AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)。
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>设置常见的应用名称。

## <a name="share-authentication-cookies-with-aspnet-core-identity"></a>与 ASP.NET Core 共享身份验证 cookieIdentity

使用 ASP.NET Core 时 Identity ：

* 数据保护密钥和应用程序名称必须在应用之间共享。 在以下示例中，将向方法提供一个公共密钥存储位置 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 。 使用 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 配置公共共享应用名称（ `SharedCookieApp` 在以下示例中）。 有关详细信息，请参阅 <xref:security/data-protection/configuration/overview>。
* 使用 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> 扩展方法为 cookie 设置数据保护服务。
* 默认的身份验证类型为 `Identity.Application` 。

在 `Startup.ConfigureServices`中：

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-cookies-without-aspnet-core-identity"></a>共享身份验证 cookie 而不 ASP.NET CoreIdentity

如果在没有 ASP.NET Core 的情况下直接使用 cookie Identity ，请在中配置数据保护和身份验证 `Startup.ConfigureServices` 。 在下面的示例中，"身份验证类型" 设置为 `Identity.Application` ：

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-cookies-across-different-base-paths"></a>跨不同的基路径共享 cookie

身份验证 cookie 使用[HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)作为其默认的[cookie。](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path) 如果应用的 cookie 必须在不同的基路径间共享，则 `Path` 必须重写：

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-cookies-across-subdomains"></a>跨子域共享 cookie

承载跨子域共享 cookie 的应用时，请在 " [Cookie](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) " 属性中指定一个公共域。 若要在中的应用之间共享 cookie （ `contoso.com` 例如 `first_subdomain.contoso.com` 和 `second_subdomain.contoso.com` ），请将指定 `Cookie.Domain` 为 `.contoso.com` ：

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>加密静态数据保护密钥

对于生产部署，请将配置 `DataProtectionProvider` 为使用 DPAPI 或 X509Certificate 加密静态密钥。 有关详细信息，请参阅 <xref:security/data-protection/implementation/key-encryption-at-rest>。 在下面的示例中，提供了一个证书指纹来 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> 执行以下操作：

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>在 ASP.NET 4.x 和 ASP.NET Core 应用之间共享身份验证 cookie

使用 Katana Cookie 身份验证中间件的 ASP.NET 4.x 应用程序可以配置为生成与 ASP.NET Core Cookie 身份验证中间件兼容的身份验证 cookie。 这允许在多个步骤中升级大型站点的单个应用，同时跨站点提供平稳的 SSO 体验。

当应用使用 Katana Cookie 身份验证中间件时，它会调用 `UseCookieAuthentication` 项目的*Startup.Auth.cs*文件。 使用 Visual Studio 2013 和更高版本创建的 ASP.NET 4.x web 应用项目默认使用 Katana Cookie 身份验证中间件。 尽管 `UseCookieAuthentication` ASP.NET Core 应用已过时且不受支持，但 `UseCookieAuthentication` 在使用 Katana Cookie 身份验证中间件的 ASP.NET 4.x 应用中调用是有效的。

ASP.NET 4.x 应用必须面向 .NET Framework 4.5.1 或更高版本。 否则，无法安装所需的 NuGet 包。

若要在 ASP.NET 4.x 应用和 ASP.NET Core 应用之间共享身份验证 cookie，请按在[ASP.NET Core 应用之间共享身份验证 cookie](#share-authentication-cookies-with-aspnet-core-identity)部分中所述配置 ASP.NET Core 应用，然后按如下所示配置 ASP.NET 4.x 应用。

确认应用的包已更新到最新版本。 将[Owin](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/)包安装到每个 ASP.NET 1.x 应用程序中。

查找并修改对的调用 `UseCookieAuthentication` ：

* 更改 cookie 名称，使其与 ASP.NET Core Cookie 身份验证中间件使用的名称匹配（ `.AspNet.SharedCookie` 在本示例中）。
* 在下面的示例中，"身份验证类型" 设置为 `Identity.Application` 。
* 向 `DataProtectionProvider` 通用数据保护密钥存储位置提供已初始化的实例。
* 确认应用名称设置为共享身份验证 cookie 的所有应用使用的通用应用名称（ `SharedCookieApp` 在此示例中）。

如果未设置 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` 和 `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` ，则将设置 <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> 为区分唯一用户的声明。

*App_Start/startup.auth.cs*：

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
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
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

生成用户标识时，身份验证类型（ `Identity.Application` ）必须与在 `AuthenticationType` `UseCookieAuthentication` *App_Start/startup.auth.cs*中的 "设置" 中定义的类型匹配。

*模型/IdentityModels*：

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

## <a name="use-a-common-user-database"></a>使用公共用户数据库

当应用使用相同 Identity 的架构（同一版本 Identity ）时，请确认 Identity 每个应用的系统指向同一用户数据库。 否则，当标识系统尝试将身份验证 cookie 中的信息与数据库中的信息进行匹配时，它将在运行时生成故障。

如果 Identity 架构在应用中不同，通常是因为应用使用的是不同 Identity 的版本，则在 Identity 不重新映射和添加其他应用的架构中的列的情况下，不能使用来共享基于最新版本的公共数据库 Identity 。 将其他应用程序升级到使用最新版本通常更为有效， Identity 以便应用可以共享公共数据库。

## <a name="additional-resources"></a>其他资源

* <xref:host-and-deploy/web-farm>
