---
title: 为 ASP.NET Core Blazor 服务器应用提供保护
author: guardrex
description: 了解如何将 Blazor 服务器应用作为 ASP.NET Core 应用程序来提供保护。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: 0021911b731e57bc6eabf857c27a13462e7400ae
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206328"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="b85b2-103">为 ASP.NET Core Blazor 服务器应用提供保护</span><span class="sxs-lookup"><span data-stu-id="b85b2-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="b85b2-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b85b2-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="b85b2-105">Blazor 服务器项目模板</span><span class="sxs-lookup"><span data-stu-id="b85b2-105">Blazor Server project template</span></span>

<span data-ttu-id="b85b2-106">创建项目后，可以配置 Blazor 服务器项目模板以进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="b85b2-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b85b2-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b85b2-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b85b2-108">按照 <xref:blazor/get-started> 一文中的 Visual Studio 指南操作，创建具有身份验证机制的新 Blazor 服务器项目。</span><span class="sxs-lookup"><span data-stu-id="b85b2-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="b85b2-109">在“创建新的 ASP.NET Core Web 应用程序”  对话框中选择“Blazor 服务器应用”  模板后，在“身份验证”  下选择“更改”  。</span><span class="sxs-lookup"><span data-stu-id="b85b2-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="b85b2-110">此时将打开一个对话框，为其他 ASP.NET Core 项目提供一组相同的身份验证机制：</span><span class="sxs-lookup"><span data-stu-id="b85b2-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="b85b2-111">**无身份验证**</span><span class="sxs-lookup"><span data-stu-id="b85b2-111">**No Authentication**</span></span>
* <span data-ttu-id="b85b2-112">**个人用户帐户** &ndash; 可以通过以下方式存储用户帐户：</span><span class="sxs-lookup"><span data-stu-id="b85b2-112">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="b85b2-113">使用 ASP.NET Core 的 [Identity](xref:security/authentication/identity) 系统在应用程序中存储。</span><span class="sxs-lookup"><span data-stu-id="b85b2-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="b85b2-114">使用 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 存储。</span><span class="sxs-lookup"><span data-stu-id="b85b2-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="b85b2-115">**工作或学校帐户**</span><span class="sxs-lookup"><span data-stu-id="b85b2-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="b85b2-116">**Windows 身份验证**</span><span class="sxs-lookup"><span data-stu-id="b85b2-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b85b2-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b85b2-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b85b2-118">按照 <xref:blazor/get-started> 一文中的 Visual Studio Code 指南操作，创建具有身份验证机制的新 Blazor 服务器项目。</span><span class="sxs-lookup"><span data-stu-id="b85b2-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="b85b2-119">下表中显示了可能的身份验证值 (`{AUTHENTICATION}`)。</span><span class="sxs-lookup"><span data-stu-id="b85b2-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="b85b2-120">身份验证机制</span><span class="sxs-lookup"><span data-stu-id="b85b2-120">Authentication mechanism</span></span> | <span data-ttu-id="b85b2-121">描述</span><span class="sxs-lookup"><span data-stu-id="b85b2-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="b85b2-122">`None`（默认值）</span><span class="sxs-lookup"><span data-stu-id="b85b2-122">`None` (default)</span></span>         | <span data-ttu-id="b85b2-123">无身份验证</span><span class="sxs-lookup"><span data-stu-id="b85b2-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="b85b2-124">使用 ASP.NET Core 标识将用户存储在应用程序中</span><span class="sxs-lookup"><span data-stu-id="b85b2-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="b85b2-125">用户存储在 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 中</span><span class="sxs-lookup"><span data-stu-id="b85b2-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="b85b2-126">对一个租户进行组织身份验证</span><span class="sxs-lookup"><span data-stu-id="b85b2-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="b85b2-127">对多个租户进行组织身份验证</span><span class="sxs-lookup"><span data-stu-id="b85b2-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="b85b2-128">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="b85b2-128">Windows Authentication</span></span> |

<span data-ttu-id="b85b2-129">使用 `-o|--output` 选项，该命令使用为 `{APP NAME}` 占位符提供的值来执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b85b2-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="b85b2-130">为项目创建文件夹。</span><span class="sxs-lookup"><span data-stu-id="b85b2-130">Create a folder for the project.</span></span>
* <span data-ttu-id="b85b2-131">为该项目命名。</span><span class="sxs-lookup"><span data-stu-id="b85b2-131">Name the project.</span></span>

<span data-ttu-id="b85b2-132">有关详细信息，请参阅 .NET Core 指南中的 [dotnet new](/dotnet/core/tools/dotnet-new) 命令。</span><span class="sxs-lookup"><span data-stu-id="b85b2-132">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b85b2-133">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b85b2-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b85b2-134">请按照 <xref:blazor/get-started> 一文中的 Visual Studio for Mac 指南进行操作。</span><span class="sxs-lookup"><span data-stu-id="b85b2-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="b85b2-135">在“配置新的 Blazor 服务器应用”  步骤中，从“身份验证”  下拉列表中选择“个人身份验证(应用内)”  。</span><span class="sxs-lookup"><span data-stu-id="b85b2-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="b85b2-136">此应用是使用 ASP.NET Core 标识为存储在应用中的个人用户创建的。</span><span class="sxs-lookup"><span data-stu-id="b85b2-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b85b2-137">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b85b2-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="b85b2-138">请按照 <xref:blazor/get-started> 一文中的 .NET Core CLI 指南操作，创建具有身份验证机制的新 Blazor 服务器项目：</span><span class="sxs-lookup"><span data-stu-id="b85b2-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="b85b2-139">下表中显示了可能的身份验证值 (`{AUTHENTICATION}`)。</span><span class="sxs-lookup"><span data-stu-id="b85b2-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="b85b2-140">身份验证机制</span><span class="sxs-lookup"><span data-stu-id="b85b2-140">Authentication mechanism</span></span> | <span data-ttu-id="b85b2-141">描述</span><span class="sxs-lookup"><span data-stu-id="b85b2-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="b85b2-142">`None`（默认值）</span><span class="sxs-lookup"><span data-stu-id="b85b2-142">`None` (default)</span></span>         | <span data-ttu-id="b85b2-143">无身份验证</span><span class="sxs-lookup"><span data-stu-id="b85b2-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="b85b2-144">使用 ASP.NET Core 标识将用户存储在应用程序中</span><span class="sxs-lookup"><span data-stu-id="b85b2-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="b85b2-145">用户存储在 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 中</span><span class="sxs-lookup"><span data-stu-id="b85b2-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="b85b2-146">对一个租户进行组织身份验证</span><span class="sxs-lookup"><span data-stu-id="b85b2-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="b85b2-147">对多个租户进行组织身份验证</span><span class="sxs-lookup"><span data-stu-id="b85b2-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="b85b2-148">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="b85b2-148">Windows Authentication</span></span> |

<span data-ttu-id="b85b2-149">使用 `-o|--output` 选项，该命令使用为 `{APP NAME}` 占位符提供的值来执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b85b2-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="b85b2-150">为项目创建文件夹。</span><span class="sxs-lookup"><span data-stu-id="b85b2-150">Create a folder for the project.</span></span>
* <span data-ttu-id="b85b2-151">为该项目命名。</span><span class="sxs-lookup"><span data-stu-id="b85b2-151">Name the project.</span></span>

<span data-ttu-id="b85b2-152">有关详细信息，请参阅 .NET Core 指南中的 [dotnet new](/dotnet/core/tools/dotnet-new) 命令。</span><span class="sxs-lookup"><span data-stu-id="b85b2-152">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---
