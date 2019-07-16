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
# <a name="share-authentication-cookies-among-aspnet-apps"></a>在 ASP.NET 应用程序之间共享身份验证 cookie

通过[Rick Anderson](https://twitter.com/RickAndMSFT)和[Luke Latham](https://github.com/guardrex)

网站通常包含单独的 web 应用程序协同工作。 若要提供单一登录 (SSO) 体验，站点内的 web 应用必须共享身份验证 cookie。 若要支持这种情况下，数据保护堆栈允许在共享 Katana cookie 身份验证和 ASP.NET Core cookie 身份验证票证。

在下面的示例：

* 身份验证 cookie 名称设置为公用值的`.AspNet.SharedCookie`。
* `AuthenticationType`设置为`Identity.Application`显式或默认情况下。
* 常见的应用名称用于启用数据保护系统共享数据保护密钥 (`SharedCookieApp`)。
* `Identity.Application` 使用作为身份验证方案。 使用任何方案，必须一致地使用它*内部和跨*共享的 cookie 应用作为默认方案或通过显式设置。 加密和解密 cookie，因此，必须在应用之间使用一致的方案时，使用方案。
* 一种常见[数据保护密钥](xref:security/data-protection/implementation/key-management)使用存储位置。
  * 在 ASP.NET Core 应用中，<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*>用于设置密钥存储位置。
  * 在.NET Framework 应用中，Cookie 身份验证中间件使用实现<xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>。 `DataProtectionProvider` 提供对身份验证 cookie 有效负载数据进行加密和解密的数据保护服务。 `DataProtectionProvider`实例都独立于应用的其他部分使用的数据保护系统。 [DataProtectionProvider.Create (System.IO.DirectoryInfo、 操作\<IDataProtectionBuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*)接受<xref:System.IO.DirectoryInfo>指定用于数据保护密钥存储的位置。
* `DataProtectionProvider` 需要[Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet 包：
  * 在 ASP.NET Core 2.x 应用中，引用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。
  * 在.NET Framework 应用程序添加到的包引用[Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)。
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 设置的常见应用程序。

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a>共享 ASP.NET Core 应用之间的身份验证 cookie

当使用 ASP.NET Core标识：

* 数据保护密钥和应用名称必须在应用之间共享。 常见的密钥存储位置提供给<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*>下面的示例中的方法。 使用<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>来配置常见的共享的应用名称 (`SharedCookieApp`下面的示例中)。 有关详细信息，请参阅 <xref:security/data-protection/configuration/overview> 。
* 使用<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*>扩展方法，以设置 cookie 的数据保护服务。
* 在以下示例中，身份验证类型设置为`Identity.Application`默认情况下。

在 `Startup.ConfigureServices`中：

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

当使用 ASP.NET Core 标识不直接的 cookie，配置数据保护和身份验证中的`Startup.ConfigureServices`。 在以下示例中，身份验证类型设置为`Identity.Application`:

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

托管在子域之间共享 cookie 的应用，指定在一个公共域[Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain)属性。 若要在应用之间共享 cookie `contoso.com`，如`first_subdomain.contoso.com`并`second_subdomain.contoso.com`，指定`Cookie.Domain`作为`.contoso.com`:

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>加密静态数据保护密钥

对于生产部署，配置`DataProtectionProvider`来加密静态使用 DPAPI 或 x509 证书的密钥。 有关详细信息，请参阅 <xref:security/data-protection/implementation/key-encryption-at-rest> 。 在以下示例中，证书指纹提供给<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>身份验证之间共享 cookie ASP.NET 4.x 和 ASP.NET Core 应用

使用 Katana Cookie 身份验证中间件的 ASP.NET 4.x 应用程序可以配置为生成 ASP.NET Core Cookie 身份验证中间件与兼容的身份验证 cookie。 这样，在几个步骤中的大型站点的单个应用升级同时在站点之间提供顺畅的 SSO 体验。

当应用使用 Katana Cookie 身份验证中间件时，它将调用`UseCookieAuthentication`在项目的*Startup.Auth.cs*文件。 ASP.NET 4.x web 应用程序项目创建与 Visual Studio 2013 和更高版本默认情况下使用 Katana Cookie 身份验证中间件。 尽管`UseCookieAuthentication`已过时或不受支持的 ASP.NET Core 应用程序，调用`UseCookieAuthentication`ASP.NET 中使用 Katana Cookie 身份验证中间件的 4.x 应用程序是否有效。

ASP.NET 4.x 应用程序必须面向.NET Framework 4.5.1 或更高版本。 否则，无法安装所需的 NuGet 包。

若要共享 ASP.NET 4.x 应用程序和 ASP.NET Core 应用之间的身份验证 cookie，请先配置 ASP.NET Core 应用，如中所述[共享在 ASP.NET Core 应用程序之间的身份验证 cookie](#share-authentication-cookies-among-aspnet-core-apps)部分，然后配置 ASP.NET 4.x 应用作为如下所示。

确认应用的包已更新到最新版本。 安装[Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/)到每个 ASP.NET 4.x 应用程序的包。

找到并修改对调用`UseCookieAuthentication`:

* 更改要使用的 ASP.NET Core Cookie 身份验证中间件的名称匹配的 cookie 名称 (`.AspNet.SharedCookie`在示例中)。
* 在以下示例中，身份验证类型设置为`Identity.Application`。
* 提供的一个实例`DataProtectionProvider`初始化为常见的数据保护密钥存储位置。
* 确认应用程序名称已设置为使用共享身份验证 cookie 的所有应用的常见应用程序名称 (`SharedCookieApp`在示例中)。

如果未设置`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`并`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`，将<xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier>到区别开来的唯一用户的声明。

*App_Start/Startup.Auth.cs*:

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

当生成用户标识，身份验证类型时 (`Identity.Application`) 中定义的类型必须匹配`AuthenticationType`集`UseCookieAuthentication`中*App_Start/Startup.Auth.cs*。

*Models/IdentityModels.cs*:

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

## <a name="use-a-common-user-database"></a>使用常见的用户数据库

当应用使用相同的标识架构 （标识的相同版本），确认每个应用的标识系统指向同一个用户数据库。 否则，标识系统将生成在运行时失败时它将尝试匹配对其数据库中的信息的身份验证 cookie 中的信息。

在应用之间不同标识架构时，通常因为应用程序正在使用不同的标识版本，共享通用基于标识的最新版本的数据库不可能而无需重新映射和其他应用程序的标识架构中添加列。 通常会更有效地升级其他应用程序以使用最新的标识版本，以便可以由应用程序共享通用的数据库。

## <a name="additional-resources"></a>其他资源

* <xref:host-and-deploy/web-farm>
