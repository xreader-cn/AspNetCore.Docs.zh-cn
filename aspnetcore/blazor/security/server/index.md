---
title: 保护 ASP.NET Core Blazor Server应用
author: guardrex
description: 了解如何将 Blazor Server应用作为 ASP.NET Core 应用来保护。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
no-loc:
- appsettings.json
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
ms.openlocfilehash: aa24def1a003a2c2608691e6168066c740f47205
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024621"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="0a7ac-103">保护 ASP.NET Core Blazor Server应用</span><span class="sxs-lookup"><span data-stu-id="0a7ac-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="0a7ac-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0a7ac-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0a7ac-105">Blazor Server应用的安全配置方式与 ASP.NET Core 应用相同。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="0a7ac-106">有关详细信息，请参阅 <xref:security/index> 下的文章。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="0a7ac-107">此“概述”下的主题特别适用于 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-107">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="0a7ac-108">Blazor Server项目模板</span><span class="sxs-lookup"><span data-stu-id="0a7ac-108">Blazor Server project template</span></span>

<span data-ttu-id="0a7ac-109">创建项目后，可配置 Blazor Server项目模板来进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a7ac-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a7ac-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a7ac-111">请按照 <xref:blazor/tooling> 中的 Visual Studio 指南操作，新建具有身份验证机制的 Blazor Server项目。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="0a7ac-112">在“新建 ASP.NET Core Web 应用”对话框中选择“Blazor Server应用”模板后，选择“身份验证”下的“更改”。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="0a7ac-113">此时将打开一个对话框，为其他 ASP.NET Core 项目提供一组相同的身份验证机制：</span><span class="sxs-lookup"><span data-stu-id="0a7ac-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="0a7ac-114">**无身份验证**</span><span class="sxs-lookup"><span data-stu-id="0a7ac-114">**No Authentication**</span></span>
* <span data-ttu-id="0a7ac-115">**个人用户帐户**：可存储用户帐户：</span><span class="sxs-lookup"><span data-stu-id="0a7ac-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="0a7ac-116">在使用 ASP.NET Core 的 [Identity](xref:security/authentication/identity) 系统的应用中存储。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="0a7ac-117">使用 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 存储。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="0a7ac-118">**工作或学校帐户**</span><span class="sxs-lookup"><span data-stu-id="0a7ac-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="0a7ac-119">**Windows 身份验证**</span><span class="sxs-lookup"><span data-stu-id="0a7ac-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a7ac-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a7ac-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0a7ac-121">请按照 <xref:blazor/tooling> 中的 Visual Studio Code 指南操作，新建具有身份验证机制的 Blazor Server项目：</span><span class="sxs-lookup"><span data-stu-id="0a7ac-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="0a7ac-122">下表中显示了可能的身份验证值 (`{AUTHENTICATION}`)。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="0a7ac-123">身份验证机制</span><span class="sxs-lookup"><span data-stu-id="0a7ac-123">Authentication mechanism</span></span> | <span data-ttu-id="0a7ac-124">描述</span><span class="sxs-lookup"><span data-stu-id="0a7ac-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="0a7ac-125">`None`（默认值）</span><span class="sxs-lookup"><span data-stu-id="0a7ac-125">`None` (default)</span></span>         | <span data-ttu-id="0a7ac-126">无身份验证</span><span class="sxs-lookup"><span data-stu-id="0a7ac-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="0a7ac-127">使用 ASP.NET Core Identity 将用户存储在应用中</span><span class="sxs-lookup"><span data-stu-id="0a7ac-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="0a7ac-128">用户存储在 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 中</span><span class="sxs-lookup"><span data-stu-id="0a7ac-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="0a7ac-129">对一个租户进行组织身份验证</span><span class="sxs-lookup"><span data-stu-id="0a7ac-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="0a7ac-130">对多个租户进行组织身份验证</span><span class="sxs-lookup"><span data-stu-id="0a7ac-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="0a7ac-131">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="0a7ac-131">Windows Authentication</span></span> |

<span data-ttu-id="0a7ac-132">使用 `-o|--output` 选项，该命令使用为 `{APP NAME}` 占位符提供的值来执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="0a7ac-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="0a7ac-133">为项目创建文件夹。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-133">Create a folder for the project.</span></span>
* <span data-ttu-id="0a7ac-134">为该项目命名。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-134">Name the project.</span></span>

