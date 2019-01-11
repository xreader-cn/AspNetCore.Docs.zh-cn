---
title: 在 ASP.NET Core中的常规数据保护法规 (GDPR) 支持
author: rick-anderson
description: 了解如何访问 ASP.NET Core web 应用中的 GDPR 扩展点。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/29/2018
uid: security/gdpr
ms.openlocfilehash: 7688ac6264b089ebd3544494c967684cb291a547
ms.sourcegitcommit: cec77d5ad8a0cedb1ecbec32834111492afd0cd2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207510"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="4baf9-103">在 ASP.NET Core 欧洲常规数据保护法规 (GDPR) 支持</span><span class="sxs-lookup"><span data-stu-id="4baf9-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="4baf9-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4baf9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4baf9-105">ASP.NET Core 提供 Api 和模板，以帮助满足一些[欧洲常规数据保护法规 (GDPR)](https://www.eugdpr.org/)要求：</span><span class="sxs-lookup"><span data-stu-id="4baf9-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements:</span></span>

* <span data-ttu-id="4baf9-106">项目模板包含扩展点以及您可以替换为您的隐私和 cookie 的使用策略的存根的标记。</span><span class="sxs-lookup"><span data-stu-id="4baf9-106">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="4baf9-107">用于存储的个人信息中，cookie 许可功能，可要求 （和跟踪） 同意的情况下从你的用户。</span><span class="sxs-lookup"><span data-stu-id="4baf9-107">A cookie consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="4baf9-108">如果某个用户尚未同意数据收集，并且应用了[CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded)设置为`true`，非必需 cookie 不会发送到浏览器。</span><span class="sxs-lookup"><span data-stu-id="4baf9-108">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) set to `true`, non-essential cookies aren't sent to the browser.</span></span>
* <span data-ttu-id="4baf9-109">Cookie 可以标记为重要。</span><span class="sxs-lookup"><span data-stu-id="4baf9-109">Cookies can be marked as essential.</span></span> <span data-ttu-id="4baf9-110">用户尚未同意和跟踪被禁用时，甚至必不可少的 cookie 发送到浏览器。</span><span class="sxs-lookup"><span data-stu-id="4baf9-110">Essential cookies are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="4baf9-111">[TempData 和会话 cookie](#tempdata)跟踪处于禁用状态时无法正常工作。</span><span class="sxs-lookup"><span data-stu-id="4baf9-111">[TempData and Session cookies](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="4baf9-112">[标识管理](#pd)页提供了用于下载和删除用户数据的链接。</span><span class="sxs-lookup"><span data-stu-id="4baf9-112">The [Identity manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="4baf9-113">[示例应用](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/gdpr/sample)允许测试大部分 GDPR 扩展点和 Api 添加到 ASP.NET Core 2.1 模板。</span><span class="sxs-lookup"><span data-stu-id="4baf9-113">The [sample app](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="4baf9-114">请参阅[自述文件](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/gdpr/sample)测试说明的文件。</span><span class="sxs-lookup"><span data-stu-id="4baf9-114">See the [ReadMe](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="4baf9-115">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/gdpr/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="4baf9-115">[View or download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="4baf9-116">模板中的 ASP.NET Core GDPR 支持生成的代码</span><span class="sxs-lookup"><span data-stu-id="4baf9-116">ASP.NET Core GDPR support in template generated code</span></span>

<span data-ttu-id="4baf9-117">Razor 页面和 MVC 使用的项目模板创建的项目包括以下 GDPR 支持：</span><span class="sxs-lookup"><span data-stu-id="4baf9-117">Razor Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="4baf9-118">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)并[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)中设置`Startup`。</span><span class="sxs-lookup"><span data-stu-id="4baf9-118">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) are set in `Startup`.</span></span>
* <span data-ttu-id="4baf9-119">*_CookieConsentPartial.cshtml* [分部视图](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="4baf9-119">The *_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span>
* <span data-ttu-id="4baf9-120">*Pages/Privacy.cshtml*页或*Views/Home/Privacy.cshtml*视图提供了详细介绍站点的隐私策略的页。</span><span class="sxs-lookup"><span data-stu-id="4baf9-120">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="4baf9-121">*_CookieConsentPartial.cshtml*文件可生成隐私页的链接。</span><span class="sxs-lookup"><span data-stu-id="4baf9-121">The *_CookieConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="4baf9-122">对于使用单个用户帐户创建的应用程序，管理页提供链接以下载和删除[个人用户数据](#pd)。</span><span class="sxs-lookup"><span data-stu-id="4baf9-122">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a><span data-ttu-id="4baf9-123">CookiePolicyOptions 和 UseCookiePolicy</span><span class="sxs-lookup"><span data-stu-id="4baf9-123">CookiePolicyOptions and UseCookiePolicy</span></span>

<span data-ttu-id="4baf9-124">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)中初始化`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4baf9-124">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="4baf9-125">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)名为`Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="4baf9-125">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="cookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="4baf9-126">_CookieConsentPartial.cshtml 分部视图</span><span class="sxs-lookup"><span data-stu-id="4baf9-126">_CookieConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="4baf9-127">*_CookieConsentPartial.cshtml*分部视图：</span><span class="sxs-lookup"><span data-stu-id="4baf9-127">The *_CookieConsentPartial.cshtml* partial view:</span></span>

[!code-html[](gdpr/sample/RP/Pages/Shared/_CookieConsentPartial.cshtml)]

<span data-ttu-id="4baf9-128">此部分中：</span><span class="sxs-lookup"><span data-stu-id="4baf9-128">This partial:</span></span>

* <span data-ttu-id="4baf9-129">获取用户跟踪的状态。</span><span class="sxs-lookup"><span data-stu-id="4baf9-129">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="4baf9-130">如果应用程序配置为需要同意的情况下，用户必须同意使用之前可以跟踪 cookie。</span><span class="sxs-lookup"><span data-stu-id="4baf9-130">If the app is configured to require consent, the user must consent before cookies can be tracked.</span></span> <span data-ttu-id="4baf9-131">如果需要同意的情况下，cookie 同意面板固定的创建的导航栏的顶部 *_Layout.cshtml*文件。</span><span class="sxs-lookup"><span data-stu-id="4baf9-131">If consent is required, the cookie consent panel is fixed at top of the navigation bar created by the *_Layout.cshtml* file.</span></span>
* <span data-ttu-id="4baf9-132">提供了一个 HTML`<p>`元素以汇总您的隐私和 cookie 使用策略。</span><span class="sxs-lookup"><span data-stu-id="4baf9-132">Provides an HTML `<p>` element to summarize your privacy and cookie use policy.</span></span>
* <span data-ttu-id="4baf9-133">提供指向隐私页面或视图，其中详细介绍站点的隐私策略。</span><span class="sxs-lookup"><span data-stu-id="4baf9-133">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-cookies"></a><span data-ttu-id="4baf9-134">基本 cookie</span><span class="sxs-lookup"><span data-stu-id="4baf9-134">Essential cookies</span></span>

<span data-ttu-id="4baf9-135">如果尚未赋予同意的情况下，只有标记为重要的 cookie 发送到浏览器。</span><span class="sxs-lookup"><span data-stu-id="4baf9-135">If consent has not been given, only cookies marked essential are sent to the browser.</span></span> <span data-ttu-id="4baf9-136">下面的代码使 cookie 重要：</span><span class="sxs-lookup"><span data-stu-id="4baf9-136">The following code makes a cookie essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

## <a name="tempdata-provider-and-session-state-cookies-are-not-essential"></a><span data-ttu-id="4baf9-137">Tempdata 提供程序和会话状态的 cookie 不重要</span><span class="sxs-lookup"><span data-stu-id="4baf9-137">Tempdata provider and session state cookies are not essential</span></span>

<span data-ttu-id="4baf9-138">[Tempdata 提供程序](xref:fundamentals/app-state#tempdata)cookie 不是必需的。</span><span class="sxs-lookup"><span data-stu-id="4baf9-138">The [Tempdata provider](xref:fundamentals/app-state#tempdata) cookie isn't essential.</span></span> <span data-ttu-id="4baf9-139">如果禁用跟踪，Tempdata 提供程序不起作用。</span><span class="sxs-lookup"><span data-stu-id="4baf9-139">If tracking is disabled, the Tempdata provider isn't functional.</span></span> <span data-ttu-id="4baf9-140">若要启用 Tempdata 提供程序跟踪处于禁用状态时，将 TempData cookie 标记为重要中`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4baf9-140">To enable the Tempdata provider when tracking is disabled, mark the TempData cookie as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP/Startup.cs?name=snippet1)]

<span data-ttu-id="4baf9-141">[会话状态](xref:fundamentals/app-state)cookie 不重要。</span><span class="sxs-lookup"><span data-stu-id="4baf9-141">[Session state](xref:fundamentals/app-state) cookies are not essential.</span></span> <span data-ttu-id="4baf9-142">跟踪处于禁用状态时，会话状态不起作用。</span><span class="sxs-lookup"><span data-stu-id="4baf9-142">Session state isn't functional when tracking is disabled.</span></span>

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="4baf9-143">个人数据</span><span class="sxs-lookup"><span data-stu-id="4baf9-143">Personal data</span></span>

<span data-ttu-id="4baf9-144">使用个人用户帐户创建的 ASP.NET Core 应用包括要下载和删除个人数据的代码。</span><span class="sxs-lookup"><span data-stu-id="4baf9-144">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="4baf9-145">选择的用户名称，然后选择**个人数据**:</span><span class="sxs-lookup"><span data-stu-id="4baf9-145">Select the user name and then select **Personal data**:</span></span>

![管理个人数据页](gdpr/_static/pd.png)

<span data-ttu-id="4baf9-147">注意：</span><span class="sxs-lookup"><span data-stu-id="4baf9-147">Notes:</span></span>

* <span data-ttu-id="4baf9-148">若要生成`Account/Manage`代码，请参阅[基架标识](xref:security/authentication/scaffold-identity)。</span><span class="sxs-lookup"><span data-stu-id="4baf9-148">To generate the `Account/Manage` code, see [Scaffold Identity](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="4baf9-149">**删除**并**下载**链接只作用于的默认标识数据。</span><span class="sxs-lookup"><span data-stu-id="4baf9-149">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="4baf9-150">创建自定义用户数据的应用，必须进行扩展，以删除/下载自定义用户数据。</span><span class="sxs-lookup"><span data-stu-id="4baf9-150">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="4baf9-151">有关详细信息，请参阅[添加、 下载和删除自定义用户数据到标识](xref:security/authentication/add-user-data)。</span><span class="sxs-lookup"><span data-stu-id="4baf9-151">For more information, see [Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="4baf9-152">保存用户的标识数据库表中存储的令牌`AspNetUserTokens`时通过级联删除方式，由于删除了用户会被删除[外键](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152)。</span><span class="sxs-lookup"><span data-stu-id="4baf9-152">Saved tokens for the user that are stored in the Identity database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span></span>

## <a name="encryption-at-rest"></a><span data-ttu-id="4baf9-153">静态加密</span><span class="sxs-lookup"><span data-stu-id="4baf9-153">Encryption at rest</span></span>

<span data-ttu-id="4baf9-154">某些数据库和存储机制允许静态加密。</span><span class="sxs-lookup"><span data-stu-id="4baf9-154">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="4baf9-155">静态加密：</span><span class="sxs-lookup"><span data-stu-id="4baf9-155">Encryption at rest:</span></span>

* <span data-ttu-id="4baf9-156">自动对存储的数据进行加密。</span><span class="sxs-lookup"><span data-stu-id="4baf9-156">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="4baf9-157">无需配置、 编程中或其他工作的软件的访问的数据进行加密。</span><span class="sxs-lookup"><span data-stu-id="4baf9-157">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="4baf9-158">是最简单且最安全的选项。</span><span class="sxs-lookup"><span data-stu-id="4baf9-158">Is the easiest and safest option.</span></span>
* <span data-ttu-id="4baf9-159">允许管理密钥和加密的数据库。</span><span class="sxs-lookup"><span data-stu-id="4baf9-159">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="4baf9-160">例如：</span><span class="sxs-lookup"><span data-stu-id="4baf9-160">For example:</span></span>

* <span data-ttu-id="4baf9-161">Microsoft SQL 和 Azure SQL 提供了[透明数据加密](/sql/relational-databases/security/encryption/transparent-data-encryption)(TDE)。</span><span class="sxs-lookup"><span data-stu-id="4baf9-161">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="4baf9-162">SQL Azure 默认情况下加密数据库</span><span class="sxs-lookup"><span data-stu-id="4baf9-162">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="4baf9-163">[默认情况下加密 azure Blob、 文件、 表和队列存储](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/)。</span><span class="sxs-lookup"><span data-stu-id="4baf9-163">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="4baf9-164">对于未提供静态的内置加密的数据库，您可能能够使用磁盘加密来提供相同的保护。</span><span class="sxs-lookup"><span data-stu-id="4baf9-164">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="4baf9-165">例如：</span><span class="sxs-lookup"><span data-stu-id="4baf9-165">For example:</span></span>

* [<span data-ttu-id="4baf9-166">适用于 Windows Server 的 BitLocker</span><span class="sxs-lookup"><span data-stu-id="4baf9-166">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="4baf9-167">Linux:</span><span class="sxs-lookup"><span data-stu-id="4baf9-167">Linux:</span></span>
  * [<span data-ttu-id="4baf9-168">eCryptfs</span><span class="sxs-lookup"><span data-stu-id="4baf9-168">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="4baf9-169">[EncFS](https://github.com/vgough/encfs)。</span><span class="sxs-lookup"><span data-stu-id="4baf9-169">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4baf9-170">其他资源</span><span class="sxs-lookup"><span data-stu-id="4baf9-170">Additional resources</span></span>

* [<span data-ttu-id="4baf9-171">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="4baf9-171">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
