---
title: 与 ASP.NET 和 ASP.NET Core 共享在应用之间的 cookie
author: rick-anderson
description: 了解如何共享身份验证 cookie 之间 ASP.NET 4.x 和 ASP.NET Core 应用。
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: security/cookie-sharing
ms.openlocfilehash: 7a049ed8787808e228859afc051b8697a6261c21
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068305"
---
# <a name="share-cookies-among-apps-with-aspnet-and-aspnet-core"></a><span data-ttu-id="cc1dd-103">与 ASP.NET 和 ASP.NET Core 共享在应用之间的 cookie</span><span class="sxs-lookup"><span data-stu-id="cc1dd-103">Share cookies among apps with ASP.NET and ASP.NET Core</span></span>

<span data-ttu-id="cc1dd-104">通过[Rick Anderson](https://twitter.com/RickAndMSFT)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cc1dd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cc1dd-105">网站通常包含单独的 web 应用程序协同工作。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="cc1dd-106">若要提供单一登录 (SSO) 体验，站点内的 web 应用必须共享身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="cc1dd-107">若要支持这种情况下，数据保护堆栈允许在共享 Katana cookie 身份验证和 ASP.NET Core cookie 身份验证票证。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="cc1dd-108">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="cc1dd-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="cc1dd-109">此示例演示了在使用 cookie 身份验证的三个应用之间共享 cookie:</span><span class="sxs-lookup"><span data-stu-id="cc1dd-109">The sample illustrates cookie sharing across three apps that use cookie authentication:</span></span>

* <span data-ttu-id="cc1dd-110">ASP.NET Core 2.0 Razor 页应用而无需使用[ASP.NET Core 标识](xref:security/authentication/identity)</span><span class="sxs-lookup"><span data-stu-id="cc1dd-110">ASP.NET Core 2.0 Razor Pages app without using [ASP.NET Core Identity](xref:security/authentication/identity)</span></span>
* <span data-ttu-id="cc1dd-111">使用 ASP.NET Core 标识的 ASP.NET Core 2.0 MVC 应用程序</span><span class="sxs-lookup"><span data-stu-id="cc1dd-111">ASP.NET Core 2.0 MVC app with ASP.NET Core Identity</span></span>
* <span data-ttu-id="cc1dd-112">使用 ASP.NET 标识的 ASP.NET Framework 4.6.1 MVC 应用程序</span><span class="sxs-lookup"><span data-stu-id="cc1dd-112">ASP.NET Framework 4.6.1 MVC app with ASP.NET Identity</span></span>

<span data-ttu-id="cc1dd-113">在下面的示例：</span><span class="sxs-lookup"><span data-stu-id="cc1dd-113">In the examples that follow:</span></span>

* <span data-ttu-id="cc1dd-114">身份验证 cookie 名称设置为公用值的`.AspNet.SharedCookie`。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-114">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="cc1dd-115">`AuthenticationType`设置为`Identity.Application`显式或默认情况下。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-115">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="cc1dd-116">常见的应用名称用于启用数据保护系统共享数据保护密钥 (`SharedCookieApp`)。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-116">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* `Identity.Application` <span data-ttu-id="cc1dd-117">使用作为身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-117">is used as the authentication scheme.</span></span> <span data-ttu-id="cc1dd-118">使用任何方案，必须一致地使用它*内部和跨*共享的 cookie 应用作为默认方案或通过显式设置。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-118">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="cc1dd-119">加密和解密 cookie，因此，必须在应用之间使用一致的方案时，使用方案。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-119">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="cc1dd-120">一种常见[数据保护密钥](xref:security/data-protection/implementation/key-management)使用存储位置。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-120">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span> <span data-ttu-id="cc1dd-121">示例应用程序使用名为的文件夹*KeyRing*要保留数据保护密钥的解决方案的根目录。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-121">The sample app uses a folder named *KeyRing* at the root of the solution to hold the data protection keys.</span></span>
* <span data-ttu-id="cc1dd-122">在 ASP.NET Core 应用中， [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)用于设置的密钥存储位置。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-122">In the ASP.NET Core apps, [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) is used to set the key storage location.</span></span> <span data-ttu-id="cc1dd-123">[SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname)用于配置公用的共享的应用名。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-123">[SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) is used to configure a common shared app name.</span></span>
* <span data-ttu-id="cc1dd-124">在.NET Framework 应用中，cookie 身份验证中间件使用实现[DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider)。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-124">In the .NET Framework app, the cookie authentication middleware uses an implementation of [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider).</span></span> `DataProtectionProvider` <span data-ttu-id="cc1dd-125">提供对身份验证 cookie 有效负载数据进行加密和解密的数据保护服务。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-125">provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="cc1dd-126">`DataProtectionProvider`实例都独立于应用的其他部分使用的数据保护系统。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-126">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span>
  * <span data-ttu-id="cc1dd-127">[DataProtectionProvider.Create (System.IO.DirectoryInfo、 操作\<IDataProtectionBuilder >)](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider.create?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionProvider_Create_System_IO_DirectoryInfo_System_Action_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder__)接受[DirectoryInfo](/dotnet/api/system.io.directoryinfo)指定用于数据保护密钥存储的位置。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-127">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider.create?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionProvider_Create_System_IO_DirectoryInfo_System_Action_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder__) accepts a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) to specify the location for data protection key storage.</span></span> <span data-ttu-id="cc1dd-128">示例应用程序提供的路径*KeyRing*向文件夹`DirectoryInfo`。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-128">The sample app provides the path of the *KeyRing* folder to `DirectoryInfo`.</span></span> <span data-ttu-id="cc1dd-129">[DataProtectionBuilderExtensions.SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionBuilderExtensions_SetApplicationName_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_)设置的常见应用程序。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-129">[DataProtectionBuilderExtensions.SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionBuilderExtensions_SetApplicationName_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_) sets the common app name.</span></span>
  * <span data-ttu-id="cc1dd-130">[DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider)需要[Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-130">[DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package.</span></span> <span data-ttu-id="cc1dd-131">若要对 ASP.NET Core 2.1 和更高版本的应用程序中获取此包，引用[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-131">To obtain this package for ASP.NET Core 2.1 and later apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="cc1dd-132">当面向.NET Framework，添加到包引用`Microsoft.AspNetCore.DataProtection.Extensions`。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-132">When targeting the .NET Framework, add a package reference to `Microsoft.AspNetCore.DataProtection.Extensions`.</span></span>

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a><span data-ttu-id="cc1dd-133">共享 ASP.NET Core 应用之间的身份验证 cookie</span><span class="sxs-lookup"><span data-stu-id="cc1dd-133">Share authentication cookies among ASP.NET Core apps</span></span>

<span data-ttu-id="cc1dd-134">当使用 ASP.NET Core标识：</span><span class="sxs-lookup"><span data-stu-id="cc1dd-134">When using ASP.NET Core Identity:</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="cc1dd-135">在中`ConfigureServices`方法，请使用[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie)扩展方法，以设置 cookie 的数据保护服务。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-135">In the `ConfigureServices` method, use the [ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie) extension method to set up the data protection service for cookies.</span></span>

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.Core/Startup.cs?name=snippet1)]

