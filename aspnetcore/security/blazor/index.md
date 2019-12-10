---
title: ASP.NET Core Blazor 身份验证和授权
author: guardrex
description: 了解 Blazor 身份验证和授权方案。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: d3860a5e7f6ae5a3193d657b77fc593c9bd39131
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944195"
---
# <a name="aspnet-core-opno-locblazor-authentication-and-authorization"></a><span data-ttu-id="115d3-103">ASP.NET Core Blazor 身份验证和授权</span><span class="sxs-lookup"><span data-stu-id="115d3-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="115d3-104">作者：[Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="115d3-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="115d3-105">ASP.NET Core 支持 Blazor 应用中的安全配置和管理。</span><span class="sxs-lookup"><span data-stu-id="115d3-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="115d3-106">Blazor 服务器和 Blazor WebAssembly 应用的安全方案存在差异。</span><span class="sxs-lookup"><span data-stu-id="115d3-106">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="115d3-107">由于 Blazor 服务器应用在服务器上运行，因此通过授权检查可确定以下内容：</span><span class="sxs-lookup"><span data-stu-id="115d3-107">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="115d3-108">向用户呈现的 UI 选项（例如，用户可以使用哪些菜单条目）。</span><span class="sxs-lookup"><span data-stu-id="115d3-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="115d3-109">应用程序和组件区域的访问规则。</span><span class="sxs-lookup"><span data-stu-id="115d3-109">Access rules for areas of the app and components.</span></span>

Blazor<span data-ttu-id="115d3-110"> WebAssembly 应用在客户端上运行。</span><span class="sxs-lookup"><span data-stu-id="115d3-110"> WebAssembly apps run on the client.</span></span> <span data-ttu-id="115d3-111">授权仅用于确定要显示的 UI 选项  。</span><span class="sxs-lookup"><span data-stu-id="115d3-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="115d3-112">由于用户可修改或绕过客户端检查，因此 Blazor WebAssembly 应用无法强制执行授权访问规则。</span><span class="sxs-lookup"><span data-stu-id="115d3-112">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

## <a name="authentication"></a><span data-ttu-id="115d3-113">身份验证</span><span class="sxs-lookup"><span data-stu-id="115d3-113">Authentication</span></span>

Blazor<span data-ttu-id="115d3-114"> 使用现有的 ASP.NET Core 身份验证机制来确立用户的身份。</span><span class="sxs-lookup"><span data-stu-id="115d3-114"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="115d3-115">具体机制取决于在 Blazor 服务器或 Blazor WebAssembly 托管 Blazor 应用的方式。</span><span class="sxs-lookup"><span data-stu-id="115d3-115">The exact mechanism depends on how the Blazor app is hosted, Blazor Server or Blazor WebAssembly.</span></span>

### <a name="opno-locblazor-server-authentication"></a>Blazor<span data-ttu-id="115d3-116"> 服务器身份验证</span><span class="sxs-lookup"><span data-stu-id="115d3-116"> Server authentication</span></span>

