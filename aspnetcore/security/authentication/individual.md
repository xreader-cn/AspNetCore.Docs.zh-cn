---
title: 基于通过单独用户帐户创建的 ASP.NET Core 项目的项目
author: rick-anderson
description: 基于通过单独用户帐户创建的 ASP.NET Core 项目发现文章。
ms.author: riande
ms.date: 12/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/individual
ms.openlocfilehash: 6d3743fc31c29bed5075ac29381aea51a64a908c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406246"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="ca7b6-103">基于通过单独用户帐户创建的 ASP.NET Core 项目的项目</span><span class="sxs-lookup"><span data-stu-id="ca7b6-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="ca7b6-104">ASP.NET Core Identity 包含在 Visual Studio 中具有 "单独用户帐户" 选项的项目模板中。</span><span class="sxs-lookup"><span data-stu-id="ca7b6-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="ca7b6-105">.NET Core CLI 中提供了身份验证模板 `-au Individual` ：</span><span class="sxs-lookup"><span data-stu-id="ca7b6-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

<span data-ttu-id="ca7b6-106">对于 web API 身份验证，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore/issues/5833)。</span><span class="sxs-lookup"><span data-stu-id="ca7b6-106">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="ca7b6-107">无身份验证</span><span class="sxs-lookup"><span data-stu-id="ca7b6-107">No Authentication</span></span>

<span data-ttu-id="ca7b6-108">在 .NET Core CLI 中通过选项指定了身份验证 `-au` 。</span><span class="sxs-lookup"><span data-stu-id="ca7b6-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="ca7b6-109">在 Visual Studio 中，"**更改身份验证**" 对话框可用于新的 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="ca7b6-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="ca7b6-110">Visual Studio 中新 web 应用的默认值为 "**无身份验证**"。</span><span class="sxs-lookup"><span data-stu-id="ca7b6-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="ca7b6-111">用无身份验证创建的项目：</span><span class="sxs-lookup"><span data-stu-id="ca7b6-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="ca7b6-112">不要包含用于登录和注销的网页和 UI。</span><span class="sxs-lookup"><span data-stu-id="ca7b6-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="ca7b6-113">不包含身份验证代码。</span><span class="sxs-lookup"><span data-stu-id="ca7b6-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="ca7b6-114">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="ca7b6-114">Windows Authentication</span></span>

<span data-ttu-id="ca7b6-115">Windows 身份验证是通过选项在 .NET Core CLI 中为新 web 应用指定的 `-au Windows` 。</span><span class="sxs-lookup"><span data-stu-id="ca7b6-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="ca7b6-116">在 Visual Studio 中，"**更改身份验证**" 对话框提供**Windows 身份验证**选项。</span><span class="sxs-lookup"><span data-stu-id="ca7b6-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="ca7b6-117">如果选择了 "Windows 身份验证"，则会将应用程序配置为使用[Windows 身份验证 IIS 模块](xref:host-and-deploy/iis/modules)。</span><span class="sxs-lookup"><span data-stu-id="ca7b6-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="ca7b6-118">Windows 身份验证适用于 Intranet 网站。</span><span class="sxs-lookup"><span data-stu-id="ca7b6-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="dotnet-new-webapp-authentication-options"></a><span data-ttu-id="ca7b6-119">dotnet new webapp authentication 选项</span><span class="sxs-lookup"><span data-stu-id="ca7b6-119">dotnet new webapp authentication options</span></span>

<span data-ttu-id="ca7b6-120">下表显示了可用于新 web 应用的身份验证选项：</span><span class="sxs-lookup"><span data-stu-id="ca7b6-120">The following table shows the authentication options available for new web apps:</span></span>

