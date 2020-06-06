---
title: Blazor使用服务器保护 ASP.NET Core WebAssembly 的托管应用 Identity
author: guardrex
description: Blazor使用[IdentityServer](https://identityserver.io/)后端在 Visual Studio 中创建具有身份验证的新托管应用程序
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: ade2d88c6a2d59e169c9019e871982a74ae46b33
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452312"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="68b69-103">Blazor使用服务器保护 ASP.NET Core WebAssembly 的托管应用 Identity</span><span class="sxs-lookup"><span data-stu-id="68b69-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="68b69-104">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="68b69-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="68b69-105">本文介绍如何创建新 Blazor 的托管应用，该应用使用[IdentityServer](https://identityserver.io/)对用户和 API 调用进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="68b69-105">This article explains how to create a new Blazor hosted app that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="68b69-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="68b69-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="68b69-107">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="68b69-107">In Visual Studio:</span></span>

1. <span data-ttu-id="68b69-108">创建新的\*\* Blazor WebAssembly\*\*应用。</span><span class="sxs-lookup"><span data-stu-id="68b69-108">Create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="68b69-109">有关详细信息，请参阅 <xref:blazor/get-started>。</span><span class="sxs-lookup"><span data-stu-id="68b69-109">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="68b69-110">在 "**创建新 Blazor 应用**" 对话框中，在 "**身份验证**" 部分中选择 "**更改**"。</span><span class="sxs-lookup"><span data-stu-id="68b69-110">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="68b69-111">选择后跟 **"确定"** 的**单个用户帐户**。</span><span class="sxs-lookup"><span data-stu-id="68b69-111">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="68b69-112">选中 "**高级**" 部分中的 " **ASP.NET Core 托管**" 复选框。</span><span class="sxs-lookup"><span data-stu-id="68b69-112">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="68b69-113">选择“创建”\*\*\*\* 按钮。</span><span class="sxs-lookup"><span data-stu-id="68b69-113">Select the **Create** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="68b69-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="68b69-114">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="68b69-115">若要在命令外壳中创建应用，请执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="68b69-115">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="68b69-116">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如， `-o BlazorSample` ）。</span><span class="sxs-lookup"><span data-stu-id="68b69-116">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="68b69-117">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="68b69-117">The folder name also becomes part of the project's name.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="68b69-118">服务器应用配置</span><span class="sxs-lookup"><span data-stu-id="68b69-118">Server app configuration</span></span>

<span data-ttu-id="68b69-119">以下各节介绍了在包括身份验证支持时对项目的添加。</span><span class="sxs-lookup"><span data-stu-id="68b69-119">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="68b69-120">Startup 类</span><span class="sxs-lookup"><span data-stu-id="68b69-120">Startup class</span></span>

<span data-ttu-id="68b69-121">`Startup`类添加了以下内容。</span><span class="sxs-lookup"><span data-stu-id="68b69-121">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="68b69-122">在 `Startup.ConfigureServices`中：</span><span class="sxs-lookup"><span data-stu-id="68b69-122">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="68b69-123">ASP.NET Core Identity ：</span><span class="sxs-lookup"><span data-stu-id="68b69-123">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="68b69-124">IdentityServer，另外还提供了一个用于 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 在 IdentityServer 上设置默认 ASP.NET Core 约定的帮助器方法：</span><span class="sxs-lookup"><span data-stu-id="68b69-124">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="68b69-125">使用其他 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 帮助器方法进行身份验证，该方法将应用程序配置为验证 IdentityServer 生成的 JWT 令牌：</span><span class="sxs-lookup"><span data-stu-id="68b69-125">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="68b69-126">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="68b69-126">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="68b69-127">IdentityServer 中间件公开 Open ID Connect （OIDC）终结点：</span><span class="sxs-lookup"><span data-stu-id="68b69-127">The IdentityServer middleware exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="68b69-128">身份验证中间件负责验证请求凭据并在请求上下文中设置用户：</span><span class="sxs-lookup"><span data-stu-id="68b69-128">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="68b69-129">授权中间件启用授权功能：</span><span class="sxs-lookup"><span data-stu-id="68b69-129">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="68b69-130">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="68b69-130">AddApiAuthorization</span></span>

<span data-ttu-id="68b69-131"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Helper 方法为 ASP.NET Core 情况配置[IdentityServer](https://identityserver.io/) 。</span><span class="sxs-lookup"><span data-stu-id="68b69-131">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="68b69-132">IdentityServer 是一个功能强大且可扩展的框架，用于处理应用安全问题。</span><span class="sxs-lookup"><span data-stu-id="68b69-132">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="68b69-133">在最常见的情况下，IdentityServer 会造成不必要的复杂性。</span><span class="sxs-lookup"><span data-stu-id="68b69-133">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="68b69-134">因此，我们考虑到了一个很好的起点。</span><span class="sxs-lookup"><span data-stu-id="68b69-134">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="68b69-135">身份验证需要更改后，IdentityServer 的全部功能可用于自定义身份验证，以满足应用的要求。</span><span class="sxs-lookup"><span data-stu-id="68b69-135">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="68b69-136">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="68b69-136">AddIdentityServerJwt</span></span>

<span data-ttu-id="68b69-137"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Helper 方法为应用配置策略方案作为默认身份验证处理程序。</span><span class="sxs-lookup"><span data-stu-id="68b69-137">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="68b69-138">此策略配置为允许 Identity 处理所有路由到 URL 空间中的子路径的请求 Identity `/Identity` 。</span><span class="sxs-lookup"><span data-stu-id="68b69-138">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="68b69-139"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>处理所有其他请求。</span><span class="sxs-lookup"><span data-stu-id="68b69-139">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="68b69-140">此外，此方法：</span><span class="sxs-lookup"><span data-stu-id="68b69-140">Additionally, this method:</span></span>

* <span data-ttu-id="68b69-141">使用 `{APPLICATION NAME}API` IdentityServer 的默认范围注册 API 资源 `{APPLICATION NAME}API` 。</span><span class="sxs-lookup"><span data-stu-id="68b69-141">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="68b69-142">将 JWT 持有者令牌中间件配置为验证 IdentityServer 为应用程序颁发的令牌。</span><span class="sxs-lookup"><span data-stu-id="68b69-142">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="68b69-143">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="68b69-143">WeatherForecastController</span></span>

<span data-ttu-id="68b69-144">在 `WeatherForecastController` （*控制器/WeatherForecastController*）中， [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 特性应用于类。</span><span class="sxs-lookup"><span data-stu-id="68b69-144">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="68b69-145">属性指示用户必须根据默认策略进行授权才能访问资源。</span><span class="sxs-lookup"><span data-stu-id="68b69-145">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="68b69-146">默认授权策略配置为使用由设置的默认身份验证方案 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 。</span><span class="sxs-lookup"><span data-stu-id="68b69-146">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="68b69-147">Helper 方法将配置 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 为应用程序请求的默认处理程序。</span><span class="sxs-lookup"><span data-stu-id="68b69-147">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="68b69-148">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="68b69-148">ApplicationDbContext</span></span>

<span data-ttu-id="68b69-149">在 `ApplicationDbContext` （*Data/ApplicationDbContext*）中， <xref:Microsoft.EntityFrameworkCore.DbContext> 扩展 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> 以包括 IdentityServer 的架构。</span><span class="sxs-lookup"><span data-stu-id="68b69-149">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="68b69-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> 派生自 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>。</span><span class="sxs-lookup"><span data-stu-id="68b69-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="68b69-151">若要完全控制数据库架构，请从某个可用的类继承， Identity <xref:Microsoft.EntityFrameworkCore.DbContext> 并 Identity 通过 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` 在方法中调用来配置上下文以包含架构 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> 。</span><span class="sxs-lookup"><span data-stu-id="68b69-151">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="68b69-152">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="68b69-152">OidcConfigurationController</span></span>

<span data-ttu-id="68b69-153">在 `OidcConfigurationController` （controller */OidcConfigurationController*）中，客户端终结点预配为提供 OIDC 参数。</span><span class="sxs-lookup"><span data-stu-id="68b69-153">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="68b69-154">应用设置文件</span><span class="sxs-lookup"><span data-stu-id="68b69-154">App settings files</span></span>

<span data-ttu-id="68b69-155">在项目根目录下的应用设置文件（*appsettings*）中， `IdentityServer` 部分描述了已配置的客户端的列表。</span><span class="sxs-lookup"><span data-stu-id="68b69-155">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="68b69-156">在下面的示例中，有一个客户端。</span><span class="sxs-lookup"><span data-stu-id="68b69-156">In the following example, there's a single client.</span></span> <span data-ttu-id="68b69-157">客户端名称对应于应用名称，并按约定映射到 OAuth `ClientId` 参数。</span><span class="sxs-lookup"><span data-stu-id="68b69-157">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="68b69-158">配置文件指示正在配置的应用类型。</span><span class="sxs-lookup"><span data-stu-id="68b69-158">The profile indicates the app type being configured.</span></span> <span data-ttu-id="68b69-159">此配置文件可在内部使用，以促进简化服务器配置过程的约定。</span><span class="sxs-lookup"><span data-stu-id="68b69-159">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="68b69-160">客户端应用配置</span><span class="sxs-lookup"><span data-stu-id="68b69-160">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="68b69-161">身份验证包</span><span class="sxs-lookup"><span data-stu-id="68b69-161">Authentication package</span></span>

<span data-ttu-id="68b69-162">当创建应用以使用单个用户帐户（）时 `Individual` ，应用会自动在应用的项目文件中接收[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)包的包引用。</span><span class="sxs-lookup"><span data-stu-id="68b69-162">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="68b69-163">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="68b69-163">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="68b69-164">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="68b69-164">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a><span data-ttu-id="68b69-165">API 授权支持</span><span class="sxs-lookup"><span data-stu-id="68b69-165">API authorization support</span></span>

<span data-ttu-id="68b69-166">对用户进行身份验证的支持通过[WebAssembly](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)包中提供的扩展方法插入到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="68b69-166">The support for authenticating users is plugged into the service container by the extension method provided inside the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="68b69-167">此方法设置应用程序所需的服务以与现有授权系统交互。</span><span class="sxs-lookup"><span data-stu-id="68b69-167">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="68b69-168">默认情况下，应用的配置由中的约定加载 `_configuration/{client-id}` 。</span><span class="sxs-lookup"><span data-stu-id="68b69-168">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="68b69-169">按照约定，将客户端 ID 设置为应用的程序集名称。</span><span class="sxs-lookup"><span data-stu-id="68b69-169">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="68b69-170">可以通过使用选项调用重载，将此 URL 更改为指向不同的终结点。</span><span class="sxs-lookup"><span data-stu-id="68b69-170">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="68b69-171">导入文件</span><span class="sxs-lookup"><span data-stu-id="68b69-171">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="68b69-172">索引页面</span><span class="sxs-lookup"><span data-stu-id="68b69-172">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="68b69-173">应用组件</span><span class="sxs-lookup"><span data-stu-id="68b69-173">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="68b69-174">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="68b69-174">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="68b69-175">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="68b69-175">LoginDisplay component</span></span>

<span data-ttu-id="68b69-176">`LoginDisplay`组件（*Shared/LoginDisplay*）在 `MainLayout` 组件（*shared/MainLayout*）中呈现并管理以下行为：</span><span class="sxs-lookup"><span data-stu-id="68b69-176">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="68b69-177">对于经过身份验证的用户：</span><span class="sxs-lookup"><span data-stu-id="68b69-177">For authenticated users:</span></span>
  * <span data-ttu-id="68b69-178">显示当前用户名。</span><span class="sxs-lookup"><span data-stu-id="68b69-178">Displays the current user name.</span></span>
  * <span data-ttu-id="68b69-179">提供 ASP.NET Core 中用户配置文件页的链接 Identity 。</span><span class="sxs-lookup"><span data-stu-id="68b69-179">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="68b69-180">提供用于注销应用的按钮。</span><span class="sxs-lookup"><span data-stu-id="68b69-180">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="68b69-181">对于匿名用户：</span><span class="sxs-lookup"><span data-stu-id="68b69-181">For anonymous users:</span></span>
  * <span data-ttu-id="68b69-182">提供注册的选项。</span><span class="sxs-lookup"><span data-stu-id="68b69-182">Offers the option to register.</span></span>
  * <span data-ttu-id="68b69-183">提供用于登录的选项。</span><span class="sxs-lookup"><span data-stu-id="68b69-183">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="68b69-184">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="68b69-184">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="68b69-185">FetchData 组件</span><span class="sxs-lookup"><span data-stu-id="68b69-185">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="68b69-186">运行应用</span><span class="sxs-lookup"><span data-stu-id="68b69-186">Run the app</span></span>

<span data-ttu-id="68b69-187">从服务器项目运行应用。</span><span class="sxs-lookup"><span data-stu-id="68b69-187">Run the app from the Server project.</span></span> <span data-ttu-id="68b69-188">使用 Visual Studio 时，可以执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="68b69-188">When using Visual Studio, either:</span></span>

* <span data-ttu-id="68b69-189">将工具栏中的 "**启动项目**" 下拉列表设置为 "*服务器 API 应用*"，然后选择 "**运行**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="68b69-189">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="68b69-190">在**解决方案资源管理器**中选择服务器项目，并在工具栏中选择 "**运行**" 按钮，或从 "**调试**" 菜单中启动该应用程序。</span><span class="sxs-lookup"><span data-stu-id="68b69-190">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="68b69-191">具有 API 授权的名称和角色声明</span><span class="sxs-lookup"><span data-stu-id="68b69-191">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="68b69-192">自定义用户工厂</span><span class="sxs-lookup"><span data-stu-id="68b69-192">Custom user factory</span></span>

<span data-ttu-id="68b69-193">在客户端应用中，创建自定义用户工厂。</span><span class="sxs-lookup"><span data-stu-id="68b69-193">In the Client app, create a custom user factory.</span></span> Identity<span data-ttu-id="68b69-194">服务器在一个声明中以 JSON 数组的形式发送多个角色 `role` 。</span><span class="sxs-lookup"><span data-stu-id="68b69-194"> Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="68b69-195">在声明中以字符串值的形式发送单个角色。</span><span class="sxs-lookup"><span data-stu-id="68b69-195">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="68b69-196">工厂 `role` 为用户的每个角色创建单个声明。</span><span class="sxs-lookup"><span data-stu-id="68b69-196">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="68b69-197">*CustomUserFactory.cs*：</span><span class="sxs-lookup"><span data-stu-id="68b69-197">*CustomUserFactory.cs*:</span></span>

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

<span data-ttu-id="68b69-198">在客户端应用程序中，注册工厂 `Program.Main` （*Program.cs*）：</span><span class="sxs-lookup"><span data-stu-id="68b69-198">In the Client app, register the factory in `Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="68b69-199">在服务器应用中，在 <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> 生成器上调用 Identity ，这会添加与角色相关的服务：</span><span class="sxs-lookup"><span data-stu-id="68b69-199">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="68b69-200">配置 Identity 服务器</span><span class="sxs-lookup"><span data-stu-id="68b69-200">Configure Identity Server</span></span>

<span data-ttu-id="68b69-201">使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="68b69-201">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="68b69-202">API 授权选项</span><span class="sxs-lookup"><span data-stu-id="68b69-202">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="68b69-203">配置文件服务</span><span class="sxs-lookup"><span data-stu-id="68b69-203">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="68b69-204">API 授权选项</span><span class="sxs-lookup"><span data-stu-id="68b69-204">API authorization options</span></span>

<span data-ttu-id="68b69-205">在服务器应用中：</span><span class="sxs-lookup"><span data-stu-id="68b69-205">In the Server app:</span></span>

* <span data-ttu-id="68b69-206">将 Identity 服务器配置为将 `name` 和 `role` 声明放入 ID 令牌和访问令牌。</span><span class="sxs-lookup"><span data-stu-id="68b69-206">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="68b69-207">阻止 JWT 标记处理程序中角色的默认映射。</span><span class="sxs-lookup"><span data-stu-id="68b69-207">Prevent the default mapping for roles in the JWT token handler.</span></span>

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

#### <a name="profile-service"></a><span data-ttu-id="68b69-208">配置文件服务</span><span class="sxs-lookup"><span data-stu-id="68b69-208">Profile Service</span></span>

<span data-ttu-id="68b69-209">在服务器应用中，创建一个 `ProfileService` 实现。</span><span class="sxs-lookup"><span data-stu-id="68b69-209">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="68b69-210">*ProfileService.cs*：</span><span class="sxs-lookup"><span data-stu-id="68b69-210">*ProfileService.cs*:</span></span>

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

<span data-ttu-id="68b69-211">在服务器应用中，在中注册配置文件服务 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="68b69-211">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="68b69-212">使用授权机制</span><span class="sxs-lookup"><span data-stu-id="68b69-212">Use authorization mechanisms</span></span>

<span data-ttu-id="68b69-213">在客户端应用程序中，组件授权方法此时可以正常工作。</span><span class="sxs-lookup"><span data-stu-id="68b69-213">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="68b69-214">组件中的任何授权机制都可以使用角色来授权用户：</span><span class="sxs-lookup"><span data-stu-id="68b69-214">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="68b69-215">[AuthorizeView 组件](xref:security/blazor/index#authorizeview-component)（示例： `<AuthorizeView Roles="admin">` ）</span><span class="sxs-lookup"><span data-stu-id="68b69-215">[AuthorizeView component](xref:security/blazor/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="68b69-216">[ `[Authorize]` attribute 指令](xref:security/blazor/index#authorize-attribute)（ <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ）（示例： `@attribute [Authorize(Roles = "admin")]` ）</span><span class="sxs-lookup"><span data-stu-id="68b69-216">[`[Authorize]` attribute directive](xref:security/blazor/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="68b69-217">[过程逻辑](xref:security/blazor/index#procedural-logic)（示例： `if (user.IsInRole("admin")) { ... }` ）</span><span class="sxs-lookup"><span data-stu-id="68b69-217">[Procedural logic](xref:security/blazor/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="68b69-218">支持多个角色测试：</span><span class="sxs-lookup"><span data-stu-id="68b69-218">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="68b69-219">`User.Identity.Name`在客户端应用程序中填充用户的用户名，该用户名通常为登录电子邮件地址。</span><span class="sxs-lookup"><span data-stu-id="68b69-219">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="68b69-220">其他资源</span><span class="sxs-lookup"><span data-stu-id="68b69-220">Additional resources</span></span>

* [<span data-ttu-id="68b69-221">部署到 Azure App Service</span><span class="sxs-lookup"><span data-stu-id="68b69-221">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="68b69-222">从 Key Vault 导入证书（Azure 文档）</span><span class="sxs-lookup"><span data-stu-id="68b69-222">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="68b69-223">使用安全的默认客户端的应用中未经身份验证或未授权的 web API 请求</span><span class="sxs-lookup"><span data-stu-id="68b69-223">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
