---
title: ASP.NET Core 中的一般数据保护条例（GDPR）支持
author: rick-anderson
description: 了解如何访问 ASP.NET Core web 应用中的 GDPR 扩展点。
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/gdpr
ms.openlocfilehash: 2e21f54ebfcb55be2b97da217b92a39843b5d702
ms.sourcegitcommit: 6c7a149168d2c4d747c36de210bfab3abd60809a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "83003203"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>ASP.NET Core 中的欧盟一般数据保护条例（GDPR）支持

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core 提供了 Api 和模板来帮助满足某些[欧盟一般数据保护条例（GDPR）](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en)要求：

::: moniker range=">= aspnetcore-3.0"

* 项目模板包括扩展点和用作存根标记，你可以将其替换为你的隐私和 cookie 使用策略。
* *Pages/私密. cshtml*页面或*Views/Home/私密*视图提供了一个页面，用于详细描述您的站点的隐私策略。

若要启用默认的 cookie 许可功能，如在 ASP.NET Core 3.0 模板生成的应用中的 ASP.NET Core 2.2 模板中找到的功能：

* 将`using Microsoft.AspNetCore.Http`添加到 using 指令列表。
* 将[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)添加`Startup.ConfigureServices`到[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) ， `Startup.Configure`并将其添加到：

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* 将 cookie 同意部分添加到 *_Layout 的 cshtml*文件中：

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* 将* \_CookieConsentPartial*文件添加到项目：

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* 选择本文的 ASP.NET Core 2.2 版本，了解 cookie 许可功能。

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* 项目模板包括扩展点和用作存根标记，你可以将其替换为你的隐私和 cookie 使用策略。
* 使用 cookie 同意功能，你可以要求你提供（并跟踪）用户同意以存储个人信息。 如果用户未同意数据收集，并且应用已将[CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded)设置为`true`，则不重要的 cookie 不会发送到浏览器。
* Cookie 可以标记为必要。 即使用户尚未同意并禁用跟踪，也会将重要 cookie 发送到浏览器。
* 禁用跟踪后[，TempData 和会话 cookie](#tempdata)不起作用。
* "[标识管理](#pd)" 页提供了一个链接，用于下载和删除用户数据。

该[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)允许你测试添加到 ASP.NET Core 2.1 模板的大多数 GDPR 扩展点和 api。 有关测试说明，请参阅[自述](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)文件。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>模板生成的代码中的 ASP.NET Core GDPR 支持

用项目模板创建 Razor Pages 和 MVC 项目包含以下 GDPR 支持：

* [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)和[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)是在`Startup`类中设置的。
* CookieConsentPartial 分部[视图](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)。 * \_* 此文件中包括 "**接受**" 按钮。 用户单击 "**接受**" 按钮时，会提供许可来存储 cookie。
* *Pages/私密. cshtml*页面或*Views/Home/私密*视图提供了一个页面，用于详细描述您的站点的隐私策略。 CookieConsentPartial 文件生成指向隐私页面的链接。 * \_*
* 对于通过单独用户帐户创建的应用，"管理" 页提供了下载和删除[个人用户数据](#pd)的链接。

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a>CookiePolicyOptions 和 UseCookiePolicy

[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)在中`Startup.ConfigureServices`进行初始化：

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)在中`Startup.Configure`调用：

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_cookieconsentpartialcshtml-partial-view"></a>\_CookieConsentPartial 分部视图

CookieConsentPartial 分部视图： * \_*

[!code-html[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

此部分内容：

* 获取用户的跟踪状态。 如果应用配置为要求同意，则用户必须同意才能跟踪 cookie。 如果需要同意，cookie 许可面板将固定在由* \_布局 cshtml*文件创建的导航栏的顶部。
* 提供一个 HTML `<p>`元素，用于汇总隐私和 cookie 使用策略。
* 提供指向隐私页面或视图的链接，您可以在其中详细说明网站的隐私策略。

## <a name="essential-cookies"></a>重要 cookie

如果尚未提供存储 cookie 的许可，则仅将标记为 "重要" 的 cookie 发送到浏览器。 以下代码使 cookie 非常重要：

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a>TempData 提供程序和会话状态 cookie 不重要

[TempData 提供程序](xref:fundamentals/app-state#tempdata)cookie 并不重要。 如果禁用跟踪，则 TempData 提供程序不起作用。 若要在禁用跟踪时启用 TempData 提供程序，请在中`Startup.ConfigureServices`将 TempData cookie 标记为重要：

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

[会话状态](xref:fundamentals/app-state)cookie 并不重要。 禁用跟踪后，会话状态不起作用。 以下代码使会话 cookie 非常重要：

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>个人数据

ASP.NET Core 通过单独用户帐户创建的应用包括下载和删除个人数据的代码。

选择用户名，然后选择 "**个人数据**"：

![管理个人数据页](gdpr/_static/pd.png)

注意：

* 若要生成`Account/Manage`代码，请参阅[基架标识](xref:security/authentication/scaffold-identity)。
* "**删除**" 和 "**下载**" 链接仅作用于默认标识数据。 必须扩展用于创建自定义用户数据的应用，以删除/下载自定义用户数据。 有关详细信息，请参阅[向标识添加、下载和删除自定义用户数据](xref:security/authentication/add-user-data)。
* 由于[外键](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152)，通过级联删除行为删除用户时， `AspNetUserTokens`将删除存储在标识数据库表中的用户的已保存令牌。
* [外部提供程序身份验证](xref:security/authentication/social/index)（如 Facebook 和 Google）在接受 cookie 策略之前不可用。

::: moniker-end

## <a name="encryption-at-rest"></a>静态加密

某些数据库和存储机制允许静态加密。 静态加密：

* 自动加密存储的数据。
* 对于访问数据的软件，不对其进行配置、编程或其他操作。
* 是最简单且最安全的选项。
* 允许数据库管理密钥和加密。

例如：

* Microsoft SQL 和 Azure SQL 提供[透明数据加密](/sql/relational-databases/security/encryption/transparent-data-encryption)（TDE）。
* [默认情况下，SQL Azure 加密数据库](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* [默认情况下，加密 Azure blob、文件、表和队列存储](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/)。

对于不提供静态内置加密的数据库，您可以使用磁盘加密来提供相同的保护。 例如：

* [适用于 Windows Server 的 BitLocker](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux：
  * [eCryptfs](https://launchpad.net/ecryptfs)
  * [EncFS](https://github.com/vgough/encfs)。

## <a name="additional-resources"></a>其他资源

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [GDPR-在 ASP.NET Core 中添加 "撤消许可" 按钮](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