Blazor<span data-ttu-id="115d3-117"> 服务器应用通过使用 SignalR 创建的实时连接执行操作。</span><span class="sxs-lookup"><span data-stu-id="115d3-117"> Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="115d3-118">建立连接后，将处理[基于 SignalR 的应用的身份验证](xref:signalr/authn-and-authz)。</span><span class="sxs-lookup"><span data-stu-id="115d3-118">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="115d3-119">可以基于 cookie 或一些其他持有者令牌进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="115d3-119">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="115d3-120">创建项目后，Blazor 服务器项目模板可为你设置身份验证。</span><span class="sxs-lookup"><span data-stu-id="115d3-120">The Blazor Server project template can set up authentication for you when the project is created.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="115d3-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="115d3-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="115d3-122">按照 <xref:blazor/get-started> 一文中的 Visual Studio 指南操作，新建具有身份验证机制的 Blazor 服务器项目。</span><span class="sxs-lookup"><span data-stu-id="115d3-122">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="115d3-123">在“创建新的 ASP.NET Core Web 应用程序”对话框中选择“Blazor 服务器应用”模板后，在“身份验证”下选择“更改”     。</span><span class="sxs-lookup"><span data-stu-id="115d3-123">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="115d3-124">此时将打开一个对话框，为其他 ASP.NET Core 项目提供一组相同的身份验证机制：</span><span class="sxs-lookup"><span data-stu-id="115d3-124">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="115d3-125">**无身份验证**</span><span class="sxs-lookup"><span data-stu-id="115d3-125">**No Authentication**</span></span>
* <span data-ttu-id="115d3-126">**个人用户帐户** &ndash; 可以通过以下方式存储用户帐户：</span><span class="sxs-lookup"><span data-stu-id="115d3-126">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="115d3-127">使用 ASP.NET Core 的 [Identity](xref:security/authentication/identity) 系统在应用程序中存储。</span><span class="sxs-lookup"><span data-stu-id="115d3-127">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="115d3-128">使用 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 存储。</span><span class="sxs-lookup"><span data-stu-id="115d3-128">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="115d3-129">**工作或学校帐户**</span><span class="sxs-lookup"><span data-stu-id="115d3-129">**Work or School Accounts**</span></span>
* <span data-ttu-id="115d3-130">**Windows 身份验证**</span><span class="sxs-lookup"><span data-stu-id="115d3-130">**Windows Authentication**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="115d3-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="115d3-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="115d3-132">按照 <xref:blazor/get-started> 一文中的 Visual Studio Code 指南操作，新建具有身份验证机制的 Blazor 服务器项目：</span><span class="sxs-lookup"><span data-stu-id="115d3-132">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="115d3-133">下表中显示了可能的身份验证值 (`{AUTHENTICATION}`)。</span><span class="sxs-lookup"><span data-stu-id="115d3-133">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="115d3-134">身份验证机制</span><span class="sxs-lookup"><span data-stu-id="115d3-134">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="115d3-135">`{AUTHENTICATION}` 值</span><span class="sxs-lookup"><span data-stu-id="115d3-135">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="115d3-136">无身份验证</span><span class="sxs-lookup"><span data-stu-id="115d3-136">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="115d3-137">个人</span><span class="sxs-lookup"><span data-stu-id="115d3-137">Individual</span></span><br><span data-ttu-id="115d3-138">使用 ASP.NET Core Identity 将用户存储在应用程序中。</span><span class="sxs-lookup"><span data-stu-id="115d3-138">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="115d3-139">个人</span><span class="sxs-lookup"><span data-stu-id="115d3-139">Individual</span></span><br><span data-ttu-id="115d3-140">用户存储在 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 中。</span><span class="sxs-lookup"><span data-stu-id="115d3-140">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="115d3-141">工作或学校帐户</span><span class="sxs-lookup"><span data-stu-id="115d3-141">Work or School Accounts</span></span><br><span data-ttu-id="115d3-142">对一个租户进行组织身份验证。</span><span class="sxs-lookup"><span data-stu-id="115d3-142">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="115d3-143">工作或学校帐户</span><span class="sxs-lookup"><span data-stu-id="115d3-143">Work or School Accounts</span></span><br><span data-ttu-id="115d3-144">对多个租户进行组织身份验证。</span><span class="sxs-lookup"><span data-stu-id="115d3-144">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="115d3-145">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="115d3-145">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="115d3-146">该命令创建一个文件夹，它将 `{APP NAME}` 占位符提供的值作为名称，并使用文件夹名称作为应用程序的名称。</span><span class="sxs-lookup"><span data-stu-id="115d3-146">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="115d3-147">有关详细信息，请参阅 .NET Core 指南中的 [dotnet new](/dotnet/core/tools/dotnet-new) 命令。</span><span class="sxs-lookup"><span data-stu-id="115d3-147">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

1. Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.

1.

1.

-->

<!--
# [.NET Core CLI](#tab/netcore-cli/)

Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.

| Authentication mechanism                                                                 | `{AUTHENTICATION}` value |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| No Authentication                                                                        | `None`                   |
| Individual<br>Users stored in the app with ASP.NET Core Identity.                        | `Individual`             |
| Individual<br>Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Work or School Accounts<br>Organizational authentication for a single tenant.            | `SingleOrg`              |
| Work or School Accounts<br>Organizational authentication for multiple tenants.           | `MultiOrg`               |
| Windows Authentication                                                                   | `Windows`                |

The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name. For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.

-->

---