<span data-ttu-id="cc1dd-136">数据保护密钥和应用名称必须在应用之间共享。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-136">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="cc1dd-137">在示例应用中，`GetKeyRingDirInfo`返回到的公共密钥存储位置[PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)方法。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-137">In the sample apps, `GetKeyRingDirInfo` returns the common key storage location to the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) method.</span></span> <span data-ttu-id="cc1dd-138">使用[SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname)来配置常见的共享的应用名称 (`SharedCookieApp`示例中)。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-138">Use [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) to configure a common shared app name (`SharedCookieApp` in the sample).</span></span> <span data-ttu-id="cc1dd-139">有关详细信息，请参阅[配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-139">For more information, see [Configuring Data Protection](xref:security/data-protection/configuration/overview).</span></span>

<span data-ttu-id="cc1dd-140">托管在子域之间共享 cookie 的应用，指定在一个公共域[Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain)属性。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-140">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain) property.</span></span> <span data-ttu-id="cc1dd-141">若要在应用之间共享 cookie `contoso.com`，如`first_subdomain.contoso.com`并`second_subdomain.contoso.com`，指定`Cookie.Domain`作为`.contoso.com`:</span><span class="sxs-lookup"><span data-stu-id="cc1dd-141">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

<span data-ttu-id="cc1dd-142">请参阅*CookieAuthWithIdentity.Core*项目中[示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/)([如何下载](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-142">See the *CookieAuthWithIdentity.Core* project in the [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="cc1dd-143">在中`Configure`方法，请使用[CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions)设置：</span><span class="sxs-lookup"><span data-stu-id="cc1dd-143">In the `Configure` method, use the [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions) to set up:</span></span>

* <span data-ttu-id="cc1dd-144">Cookie 数据保护服务。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-144">The data protection service for cookies.</span></span>
* <span data-ttu-id="cc1dd-145">`AuthenticationScheme`以匹配 ASP.NET 4.x。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-145">The `AuthenticationScheme` to match ASP.NET 4.x.</span></span>

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

<span data-ttu-id="cc1dd-146">当直接使用 cookie:</span><span class="sxs-lookup"><span data-stu-id="cc1dd-146">When using cookies directly:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](cookie-sharing/sample/CookieAuth.Core/Startup.cs?name=snippet1)]

