---
title: 在 ASP.NET Core中的常规数据保护法规 (GDPR) 支持
author: rick-anderson
description: 了解如何访问 ASP.NET Core web 应用中的 GDPR 扩展点。
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
uid: security/gdpr
ms.openlocfilehash: 01d2f8943c0995c1400122b89c4ca7c459a85279
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724564"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>在 ASP.NET Core 欧洲常规数据保护法规 (GDPR) 支持

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core 提供 Api 和模板，以帮助满足一些[欧洲常规数据保护法规 (GDPR)](https://www.eugdpr.org/)要求：

::: moniker range=">= aspnetcore-3.0"

* 项目模板包含扩展点以及您可以替换为您的隐私和 cookie 的使用策略的存根的标记。
* *Pages/Privacy.cshtml*页或*Views/Home/Privacy.cshtml*视图提供了详细介绍站点的隐私策略的页。

若要启用默认 cookie 同意的情况下功能类似的位于 ASP.NET Core 3.0 模板生成应用程序中的 ASP.NET Core 2.2 模板：

* 添加[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)太`Startup.ConfigureServices`并[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)到`Startup.Configure`:

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* 添加到 cookie 同意分部 *_Layout.cshtml*文件：

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* 添加 *\_CookieConsentPartial.cshtml*文件到项目：

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* 选择本文，了解 cookie 同意的情况下功能的 ASP.NET Core 2.2 版本。

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* 项目模板包含扩展点以及您可以替换为您的隐私和 cookie 的使用策略的存根的标记。
* 用于存储的个人信息中，cookie 许可功能，可要求 （和跟踪） 同意的情况下从你的用户。 如果某个用户尚未同意数据收集，并且应用了[CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded)设置为`true`，非必需 cookie 不会发送到浏览器。
* Cookie 可以标记为重要。 用户尚未同意和跟踪被禁用时，甚至必不可少的 cookie 发送到浏览器。
* [TempData 和会话 cookie](#tempdata)跟踪处于禁用状态时无法正常工作。
* [标识管理](#pd)页提供了用于下载和删除用户数据的链接。

[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)允许测试大部分 GDPR 扩展点和 Api 添加到 ASP.NET Core 2.1 模板。 请参阅[自述文件](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)测试说明的文件。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>支持模板生成的代码中的 ASP.NET Core GDPR

Razor 页面和 MVC 使用的项目模板创建的项目包括以下 GDPR 支持：

* [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)并[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)中设置`Startup`类。
* *\_CookieConsentPartial.cshtml* [分部视图](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)。 **接受**按钮包含在此文件。 当用户单击**接受**按钮，同意将 cookie 提供。
* *Pages/Privacy.cshtml*页或*Views/Home/Privacy.cshtml*视图提供了详细介绍站点的隐私策略的页。 *\_CookieConsentPartial.cshtml*文件可生成隐私页的链接。
* 对于使用单个用户帐户创建的应用程序，管理页提供链接以下载和删除[个人用户数据](#pd)。

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a>CookiePolicyOptions 和 UseCookiePolicy

[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)中初始化`Startup.ConfigureServices`:

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)名为`Startup.Configure`:

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="cookieconsentpartialcshtml-partial-view"></a>\_CookieConsentPartial.cshtml partial view

*\_CookieConsentPartial.cshtml*分部视图：

[!code-html[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

此部分中：

* 获取用户跟踪的状态。 如果应用程序配置为需要同意的情况下，用户必须同意使用之前可以跟踪 cookie。 如果需要同意的情况下，cookie 同意面板固定的创建的导航栏的顶部 *\_Layout.cshtml*文件。
* 提供了一个 HTML`<p>`元素以汇总您的隐私和 cookie 使用策略。
* 提供指向隐私页面或视图，其中详细介绍站点的隐私策略。

## <a name="essential-cookies"></a>基本 cookie

如果同意来存储 cookie 未对其提供，仅标记为重要的 cookie 会被发送到浏览器。 下面的代码使 cookie 重要：

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a>TempData 提供程序和会话状态的 cookie 不重要

[TempData 提供程序](xref:fundamentals/app-state#tempdata)cookie 不是必需的。 如果禁用跟踪，TempData 提供程序不起作用。 若要启用 TempData 提供程序跟踪处于禁用状态时，将 TempData cookie 标记为重要中`Startup.ConfigureServices`:

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

[会话状态](xref:fundamentals/app-state)cookie 不重要。 跟踪处于禁用状态时，会话状态不起作用。 下面的代码使基本会话 cookie:

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>个人数据

使用个人用户帐户创建的 ASP.NET Core 应用包括要下载和删除个人数据的代码。

选择的用户名称，然后选择**个人数据**:

![管理个人数据页](gdpr/_static/pd.png)

注意：

* 若要生成`Account/Manage`代码，请参阅[基架标识](xref:security/authentication/scaffold-identity)。
* **删除**并**下载**链接只作用于的默认标识数据。 创建自定义用户数据的应用，必须进行扩展，以删除/下载自定义用户数据。 有关详细信息，请参阅[添加、 下载和删除自定义用户数据到标识](xref:security/authentication/add-user-data)。
* 保存用户的标识数据库表中存储的令牌`AspNetUserTokens`时通过级联删除方式，由于删除了用户会被删除[外键](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152)。
* [外部提供程序身份验证](xref:security/authentication/social/index)，如 Facebook 和 Google，不可用之前接受 cookie 策略。

::: moniker-end

## <a name="encryption-at-rest"></a>静态加密

某些数据库和存储机制允许静态加密。 静态加密：

* 自动对存储的数据进行加密。
* 无需配置、 编程中或其他工作的软件的访问的数据进行加密。
* 是最简单且最安全的选项。
* 允许管理密钥和加密的数据库。

例如：

* Microsoft SQL 和 Azure SQL 提供了[透明数据加密](/sql/relational-databases/security/encryption/transparent-data-encryption)(TDE)。
* [SQL Azure 默认情况下加密数据库](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* [默认情况下加密 azure Blob、 文件、 表和队列存储](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/)。

对于未提供静态的内置加密的数据库，您可能能够使用磁盘加密来提供相同的保护。 例如：

* [BitLocker for Windows Server](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux：
  * [eCryptfs](https://launchpad.net/ecryptfs)
  * [EncFS](https://github.com/vgough/encfs)。

## <a name="additional-resources"></a>其他资源

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
