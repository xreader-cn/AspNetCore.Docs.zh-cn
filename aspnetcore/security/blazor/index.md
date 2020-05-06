---
title: ASP.NET Core Blazor 身份验证和授权
author: guardrex
description: 了解 Blazor 身份验证和授权方案。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: d55880265ed1ceedf8f115412e5ac47309521239
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772890"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="eecec-103">ASP.NET Core Blazor 身份验证和授权</span><span class="sxs-lookup"><span data-stu-id="eecec-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="eecec-104">作者：[Steve Sanderson](https://github.com/SteveSandersonMS) 及 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="eecec-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> <span data-ttu-id="eecec-105">对于本文中适用于 Blazor WebAssembly 的指导，ASP.NET Core Blazor WebAssembly 模板必须是 3.2 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="eecec-105">For the guidance in this article that applies to Blazor WebAssembly, the ASP.NET Core Blazor WebAssembly template version 3.2 or later is required.</span></span> <span data-ttu-id="eecec-106">如果不使用 Visual Studio 版本 16.6 预览版 2 或更高版本，可按照以下 <xref:blazor/get-started> 中的指导获取最新的 Blazor WebAssembly 模板。</span><span class="sxs-lookup"><span data-stu-id="eecec-106">If you aren't using Visual Studio version 16.6 Preview 2 or later, obtain the latest Blazor WebAssembly template by following the guidance in <xref:blazor/get-started>.</span></span>

<span data-ttu-id="eecec-107">ASP.NET Core 支持 Blazor 应用中的安全配置和管理。</span><span class="sxs-lookup"><span data-stu-id="eecec-107">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="eecec-108">Blazor 服务器和 Blazor WebAssembly 应用的安全方案存在差异。</span><span class="sxs-lookup"><span data-stu-id="eecec-108">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="eecec-109">由于 Blazor 服务器应用在服务器上运行，因此通过授权检查可确定以下内容：</span><span class="sxs-lookup"><span data-stu-id="eecec-109">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="eecec-110">向用户呈现的 UI 选项（例如，用户可以使用哪些菜单条目）。</span><span class="sxs-lookup"><span data-stu-id="eecec-110">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="eecec-111">应用程序和组件区域的访问规则。</span><span class="sxs-lookup"><span data-stu-id="eecec-111">Access rules for areas of the app and components.</span></span>

Blazor<span data-ttu-id="eecec-112"> WebAssembly 应用在客户端上运行。</span><span class="sxs-lookup"><span data-stu-id="eecec-112"> WebAssembly apps run on the client.</span></span> <span data-ttu-id="eecec-113">授权仅用于确定要显示的 UI 选项  。</span><span class="sxs-lookup"><span data-stu-id="eecec-113">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="eecec-114">由于用户可修改或绕过客户端检查，因此 Blazor WebAssembly 应用无法强制执行授权访问规则。</span><span class="sxs-lookup"><span data-stu-id="eecec-114">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="eecec-115">[Razor Pages 授权约定](xref:security/authorization/razor-pages-authorization) 不适用于可路由的 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="eecec-115">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="eecec-116">如果非可路由的 Razor 组件[嵌入在页面中](xref:blazor/integrate-components#render-components-from-a-page-or-view)，则页面的授权约定会间接影响 Razor 组件以及其余页面内容。</span><span class="sxs-lookup"><span data-stu-id="eecec-116">If a non-routable Razor component is [embedded in a page](xref:blazor/integrate-components#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="eecec-117">Razor 组件中不支持 <xref:Microsoft.AspNetCore.Identity.SignInManager%601> 和 <xref:Microsoft.AspNetCore.Identity.UserManager%601>。</span><span class="sxs-lookup"><span data-stu-id="eecec-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="eecec-118">身份验证</span><span class="sxs-lookup"><span data-stu-id="eecec-118">Authentication</span></span>

Blazor<span data-ttu-id="eecec-119"> 使用现有的 ASP.NET Core 身份验证机制来确立用户的身份。</span><span class="sxs-lookup"><span data-stu-id="eecec-119"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="eecec-120">具体机制取决于是用 Blazor WebAssembly 还是 Blazor 服务器托管 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="eecec-120">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="blazor-webassembly-authentication"></a>Blazor<span data-ttu-id="eecec-121"> WebAssembly 身份验证</span><span class="sxs-lookup"><span data-stu-id="eecec-121"> WebAssembly authentication</span></span>

<span data-ttu-id="eecec-122">在 Blazor WebAssembly 应用中，可绕过身份验证检查，因为用户可修改所有客户端代码。</span><span class="sxs-lookup"><span data-stu-id="eecec-122">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="eecec-123">所有客户端应用程序技术都是如此，其中包括 JavaScript SPA 框架或任何操作系统的本机应用程序。</span><span class="sxs-lookup"><span data-stu-id="eecec-123">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="eecec-124">添加以下内容：</span><span class="sxs-lookup"><span data-stu-id="eecec-124">Add the following:</span></span>

* <span data-ttu-id="eecec-125">在应用的项目文件中添加 [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="eecec-125">A package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="eecec-126">在应用的 *_Imports.razor* 文件中添加 `Microsoft.AspNetCore.Components.Authorization` 命名空间。</span><span class="sxs-lookup"><span data-stu-id="eecec-126">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's *_Imports.razor* file.</span></span>

<span data-ttu-id="eecec-127">为处理身份验证，需实现内置或自定义 `AuthenticationStateProvider` 服务，以下几节对此进行了介绍。</span><span class="sxs-lookup"><span data-stu-id="eecec-127">To handle authentication, implementation of a built-in or custom `AuthenticationStateProvider` service is covered in the following sections.</span></span>

<span data-ttu-id="eecec-128">有关创建应用和配置的详细信息，请参阅 <xref:security/blazor/webassembly/index>。</span><span class="sxs-lookup"><span data-stu-id="eecec-128">For more information on creating apps and configuration, see <xref:security/blazor/webassembly/index>.</span></span>

### <a name="blazor-server-authentication"></a>Blazor<span data-ttu-id="eecec-129"> 服务器身份验证</span><span class="sxs-lookup"><span data-stu-id="eecec-129"> Server authentication</span></span>

Blazor<span data-ttu-id="eecec-130"> 服务器应用通过使用 SignalR 创建的实时连接执行操作。</span><span class="sxs-lookup"><span data-stu-id="eecec-130"> Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="eecec-131">建立连接后，将处理[基于 SignalR 的应用的身份验证](xref:signalr/authn-and-authz)。</span><span class="sxs-lookup"><span data-stu-id="eecec-131">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="eecec-132">可以基于 cookie 或一些其他持有者令牌进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="eecec-132">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="eecec-133">有关创建应用和配置的详细信息，请参阅 <xref:security/blazor/server/index>。</span><span class="sxs-lookup"><span data-stu-id="eecec-133">For more information on creating apps and configuration, see <xref:security/blazor/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="eecec-134">AuthenticationStateProvider 服务</span><span class="sxs-lookup"><span data-stu-id="eecec-134">AuthenticationStateProvider service</span></span>

<span data-ttu-id="eecec-135">内置的 `AuthenticationStateProvider` 服务可从 ASP.NET Core 的 `HttpContext.User` 获取身份验证状态数据。</span><span class="sxs-lookup"><span data-stu-id="eecec-135">The built-in `AuthenticationStateProvider` service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="eecec-136">身份验证状态就是这样与现有 ASP.NET Core 身份验证机制集成。</span><span class="sxs-lookup"><span data-stu-id="eecec-136">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="eecec-137">`AuthenticationStateProvider` 是 `AuthorizeView` 组件和 `CascadingAuthenticationState` 组件用于获取身份验证状态的基础服务。</span><span class="sxs-lookup"><span data-stu-id="eecec-137">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="eecec-138">通常不直接使用 `AuthenticationStateProvider`。</span><span class="sxs-lookup"><span data-stu-id="eecec-138">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="eecec-139">使用本文后面介绍的 [AuthorizeView 组件](#authorizeview-component) 或 [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) 方法。</span><span class="sxs-lookup"><span data-stu-id="eecec-139">Use the [AuthorizeView component](#authorizeview-component) or [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="eecec-140">直接使用 `AuthenticationStateProvider` 的主要缺点是，如果基础身份验证状态数据发生更改，不会自动通知组件。</span><span class="sxs-lookup"><span data-stu-id="eecec-140">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="eecec-141">`AuthenticationStateProvider` 服务可以提供当前用户的 <xref:System.Security.Claims.ClaimsPrincipal> 数据，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="eecec-141">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type &ndash; @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="eecec-142">由于用户是 <xref:System.Security.Claims.ClaimsPrincipal>，如果 `user.Identity.IsAuthenticated` 为 `true`，可以枚举声明并评估角色成员身份。</span><span class="sxs-lookup"><span data-stu-id="eecec-142">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="eecec-143">有关依赖关系注入 (DI) 和服务的详细信息，请参阅<xref:blazor/dependency-injection>和 <xref:fundamentals/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="eecec-143">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="eecec-144">现自定义 AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="eecec-144">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="eecec-145">如果应用需要自定义提供程序，请实现 `AuthenticationStateProvider` 并替代 `GetAuthenticationStateAsync`：</span><span class="sxs-lookup"><span data-stu-id="eecec-145">If the app requires a custom provider, implement `AuthenticationStateProvider` and override `GetAuthenticationStateAsync`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

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
```

<span data-ttu-id="eecec-146">在 Blazor WebAssembly 应用中，`CustomAuthStateProvider` 服务已在 Program.cs  的 `Main` 中注册：</span><span class="sxs-lookup"><span data-stu-id="eecec-146">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="eecec-147">在 Blazor 服务器应用中，在 `Startup.ConfigureServices`中注册 `CustomAuthStateProvider` 服务：</span><span class="sxs-lookup"><span data-stu-id="eecec-147">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="eecec-148">使用以上示例中的 `CustomAuthStateProvider`，通过用户名 `mrfibuli` 对所有用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="eecec-148">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="eecec-149">公开身份验证状态作为级联参数</span><span class="sxs-lookup"><span data-stu-id="eecec-149">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="eecec-150">如果过程逻辑需要身份验证状态数据（例如在执行用户触发的操作时），请通过定义 `Task<AuthenticationState>` 类型的级联参数来获取身份验证状态数据：</span><span class="sxs-lookup"><span data-stu-id="eecec-150">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="eecec-151">如果 `user.Identity.IsAuthenticated` 为 `true`，可以枚举声明并评估角色成员身份。</span><span class="sxs-lookup"><span data-stu-id="eecec-151">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="eecec-152">使用 `App` 组件 (App.razor )  中的 `AuthorizeRouteView` 和 `CascadingAuthenticationState` 组件设置 `Task<AuthenticationState>` 级联参数：</span><span class="sxs-lookup"><span data-stu-id="eecec-152">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components in the `App` component (*App.razor*):</span></span>

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

<span data-ttu-id="eecec-153">在 Blazor WebAssembly 应用中，将服务选项和授权添加到 `Program.Main`：</span><span class="sxs-lookup"><span data-stu-id="eecec-153">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="eecec-154">在 Blazor 服务器应用中，已存在选项和授权服务，因此无需进一步操作。</span><span class="sxs-lookup"><span data-stu-id="eecec-154">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="eecec-155">授权</span><span class="sxs-lookup"><span data-stu-id="eecec-155">Authorization</span></span>

<span data-ttu-id="eecec-156">对用户进行身份验证后，应用授权规则来控制用户可以执行的操作  。</span><span class="sxs-lookup"><span data-stu-id="eecec-156">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="eecec-157">通常根据以下几点确定是授权访问还是拒绝访问：</span><span class="sxs-lookup"><span data-stu-id="eecec-157">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="eecec-158">已对用户进行身份验证（已登录）。</span><span class="sxs-lookup"><span data-stu-id="eecec-158">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="eecec-159">用户属于某个角色  。</span><span class="sxs-lookup"><span data-stu-id="eecec-159">A user is in a *role*.</span></span>
* <span data-ttu-id="eecec-160">用户具有声明  。</span><span class="sxs-lookup"><span data-stu-id="eecec-160">A user has a *claim*.</span></span>
* <span data-ttu-id="eecec-161">满足策略要求  。</span><span class="sxs-lookup"><span data-stu-id="eecec-161">A *policy* is satisfied.</span></span>

<span data-ttu-id="eecec-162">上述所有概念都与 ASP.NET Core MVC 或 Razor Pages 应用中的概念相同。</span><span class="sxs-lookup"><span data-stu-id="eecec-162">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="eecec-163">有关 ASP.NET Core 安全性的详细信息，请参阅 [ASP.NET Core 安全性和 Identity](xref:security/index) 下的文章。</span><span class="sxs-lookup"><span data-stu-id="eecec-163">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="eecec-164">AuthorizeView 组件</span><span class="sxs-lookup"><span data-stu-id="eecec-164">AuthorizeView component</span></span>

<span data-ttu-id="eecec-165">`AuthorizeView` 组件根据用户是否有权查看来选择性地显示 UI。</span><span class="sxs-lookup"><span data-stu-id="eecec-165">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="eecec-166">如果只需要为用户显示数据，而不需要在过程逻辑中使用用户的标识，那么此方法很有用  。</span><span class="sxs-lookup"><span data-stu-id="eecec-166">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="eecec-167">该组件公开了一个 `AuthenticationState` 类型的 `context` 变量，可以使用该变量来访问有关已登录用户的信息：</span><span class="sxs-lookup"><span data-stu-id="eecec-167">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="eecec-168">另外，如果用户未经过身份验证，你还可以提供不同的内容以供显示：</span><span class="sxs-lookup"><span data-stu-id="eecec-168">You can also supply different content for display if the user isn't authenticated:</span></span>

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

<span data-ttu-id="eecec-169">可以在 `NavMenu` 组件 (Shared/NavMenu.razor)  中使用 `AuthorizeView` 组件来显示 `NavLink` 的列表项 (`<li>...</li>`)，但请注意，此方法仅从呈现的输出中删除列表项。</span><span class="sxs-lookup"><span data-stu-id="eecec-169">The `AuthorizeView` component can be used in the `NavMenu` component (*Shared/NavMenu.razor*) to display a list item (`<li>...</li>`) for a `NavLink`, but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="eecec-170">它不会阻止用户导航到该组件。</span><span class="sxs-lookup"><span data-stu-id="eecec-170">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="eecec-171">`<Authorized>` 和 `<NotAuthorized>` 标记的内容可以包括任意项，如其他交互式组件。</span><span class="sxs-lookup"><span data-stu-id="eecec-171">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="eecec-172">[授权](#authorization)一节中介绍了授权条件，如用于控制 UI 选项或访问权限的角色或策略。</span><span class="sxs-lookup"><span data-stu-id="eecec-172">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="eecec-173">如果未指定授权条件，则 `AuthorizeView` 使用默认策略，并且：</span><span class="sxs-lookup"><span data-stu-id="eecec-173">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="eecec-174">将经过身份验证（已登录）的用户视为已授权。</span><span class="sxs-lookup"><span data-stu-id="eecec-174">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="eecec-175">将未经过身份验证（已注销）的用户视为未授权。</span><span class="sxs-lookup"><span data-stu-id="eecec-175">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="eecec-176">基于角色和基于策略的授权</span><span class="sxs-lookup"><span data-stu-id="eecec-176">Role-based and policy-based authorization</span></span>

<span data-ttu-id="eecec-177">`AuthorizeView` 组件支持基于角色或基于策略的授权   。</span><span class="sxs-lookup"><span data-stu-id="eecec-177">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="eecec-178">对于基于角色的授权，请使用 `Roles` 参数：</span><span class="sxs-lookup"><span data-stu-id="eecec-178">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="eecec-179">有关详细信息，请参阅 <xref:security/authorization/roles>。</span><span class="sxs-lookup"><span data-stu-id="eecec-179">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="eecec-180">对于基于策略的授权，请使用 `Policy` 参数：</span><span class="sxs-lookup"><span data-stu-id="eecec-180">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="eecec-181">基于策略的授权包含一个特例，即基于声明的授权。</span><span class="sxs-lookup"><span data-stu-id="eecec-181">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="eecec-182">例如，可以定义一个要求用户具有特定声明的策略。</span><span class="sxs-lookup"><span data-stu-id="eecec-182">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="eecec-183">有关详细信息，请参阅 <xref:security/authorization/policies>。</span><span class="sxs-lookup"><span data-stu-id="eecec-183">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="eecec-184">这些 API 可用于 Blazor 服务器或 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="eecec-184">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="eecec-185">如果 `Roles` 或 `Policy` 均未指定，则 `AuthorizeView` 使用默认策略。</span><span class="sxs-lookup"><span data-stu-id="eecec-185">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="eecec-186">异步身份验证期间显示的内容</span><span class="sxs-lookup"><span data-stu-id="eecec-186">Content displayed during asynchronous authentication</span></span>

<span data-ttu-id="eecec-187">通过 Blazor，可通过异步方式确定身份验证状态  。</span><span class="sxs-lookup"><span data-stu-id="eecec-187">Blazor allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="eecec-188">此方法的主要应用场景是 Blazor WebAssembly 应用向外部终结点发出请求来进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="eecec-188">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="eecec-189">正在进行身份验证时，`AuthorizeView` 默认情况下不显示任何内容。</span><span class="sxs-lookup"><span data-stu-id="eecec-189">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="eecec-190">若要在进行身份验证期间显示内容，请使用 `<Authorizing>` 元素：</span><span class="sxs-lookup"><span data-stu-id="eecec-190">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

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

<span data-ttu-id="eecec-191">此方法通常不适用于 Blazor 服务器应用。</span><span class="sxs-lookup"><span data-stu-id="eecec-191">This approach isn't normally applicable to Blazor Server apps.</span></span> <span data-ttu-id="eecec-192">身份验证状态一经确立，Blazor 服务器应用便会立即获知身份验证状态。</span><span class="sxs-lookup"><span data-stu-id="eecec-192">Blazor Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="eecec-193">`Authorizing` 内容可在 Blazor 服务器应用的 `AuthorizeView` 组件中提供，但该内容永远不会显示。</span><span class="sxs-lookup"><span data-stu-id="eecec-193">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="eecec-194">[Authorize] 属性</span><span class="sxs-lookup"><span data-stu-id="eecec-194">[Authorize] attribute</span></span>

<span data-ttu-id="eecec-195">`[Authorize]` 属性可在 Razor 组件中使用：</span><span class="sxs-lookup"><span data-stu-id="eecec-195">The `[Authorize]` attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="eecec-196">仅对通过 Blazor 路由器到达的 `@page` 组件使用 `[Authorize]`。</span><span class="sxs-lookup"><span data-stu-id="eecec-196">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="eecec-197">授权仅作为路由的一个方面执行，而不是作为页面中呈现的子组件来执行  。</span><span class="sxs-lookup"><span data-stu-id="eecec-197">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="eecec-198">若要授权在页面中显示特定部分，请改用 `AuthorizeView`。</span><span class="sxs-lookup"><span data-stu-id="eecec-198">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="eecec-199">`[Authorize]` 属性还支持基于角色或基于策略的授权。</span><span class="sxs-lookup"><span data-stu-id="eecec-199">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="eecec-200">对于基于角色的授权，请使用 `Roles` 参数：</span><span class="sxs-lookup"><span data-stu-id="eecec-200">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="eecec-201">对于基于策略的授权，请使用 `Policy` 参数：</span><span class="sxs-lookup"><span data-stu-id="eecec-201">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="eecec-202">如果 `Roles` 或 `Policy` 均未指定，则 `[Authorize]` 使用默认策略，默认情况下，它会：</span><span class="sxs-lookup"><span data-stu-id="eecec-202">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="eecec-203">将经过身份验证（已登录）的用户视为已授权。</span><span class="sxs-lookup"><span data-stu-id="eecec-203">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="eecec-204">将未经过身份验证（已注销）的用户视为未授权。</span><span class="sxs-lookup"><span data-stu-id="eecec-204">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="eecec-205">使用路由器组件自定义未授权的内容</span><span class="sxs-lookup"><span data-stu-id="eecec-205">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="eecec-206">`Router` 组件与 `AuthorizeRouteView` 组件搭配使用时，可允许应用程序在以下情况下指定自定义内容：</span><span class="sxs-lookup"><span data-stu-id="eecec-206">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="eecec-207">找不到内容。</span><span class="sxs-lookup"><span data-stu-id="eecec-207">Content isn't found.</span></span>
* <span data-ttu-id="eecec-208">用户不符合应用于组件的 `[Authorize]` 条件。</span><span class="sxs-lookup"><span data-stu-id="eecec-208">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="eecec-209">[`[Authorize]` 属性](#authorize-attribute)一节中介绍了 `[Authorize]` 属性。</span><span class="sxs-lookup"><span data-stu-id="eecec-209">The `[Authorize]` attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="eecec-210">正在进行异步身份验证。</span><span class="sxs-lookup"><span data-stu-id="eecec-210">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="eecec-211">在默认的 Blazor 服务器项目模板中，`App` 组件 (App.razor) 演示了如何设置自定义内容：</span><span class="sxs-lookup"><span data-stu-id="eecec-211">In the default Blazor Server project template, the `App` component (*App.razor*) demonstrates how to set custom content:</span></span>

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

<span data-ttu-id="eecec-212">`<NotFound>`、`<NotAuthorized>` 和 `<Authorizing>` 标记的内容可以包括任意项，如其他交互式组件。</span><span class="sxs-lookup"><span data-stu-id="eecec-212">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="eecec-213">如果未指定 `<NotAuthorized>` 元素，`AuthorizeRouteView` 就会使用以下回退消息：</span><span class="sxs-lookup"><span data-stu-id="eecec-213">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="eecec-214">有关身份验证状态更改的通知</span><span class="sxs-lookup"><span data-stu-id="eecec-214">Notification about authentication state changes</span></span>

<span data-ttu-id="eecec-215">如果应用确定基础身份验证状态数据已更改（例如由于用户注销或其他用户更改了其角色），自定义 [custom AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) 可以选择对 `AuthenticationStateProvider` 基类调用 `NotifyAuthenticationStateChanged` 方法。</span><span class="sxs-lookup"><span data-stu-id="eecec-215">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="eecec-216">这会通知身份验证状态数据（例如 `AuthorizeView`）使用者使用新数据重新呈现。</span><span class="sxs-lookup"><span data-stu-id="eecec-216">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="eecec-217">过程逻辑</span><span class="sxs-lookup"><span data-stu-id="eecec-217">Procedural logic</span></span>

<span data-ttu-id="eecec-218">如果作为过程逻辑的一部分应用程序需要检查授权规则，请使用 `Task<AuthenticationState>` 类型的级联参数获取用户的 <xref:System.Security.Claims.ClaimsPrincipal>。</span><span class="sxs-lookup"><span data-stu-id="eecec-218">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="eecec-219">`Task<AuthenticationState>` 可以与其他服务（例如 `IAuthorizationService`）结合使用以评估策略。</span><span class="sxs-lookup"><span data-stu-id="eecec-219">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```razor
@using Microsoft.AspNetCore.Authorization
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
> <span data-ttu-id="eecec-220">在 Blazor WebAssembly 应用组件中，添加 `Microsoft.AspNetCore.Authorization` 和 `Microsoft.AspNetCore.Components.Authorization` 命名空间：</span><span class="sxs-lookup"><span data-stu-id="eecec-220">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="eecec-221">可以通过将这些命名空间添加到应用的 _Imports razor 文件来全局提供它们。 </span><span class="sxs-lookup"><span data-stu-id="eecec-221">These namespaces can be provided globally by adding them to the app's *_Imports.razor* file.</span></span>

## <a name="authorization-in-blazor-webassembly-apps"></a><span data-ttu-id="eecec-222">Blazor WebAssembly 应用中的授权</span><span class="sxs-lookup"><span data-stu-id="eecec-222">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="eecec-223">在 Blazor WebAssembly 应用中，可绕过授权检查，因为用户可修改所有客户端代码。</span><span class="sxs-lookup"><span data-stu-id="eecec-223">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="eecec-224">所有客户端应用程序技术都是如此，其中包括 JavaScript SPA 框架或任何操作系统的本机应用程序。</span><span class="sxs-lookup"><span data-stu-id="eecec-224">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="eecec-225">**始终对客户端应用程序访问的任何 API 终结点内的服务器执行授权检查。**</span><span class="sxs-lookup"><span data-stu-id="eecec-225">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

<span data-ttu-id="eecec-226">有关详细信息，请参阅 <xref:security/blazor/webassembly/index> 下的文章。</span><span class="sxs-lookup"><span data-stu-id="eecec-226">For more information, see the articles under <xref:security/blazor/webassembly/index>.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="eecec-227">排查错误</span><span class="sxs-lookup"><span data-stu-id="eecec-227">Troubleshoot errors</span></span>

<span data-ttu-id="eecec-228">常见错误：</span><span class="sxs-lookup"><span data-stu-id="eecec-228">Common errors:</span></span>

* <span data-ttu-id="eecec-229">**授权需要 Task\<AuthenticationState> 类型的级联参数。考虑使用 CascadingAuthenticationState 来提供此参数。**</span><span class="sxs-lookup"><span data-stu-id="eecec-229">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="eecec-230">**对于 `authenticationStateTask`，收到了 `null` 值**</span><span class="sxs-lookup"><span data-stu-id="eecec-230">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="eecec-231">项目可能不是使用启用了身份验证的 Blazor 服务器模板创建的。</span><span class="sxs-lookup"><span data-stu-id="eecec-231">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="eecec-232">使用 `<CascadingAuthenticationState>` 将 UI 树的某些部分括起来，例如下面 `App` 组件 (App.razor) 中所示  ：</span><span class="sxs-lookup"><span data-stu-id="eecec-232">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (*App.razor*) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="eecec-233">`CascadingAuthenticationState` 提供 `Task<AuthenticationState>` 级联参数，这是它从基础 `AuthenticationStateProvider` DI 服务那里接收的参数。</span><span class="sxs-lookup"><span data-stu-id="eecec-233">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eecec-234">其他资源</span><span class="sxs-lookup"><span data-stu-id="eecec-234">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="eecec-235">[令人惊叹的 Blazor：身份验证](https://github.com/AdrienTorris/awesome-blazor#authentication)社区示例链接</span><span class="sxs-lookup"><span data-stu-id="eecec-235">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
