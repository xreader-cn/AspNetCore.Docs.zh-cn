---
title: 基于 ASP.NET Core 项目使用单个用户帐户创建项目
author: rick-anderson
description: 发现基于 ASP.NET Core 项目使用单个用户帐户创建的文章。
ms.author: riande
ms.date: 11/30/2017
uid: security/authentication/individual
ms.openlocfilehash: cf548417268a8587787471b9ed91c0ed109fbee9
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080707"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="a9c69-103">基于 ASP.NET Core 项目使用单个用户帐户创建项目</span><span class="sxs-lookup"><span data-stu-id="a9c69-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="a9c69-104">ASP.NET Core 标识包含在 Visual Studio 中使用"单个用户帐户"选项的项目模板中。</span><span class="sxs-lookup"><span data-stu-id="a9c69-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="a9c69-105">中使用的.NET Core CLI 中可用的身份验证模板`-au Individual`:</span><span class="sxs-lookup"><span data-stu-id="a9c69-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

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

<span data-ttu-id="a9c69-106">对于 web API 身份验证，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore/issues/5833)。</span><span class="sxs-lookup"><span data-stu-id="a9c69-106">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="a9c69-107">无身份验证</span><span class="sxs-lookup"><span data-stu-id="a9c69-107">No Authentication</span></span>

<span data-ttu-id="a9c69-108">在 .NET Core CLI 中通过`-au`选项指定了身份验证。</span><span class="sxs-lookup"><span data-stu-id="a9c69-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="a9c69-109">在 Visual Studio 中，"**更改身份验证**" 对话框可用于新的 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="a9c69-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="a9c69-110">Visual Studio 中新 web 应用的默认值为 "**无身份验证**"。</span><span class="sxs-lookup"><span data-stu-id="a9c69-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="a9c69-111">用无身份验证创建的项目：</span><span class="sxs-lookup"><span data-stu-id="a9c69-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="a9c69-112">不要包含用于登录和注销的网页和 UI。</span><span class="sxs-lookup"><span data-stu-id="a9c69-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="a9c69-113">不包含身份验证代码。</span><span class="sxs-lookup"><span data-stu-id="a9c69-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="a9c69-114">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="a9c69-114">Windows Authentication</span></span>

<span data-ttu-id="a9c69-115">Windows 身份验证是通过`-au Windows`选项在 .NET Core CLI 中为新 web 应用指定的。</span><span class="sxs-lookup"><span data-stu-id="a9c69-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="a9c69-116">在 Visual Studio 中，"**更改身份验证**" 对话框提供**Windows 身份验证**选项。</span><span class="sxs-lookup"><span data-stu-id="a9c69-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="a9c69-117">如果选择了 "Windows 身份验证"，则会将应用程序配置为使用[Windows 身份验证 IIS 模块](xref:host-and-deploy/iis/modules)。</span><span class="sxs-lookup"><span data-stu-id="a9c69-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="a9c69-118">Windows 身份验证适用于 Intranet 网站。</span><span class="sxs-lookup"><span data-stu-id="a9c69-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a9c69-119">其他资源</span><span class="sxs-lookup"><span data-stu-id="a9c69-119">Additional resources</span></span>

<span data-ttu-id="a9c69-120">以下文章演示了如何使用 ASP.NET Core 使用单个用户帐户的模板中生成的代码：</span><span class="sxs-lookup"><span data-stu-id="a9c69-120">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="a9c69-121">使用 SMS 设置双因素身份验证</span><span class="sxs-lookup"><span data-stu-id="a9c69-121">Two-factor authentication with SMS</span></span>](xref:security/authentication/2fa)
* [<span data-ttu-id="a9c69-122">ASP.NET Core 中的帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="a9c69-122">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="a9c69-123">使用受授权的用户数据创建 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="a9c69-123">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