### <a name="opno-locblazor-webassembly-authentication"></a>Blazor<span data-ttu-id="115d3-148"> WebAssembly 身份验证</span><span class="sxs-lookup"><span data-stu-id="115d3-148"> WebAssembly authentication</span></span>

<span data-ttu-id="115d3-149">在 Blazor WebAssembly 应用中，可绕过身份验证检查，因为用户可修改所有客户端代码。</span><span class="sxs-lookup"><span data-stu-id="115d3-149">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="115d3-150">所有客户端应用程序技术都是如此，其中包括 JavaScript SPA 框架或任何操作系统的本机应用程序。</span><span class="sxs-lookup"><span data-stu-id="115d3-150">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="115d3-151">向应用的项目文件中添加 [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="115d3-151">Add a package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>

<span data-ttu-id="115d3-152">以下各节介绍了如何实现 Blazor WebAssembly 应用的自定义 `AuthenticationStateProvider` 服务。</span><span class="sxs-lookup"><span data-stu-id="115d3-152">Implementation of a custom `AuthenticationStateProvider` service for Blazor WebAssembly apps is covered in the following sections.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="115d3-153">AuthenticationStateProvider 服务</span><span class="sxs-lookup"><span data-stu-id="115d3-153">AuthenticationStateProvider service</span></span>

Blazor<span data-ttu-id="115d3-154"> 服务器应用包含一个内置 `AuthenticationStateProvider` 服务，可从 ASP.NET Core 的 `HttpContext.User` 获取身份验证状态数据。</span><span class="sxs-lookup"><span data-stu-id="115d3-154"> Server apps include a built-in `AuthenticationStateProvider` service that obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="115d3-155">这就是身份验证状态与现有 ASP.NET Core 服务器端身份验证机制的集成。</span><span class="sxs-lookup"><span data-stu-id="115d3-155">This is how authentication state integrates with existing ASP.NET Core server-side authentication mechanisms.</span></span>

<span data-ttu-id="115d3-156">`AuthenticationStateProvider` 是 `AuthorizeView` 组件和 `CascadingAuthenticationState` 组件用于获取身份验证状态的基础服务。</span><span class="sxs-lookup"><span data-stu-id="115d3-156">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="115d3-157">通常不直接使用 `AuthenticationStateProvider`。</span><span class="sxs-lookup"><span data-stu-id="115d3-157">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="115d3-158">使用本文后面介绍的 [AuthorizeView 组件](#authorizeview-component) 或 [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) 方法。</span><span class="sxs-lookup"><span data-stu-id="115d3-158">Use the [AuthorizeView component](#authorizeview-component) or [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="115d3-159">直接使用 `AuthenticationStateProvider` 的主要缺点是，如果基础身份验证状态数据发生更改，不会自动通知组件。</span><span class="sxs-lookup"><span data-stu-id="115d3-159">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="115d3-160">`AuthenticationStateProvider` 服务可以提供当前用户的 <xref:System.Security.Claims.ClaimsPrincipal> 数据，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="115d3-160">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<button @onclick="@LogUsername">Write user info to console</button>

@code {
    private async Task LogUsername()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

<span data-ttu-id="115d3-161">由于用户是 <xref:System.Security.Claims.ClaimsPrincipal>，如果 `user.Identity.IsAuthenticated` 为 `true`，可以枚举声明并评估角色成员身份。</span><span class="sxs-lookup"><span data-stu-id="115d3-161">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="115d3-162">有关依赖关系注入 (DI) 和服务的详细信息，请参阅<xref:blazor/dependency-injection>和 <xref:fundamentals/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="115d3-162">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="115d3-163">现自定义 AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="115d3-163">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="115d3-164">如果你正在生成 Blazor WebAssembly 应用，或者如果你的应用规范确实需要自定义提供程序，可实现提供程序并覆盖 `GetAuthenticationStateAsync`：</span><span class="sxs-lookup"><span data-stu-id="115d3-164">If you're building a Blazor WebAssembly app or if your app's specification absolutely requires a custom provider, implement a provider and override `GetAuthenticationStateAsync`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

namespace BlazorSample.Services
{
    public class CustomAuthStateProvider : AuthenticationStateProvider
    {
        public override Task<AuthenticationState> GetAuthenticationStateAsync()
        {
            var identity = new ClaimsIdentity(new[]
            {
                new Claim(ClaimTypes.Name, "mrfibuli"),
            }, "Fake authentication type");

            var user = new ClaimsPrincipal(identity);

            return Task.FromResult(new AuthenticationState(user));
        }
    }
}
```

<span data-ttu-id="115d3-165">在 `Startup.ConfigureServices` 中注册 `CustomAuthStateProvider` 服务：</span><span class="sxs-lookup"><span data-stu-id="115d3-165">The `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
// using Microsoft.AspNetCore.Components.Authorization;
// using BlazorSample.Services;

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="115d3-166">使用 `CustomAuthStateProvider` 后，通过用户名 `mrfibuli` 对所有用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="115d3-166">Using the `CustomAuthStateProvider`, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="115d3-167">公开身份验证状态作为级联参数</span><span class="sxs-lookup"><span data-stu-id="115d3-167">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="115d3-168">如果过程逻辑需要身份验证状态数据（例如在执行用户触发的操作时），请通过定义 `Task<AuthenticationState>` 类型的级联参数来获取身份验证状态数据：</span><span class="sxs-lookup"><span data-stu-id="115d3-168">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

```razor
@page "/"

<button @onclick="@LogUsername">Log username</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="115d3-169">在 Blazor WebAssembly 应用组件中，添加 `Microsoft.AspNetCore.Components.Authorization` 命名空间 (`@using Microsoft.AspNetCore.Components.Authorization`)。</span><span class="sxs-lookup"><span data-stu-id="115d3-169">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Components.Authorization` namespace (`@using Microsoft.AspNetCore.Components.Authorization`).</span></span>

<span data-ttu-id="115d3-170">如果 `user.Identity.IsAuthenticated` 为 `true`，可以枚举声明并评估角色成员身份。</span><span class="sxs-lookup"><span data-stu-id="115d3-170">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="115d3-171">使用 App.razor  文件中的 `AuthorizeRouteView` 和 `CascadingAuthenticationState` 组件设置 `Task<AuthenticationState>` 级联参数：</span><span class="sxs-lookup"><span data-stu-id="115d3-171">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components in the *App.razor* file:</span></span>

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

## <a name="authorization"></a><span data-ttu-id="115d3-172">授权</span><span class="sxs-lookup"><span data-stu-id="115d3-172">Authorization</span></span>

<span data-ttu-id="115d3-173">对用户进行身份验证后，应用授权规则来控制用户可以执行的操作  。</span><span class="sxs-lookup"><span data-stu-id="115d3-173">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="115d3-174">通常根据以下几点确定是授权访问还是拒绝访问：</span><span class="sxs-lookup"><span data-stu-id="115d3-174">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="115d3-175">已对用户进行身份验证（已登录）。</span><span class="sxs-lookup"><span data-stu-id="115d3-175">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="115d3-176">用户属于某个角色  。</span><span class="sxs-lookup"><span data-stu-id="115d3-176">A user is in a *role*.</span></span>
* <span data-ttu-id="115d3-177">用户具有声明  。</span><span class="sxs-lookup"><span data-stu-id="115d3-177">A user has a *claim*.</span></span>
* <span data-ttu-id="115d3-178">满足策略要求  。</span><span class="sxs-lookup"><span data-stu-id="115d3-178">A *policy* is satisfied.</span></span>

<span data-ttu-id="115d3-179">上述所有概念都与 ASP.NET Core MVC 或 Razor Pages 应用程序中的概念相同。</span><span class="sxs-lookup"><span data-stu-id="115d3-179">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="115d3-180">有关 ASP.NET Core 安全性的详细信息，请参阅 [ASP.NET Core 安全性和标识](xref:security/index)下的文章。</span><span class="sxs-lookup"><span data-stu-id="115d3-180">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="115d3-181">AuthorizeView 组件</span><span class="sxs-lookup"><span data-stu-id="115d3-181">AuthorizeView component</span></span>

<span data-ttu-id="115d3-182">`AuthorizeView` 组件根据用户是否有权查看来选择性地显示 UI。</span><span class="sxs-lookup"><span data-stu-id="115d3-182">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="115d3-183">如果只需要为用户显示数据，而不需要在过程逻辑中使用用户的标识，那么此方法很有用  。</span><span class="sxs-lookup"><span data-stu-id="115d3-183">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="115d3-184">该组件公开了一个 `AuthenticationState` 类型的 `context` 变量，可以使用该变量来访问有关已登录用户的信息：</span><span class="sxs-lookup"><span data-stu-id="115d3-184">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="115d3-185">另外，如果用户未经过身份验证，你还可以提供不同的内容以供显示：</span><span class="sxs-lookup"><span data-stu-id="115d3-185">You can also supply different content for display if the user isn't authenticated:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="115d3-186">`<Authorized>` 和 `<NotAuthorized>` 标记的内容可以包括任意项，如其他交互式组件。</span><span class="sxs-lookup"><span data-stu-id="115d3-186">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="115d3-187">[授权](#authorization)一节中介绍了授权条件，如用于控制 UI 选项或访问权限的角色或策略。</span><span class="sxs-lookup"><span data-stu-id="115d3-187">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="115d3-188">如果未指定授权条件，则 `AuthorizeView` 使用默认策略，并且：</span><span class="sxs-lookup"><span data-stu-id="115d3-188">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="115d3-189">将经过身份验证（已登录）的用户视为已授权。</span><span class="sxs-lookup"><span data-stu-id="115d3-189">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="115d3-190">将未经过身份验证（已注销）的用户视为未授权。</span><span class="sxs-lookup"><span data-stu-id="115d3-190">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="115d3-191">基于角色和基于策略的授权</span><span class="sxs-lookup"><span data-stu-id="115d3-191">Role-based and policy-based authorization</span></span>

<span data-ttu-id="115d3-192">`AuthorizeView` 组件支持基于角色或基于策略的授权   。</span><span class="sxs-lookup"><span data-stu-id="115d3-192">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="115d3-193">对于基于角色的授权，请使用 `Roles` 参数：</span><span class="sxs-lookup"><span data-stu-id="115d3-193">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="115d3-194">有关详细信息，请参阅 <xref:security/authorization/roles>。</span><span class="sxs-lookup"><span data-stu-id="115d3-194">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="115d3-195">对于基于策略的授权，请使用 `Policy` 参数：</span><span class="sxs-lookup"><span data-stu-id="115d3-195">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="115d3-196">基于策略的授权包含一个特例，即基于声明的授权。</span><span class="sxs-lookup"><span data-stu-id="115d3-196">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="115d3-197">例如，可以定义一个要求用户具有特定声明的策略。</span><span class="sxs-lookup"><span data-stu-id="115d3-197">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="115d3-198">有关详细信息，请参阅 <xref:security/authorization/policies>。</span><span class="sxs-lookup"><span data-stu-id="115d3-198">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="115d3-199">这些 API 可用于 Blazor 服务器或 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="115d3-199">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="115d3-200">如果 `Roles` 或 `Policy` 均未指定，则 `AuthorizeView` 使用默认策略。</span><span class="sxs-lookup"><span data-stu-id="115d3-200">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="115d3-201">异步身份验证期间显示的内容</span><span class="sxs-lookup"><span data-stu-id="115d3-201">Content displayed during asynchronous authentication</span></span>

<span data-ttu-id="115d3-202">通过 Blazor，可通过异步方式确定身份验证状态  。</span><span class="sxs-lookup"><span data-stu-id="115d3-202">Blazor allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="115d3-203">此方法的主要应用场景是 Blazor WebAssembly 应用向外部终结点发出请求来进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="115d3-203">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="115d3-204">正在进行身份验证时，`AuthorizeView` 默认情况下不显示任何内容。</span><span class="sxs-lookup"><span data-stu-id="115d3-204">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="115d3-205">若要在进行身份验证期间显示内容，请使用 `<Authorizing>` 元素：</span><span class="sxs-lookup"><span data-stu-id="115d3-205">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

<span data-ttu-id="115d3-206">此方法通常不适用于 Blazor 服务器应用。</span><span class="sxs-lookup"><span data-stu-id="115d3-206">This approach isn't normally applicable to Blazor Server apps.</span></span> <span data-ttu-id="115d3-207">身份验证状态一经确立，Blazor 服务器应用便会立即获知身份验证状态。</span><span class="sxs-lookup"><span data-stu-id="115d3-207">Blazor Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="115d3-208">`Authorizing` 内容可在 Blazor 服务器应用的 `AuthorizeView` 组件中提供，但该内容永远不会显示。</span><span class="sxs-lookup"><span data-stu-id="115d3-208">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="115d3-209">[Authorize] 属性</span><span class="sxs-lookup"><span data-stu-id="115d3-209">[Authorize] attribute</span></span>

<span data-ttu-id="115d3-210">`[Authorize]` 属性可在 Razor 组件中使用：</span><span class="sxs-lookup"><span data-stu-id="115d3-210">The `[Authorize]` attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!NOTE]
> <span data-ttu-id="115d3-211">在 Blazor WebAssembly 应用组件中，向本部分的示例中添加 `Microsoft.AspNetCore.Authorization` 命名空间 (`@using Microsoft.AspNetCore.Authorization`)。</span><span class="sxs-lookup"><span data-stu-id="115d3-211">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` namespace (`@using Microsoft.AspNetCore.Authorization`) to the examples in this section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="115d3-212">仅对通过 Blazor 路由器到达的 `@page` 组件使用 `[Authorize]`。</span><span class="sxs-lookup"><span data-stu-id="115d3-212">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="115d3-213">授权仅作为路由的一个方面执行，而不是作为页面中呈现的子组件来执行  。</span><span class="sxs-lookup"><span data-stu-id="115d3-213">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="115d3-214">若要授权在页面中显示特定部分，请改用 `AuthorizeView`。</span><span class="sxs-lookup"><span data-stu-id="115d3-214">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="115d3-215">`[Authorize]` 属性还支持基于角色或基于策略的授权。</span><span class="sxs-lookup"><span data-stu-id="115d3-215">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="115d3-216">对于基于角色的授权，请使用 `Roles` 参数：</span><span class="sxs-lookup"><span data-stu-id="115d3-216">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="115d3-217">对于基于策略的授权，请使用 `Policy` 参数：</span><span class="sxs-lookup"><span data-stu-id="115d3-217">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="115d3-218">如果 `Roles` 或 `Policy` 均未指定，则 `[Authorize]` 使用默认策略，默认情况下，它会：</span><span class="sxs-lookup"><span data-stu-id="115d3-218">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="115d3-219">将经过身份验证（已登录）的用户视为已授权。</span><span class="sxs-lookup"><span data-stu-id="115d3-219">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="115d3-220">将未经过身份验证（已注销）的用户视为未授权。</span><span class="sxs-lookup"><span data-stu-id="115d3-220">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="115d3-221">使用路由器组件自定义未授权的内容</span><span class="sxs-lookup"><span data-stu-id="115d3-221">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="115d3-222">`Router` 组件与 `AuthorizeRouteView` 组件搭配使用时，可允许应用程序在以下情况下指定自定义内容：</span><span class="sxs-lookup"><span data-stu-id="115d3-222">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="115d3-223">找不到内容。</span><span class="sxs-lookup"><span data-stu-id="115d3-223">Content isn't found.</span></span>
* <span data-ttu-id="115d3-224">用户不符合应用于组件的 `[Authorize]` 条件。</span><span class="sxs-lookup"><span data-stu-id="115d3-224">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="115d3-225">[`[Authorize]` 属性](#authorize-attribute)一节中介绍了 `[Authorize]` 属性。</span><span class="sxs-lookup"><span data-stu-id="115d3-225">The `[Authorize]` attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="115d3-226">正在进行异步身份验证。</span><span class="sxs-lookup"><span data-stu-id="115d3-226">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="115d3-227">在默认的 Blazor 服务器项目模板中，App.razor 文件演示了如何设置自定义内容  ：</span><span class="sxs-lookup"><span data-stu-id="115d3-227">In the default Blazor Server project template, the *App.razor* file demonstrates how to set custom content:</span></span>

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
            <NotAuthorized>
                <h1>Sorry</h1>
                <p>You're not authorized to reach this page.</p>
                <p>You may need to log in as a different user.</p>
            </NotAuthorized>
            <Authorizing>
                <h1>Authentication in progress</h1>
                <p>Only visible while authentication is in progress.</p>
            </Authorizing>
        </AuthorizeRouteView>
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

<span data-ttu-id="115d3-228">`<NotFound>`、`<NotAuthorized>` 和 `<Authorizing>` 标记的内容可以包括任意项，如其他交互式组件。</span><span class="sxs-lookup"><span data-stu-id="115d3-228">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="115d3-229">如果未指定 `<NotAuthorized>` 元素，`AuthorizeRouteView` 就会使用以下回退消息：</span><span class="sxs-lookup"><span data-stu-id="115d3-229">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="115d3-230">有关身份验证状态更改的通知</span><span class="sxs-lookup"><span data-stu-id="115d3-230">Notification about authentication state changes</span></span>

<span data-ttu-id="115d3-231">如果应用程序确定基础身份验证状态数据已更改（例如，由于用户已注销或其他用户已更改其角色），则自定义 `AuthenticationStateProvider` 可以选择对 `AuthenticationStateProvider` 基类调用 `NotifyAuthenticationStateChanged` 方法。</span><span class="sxs-lookup"><span data-stu-id="115d3-231">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a custom `AuthenticationStateProvider` can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="115d3-232">这会通知身份验证状态数据（例如 `AuthorizeView`）使用者使用新数据重新呈现。</span><span class="sxs-lookup"><span data-stu-id="115d3-232">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="115d3-233">过程逻辑</span><span class="sxs-lookup"><span data-stu-id="115d3-233">Procedural logic</span></span>

<span data-ttu-id="115d3-234">如果作为过程逻辑的一部分应用程序需要检查授权规则，请使用 `Task<AuthenticationState>` 类型的级联参数获取用户的 <xref:System.Security.Claims.ClaimsPrincipal>。</span><span class="sxs-lookup"><span data-stu-id="115d3-234">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="115d3-235">`Task<AuthenticationState>` 可以与其他服务（例如 `IAuthorizationService`）结合使用以评估策略。</span><span class="sxs-lookup"><span data-stu-id="115d3-235">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```razor
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="115d3-236">在 Blazor WebAssembly 应用组件中，添加 `Microsoft.AspNetCore.Authorization` 和 `Microsoft.AspNetCore.Components.Authorization` 命名空间：</span><span class="sxs-lookup"><span data-stu-id="115d3-236">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a><span data-ttu-id="115d3-237">Blazor WebAssembly 应用中的授权</span><span class="sxs-lookup"><span data-stu-id="115d3-237">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="115d3-238">在 Blazor WebAssembly 应用中，可绕过授权检查，因为用户可修改所有客户端代码。</span><span class="sxs-lookup"><span data-stu-id="115d3-238">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="115d3-239">所有客户端应用程序技术都是如此，其中包括 JavaScript SPA 框架或任何操作系统的本机应用程序。</span><span class="sxs-lookup"><span data-stu-id="115d3-239">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="115d3-240">**始终对客户端应用程序访问的任何 API 终结点内的服务器执行授权检查。**</span><span class="sxs-lookup"><span data-stu-id="115d3-240">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="115d3-241">排查错误</span><span class="sxs-lookup"><span data-stu-id="115d3-241">Troubleshoot errors</span></span>

<span data-ttu-id="115d3-242">常见错误：</span><span class="sxs-lookup"><span data-stu-id="115d3-242">Common errors:</span></span>

* <span data-ttu-id="115d3-243">**授权需要 Task\<AuthenticationState> 类型的级联参数。考虑使用 CascadingAuthenticationState 来提供此参数。**</span><span class="sxs-lookup"><span data-stu-id="115d3-243">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="115d3-244">**对于 `authenticationStateTask`，收到了 `null` 值**</span><span class="sxs-lookup"><span data-stu-id="115d3-244">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="115d3-245">项目可能不是使用启用了身份验证的 Blazor 服务器模板创建的。</span><span class="sxs-lookup"><span data-stu-id="115d3-245">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="115d3-246">使用 `<CascadingAuthenticationState>` 将 UI 树的某些部分括起来，例如下面所示的 App.razor  ：</span><span class="sxs-lookup"><span data-stu-id="115d3-246">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in *App.razor* as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="115d3-247">`CascadingAuthenticationState` 提供 `Task<AuthenticationState>` 级联参数，这是它从基础 `AuthenticationStateProvider` DI 服务那里接收的参数。</span><span class="sxs-lookup"><span data-stu-id="115d3-247">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="115d3-248">其他资源</span><span class="sxs-lookup"><span data-stu-id="115d3-248">Additional resources</span></span>

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