| <span data-ttu-id="ca7b6-121">选项</span><span class="sxs-lookup"><span data-stu-id="ca7b6-121">Option</span></span> | <span data-ttu-id="ca7b6-122">身份验证类型</span><span class="sxs-lookup"><span data-stu-id="ca7b6-122">Type of authentication</span></span> | <span data-ttu-id="ca7b6-123">有关详细信息的链接</span><span class="sxs-lookup"><span data-stu-id="ca7b6-123">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="ca7b6-124">无</span><span class="sxs-lookup"><span data-stu-id="ca7b6-124">None</span></span>            |  <span data-ttu-id="ca7b6-125">无身份验证</span><span class="sxs-lookup"><span data-stu-id="ca7b6-125">No authentication</span></span> | | 
| <span data-ttu-id="ca7b6-126">个人</span><span class="sxs-lookup"><span data-stu-id="ca7b6-126">Individual</span></span>      |  <span data-ttu-id="ca7b6-127">单个身份验证</span><span class="sxs-lookup"><span data-stu-id="ca7b6-127">Individual authentication</span></span> | <xref:security/authentication/identity>
| <span data-ttu-id="ca7b6-128">IndividualB2C</span><span class="sxs-lookup"><span data-stu-id="ca7b6-128">IndividualB2C</span></span>   |  <span data-ttu-id="ca7b6-129">Azure AD B2C 的云托管的单个身份验证</span><span class="sxs-lookup"><span data-stu-id="ca7b6-129">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="ca7b6-130">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="ca7b6-130">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="ca7b6-131">SingleOrg</span><span class="sxs-lookup"><span data-stu-id="ca7b6-131">SingleOrg</span></span>       |  <span data-ttu-id="ca7b6-132">对一个租户进行组织身份验证</span><span class="sxs-lookup"><span data-stu-id="ca7b6-132">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="ca7b6-133">Azure AD</span><span class="sxs-lookup"><span data-stu-id="ca7b6-133">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="ca7b6-134">MultiOrg</span><span class="sxs-lookup"><span data-stu-id="ca7b6-134">MultiOrg</span></span>        |  <span data-ttu-id="ca7b6-135">对多个租户进行组织身份验证</span><span class="sxs-lookup"><span data-stu-id="ca7b6-135">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="ca7b6-136">Azure AD</span><span class="sxs-lookup"><span data-stu-id="ca7b6-136">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="ca7b6-137">Windows</span><span class="sxs-lookup"><span data-stu-id="ca7b6-137">Windows</span></span>         |  <span data-ttu-id="ca7b6-138">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="ca7b6-138">Windows authentication</span></span> | [<span data-ttu-id="ca7b6-139">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="ca7b6-139">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a><span data-ttu-id="ca7b6-140">Visual Studio new webapp authentication 选项</span><span class="sxs-lookup"><span data-stu-id="ca7b6-140">Visual Studio new webapp authentication options</span></span>

<span data-ttu-id="ca7b6-141">下表显示了使用 Visual Studio 创建新的 web 应用时可用的身份验证选项：</span><span class="sxs-lookup"><span data-stu-id="ca7b6-141">The following table shows the authentication options available when creating a new web app with Visual Studio:</span></span>

| <span data-ttu-id="ca7b6-142">选项</span><span class="sxs-lookup"><span data-stu-id="ca7b6-142">Option</span></span> | <span data-ttu-id="ca7b6-143">身份验证类型</span><span class="sxs-lookup"><span data-stu-id="ca7b6-143">Type of authentication</span></span> | <span data-ttu-id="ca7b6-144">有关详细信息的链接</span><span class="sxs-lookup"><span data-stu-id="ca7b6-144">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="ca7b6-145">无</span><span class="sxs-lookup"><span data-stu-id="ca7b6-145">None</span></span>            |  <span data-ttu-id="ca7b6-146">无身份验证</span><span class="sxs-lookup"><span data-stu-id="ca7b6-146">No authentication</span></span> | | 
| <span data-ttu-id="ca7b6-147">应用中的单个用户帐户/存储用户帐户</span><span class="sxs-lookup"><span data-stu-id="ca7b6-147">Individual User Accounts / Store user accounts in-app</span></span> |  <span data-ttu-id="ca7b6-148">单个身份验证</span><span class="sxs-lookup"><span data-stu-id="ca7b6-148">Individual authentication</span></span> | <xref:security/authentication/identity> |
| <span data-ttu-id="ca7b6-149">单个用户帐户/连接到云中的现有用户存储</span><span class="sxs-lookup"><span data-stu-id="ca7b6-149">Individual User Accounts / Connect to an existing user store in the cloud</span></span> |  <span data-ttu-id="ca7b6-150">Azure AD B2C 的云托管的单个身份验证</span><span class="sxs-lookup"><span data-stu-id="ca7b6-150">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="ca7b6-151">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="ca7b6-151">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="ca7b6-152">工作或学校云/单个组织</span><span class="sxs-lookup"><span data-stu-id="ca7b6-152">Work or School Cloud / Single Org</span></span>  |  <span data-ttu-id="ca7b6-153">对一个租户进行组织身份验证</span><span class="sxs-lookup"><span data-stu-id="ca7b6-153">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="ca7b6-154">Azure AD</span><span class="sxs-lookup"><span data-stu-id="ca7b6-154">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="ca7b6-155">工作或学校云/多个组织</span><span class="sxs-lookup"><span data-stu-id="ca7b6-155">Work or School Cloud / Multiple Org</span></span> |  <span data-ttu-id="ca7b6-156">对多个租户进行组织身份验证</span><span class="sxs-lookup"><span data-stu-id="ca7b6-156">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="ca7b6-157">Azure AD</span><span class="sxs-lookup"><span data-stu-id="ca7b6-157">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="ca7b6-158">Windows</span><span class="sxs-lookup"><span data-stu-id="ca7b6-158">Windows</span></span>         |  <span data-ttu-id="ca7b6-159">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="ca7b6-159">Windows authentication</span></span> | [<span data-ttu-id="ca7b6-160">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="ca7b6-160">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a><span data-ttu-id="ca7b6-161">其他资源</span><span class="sxs-lookup"><span data-stu-id="ca7b6-161">Additional resources</span></span>

<span data-ttu-id="ca7b6-162">以下文章演示了如何使用 ASP.NET Core 使用单个用户帐户的模板中生成的代码：</span><span class="sxs-lookup"><span data-stu-id="ca7b6-162">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="ca7b6-163">ASP.NET Core 中的帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="ca7b6-163">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="ca7b6-164">使用授权保护的用户数据创建 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="ca7b6-164">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
