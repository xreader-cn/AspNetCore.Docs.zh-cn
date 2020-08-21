---
title: 保护 ASP.NET Core Blazor Server应用
author: guardrex
description: 了解如何将 Blazor Server应用作为 ASP.NET Core 应用来保护。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: ba9fe3c0149679fa5760c0c9214cd426f1804c31
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626449"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="51cb5-103">保护 ASP.NET Core Blazor Server应用</span><span class="sxs-lookup"><span data-stu-id="51cb5-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="51cb5-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="51cb5-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="51cb5-105">Blazor Server应用的安全配置方式与 ASP.NET Core 应用相同。</span><span class="sxs-lookup"><span data-stu-id="51cb5-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="51cb5-106">有关详细信息，请参阅 <xref:security/index> 下的文章。</span><span class="sxs-lookup"><span data-stu-id="51cb5-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="51cb5-107">此“概述”下的主题特别适用于 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="51cb5-107">Topics under this overview apply specifically to Blazor Server.</span></span> 

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="51cb5-108">Blazor Server项目模板</span><span class="sxs-lookup"><span data-stu-id="51cb5-108">Blazor Server project template</span></span>

<span data-ttu-id="51cb5-109">创建项目后，可配置 Blazor Server项目模板来进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="51cb5-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="51cb5-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="51cb5-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="51cb5-111">请按照 <xref:blazor/tooling> 中的 Visual Studio 指南操作，新建具有身份验证机制的 Blazor Server项目。</span><span class="sxs-lookup"><span data-stu-id="51cb5-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="51cb5-112">在“新建 ASP.NET Core Web 应用”对话框中选择“Blazor Server应用”模板后，选择“身份验证”下的“更改”。</span><span class="sxs-lookup"><span data-stu-id="51cb5-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="51cb5-113">此时将打开一个对话框，为其他 ASP.NET Core 项目提供一组相同的身份验证机制：</span><span class="sxs-lookup"><span data-stu-id="51cb5-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="51cb5-114">**无身份验证**</span><span class="sxs-lookup"><span data-stu-id="51cb5-114">**No Authentication**</span></span>
* <span data-ttu-id="51cb5-115">**个人用户帐户**：可存储用户帐户：</span><span class="sxs-lookup"><span data-stu-id="51cb5-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="51cb5-116">在使用 ASP.NET Core 的 [Identity](xref:security/authentication/identity) 系统的应用中存储。</span><span class="sxs-lookup"><span data-stu-id="51cb5-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="51cb5-117">使用 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 存储。</span><span class="sxs-lookup"><span data-stu-id="51cb5-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="51cb5-118">**工作或学校帐户**</span><span class="sxs-lookup"><span data-stu-id="51cb5-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="51cb5-119">**Windows 身份验证**</span><span class="sxs-lookup"><span data-stu-id="51cb5-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="51cb5-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="51cb5-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="51cb5-121">请按照 <xref:blazor/tooling> 中的 Visual Studio Code 指南操作，新建具有身份验证机制的 Blazor Server项目：</span><span class="sxs-lookup"><span data-stu-id="51cb5-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="51cb5-122">下表中显示了可能的身份验证值 (`{AUTHENTICATION}`)。</span><span class="sxs-lookup"><span data-stu-id="51cb5-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="51cb5-123">身份验证机制</span><span class="sxs-lookup"><span data-stu-id="51cb5-123">Authentication mechanism</span></span> | <span data-ttu-id="51cb5-124">描述</span><span class="sxs-lookup"><span data-stu-id="51cb5-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="51cb5-125">`None`（默认值）</span><span class="sxs-lookup"><span data-stu-id="51cb5-125">`None` (default)</span></span>         | <span data-ttu-id="51cb5-126">无身份验证</span><span class="sxs-lookup"><span data-stu-id="51cb5-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="51cb5-127">使用 ASP.NET Core Identity 将用户存储在应用中</span><span class="sxs-lookup"><span data-stu-id="51cb5-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="51cb5-128">用户存储在 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 中</span><span class="sxs-lookup"><span data-stu-id="51cb5-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="51cb5-129">对一个租户进行组织身份验证</span><span class="sxs-lookup"><span data-stu-id="51cb5-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="51cb5-130">对多个租户进行组织身份验证</span><span class="sxs-lookup"><span data-stu-id="51cb5-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="51cb5-131">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="51cb5-131">Windows Authentication</span></span> |

