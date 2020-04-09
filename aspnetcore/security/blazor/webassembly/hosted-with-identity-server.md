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
ms.openlocfilehash: 832109530c4aac372fd75aa1a1d2edbe3768f55f
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501282"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a>使用标识服务器保护BlazorASP.NET核心 Web 组件托管应用

哈维尔[·卡尔瓦罗·纳尔逊](https://github.com/javiercn)和[卢克·莱瑟姆](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

要在 Visual Blazor Studio 中创建新托管应用，使用[标识服务器](https://identityserver.io/)对用户和 API 调用进行身份验证，

1. 使用可视化工作室创建新的**BlazorWeb 组装**应用。 有关详细信息，请参阅 <xref:blazor/get-started>。
1. 在"**创建新Blazor应用"** 对话框中，在 **"身份验证**"部分中选择 **"更改**"。
1. 选择**单个用户帐户**后跟 **"确定**"。
1. 在 **"高级"** 部分中选择 **"ASP.NET核心托管**复选框。
1. 选择“创建”**** 按钮。

要在命令外壳中创建应用，请执行以下命令：

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

要指定输出位置（如果不存在，则创建项目文件夹）请在命令中包含具有路径的输出选项（例如。 `-o BlazorSample` 文件夹名称也将成为项目名称的一部分。

## <a name="server-app-configuration"></a>服务器应用配置

以下各节介绍在包含身份验证支持时对项目的补充。

### <a name="startup-class"></a>Startup 类

该`Startup`类具有以下添加功能：

* 在 `Startup.ConfigureServices` 中：

  * 具有默认 UI 的标识：

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * 标识服务器具有附加<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>帮助器方法，在标识服务器顶部设置一些默认ASP.NET核心约定：

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * 使用其他<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>帮助程序方法进行身份验证，该方法将应用配置为验证 IdentityServer 生成的 JWT 令牌：

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* 在 `Startup.Configure` 中：

  * 负责验证请求凭据并在请求上下文中设置用户的身份验证中间件：

    ```csharp
    app.UseAuthentication();
    ```

  * 公开打开 ID 连接 （OIDC） 终结点的标识服务器中间件：

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>添加 Api 授权

<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>帮助器方法为ASP.NET核心方案配置[标识服务器](https://identityserver.io/)。 IdentityServer 是一个功能强大且可扩展的框架，用于处理应用安全问题。 标识服务器会为最常见的方案公开不必要的复杂性。 因此，提供了一组约定和配置选项，我们认为这是一个良好的起点。 一旦身份验证需要更改，身份服务器的全部功能仍可用于自定义身份验证以满足应用的要求。

### <a name="addidentityserverjwt"></a>添加身份服务器Jwt

<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>帮助程序方法将应用的策略方案配置为默认身份验证处理程序。 该策略配置为允许标识处理路由到标识 URL 空间`/Identity`中的任何子路径的所有请求。 处理<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>所有其他请求。 此外，此方法：

* 将`{APPLICATION NAME}API`API 资源注册为默认作用域 的`{APPLICATION NAME}API`标识服务器。
* 配置 JWT 承载令牌中间件以验证 IdentityServer 为应用颁发的令牌。

### <a name="weatherforecastcontroller"></a>天气预报控制器

在`WeatherForecastController`（*控制器/天气预报控制器.cs*）[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)中，该属性应用于类。 该属性指示必须根据默认策略授权用户才能访问资源。 默认授权策略配置为使用默认身份验证方案，该方案由<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>前面提到的策略方案设置。 帮助程序方法<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>配置为对应用的请求的默认处理程序。

### <a name="applicationdbcontext"></a>应用程序数据库上下文

在`ApplicationDbContext`（*数据/应用程序DbContext.cs）* 中<xref:Microsoft.EntityFrameworkCore.DbContext>，标识中使用相同的，但扩展<xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>为包括标识服务器的架构除外。 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> 派生自 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>。

要完全控制数据库架构，请从其中一个可用的标识<xref:Microsoft.EntityFrameworkCore.DbContext>类继承，并通过调用`builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)``OnModelCreating`方法将上下文配置为包括标识架构。

### <a name="oidcconfigurationcontroller"></a>Oidc配置控制器

在`OidcConfigurationController`（*控制器/Oidc配置控制器.cs*） 中，客户端终结点被预配以服务 OIDC 参数。

### <a name="app-settings-files"></a>应用设置文件

在项目根部的应用设置文件 （*appsettings.json*）`IdentityServer`中，该部分描述已配置的客户端的列表。 在下面的示例中，只有一个客户端。 客户端名称对应于应用名称，并通过约定映射到 OAuth`ClientId`参数。 配置文件指示正在配置的应用类型。 配置文件在内部用于驱动简化服务器配置过程的约定。 <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

在开发环境应用设置文件中（*应用设置）。在项目根目录的开发.json*中`IdentityServer`，该部分描述了用于对令牌进行签名的键。 <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a>客户端应用配置

### <a name="authentication-package"></a>身份验证包

创建应用以使用个人用户帐户 （）`Individual`时，应用会自动在应用的项目文件中接收`Microsoft.AspNetCore.Components.WebAssembly.Authentication`包的包引用。 该包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌来调用受保护的 API。

如果向应用添加身份验证，则手动将包添加到应用的项目文件中：

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

在前面`{VERSION}`的包引用中替换为`Microsoft.AspNetCore.Blazor.Templates`<xref:blazor/get-started>本文中显示的包版本。

### <a name="api-authorization-support"></a>API 授权支持

通过`Microsoft.AspNetCore.Components.WebAssembly.Authentication`包内提供的扩展方法将用户身份验证支持插入服务容器。 此方法设置应用与现有授权系统交互所需的所有服务。

```csharp
builder.Services.AddApiAuthorization();
```

默认情况下，它按约定从`_configuration/{client-id}`加载应用的配置。 按照惯例，客户端 ID 设置为应用的程序集名称。 可以通过使用选项调用重载来更改此 URL 以指向单独的终结点。

### <a name="imports-file"></a>导入文件

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>索引页面

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>应用组件

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>重定向到登录组件

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>登录显示组件

组件`LoginDisplay`（*共享/LoginDisplay.razor*） 呈现在`MainLayout`组件中 （*共享/MainLayout.razor*） 并管理以下行为：

* 对于经过身份验证的用户：
  * 显示当前用户名。
  * 提供指向ASP.NET核心标识中的用户配置文件页的链接。
  * 提供一个按钮以注销应用程序。
* 对于匿名用户：
  * 提供注册选项。
  * 提供登录选项。

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

### <a name="authentication-component"></a>身份验证组件

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>提取数据组件

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>运行应用

从"服务器"项目运行应用。 使用 Visual Studio 时，在**解决方案资源管理器**中选择"服务器"项目，然后选择工具栏中的 **"运行"** 按钮，或从 **"调试"** 菜单启动应用。

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>其他资源

* [请求其他访问令牌](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
