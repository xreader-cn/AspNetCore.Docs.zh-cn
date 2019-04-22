---
title: 与 ASP.NET 和 ASP.NET Core 共享在应用之间的 cookie
author: rick-anderson
description: 了解如何共享身份验证 cookie 之间 ASP.NET 4.x 和 ASP.NET Core 应用。
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: security/cookie-sharing
ms.openlocfilehash: 7a049ed8787808e228859afc051b8697a6261c21
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068305"
---
# <a name="share-cookies-among-apps-with-aspnet-and-aspnet-core"></a>与 ASP.NET 和 ASP.NET Core 共享在应用之间的 cookie

通过[Rick Anderson](https://twitter.com/RickAndMSFT)和[Luke Latham](https://github.com/guardrex)

网站通常包含单独的 web 应用程序协同工作。 若要提供单一登录 (SSO) 体验，站点内的 web 应用必须共享身份验证 cookie。 若要支持这种情况下，数据保护堆栈允许在共享 Katana cookie 身份验证和 ASP.NET Core cookie 身份验证票证。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/)（[如何下载](xref:index#how-to-download-a-sample)）

此示例演示了在使用 cookie 身份验证的三个应用之间共享 cookie:

* ASP.NET Core 2.0 Razor 页应用而无需使用[ASP.NET Core 标识](xref:security/authentication/identity)
* 使用 ASP.NET Core 标识的 ASP.NET Core 2.0 MVC 应用程序
* 使用 ASP.NET 标识的 ASP.NET Framework 4.6.1 MVC 应用程序

在下面的示例：

* 身份验证 cookie 名称设置为公用值的`.AspNet.SharedCookie`。
* `AuthenticationType`设置为`Identity.Application`显式或默认情况下。
* 常见的应用名称用于启用数据保护系统共享数据保护密钥 (`SharedCookieApp`)。
* `Identity.Application` 使用作为身份验证方案。 使用任何方案，必须一致地使用它*内部和跨*共享的 cookie 应用作为默认方案或通过显式设置。 加密和解密 cookie，因此，必须在应用之间使用一致的方案时，使用方案。
* 一种常见[数据保护密钥](xref:security/data-protection/implementation/key-management)使用存储位置。 示例应用程序使用名为的文件夹*KeyRing*要保留数据保护密钥的解决方案的根目录。
* 在 ASP.NET Core 应用中， [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)用于设置的密钥存储位置。 [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname)用于配置公用的共享的应用名。
* 在.NET Framework 应用中，cookie 身份验证中间件使用实现[DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider)。 `DataProtectionProvider` 提供对身份验证 cookie 有效负载数据进行加密和解密的数据保护服务。 `DataProtectionProvider`实例都独立于应用的其他部分使用的数据保护系统。
  * [DataProtectionProvider.Create (System.IO.DirectoryInfo、 操作\<IDataProtectionBuilder >)](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider.create?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionProvider_Create_System_IO_DirectoryInfo_System_Action_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder__)接受[DirectoryInfo](/dotnet/api/system.io.directoryinfo)指定用于数据保护密钥存储的位置。 示例应用程序提供的路径*KeyRing*向文件夹`DirectoryInfo`。 [DataProtectionBuilderExtensions.SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionBuilderExtensions_SetApplicationName_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_)设置的常见应用程序。
  * [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider)需要[Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet 包。 若要对 ASP.NET Core 2.1 和更高版本的应用程序中获取此包，引用[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app)。 当面向.NET Framework，添加到包引用`Microsoft.AspNetCore.DataProtection.Extensions`。

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a>共享 ASP.NET Core 应用之间的身份验证 cookie

当使用 ASP.NET Core标识：

::: moniker range=">= aspnetcore-2.0"

在中`ConfigureServices`方法，请使用[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie)扩展方法，以设置 cookie 的数据保护服务。

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.Core/Startup.cs?name=snippet1)]

数据保护密钥和应用名称必须在应用之间共享。 在示例应用中，`GetKeyRingDirInfo`返回到的公共密钥存储位置[PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)方法。 使用[SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname)来配置常见的共享的应用名称 (`SharedCookieApp`示例中)。 有关详细信息，请参阅[配置数据保护](xref:security/data-protection/configuration/overview)。

托管在子域之间共享 cookie 的应用，指定在一个公共域[Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain)属性。 若要在应用之间共享 cookie `contoso.com`，如`first_subdomain.contoso.com`并`second_subdomain.contoso.com`，指定`Cookie.Domain`作为`.contoso.com`:

```csharp
options.Cookie.Domain = ".contoso.com";
```

