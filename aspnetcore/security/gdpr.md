---
title: ASP.NET Core 中一般数据保护条例 (GDPR) 支持
author: rick-anderson
description: 了解如何访问 ASP.NET Core web 应用中的 GDPR 扩展点。
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/gdpr
ms.openlocfilehash: 6392a22e316f903da18cd1a91d1eb779d8dde1b3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020010"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="a1ff5-103">欧盟一般数据保护条例 (GDPR) 支持 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a1ff5-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="a1ff5-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a1ff5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a1ff5-105">ASP.NET Core 提供 Api 和模板来帮助满足某些[欧盟一般数据保护条例 (GDPR) ](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en)要求：</span><span class="sxs-lookup"><span data-stu-id="a1ff5-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) requirements:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="a1ff5-106">项目模板包括扩展点和用作存根标记，你可以将其替换为你的隐私和 cookie 使用策略。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-106">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="a1ff5-107">*Pages/私密. cshtml*页面或*Views/Home/私密*视图提供了一个页面，用于详细描述您的站点的隐私策略。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-107">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span>

<span data-ttu-id="a1ff5-108">若要启用默认 cookie 许可功能，如 ASP.NET Core 3.0 模板生成的应用中的 ASP.NET Core 2.2 模板中所示：</span><span class="sxs-lookup"><span data-stu-id="a1ff5-108">To enable the default cookie consent feature like that found in the ASP.NET Core 2.2 templates in an ASP.NET Core 3.0 template generated app:</span></span>