<span data-ttu-id="cc1dd-147">数据保护密钥和应用名称必须在应用之间共享。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-147">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="cc1dd-148">在示例应用中，`GetKeyRingDirInfo`返回到的公共密钥存储位置[PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)方法。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-148">In the sample apps, `GetKeyRingDirInfo` returns the common key storage location to the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) method.</span></span> <span data-ttu-id="cc1dd-149">使用[SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname)来配置常见的共享的应用名称 (`SharedCookieApp`示例中)。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-149">Use [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) to configure a common shared app name (`SharedCookieApp` in the sample).</span></span> <span data-ttu-id="cc1dd-150">有关详细信息，请参阅[配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-150">For more information, see [Configuring Data Protection](xref:security/data-protection/configuration/overview).</span></span>

<span data-ttu-id="cc1dd-151">托管在子域之间共享 cookie 的应用，指定在一个公共域[Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain)属性。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-151">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain) property.</span></span> <span data-ttu-id="cc1dd-152">若要在应用之间共享 cookie `contoso.com`，如`first_subdomain.contoso.com`并`second_subdomain.contoso.com`，指定`Cookie.Domain`作为`.contoso.com`:</span><span class="sxs-lookup"><span data-stu-id="cc1dd-152">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

<span data-ttu-id="cc1dd-153">请参阅*CookieAuth.Core*项目中[示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/)([如何下载](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-153">See the *CookieAuth.Core* project in the [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

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

## <a name="encrypting-data-protection-keys-at-rest"></a><span data-ttu-id="cc1dd-154">加密静态数据保护密钥</span><span class="sxs-lookup"><span data-stu-id="cc1dd-154">Encrypting data protection keys at rest</span></span>

<span data-ttu-id="cc1dd-155">对于生产部署，配置`DataProtectionProvider`来加密静态使用 DPAPI 或 x509 证书的密钥。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-155">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="cc1dd-156">请参阅[密钥静态加密](xref:security/data-protection/implementation/key-encryption-at-rest)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-156">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

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

## <a name="sharing-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="cc1dd-157">共享身份验证 cookie 之间 ASP.NET 4.x 和 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="cc1dd-157">Sharing authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="cc1dd-158">ASP.NET 4.x 应用程序使用 Katana cookie 身份验证中间件该对话框可以配置为生成与 ASP.NET Core cookie 身份验证中间件兼容的身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-158">ASP.NET 4.x apps which use Katana cookie authentication middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core cookie authentication middleware.</span></span> <span data-ttu-id="cc1dd-159">这样，段落将大型站点的单个应用升级同时在站点之间提供顺畅的 SSO 体验。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-159">This allows upgrading a large site's individual apps piecemeal while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="cc1dd-160">当应用使用 Katana cookie 身份验证中间件时，它将调用`UseCookieAuthentication`在项目的*Startup.Auth.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-160">When an app uses Katana cookie authentication middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="cc1dd-161">ASP.NET 4.x web 应用程序项目创建与 Visual Studio 2013 和更高版本默认情况下使用 Katana cookie 身份验证中间件。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-161">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana cookie authentication middleware by default.</span></span> <span data-ttu-id="cc1dd-162">尽管`UseCookieAuthentication`已过时，不支持对于 ASP.NET Core 应用程序，调用`UseCookieAuthentication`在 ASP.NET 4.x 应用程序使用 Katana cookie 身份验证中间件是有效的。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-162">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana cookie authentication middleware is valid.</span></span>

<span data-ttu-id="cc1dd-163">ASP.NET 4.x 应用程序必须面向.NET Framework 4.5.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-163">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="cc1dd-164">否则，无法安装所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-164">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="cc1dd-165">若要共享 ASP.NET 4.x 应用程序和 ASP.NET Core 应用程序之间的身份验证 cookie，如前所述，将 ASP.NET Core 应用配置，然后通过执行以下步骤配置 ASP.NET 4.x 应用程序：</span><span class="sxs-lookup"><span data-stu-id="cc1dd-165">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated above, then configure the ASP.NET 4.x app by following these steps:</span></span>

1. <span data-ttu-id="cc1dd-166">安装包[Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/)到每个 ASP.NET 4.x 应用程序。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-166">Install the package [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) into each ASP.NET 4.x app.</span></span>

2. <span data-ttu-id="cc1dd-167">在中*Startup.Auth.cs*，找到调用`UseCookieAuthentication`和对其进行修改，如下所示。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-167">In *Startup.Auth.cs*, locate the call to `UseCookieAuthentication` and modify it as follows.</span></span> <span data-ttu-id="cc1dd-168">更改要匹配使用 ASP.NET Core cookie 身份验证中间件的名称的 cookie 名称。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-168">Change the cookie name to match the name used by the ASP.NET Core cookie authentication middleware.</span></span> <span data-ttu-id="cc1dd-169">提供的一个实例`DataProtectionProvider`初始化为常见的数据保护密钥存储位置。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-169">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span> <span data-ttu-id="cc1dd-170">请确保应用程序名称设置为使用共享 cookie 的所有应用的常见应用程序名称`SharedCookieApp`示例应用程序中。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-170">Make sure that the app name is set to the common app name used by all apps that share cookies, `SharedCookieApp` in the sample app.</span></span>

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.NETFramework/CookieAuthWithIdentity.NETFramework/App_Start/Startup.Auth.cs?name=snippet1)]