请参阅*CookieAuthWithIdentity.Core*项目中[示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/)([如何下载](xref:index#how-to-download-a-sample))。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

在中`Configure`方法，请使用[CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions)设置：

* Cookie 数据保护服务。
* `AuthenticationScheme`以匹配 ASP.NET 4.x。

```csharp
app.AddIdentity<ApplicationUser, IdentityRole>(options =>
{
    options.Cookies.ApplicationCookie.AuthenticationScheme = 
        "ApplicationCookie";

    var protectionProvider = 
        DataProtectionProvider.Create(
            new DirectoryInfo(@"PATH_TO_KEY_RING_FOLDER"));

    options.Cookies.ApplicationCookie.DataProtectionProvider = 
        protectionProvider;

    options.Cookies.ApplicationCookie.TicketDataFormat = 
        new TicketDataFormat(protectionProvider.CreateProtector(
            "Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationMiddleware", 
            "Cookies", 
            "v2"));
});
```

::: moniker-end

当直接使用 cookie:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](cookie-sharing/sample/CookieAuth.Core/Startup.cs?name=snippet1)]

数据保护密钥和应用名称必须在应用之间共享。 在示例应用中，`GetKeyRingDirInfo`返回到的公共密钥存储位置[PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)方法。 使用[SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname)来配置常见的共享的应用名称 (`SharedCookieApp`示例中)。 有关详细信息，请参阅[配置数据保护](xref:security/data-protection/configuration/overview)。

托管在子域之间共享 cookie 的应用，指定在一个公共域[Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain)属性。 若要在应用之间共享 cookie `contoso.com`，如`first_subdomain.contoso.com`并`second_subdomain.contoso.com`，指定`Cookie.Domain`作为`.contoso.com`:

```csharp
options.Cookie.Domain = ".contoso.com";
```

请参阅*CookieAuth.Core*项目中[示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/)([如何下载](xref:index#how-to-download-a-sample))。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    DataProtectionProvider = 
        DataProtectionProvider.Create(
            new DirectoryInfo(@"PATH_TO_KEY_RING_FOLDER"))
});
```

::: moniker-end

## <a name="encrypting-data-protection-keys-at-rest"></a>加密静态数据保护密钥

对于生产部署，配置`DataProtectionProvider`来加密静态使用 DPAPI 或 x509 证书的密钥。 请参阅[密钥静态加密](xref:security/data-protection/implementation/key-encryption-at-rest)有关详细信息。

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("thumbprint");
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    DataProtectionProvider = DataProtectionProvider.Create(
        new DirectoryInfo(@"PATH_TO_KEY_RING"),
        configure =>
        {
            configure.ProtectKeysWithCertificate("thumbprint");
        })
});
```

::: moniker-end

## <a name="sharing-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>共享身份验证 cookie 之间 ASP.NET 4.x 和 ASP.NET Core 应用

ASP.NET 4.x 应用程序使用 Katana cookie 身份验证中间件该对话框可以配置为生成与 ASP.NET Core cookie 身份验证中间件兼容的身份验证 cookie。 这样，段落将大型站点的单个应用升级同时在站点之间提供顺畅的 SSO 体验。

当应用使用 Katana cookie 身份验证中间件时，它将调用`UseCookieAuthentication`在项目的*Startup.Auth.cs*文件。 ASP.NET 4.x web 应用程序项目创建与 Visual Studio 2013 和更高版本默认情况下使用 Katana cookie 身份验证中间件。 尽管`UseCookieAuthentication`已过时，不支持对于 ASP.NET Core 应用程序，调用`UseCookieAuthentication`在 ASP.NET 4.x 应用程序使用 Katana cookie 身份验证中间件是有效的。

ASP.NET 4.x 应用程序必须面向.NET Framework 4.5.1 或更高版本。 否则，无法安装所需的 NuGet 包。

若要共享 ASP.NET 4.x 应用程序和 ASP.NET Core 应用程序之间的身份验证 cookie，如前所述，将 ASP.NET Core 应用配置，然后通过执行以下步骤配置 ASP.NET 4.x 应用程序：

1. 安装包[Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/)到每个 ASP.NET 4.x 应用程序。

2. 在中*Startup.Auth.cs*，找到调用`UseCookieAuthentication`和对其进行修改，如下所示。 更改要匹配使用 ASP.NET Core cookie 身份验证中间件的名称的 cookie 名称。 提供的一个实例`DataProtectionProvider`初始化为常见的数据保护密钥存储位置。 请确保应用程序名称设置为使用共享 cookie 的所有应用的常见应用程序名称`SharedCookieApp`示例应用程序中。

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.NETFramework/CookieAuthWithIdentity.NETFramework/App_Start/Startup.Auth.cs?name=snippet1)]

请参阅*CookieAuthWithIdentity.NETFramework*项目中[示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/)([如何下载](xref:index#how-to-download-a-sample))。

身份验证类型时生成的用户标识，必须与中定义的类型匹配`AuthenticationType`设置与`UseCookieAuthentication`。

*Models/IdentityModels.cs*:

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.NETFramework/CookieAuthWithIdentity.NETFramework/Models/IdentityModels.cs?name=snippet1)]

## <a name="use-a-common-user-database"></a>使用常见的用户数据库

确认每个应用的标识系统指向同一个用户数据库。 否则，标识系统将生成在运行时失败时它将尝试匹配对其数据库中的信息的身份验证 cookie 中的信息。

## <a name="additional-resources"></a>其他资源

<xref:host-and-deploy/web-farm>
