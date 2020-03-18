---
title: 使用标识服务器保护 ASP.NET Core Blazor WebAssembly 托管应用
author: guardrex
description: 使用[IdentityServer](https://identityserver.io/)后端在 Visual Studio 中创建具有身份验证的新 Blazor 托管应用
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: a3993bf635e5a7aae408d72796015f2414e13c14
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434468"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="7c825-103">使用标识服务器保护 ASP.NET Core Blazor WebAssembly 托管应用</span><span class="sxs-lookup"><span data-stu-id="7c825-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="7c825-104">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7c825-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="7c825-105">若要在 Visual Studio 中创建新的 Blazor 托管应用，使用[IdentityServer](https://identityserver.io/)对用户和 API 调用进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="7c825-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="7c825-106">使用 Visual Studio 创建新的 **Blazor WebAssembly**应用。</span><span class="sxs-lookup"><span data-stu-id="7c825-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="7c825-107">有关详细信息，请参阅 <xref:blazor/get-started>。</span><span class="sxs-lookup"><span data-stu-id="7c825-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="7c825-108">在 "**新建 Blazor 应用**" 对话框中，在 "**身份验证**" 部分中选择 "**更改**"。</span><span class="sxs-lookup"><span data-stu-id="7c825-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="7c825-109">选择后跟 **"确定"** 的**单个用户帐户**。</span><span class="sxs-lookup"><span data-stu-id="7c825-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="7c825-110">选中 "**高级**" 部分中的 " **ASP.NET Core 托管**" 复选框。</span><span class="sxs-lookup"><span data-stu-id="7c825-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="7c825-111">选择“创建”按钮。</span><span class="sxs-lookup"><span data-stu-id="7c825-111">Select the **Create** button.</span></span>

<span data-ttu-id="7c825-112">若要在命令外壳中创建应用，请执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="7c825-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="7c825-113">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含 output 选项，其中包含一个路径（例如 `-o BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="7c825-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="7c825-114">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="7c825-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="7c825-115">服务器应用配置</span><span class="sxs-lookup"><span data-stu-id="7c825-115">Server app configuration</span></span>

<span data-ttu-id="7c825-116">以下各节介绍了在包括身份验证支持时对项目的添加。</span><span class="sxs-lookup"><span data-stu-id="7c825-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="7c825-117">Startup 类</span><span class="sxs-lookup"><span data-stu-id="7c825-117">Startup class</span></span>

<span data-ttu-id="7c825-118">`Startup` 类具有以下附加项：</span><span class="sxs-lookup"><span data-stu-id="7c825-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="7c825-119">在 `Startup.ConfigureServices` 中：</span><span class="sxs-lookup"><span data-stu-id="7c825-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="7c825-120">具有默认 UI 的标识：</span><span class="sxs-lookup"><span data-stu-id="7c825-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="7c825-121">使用附加的 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 帮助器方法，可在 IdentityServer 上设置某些默认的 ASP.NET Core 约定：</span><span class="sxs-lookup"><span data-stu-id="7c825-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="7c825-122">使用附加的 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 帮助器方法进行身份验证，该方法将应用程序配置为验证 IdentityServer 生成的 JWT 令牌：</span><span class="sxs-lookup"><span data-stu-id="7c825-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="7c825-123">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="7c825-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="7c825-124">负责验证请求凭据并在请求上下文上设置用户的身份验证中间件：</span><span class="sxs-lookup"><span data-stu-id="7c825-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="7c825-125">公开 Open ID Connect （OIDC）终结点的 IdentityServer 中间件：</span><span class="sxs-lookup"><span data-stu-id="7c825-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="7c825-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="7c825-126">AddApiAuthorization</span></span>

<span data-ttu-id="7c825-127"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper 方法为 ASP.NET Core 情况配置[IdentityServer](https://identityserver.io/) 。</span><span class="sxs-lookup"><span data-stu-id="7c825-127">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="7c825-128">IdentityServer 是一个功能强大且可扩展的框架，用于处理应用安全问题。</span><span class="sxs-lookup"><span data-stu-id="7c825-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="7c825-129">在最常见的情况下，IdentityServer 会造成不必要的复杂性。</span><span class="sxs-lookup"><span data-stu-id="7c825-129">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="7c825-130">因此，我们考虑到了一个很好的起点。</span><span class="sxs-lookup"><span data-stu-id="7c825-130">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="7c825-131">身份验证需要更改后，IdentityServer 的全部功能仍可用于自定义身份验证，以满足应用程序的要求。</span><span class="sxs-lookup"><span data-stu-id="7c825-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="7c825-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="7c825-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="7c825-133"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper 方法为应用配置策略方案，作为默认身份验证处理程序。</span><span class="sxs-lookup"><span data-stu-id="7c825-133">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="7c825-134">此策略配置为允许标识处理路由到标识 URL 空间中任何子路径的所有请求 `/Identity`。</span><span class="sxs-lookup"><span data-stu-id="7c825-134">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="7c825-135"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 处理所有其他请求。</span><span class="sxs-lookup"><span data-stu-id="7c825-135">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="7c825-136">此外，此方法：</span><span class="sxs-lookup"><span data-stu-id="7c825-136">Additionally, this method:</span></span>

* <span data-ttu-id="7c825-137">使用 `{APPLICATION NAME}API`的默认范围向 IdentityServer 注册 `{APPLICATION NAME}API` API 资源。</span><span class="sxs-lookup"><span data-stu-id="7c825-137">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="7c825-138">将 JWT 持有者令牌中间件配置为验证 IdentityServer 为应用程序颁发的令牌。</span><span class="sxs-lookup"><span data-stu-id="7c825-138">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="7c825-139">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="7c825-139">WeatherForecastController</span></span>

<span data-ttu-id="7c825-140">在 `WeatherForecastController` （*控制器/WeatherForecastController*）中， [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)属性应用于类。</span><span class="sxs-lookup"><span data-stu-id="7c825-140">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="7c825-141">属性指示用户必须根据默认策略进行授权才能访问资源。</span><span class="sxs-lookup"><span data-stu-id="7c825-141">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="7c825-142">默认授权策略配置为使用默认身份验证方案，该方案由 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 设置为之前提到的策略方案。</span><span class="sxs-lookup"><span data-stu-id="7c825-142">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="7c825-143">Helper 方法将 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 配置为应用程序请求的默认处理程序。</span><span class="sxs-lookup"><span data-stu-id="7c825-143">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="7c825-144">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="7c825-144">ApplicationDbContext</span></span>

<span data-ttu-id="7c825-145">在 `ApplicationDbContext` （*Data/ApplicationDbContext*）中，同一 <xref:Microsoft.EntityFrameworkCore.DbContext> 用于标识，但它扩展 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> 以包括 IdentityServer 的架构。</span><span class="sxs-lookup"><span data-stu-id="7c825-145">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="7c825-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> 派生自 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>。</span><span class="sxs-lookup"><span data-stu-id="7c825-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="7c825-147">若要完全控制数据库架构，请从某个可用的标识 <xref:Microsoft.EntityFrameworkCore.DbContext> 类继承，并通过在 `OnModelCreating` 方法中调用 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)`，将上下文配置为包含标识架构。</span><span class="sxs-lookup"><span data-stu-id="7c825-147">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="7c825-148">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="7c825-148">OidcConfigurationController</span></span>

<span data-ttu-id="7c825-149">在 `OidcConfigurationController` （controller */OidcConfigurationController*）中，客户端终结点配置为提供 OIDC 参数。</span><span class="sxs-lookup"><span data-stu-id="7c825-149">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="7c825-150">应用设置文件</span><span class="sxs-lookup"><span data-stu-id="7c825-150">App settings files</span></span>

<span data-ttu-id="7c825-151">在项目根目录下的应用设置文件（*appsettings*）中，`IdentityServer` 部分介绍了已配置的客户端的列表。</span><span class="sxs-lookup"><span data-stu-id="7c825-151">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="7c825-152">在下面的示例中，有一个客户端。</span><span class="sxs-lookup"><span data-stu-id="7c825-152">In the following example, there's a single client.</span></span> <span data-ttu-id="7c825-153">客户端名称对应于应用名称，并按约定映射到 OAuth `ClientId` 参数。</span><span class="sxs-lookup"><span data-stu-id="7c825-153">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="7c825-154">配置文件指示正在配置的应用类型。</span><span class="sxs-lookup"><span data-stu-id="7c825-154">The profile indicates the app type being configured.</span></span> <span data-ttu-id="7c825-155">此配置文件可在内部使用，以促进简化服务器配置过程的约定。</span><span class="sxs-lookup"><span data-stu-id="7c825-155">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="7c825-156">开发环境中的应用设置文件（*appsettings）。开发*）在项目根目录下，`IdentityServer` 部分介绍了用于对令牌进行签名的密钥。</span><span class="sxs-lookup"><span data-stu-id="7c825-156">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="7c825-157">客户端应用配置</span><span class="sxs-lookup"><span data-stu-id="7c825-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="7c825-158">身份验证包</span><span class="sxs-lookup"><span data-stu-id="7c825-158">Authentication package</span></span>

<span data-ttu-id="7c825-159">创建应用以使用单个用户帐户（`Individual`）时，应用会自动在应用的项目文件中接收 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 包的包引用。</span><span class="sxs-lookup"><span data-stu-id="7c825-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="7c825-160">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="7c825-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="7c825-161">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="7c825-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="7c825-162">将前面的包引用中的 `{VERSION}` 替换为 <xref:blazor/get-started> 一文中所示 `Microsoft.AspNetCore.Blazor.Templates` 包的版本。</span><span class="sxs-lookup"><span data-stu-id="7c825-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="7c825-163">API 授权支持</span><span class="sxs-lookup"><span data-stu-id="7c825-163">API authorization support</span></span>

<span data-ttu-id="7c825-164">通过 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 包内提供的扩展方法，将对用户进行身份验证的支持插入到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="7c825-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="7c825-165">此方法设置应用与现有授权系统交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="7c825-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="7c825-166">默认情况下，它按约定从 `_configuration/{client-id}`加载应用的配置。</span><span class="sxs-lookup"><span data-stu-id="7c825-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="7c825-167">按照约定，将客户端 ID 设置为应用的程序集名称。</span><span class="sxs-lookup"><span data-stu-id="7c825-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="7c825-168">可以通过使用选项调用重载，将此 URL 更改为指向不同的终结点。</span><span class="sxs-lookup"><span data-stu-id="7c825-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="index-page"></a><span data-ttu-id="7c825-169">索引页面</span><span class="sxs-lookup"><span data-stu-id="7c825-169">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a><span data-ttu-id="7c825-170">应用组件</span><span class="sxs-lookup"><span data-stu-id="7c825-170">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="7c825-171">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="7c825-171">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="7c825-172">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="7c825-172">LoginDisplay component</span></span>

<span data-ttu-id="7c825-173">`LoginDisplay` 组件（*shared/LoginDisplay*）在 `MainLayout` 组件（*shared/MainLayout*）中呈现并管理以下行为：</span><span class="sxs-lookup"><span data-stu-id="7c825-173">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="7c825-174">对于经过身份验证的用户：</span><span class="sxs-lookup"><span data-stu-id="7c825-174">For authenticated users:</span></span>
  * <span data-ttu-id="7c825-175">显示当前用户名。</span><span class="sxs-lookup"><span data-stu-id="7c825-175">Displays the current user name.</span></span>
  * <span data-ttu-id="7c825-176">提供指向 ASP.NET Core 标识中的用户配置文件页的链接。</span><span class="sxs-lookup"><span data-stu-id="7c825-176">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="7c825-177">提供用于注销应用的按钮。</span><span class="sxs-lookup"><span data-stu-id="7c825-177">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="7c825-178">对于匿名用户：</span><span class="sxs-lookup"><span data-stu-id="7c825-178">For anonymous users:</span></span>
  * <span data-ttu-id="7c825-179">提供注册的选项。</span><span class="sxs-lookup"><span data-stu-id="7c825-179">Offers the option to register.</span></span>
  * <span data-ttu-id="7c825-180">提供用于登录的选项。</span><span class="sxs-lookup"><span data-stu-id="7c825-180">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="7c825-181">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="7c825-181">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="7c825-182">FetchData 组件</span><span class="sxs-lookup"><span data-stu-id="7c825-182">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="7c825-183">运行应用程序</span><span class="sxs-lookup"><span data-stu-id="7c825-183">Run the app</span></span>

<span data-ttu-id="7c825-184">从服务器项目运行应用。</span><span class="sxs-lookup"><span data-stu-id="7c825-184">Run the app from the Server project.</span></span> <span data-ttu-id="7c825-185">使用 Visual Studio 时，请在**解决方案资源管理器**中选择服务器项目，并在工具栏中选择 "**运行**" 按钮，或从 "**调试**" 菜单启动应用程序。</span><span class="sxs-lookup"><span data-stu-id="7c825-185">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]