<span data-ttu-id="51cb5-132">使用 `-o|--output` 选项，该命令使用为 `{APP NAME}` 占位符提供的值来执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="51cb5-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="51cb5-133">为项目创建文件夹。</span><span class="sxs-lookup"><span data-stu-id="51cb5-133">Create a folder for the project.</span></span>
* <span data-ttu-id="51cb5-134">为该项目命名。</span><span class="sxs-lookup"><span data-stu-id="51cb5-134">Name the project.</span></span>

<span data-ttu-id="51cb5-135">有关详细信息，请参阅 .NET Core 指南中的 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令。</span><span class="sxs-lookup"><span data-stu-id="51cb5-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="51cb5-136">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="51cb5-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="51cb5-137">请按照 <xref:blazor/tooling> 中的 Visual Studio for Mac 指南进行操作。</span><span class="sxs-lookup"><span data-stu-id="51cb5-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="51cb5-138">在“配置新的 Blazor Server应用”步骤中，从“身份验证”下拉列表中选择“个人身份验证(应用内)”。</span><span class="sxs-lookup"><span data-stu-id="51cb5-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="51cb5-139">此应用是使用 ASP.NET Core Identity 为应用中存储的个人用户创建的。</span><span class="sxs-lookup"><span data-stu-id="51cb5-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="51cb5-140">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="51cb5-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="51cb5-141">在命令行界面中使用以下命令，新建具有身份验证机制的 Blazor Server项目：</span><span class="sxs-lookup"><span data-stu-id="51cb5-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="51cb5-142">下表中显示了可能的身份验证值 (`{AUTHENTICATION}`)。</span><span class="sxs-lookup"><span data-stu-id="51cb5-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="51cb5-143">身份验证机制</span><span class="sxs-lookup"><span data-stu-id="51cb5-143">Authentication mechanism</span></span> | <span data-ttu-id="51cb5-144">描述</span><span class="sxs-lookup"><span data-stu-id="51cb5-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="51cb5-145">`None`（默认值）</span><span class="sxs-lookup"><span data-stu-id="51cb5-145">`None` (default)</span></span>         | <span data-ttu-id="51cb5-146">无身份验证</span><span class="sxs-lookup"><span data-stu-id="51cb5-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="51cb5-147">使用 ASP.NET Core Identity 将用户存储在应用中</span><span class="sxs-lookup"><span data-stu-id="51cb5-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="51cb5-148">用户存储在 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 中</span><span class="sxs-lookup"><span data-stu-id="51cb5-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="51cb5-149">对一个租户进行组织身份验证</span><span class="sxs-lookup"><span data-stu-id="51cb5-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="51cb5-150">对多个租户进行组织身份验证</span><span class="sxs-lookup"><span data-stu-id="51cb5-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="51cb5-151">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="51cb5-151">Windows Authentication</span></span> |

<span data-ttu-id="51cb5-152">使用 `-o|--output` 选项，该命令使用为 `{APP NAME}` 占位符提供的值来执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="51cb5-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="51cb5-153">为项目创建文件夹。</span><span class="sxs-lookup"><span data-stu-id="51cb5-153">Create a folder for the project.</span></span>
* <span data-ttu-id="51cb5-154">为该项目命名。</span><span class="sxs-lookup"><span data-stu-id="51cb5-154">Name the project.</span></span>

<span data-ttu-id="51cb5-155">有关详细信息，请参阅 .NET Core 指南中的 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令。</span><span class="sxs-lookup"><span data-stu-id="51cb5-155">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="51cb5-156">设置Identity的基架</span><span class="sxs-lookup"><span data-stu-id="51cb5-156">Scaffold Identity</span></span>

<span data-ttu-id="51cb5-157">将 Identity 架构到 Blazor Server项目中：</span><span class="sxs-lookup"><span data-stu-id="51cb5-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="51cb5-158">[当前没有授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)。</span><span class="sxs-lookup"><span data-stu-id="51cb5-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="51cb5-159">[有授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)。</span><span class="sxs-lookup"><span data-stu-id="51cb5-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>
