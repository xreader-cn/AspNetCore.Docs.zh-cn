---
title: 使用标识服务器保护BlazorASP.NET核心 Web 组件托管应用
author: guardrex
description: 使用[标识服务器](https://identityserver.io/)后端Blazor的 Visual Studio 中使用身份验证创建新托管应用
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 4c51200159ced16132e15bb4a1f0915ca0cf5945
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791621"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="90baa-103">使用标识服务器保护BlazorASP.NET核心 Web 组件托管应用</span><span class="sxs-lookup"><span data-stu-id="90baa-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="90baa-104">哈维尔[·卡尔瓦罗·纳尔逊](https://github.com/javiercn)和[卢克·莱瑟姆](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="90baa-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="90baa-105">要在 Visual Blazor Studio 中创建新托管应用，使用[标识服务器](https://identityserver.io/)对用户和 API 调用进行身份验证，</span><span class="sxs-lookup"><span data-stu-id="90baa-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="90baa-106">使用可视化工作室创建新的**BlazorWeb 组装**应用。</span><span class="sxs-lookup"><span data-stu-id="90baa-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="90baa-107">有关详细信息，请参阅 <xref:blazor/get-started>。</span><span class="sxs-lookup"><span data-stu-id="90baa-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="90baa-108">在"**创建新Blazor应用"** 对话框中，在 **"身份验证**"部分中选择 **"更改**"。</span><span class="sxs-lookup"><span data-stu-id="90baa-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="90baa-109">选择**单个用户帐户**后跟 **"确定**"。</span><span class="sxs-lookup"><span data-stu-id="90baa-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="90baa-110">在 **"高级"** 部分中选择 **"ASP.NET核心托管**复选框。</span><span class="sxs-lookup"><span data-stu-id="90baa-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="90baa-111">选择“创建”\*\*\*\* 按钮。</span><span class="sxs-lookup"><span data-stu-id="90baa-111">Select the **Create** button.</span></span>

<span data-ttu-id="90baa-112">要在命令外壳中创建应用，请执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="90baa-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="90baa-113">要指定输出位置（如果不存在，则创建项目文件夹）请在命令中包含具有路径的输出选项（例如。 `-o BlazorSample`</span><span class="sxs-lookup"><span data-stu-id="90baa-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="90baa-114">文件夹名称也将成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="90baa-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="90baa-115">服务器应用配置</span><span class="sxs-lookup"><span data-stu-id="90baa-115">Server app configuration</span></span>

<span data-ttu-id="90baa-116">以下各节介绍在包含身份验证支持时对项目的补充。</span><span class="sxs-lookup"><span data-stu-id="90baa-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="90baa-117">Startup 类</span><span class="sxs-lookup"><span data-stu-id="90baa-117">Startup class</span></span>

<span data-ttu-id="90baa-118">该`Startup`类具有以下添加功能：</span><span class="sxs-lookup"><span data-stu-id="90baa-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="90baa-119">在 `Startup.ConfigureServices`中：</span><span class="sxs-lookup"><span data-stu-id="90baa-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="90baa-120">标识：</span><span class="sxs-lookup"><span data-stu-id="90baa-120">Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="90baa-121">标识服务器具有附加<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>帮助器方法，在标识服务器顶部设置一些默认ASP.NET核心约定：</span><span class="sxs-lookup"><span data-stu-id="90baa-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="90baa-122">使用其他<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>帮助程序方法进行身份验证，该方法将应用配置为验证 IdentityServer 生成的 JWT 令牌：</span><span class="sxs-lookup"><span data-stu-id="90baa-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="90baa-123">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="90baa-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="90baa-124">负责验证请求凭据并在请求上下文中设置用户的身份验证中间件：</span><span class="sxs-lookup"><span data-stu-id="90baa-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="90baa-125">公开打开 ID 连接 （OIDC） 终结点的标识服务器中间件：</span><span class="sxs-lookup"><span data-stu-id="90baa-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="90baa-126">添加 Api 授权</span><span class="sxs-lookup"><span data-stu-id="90baa-126">AddApiAuthorization</span></span>

<span data-ttu-id="90baa-127"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>帮助器方法为ASP.NET核心方案配置[标识服务器](https://identityserver.io/)。</span><span class="sxs-lookup"><span data-stu-id="90baa-127">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="90baa-128">IdentityServer 是一个功能强大且可扩展的框架，用于处理应用安全问题。</span><span class="sxs-lookup"><span data-stu-id="90baa-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="90baa-129">标识服务器会为最常见的方案公开不必要的复杂性。</span><span class="sxs-lookup"><span data-stu-id="90baa-129">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="90baa-130">因此，提供了一组约定和配置选项，我们认为这是一个良好的起点。</span><span class="sxs-lookup"><span data-stu-id="90baa-130">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="90baa-131">一旦身份验证需要更改，身份服务器的全部功能仍可用于自定义身份验证以满足应用的要求。</span><span class="sxs-lookup"><span data-stu-id="90baa-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="90baa-132">添加身份服务器Jwt</span><span class="sxs-lookup"><span data-stu-id="90baa-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="90baa-133"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>帮助程序方法将应用的策略方案配置为默认身份验证处理程序。</span><span class="sxs-lookup"><span data-stu-id="90baa-133">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="90baa-134">该策略配置为允许标识处理路由到标识 URL 空间`/Identity`中的任何子路径的所有请求。</span><span class="sxs-lookup"><span data-stu-id="90baa-134">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="90baa-135">处理<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>所有其他请求。</span><span class="sxs-lookup"><span data-stu-id="90baa-135">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="90baa-136">此外，此方法：</span><span class="sxs-lookup"><span data-stu-id="90baa-136">Additionally, this method:</span></span>

* <span data-ttu-id="90baa-137">将`{APPLICATION NAME}API`API 资源注册为默认作用域 的`{APPLICATION NAME}API`标识服务器。</span><span class="sxs-lookup"><span data-stu-id="90baa-137">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="90baa-138">配置 JWT 承载令牌中间件以验证 IdentityServer 为应用颁发的令牌。</span><span class="sxs-lookup"><span data-stu-id="90baa-138">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="90baa-139">天气预报控制器</span><span class="sxs-lookup"><span data-stu-id="90baa-139">WeatherForecastController</span></span>

<span data-ttu-id="90baa-140">在`WeatherForecastController`（*控制器/天气预报控制器.cs*）[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)中，该属性应用于类。</span><span class="sxs-lookup"><span data-stu-id="90baa-140">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="90baa-141">该属性指示必须根据默认策略授权用户才能访问资源。</span><span class="sxs-lookup"><span data-stu-id="90baa-141">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="90baa-142">默认授权策略配置为使用默认身份验证方案，该方案由<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>前面提到的策略方案设置。</span><span class="sxs-lookup"><span data-stu-id="90baa-142">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="90baa-143">帮助程序方法<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>配置为对应用的请求的默认处理程序。</span><span class="sxs-lookup"><span data-stu-id="90baa-143">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="90baa-144">应用程序数据库上下文</span><span class="sxs-lookup"><span data-stu-id="90baa-144">ApplicationDbContext</span></span>

<span data-ttu-id="90baa-145">在`ApplicationDbContext`（*数据/应用程序DbContext.cs）* 中<xref:Microsoft.EntityFrameworkCore.DbContext>，标识中使用相同的，但扩展<xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>为包括标识服务器的架构除外。</span><span class="sxs-lookup"><span data-stu-id="90baa-145">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="90baa-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> 派生自 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>。</span><span class="sxs-lookup"><span data-stu-id="90baa-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="90baa-147">要完全控制数据库架构，请从其中一个可用的标识<xref:Microsoft.EntityFrameworkCore.DbContext>类继承，并通过调用`builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)``OnModelCreating`方法将上下文配置为包括标识架构。</span><span class="sxs-lookup"><span data-stu-id="90baa-147">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="90baa-148">Oidc配置控制器</span><span class="sxs-lookup"><span data-stu-id="90baa-148">OidcConfigurationController</span></span>

<span data-ttu-id="90baa-149">在`OidcConfigurationController`（*控制器/Oidc配置控制器.cs*） 中，客户端终结点被预配以服务 OIDC 参数。</span><span class="sxs-lookup"><span data-stu-id="90baa-149">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="90baa-150">应用设置文件</span><span class="sxs-lookup"><span data-stu-id="90baa-150">App settings files</span></span>

<span data-ttu-id="90baa-151">在项目根部的应用设置文件 （*appsettings.json*）`IdentityServer`中，该部分描述已配置的客户端的列表。</span><span class="sxs-lookup"><span data-stu-id="90baa-151">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="90baa-152">在下面的示例中，只有一个客户端。</span><span class="sxs-lookup"><span data-stu-id="90baa-152">In the following example, there's a single client.</span></span> <span data-ttu-id="90baa-153">客户端名称对应于应用名称，并通过约定映射到 OAuth`ClientId`参数。</span><span class="sxs-lookup"><span data-stu-id="90baa-153">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="90baa-154">配置文件指示正在配置的应用类型。</span><span class="sxs-lookup"><span data-stu-id="90baa-154">The profile indicates the app type being configured.</span></span> <span data-ttu-id="90baa-155">配置文件在内部用于驱动简化服务器配置过程的约定。</span><span class="sxs-lookup"><span data-stu-id="90baa-155">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="90baa-156">在开发环境应用设置文件中（*应用设置）。在项目根目录的开发.json*中`IdentityServer`，该部分描述了用于对令牌进行签名的键。</span><span class="sxs-lookup"><span data-stu-id="90baa-156">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="90baa-157">客户端应用配置</span><span class="sxs-lookup"><span data-stu-id="90baa-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="90baa-158">身份验证包</span><span class="sxs-lookup"><span data-stu-id="90baa-158">Authentication package</span></span>

<span data-ttu-id="90baa-159">创建应用以使用个人用户帐户 （）`Individual`时，应用会自动在应用的项目文件中接收`Microsoft.AspNetCore.Components.WebAssembly.Authentication`包的包引用。</span><span class="sxs-lookup"><span data-stu-id="90baa-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="90baa-160">该包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌来调用受保护的 API。</span><span class="sxs-lookup"><span data-stu-id="90baa-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="90baa-161">如果向应用添加身份验证，则手动将包添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="90baa-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="90baa-162">在前面`{VERSION}`的包引用中替换为`Microsoft.AspNetCore.Blazor.Templates`<xref:blazor/get-started>本文中显示的包版本。</span><span class="sxs-lookup"><span data-stu-id="90baa-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="90baa-163">API 授权支持</span><span class="sxs-lookup"><span data-stu-id="90baa-163">API authorization support</span></span>

<span data-ttu-id="90baa-164">通过`Microsoft.AspNetCore.Components.WebAssembly.Authentication`包内提供的扩展方法将用户身份验证支持插入服务容器。</span><span class="sxs-lookup"><span data-stu-id="90baa-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="90baa-165">此方法设置应用与现有授权系统交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="90baa-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="90baa-166">默认情况下，它按约定从`_configuration/{client-id}`加载应用的配置。</span><span class="sxs-lookup"><span data-stu-id="90baa-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="90baa-167">按照惯例，客户端 ID 设置为应用的程序集名称。</span><span class="sxs-lookup"><span data-stu-id="90baa-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="90baa-168">可以通过使用选项调用重载来更改此 URL 以指向单独的终结点。</span><span class="sxs-lookup"><span data-stu-id="90baa-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="90baa-169">导入文件</span><span class="sxs-lookup"><span data-stu-id="90baa-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="90baa-170">索引页面</span><span class="sxs-lookup"><span data-stu-id="90baa-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="90baa-171">应用组件</span><span class="sxs-lookup"><span data-stu-id="90baa-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="90baa-172">重定向到登录组件</span><span class="sxs-lookup"><span data-stu-id="90baa-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="90baa-173">登录显示组件</span><span class="sxs-lookup"><span data-stu-id="90baa-173">LoginDisplay component</span></span>

<span data-ttu-id="90baa-174">组件`LoginDisplay`（*共享/LoginDisplay.razor*） 呈现在`MainLayout`组件中 （*共享/MainLayout.razor*） 并管理以下行为：</span><span class="sxs-lookup"><span data-stu-id="90baa-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="90baa-175">对于经过身份验证的用户：</span><span class="sxs-lookup"><span data-stu-id="90baa-175">For authenticated users:</span></span>
  * <span data-ttu-id="90baa-176">显示当前用户名。</span><span class="sxs-lookup"><span data-stu-id="90baa-176">Displays the current user name.</span></span>
  * <span data-ttu-id="90baa-177">提供指向ASP.NET核心标识中的用户配置文件页的链接。</span><span class="sxs-lookup"><span data-stu-id="90baa-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="90baa-178">提供一个按钮以注销应用程序。</span><span class="sxs-lookup"><span data-stu-id="90baa-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="90baa-179">对于匿名用户：</span><span class="sxs-lookup"><span data-stu-id="90baa-179">For anonymous users:</span></span>
  * <span data-ttu-id="90baa-180">提供注册选项。</span><span class="sxs-lookup"><span data-stu-id="90baa-180">Offers the option to register.</span></span>
  * <span data-ttu-id="90baa-181">提供登录选项。</span><span class="sxs-lookup"><span data-stu-id="90baa-181">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="90baa-182">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="90baa-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="90baa-183">提取数据组件</span><span class="sxs-lookup"><span data-stu-id="90baa-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="90baa-184">运行应用</span><span class="sxs-lookup"><span data-stu-id="90baa-184">Run the app</span></span>

<span data-ttu-id="90baa-185">从"服务器"项目运行应用。</span><span class="sxs-lookup"><span data-stu-id="90baa-185">Run the app from the Server project.</span></span> <span data-ttu-id="90baa-186">使用 Visual Studio 时，在**解决方案资源管理器**中选择"服务器"项目，然后选择工具栏中的 **"运行"** 按钮，或从 **"调试"** 菜单启动应用。</span><span class="sxs-lookup"><span data-stu-id="90baa-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="90baa-187">其他资源</span><span class="sxs-lookup"><span data-stu-id="90baa-187">Additional resources</span></span>

* [<span data-ttu-id="90baa-188">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="90baa-188">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
