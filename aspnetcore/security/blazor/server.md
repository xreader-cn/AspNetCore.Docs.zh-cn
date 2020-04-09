---
title: 安全ASP.NET核心Blazor服务器应用
author: guardrex
description: 了解如何缓解对服务器应用的Blazor安全威胁。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: bd03f811d0425fdfdb7bbbc24fea5481b49b8ed3
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80626022"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="cd87a-103">安全ASP.NET核心布拉佐服务器应用程序</span><span class="sxs-lookup"><span data-stu-id="cd87a-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="cd87a-104">哈维尔[·卡尔瓦罗·纳尔逊](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="cd87a-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="cd87a-105">Blazor Server 应用采用*有状态*的数据处理模型，其中服务器和客户端保持长期关系。</span><span class="sxs-lookup"><span data-stu-id="cd87a-105">Blazor Server apps adopt a *stateful* data processing model, where the server and client maintain a long-lived relationship.</span></span> <span data-ttu-id="cd87a-106">持久状态由电路保持，该[电路](xref:blazor/state-management)可以跨越可能寿命很长的连接。</span><span class="sxs-lookup"><span data-stu-id="cd87a-106">The persistent state is maintained by a [circuit](xref:blazor/state-management), which can span connections that are also potentially long-lived.</span></span>

<span data-ttu-id="cd87a-107">当用户访问 Blazor Server 站点时，服务器会在服务器的内存中创建一个电路。</span><span class="sxs-lookup"><span data-stu-id="cd87a-107">When a user visits a Blazor Server site, the server creates a circuit in the server's memory.</span></span> <span data-ttu-id="cd87a-108">该电路向浏览器指示要呈现哪些内容并响应事件，例如当用户在 UI 中选择按钮时。</span><span class="sxs-lookup"><span data-stu-id="cd87a-108">The circuit indicates to the browser what content to render and responds to events, such as when the user selects a button in the UI.</span></span> <span data-ttu-id="cd87a-109">要执行这些操作，电路在用户的浏览器和服务器上的 .NET 方法中调用 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="cd87a-109">To perform these actions, a circuit invokes JavaScript functions in the user's browser and .NET methods on the server.</span></span> <span data-ttu-id="cd87a-110">这种基于JavaScript的双向交互称为[JavaScript互操作（JS互操作）。](xref:blazor/call-javascript-from-dotnet)</span><span class="sxs-lookup"><span data-stu-id="cd87a-110">This two-way JavaScript-based interaction is referred to as [JavaScript interop (JS interop)](xref:blazor/call-javascript-from-dotnet).</span></span>

<span data-ttu-id="cd87a-111">由于 JS 互通通过 Internet 进行，并且客户端使用远程浏览器，因此 Blazor Server 应用共享大多数 Web 应用安全问题。</span><span class="sxs-lookup"><span data-stu-id="cd87a-111">Because JS interop occurs over the Internet and the client uses a remote browser, Blazor Server apps share most web app security concerns.</span></span> <span data-ttu-id="cd87a-112">本主题介绍对 Blazor Server 应用的常见威胁，并提供侧重于面向 Internet 的应用的威胁缓解指南。</span><span class="sxs-lookup"><span data-stu-id="cd87a-112">This topic describes common threats to Blazor Server apps and provides threat mitigation guidance focused on Internet-facing apps.</span></span>

<span data-ttu-id="cd87a-113">在受限环境中（如公司网络或 Intranet 内部）中，一些缓解指南要么：</span><span class="sxs-lookup"><span data-stu-id="cd87a-113">In constrained environments, such as inside corporate networks or intranets, some of the mitigation guidance either:</span></span>

* <span data-ttu-id="cd87a-114">不适用于受约束的环境。</span><span class="sxs-lookup"><span data-stu-id="cd87a-114">Doesn't apply in the constrained environment.</span></span>
* <span data-ttu-id="cd87a-115">不值得花费成本来实现，因为安全风险在受限的环境中很低。</span><span class="sxs-lookup"><span data-stu-id="cd87a-115">Isn't worth the cost to implement because the security risk is low in a constrained environment.</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="cd87a-116">布拉佐服务器项目模板</span><span class="sxs-lookup"><span data-stu-id="cd87a-116">Blazor Server project template</span></span>

<span data-ttu-id="cd87a-117">布拉佐服务器项目模板可以配置为在创建项目时进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="cd87a-117">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cd87a-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd87a-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cd87a-119">按照 <xref:blazor/get-started> 一文中的 Visual Studio 指南操作，创建具有身份验证机制的新 Blazor 服务器项目。</span><span class="sxs-lookup"><span data-stu-id="cd87a-119">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="cd87a-120">在“创建新的 ASP.NET Core Web 应用程序”\*\*\*\* 对话框中选择“Blazor 服务器应用”\*\*\*\* 模板后，在“身份验证”\*\*\*\* 下选择“更改”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="cd87a-120">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="cd87a-121">此时将打开一个对话框，为其他 ASP.NET Core 项目提供一组相同的身份验证机制：</span><span class="sxs-lookup"><span data-stu-id="cd87a-121">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="cd87a-122">**无身份验证**</span><span class="sxs-lookup"><span data-stu-id="cd87a-122">**No Authentication**</span></span>
* <span data-ttu-id="cd87a-123">**个人用户帐户** &ndash; 可以通过以下方式存储用户帐户：</span><span class="sxs-lookup"><span data-stu-id="cd87a-123">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="cd87a-124">使用 ASP.NET Core 的 [Identity](xref:security/authentication/identity) 系统在应用程序中存储。</span><span class="sxs-lookup"><span data-stu-id="cd87a-124">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="cd87a-125">使用[Azure AD B2C](xref:security/authentication/azure-ad-b2c)。</span><span class="sxs-lookup"><span data-stu-id="cd87a-125">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="cd87a-126">**工作或学校帐户**</span><span class="sxs-lookup"><span data-stu-id="cd87a-126">**Work or School Accounts**</span></span>
* <span data-ttu-id="cd87a-127">**Windows 身份验证**</span><span class="sxs-lookup"><span data-stu-id="cd87a-127">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cd87a-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cd87a-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cd87a-129">按照 <xref:blazor/get-started> 一文中的 Visual Studio Code 指南操作，创建具有身份验证机制的新 Blazor 服务器项目。</span><span class="sxs-lookup"><span data-stu-id="cd87a-129">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="cd87a-130">下表中显示了可能的身份验证值 (`{AUTHENTICATION}`)。</span><span class="sxs-lookup"><span data-stu-id="cd87a-130">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="cd87a-131">身份验证机制</span><span class="sxs-lookup"><span data-stu-id="cd87a-131">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="cd87a-132">`{AUTHENTICATION}` 值</span><span class="sxs-lookup"><span data-stu-id="cd87a-132">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="cd87a-133">无身份验证</span><span class="sxs-lookup"><span data-stu-id="cd87a-133">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="cd87a-134">个人</span><span class="sxs-lookup"><span data-stu-id="cd87a-134">Individual</span></span><br><span data-ttu-id="cd87a-135">使用 ASP.NET Core Identity 将用户存储在应用程序中。</span><span class="sxs-lookup"><span data-stu-id="cd87a-135">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="cd87a-136">个人</span><span class="sxs-lookup"><span data-stu-id="cd87a-136">Individual</span></span><br><span data-ttu-id="cd87a-137">用户存储在 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 中。</span><span class="sxs-lookup"><span data-stu-id="cd87a-137">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="cd87a-138">工作或学校帐户</span><span class="sxs-lookup"><span data-stu-id="cd87a-138">Work or School Accounts</span></span><br><span data-ttu-id="cd87a-139">对一个租户进行组织身份验证。</span><span class="sxs-lookup"><span data-stu-id="cd87a-139">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="cd87a-140">工作或学校帐户</span><span class="sxs-lookup"><span data-stu-id="cd87a-140">Work or School Accounts</span></span><br><span data-ttu-id="cd87a-141">对多个租户进行组织身份验证。</span><span class="sxs-lookup"><span data-stu-id="cd87a-141">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="cd87a-142">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="cd87a-142">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="cd87a-143">该命令创建一个文件夹，它将 `{APP NAME}` 占位符提供的值作为名称，并使用文件夹名称作为应用程序的名称。</span><span class="sxs-lookup"><span data-stu-id="cd87a-143">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="cd87a-144">有关详细信息，请参阅 .NET Core 指南中的 [dotnet new](/dotnet/core/tools/dotnet-new) 命令。</span><span class="sxs-lookup"><span data-stu-id="cd87a-144">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cd87a-145">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cd87a-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="cd87a-146">请按照"视觉工作室"了解本文中的<xref:blazor/get-started>Mac 指南。</span><span class="sxs-lookup"><span data-stu-id="cd87a-146">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="cd87a-147">在 **"配置新的 Blazor 服务器应用**"步骤中，从 **"身份验证**"下拉列表中选择 **"个人身份验证（应用内"）。**</span><span class="sxs-lookup"><span data-stu-id="cd87a-147">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="cd87a-148">该应用程序是为存储在应用中的具有ASP.NET核心标识的单个用户创建的。</span><span class="sxs-lookup"><span data-stu-id="cd87a-148">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cd87a-149">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cd87a-149">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="cd87a-150">按照<xref:blazor/get-started>本文中的 .NET Core CLI 指南创建具有身份验证机制的新 Blazor Server 项目：</span><span class="sxs-lookup"><span data-stu-id="cd87a-150">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="cd87a-151">下表中显示了可能的身份验证值 (`{AUTHENTICATION}`)。</span><span class="sxs-lookup"><span data-stu-id="cd87a-151">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="cd87a-152">身份验证机制</span><span class="sxs-lookup"><span data-stu-id="cd87a-152">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="cd87a-153">`{AUTHENTICATION}` 值</span><span class="sxs-lookup"><span data-stu-id="cd87a-153">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="cd87a-154">无身份验证</span><span class="sxs-lookup"><span data-stu-id="cd87a-154">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="cd87a-155">个人</span><span class="sxs-lookup"><span data-stu-id="cd87a-155">Individual</span></span><br><span data-ttu-id="cd87a-156">使用 ASP.NET Core Identity 将用户存储在应用程序中。</span><span class="sxs-lookup"><span data-stu-id="cd87a-156">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="cd87a-157">个人</span><span class="sxs-lookup"><span data-stu-id="cd87a-157">Individual</span></span><br><span data-ttu-id="cd87a-158">用户存储在 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 中。</span><span class="sxs-lookup"><span data-stu-id="cd87a-158">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="cd87a-159">工作或学校帐户</span><span class="sxs-lookup"><span data-stu-id="cd87a-159">Work or School Accounts</span></span><br><span data-ttu-id="cd87a-160">对一个租户进行组织身份验证。</span><span class="sxs-lookup"><span data-stu-id="cd87a-160">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="cd87a-161">工作或学校帐户</span><span class="sxs-lookup"><span data-stu-id="cd87a-161">Work or School Accounts</span></span><br><span data-ttu-id="cd87a-162">对多个租户进行组织身份验证。</span><span class="sxs-lookup"><span data-stu-id="cd87a-162">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="cd87a-163">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="cd87a-163">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="cd87a-164">该命令创建一个文件夹，它将 `{APP NAME}` 占位符提供的值作为名称，并使用文件夹名称作为应用程序的名称。</span><span class="sxs-lookup"><span data-stu-id="cd87a-164">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="cd87a-165">有关详细信息，请参阅 .NET Core 指南中的 [dotnet new](/dotnet/core/tools/dotnet-new) 命令。</span><span class="sxs-lookup"><span data-stu-id="cd87a-165">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="cd87a-166">将令牌传递到 Blazor 服务器应用</span><span class="sxs-lookup"><span data-stu-id="cd87a-166">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="cd87a-167">使用常规剃刀页面或 MVC 应用验证 Blazor Server 应用。</span><span class="sxs-lookup"><span data-stu-id="cd87a-167">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="cd87a-168">预配令牌并将其保存到身份验证 Cookie。</span><span class="sxs-lookup"><span data-stu-id="cd87a-168">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="cd87a-169">例如：</span><span class="sxs-lookup"><span data-stu-id="cd87a-169">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

<span data-ttu-id="cd87a-170">有关示例代码（包括完整`Startup.ConfigureServices`示例），请参阅[将令牌传递到服务器端 Blazor 应用程序](https://github.com/javiercn/blazor-server-aad-sample)。</span><span class="sxs-lookup"><span data-stu-id="cd87a-170">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="cd87a-171">定义要在初始应用状态中传递的类，并带有访问和刷新令牌：</span><span class="sxs-lookup"><span data-stu-id="cd87a-171">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="cd87a-172">定义可在 Blazor 应用中使用**的范围范围**令牌提供程序服务来解决 DI 中的令牌：</span><span class="sxs-lookup"><span data-stu-id="cd87a-172">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from DI:</span></span>

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;

public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="cd87a-173">在`Startup.ConfigureServices`中，添加服务：</span><span class="sxs-lookup"><span data-stu-id="cd87a-173">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="cd87a-174">在 *_Host.cshtml*文件中，创建 和`InitialApplicationState`实例并将其作为参数传递给应用：</span><span class="sxs-lookup"><span data-stu-id="cd87a-174">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

<span data-ttu-id="cd87a-175">在`App`组件 *（App.razor）* 中，解析服务，并使用参数中的数据初始化该服务：</span><span class="sxs-lookup"><span data-stu-id="cd87a-175">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokensProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokensProvider.AccessToken = InitialState.AccessToken;
        TokensProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="cd87a-176">在发出安全 API 请求的服务中，注入令牌提供程序并检索令牌以调用 API：</span><span class="sxs-lookup"><span data-stu-id="cd87a-176">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="resource-exhaustion"></a><span data-ttu-id="cd87a-177">资源耗尽</span><span class="sxs-lookup"><span data-stu-id="cd87a-177">Resource exhaustion</span></span>

<span data-ttu-id="cd87a-178">当客户端与服务器交互并导致服务器消耗过多资源时，可能会出现资源耗尽。</span><span class="sxs-lookup"><span data-stu-id="cd87a-178">Resource exhaustion can occur when a client interacts with the server and causes the server to consume excessive resources.</span></span> <span data-ttu-id="cd87a-179">过多的资源消耗主要影响：</span><span class="sxs-lookup"><span data-stu-id="cd87a-179">Excessive resource consumption primarily affects:</span></span>

* <span data-ttu-id="cd87a-180">CPU[](#cpu)</span><span class="sxs-lookup"><span data-stu-id="cd87a-180">[CPU](#cpu)</span></span>
* [<span data-ttu-id="cd87a-181">内存</span><span class="sxs-lookup"><span data-stu-id="cd87a-181">Memory</span></span>](#memory)
* [<span data-ttu-id="cd87a-182">客户端连接</span><span class="sxs-lookup"><span data-stu-id="cd87a-182">Client connections</span></span>](#client-connections)

<span data-ttu-id="cd87a-183">拒绝服务 （DoS） 攻击通常会消耗应用或服务器的资源。</span><span class="sxs-lookup"><span data-stu-id="cd87a-183">Denial of service (DoS) attacks usually seek to exhaust an app or server's resources.</span></span> <span data-ttu-id="cd87a-184">但是，资源耗尽不一定是系统攻击的结果。</span><span class="sxs-lookup"><span data-stu-id="cd87a-184">However, resource exhaustion isn't necessarily the result of an attack on the system.</span></span> <span data-ttu-id="cd87a-185">例如，由于用户需求高，有限资源可能会耗尽。</span><span class="sxs-lookup"><span data-stu-id="cd87a-185">For example, finite resources can be exhausted due to high user demand.</span></span> <span data-ttu-id="cd87a-186">DoS 在[拒绝服务 （DoS） 攻击](#denial-of-service-dos-attacks)部分中进一步介绍。</span><span class="sxs-lookup"><span data-stu-id="cd87a-186">DoS is covered further in the [Denial of service (DoS) attacks](#denial-of-service-dos-attacks) section.</span></span>

<span data-ttu-id="cd87a-187">Blazor 框架外部的资源（如数据库和文件句柄（用于读取和写入文件）也可能经历资源耗尽。</span><span class="sxs-lookup"><span data-stu-id="cd87a-187">Resources external to the Blazor framework, such as databases and file handles (used to read and write files), may also experience resource exhaustion.</span></span> <span data-ttu-id="cd87a-188">有关详细信息，请参阅 <xref:performance/performance-best-practices>。</span><span class="sxs-lookup"><span data-stu-id="cd87a-188">For more information, see <xref:performance/performance-best-practices>.</span></span>

### <a name="cpu"></a><span data-ttu-id="cd87a-189">CPU</span><span class="sxs-lookup"><span data-stu-id="cd87a-189">CPU</span></span>

<span data-ttu-id="cd87a-190">当一个或多个客户端强制服务器执行密集的 CPU 工作时，可能会出现 CPU 耗尽。</span><span class="sxs-lookup"><span data-stu-id="cd87a-190">CPU exhaustion can occur when one or more clients force the server to perform intensive CPU work.</span></span>

<span data-ttu-id="cd87a-191">例如，考虑一个布拉佐服务器应用程序，它计算*一个斐波纳奇数字*。</span><span class="sxs-lookup"><span data-stu-id="cd87a-191">For example, consider a Blazor Server app that calculates a *Fibonnacci number*.</span></span> <span data-ttu-id="cd87a-192">菲博纳奇数字由菲博纳奇序列生成，其中序列中的每个数字都是前两个数字的总和。</span><span class="sxs-lookup"><span data-stu-id="cd87a-192">A Fibonnacci number is produced from a Fibonnacci sequence, where each number in the sequence is the sum of the two preceding numbers.</span></span> <span data-ttu-id="cd87a-193">到达答案所需的工作量取决于序列的长度和初始值的大小。</span><span class="sxs-lookup"><span data-stu-id="cd87a-193">The amount of work required to reach the answer depends on the length of the sequence and the size of the initial value.</span></span> <span data-ttu-id="cd87a-194">如果应用未对客户端的请求进行限制，则 CPU 密集型计算可能会支配 CPU 的时间，并降低其他任务的性能。</span><span class="sxs-lookup"><span data-stu-id="cd87a-194">If the app doesn't place limits on a client's request, the CPU-intensive calculations may dominate the CPU's time and diminish the performance of other tasks.</span></span> <span data-ttu-id="cd87a-195">过多的资源消耗是影响可用性的安全问题。</span><span class="sxs-lookup"><span data-stu-id="cd87a-195">Excessive resource consumption is a security concern impacting availability.</span></span>

<span data-ttu-id="cd87a-196">CPU 耗尽是所有面向公众的应用的问题。</span><span class="sxs-lookup"><span data-stu-id="cd87a-196">CPU exhaustion is a concern for all public-facing apps.</span></span> <span data-ttu-id="cd87a-197">在常规 Web 应用中，请求和连接超时作为一种保护措施，但 Blazor Server 应用不提供相同的安全措施。</span><span class="sxs-lookup"><span data-stu-id="cd87a-197">In regular web apps, requests and connections time out as a safeguard, but Blazor Server apps don't provide the same safeguards.</span></span> <span data-ttu-id="cd87a-198">Blazor Server 应用在执行潜在的 CPU 密集型工作之前必须包括适当的检查和限制。</span><span class="sxs-lookup"><span data-stu-id="cd87a-198">Blazor Server apps must include appropriate checks and limits before performing potentially CPU-intensive work.</span></span>

### <a name="memory"></a><span data-ttu-id="cd87a-199">内存</span><span class="sxs-lookup"><span data-stu-id="cd87a-199">Memory</span></span>

<span data-ttu-id="cd87a-200">当一个或多个客户端强制服务器消耗大量内存时，可能会出现内存耗尽。</span><span class="sxs-lookup"><span data-stu-id="cd87a-200">Memory exhaustion can occur when one or more clients force the server to consume a large amount of memory.</span></span>

<span data-ttu-id="cd87a-201">例如，请考虑 Blazor 服务器端应用，该应用具有接受并显示项目列表的组件。</span><span class="sxs-lookup"><span data-stu-id="cd87a-201">For example, consider a Blazor-server side app with a component that accepts and displays a list of items.</span></span> <span data-ttu-id="cd87a-202">如果 Blazor 应用没有限制允许的项目数或呈现回客户端的项目数，则内存密集型处理和呈现可能会支配服务器的内存，使其达到服务器性能受损程度。</span><span class="sxs-lookup"><span data-stu-id="cd87a-202">If the Blazor app doesn't place limits on the number of items allowed or the number of items rendered back to the client, the memory-intensive processing and rendering may dominate the server's memory to the point where performance of the server suffers.</span></span> <span data-ttu-id="cd87a-203">服务器可能会崩溃或慢速到它似乎已崩溃的点。</span><span class="sxs-lookup"><span data-stu-id="cd87a-203">The server may crash or slow to the point that it appears to have crashed.</span></span>

<span data-ttu-id="cd87a-204">请考虑以下方案，用于维护和显示与服务器上的潜在内存耗尽方案相关的项目列表：</span><span class="sxs-lookup"><span data-stu-id="cd87a-204">Consider the following scenario for maintaining and displaying a list of items that pertain to a potential memory exhaustion scenario on the server:</span></span>

* <span data-ttu-id="cd87a-205">属性或字段中的项目`List<MyItem>`使用服务器的内存。</span><span class="sxs-lookup"><span data-stu-id="cd87a-205">The items in a `List<MyItem>` property or field use the server's memory.</span></span> <span data-ttu-id="cd87a-206">如果应用允许项目列表无限制增长，则存在服务器内存不足的风险。</span><span class="sxs-lookup"><span data-stu-id="cd87a-206">If the app allows the list of items to grow unbounded, there's a risk of the server running out of memory.</span></span> <span data-ttu-id="cd87a-207">内存不足会导致当前会话结束（崩溃），并且该服务器实例中的所有并发会话都会收到内存不足异常。</span><span class="sxs-lookup"><span data-stu-id="cd87a-207">Running out of memory causes the current session to end (crash) and all of the concurrent sessions in that server instance receive an out-of-memory exception.</span></span> <span data-ttu-id="cd87a-208">为了防止发生此情况，应用必须使用对并发用户施加项限制的数据结构。</span><span class="sxs-lookup"><span data-stu-id="cd87a-208">To prevent this scenario from occurring, the app must use a data structure that imposes an item limit on concurrent users.</span></span>
* <span data-ttu-id="cd87a-209">如果分页方案不用于呈现，则服务器对 UI 中不可见的对象使用其他内存。</span><span class="sxs-lookup"><span data-stu-id="cd87a-209">If a paging scheme isn't used for rendering, the server uses additional memory for objects that aren't visible in the UI.</span></span> <span data-ttu-id="cd87a-210">如果项目数量没有限制，内存需求可能会耗尽可用的服务器内存。</span><span class="sxs-lookup"><span data-stu-id="cd87a-210">Without a limit on the number of items, memory demands may exhaust the available server memory.</span></span> <span data-ttu-id="cd87a-211">要防止这种情况，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="cd87a-211">To prevent this scenario, use one of the following approaches:</span></span>
  * <span data-ttu-id="cd87a-212">渲染时使用分页列表。</span><span class="sxs-lookup"><span data-stu-id="cd87a-212">Use paginated lists when rendering.</span></span>
  * <span data-ttu-id="cd87a-213">仅显示前 100 到 1，000 个项目，并要求用户输入搜索条件以查找显示的项目以外的项目。</span><span class="sxs-lookup"><span data-stu-id="cd87a-213">Only display the first 100 to 1,000 items and require the user to enter search criteria to find items beyond the items displayed.</span></span>
  * <span data-ttu-id="cd87a-214">对于更高级的呈现方案，实现支持*虚拟化*的列表或网格。</span><span class="sxs-lookup"><span data-stu-id="cd87a-214">For a more advanced rendering scenario, implement lists or grids that support *virtualization*.</span></span> <span data-ttu-id="cd87a-215">使用虚拟化，列表仅呈现当前对用户可见的项子集。</span><span class="sxs-lookup"><span data-stu-id="cd87a-215">Using virtualization, lists only render a subset of items currently visible to the user.</span></span> <span data-ttu-id="cd87a-216">当用户与 UI 中的滚动条交互时，组件仅呈现显示所需的项。</span><span class="sxs-lookup"><span data-stu-id="cd87a-216">When the user interacts with the scrollbar in the UI, the component renders only those items required for display.</span></span> <span data-ttu-id="cd87a-217">显示当前不需要的项目可以保存在辅助存储中，这是理想的方法。</span><span class="sxs-lookup"><span data-stu-id="cd87a-217">The items that aren't currently required for display can be held in secondary storage, which is the ideal approach.</span></span> <span data-ttu-id="cd87a-218">未显示的项目也可以记在内存中，这不太理想。</span><span class="sxs-lookup"><span data-stu-id="cd87a-218">Undisplayed items can also be held in memory, which is less ideal.</span></span>

<span data-ttu-id="cd87a-219">Blazor Server 应用为有状态应用（如 WPF、Windows 窗体或 Blazor WebAssembly）提供与其他 UI 框架类似的编程模型。</span><span class="sxs-lookup"><span data-stu-id="cd87a-219">Blazor Server apps offer a similar programming model to other UI frameworks for stateful apps, such as WPF, Windows Forms, or Blazor WebAssembly.</span></span> <span data-ttu-id="cd87a-220">主要区别是，在几个 UI 框架中，应用使用的内存属于客户端，并且只影响该单个客户端。</span><span class="sxs-lookup"><span data-stu-id="cd87a-220">The main difference is that in several of the UI frameworks the memory consumed by the app belongs to the client and only affects that individual client.</span></span> <span data-ttu-id="cd87a-221">例如，Blazor WebAssembly 应用完全在客户端上运行，并且仅使用客户端内存资源。</span><span class="sxs-lookup"><span data-stu-id="cd87a-221">For example, a Blazor WebAssembly app runs entirely on the client and only uses client memory resources.</span></span> <span data-ttu-id="cd87a-222">在 Blazor Server 方案中，应用使用的内存属于服务器，并在服务器实例上的客户端之间共享。</span><span class="sxs-lookup"><span data-stu-id="cd87a-222">In the Blazor Server scenario, the memory consumed by the app belongs to the server and is shared among clients on the server instance.</span></span>

<span data-ttu-id="cd87a-223">服务器端内存需求是所有 Blazor Server 应用的考虑因素。</span><span class="sxs-lookup"><span data-stu-id="cd87a-223">Server-side memory demands are a consideration for all Blazor Server apps.</span></span> <span data-ttu-id="cd87a-224">但是，大多数 Web 应用都是无状态的，在返回响应时，在处理请求时使用的内存将被释放。</span><span class="sxs-lookup"><span data-stu-id="cd87a-224">However, most web apps are stateless, and the memory used while processing a request is released when the response is returned.</span></span> <span data-ttu-id="cd87a-225">作为一般建议，不允许客户端分配未绑定的内存量，就像保留客户端连接的任何其他服务器端应用一样。</span><span class="sxs-lookup"><span data-stu-id="cd87a-225">As a general recommendation, don't permit clients to allocate an unbound amount of memory as in any other server-side app that persists client connections.</span></span> <span data-ttu-id="cd87a-226">Blazor Server 应用使用的内存保留的时间比单个请求长。</span><span class="sxs-lookup"><span data-stu-id="cd87a-226">The memory consumed by a Blazor Server app persists for a longer time than a single request.</span></span>

> [!NOTE]
> <span data-ttu-id="cd87a-227">在开发过程中，可以使用探查器或捕获跟踪来评估客户端的内存需求。</span><span class="sxs-lookup"><span data-stu-id="cd87a-227">During development, a profiler can be used or a trace captured to assess memory demands of clients.</span></span> <span data-ttu-id="cd87a-228">探查器或跟踪不会捕获分配给特定客户端的内存。</span><span class="sxs-lookup"><span data-stu-id="cd87a-228">A profiler or trace won't capture the memory allocated to a specific client.</span></span> <span data-ttu-id="cd87a-229">要捕获开发过程中特定客户端的内存使用情况，捕获转储并检查根植于用户电路中的所有对象的内存需求。</span><span class="sxs-lookup"><span data-stu-id="cd87a-229">To capture the memory use of a specific client during development, capture a dump and examine the memory demand of all the objects rooted at a user's circuit.</span></span>

### <a name="client-connections"></a><span data-ttu-id="cd87a-230">客户端连接</span><span class="sxs-lookup"><span data-stu-id="cd87a-230">Client connections</span></span>

<span data-ttu-id="cd87a-231">当一个或多个客户端打开与服务器的并发连接过多，从而阻止其他客户端建立新连接时，可能会导致连接耗尽。</span><span class="sxs-lookup"><span data-stu-id="cd87a-231">Connection exhaustion can occur when one or more clients open too many concurrent connections to the server, preventing other clients from establishing new connections.</span></span>

<span data-ttu-id="cd87a-232">Blazor 客户端为每个会话建立单个连接，并在浏览器窗口打开时保持连接打开状态。</span><span class="sxs-lookup"><span data-stu-id="cd87a-232">Blazor clients establish a single connection per session and keep the connection open for as long as the browser window is open.</span></span> <span data-ttu-id="cd87a-233">对服务器维护所有连接的要求并不特定于 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="cd87a-233">The demands on the server of maintaining all of the connections isn't specific to Blazor apps.</span></span> <span data-ttu-id="cd87a-234">鉴于连接的持久性和 Blazor Server 应用的状态性，连接耗尽对应用的可用性风险更大。</span><span class="sxs-lookup"><span data-stu-id="cd87a-234">Given the persistent nature of the connections and the stateful nature of Blazor Server apps, connection exhaustion is a greater risk to availability of the app.</span></span>

<span data-ttu-id="cd87a-235">默认情况下，Blazor Server 应用的每位用户的连接数没有限制。</span><span class="sxs-lookup"><span data-stu-id="cd87a-235">By default, there's no limit on the number of connections per user for a Blazor Server app.</span></span> <span data-ttu-id="cd87a-236">如果应用需要连接限制，请采用以下一种或多种方法：</span><span class="sxs-lookup"><span data-stu-id="cd87a-236">If the app requires a connection limit, take one or more of the following approaches:</span></span>

* <span data-ttu-id="cd87a-237">需要身份验证，这自然会限制未经授权的用户连接到应用的能力。</span><span class="sxs-lookup"><span data-stu-id="cd87a-237">Require authentication, which naturally limits the ability of unauthorized users to connect to the app.</span></span> <span data-ttu-id="cd87a-238">要使此方案有效，必须防止用户以任何意愿预配新用户。</span><span class="sxs-lookup"><span data-stu-id="cd87a-238">For this scenario to be effective, users must be prevented from provisioning new users at will.</span></span>
* <span data-ttu-id="cd87a-239">限制每个用户的连接数。</span><span class="sxs-lookup"><span data-stu-id="cd87a-239">Limit the number of connections per user.</span></span> <span data-ttu-id="cd87a-240">限制连接可以通过以下方法完成。</span><span class="sxs-lookup"><span data-stu-id="cd87a-240">Limiting connections can be accomplished via the following approaches.</span></span> <span data-ttu-id="cd87a-241">练习谨慎以允许合法用户访问应用（例如，当基于客户端的 IP 地址建立连接限制时）。</span><span class="sxs-lookup"><span data-stu-id="cd87a-241">Exercise care to allow legitimate users to access the app (for example, when a connection limit is established based on the client's IP address).</span></span>
  * <span data-ttu-id="cd87a-242">在应用级别：</span><span class="sxs-lookup"><span data-stu-id="cd87a-242">At the app level:</span></span>
    * <span data-ttu-id="cd87a-243">端点路由可扩展性。</span><span class="sxs-lookup"><span data-stu-id="cd87a-243">Endpoint routing extensibility.</span></span>
    * <span data-ttu-id="cd87a-244">需要身份验证才能连接到应用并跟踪每个用户的活动会话。</span><span class="sxs-lookup"><span data-stu-id="cd87a-244">Require authentication to connect to the app and keep track of the active sessions per user.</span></span>
    * <span data-ttu-id="cd87a-245">达到限制时拒绝新会话。</span><span class="sxs-lookup"><span data-stu-id="cd87a-245">Reject new sessions upon reaching a limit.</span></span>
    * <span data-ttu-id="cd87a-246">使用代理（如[Azure SignalR 服务](/azure/azure-signalr/signalr-overview)）与应用的连接，该服务将连接从客户端到应用进行多路复用。</span><span class="sxs-lookup"><span data-stu-id="cd87a-246">Proxy WebSocket connections to an app through the use of a proxy, such as the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) that multiplexes connections from clients to an app.</span></span> <span data-ttu-id="cd87a-247">这为具有比单个客户端可以建立的连接容量更大的应用提供了，从而防止客户端耗尽与服务器的连接。</span><span class="sxs-lookup"><span data-stu-id="cd87a-247">This provides an app with greater connection capacity than a single client can establish, preventing a client from exhausting the connections to the server.</span></span>
  * <span data-ttu-id="cd87a-248">在服务器级别：在应用前面使用代理/网关。</span><span class="sxs-lookup"><span data-stu-id="cd87a-248">At the server level: Use a proxy/gateway in front of the app.</span></span> <span data-ttu-id="cd87a-249">例如[，Azure 前门](/azure/frontdoor/front-door-overview)使您能够定义、管理和监视 Web 流量到应用的全局路由。</span><span class="sxs-lookup"><span data-stu-id="cd87a-249">For example, [Azure Front Door](/azure/frontdoor/front-door-overview) enables you to define, manage, and monitor the global routing of web traffic to an app.</span></span>

## <a name="denial-of-service-dos-attacks"></a><span data-ttu-id="cd87a-250">拒绝服务 （DoS） 攻击</span><span class="sxs-lookup"><span data-stu-id="cd87a-250">Denial of service (DoS) attacks</span></span>

<span data-ttu-id="cd87a-251">拒绝服务 （DoS） 攻击涉及客户端，导致服务器耗尽其一个或多个资源，使应用不可用。</span><span class="sxs-lookup"><span data-stu-id="cd87a-251">Denial of service (DoS) attacks involve a client causing the server to exhaust one or more of its resources making the app unavailable.</span></span> <span data-ttu-id="cd87a-252">Blazor Server 应用包含一些默认限制，并依赖于其他ASP.NET核心和信号R限制来抵御 DoS 攻击：</span><span class="sxs-lookup"><span data-stu-id="cd87a-252">Blazor Server apps include some default limits and rely on other ASP.NET Core and SignalR limits to protect against DoS attacks:</span></span>

| <span data-ttu-id="cd87a-253">布拉佐服务器应用程序限制</span><span class="sxs-lookup"><span data-stu-id="cd87a-253">Blazor Server app limit</span></span>                            | <span data-ttu-id="cd87a-254">说明</span><span class="sxs-lookup"><span data-stu-id="cd87a-254">Description</span></span> | <span data-ttu-id="cd87a-255">默认</span><span class="sxs-lookup"><span data-stu-id="cd87a-255">Default</span></span> |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | <span data-ttu-id="cd87a-256">给定服务器一次在内存中保存的最大断开连接电路数。</span><span class="sxs-lookup"><span data-stu-id="cd87a-256">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> | <span data-ttu-id="cd87a-257">100</span><span class="sxs-lookup"><span data-stu-id="cd87a-257">100</span></span> |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | <span data-ttu-id="cd87a-258">断开电路在断开之前在内存中保持最大时间。</span><span class="sxs-lookup"><span data-stu-id="cd87a-258">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> | <span data-ttu-id="cd87a-259">3 分钟</span><span class="sxs-lookup"><span data-stu-id="cd87a-259">3 minutes</span></span> |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | <span data-ttu-id="cd87a-260">服务器在超时异步 JavaScript 函数调用之前等待的时间最长。</span><span class="sxs-lookup"><span data-stu-id="cd87a-260">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> | <span data-ttu-id="cd87a-261">1 分钟</span><span class="sxs-lookup"><span data-stu-id="cd87a-261">1 minute</span></span> |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | <span data-ttu-id="cd87a-262">服务器在给定时间每个电路的内存中保留的最大未确认呈现批处理数，以支持可靠的重新连接。</span><span class="sxs-lookup"><span data-stu-id="cd87a-262">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="cd87a-263">达到限制后，服务器将停止生成新的呈现批处理，直到客户端确认一个或多个批处理。</span><span class="sxs-lookup"><span data-stu-id="cd87a-263">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> | <span data-ttu-id="cd87a-264">10</span><span class="sxs-lookup"><span data-stu-id="cd87a-264">10</span></span> |


| <span data-ttu-id="cd87a-265">信号R 和 ASP.NET核心限制</span><span class="sxs-lookup"><span data-stu-id="cd87a-265">SignalR and ASP.NET Core limit</span></span>             | <span data-ttu-id="cd87a-266">说明</span><span class="sxs-lookup"><span data-stu-id="cd87a-266">Description</span></span> | <span data-ttu-id="cd87a-267">默认</span><span class="sxs-lookup"><span data-stu-id="cd87a-267">Default</span></span> |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | <span data-ttu-id="cd87a-268">单个邮件的消息大小。</span><span class="sxs-lookup"><span data-stu-id="cd87a-268">Message size for an individual message.</span></span> | <span data-ttu-id="cd87a-269">32 KB</span><span class="sxs-lookup"><span data-stu-id="cd87a-269">32 KB</span></span> |

## <a name="interactions-with-the-browser-client"></a><span data-ttu-id="cd87a-270">与浏览器（客户端）的交互</span><span class="sxs-lookup"><span data-stu-id="cd87a-270">Interactions with the browser (client)</span></span>

<span data-ttu-id="cd87a-271">客户端通过 JS 互通事件调度和呈现完成与服务器交互。</span><span class="sxs-lookup"><span data-stu-id="cd87a-271">A client interacts with the server through JS interop event dispatching and render completion.</span></span> <span data-ttu-id="cd87a-272">JS 互通通信在 JavaScript 和 .NET 之间双向进行：</span><span class="sxs-lookup"><span data-stu-id="cd87a-272">JS interop communication goes both ways between JavaScript and .NET:</span></span>

* <span data-ttu-id="cd87a-273">浏览器事件以异步方式从客户端发送到服务器。</span><span class="sxs-lookup"><span data-stu-id="cd87a-273">Browser events are dispatched from the client to the server in an asynchronous fashion.</span></span>
* <span data-ttu-id="cd87a-274">服务器根据需要异步重新呈现 UI。</span><span class="sxs-lookup"><span data-stu-id="cd87a-274">The server responds asynchronously rerendering the UI as necessary.</span></span>

### <a name="javascript-functions-invoked-from-net"></a><span data-ttu-id="cd87a-275">从 .NET 调用的 JavaScript 函数</span><span class="sxs-lookup"><span data-stu-id="cd87a-275">JavaScript functions invoked from .NET</span></span>

<span data-ttu-id="cd87a-276">对于从 .NET 方法到 JavaScript 的调用：</span><span class="sxs-lookup"><span data-stu-id="cd87a-276">For calls from .NET methods to JavaScript:</span></span>

* <span data-ttu-id="cd87a-277">所有调用都有可配置的超时，之后它们将返回<xref:System.OperationCanceledException>给调用方。</span><span class="sxs-lookup"><span data-stu-id="cd87a-277">All invocations have a configurable timeout after which they fail, returning a <xref:System.OperationCanceledException> to the caller.</span></span>
  * <span data-ttu-id="cd87a-278">呼叫的默认超时 （`CircuitOptions.JSInteropDefaultCallTimeout`） 为一分钟。</span><span class="sxs-lookup"><span data-stu-id="cd87a-278">There's a default timeout for the calls (`CircuitOptions.JSInteropDefaultCallTimeout`) of one minute.</span></span> <span data-ttu-id="cd87a-279">要配置此限制，请参阅<xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>。</span><span class="sxs-lookup"><span data-stu-id="cd87a-279">To configure this limit, see <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>.</span></span>
  * <span data-ttu-id="cd87a-280">可以提供取消令牌以按每次呼叫控制取消。</span><span class="sxs-lookup"><span data-stu-id="cd87a-280">A cancellation token can be provided to control the cancellation on a per-call basis.</span></span> <span data-ttu-id="cd87a-281">如果提供了取消令牌，则尽可能依赖默认调用超时，并规定对客户端的任何调用。</span><span class="sxs-lookup"><span data-stu-id="cd87a-281">Rely on the default call timeout where possible and time-bound any call to the client if a cancellation token is provided.</span></span>
* <span data-ttu-id="cd87a-282">不能信任 JavaScript 调用的结果。</span><span class="sxs-lookup"><span data-stu-id="cd87a-282">The result of a JavaScript call can't be trusted.</span></span> <span data-ttu-id="cd87a-283">在Blazor浏览器中运行的应用客户端搜索要调用的 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="cd87a-283">The Blazor app client running in the browser searches for the JavaScript function to invoke.</span></span> <span data-ttu-id="cd87a-284">调用该函数，并生成结果或错误。</span><span class="sxs-lookup"><span data-stu-id="cd87a-284">The function is invoked, and either the result or an error is produced.</span></span> <span data-ttu-id="cd87a-285">恶意客户端可以尝试：</span><span class="sxs-lookup"><span data-stu-id="cd87a-285">A malicious client can attempt to:</span></span>
  * <span data-ttu-id="cd87a-286">通过从 JavaScript 函数返回错误，在应用中导致问题。</span><span class="sxs-lookup"><span data-stu-id="cd87a-286">Cause an issue in the app by returning an error from the JavaScript function.</span></span>
  * <span data-ttu-id="cd87a-287">通过从 JavaScript 函数返回意外结果，在服务器上引发意外行为。</span><span class="sxs-lookup"><span data-stu-id="cd87a-287">Induce an unintended behavior on the server by returning an unexpected result from the JavaScript function.</span></span>

<span data-ttu-id="cd87a-288">采取以下预防措施，防止上述情况：</span><span class="sxs-lookup"><span data-stu-id="cd87a-288">Take the following precautions to guard against the preceding scenarios:</span></span>

* <span data-ttu-id="cd87a-289">在[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)语句中包装 JS 互操作调用，以考虑调用期间可能发生的错误。</span><span class="sxs-lookup"><span data-stu-id="cd87a-289">Wrap JS interop calls within [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements to account for errors that might occur during the invocations.</span></span> <span data-ttu-id="cd87a-290">有关详细信息，请参阅 <xref:blazor/handle-errors#javascript-interop>。</span><span class="sxs-lookup"><span data-stu-id="cd87a-290">For more information, see <xref:blazor/handle-errors#javascript-interop>.</span></span>
* <span data-ttu-id="cd87a-291">在采取任何操作之前，验证从 JS 互操作调用返回的数据，包括错误消息。</span><span class="sxs-lookup"><span data-stu-id="cd87a-291">Validate data returned from JS interop invocations, including error messages, before taking any action.</span></span>

### <a name="net-methods-invoked-from-the-browser"></a><span data-ttu-id="cd87a-292">从浏览器调用 .NET 方法</span><span class="sxs-lookup"><span data-stu-id="cd87a-292">.NET methods invoked from the browser</span></span>

<span data-ttu-id="cd87a-293">不要信任从 JavaScript 到 .NET 方法的调用。</span><span class="sxs-lookup"><span data-stu-id="cd87a-293">Don't trust calls from JavaScript to .NET methods.</span></span> <span data-ttu-id="cd87a-294">当 .NET 方法向 JavaScript 公开时，请考虑如何调用 .NET 方法：</span><span class="sxs-lookup"><span data-stu-id="cd87a-294">When a .NET method is exposed to JavaScript, consider how the .NET method is invoked:</span></span>

* <span data-ttu-id="cd87a-295">对待任何向 JavaScript 公开的任何 .NET 方法，就像将应用的公共终结点一样。</span><span class="sxs-lookup"><span data-stu-id="cd87a-295">Treat any .NET method exposed to JavaScript as you would a public endpoint to the app.</span></span>
  * <span data-ttu-id="cd87a-296">验证输入。</span><span class="sxs-lookup"><span data-stu-id="cd87a-296">Validate input.</span></span>
    * <span data-ttu-id="cd87a-297">确保值在预期范围内。</span><span class="sxs-lookup"><span data-stu-id="cd87a-297">Ensure that values are within expected ranges.</span></span>
    * <span data-ttu-id="cd87a-298">确保用户具有执行请求的操作的权限。</span><span class="sxs-lookup"><span data-stu-id="cd87a-298">Ensure that the user has permission to perform the action requested.</span></span>
  * <span data-ttu-id="cd87a-299">不要将过多的资源分配为 .NET 方法调用的一部分。</span><span class="sxs-lookup"><span data-stu-id="cd87a-299">Don't allocate an excessive quantity of resources as part of the .NET method invocation.</span></span> <span data-ttu-id="cd87a-300">例如，执行检查并限制 CPU 和内存使用。</span><span class="sxs-lookup"><span data-stu-id="cd87a-300">For example, perform checks and place limits on CPU and memory use.</span></span>
  * <span data-ttu-id="cd87a-301">考虑到静态和实例方法可以公开给 JavaScript 客户端。</span><span class="sxs-lookup"><span data-stu-id="cd87a-301">Take into account that static and instance methods can be exposed to JavaScript clients.</span></span> <span data-ttu-id="cd87a-302">除非设计要求共享具有适当约束的状态，否则应避免跨会话共享状态。</span><span class="sxs-lookup"><span data-stu-id="cd87a-302">Avoid sharing state across sessions unless the design calls for sharing state with appropriate constraints.</span></span>
    * <span data-ttu-id="cd87a-303">对于最初通过`DotNetReference`依赖项注入 （DI） 创建的对象公开的方法，这些对象应注册为作用域对象。</span><span class="sxs-lookup"><span data-stu-id="cd87a-303">For instance methods exposed through `DotNetReference` objects that are originally created through dependency injection (DI), the objects should be registered as scoped objects.</span></span> <span data-ttu-id="cd87a-304">这适用于Blazor服务器应用使用的任何 DI 服务。</span><span class="sxs-lookup"><span data-stu-id="cd87a-304">This applies to any DI service that the Blazor Server app uses.</span></span>
    * <span data-ttu-id="cd87a-305">对于静态方法，避免建立无法限定到客户端的状态，除非应用在服务器实例上的所有用户之间显式共享状态。</span><span class="sxs-lookup"><span data-stu-id="cd87a-305">For static methods, avoid establishing state that can't be scoped to the client unless the app is explicitly sharing state by-design across all users on a server instance.</span></span>
  * <span data-ttu-id="cd87a-306">避免将参数中用户提供的数据传递给 JavaScript 调用。</span><span class="sxs-lookup"><span data-stu-id="cd87a-306">Avoid passing user-supplied data in parameters to JavaScript calls.</span></span> <span data-ttu-id="cd87a-307">如果绝对需要传入参数中的数据，请确保 JavaScript 代码处理传递数据，而不会引入[跨站点脚本 （XSS）](#cross-site-scripting-xss)漏洞。</span><span class="sxs-lookup"><span data-stu-id="cd87a-307">If passing data in parameters is absolutely required, ensure that the JavaScript code handles passing the data without introducing [Cross-site scripting (XSS)](#cross-site-scripting-xss) vulnerabilities.</span></span> <span data-ttu-id="cd87a-308">例如，不要通过设置元素`innerHTML`的属性将用户提供的数据写入文档对象模型 （DOM）。</span><span class="sxs-lookup"><span data-stu-id="cd87a-308">For example, don't write user-supplied data to the Document Object Model (DOM) by setting the `innerHTML` property of an element.</span></span> <span data-ttu-id="cd87a-309">请考虑使用[内容安全策略 （CSP）](https://developer.mozilla.org/docs/Web/HTTP/CSP) `eval`来禁用和其他不安全的 JavaScript 基元。</span><span class="sxs-lookup"><span data-stu-id="cd87a-309">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to disable `eval` and other unsafe JavaScript primitives.</span></span>
* <span data-ttu-id="cd87a-310">避免在框架的调度实现之上实现自定义的 .NET 调用。</span><span class="sxs-lookup"><span data-stu-id="cd87a-310">Avoid implementing custom dispatching of .NET invocations on top of the framework's dispatching implementation.</span></span> <span data-ttu-id="cd87a-311">向浏览器公开 .NET 方法是一种高级方案，不建议用于Blazor一般开发。</span><span class="sxs-lookup"><span data-stu-id="cd87a-311">Exposing .NET methods to the browser is an advanced scenario, not recommended for general Blazor development.</span></span>

### <a name="events"></a><span data-ttu-id="cd87a-312">事件</span><span class="sxs-lookup"><span data-stu-id="cd87a-312">Events</span></span>

<span data-ttu-id="cd87a-313">事件为Blazor服务器应用提供入口点。</span><span class="sxs-lookup"><span data-stu-id="cd87a-313">Events provide an entry point to a Blazor Server app.</span></span> <span data-ttu-id="cd87a-314">保护 Web 应用中终结点的相同规则适用于服务器应用中Blazor的事件处理。</span><span class="sxs-lookup"><span data-stu-id="cd87a-314">The same rules for safeguarding endpoints in web apps apply to event handling in Blazor Server apps.</span></span> <span data-ttu-id="cd87a-315">恶意客户端可以发送它希望作为事件的有效负载发送的任何数据。</span><span class="sxs-lookup"><span data-stu-id="cd87a-315">A malicious client can send any data it wishes to send as the payload for an event.</span></span>

<span data-ttu-id="cd87a-316">例如：</span><span class="sxs-lookup"><span data-stu-id="cd87a-316">For example:</span></span>

* <span data-ttu-id="cd87a-317">的更改事件`<select>`可以发送不在应用向客户端显示的选项中的值。</span><span class="sxs-lookup"><span data-stu-id="cd87a-317">A change event for a `<select>` could send a value that isn't within the options that the app presented to the client.</span></span>
* <span data-ttu-id="cd87a-318">可以`<input>`绕过客户端验证向服务器发送任何文本数据。</span><span class="sxs-lookup"><span data-stu-id="cd87a-318">An `<input>` could send any text data to the server, bypassing client-side validation.</span></span>

<span data-ttu-id="cd87a-319">应用必须验证应用处理的任何事件的数据。</span><span class="sxs-lookup"><span data-stu-id="cd87a-319">The app must validate the data for any event that the app handles.</span></span> <span data-ttu-id="cd87a-320">框架Blazor[窗体组件](xref:blazor/forms-validation)执行基本验证。</span><span class="sxs-lookup"><span data-stu-id="cd87a-320">The Blazor framework [forms components](xref:blazor/forms-validation) perform basic validations.</span></span> <span data-ttu-id="cd87a-321">如果应用使用自定义窗体组件，则必须编写自定义代码以根据需要验证事件数据。</span><span class="sxs-lookup"><span data-stu-id="cd87a-321">If the app uses custom forms components, custom code must be written to validate event data as appropriate.</span></span>

Blazor<span data-ttu-id="cd87a-322">服务器事件是异步的，因此在应用有时间通过生成新的呈现来做出反应之前，可以将多个事件调度到服务器。</span><span class="sxs-lookup"><span data-stu-id="cd87a-322"> Server events are asynchronous, so multiple events can be dispatched to the server before the app has time to react by producing a new render.</span></span> <span data-ttu-id="cd87a-323">这需要考虑一些安全问题。</span><span class="sxs-lookup"><span data-stu-id="cd87a-323">This has some security implications to consider.</span></span> <span data-ttu-id="cd87a-324">必须在事件处理程序内执行限制应用中的客户端操作，而不是依赖于当前呈现的视图状态。</span><span class="sxs-lookup"><span data-stu-id="cd87a-324">Limiting client actions in the app must be performed inside event handlers and not depend on the current rendered view state.</span></span>

<span data-ttu-id="cd87a-325">考虑一个计数器组件，该组件应允许用户将计数器增量最多三次。</span><span class="sxs-lookup"><span data-stu-id="cd87a-325">Consider a counter component that should allow a user to increment a counter a maximum of three times.</span></span> <span data-ttu-id="cd87a-326">用于增加计数器的按钮基于`count`： 的值有条件地基于 ：</span><span class="sxs-lookup"><span data-stu-id="cd87a-326">The button to increment the counter is conditionally based on the value of `count`:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

<span data-ttu-id="cd87a-327">客户端可以在框架生成此组件的新呈现之前调度一个或多个增量事件。</span><span class="sxs-lookup"><span data-stu-id="cd87a-327">A client can dispatch one or more increment events before the framework produces a new render of this component.</span></span> <span data-ttu-id="cd87a-328">结果是，用户可以增量`count`*三次以上*，因为 UI 未足够快地删除该按钮。</span><span class="sxs-lookup"><span data-stu-id="cd87a-328">The result is that the `count` can be incremented *over three times* by the user because the button isn't removed by the UI quickly enough.</span></span> <span data-ttu-id="cd87a-329">实现三`count`个增量限制的正确方法如下示例所示：</span><span class="sxs-lookup"><span data-stu-id="cd87a-329">The correct way to achieve the limit of three `count` increments is shown in the following example:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

<span data-ttu-id="cd87a-330">通过在处理程序中`if (count < 3) { ... }`添加检查，增量`count`决定基于当前应用状态。</span><span class="sxs-lookup"><span data-stu-id="cd87a-330">By adding the `if (count < 3) { ... }` check inside the handler, the decision to increment `count` is based on the current app state.</span></span> <span data-ttu-id="cd87a-331">决策不像以前示例中那样基于 UI 的状态，后者可能暂时过时。</span><span class="sxs-lookup"><span data-stu-id="cd87a-331">The decision isn't based on the state of the UI as it was in the previous example, which might be temporarily stale.</span></span>

### <a name="guard-against-multiple-dispatches"></a><span data-ttu-id="cd87a-332">防止多个调度</span><span class="sxs-lookup"><span data-stu-id="cd87a-332">Guard against multiple dispatches</span></span>

<span data-ttu-id="cd87a-333">如果事件回调异步调用长时间运行的操作（例如从外部服务或数据库提取数据），请考虑使用保护。</span><span class="sxs-lookup"><span data-stu-id="cd87a-333">If an event callback invokes a long running operation asynchronously, such as fetching data from an external service or database, consider using a guard.</span></span> <span data-ttu-id="cd87a-334">在操作进行中，使用视觉反馈，保护可以防止用户排队执行多个操作。</span><span class="sxs-lookup"><span data-stu-id="cd87a-334">The guard can prevent the user from queueing up multiple operations while the operation is in progress with visual feedback.</span></span> <span data-ttu-id="cd87a-335">从服务器获取数据时`isLoading``GetForecastAsync`，`true`以下组件代码将集到。</span><span class="sxs-lookup"><span data-stu-id="cd87a-335">The following component code sets `isLoading` to `true` while `GetForecastAsync` obtains data from the server.</span></span> <span data-ttu-id="cd87a-336">当`isLoading``true`是 时，该按钮在 UI 中禁用：</span><span class="sxs-lookup"><span data-stu-id="cd87a-336">While `isLoading` is `true`, the button is disabled in the UI:</span></span>

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

<span data-ttu-id="cd87a-337">如果后台操作使用`async`-`await`模式异步执行，则上述示例中演示的防护模式有效。</span><span class="sxs-lookup"><span data-stu-id="cd87a-337">The guard pattern demonstrated in the preceding example works if the background operation is executed asynchronously with the `async`-`await` pattern.</span></span>

### <a name="cancel-early-and-avoid-use-after-dispose"></a><span data-ttu-id="cd87a-338">提前取消，避免处置后使用</span><span class="sxs-lookup"><span data-stu-id="cd87a-338">Cancel early and avoid use-after-dispose</span></span>

<span data-ttu-id="cd87a-339">除了使用["防护"中所述的防护装置（如针对多个调度部分](#guard-against-multiple-dispatches)）外，请考虑<xref:System.Threading.CancellationToken>在释放组件时使用 取消长时间运行的操作。</span><span class="sxs-lookup"><span data-stu-id="cd87a-339">In addition to using a guard as described in the [Guard against multiple dispatches](#guard-against-multiple-dispatches) section, consider using a <xref:System.Threading.CancellationToken> to cancel long-running operations when the component is disposed.</span></span> <span data-ttu-id="cd87a-340">此方法具有避免组件在*处置后使用*的额外好处：</span><span class="sxs-lookup"><span data-stu-id="cd87a-340">This approach has the added benefit of avoiding *use-after-dispose* in components:</span></span>

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a><span data-ttu-id="cd87a-341">避免生成大量数据的事件</span><span class="sxs-lookup"><span data-stu-id="cd87a-341">Avoid events that produce large amounts of data</span></span>

<span data-ttu-id="cd87a-342">某些 DOM 事件（`oninput`如`onscroll`或 ）可以生成大量数据。</span><span class="sxs-lookup"><span data-stu-id="cd87a-342">Some DOM events, such as `oninput` or `onscroll`, can produce a large amount of data.</span></span> <span data-ttu-id="cd87a-343">避免在Blazor服务器应用中使用这些事件。</span><span class="sxs-lookup"><span data-stu-id="cd87a-343">Avoid using these events in Blazor server apps.</span></span>

## <a name="additional-security-guidance"></a><span data-ttu-id="cd87a-344">其他安全指南</span><span class="sxs-lookup"><span data-stu-id="cd87a-344">Additional security guidance</span></span>

<span data-ttu-id="cd87a-345">保护ASP.NET核心应用的指南适用于Blazor服务器应用，并涵盖以下各节：</span><span class="sxs-lookup"><span data-stu-id="cd87a-345">The guidance for securing ASP.NET Core apps apply to Blazor Server apps and are covered in the following sections:</span></span>

* [<span data-ttu-id="cd87a-346">日志记录和敏感数据</span><span class="sxs-lookup"><span data-stu-id="cd87a-346">Logging and sensitive data</span></span>](#logging-and-sensitive-data)
* [<span data-ttu-id="cd87a-347">使用 HTTPS 保护传输中的信息</span><span class="sxs-lookup"><span data-stu-id="cd87a-347">Protect information in transit with HTTPS</span></span>](#protect-information-in-transit-with-https)
* <span data-ttu-id="cd87a-348">[跨站点脚本 （XSS）](#cross-site-scripting-xss)）</span><span class="sxs-lookup"><span data-stu-id="cd87a-348">[Cross-site scripting (XSS)](#cross-site-scripting-xss))</span></span>
* [<span data-ttu-id="cd87a-349">跨源保护</span><span class="sxs-lookup"><span data-stu-id="cd87a-349">Cross-origin protection</span></span>](#cross-origin-protection)
* [<span data-ttu-id="cd87a-350">单击劫持</span><span class="sxs-lookup"><span data-stu-id="cd87a-350">Click-jacking</span></span>](#click-jacking)
* [<span data-ttu-id="cd87a-351">打开重定向</span><span class="sxs-lookup"><span data-stu-id="cd87a-351">Open redirects</span></span>](#open-redirects)

### <a name="logging-and-sensitive-data"></a><span data-ttu-id="cd87a-352">日志记录和敏感数据</span><span class="sxs-lookup"><span data-stu-id="cd87a-352">Logging and sensitive data</span></span>

<span data-ttu-id="cd87a-353">客户端和服务器之间的 JS 交互记录在服务器的日志中，并与实例一起<xref:Microsoft.Extensions.Logging.ILogger>记录。</span><span class="sxs-lookup"><span data-stu-id="cd87a-353">JS interop interactions between the client and server are recorded in the server's logs with <xref:Microsoft.Extensions.Logging.ILogger> instances.</span></span> Blazor<span data-ttu-id="cd87a-354">避免记录敏感信息，如实际事件或 JS 互通输入和输出。</span><span class="sxs-lookup"><span data-stu-id="cd87a-354"> avoids logging sensitive information, such as actual events or JS interop inputs and outputs.</span></span>

<span data-ttu-id="cd87a-355">当服务器上发生错误时，框架会通知客户端并撕下会话。</span><span class="sxs-lookup"><span data-stu-id="cd87a-355">When an error occurs on the server, the framework notifies the client and tears down the session.</span></span> <span data-ttu-id="cd87a-356">默认情况下，客户端会收到一条通用错误消息，可在浏览器的开发人员工具中看到。</span><span class="sxs-lookup"><span data-stu-id="cd87a-356">By default, the client receives a generic error message that can be seen in the browser's developer tools.</span></span>

<span data-ttu-id="cd87a-357">客户端错误不包括调用堆栈，并且不提供有关错误原因的详细信息，但服务器日志确实包含此类信息。</span><span class="sxs-lookup"><span data-stu-id="cd87a-357">The client-side error doesn't include the callstack and doesn't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="cd87a-358">出于开发目的，可以通过启用详细的错误来向客户端提供敏感的错误信息。</span><span class="sxs-lookup"><span data-stu-id="cd87a-358">For development purposes, sensitive error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="cd87a-359">通过：</span><span class="sxs-lookup"><span data-stu-id="cd87a-359">Enable detailed errors with:</span></span>

* <span data-ttu-id="cd87a-360">`CircuitOptions.DetailedErrors`.</span><span class="sxs-lookup"><span data-stu-id="cd87a-360">`CircuitOptions.DetailedErrors`.</span></span>
* <span data-ttu-id="cd87a-361">`DetailedErrors`配置密钥。</span><span class="sxs-lookup"><span data-stu-id="cd87a-361">`DetailedErrors` configuration key.</span></span> <span data-ttu-id="cd87a-362">例如，将`ASPNETCORE_DETAILEDERRORS`环境变量设置为`true`的值。</span><span class="sxs-lookup"><span data-stu-id="cd87a-362">For example, set the `ASPNETCORE_DETAILEDERRORS` environment variable to a value of `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="cd87a-363">向 Internet 上的客户端公开错误信息是应始终避免的安全风险。</span><span class="sxs-lookup"><span data-stu-id="cd87a-363">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

### <a name="protect-information-in-transit-with-https"></a><span data-ttu-id="cd87a-364">使用 HTTPS 保护传输中的信息</span><span class="sxs-lookup"><span data-stu-id="cd87a-364">Protect information in transit with HTTPS</span></span>

Blazor<span data-ttu-id="cd87a-365">服务器用于SignalR客户端和服务器之间的通信。</span><span class="sxs-lookup"><span data-stu-id="cd87a-365"> Server uses SignalR for communication between the client and the server.</span></span> Blazor<span data-ttu-id="cd87a-366">服务器通常使用协商的SignalR传输，通常是 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="cd87a-366"> Server normally uses the transport that SignalR negotiates, which is typically WebSockets.</span></span>

Blazor<span data-ttu-id="cd87a-367">服务器不能确保服务器和客户端之间发送的数据的完整性和机密性。</span><span class="sxs-lookup"><span data-stu-id="cd87a-367"> Server doesn't ensure the integrity and confidentiality of the data sent between the server and the client.</span></span> <span data-ttu-id="cd87a-368">始终使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="cd87a-368">Always use HTTPS.</span></span>

### <a name="cross-site-scripting-xss"></a><span data-ttu-id="cd87a-369">跨站点脚本 （XSS）</span><span class="sxs-lookup"><span data-stu-id="cd87a-369">Cross-site scripting (XSS)</span></span>

<span data-ttu-id="cd87a-370">跨站点脚本 （XSS） 允许未经授权的一方在浏览器上下文中执行任意逻辑。</span><span class="sxs-lookup"><span data-stu-id="cd87a-370">Cross-site scripting (XSS) allows an unauthorized party to execute arbitrary logic in the context of the browser.</span></span> <span data-ttu-id="cd87a-371">受攻击的应用可能在客户端上运行任意代码。</span><span class="sxs-lookup"><span data-stu-id="cd87a-371">A compromised app could potentially run arbitrary code on the client.</span></span> <span data-ttu-id="cd87a-372">该漏洞可用于对服务器执行许多恶意操作：</span><span class="sxs-lookup"><span data-stu-id="cd87a-372">The vulnerability could be used to potentially perform a number of malicious actions against the server:</span></span>

* <span data-ttu-id="cd87a-373">向服务器发送虚假/无效事件。</span><span class="sxs-lookup"><span data-stu-id="cd87a-373">Dispatch fake/invalid events to the server.</span></span>
* <span data-ttu-id="cd87a-374">派单失败/无效呈现完成。</span><span class="sxs-lookup"><span data-stu-id="cd87a-374">Dispatch fail/invalid render completions.</span></span>
* <span data-ttu-id="cd87a-375">避免调度渲染完成。</span><span class="sxs-lookup"><span data-stu-id="cd87a-375">Avoid dispatching render completions.</span></span>
* <span data-ttu-id="cd87a-376">将跨攻呼叫从 JavaScript 调度到 .NET。</span><span class="sxs-lookup"><span data-stu-id="cd87a-376">Dispatch interop calls from JavaScript to .NET.</span></span>
* <span data-ttu-id="cd87a-377">修改从 .NET 到 JavaScript 的互操作调用的响应。</span><span class="sxs-lookup"><span data-stu-id="cd87a-377">Modify the response of interop calls from .NET to JavaScript.</span></span>
* <span data-ttu-id="cd87a-378">避免将 .NET 调度到 JS 互通结果。</span><span class="sxs-lookup"><span data-stu-id="cd87a-378">Avoid dispatching .NET to JS interop results.</span></span>

<span data-ttu-id="cd87a-379">Blazor服务器框架采取措施防止上述威胁：</span><span class="sxs-lookup"><span data-stu-id="cd87a-379">The Blazor Server framework takes steps to protect against some of the preceding threats:</span></span>

* <span data-ttu-id="cd87a-380">如果客户端未确认呈现批处理，则停止生成新的 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="cd87a-380">Stops producing new UI updates if the client isn't acknowledging render batches.</span></span> <span data-ttu-id="cd87a-381">配置了`CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`。</span><span class="sxs-lookup"><span data-stu-id="cd87a-381">Configured with `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`.</span></span>
* <span data-ttu-id="cd87a-382">在一分钟后超时任何 .NET 到 JavaScript 调用，而不会收到客户端的响应。</span><span class="sxs-lookup"><span data-stu-id="cd87a-382">Times out any .NET to JavaScript call after one minute without receiving a response from the client.</span></span> <span data-ttu-id="cd87a-383">配置了`CircuitOptions.JSInteropDefaultCallTimeout`。</span><span class="sxs-lookup"><span data-stu-id="cd87a-383">Configured with `CircuitOptions.JSInteropDefaultCallTimeout`.</span></span>
* <span data-ttu-id="cd87a-384">在 JS 互通期间对来自浏览器的所有输入执行基本验证：</span><span class="sxs-lookup"><span data-stu-id="cd87a-384">Performs basic validation on all input coming from the browser during JS interop:</span></span>
  * <span data-ttu-id="cd87a-385">.NET 引用有效，且为 .NET 方法预期的类型。</span><span class="sxs-lookup"><span data-stu-id="cd87a-385">.NET references are valid and of the type expected by the .NET method.</span></span>
  * <span data-ttu-id="cd87a-386">数据没有格式错误。</span><span class="sxs-lookup"><span data-stu-id="cd87a-386">The data isn't malformed.</span></span>
  * <span data-ttu-id="cd87a-387">有效负载中存在该方法的正确参数数。</span><span class="sxs-lookup"><span data-stu-id="cd87a-387">The correct number of arguments for the method are present in the payload.</span></span>
  * <span data-ttu-id="cd87a-388">在调用 方法之前，可以正确反序列化参数或结果。</span><span class="sxs-lookup"><span data-stu-id="cd87a-388">The arguments or result can be deserialized correctly before invoking the method.</span></span>
* <span data-ttu-id="cd87a-389">在来自来自来自调度事件的所有输入中执行基本验证：</span><span class="sxs-lookup"><span data-stu-id="cd87a-389">Performs basic validation in all input coming from the browser from dispatched events:</span></span>
  * <span data-ttu-id="cd87a-390">事件具有有效类型。</span><span class="sxs-lookup"><span data-stu-id="cd87a-390">The event has a valid type.</span></span>
  * <span data-ttu-id="cd87a-391">可以对事件的数据进行反序列化。</span><span class="sxs-lookup"><span data-stu-id="cd87a-391">The data for the event can be deserialized.</span></span>
  * <span data-ttu-id="cd87a-392">有一个事件处理程序与事件相关联。</span><span class="sxs-lookup"><span data-stu-id="cd87a-392">There's an event handler associated with the event.</span></span>

<span data-ttu-id="cd87a-393">除了框架实现的安全措施外，开发人员还必须对应用进行编码，以防止威胁并采取适当操作：</span><span class="sxs-lookup"><span data-stu-id="cd87a-393">In addition to the safeguards that the framework implements, the app must be coded by the developer to safeguard against threats and take appropriate actions:</span></span>

* <span data-ttu-id="cd87a-394">在处理事件时，始终验证数据。</span><span class="sxs-lookup"><span data-stu-id="cd87a-394">Always validate data when handling events.</span></span>
* <span data-ttu-id="cd87a-395">在接收无效数据时采取适当的操作：</span><span class="sxs-lookup"><span data-stu-id="cd87a-395">Take appropriate action upon receiving invalid data:</span></span>
  * <span data-ttu-id="cd87a-396">忽略数据并返回。</span><span class="sxs-lookup"><span data-stu-id="cd87a-396">Ignore the data and return.</span></span> <span data-ttu-id="cd87a-397">这允许应用继续处理请求。</span><span class="sxs-lookup"><span data-stu-id="cd87a-397">This allows the app to continue processing requests.</span></span>
  * <span data-ttu-id="cd87a-398">如果应用确定输入是非法的，并且无法由合法客户端生成，则引发异常。</span><span class="sxs-lookup"><span data-stu-id="cd87a-398">If the app determines that the input is illegitimate and couldn't be produced by legitimate client, throw an exception.</span></span> <span data-ttu-id="cd87a-399">引发异常会撕裂电路并结束会话。</span><span class="sxs-lookup"><span data-stu-id="cd87a-399">Throwing an exception tears down the circuit and ends the session.</span></span>
* <span data-ttu-id="cd87a-400">不要相信日志中包含的呈现批处理完成提供的错误消息。</span><span class="sxs-lookup"><span data-stu-id="cd87a-400">Don't trust the error message provided by render batch completions included in the logs.</span></span> <span data-ttu-id="cd87a-401">该错误由客户端提供，通常不能信任，因为客户端可能会受到威胁。</span><span class="sxs-lookup"><span data-stu-id="cd87a-401">The error is provided by the client and can't generally be trusted, as the client might be compromised.</span></span>
* <span data-ttu-id="cd87a-402">不要信任在 JavaScript 和 .NET 方法之间朝任一方向对 JS 互操作调用的输入。</span><span class="sxs-lookup"><span data-stu-id="cd87a-402">Don't trust the input on JS interop calls in either direction between JavaScript and .NET methods.</span></span>
* <span data-ttu-id="cd87a-403">应用负责验证参数和结果的内容是否有效，即使参数或结果已正确反序列化。</span><span class="sxs-lookup"><span data-stu-id="cd87a-403">The app is responsible for validating that the content of arguments and results are valid, even if the arguments or results are correctly deserialized.</span></span>

<span data-ttu-id="cd87a-404">要存在 XSS 漏洞，应用必须在呈现的页面中包含用户输入。</span><span class="sxs-lookup"><span data-stu-id="cd87a-404">For a XSS vulnerability to exist, the app must incorporate user input in the rendered page.</span></span> Blazor<span data-ttu-id="cd87a-405">服务器组件执行编译时间步骤，其中 *.razor*文件中的标记转换为过程 C# 逻辑。</span><span class="sxs-lookup"><span data-stu-id="cd87a-405"> Server components execute a compile-time step where the markup in a *.razor* file is transformed into procedural C# logic.</span></span> <span data-ttu-id="cd87a-406">在运行时，C# 逻辑生成描述元素、文本和子组件的*呈现树*。</span><span class="sxs-lookup"><span data-stu-id="cd87a-406">At runtime, the C# logic builds a *render tree* describing the elements, text, and child components.</span></span> <span data-ttu-id="cd87a-407">这通过一系列 JavaScript 指令应用于浏览器的 DOM（或在预渲染的情况下序列化为 HTML）：</span><span class="sxs-lookup"><span data-stu-id="cd87a-407">This is applied to the browser's DOM via a sequence of JavaScript instructions (or is serialized to HTML in the case of prerendering):</span></span>

* <span data-ttu-id="cd87a-408">通过普通 Razor 语法（例如），`@someStringValue`呈现的用户输入不会公开 XSS 漏洞，因为 Razor 语法通过只能写入文本的命令添加到 DOM。</span><span class="sxs-lookup"><span data-stu-id="cd87a-408">User input rendered via normal Razor syntax (for example, `@someStringValue`) doesn't expose a XSS vulnerability because the Razor syntax is added to the DOM via commands that can only write text.</span></span> <span data-ttu-id="cd87a-409">即使该值包含 HTML 标记，该值也会显示为静态文本。</span><span class="sxs-lookup"><span data-stu-id="cd87a-409">Even if the value includes HTML markup, the value is displayed as static text.</span></span> <span data-ttu-id="cd87a-410">预渲染时，输出由 HTML 编码，该输出还会将内容显示为静态文本。</span><span class="sxs-lookup"><span data-stu-id="cd87a-410">When prerendering, the output is HTML-encoded, which also displays the content as static text.</span></span>
* <span data-ttu-id="cd87a-411">不允许脚本标记，不应包含在应用的组件呈现树中。</span><span class="sxs-lookup"><span data-stu-id="cd87a-411">Script tags aren't allowed and shouldn't be included in the app's component render tree.</span></span> <span data-ttu-id="cd87a-412">如果组件的标记中包含脚本标记，则生成编译时间错误。</span><span class="sxs-lookup"><span data-stu-id="cd87a-412">If a script tag is included in a component's markup, a compile-time error is generated.</span></span>
* <span data-ttu-id="cd87a-413">组件作者无需使用 Razor 即可创作 C# 中的组件。</span><span class="sxs-lookup"><span data-stu-id="cd87a-413">Component authors can author components in C# without using Razor.</span></span> <span data-ttu-id="cd87a-414">组件作者负责在发出输出时使用正确的 API。</span><span class="sxs-lookup"><span data-stu-id="cd87a-414">The component author is responsible for using the correct APIs when emitting output.</span></span> <span data-ttu-id="cd87a-415">例如，使用`builder.AddContent(0, someUserSuppliedString)`*而不是*`builder.AddMarkupContent(0, someUserSuppliedString)`，因为后者可能会创建 XSS 漏洞。</span><span class="sxs-lookup"><span data-stu-id="cd87a-415">For example, use `builder.AddContent(0, someUserSuppliedString)` and *not* `builder.AddMarkupContent(0, someUserSuppliedString)`, as the latter could create a XSS vulnerability.</span></span>

<span data-ttu-id="cd87a-416">作为防范 XSS 攻击的一部分，请考虑实施 XSS 缓解措施，如[内容安全策略 （CSP）。](https://developer.mozilla.org/docs/Web/HTTP/CSP)</span><span class="sxs-lookup"><span data-stu-id="cd87a-416">As part of protecting against XSS attacks, consider implementing XSS mitigations, such as [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).</span></span>

<span data-ttu-id="cd87a-417">有关详细信息，请参阅 <xref:security/cross-site-scripting>。</span><span class="sxs-lookup"><span data-stu-id="cd87a-417">For more information, see <xref:security/cross-site-scripting>.</span></span>

### <a name="cross-origin-protection"></a><span data-ttu-id="cd87a-418">跨源保护</span><span class="sxs-lookup"><span data-stu-id="cd87a-418">Cross-origin protection</span></span>

<span data-ttu-id="cd87a-419">跨源攻击涉及来自不同来源的客户端对服务器执行操作。</span><span class="sxs-lookup"><span data-stu-id="cd87a-419">Cross-origin attacks involve a client from a different origin performing an action against the server.</span></span> <span data-ttu-id="cd87a-420">恶意操作通常是 GET 请求或表单 POST（跨站点请求伪造、CSRF），但也可以打开恶意 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="cd87a-420">The malicious action is typically a GET request or a form POST (Cross-Site Request Forgery, CSRF), but opening a malicious WebSocket is also possible.</span></span> Blazor<span data-ttu-id="cd87a-421">服务器应用提供[与使用中心协议的任何其他SignalR应用相同的保证：](xref:signalr/security)</span><span class="sxs-lookup"><span data-stu-id="cd87a-421"> Server apps offer [the same guarantees that any other SignalR app using the hub protocol offer](xref:signalr/security):</span></span>

* Blazor<span data-ttu-id="cd87a-422">除非采取其他措施防止服务器应用跨源访问，否则可以跨源访问服务器应用。</span><span class="sxs-lookup"><span data-stu-id="cd87a-422"> Server apps can be accessed cross-origin unless additional measures are taken to prevent it.</span></span> <span data-ttu-id="cd87a-423">要禁用跨源访问，请通过将 CORS 中间件添加到管道并将 添加到`DisableCorsAttribute`Blazor终结点元数据或通过[配置跨源资源共享SignalR来](xref:signalr/security#cross-origin-resource-sharing)限制允许源集，从而禁用终结点中的 CORS。</span><span class="sxs-lookup"><span data-stu-id="cd87a-423">To disable cross-origin access, either disable CORS in the endpoint by adding the CORS middleware to the pipeline and adding the `DisableCorsAttribute` to the Blazor endpoint metadata or limit the set of allowed origins by [configuring SignalR for cross-origin resource sharing](xref:signalr/security#cross-origin-resource-sharing).</span></span>
* <span data-ttu-id="cd87a-424">如果启用了 CORS，则可能需要执行额外的步骤来保护应用，具体取决于 CORS 配置。</span><span class="sxs-lookup"><span data-stu-id="cd87a-424">If CORS is enabled, extra steps might be required to protect the app depending on the CORS configuration.</span></span> <span data-ttu-id="cd87a-425">如果 CORS 已全局启用，则可以通过在调用Blazor`DisableCorsAttribute``hub.MapBlazorHub()`后将元数据添加到终结点元数据来禁用服务器中心。</span><span class="sxs-lookup"><span data-stu-id="cd87a-425">If CORS is globally enabled, CORS can be disabled for the Blazor Server hub by adding the `DisableCorsAttribute` metadata to the endpoint metadata after calling `hub.MapBlazorHub()`.</span></span>

<span data-ttu-id="cd87a-426">有关详细信息，请参阅 <xref:security/anti-request-forgery>。</span><span class="sxs-lookup"><span data-stu-id="cd87a-426">For more information, see <xref:security/anti-request-forgery>.</span></span>

### <a name="click-jacking"></a><span data-ttu-id="cd87a-427">单击劫持</span><span class="sxs-lookup"><span data-stu-id="cd87a-427">Click-jacking</span></span>

<span data-ttu-id="cd87a-428">单击劫持涉及将网站从不同来源呈现为`<iframe>`站点内部，以诱使用户在受到攻击的站点上执行操作。</span><span class="sxs-lookup"><span data-stu-id="cd87a-428">Click-jacking involves rendering a site as an `<iframe>` inside a site from a different origin in order to trick the user into performing actions on the site under attack.</span></span>

<span data-ttu-id="cd87a-429">要保护应用不在 中`<iframe>`呈现，请使用[内容安全策略 （CSP）](https://developer.mozilla.org/docs/Web/HTTP/CSP)和`X-Frame-Options`标头。</span><span class="sxs-lookup"><span data-stu-id="cd87a-429">To protect an app from rendering inside of an `<iframe>`, use [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) and the `X-Frame-Options` header.</span></span> <span data-ttu-id="cd87a-430">有关详细信息，请参阅[MDN Web 文档：X 帧选项](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)。</span><span class="sxs-lookup"><span data-stu-id="cd87a-430">For more information, see [MDN web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span></span>

### <a name="open-redirects"></a><span data-ttu-id="cd87a-431">打开重定向</span><span class="sxs-lookup"><span data-stu-id="cd87a-431">Open redirects</span></span>

<span data-ttu-id="cd87a-432">当Blazor服务器应用会话启动时，服务器将执行作为启动会话的一部分发送的 URL 的基本验证。</span><span class="sxs-lookup"><span data-stu-id="cd87a-432">When a Blazor Server app session starts, the server performs basic validation of the URLs sent as part of starting the session.</span></span> <span data-ttu-id="cd87a-433">在建立电路之前，框架会检查基本 URL 是否是当前 URL 的父 URL。</span><span class="sxs-lookup"><span data-stu-id="cd87a-433">The framework checks that the base URL is a parent of the current URL before establishing the circuit.</span></span> <span data-ttu-id="cd87a-434">框架不执行其他检查。</span><span class="sxs-lookup"><span data-stu-id="cd87a-434">No additional checks are performed by the framework.</span></span>

<span data-ttu-id="cd87a-435">当用户选择客户端上的链接时，链接的 URL 将发送到服务器，从而确定要执行的操作。</span><span class="sxs-lookup"><span data-stu-id="cd87a-435">When a user selects a link on the client, the URL for the link is sent to the server, which determines what action to take.</span></span> <span data-ttu-id="cd87a-436">例如，应用可以执行客户端导航或指示浏览器转到新位置。</span><span class="sxs-lookup"><span data-stu-id="cd87a-436">For example, the app may perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="cd87a-437">组件还可以使用 以编程方式触发导航请求`NavigationManager`。</span><span class="sxs-lookup"><span data-stu-id="cd87a-437">Components can also trigger navigation requests programatically through the use of `NavigationManager`.</span></span> <span data-ttu-id="cd87a-438">在这种情况下，应用可能会执行客户端导航或指示浏览器转到新位置。</span><span class="sxs-lookup"><span data-stu-id="cd87a-438">In such scenarios, the app might perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="cd87a-439">组件必须：</span><span class="sxs-lookup"><span data-stu-id="cd87a-439">Components must:</span></span>

* <span data-ttu-id="cd87a-440">避免将用户输入用作导航调用参数的一部分。</span><span class="sxs-lookup"><span data-stu-id="cd87a-440">Avoid using user input as part of the navigation call arguments.</span></span>
* <span data-ttu-id="cd87a-441">验证参数以确保应用允许目标。</span><span class="sxs-lookup"><span data-stu-id="cd87a-441">Validate arguments to ensure that the target is allowed by the app.</span></span>

<span data-ttu-id="cd87a-442">否则，恶意用户可以强制浏览器转到攻击者控制的站点。</span><span class="sxs-lookup"><span data-stu-id="cd87a-442">Otherwise, a malicious user can force the browser to go to an attacker-controlled site.</span></span> <span data-ttu-id="cd87a-443">在这种情况下，攻击者会诱使应用使用某些用户输入作为`NavigationManager.Navigate`方法调用的一部分。</span><span class="sxs-lookup"><span data-stu-id="cd87a-443">In this scenario, the attacker tricks the app into using some user input as part of the invocation of the `NavigationManager.Navigate` method.</span></span>

<span data-ttu-id="cd87a-444">在将链接作为应用的一部分呈现时，此建议也适用于：</span><span class="sxs-lookup"><span data-stu-id="cd87a-444">This advice also applies when rendering links as part of the app:</span></span>

* <span data-ttu-id="cd87a-445">如果可能，请使用相对链接。</span><span class="sxs-lookup"><span data-stu-id="cd87a-445">If possible, use relative links.</span></span>
* <span data-ttu-id="cd87a-446">在将绝对链接目标包括在页面中之前，验证其绝对链接目标是否有效。</span><span class="sxs-lookup"><span data-stu-id="cd87a-446">Validate that absolute link destinations are valid before including them in a page.</span></span>

<span data-ttu-id="cd87a-447">有关详细信息，请参阅 <xref:security/preventing-open-redirects>。</span><span class="sxs-lookup"><span data-stu-id="cd87a-447">For more information, see <xref:security/preventing-open-redirects>.</span></span>

## <a name="authentication-and-authorization"></a><span data-ttu-id="cd87a-448">身份验证和授权</span><span class="sxs-lookup"><span data-stu-id="cd87a-448">Authentication and authorization</span></span>

<span data-ttu-id="cd87a-449">有关身份验证和授权的指南，请参阅<xref:security/blazor/index>。</span><span class="sxs-lookup"><span data-stu-id="cd87a-449">For guidance on authentication and authorization, see <xref:security/blazor/index>.</span></span>

## <a name="security-checklist"></a><span data-ttu-id="cd87a-450">安全清单</span><span class="sxs-lookup"><span data-stu-id="cd87a-450">Security checklist</span></span>

<span data-ttu-id="cd87a-451">以下安全注意事项列表并不全面：</span><span class="sxs-lookup"><span data-stu-id="cd87a-451">The following list of security considerations isn't comprehensive:</span></span>

* <span data-ttu-id="cd87a-452">验证事件参数。</span><span class="sxs-lookup"><span data-stu-id="cd87a-452">Validate arguments from events.</span></span>
* <span data-ttu-id="cd87a-453">验证 JS 互通调用的输入和结果。</span><span class="sxs-lookup"><span data-stu-id="cd87a-453">Validate inputs and results from JS interop calls.</span></span>
* <span data-ttu-id="cd87a-454">避免使用 （或事先验证） 用户输入 .NET 到 JS 互通调用。</span><span class="sxs-lookup"><span data-stu-id="cd87a-454">Avoid using (or validate beforehand) user input for .NET to JS interop calls.</span></span>
* <span data-ttu-id="cd87a-455">防止客户端分配未绑定的内存量。</span><span class="sxs-lookup"><span data-stu-id="cd87a-455">Prevent the client from allocating an unbound amount of memory.</span></span>
  * <span data-ttu-id="cd87a-456">组件中的数据。</span><span class="sxs-lookup"><span data-stu-id="cd87a-456">Data within the component.</span></span>
  * <span data-ttu-id="cd87a-457">`DotNetObject`返回给客户端的引用。</span><span class="sxs-lookup"><span data-stu-id="cd87a-457">`DotNetObject` references returned to the client.</span></span>
* <span data-ttu-id="cd87a-458">防止多个调度。</span><span class="sxs-lookup"><span data-stu-id="cd87a-458">Guard against multiple dispatches.</span></span>
* <span data-ttu-id="cd87a-459">释放组件时取消长时间运行的操作。</span><span class="sxs-lookup"><span data-stu-id="cd87a-459">Cancel long-running operations when the component is disposed.</span></span>
* <span data-ttu-id="cd87a-460">避免生成大量数据的事件。</span><span class="sxs-lookup"><span data-stu-id="cd87a-460">Avoid events that produce large amounts of data.</span></span>
* <span data-ttu-id="cd87a-461">避免将用户输入用作调用`NavigationManager.Navigate`URL 的一部分，如果不可避免，请先根据一组允许的原点验证 URL 的用户输入。</span><span class="sxs-lookup"><span data-stu-id="cd87a-461">Avoid using user input as part of calls to `NavigationManager.Navigate` and validate user input for URLs against a set of allowed origins first if unavoidable.</span></span>
* <span data-ttu-id="cd87a-462">不要根据 UI 的状态做出授权决策，而只能根据组件状态做出授权决策。</span><span class="sxs-lookup"><span data-stu-id="cd87a-462">Don't make authorization decisions based on the state of the UI but only from component state.</span></span>
* <span data-ttu-id="cd87a-463">请考虑使用[内容安全策略 （CSP）](https://developer.mozilla.org/docs/Web/HTTP/CSP)来抵御 XSS 攻击。</span><span class="sxs-lookup"><span data-stu-id="cd87a-463">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to protect against XSS attacks.</span></span>
* <span data-ttu-id="cd87a-464">请考虑使用 CSP 和[X 帧选项](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)来防止咔嗒声劫持。</span><span class="sxs-lookup"><span data-stu-id="cd87a-464">Consider using CSP and [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) to protect against click-jacking.</span></span>
* <span data-ttu-id="cd87a-465">在启用 CORS 或显式禁用应用Blazor的 CORS 时，确保 CORS 设置是合适的。</span><span class="sxs-lookup"><span data-stu-id="cd87a-465">Ensure CORS settings are appropriate when enabling CORS or explicitly disable CORS for Blazor apps.</span></span>
* <span data-ttu-id="cd87a-466">测试以确保Blazor应用的服务器端限制提供可接受的用户体验，而不会造成不可接受的风险级别。</span><span class="sxs-lookup"><span data-stu-id="cd87a-466">Test to ensure that the server-side limits for the Blazor app provide an acceptable user experience without unacceptable levels of risk.</span></span>