<span data-ttu-id="0a7ac-135">有关详细信息，请参阅 .NET Core 指南中的 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a7ac-136">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0a7ac-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0a7ac-137">请按照 <xref:blazor/tooling> 中的 Visual Studio for Mac 指南进行操作。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="0a7ac-138">在“配置新的 Blazor Server应用”步骤中，从“身份验证”下拉列表中选择“个人身份验证(应用内)”。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="0a7ac-139">此应用是使用 ASP.NET Core Identity 为应用中存储的个人用户创建的。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0a7ac-140">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0a7ac-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="0a7ac-141">在命令行界面中使用以下命令，新建具有身份验证机制的 Blazor Server项目：</span><span class="sxs-lookup"><span data-stu-id="0a7ac-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="0a7ac-142">下表中显示了可能的身份验证值 (`{AUTHENTICATION}`)。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="0a7ac-143">身份验证机制</span><span class="sxs-lookup"><span data-stu-id="0a7ac-143">Authentication mechanism</span></span> | <span data-ttu-id="0a7ac-144">描述</span><span class="sxs-lookup"><span data-stu-id="0a7ac-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="0a7ac-145">`None`（默认值）</span><span class="sxs-lookup"><span data-stu-id="0a7ac-145">`None` (default)</span></span>         | <span data-ttu-id="0a7ac-146">无身份验证</span><span class="sxs-lookup"><span data-stu-id="0a7ac-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="0a7ac-147">使用 ASP.NET Core Identity 将用户存储在应用中</span><span class="sxs-lookup"><span data-stu-id="0a7ac-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="0a7ac-148">用户存储在 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 中</span><span class="sxs-lookup"><span data-stu-id="0a7ac-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="0a7ac-149">对一个租户进行组织身份验证</span><span class="sxs-lookup"><span data-stu-id="0a7ac-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="0a7ac-150">对多个租户进行组织身份验证</span><span class="sxs-lookup"><span data-stu-id="0a7ac-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="0a7ac-151">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="0a7ac-151">Windows Authentication</span></span> |

<span data-ttu-id="0a7ac-152">使用 `-o|--output` 选项，该命令使用为 `{APP NAME}` 占位符提供的值来执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="0a7ac-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="0a7ac-153">为项目创建文件夹。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-153">Create a folder for the project.</span></span>
* <span data-ttu-id="0a7ac-154">为该项目命名。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-154">Name the project.</span></span>

<span data-ttu-id="0a7ac-155">更多相关信息：</span><span class="sxs-lookup"><span data-stu-id="0a7ac-155">For more information:</span></span>

* <span data-ttu-id="0a7ac-156">请参阅 .NET Core 指南中的 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="0a7ac-157">在命令行界面中为 Blazor Server模板 (`blazorserver`) 执行 help 命令：</span><span class="sxs-lookup"><span data-stu-id="0a7ac-157">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="0a7ac-158">设置Identity的基架</span><span class="sxs-lookup"><span data-stu-id="0a7ac-158">Scaffold Identity</span></span>

<span data-ttu-id="0a7ac-159">将 Identity 架构到 Blazor Server项目中：</span><span class="sxs-lookup"><span data-stu-id="0a7ac-159">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="0a7ac-160">[当前没有授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="0a7ac-161">[有授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0a7ac-162">其他资源</span><span class="sxs-lookup"><span data-stu-id="0a7ac-162">Additional resources</span></span>

* [<span data-ttu-id="0a7ac-163">快速入门：将 Microsoft 登录添加到 ASP.NET Core Web 应用</span><span class="sxs-lookup"><span data-stu-id="0a7ac-163">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="0a7ac-164">快速入门：使用 Microsoft 标识平台保护 ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="0a7ac-164">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <span data-ttu-id="0a7ac-165"><xref:host-and-deploy/proxy-load-balancer>：包含有关以下内容的指导：</span><span class="sxs-lookup"><span data-stu-id="0a7ac-165"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="0a7ac-166">使用转接头中间件跨代理服务器和内部网络保留 HTTPS 方案信息。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-166">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="0a7ac-167">其他方案和用例，包括手动方案配置、请求路径更改以进行正确请求路由，以及转发适用于 Linux 和非 IIS 反向代理的请求方案。</span><span class="sxs-lookup"><span data-stu-id="0a7ac-167">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