* <span data-ttu-id="a1ff5-109">将添加 `using Microsoft.AspNetCore.Http` 到 using 指令列表。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-109">Add `using Microsoft.AspNetCore.Http` to the list of using directives.</span></span>
* <span data-ttu-id="a1ff5-110">将[ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)添加到 `Startup.ConfigureServices` ，并[使用 Cookie 策略](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) `Startup.Configure` 执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="a1ff5-110">Add [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) to `Startup.ConfigureServices` and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) to `Startup.Configure`:</span></span>

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* <span data-ttu-id="a1ff5-111">将 cookie 同意部分添加到 *_Layout 的 cshtml*文件中：</span><span class="sxs-lookup"><span data-stu-id="a1ff5-111">Add the cookie consent partial to the *_Layout.cshtml* file:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* <span data-ttu-id="a1ff5-112">将\* \_ Cookie ConsentPartial\*文件添加到项目：</span><span class="sxs-lookup"><span data-stu-id="a1ff5-112">Add the *\_CookieConsentPartial.cshtml* file to the project:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* <span data-ttu-id="a1ff5-113">若要了解同意功能，请选择本文的 ASP.NET Core 2.2 版本 cookie 。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-113">Select the ASP.NET Core 2.2 version of this article to read about the cookie consent feature.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="a1ff5-114">项目模板包括扩展点和用作存根标记，你可以将其替换为你的隐私和 cookie 使用策略。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-114">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="a1ff5-115">cookie许可功能允许您 (和跟踪用户的) 同意，以存储个人信息。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-115">A cookie consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="a1ff5-116">如果用户未同意数据收集，并且应用已将[CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded)设置为，则不会将不 `true` 必要 cookie 的发送到浏览器。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-116">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) set to `true`, non-essential cookies aren't sent to the browser.</span></span>
* <span data-ttu-id="a1ff5-117">Cookie可以将 s 标记为必要。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-117">Cookies can be marked as essential.</span></span> <span data-ttu-id="a1ff5-118">cookie即使用户尚未同意并禁用跟踪，也会将重要的发送到浏览器。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-118">Essential cookies are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="a1ff5-119">禁用跟踪后[，TempData 和会话 cookie s](#tempdata)不起作用。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-119">[TempData and Session cookies](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="a1ff5-120">" [ Identity 管理](#pd)" 页提供了一个链接，用于下载和删除用户数据。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-120">The [Identity manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="a1ff5-121">该[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)允许你测试添加到 ASP.NET Core 2.1 模板的大多数 GDPR 扩展点和 api。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-121">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="a1ff5-122">有关测试说明，请参阅[自述](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)文件。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-122">See the [ReadMe](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="a1ff5-123">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="a1ff5-123">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="a1ff5-124">模板生成的代码中的 ASP.NET Core GDPR 支持</span><span class="sxs-lookup"><span data-stu-id="a1ff5-124">ASP.NET Core GDPR support in template-generated code</span></span>

<span data-ttu-id="a1ff5-125">Razor用项目模板创建的页和 MVC 项目包含以下 GDPR 支持：</span><span class="sxs-lookup"><span data-stu-id="a1ff5-125">Razor Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="a1ff5-126">[ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)和[Use Cookie 策略](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)在类中设置 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-126">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="a1ff5-127">\* \_ Cookie ConsentPartial\* [分部视图](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-127">The *\_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="a1ff5-128">此文件中包括 "**接受**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-128">An **Accept** button is included in this file.</span></span> <span data-ttu-id="a1ff5-129">用户单击 "**接受**" 按钮时， cookie 会提供许可。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-129">When the user clicks the **Accept** button, consent to store cookies is provided.</span></span>
* <span data-ttu-id="a1ff5-130">*Pages/私密. cshtml*页面或*Views/Home/私密*视图提供了一个页面，用于详细描述您的站点的隐私策略。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-130">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="a1ff5-131">\* \_ Cookie ConsentPartial\*文件生成指向隐私页面的链接。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-131">The *\_CookieConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="a1ff5-132">对于通过单独用户帐户创建的应用，"管理" 页提供了下载和删除[个人用户数据](#pd)的链接。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-132">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a><span data-ttu-id="a1ff5-133">CookiePolicyOptions 和使用 Cookie 策略</span><span class="sxs-lookup"><span data-stu-id="a1ff5-133">CookiePolicyOptions and UseCookiePolicy</span></span>

<span data-ttu-id="a1ff5-134">[ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)在中进行初始化 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="a1ff5-134">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="a1ff5-135">[使用 Cookie策略](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)在中调用 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="a1ff5-135">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="a1ff5-136">\_CookieConsentPartial 分部视图</span><span class="sxs-lookup"><span data-stu-id="a1ff5-136">\_CookieConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="a1ff5-137">\* \_ Cookie ConsentPartial\*分部视图：</span><span class="sxs-lookup"><span data-stu-id="a1ff5-137">The *\_CookieConsentPartial.cshtml* partial view:</span></span>

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

<span data-ttu-id="a1ff5-138">此部分内容：</span><span class="sxs-lookup"><span data-stu-id="a1ff5-138">This partial:</span></span>

* <span data-ttu-id="a1ff5-139">获取用户的跟踪状态。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-139">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="a1ff5-140">如果应用配置为要求同意，则用户必须同意才能 cookie 跟踪。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-140">If the app is configured to require consent, the user must consent before cookies can be tracked.</span></span> <span data-ttu-id="a1ff5-141">如果需要同意，则 cookie 在由\* \_ 布局 cshtml\*文件创建的导航栏的顶部固定同意面板。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-141">If consent is required, the cookie consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="a1ff5-142">提供一个 HTML `<p>` 元素，用于汇总隐私和 cookie 使用策略。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-142">Provides an HTML `<p>` element to summarize your privacy and cookie use policy.</span></span>
* <span data-ttu-id="a1ff5-143">提供指向隐私页面或视图的链接，您可以在其中详细说明网站的隐私策略。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-143">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-no-loccookies"></a><span data-ttu-id="a1ff5-144">基本 cookie s</span><span class="sxs-lookup"><span data-stu-id="a1ff5-144">Essential cookies</span></span>

<span data-ttu-id="a1ff5-145">如果 cookie 尚未提供许可，则仅将 cookie 标记为 "必需" 的标记发送到浏览器。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-145">If consent to store cookies hasn't been provided, only cookies marked essential are sent to the browser.</span></span> <span data-ttu-id="a1ff5-146">以下代码 cookie 非常重要：</span><span class="sxs-lookup"><span data-stu-id="a1ff5-146">The following code makes a cookie essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a><span data-ttu-id="a1ff5-147">TempData 提供程序和会话状态 cookie 不必要</span><span class="sxs-lookup"><span data-stu-id="a1ff5-147">TempData provider and session state cookies aren't essential</span></span>

<span data-ttu-id="a1ff5-148">[TempData 提供程序](xref:fundamentals/app-state#tempdata) cookie 不是必需的。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-148">The [TempData provider](xref:fundamentals/app-state#tempdata) cookie isn't essential.</span></span> <span data-ttu-id="a1ff5-149">如果禁用跟踪，则 TempData 提供程序不起作用。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-149">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="a1ff5-150">若要在禁用跟踪时启用 TempData 提供程序，请在中将 TempData 标记 cookie 为 "重要" `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-150">To enable the TempData provider when tracking is disabled, mark the TempData cookie as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

<span data-ttu-id="a1ff5-151">[会话状态](xref:fundamentals/app-state) cookie不是必需的。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-151">[Session state](xref:fundamentals/app-state) cookies are not essential.</span></span> <span data-ttu-id="a1ff5-152">禁用跟踪后，会话状态不起作用。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-152">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="a1ff5-153">以下代码使会话成为 cookie 必要的：</span><span class="sxs-lookup"><span data-stu-id="a1ff5-153">The following code makes session cookies essential:</span></span>

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="a1ff5-154">个人数据</span><span class="sxs-lookup"><span data-stu-id="a1ff5-154">Personal data</span></span>

<span data-ttu-id="a1ff5-155">ASP.NET Core 通过单独用户帐户创建的应用包括下载和删除个人数据的代码。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-155">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="a1ff5-156">选择用户名，然后选择 "**个人数据**"：</span><span class="sxs-lookup"><span data-stu-id="a1ff5-156">Select the user name and then select **Personal data**:</span></span>

![管理个人数据页](gdpr/_static/pd.png)

<span data-ttu-id="a1ff5-158">说明：</span><span class="sxs-lookup"><span data-stu-id="a1ff5-158">Notes:</span></span>

* <span data-ttu-id="a1ff5-159">若要生成 `Account/Manage` 代码，请[参阅 Identity 基架](xref:security/authentication/scaffold-identity)。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-159">To generate the `Account/Manage` code, see [Scaffold Identity](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="a1ff5-160">"**删除**" 和 "**下载**" 链接仅作用于默认标识数据。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-160">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="a1ff5-161">必须扩展用于创建自定义用户数据的应用，以删除/下载自定义用户数据。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-161">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="a1ff5-162">有关详细信息，请参阅向[添加、下载和删除自定义用户 Identity 数据](xref:security/authentication/add-user-data)。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-162">For more information, see [Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="a1ff5-163">Identity `AspNetUserTokens` 由于[外键](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152)，通过级联删除行为删除用户时，将删除存储在数据库表中的用户的已保存令牌。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-163">Saved tokens for the user that are stored in the Identity database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span></span>
* <span data-ttu-id="a1ff5-164">[外部提供程序身份验证](xref:security/authentication/social/index)（如 Facebook 和 Google）在接受策略之前不可用 cookie 。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-164">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the cookie policy is accepted.</span></span>

::: moniker-end

## <a name="encryption-at-rest"></a><span data-ttu-id="a1ff5-165">静态加密</span><span class="sxs-lookup"><span data-stu-id="a1ff5-165">Encryption at rest</span></span>

<span data-ttu-id="a1ff5-166">某些数据库和存储机制允许静态加密。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-166">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="a1ff5-167">静态加密：</span><span class="sxs-lookup"><span data-stu-id="a1ff5-167">Encryption at rest:</span></span>

* <span data-ttu-id="a1ff5-168">自动加密存储的数据。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-168">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="a1ff5-169">对于访问数据的软件，不对其进行配置、编程或其他操作。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-169">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="a1ff5-170">是最简单且最安全的选项。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-170">Is the easiest and safest option.</span></span>
* <span data-ttu-id="a1ff5-171">允许数据库管理密钥和加密。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-171">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="a1ff5-172">例如：</span><span class="sxs-lookup"><span data-stu-id="a1ff5-172">For example:</span></span>

* <span data-ttu-id="a1ff5-173">Microsoft SQL 和 Azure SQL 提供[透明数据加密](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) 。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-173">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="a1ff5-174">默认情况下，SQL Azure 加密数据库</span><span class="sxs-lookup"><span data-stu-id="a1ff5-174">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="a1ff5-175">[默认情况下，加密 Azure blob、文件、表和队列存储](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/)。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-175">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="a1ff5-176">对于不提供静态内置加密的数据库，您可以使用磁盘加密来提供相同的保护。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-176">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="a1ff5-177">例如：</span><span class="sxs-lookup"><span data-stu-id="a1ff5-177">For example:</span></span>

* [<span data-ttu-id="a1ff5-178">适用于 Windows Server 的 BitLocker</span><span class="sxs-lookup"><span data-stu-id="a1ff5-178">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="a1ff5-179">Linux：</span><span class="sxs-lookup"><span data-stu-id="a1ff5-179">Linux:</span></span>
  * [<span data-ttu-id="a1ff5-180">eCryptfs</span><span class="sxs-lookup"><span data-stu-id="a1ff5-180">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="a1ff5-181">[EncFS](https://github.com/vgough/encfs)。</span><span class="sxs-lookup"><span data-stu-id="a1ff5-181">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a1ff5-182">其他资源</span><span class="sxs-lookup"><span data-stu-id="a1ff5-182">Additional resources</span></span>

* [<span data-ttu-id="a1ff5-183">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="a1ff5-183">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [<span data-ttu-id="a1ff5-184">GDPR-在 ASP.NET Core 中添加 "撤消许可" 按钮</span><span class="sxs-lookup"><span data-stu-id="a1ff5-184">GDPR - Adding a Revoke Consent Button in ASP.NET Core</span></span>](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