<span data-ttu-id="cc1dd-171">请参阅*CookieAuthWithIdentity.NETFramework*项目中[示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/)([如何下载](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-171">See the *CookieAuthWithIdentity.NETFramework* project in the [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="cc1dd-172">身份验证类型时生成的用户标识，必须与中定义的类型匹配`AuthenticationType`设置与`UseCookieAuthentication`。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-172">When generating a user identity, the authentication type must match the type defined in `AuthenticationType` set with `UseCookieAuthentication`.</span></span>

<span data-ttu-id="cc1dd-173">*Models/IdentityModels.cs*:</span><span class="sxs-lookup"><span data-stu-id="cc1dd-173">*Models/IdentityModels.cs*:</span></span>

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.NETFramework/CookieAuthWithIdentity.NETFramework/Models/IdentityModels.cs?name=snippet1)]

## <a name="use-a-common-user-database"></a><span data-ttu-id="cc1dd-174">使用常见的用户数据库</span><span class="sxs-lookup"><span data-stu-id="cc1dd-174">Use a common user database</span></span>

<span data-ttu-id="cc1dd-175">确认每个应用的标识系统指向同一个用户数据库。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-175">Confirm that the identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="cc1dd-176">否则，标识系统将生成在运行时失败时它将尝试匹配对其数据库中的信息的身份验证 cookie 中的信息。</span><span class="sxs-lookup"><span data-stu-id="cc1dd-176">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cc1dd-177">其他资源</span><span class="sxs-lookup"><span data-stu-id="cc1dd-177">Additional resources</span></span>

<xref:host-and-deploy/web-farm>
