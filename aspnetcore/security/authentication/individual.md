---
title: 基于 ASP.NET Core 项目使用单个用户帐户创建项目
author: rick-anderson
description: 发现基于 ASP.NET Core 项目使用单个用户帐户创建的文章。
ms.author: riande
ms.date: 11/30/2017
uid: security/authentication/individual
ms.openlocfilehash: f9c1be16386da935382275815bb5fd5c72894b1c
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265435"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="7094a-103">基于 ASP.NET Core 项目使用单个用户帐户创建项目</span><span class="sxs-lookup"><span data-stu-id="7094a-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="7094a-104">ASP.NET Core 标识包含在 Visual Studio 中使用"单个用户帐户"选项的项目模板中。</span><span class="sxs-lookup"><span data-stu-id="7094a-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="7094a-105">中使用的.NET Core CLI 中可用的身份验证模板`-au Individual`:</span><span class="sxs-lookup"><span data-stu-id="7094a-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

::: moniker range=">= aspnetcore-2.1"

```console
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```console
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

<span data-ttu-id="7094a-106">请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore/issues/5833)web API 身份验证。</span><span class="sxs-lookup"><span data-stu-id="7094a-106">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="7094a-107">无身份验证</span><span class="sxs-lookup"><span data-stu-id="7094a-107">No Authentication</span></span>

<span data-ttu-id="7094a-108">在.NET Core CLI 与指定身份验证`-au`选项。</span><span class="sxs-lookup"><span data-stu-id="7094a-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="7094a-109">在 Visual Studio 中，**更改身份验证**对话框是适用于新 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="7094a-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="7094a-110">默认值为 Visual Studio 中的新 web 应用**无身份验证**。</span><span class="sxs-lookup"><span data-stu-id="7094a-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="7094a-111">使用无身份验证创建的项目：</span><span class="sxs-lookup"><span data-stu-id="7094a-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="7094a-112">不包含 web 页面和 UI 中登录和注销。</span><span class="sxs-lookup"><span data-stu-id="7094a-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="7094a-113">不包含身份验证代码。</span><span class="sxs-lookup"><span data-stu-id="7094a-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="7094a-114">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="7094a-114">Windows Authentication</span></span>

<span data-ttu-id="7094a-115">Windows 身份验证指定为新 web 应用程序中使用.NET Core CLI`-au Windows`选项。</span><span class="sxs-lookup"><span data-stu-id="7094a-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="7094a-116">在 Visual Studio 中，**更改身份验证**对话框提供了**Windows 身份验证**选项。</span><span class="sxs-lookup"><span data-stu-id="7094a-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="7094a-117">如果选择 Windows 身份验证，则该应用程序配置为使用[Windows 身份验证 IIS 模块](xref:host-and-deploy/iis/modules)。</span><span class="sxs-lookup"><span data-stu-id="7094a-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="7094a-118">Windows 身份验证适用于 Intranet web 站点。</span><span class="sxs-lookup"><span data-stu-id="7094a-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7094a-119">其他资源</span><span class="sxs-lookup"><span data-stu-id="7094a-119">Additional resources</span></span>

<span data-ttu-id="7094a-120">以下文章演示了如何使用 ASP.NET Core 使用单个用户帐户的模板中生成的代码：</span><span class="sxs-lookup"><span data-stu-id="7094a-120">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="7094a-121">使用 SMS 设置双因素身份验证</span><span class="sxs-lookup"><span data-stu-id="7094a-121">Two-factor authentication with SMS</span></span>](xref:security/authentication/2fa)
* [<span data-ttu-id="7094a-122">ASP.NET Core 中的帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="7094a-122">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="7094a-123">使用受授权的用户数据创建 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="7094a-123">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
