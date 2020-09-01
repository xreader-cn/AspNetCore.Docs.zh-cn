---
title: 使用 Identity 服务器保护托管 ASP.NET Core Blazor WebAssembly 应用
author: guardrex
description: 了解如何使用 Identity 服务器保护托管 ASP.NET Core Blazor WebAssembly 应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
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
ms.openlocfilehash: 58c21f4dbe831e99570ca8b0d7bc78616c1e5bfb
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712371"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a><span data-ttu-id="dac1d-103">使用 Identity 服务器保护 ASP.NET Core Blazor WebAssembly 托管应用</span><span class="sxs-lookup"><span data-stu-id="dac1d-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="dac1d-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dac1d-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="dac1d-105">本文介绍如何创建[托管的 Blazor WebAssembly 应用](xref:blazor/hosting-models#blazor-webassembly)，该应用使用 [IdentityServer](https://identityserver.io/) 以对用户和 API 调用进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="dac1d-105">This article explains how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="dac1d-106">若要将独立或托管 Blazor WebAssembly 应用配置为使用现有的外部 Identity 服务器实例，请按照 <xref:blazor/security/webassembly/standalone-with-authentication-library> 中的指南操作。</span><span class="sxs-lookup"><span data-stu-id="dac1d-106">To configure a standalone or hosted Blazor WebAssembly app to use an existing, external Identity Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dac1d-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dac1d-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dac1d-108">新建具有身份验证机制的 Blazor WebAssembly 项目：</span><span class="sxs-lookup"><span data-stu-id="dac1d-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="dac1d-109">在“新建 ASP.NET Core Web 应用”对话框中选择“Blazor WebAssembly应用”模板后，选择“身份验证”下的“更改”。</span><span class="sxs-lookup"><span data-stu-id="dac1d-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="dac1d-110">通过“存储应用内的用户帐户”选项选择“单个用户帐户”，以使用 ASP.NET Core 的 [Identity](xref:security/authentication/identity) 系统存储应用内的用户。 </span><span class="sxs-lookup"><span data-stu-id="dac1d-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="dac1d-111">在“高级”部分中选中“托管的 ASP.NET Core”复选框 。</span><span class="sxs-lookup"><span data-stu-id="dac1d-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="dac1d-112">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="dac1d-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="dac1d-113">若要在空文件夹中新建具有身份验证机制的 Blazor WebAssembly 项目，则通过 `-au|--auth` 选项指定 `Individual` 身份验证机制，以使用 ASP.NET Core 的 [Identity](xref:security/authentication/identity) 系统存储应用内的用户：</span><span class="sxs-lookup"><span data-stu-id="dac1d-113">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="dac1d-114">占位符</span><span class="sxs-lookup"><span data-stu-id="dac1d-114">Placeholder</span></span>  | <span data-ttu-id="dac1d-115">示例</span><span class="sxs-lookup"><span data-stu-id="dac1d-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="dac1d-116">使用 `-o|--output` 选项指定的输出位置将创建一个项目文件夹（如果该文件夹不存在）并成为应用程序名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="dac1d-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="dac1d-117">有关详细信息，请参阅 .NET Core 指南中的 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令。</span><span class="sxs-lookup"><span data-stu-id="dac1d-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dac1d-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dac1d-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dac1d-119">新建具有身份验证机制的 Blazor WebAssembly 项目：</span><span class="sxs-lookup"><span data-stu-id="dac1d-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="dac1d-120">在“配置新的 Blazor WebAssembly应用”步骤中，从“身份验证”下拉列表中选择“个人身份验证(应用内)”。</span><span class="sxs-lookup"><span data-stu-id="dac1d-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="dac1d-121">此应用是使用 ASP.NET Core [Identity](xref:security/authentication/identity) 为应用中存储的个人用户创建的。</span><span class="sxs-lookup"><span data-stu-id="dac1d-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="dac1d-122">选中“托管的 ASP.NET Core”复选框。</span><span class="sxs-lookup"><span data-stu-id="dac1d-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="dac1d-123">服务器应用配置</span><span class="sxs-lookup"><span data-stu-id="dac1d-123">Server app configuration</span></span>

<span data-ttu-id="dac1d-124">以下部分介绍了在包括身份验证支持的情况下对项目添加的内容。</span><span class="sxs-lookup"><span data-stu-id="dac1d-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="dac1d-125">Startup 类</span><span class="sxs-lookup"><span data-stu-id="dac1d-125">Startup class</span></span>

