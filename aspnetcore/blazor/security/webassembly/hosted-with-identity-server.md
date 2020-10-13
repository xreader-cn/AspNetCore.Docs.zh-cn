---
title: 使用 Identity 服务器保护托管 ASP.NET Core Blazor WebAssembly 应用
author: guardrex
description: 了解如何使用 Identity 服务器保护托管 ASP.NET Core Blazor WebAssembly 应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/02/2020
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 91cc7ffc46f5f1f68efd7e481479b19938476cb0
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762238"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a><span data-ttu-id="43515-103">使用 Identity 服务器保护 ASP.NET Core Blazor WebAssembly 托管应用</span><span class="sxs-lookup"><span data-stu-id="43515-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="43515-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="43515-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="43515-105">本文介绍如何创建[托管的 Blazor WebAssembly 应用](xref:blazor/hosting-models#blazor-webassembly)，该应用使用 [IdentityServer](https://identityserver.io/) 以对用户和 API 调用进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="43515-105">This article explains how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="43515-106">若要将独立或托管 Blazor WebAssembly 应用配置为使用现有的外部 Identity 服务器实例，请按照 <xref:blazor/security/webassembly/standalone-with-authentication-library> 中的指南操作。</span><span class="sxs-lookup"><span data-stu-id="43515-106">To configure a standalone or hosted Blazor WebAssembly app to use an existing, external Identity Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="43515-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43515-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="43515-108">新建具有身份验证机制的 Blazor WebAssembly 项目：</span><span class="sxs-lookup"><span data-stu-id="43515-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="43515-109">在“新建 ASP.NET Core Web 应用”对话框中选择“Blazor WebAssembly应用”模板后，选择“身份验证”下的“更改”。</span><span class="sxs-lookup"><span data-stu-id="43515-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="43515-110">通过“存储应用内的用户帐户”选项选择“单个用户帐户”，以使用 ASP.NET Core 的 [Identity](xref:security/authentication/identity) 系统存储应用内的用户。 </span><span class="sxs-lookup"><span data-stu-id="43515-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="43515-111">在“高级”部分中选中“托管的 ASP.NET Core”复选框 。</span><span class="sxs-lookup"><span data-stu-id="43515-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="43515-112">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="43515-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="43515-113">若要在空文件夹中新建具有身份验证机制的 Blazor WebAssembly 项目，则通过 `-au|--auth` 选项指定 `Individual` 身份验证机制，以使用 ASP.NET Core 的 [Identity](xref:security/authentication/identity) 系统存储应用内的用户：</span><span class="sxs-lookup"><span data-stu-id="43515-113">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="43515-114">占位符</span><span class="sxs-lookup"><span data-stu-id="43515-114">Placeholder</span></span>  | <span data-ttu-id="43515-115">示例</span><span class="sxs-lookup"><span data-stu-id="43515-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="43515-116">使用 `-o|--output` 选项指定的输出位置将创建一个项目文件夹（如果该文件夹不存在）并成为应用程序名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="43515-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="43515-117">有关详细信息，请参阅 .NET Core 指南中的 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令。</span><span class="sxs-lookup"><span data-stu-id="43515-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="43515-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="43515-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="43515-119">新建具有身份验证机制的 Blazor WebAssembly 项目：</span><span class="sxs-lookup"><span data-stu-id="43515-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="43515-120">在“配置新的 Blazor WebAssembly应用”步骤中，从“身份验证”下拉列表中选择“个人身份验证(应用内)”。</span><span class="sxs-lookup"><span data-stu-id="43515-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="43515-121">此应用是使用 ASP.NET Core [Identity](xref:security/authentication/identity) 为应用中存储的个人用户创建的。</span><span class="sxs-lookup"><span data-stu-id="43515-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="43515-122">选中“托管的 ASP.NET Core”复选框。</span><span class="sxs-lookup"><span data-stu-id="43515-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="43515-123">服务器应用配置</span><span class="sxs-lookup"><span data-stu-id="43515-123">Server app configuration</span></span>

<span data-ttu-id="43515-124">以下部分介绍了在包括身份验证支持的情况下对项目添加的内容。</span><span class="sxs-lookup"><span data-stu-id="43515-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="43515-125">Startup 类</span><span class="sxs-lookup"><span data-stu-id="43515-125">Startup class</span></span>

<span data-ttu-id="43515-126">`Startup` 类包含以下添加项。</span><span class="sxs-lookup"><span data-stu-id="43515-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="43515-127">在 `Startup.ConfigureServices`中：</span><span class="sxs-lookup"><span data-stu-id="43515-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="43515-128">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="43515-128">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="43515-129">包含附加 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 帮助器方法的 IdentityServer，该方法在 IdentityServer 之上设置默认 ASP.NET Core 约定：</span><span class="sxs-lookup"><span data-stu-id="43515-129">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="43515-130">包含附加 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 帮助器方法的身份验证，该方法将应用配置为验证由 IdentityServer 生成的 JWT 令牌：</span><span class="sxs-lookup"><span data-stu-id="43515-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="43515-131">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="43515-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="43515-132">IdentityServer 中间件公开 OpenID Connect (OIDC) 终结点：</span><span class="sxs-lookup"><span data-stu-id="43515-132">The IdentityServer middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="43515-133">身份验证中间件负责验证请求凭据并在请求上下文中设置用户：</span><span class="sxs-lookup"><span data-stu-id="43515-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="43515-134">授权中间件支持授权功能：</span><span class="sxs-lookup"><span data-stu-id="43515-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="43515-135">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="43515-135">AddApiAuthorization</span></span>

<span data-ttu-id="43515-136"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 帮助器方法配置 [IdentityServer](https://identityserver.io/) 用于 ASP.NET Core 方案。</span><span class="sxs-lookup"><span data-stu-id="43515-136">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="43515-137">IdentityServer 是一个功能强大且可扩展的框架，用于处理应用安全问题。</span><span class="sxs-lookup"><span data-stu-id="43515-137">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="43515-138">IdentityServer 公开大多数情况下不必要的复杂性。</span><span class="sxs-lookup"><span data-stu-id="43515-138">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="43515-139">因此，提供了一组约定和配置选项作为良好的起点。</span><span class="sxs-lookup"><span data-stu-id="43515-139">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="43515-140">一旦身份验证需要更改，就可使用 IdentityServer 的完整功能自定义身份验证以满足应用的要求。</span><span class="sxs-lookup"><span data-stu-id="43515-140">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="43515-141">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="43515-141">AddIdentityServerJwt</span></span>

<span data-ttu-id="43515-142"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 帮助器方法将应用的策略方案配置为默认身份验证处理程序。</span><span class="sxs-lookup"><span data-stu-id="43515-142">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="43515-143">该策略配置为允许 Identity 处理路由到 Identity URL 空间 `/Identity` 中任何子路径的所有请求。</span><span class="sxs-lookup"><span data-stu-id="43515-143">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="43515-144"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 处理所有其他请求。</span><span class="sxs-lookup"><span data-stu-id="43515-144">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="43515-145">此外，此方法还可以：</span><span class="sxs-lookup"><span data-stu-id="43515-145">Additionally, this method:</span></span>

* <span data-ttu-id="43515-146">向具有默认 `{APPLICATION NAME}API` 范围的 IdentityServer 注册 `{APPLICATION NAME}API` API 资源。</span><span class="sxs-lookup"><span data-stu-id="43515-146">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="43515-147">配置 JWT 持有者令牌中间件以验证 IdentityServer 为应用颁发的令牌。</span><span class="sxs-lookup"><span data-stu-id="43515-147">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="43515-148">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="43515-148">WeatherForecastController</span></span>

<span data-ttu-id="43515-149">在 `WeatherForecastController` (`Controllers/WeatherForecastController.cs`) 中，[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 特性应用于该类。</span><span class="sxs-lookup"><span data-stu-id="43515-149">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="43515-150">该属性指示用户必须根据默认策略获得授权才能访问资源。</span><span class="sxs-lookup"><span data-stu-id="43515-150">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="43515-151">默认授权策略配置为使用默认身份验证方案，由 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 设置。</span><span class="sxs-lookup"><span data-stu-id="43515-151">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="43515-152">帮助器方法将 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 配置为对应用的请求的默认处理程序。</span><span class="sxs-lookup"><span data-stu-id="43515-152">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="43515-153">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="43515-153">ApplicationDbContext</span></span>

<span data-ttu-id="43515-154">在 `ApplicationDbContext` (`Data/ApplicationDbContext.cs`) 中，<xref:Microsoft.EntityFrameworkCore.DbContext> 扩展 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> 以包括 IdentityServer 的架构。</span><span class="sxs-lookup"><span data-stu-id="43515-154">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="43515-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> 派生自 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>。</span><span class="sxs-lookup"><span data-stu-id="43515-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="43515-156">要获取对数据库架构的完全控制，请从其中一个可用的 Identity <xref:Microsoft.EntityFrameworkCore.DbContext> 类继承，并通过在 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> 方法中调用 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` 来配置上下文以包括 Identity 架构。</span><span class="sxs-lookup"><span data-stu-id="43515-156">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="43515-157">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="43515-157">OidcConfigurationController</span></span>

<span data-ttu-id="43515-158">在 `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`) 中，客户端终结点预配为提供 OIDC 参数。</span><span class="sxs-lookup"><span data-stu-id="43515-158">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="43515-159">应用设置</span><span class="sxs-lookup"><span data-stu-id="43515-159">App settings</span></span>

<span data-ttu-id="43515-160">在项目根目录的应用设置文件 (`appsettings.json`) 中，`IdentityServer` 部分描述已配置的客户端列表。</span><span class="sxs-lookup"><span data-stu-id="43515-160">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="43515-161">下例中存在一个客户端。</span><span class="sxs-lookup"><span data-stu-id="43515-161">In the following example, there's a single client.</span></span> <span data-ttu-id="43515-162">客户端名称对应于应用名称，并通过约定映射到 OAuth `ClientId` 参数。</span><span class="sxs-lookup"><span data-stu-id="43515-162">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="43515-163">配置文件指示正在配置的应用类型。</span><span class="sxs-lookup"><span data-stu-id="43515-163">The profile indicates the app type being configured.</span></span> <span data-ttu-id="43515-164">配置文件在内部用于促进简化服务器配置过程的约定。</span><span class="sxs-lookup"><span data-stu-id="43515-164">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="43515-165">占位符 `{APP ASSEMBLY}` 是应用的程序集名称（例如 `BlazorSample.Client`）。</span><span class="sxs-lookup"><span data-stu-id="43515-165">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="43515-166">客户端应用配置</span><span class="sxs-lookup"><span data-stu-id="43515-166">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="43515-167">身份验证包</span><span class="sxs-lookup"><span data-stu-id="43515-167">Authentication package</span></span>

<span data-ttu-id="43515-168">创建应用以使用个人用户帐户 (`Individual`) 时，该应用会在其项目文件中自动接收 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 包的包引用。</span><span class="sxs-lookup"><span data-stu-id="43515-168">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="43515-169">此包提供了一组基元，可帮助应用验证用户身份并获取令牌以调用受保护的 API。</span><span class="sxs-lookup"><span data-stu-id="43515-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="43515-170">如果向应用添加身份验证，请手动将包添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="43515-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="43515-171">对于占位符 `{VERSION}`，可在包的版本历史记录（位于 [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)）中找到与应用的共享框架版本匹配的最新稳定版本的包。</span><span class="sxs-lookup"><span data-stu-id="43515-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

### <a name="httpclient-configuration"></a><span data-ttu-id="43515-172">`HttpClient` 配置</span><span class="sxs-lookup"><span data-stu-id="43515-172">`HttpClient` configuration</span></span>

<span data-ttu-id="43515-173">在 `Program.Main` (`Program.cs`) 中，命名的 <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) 配置为提供 <xref:System.Net.Http.HttpClient> 实例，以在向服务器 API 发出请求时包含访问令牌：</span><span class="sxs-lookup"><span data-stu-id="43515-173">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="43515-174">若要将 Blazor WebAssembly 应用配置为使用不属于托管 Blazor 解决方案的现有 Identity 服务器实例，请将 <xref:System.Net.Http.HttpClient> 基址注册从 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) 更改为服务器应用的 API 授权终结点 URL。</span><span class="sxs-lookup"><span data-stu-id="43515-174">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a hosted Blazor solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="43515-175">API 身份验证支持</span><span class="sxs-lookup"><span data-stu-id="43515-175">API authorization support</span></span>

<span data-ttu-id="43515-176">使用 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 包中提供的扩展方法在服务容器中加入用户身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="43515-176">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="43515-177">此方法设置应用所需的服务以与现有授权系统交互。</span><span class="sxs-lookup"><span data-stu-id="43515-177">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="43515-178">默认情况下，应用的配置按约定从 `_configuration/{client-id}` 加载。</span><span class="sxs-lookup"><span data-stu-id="43515-178">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="43515-179">按照约定，客户端 ID 设置为应用的程序集名称。</span><span class="sxs-lookup"><span data-stu-id="43515-179">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="43515-180">可以通过使用选项调用重载来更改此 URL 以指向单独的终结点。</span><span class="sxs-lookup"><span data-stu-id="43515-180">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="43515-181">导入文件</span><span class="sxs-lookup"><span data-stu-id="43515-181">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="43515-182">索引页</span><span class="sxs-lookup"><span data-stu-id="43515-182">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="43515-183">应用组件</span><span class="sxs-lookup"><span data-stu-id="43515-183">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="43515-184">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="43515-184">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="43515-185">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="43515-185">LoginDisplay component</span></span>

<span data-ttu-id="43515-186">`LoginDisplay` 组件 (`Shared/LoginDisplay.razor`) 在 `MainLayout` 组件 (`Shared/MainLayout.razor`) 中呈现并管理以下行为：</span><span class="sxs-lookup"><span data-stu-id="43515-186">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="43515-187">对于经过身份验证的用户：</span><span class="sxs-lookup"><span data-stu-id="43515-187">For authenticated users:</span></span>
  * <span data-ttu-id="43515-188">显示当前用户名。</span><span class="sxs-lookup"><span data-stu-id="43515-188">Displays the current user name.</span></span>
  * <span data-ttu-id="43515-189">提供指向 ASP.NET Core Identity 中的用户配置文件页面的链接。</span><span class="sxs-lookup"><span data-stu-id="43515-189">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="43515-190">提供用于注销应用的按钮。</span><span class="sxs-lookup"><span data-stu-id="43515-190">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="43515-191">对于匿名用户：</span><span class="sxs-lookup"><span data-stu-id="43515-191">For anonymous users:</span></span>
  * <span data-ttu-id="43515-192">提供用于注册的选项。</span><span class="sxs-lookup"><span data-stu-id="43515-192">Offers the option to register.</span></span>
  * <span data-ttu-id="43515-193">提供用于登录的选项。</span><span class="sxs-lookup"><span data-stu-id="43515-193">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a><span data-ttu-id="43515-194">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="43515-194">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="43515-195">FetchData 组件</span><span class="sxs-lookup"><span data-stu-id="43515-195">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="43515-196">运行应用</span><span class="sxs-lookup"><span data-stu-id="43515-196">Run the app</span></span>

<span data-ttu-id="43515-197">从服务器项目运行应用。</span><span class="sxs-lookup"><span data-stu-id="43515-197">Run the app from the Server project.</span></span> <span data-ttu-id="43515-198">使用 Visual Studio 时，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="43515-198">When using Visual Studio, either:</span></span>

* <span data-ttu-id="43515-199">在工具栏中将“启动项目”下拉列表设置为“服务器 API 应用”，然后选择“运行”按钮。</span><span class="sxs-lookup"><span data-stu-id="43515-199">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="43515-200">在“解决方案资源管理器”中选择服务器项目，然后选择工具栏中的“运行”按钮，或从“调试”菜单启动应用  。</span><span class="sxs-lookup"><span data-stu-id="43515-200">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="43515-201">具有 API 授权的名称和角色声明</span><span class="sxs-lookup"><span data-stu-id="43515-201">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="43515-202">自定义用户工厂</span><span class="sxs-lookup"><span data-stu-id="43515-202">Custom user factory</span></span>

<span data-ttu-id="43515-203">在“客户端”应用中，创建自定义用户工厂。</span><span class="sxs-lookup"><span data-stu-id="43515-203">In the Client app, create a custom user factory.</span></span> <span data-ttu-id="43515-204">Identity 服务器在一个 `role` 声明中发送多个角色作为 JSON 数组。</span><span class="sxs-lookup"><span data-stu-id="43515-204">Identity Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="43515-205">单个角色在该声明中作为单个字符串值进行发送。</span><span class="sxs-lookup"><span data-stu-id="43515-205">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="43515-206">工厂为每个用户的角色创建单个 `role` 声明。</span><span class="sxs-lookup"><span data-stu-id="43515-206">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="43515-207">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="43515-207">`CustomUserFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

<span data-ttu-id="43515-208">在客户端应用中，在 `Program.Main` (`Program.cs`) 中注册工厂：</span><span class="sxs-lookup"><span data-stu-id="43515-208">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="43515-209">在服务器应用中，调用 Identity 生成器上的 <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*>，添加与角色相关的服务：</span><span class="sxs-lookup"><span data-stu-id="43515-209">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a><span data-ttu-id="43515-210">配置 Identity 服务器</span><span class="sxs-lookup"><span data-stu-id="43515-210">Configure Identity Server</span></span>

<span data-ttu-id="43515-211">使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="43515-211">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="43515-212">API 身份验证选项</span><span class="sxs-lookup"><span data-stu-id="43515-212">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="43515-213">配置文件服务</span><span class="sxs-lookup"><span data-stu-id="43515-213">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="43515-214">API 身份验证选项</span><span class="sxs-lookup"><span data-stu-id="43515-214">API authorization options</span></span>

<span data-ttu-id="43515-215">在服务器应用中：</span><span class="sxs-lookup"><span data-stu-id="43515-215">In the Server app:</span></span>

* <span data-ttu-id="43515-216">配置 Identity 服务器，将 `name` 和 `role` 声明放入 ID 令牌和访问令牌中。</span><span class="sxs-lookup"><span data-stu-id="43515-216">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="43515-217">阻止 JWT 令牌处理程序中角色的默认映射。</span><span class="sxs-lookup"><span data-stu-id="43515-217">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="43515-218">配置文件服务</span><span class="sxs-lookup"><span data-stu-id="43515-218">Profile Service</span></span>

<span data-ttu-id="43515-219">在服务器应用中，创建 `ProfileService` 实现。</span><span class="sxs-lookup"><span data-stu-id="43515-219">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="43515-220">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="43515-220">`ProfileService.cs`:</span></span>

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

<span data-ttu-id="43515-221">在服务器应用中，在 `Startup.ConfigureServices` 中注册配置文件服务：</span><span class="sxs-lookup"><span data-stu-id="43515-221">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="43515-222">使用授权机制</span><span class="sxs-lookup"><span data-stu-id="43515-222">Use authorization mechanisms</span></span>

<span data-ttu-id="43515-223">在客户端应用中，组件授权方法此时有效。</span><span class="sxs-lookup"><span data-stu-id="43515-223">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="43515-224">组件中的任何授权机制都可以使用角色来授权用户：</span><span class="sxs-lookup"><span data-stu-id="43515-224">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="43515-225">[`AuthorizeView` 组件](xref:blazor/security/index#authorizeview-component)（例如：`<AuthorizeView Roles="admin">`）</span><span class="sxs-lookup"><span data-stu-id="43515-225">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="43515-226">[`[Authorize]` 属性指令](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)（例如 `@attribute [Authorize(Roles = "admin")]`）</span><span class="sxs-lookup"><span data-stu-id="43515-226">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="43515-227">[过程逻辑](xref:blazor/security/index#procedural-logic)（例如 `if (user.IsInRole("admin")) { ... }`）</span><span class="sxs-lookup"><span data-stu-id="43515-227">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="43515-228">支持多个角色测试：</span><span class="sxs-lookup"><span data-stu-id="43515-228">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="43515-229">`User.Identity.Name` 在客户端应用中进行填充，并带有用户的用户名，这通常是他们的登录电子邮件地址。</span><span class="sxs-lookup"><span data-stu-id="43515-229">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain"></a><span data-ttu-id="43515-230">使用自定义域托管在 Azure 应用服务中</span><span class="sxs-lookup"><span data-stu-id="43515-230">Host in Azure App Service with a custom domain</span></span>

<span data-ttu-id="43515-231">以下指南介绍如何通过 Identity 服务器，使用自定义域将托管的 Blazor WebAssembly 应用部署到 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)。</span><span class="sxs-lookup"><span data-stu-id="43515-231">The following guidance explains how to deploy a hosted Blazor WebAssembly app with Identity Server to [Azure App Service](https://azure.microsoft.com/services/app-service/) with a custom domain.</span></span>

<span data-ttu-id="43515-232">对于这种托管方案，请勿将相同的证书用于 [Identity 服务器的令牌签名密钥](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation)以及站点与浏览器的 HTTPS 安全通信：</span><span class="sxs-lookup"><span data-stu-id="43515-232">For this hosting scenario, do **not** use the same certificate for [Identity Server's token signing key](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) and the site's HTTPS secure communication with browsers:</span></span>

* <span data-ttu-id="43515-233">针对这两个要求使用不同的证书是一种很好的安全做法，因为这样能隔离不同用途的私钥。</span><span class="sxs-lookup"><span data-stu-id="43515-233">Using different certificates for these two requirements is a good security practice because it isolates private keys for each purpose.</span></span>
* <span data-ttu-id="43515-234">用于与浏览器进行通信的 TLS 证书是独立管理的，不会影响 Identity 服务器的令牌签名。</span><span class="sxs-lookup"><span data-stu-id="43515-234">TLS certificates for communication with browsers is managed independently without affecting Identity Server's token signing.</span></span>
* <span data-ttu-id="43515-235">当 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 向应用服务应用提供用于绑定自定义域的证书时，Identity 服务器无法从 Azure Key Vault 获取相同的证书来进行令牌签名。</span><span class="sxs-lookup"><span data-stu-id="43515-235">When [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) supplies a certificate to an App Service app for custom domain binding, Identity Server can't obtain the same certificate from Azure Key Vault for token signing.</span></span> <span data-ttu-id="43515-236">尽管可以将 Identity 服务器配置为使用来自物理路径的相同 TLS 证书，但将安全证书置于源代码管理中是一种不太好的做法，在大多数情况下都应尽量避免此类行为。</span><span class="sxs-lookup"><span data-stu-id="43515-236">Although configuring Identity Server to use the same TLS certificate from a physical path is possible, placing security certificates into source control is a **poor practice and should be avoided in most scenarios**.</span></span>

<span data-ttu-id="43515-237">接下来的指南将在 Azure Key Vault 中创建一个仅用于 Identity 服务器令牌签名的自签名证书。</span><span class="sxs-lookup"><span data-stu-id="43515-237">In the following guidance, a self-signed certificate is created in Azure Key Vault solely for Identity Server token signing.</span></span> <span data-ttu-id="43515-238">Identity 服务器配置通过应用的 `My` > `CurrentUser` 证书存储使用密钥保管库证书。</span><span class="sxs-lookup"><span data-stu-id="43515-238">The Identity Server configuration uses the key vault certificate via the app's `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="43515-239">其他用于自定义域 HTTPS 流量的证书是与 Identity 服务器签名证书分开创建和配置的。</span><span class="sxs-lookup"><span data-stu-id="43515-239">Other certificates used for HTTPS traffic with custom domains are created and configured separately from the Identity Server signing certificate.</span></span>

<span data-ttu-id="43515-240">若要将应用、Azure 应用服务和 Azure Key Vault 配置为使用自定义域和 HTTPS 进行托管，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="43515-240">To configure an app, Azure App Service, and Azure Key Vault to host with a custom domain and HTTPS:</span></span>

1. <span data-ttu-id="43515-241">创建计划级别不低于 `Basic B1` 的[应用服务计划](/azure/app-service/overview-hosting-plans)。</span><span class="sxs-lookup"><span data-stu-id="43515-241">Create an [App Service plan](/azure/app-service/overview-hosting-plans) with an plan level of `Basic B1` or higher.</span></span> <span data-ttu-id="43515-242">应用服务需要 `Basic B1` 或更高的服务层级才能使用自定义域。</span><span class="sxs-lookup"><span data-stu-id="43515-242">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="43515-243">使用组织控制的站点的完全限定的域名 (FQDN) 的公用名（例如 `www.contoso.com`）为站点的安全浏览器通信（HTTPS 协议）创建 PFX 证书。</span><span class="sxs-lookup"><span data-stu-id="43515-243">Create a PFX certificate for the site's secure browser communication (HTTPS protocol) with a common name of the site's fully qualified domain name (FQDN) that your organization controls (for example, `www.contoso.com`).</span></span> <span data-ttu-id="43515-244">通过以下几项内容创建证书：</span><span class="sxs-lookup"><span data-stu-id="43515-244">Create the certificate with:</span></span>
   * <span data-ttu-id="43515-245">密钥使用</span><span class="sxs-lookup"><span data-stu-id="43515-245">Key uses</span></span>
     * <span data-ttu-id="43515-246">数字签名验证 (`digitalSignature`)</span><span class="sxs-lookup"><span data-stu-id="43515-246">Digital signature validation (`digitalSignature`)</span></span>
     * <span data-ttu-id="43515-247">密钥加密 (`keyEncipherment`)</span><span class="sxs-lookup"><span data-stu-id="43515-247">Key encipherment (`keyEncipherment`)</span></span>
   * <span data-ttu-id="43515-248">增强/扩展的密钥使用</span><span class="sxs-lookup"><span data-stu-id="43515-248">Enhanced/extended key uses</span></span>
     * <span data-ttu-id="43515-249">客户端身份验证 (1.3.6.1.5.5.7.3.2)</span><span class="sxs-lookup"><span data-stu-id="43515-249">Client Authentication (1.3.6.1.5.5.7.3.2)</span></span>
     * <span data-ttu-id="43515-250">服务器身份验证 (1.3.6.1.5.5.7.3.1)</span><span class="sxs-lookup"><span data-stu-id="43515-250">Server Authentication (1.3.6.1.5.5.7.3.1)</span></span>

   <span data-ttu-id="43515-251">若要创建证书，请使用以下方法之一或任何其他合适的工具或在线服务：</span><span class="sxs-lookup"><span data-stu-id="43515-251">To create the certificate, use one of the following approaches or any other suitable tool or online service:</span></span>

   * [<span data-ttu-id="43515-252">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="43515-252">Azure Key Vault</span></span>](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [<span data-ttu-id="43515-253">Windows 上的 MakeCert</span><span class="sxs-lookup"><span data-stu-id="43515-253">MakeCert on Windows</span></span>](/windows/desktop/seccrypto/makecert)
   * [<span data-ttu-id="43515-254">OpenSSL</span><span class="sxs-lookup"><span data-stu-id="43515-254">OpenSSL</span></span>](https://www.openssl.org)

   <span data-ttu-id="43515-255">记下密码，稍后将证书导入 Azure Key Vault 时会用到该密码。</span><span class="sxs-lookup"><span data-stu-id="43515-255">Make note of the password, which is used later to import the certificate into Azure Key Vault.</span></span>

   <span data-ttu-id="43515-256">有关 Azure Key Vault 证书的详细信息，请参阅 [Azure Key Vault：](/azure/key-vault/certificates/)证书”。</span><span class="sxs-lookup"><span data-stu-id="43515-256">For more information on Azure Key Vault certificates, see [Azure Key Vault: Certificates](/azure/key-vault/certificates/).</span></span>
1. <span data-ttu-id="43515-257">创建新的 Azure Key Vault 或使用 Azure 订阅中现有的密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="43515-257">Create a new Azure Key Vault or use an existing key vault in your Azure subscription.</span></span>
1. <span data-ttu-id="43515-258">在密钥保管库的“证书”区域中，导入 PFX 站点证书。</span><span class="sxs-lookup"><span data-stu-id="43515-258">In the key vault's **Certificates** area, import the PFX site certificate.</span></span> <span data-ttu-id="43515-259">记录证书的指纹，稍后在应用的配置过程中会用到它。</span><span class="sxs-lookup"><span data-stu-id="43515-259">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="43515-260">在 Azure Key Vault 中，生成新的自签名证书以用于 Identity 服务器令牌签名。</span><span class="sxs-lookup"><span data-stu-id="43515-260">In Azure Key Vault, generate a new self-signed certificate for Identity Server token signing.</span></span> <span data-ttu-id="43515-261">给出证书的“证书名称”以及“使用者” 。</span><span class="sxs-lookup"><span data-stu-id="43515-261">Give the certificate a **Certificate Name** and **Subject**.</span></span> <span data-ttu-id="43515-262">“使用者”指定为 `CN={COMMON NAME}`，其中 `{COMMON NAME}` 占位符是证书的公用名。</span><span class="sxs-lookup"><span data-stu-id="43515-262">The **Subject** is specified as `CN={COMMON NAME}`, where the `{COMMON NAME}` placeholder is the certificate's common name.</span></span> <span data-ttu-id="43515-263">公用名可以是任意字母数字字符串。</span><span class="sxs-lookup"><span data-stu-id="43515-263">The common name can be any alphanumeric string.</span></span> <span data-ttu-id="43515-264">例如 `CN=IdentityServerSigning` 就是一个有效的证书“使用者”。</span><span class="sxs-lookup"><span data-stu-id="43515-264">For example, `CN=IdentityServerSigning` is a valid certificate **Subject**.</span></span> <span data-ttu-id="43515-265">使用默认的高级策略配置设置。</span><span class="sxs-lookup"><span data-stu-id="43515-265">Use the default **Advanced Policy Configuration** settings.</span></span> <span data-ttu-id="43515-266">记录证书的指纹，稍后在应用的配置过程中会用到它。</span><span class="sxs-lookup"><span data-stu-id="43515-266">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="43515-267">导航到 Azure 门户中的 Azure 应用服务，并使用以下配置创建新的应用服务：</span><span class="sxs-lookup"><span data-stu-id="43515-267">Navigate to Azure App Service in the Azure portal and create a new App Service with the following configuration:</span></span>
   * <span data-ttu-id="43515-268">将“发布”设置为 `Code`。</span><span class="sxs-lookup"><span data-stu-id="43515-268">**Publish** set to `Code`.</span></span>
   * <span data-ttu-id="43515-269">将“运行时堆栈”设置为应用的运行时。</span><span class="sxs-lookup"><span data-stu-id="43515-269">**Runtime stack** set to the app's runtime.</span></span>
   * <span data-ttu-id="43515-270">对于“Sku 和大小”，请确认应用服务层级不低于 `Basic B1`。</span><span class="sxs-lookup"><span data-stu-id="43515-270">For **Sku and size**, confirm that the App Service tier is `Basic B1` or higher.</span></span>  <span data-ttu-id="43515-271">应用服务需要 `Basic B1` 或更高的服务层级才能使用自定义域。</span><span class="sxs-lookup"><span data-stu-id="43515-271">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="43515-272">在 Azure 创建应用服务后，请打开应用的“配置”并添加一个新的应用程序设置，该设置指定之前记录的证书指纹。</span><span class="sxs-lookup"><span data-stu-id="43515-272">After Azure creates the App Service, open the app's **Configuration** and add a new application setting specifying the certificate thumbprints recorded earlier.</span></span> <span data-ttu-id="43515-273">应用设置密钥为 `WEBSITE_LOAD_CERTIFICATES`。</span><span class="sxs-lookup"><span data-stu-id="43515-273">The app setting key is `WEBSITE_LOAD_CERTIFICATES`.</span></span> <span data-ttu-id="43515-274">使用逗号分隔应用设置值中的证书指纹，如下面的示例所示：</span><span class="sxs-lookup"><span data-stu-id="43515-274">Separate the certificate thumbprints in the app setting value with a comma, as the following example shows:</span></span>
   * <span data-ttu-id="43515-275">键：`WEBSITE_LOAD_CERTIFICATES`</span><span class="sxs-lookup"><span data-stu-id="43515-275">Key: `WEBSITE_LOAD_CERTIFICATES`</span></span>
   * <span data-ttu-id="43515-276">值：`57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span><span class="sxs-lookup"><span data-stu-id="43515-276">Value: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span></span>

   <span data-ttu-id="43515-277">在 Azure 门户中，可通过两个步骤保存应用设置：保存 `WEBSITE_LOAD_CERTIFICATES` 键-值设置，然后选择边栏选项卡顶部的“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="43515-277">In the Azure portal, saving app settings is a two-step process: Save the `WEBSITE_LOAD_CERTIFICATES` key-value setting, then select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="43515-278">选择应用的“TLS/SSL 设置”。</span><span class="sxs-lookup"><span data-stu-id="43515-278">Select the app's **TLS/SSL settings**.</span></span> <span data-ttu-id="43515-279">选择“私钥证书(.pfx)”。</span><span class="sxs-lookup"><span data-stu-id="43515-279">Select **Private Key Certificates (.pfx)**.</span></span> <span data-ttu-id="43515-280">完成两次“导入 Key Vault 证书”流程，导入用于 HTTPS 通信的站点证书以及站点的自签名 Identity 服务器令牌签名证书。</span><span class="sxs-lookup"><span data-stu-id="43515-280">Use the **Import Key Vault Certificate** process twice to import both the site's certificate for HTTPS communication and the site's self-signed Identity Server token signing certificate.</span></span>
1. <span data-ttu-id="43515-281">导航到“自定义域”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="43515-281">Navigate to the **Custom domains** blade.</span></span> <span data-ttu-id="43515-282">在域注册机构的网站上，使用 IP 地址和自定义域验证 ID 来配置域 。</span><span class="sxs-lookup"><span data-stu-id="43515-282">At your domain registrar's website, use the **IP address** and **Custom Domain Verification ID** to configure the domain.</span></span> <span data-ttu-id="43515-283">域配置通常包括：</span><span class="sxs-lookup"><span data-stu-id="43515-283">A typical domain configuration includes:</span></span>
   * <span data-ttu-id="43515-284">一个 A 记录，主机为 `@`，以及来自 Azure 门户的 IP 地址值 。</span><span class="sxs-lookup"><span data-stu-id="43515-284">An **A Record** with a **Host** of `@` and a value of the IP address from the Azure portal.</span></span>
   * <span data-ttu-id="43515-285">一个 TXT 记录，主机为 `asuid`，以及由 Azure 生成且由 Azure 门户提供的验证 ID 的值 。</span><span class="sxs-lookup"><span data-stu-id="43515-285">A **TXT Record** with a **Host** of `asuid` and the value of the verification ID generated by Azure and provided by the Azure portal.</span></span>

   <span data-ttu-id="43515-286">请确保将更改正确地保存到域注册机构的网站上。</span><span class="sxs-lookup"><span data-stu-id="43515-286">Make sure that you save the changes at your domain registrar's website correctly.</span></span> <span data-ttu-id="43515-287">部分注册机构网站需要执行两个步骤来保存域记录：先单独保存一条或多条记录，然后使用单独的按钮更新域的注册。</span><span class="sxs-lookup"><span data-stu-id="43515-287">Some registrar websites require a two-step process to save domain records: One or more records are saved individually followed by updating the domain's registration with a separate button.</span></span>
1. <span data-ttu-id="43515-288">返回到 Azure 门户中的“自定义域”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="43515-288">Return to the **Custom domains** blade in the Azure portal.</span></span> <span data-ttu-id="43515-289">选择“添加自定义域”。</span><span class="sxs-lookup"><span data-stu-id="43515-289">Select **Add custom domain**.</span></span> <span data-ttu-id="43515-290">选择“A 记录”选项。</span><span class="sxs-lookup"><span data-stu-id="43515-290">Select the **A Record** option.</span></span> <span data-ttu-id="43515-291">提供域并选择“验证”。</span><span class="sxs-lookup"><span data-stu-id="43515-291">Provide the domain and select **Validate**.</span></span> <span data-ttu-id="43515-292">如果域记录正确并跨 Internet 传播，则门户允许选择“添加自定义域”按钮。</span><span class="sxs-lookup"><span data-stu-id="43515-292">If the domain records are correct and propagated across the Internet, the portal allows you to select the **Add custom domain** button.</span></span>

   <span data-ttu-id="43515-293">域注册机构处理域注册更改后，可能需要几天的时间才能让更改在 Internet 域名服务器 (DNS) 之间传播。</span><span class="sxs-lookup"><span data-stu-id="43515-293">It can take a few days for domain registration changes to propagate across Internet domain name servers (DNS) after they're processed by your domain registrar.</span></span> <span data-ttu-id="43515-294">如果域记录在三个工作日内未更新，请确认是否已通过域注册机构正确设置记录，并与其客户支持部门联系。</span><span class="sxs-lookup"><span data-stu-id="43515-294">If domain records aren't updated within three business days, confirm the records are correctly set with the domain registrar and contact their customer support.</span></span>
1. <span data-ttu-id="43515-295">在“自定义域”边栏选项卡中，域的“SSL STATE”被标记为 `Not Secure` 。</span><span class="sxs-lookup"><span data-stu-id="43515-295">In the **Custom domains** blade, the **SSL STATE** for the domain is marked `Not Secure`.</span></span> <span data-ttu-id="43515-296">选择“添加绑定”链接。</span><span class="sxs-lookup"><span data-stu-id="43515-296">Select the **Add binding** link.</span></span> <span data-ttu-id="43515-297">从密钥保管库中选择站点 HTTPS 证书以绑定自定义域。</span><span class="sxs-lookup"><span data-stu-id="43515-297">Select the site HTTPS certificate from the key vault for the custom domain binding.</span></span>
1. <span data-ttu-id="43515-298">在 Visual Studio 中，打开 Server 项目的应用设置文件（`appsettings.json` 或 `appsettings.Production.json`）。</span><span class="sxs-lookup"><span data-stu-id="43515-298">In Visual Studio, open the *Server* project's app settings file (`appsettings.json` or `appsettings.Production.json`).</span></span> <span data-ttu-id="43515-299">在 Identity 服务器配置中，添加以下 `Key` 部分。</span><span class="sxs-lookup"><span data-stu-id="43515-299">In the Identity Server configuration, add the following `Key` section.</span></span> <span data-ttu-id="43515-300">为 `Name` 密钥指定自签名证书使用者。</span><span class="sxs-lookup"><span data-stu-id="43515-300">Specify the self-signed certificate **Subject** for the `Name` key.</span></span> <span data-ttu-id="43515-301">在以下示例中，密钥保管库中分配的证书公用名为 `IdentityServerSigning`，它生成的使用者为 `CN=IdentityServerSigning`：</span><span class="sxs-lookup"><span data-stu-id="43515-301">In the following example, the certificate's common name assigned in the key vault is `IdentityServerSigning`, which yields a **Subject** of `CN=IdentityServerSigning`:</span></span>

   ```json
   "IdentityServer": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=IdentityServerSigning"
     }
   },
   ```

1. <span data-ttu-id="43515-302">在 Visual Studio 中，创建用于“Server”项目的 Azure 应用服务[发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="43515-302">In Visual Studio, create an Azure App Service [publish profile](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) for the *Server* project.</span></span> <span data-ttu-id="43515-303">在菜单栏中，依次选择：“生成” > “发布” > “新建” > “Azure” > “Azure 应用服务”（Windows 或 Linux）    。</span><span class="sxs-lookup"><span data-stu-id="43515-303">From the menu bar, select: **Build** > **Publish** > **New** > **Azure** > **Azure App Service** (Windows or Linux).</span></span> <span data-ttu-id="43515-304">Visual Studio 连接到 Azure 订阅后，可以按资源类型来设置 Azure 资源的视图 。</span><span class="sxs-lookup"><span data-stu-id="43515-304">When Visual Studio is connected to an Azure subscription, you can set the **View** of Azure resources by **Resource type**.</span></span> <span data-ttu-id="43515-305">在“Web 应用”列表中导航，查找应用的应用服务并将其选中。</span><span class="sxs-lookup"><span data-stu-id="43515-305">Navigate within the **Web App** list to find the App Service for the app and select it.</span></span> <span data-ttu-id="43515-306">选择“完成”  。</span><span class="sxs-lookup"><span data-stu-id="43515-306">Select **Finish**.</span></span>
1. <span data-ttu-id="43515-307">当 Visual Studio 返回到“发布”窗口时，会自动检测密钥保管库和 SQL Server 数据库服务依赖关系。</span><span class="sxs-lookup"><span data-stu-id="43515-307">When Visual Studio returns to the **Publish** window, the key vault and SQL Server database service dependencies are automatically detected.</span></span>

   <span data-ttu-id="43515-308">对于密钥保管库服务，不需要对默认设置进行配置更改。</span><span class="sxs-lookup"><span data-stu-id="43515-308">No configuration changes to the default settings are required for the key vault service.</span></span>

   <span data-ttu-id="43515-309">为了进行测试，应用的本地 [SQLite](https://www.sqlite.org/index.html) 数据库（默认情况下由 Blazor 模板配置）可以与应用一起部署，而无需进行其他配置。</span><span class="sxs-lookup"><span data-stu-id="43515-309">For testing purposes, an app's local [SQLite](https://www.sqlite.org/index.html) database, which is configured by default by the Blazor template, can be deployed with the app without additional configuration.</span></span> <span data-ttu-id="43515-310">本文不讨论在生产环境中为 Identity 服务器配置其他数据库的情况。</span><span class="sxs-lookup"><span data-stu-id="43515-310">Configuring a different database for Identity Server in production is beyond the scope of this article.</span></span> <span data-ttu-id="43515-311">有关详细信息，请参阅以下文档集中的数据库资源：</span><span class="sxs-lookup"><span data-stu-id="43515-311">For more information, see the database resources in the following documentation sets:</span></span>
   * [<span data-ttu-id="43515-312">应用服务</span><span class="sxs-lookup"><span data-stu-id="43515-312">App Service</span></span>](/azure/app-service/)
   * [<span data-ttu-id="43515-313">Identity 服务器</span><span class="sxs-lookup"><span data-stu-id="43515-313">Identity Server</span></span>](https://identityserver4.readthedocs.io/en/latest/)

1. <span data-ttu-id="43515-314">在窗口顶部的部署配置文件名称下，选择“编辑”链接。</span><span class="sxs-lookup"><span data-stu-id="43515-314">Select the **Edit** link under the deployment profile name at the top of the window.</span></span> <span data-ttu-id="43515-315">将“目标 URL”更改为站点的自定义域 URL（例如 `https://www.contoso.com`）。</span><span class="sxs-lookup"><span data-stu-id="43515-315">Change the destination URL to the site's custom domain URL (for example, `https://www.contoso.com`).</span></span> <span data-ttu-id="43515-316">保存设置。</span><span class="sxs-lookup"><span data-stu-id="43515-316">Save the settings.</span></span>
1. <span data-ttu-id="43515-317">发布应用。</span><span class="sxs-lookup"><span data-stu-id="43515-317">Publish the app.</span></span> <span data-ttu-id="43515-318">Visual Studio 将打开一个浏览器窗口，并请求其自定义域所对应的站点。</span><span class="sxs-lookup"><span data-stu-id="43515-318">Visual Studio opens a browser window and requests the site at its custom domain.</span></span>

<span data-ttu-id="43515-319">Azure 文档包含有关在应用服务中通过 TLS 绑定使用 Azure 服务和自定义域的其他详细信息，包括有关使用 CNAME 记录而不是 A 记录的信息。</span><span class="sxs-lookup"><span data-stu-id="43515-319">The Azure documentation contains additional detail on using Azure services and custom domains with TLS binding in App Service, including information on using CNAME records instead of A records.</span></span> <span data-ttu-id="43515-320">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="43515-320">For more information, see the following resources:</span></span>

* [<span data-ttu-id="43515-321">应用服务文档</span><span class="sxs-lookup"><span data-stu-id="43515-321">App Service documentation</span></span>](/azure/app-service/)
* [<span data-ttu-id="43515-322">教程：将现有的自定义 DNS 名称映射到 Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="43515-322">Tutorial: Map an existing custom DNS name to Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-custom-domain)
* [<span data-ttu-id="43515-323">在 Azure 应用服务中使用 TLS/SSL 绑定保护自定义 DNS 名称</span><span class="sxs-lookup"><span data-stu-id="43515-323">Secure a custom DNS name with a TLS/SSL binding in Azure App Service</span></span>](/azure/app-service/configure-ssl-bindings)
* [<span data-ttu-id="43515-324">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="43515-324">Azure Key Vault</span></span>](/azure/key-vault/)

<span data-ttu-id="43515-325">在 Azure 门户中的应用、应用配置或 Azure 服务发生更改后，建议使用新的私密或隐匿浏览器窗口运行每个应用测试。</span><span class="sxs-lookup"><span data-stu-id="43515-325">We recommend using a new in-private or incognito browser window for each app test run after a change to the app, app configuration, or Azure services in the Azure portal.</span></span> <span data-ttu-id="43515-326">在测试站点时，即使站点的配置是正确的，前一个测试运行中的延迟 cookie 仍然可能导致身份验证失败或授权失败。</span><span class="sxs-lookup"><span data-stu-id="43515-326">Lingering cookies from a previous test run can result in failed authentication or authorization when testing the site even when the site's configuration is correct.</span></span> <span data-ttu-id="43515-327">若要详细了解如何将 Visual Studio 配置为针对每个测试运行打开新的私密或隐匿浏览器窗口，请参阅 [Cookie 和站点数据](#cookies-and-site-data)部分。</span><span class="sxs-lookup"><span data-stu-id="43515-327">For more information on how to configure Visual Studio to open a new in-private or incognito browser window for each test run, see the [Cookies and site data](#cookies-and-site-data) section.</span></span>

<span data-ttu-id="43515-328">如果在 Azure 门户中更改了应用服务配置，则更新通常会快速生效，但不会立即生效。</span><span class="sxs-lookup"><span data-stu-id="43515-328">When App Service configuration is changed in the Azure portal, the updates generally take effect quickly but aren't instant.</span></span> <span data-ttu-id="43515-329">有时，必须等待一小段时间来让应用服务重新启动，配置更改才能生效。</span><span class="sxs-lookup"><span data-stu-id="43515-329">Sometimes, you must wait a short period for an App Service to restart in order for a configuration change to take effect.</span></span>

<span data-ttu-id="43515-330">若要对证书加载问题进行故障排除，请在 Azure 门户 [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell 命令外壳中执行以下命令。</span><span class="sxs-lookup"><span data-stu-id="43515-330">If troubleshooting a certificate loading problem, execute the following command in an Azure portal [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell command shell.</span></span> <span data-ttu-id="43515-331">该命令提供应用可在 `My` > `CurrentUser` 证书存储中访问的证书列表。</span><span class="sxs-lookup"><span data-stu-id="43515-331">The command provides a list of certificates that the app can access from the `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="43515-332">调试应用时，输出包括非常有用的证书使用者和指纹：</span><span class="sxs-lookup"><span data-stu-id="43515-332">The output includes certificate subjects and thumbprints useful when debugging an app:</span></span>

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="43515-333">其他资源</span><span class="sxs-lookup"><span data-stu-id="43515-333">Additional resources</span></span>

* [<span data-ttu-id="43515-334">部署到 Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="43515-334">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="43515-335">导入来自 Key Vault 的证书（Azure 文档）</span><span class="sxs-lookup"><span data-stu-id="43515-335">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="43515-336">使用安全默认客户端的应用中未经身份验证或未经授权的 Web API 请求</span><span class="sxs-lookup"><span data-stu-id="43515-336">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