<span data-ttu-id="dac1d-126">`Startup` 类包含以下添加项。</span><span class="sxs-lookup"><span data-stu-id="dac1d-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="dac1d-127">在 `Startup.ConfigureServices`中：</span><span class="sxs-lookup"><span data-stu-id="dac1d-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="dac1d-128">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="dac1d-128">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="dac1d-129">包含附加 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 帮助器方法的 IdentityServer，该方法在 IdentityServer 之上设置默认 ASP.NET Core 约定：</span><span class="sxs-lookup"><span data-stu-id="dac1d-129">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="dac1d-130">包含附加 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 帮助器方法的身份验证，该方法将应用配置为验证由 IdentityServer 生成的 JWT 令牌：</span><span class="sxs-lookup"><span data-stu-id="dac1d-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="dac1d-131">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="dac1d-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="dac1d-132">IdentityServer 中间件公开 OpenID Connect (OIDC) 终结点：</span><span class="sxs-lookup"><span data-stu-id="dac1d-132">The IdentityServer middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="dac1d-133">身份验证中间件负责验证请求凭据并在请求上下文中设置用户：</span><span class="sxs-lookup"><span data-stu-id="dac1d-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="dac1d-134">授权中间件支持授权功能：</span><span class="sxs-lookup"><span data-stu-id="dac1d-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="dac1d-135">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="dac1d-135">AddApiAuthorization</span></span>

<span data-ttu-id="dac1d-136"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 帮助器方法配置 [IdentityServer](https://identityserver.io/) 用于 ASP.NET Core 方案。</span><span class="sxs-lookup"><span data-stu-id="dac1d-136">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="dac1d-137">IdentityServer 是一个功能强大且可扩展的框架，用于处理应用安全问题。</span><span class="sxs-lookup"><span data-stu-id="dac1d-137">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="dac1d-138">IdentityServer 公开大多数情况下不必要的复杂性。</span><span class="sxs-lookup"><span data-stu-id="dac1d-138">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="dac1d-139">因此，提供了一组约定和配置选项作为良好的起点。</span><span class="sxs-lookup"><span data-stu-id="dac1d-139">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="dac1d-140">一旦身份验证需要更改，就可使用 IdentityServer 的完整功能自定义身份验证以满足应用的要求。</span><span class="sxs-lookup"><span data-stu-id="dac1d-140">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="dac1d-141">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="dac1d-141">AddIdentityServerJwt</span></span>

<span data-ttu-id="dac1d-142"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 帮助器方法将应用的策略方案配置为默认身份验证处理程序。</span><span class="sxs-lookup"><span data-stu-id="dac1d-142">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="dac1d-143">该策略配置为允许 Identity 处理路由到 Identity URL 空间 `/Identity` 中任何子路径的所有请求。</span><span class="sxs-lookup"><span data-stu-id="dac1d-143">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="dac1d-144"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 处理所有其他请求。</span><span class="sxs-lookup"><span data-stu-id="dac1d-144">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="dac1d-145">此外，此方法还可以：</span><span class="sxs-lookup"><span data-stu-id="dac1d-145">Additionally, this method:</span></span>

* <span data-ttu-id="dac1d-146">向具有默认 `{APPLICATION NAME}API` 范围的 IdentityServer 注册 `{APPLICATION NAME}API` API 资源。</span><span class="sxs-lookup"><span data-stu-id="dac1d-146">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="dac1d-147">配置 JWT 持有者令牌中间件以验证 IdentityServer 为应用颁发的令牌。</span><span class="sxs-lookup"><span data-stu-id="dac1d-147">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="dac1d-148">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="dac1d-148">WeatherForecastController</span></span>

<span data-ttu-id="dac1d-149">在 `WeatherForecastController` (`Controllers/WeatherForecastController.cs`) 中，[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 特性应用于该类。</span><span class="sxs-lookup"><span data-stu-id="dac1d-149">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="dac1d-150">该属性指示用户必须根据默认策略获得授权才能访问资源。</span><span class="sxs-lookup"><span data-stu-id="dac1d-150">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="dac1d-151">默认授权策略配置为使用默认身份验证方案，由 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 设置。</span><span class="sxs-lookup"><span data-stu-id="dac1d-151">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="dac1d-152">帮助器方法将 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 配置为对应用的请求的默认处理程序。</span><span class="sxs-lookup"><span data-stu-id="dac1d-152">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="dac1d-153">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="dac1d-153">ApplicationDbContext</span></span>

<span data-ttu-id="dac1d-154">在 `ApplicationDbContext` (`Data/ApplicationDbContext.cs`) 中，<xref:Microsoft.EntityFrameworkCore.DbContext> 扩展 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> 以包括 IdentityServer 的架构。</span><span class="sxs-lookup"><span data-stu-id="dac1d-154">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="dac1d-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> 派生自 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>。</span><span class="sxs-lookup"><span data-stu-id="dac1d-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="dac1d-156">要获取对数据库架构的完全控制，请从其中一个可用的 Identity <xref:Microsoft.EntityFrameworkCore.DbContext> 类继承，并通过在 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> 方法中调用 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` 来配置上下文以包括 Identity 架构。</span><span class="sxs-lookup"><span data-stu-id="dac1d-156">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="dac1d-157">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="dac1d-157">OidcConfigurationController</span></span>

<span data-ttu-id="dac1d-158">在 `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`) 中，客户端终结点预配为提供 OIDC 参数。</span><span class="sxs-lookup"><span data-stu-id="dac1d-158">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="dac1d-159">应用设置</span><span class="sxs-lookup"><span data-stu-id="dac1d-159">App settings</span></span>

<span data-ttu-id="dac1d-160">在项目根目录的应用设置文件 (`appsettings.json`) 中，`IdentityServer` 部分描述已配置的客户端列表。</span><span class="sxs-lookup"><span data-stu-id="dac1d-160">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="dac1d-161">下例中存在一个客户端。</span><span class="sxs-lookup"><span data-stu-id="dac1d-161">In the following example, there's a single client.</span></span> <span data-ttu-id="dac1d-162">客户端名称对应于应用名称，并通过约定映射到 OAuth `ClientId` 参数。</span><span class="sxs-lookup"><span data-stu-id="dac1d-162">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="dac1d-163">配置文件指示正在配置的应用类型。</span><span class="sxs-lookup"><span data-stu-id="dac1d-163">The profile indicates the app type being configured.</span></span> <span data-ttu-id="dac1d-164">配置文件在内部用于促进简化服务器配置过程的约定。</span><span class="sxs-lookup"><span data-stu-id="dac1d-164">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="dac1d-165">占位符 `{APP ASSEMBLY}` 是应用的程序集名称（例如 `BlazorSample.Client`）。</span><span class="sxs-lookup"><span data-stu-id="dac1d-165">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="dac1d-166">客户端应用配置</span><span class="sxs-lookup"><span data-stu-id="dac1d-166">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="dac1d-167">身份验证包</span><span class="sxs-lookup"><span data-stu-id="dac1d-167">Authentication package</span></span>

<span data-ttu-id="dac1d-168">创建应用以使用个人用户帐户 (`Individual`) 时，该应用会在其项目文件中自动接收 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 包的包引用。</span><span class="sxs-lookup"><span data-stu-id="dac1d-168">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="dac1d-169">此包提供了一组基元，可帮助应用验证用户身份并获取令牌以调用受保护的 API。</span><span class="sxs-lookup"><span data-stu-id="dac1d-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="dac1d-170">如果向应用添加身份验证，请手动将包添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="dac1d-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="dac1d-171">对于占位符 `{VERSION}`，可在包的版本历史记录（位于 [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)）中找到与应用的共享框架版本匹配的最新稳定版本的包。</span><span class="sxs-lookup"><span data-stu-id="dac1d-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

### <a name="httpclient-configuration"></a><span data-ttu-id="dac1d-172">`HttpClient` 配置</span><span class="sxs-lookup"><span data-stu-id="dac1d-172">`HttpClient` configuration</span></span>

<span data-ttu-id="dac1d-173">在 `Program.Main` (`Program.cs`) 中，命名的 <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) 配置为提供 <xref:System.Net.Http.HttpClient> 实例，以在向服务器 API 发出请求时包含访问令牌：</span><span class="sxs-lookup"><span data-stu-id="dac1d-173">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="dac1d-174">若要将 Blazor WebAssembly 应用配置为使用不属于托管 Blazor 解决方案的现有 Identity 服务器实例，请将 <xref:System.Net.Http.HttpClient> 基址注册从 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) 更改为服务器应用的 API 授权终结点 URL。</span><span class="sxs-lookup"><span data-stu-id="dac1d-174">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a hosted Blazor solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="dac1d-175">API 身份验证支持</span><span class="sxs-lookup"><span data-stu-id="dac1d-175">API authorization support</span></span>

<span data-ttu-id="dac1d-176">使用 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 包中提供的扩展方法在服务容器中加入用户身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="dac1d-176">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="dac1d-177">此方法设置应用所需的服务以与现有授权系统交互。</span><span class="sxs-lookup"><span data-stu-id="dac1d-177">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="dac1d-178">默认情况下，应用的配置按约定从 `_configuration/{client-id}` 加载。</span><span class="sxs-lookup"><span data-stu-id="dac1d-178">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="dac1d-179">按照约定，客户端 ID 设置为应用的程序集名称。</span><span class="sxs-lookup"><span data-stu-id="dac1d-179">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="dac1d-180">可以通过使用选项调用重载来更改此 URL 以指向单独的终结点。</span><span class="sxs-lookup"><span data-stu-id="dac1d-180">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="dac1d-181">导入文件</span><span class="sxs-lookup"><span data-stu-id="dac1d-181">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="dac1d-182">索引页</span><span class="sxs-lookup"><span data-stu-id="dac1d-182">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="dac1d-183">应用组件</span><span class="sxs-lookup"><span data-stu-id="dac1d-183">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="dac1d-184">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="dac1d-184">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="dac1d-185">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="dac1d-185">LoginDisplay component</span></span>

<span data-ttu-id="dac1d-186">`LoginDisplay` 组件 (`Shared/LoginDisplay.razor`) 在 `MainLayout` 组件 (`Shared/MainLayout.razor`) 中呈现并管理以下行为：</span><span class="sxs-lookup"><span data-stu-id="dac1d-186">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="dac1d-187">对于经过身份验证的用户：</span><span class="sxs-lookup"><span data-stu-id="dac1d-187">For authenticated users:</span></span>
  * <span data-ttu-id="dac1d-188">显示当前用户名。</span><span class="sxs-lookup"><span data-stu-id="dac1d-188">Displays the current user name.</span></span>
  * <span data-ttu-id="dac1d-189">提供指向 ASP.NET Core Identity 中的用户配置文件页面的链接。</span><span class="sxs-lookup"><span data-stu-id="dac1d-189">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="dac1d-190">提供用于注销应用的按钮。</span><span class="sxs-lookup"><span data-stu-id="dac1d-190">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="dac1d-191">对于匿名用户：</span><span class="sxs-lookup"><span data-stu-id="dac1d-191">For anonymous users:</span></span>
  * <span data-ttu-id="dac1d-192">提供用于注册的选项。</span><span class="sxs-lookup"><span data-stu-id="dac1d-192">Offers the option to register.</span></span>
  * <span data-ttu-id="dac1d-193">提供用于登录的选项。</span><span class="sxs-lookup"><span data-stu-id="dac1d-193">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="dac1d-194">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="dac1d-194">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="dac1d-195">FetchData 组件</span><span class="sxs-lookup"><span data-stu-id="dac1d-195">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="dac1d-196">运行应用</span><span class="sxs-lookup"><span data-stu-id="dac1d-196">Run the app</span></span>

<span data-ttu-id="dac1d-197">从服务器项目运行应用。</span><span class="sxs-lookup"><span data-stu-id="dac1d-197">Run the app from the Server project.</span></span> <span data-ttu-id="dac1d-198">使用 Visual Studio 时，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="dac1d-198">When using Visual Studio, either:</span></span>

* <span data-ttu-id="dac1d-199">在工具栏中将“启动项目”下拉列表设置为“服务器 API 应用”，然后选择“运行”按钮。</span><span class="sxs-lookup"><span data-stu-id="dac1d-199">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="dac1d-200">在“解决方案资源管理器”中选择服务器项目，然后选择工具栏中的“运行”按钮，或从“调试”菜单启动应用  。</span><span class="sxs-lookup"><span data-stu-id="dac1d-200">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="dac1d-201">具有 API 授权的名称和角色声明</span><span class="sxs-lookup"><span data-stu-id="dac1d-201">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="dac1d-202">自定义用户工厂</span><span class="sxs-lookup"><span data-stu-id="dac1d-202">Custom user factory</span></span>

<span data-ttu-id="dac1d-203">在“客户端”应用中，创建自定义用户工厂。</span><span class="sxs-lookup"><span data-stu-id="dac1d-203">In the Client app, create a custom user factory.</span></span> <span data-ttu-id="dac1d-204">Identity 服务器在一个 `role` 声明中发送多个角色作为 JSON 数组。</span><span class="sxs-lookup"><span data-stu-id="dac1d-204">Identity Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="dac1d-205">单个角色在该声明中作为单个字符串值进行发送。</span><span class="sxs-lookup"><span data-stu-id="dac1d-205">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="dac1d-206">工厂为每个用户的角色创建单个 `role` 声明。</span><span class="sxs-lookup"><span data-stu-id="dac1d-206">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="dac1d-207">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="dac1d-207">`CustomUserFactory.cs`:</span></span>

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
            var roleClaims = identity.FindAll(identity.RoleClaimType);

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

<span data-ttu-id="dac1d-208">在客户端应用中，在 `Program.Main` (`Program.cs`) 中注册工厂：</span><span class="sxs-lookup"><span data-stu-id="dac1d-208">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="dac1d-209">在服务器应用中，调用 Identity 生成器上的 <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*>，添加与角色相关的服务：</span><span class="sxs-lookup"><span data-stu-id="dac1d-209">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a><span data-ttu-id="dac1d-210">配置 Identity 服务器</span><span class="sxs-lookup"><span data-stu-id="dac1d-210">Configure Identity Server</span></span>

<span data-ttu-id="dac1d-211">使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="dac1d-211">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="dac1d-212">API 身份验证选项</span><span class="sxs-lookup"><span data-stu-id="dac1d-212">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="dac1d-213">配置文件服务</span><span class="sxs-lookup"><span data-stu-id="dac1d-213">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="dac1d-214">API 身份验证选项</span><span class="sxs-lookup"><span data-stu-id="dac1d-214">API authorization options</span></span>

<span data-ttu-id="dac1d-215">在服务器应用中：</span><span class="sxs-lookup"><span data-stu-id="dac1d-215">In the Server app:</span></span>

* <span data-ttu-id="dac1d-216">配置 Identity 服务器，将 `name` 和 `role` 声明放入 ID 令牌和访问令牌中。</span><span class="sxs-lookup"><span data-stu-id="dac1d-216">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="dac1d-217">阻止 JWT 令牌处理程序中角色的默认映射。</span><span class="sxs-lookup"><span data-stu-id="dac1d-217">Prevent the default mapping for roles in the JWT token handler.</span></span>

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

#### <a name="profile-service"></a><span data-ttu-id="dac1d-218">配置文件服务</span><span class="sxs-lookup"><span data-stu-id="dac1d-218">Profile Service</span></span>

<span data-ttu-id="dac1d-219">在服务器应用中，创建 `ProfileService` 实现。</span><span class="sxs-lookup"><span data-stu-id="dac1d-219">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="dac1d-220">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="dac1d-220">`ProfileService.cs`:</span></span>

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

<span data-ttu-id="dac1d-221">在服务器应用中，在 `Startup.ConfigureServices` 中注册配置文件服务：</span><span class="sxs-lookup"><span data-stu-id="dac1d-221">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="dac1d-222">使用授权机制</span><span class="sxs-lookup"><span data-stu-id="dac1d-222">Use authorization mechanisms</span></span>

<span data-ttu-id="dac1d-223">在客户端应用中，组件授权方法此时有效。</span><span class="sxs-lookup"><span data-stu-id="dac1d-223">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="dac1d-224">组件中的任何授权机制都可以使用角色来授权用户：</span><span class="sxs-lookup"><span data-stu-id="dac1d-224">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="dac1d-225">[`AuthorizeView` 组件](xref:blazor/security/index#authorizeview-component)（例如：`<AuthorizeView Roles="admin">`）</span><span class="sxs-lookup"><span data-stu-id="dac1d-225">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="dac1d-226">[`[Authorize]` 属性指令](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)（例如 `@attribute [Authorize(Roles = "admin")]`）</span><span class="sxs-lookup"><span data-stu-id="dac1d-226">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="dac1d-227">[过程逻辑](xref:blazor/security/index#procedural-logic)（例如 `if (user.IsInRole("admin")) { ... }`）</span><span class="sxs-lookup"><span data-stu-id="dac1d-227">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="dac1d-228">支持多个角色测试：</span><span class="sxs-lookup"><span data-stu-id="dac1d-228">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="dac1d-229">`User.Identity.Name` 在客户端应用中进行填充，并带有用户的用户名，这通常是他们的登录电子邮件地址。</span><span class="sxs-lookup"><span data-stu-id="dac1d-229">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="dac1d-230">其他资源</span><span class="sxs-lookup"><span data-stu-id="dac1d-230">Additional resources</span></span>

* [<span data-ttu-id="dac1d-231">部署到 Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="dac1d-231">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="dac1d-232">导入来自 Key Vault 的证书（Azure 文档）</span><span class="sxs-lookup"><span data-stu-id="dac1d-232">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="dac1d-233">使用安全默认客户端的应用中未经身份验证或未经授权的 Web API 请求</span><span class="sxs-lookup"><span data-stu-id="dac1d-233">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
