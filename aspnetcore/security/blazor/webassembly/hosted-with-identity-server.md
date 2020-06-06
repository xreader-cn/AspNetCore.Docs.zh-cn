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
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>Blazor使用服务器保护 ASP.NET Core WebAssembly 的托管应用 Identity

作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)

本文介绍如何创建新 Blazor 的托管应用，该应用使用[IdentityServer](https://identityserver.io/)对用户和 API 调用进行身份验证。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在 Visual Studio 中：

1. 创建新的** Blazor WebAssembly**应用。 有关详细信息，请参阅 <xref:blazor/get-started>。
1. 在 "**创建新 Blazor 应用**" 对话框中，在 "**身份验证**" 部分中选择 "**更改**"。
1. 选择后跟 **"确定"** 的**单个用户帐户**。
1. 选中 "**高级**" 部分中的 " **ASP.NET Core 托管**" 复选框。
1. 选择“创建”**** 按钮。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

若要在命令外壳中创建应用，请执行以下命令：

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如， `-o BlazorSample` ）。 文件夹名称还会成为项目名称的一部分。

---

## <a name="server-app-configuration"></a>服务器应用配置

以下各节介绍了在包括身份验证支持时对项目的添加。

### <a name="startup-class"></a>Startup 类

`Startup`类添加了以下内容。

* 在 `Startup.ConfigureServices`中：

  * ASP.NET Core Identity ：

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer，另外还提供了一个用于 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 在 IdentityServer 上设置默认 ASP.NET Core 约定的帮助器方法：

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * 使用其他 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 帮助器方法进行身份验证，该方法将应用程序配置为验证 IdentityServer 生成的 JWT 令牌：

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* 在 `Startup.Configure`中：

  * IdentityServer 中间件公开 Open ID Connect （OIDC）终结点：

    ```csharp
    app.UseIdentityServer();
    ```

  * 身份验证中间件负责验证请求凭据并在请求上下文中设置用户：

    ```csharp
    app.UseAuthentication();
    ```

  * 授权中间件启用授权功能：

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Helper 方法为 ASP.NET Core 情况配置[IdentityServer](https://identityserver.io/) 。 IdentityServer 是一个功能强大且可扩展的框架，用于处理应用安全问题。 在最常见的情况下，IdentityServer 会造成不必要的复杂性。 因此，我们考虑到了一个很好的起点。 身份验证需要更改后，IdentityServer 的全部功能可用于自定义身份验证，以满足应用的要求。

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Helper 方法为应用配置策略方案作为默认身份验证处理程序。 此策略配置为允许 Identity 处理所有路由到 URL 空间中的子路径的请求 Identity `/Identity` 。 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>处理所有其他请求。 此外，此方法：

* 使用 `{APPLICATION NAME}API` IdentityServer 的默认范围注册 API 资源 `{APPLICATION NAME}API` 。
* 将 JWT 持有者令牌中间件配置为验证 IdentityServer 为应用程序颁发的令牌。

### <a name="weatherforecastcontroller"></a>WeatherForecastController

在 `WeatherForecastController` （*控制器/WeatherForecastController*）中， [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 特性应用于类。 属性指示用户必须根据默认策略进行授权才能访问资源。 默认授权策略配置为使用由设置的默认身份验证方案 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 。 Helper 方法将配置 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 为应用程序请求的默认处理程序。

### <a name="applicationdbcontext"></a>ApplicationDbContext

在 `ApplicationDbContext` （*Data/ApplicationDbContext*）中， <xref:Microsoft.EntityFrameworkCore.DbContext> 扩展 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> 以包括 IdentityServer 的架构。 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> 派生自 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>。

若要完全控制数据库架构，请从某个可用的类继承， Identity <xref:Microsoft.EntityFrameworkCore.DbContext> 并 Identity 通过 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` 在方法中调用来配置上下文以包含架构 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> 。

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

在 `OidcConfigurationController` （controller */OidcConfigurationController*）中，客户端终结点预配为提供 OIDC 参数。

### <a name="app-settings-files"></a>应用设置文件

在项目根目录下的应用设置文件（*appsettings*）中， `IdentityServer` 部分描述了已配置的客户端的列表。 在下面的示例中，有一个客户端。 客户端名称对应于应用名称，并按约定映射到 OAuth `ClientId` 参数。 配置文件指示正在配置的应用类型。 此配置文件可在内部使用，以促进简化服务器配置过程的约定。 <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a>客户端应用配置

### <a name="authentication-package"></a>身份验证包

当创建应用以使用单个用户帐户（）时 `Individual` ，应用会自动在应用的项目文件中接收[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)包的包引用。 包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。

如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a>API 授权支持

对用户进行身份验证的支持通过[WebAssembly](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)包中提供的扩展方法插入到服务容器中。 此方法设置应用程序所需的服务以与现有授权系统交互。

```csharp
builder.Services.AddApiAuthorization();
```

默认情况下，应用的配置由中的约定加载 `_configuration/{client-id}` 。 按照约定，将客户端 ID 设置为应用的程序集名称。 可以通过使用选项调用重载，将此 URL 更改为指向不同的终结点。

### <a name="imports-file"></a>导入文件

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>索引页面

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>应用组件

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin 组件

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay 组件

`LoginDisplay`组件（*Shared/LoginDisplay*）在 `MainLayout` 组件（*shared/MainLayout*）中呈现并管理以下行为：

* 对于经过身份验证的用户：
  * 显示当前用户名。
  * 提供 ASP.NET Core 中用户配置文件页的链接 Identity 。
  * 提供用于注销应用的按钮。
* 对于匿名用户：
  * 提供注册的选项。
  * 提供用于登录的选项。

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

### <a name="fetchdata-component"></a>FetchData 组件

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>运行应用

从服务器项目运行应用。 使用 Visual Studio 时，可以执行以下任一操作：

* 将工具栏中的 "**启动项目**" 下拉列表设置为 "*服务器 API 应用*"，然后选择 "**运行**" 按钮。
* 在**解决方案资源管理器**中选择服务器项目，并在工具栏中选择 "**运行**" 按钮，或从 "**调试**" 菜单中启动该应用程序。

## <a name="name-and-role-claim-with-api-authorization"></a>具有 API 授权的名称和角色声明

### <a name="custom-user-factory"></a>自定义用户工厂

在客户端应用中，创建自定义用户工厂。 Identity服务器在一个声明中以 JSON 数组的形式发送多个角色 `role` 。 在声明中以字符串值的形式发送单个角色。 工厂 `role` 为用户的每个角色创建单个声明。

*CustomUserFactory.cs*：

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

在客户端应用程序中，注册工厂 `Program.Main` （*Program.cs*）：

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

在服务器应用中，在 <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> 生成器上调用 Identity ，这会添加与角色相关的服务：

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a>配置 Identity 服务器

使用以下方法之一：

* [API 授权选项](#api-authorization-options)
* [配置文件服务](#profile-service)

#### <a name="api-authorization-options"></a>API 授权选项

在服务器应用中：

* 将 Identity 服务器配置为将 `name` 和 `role` 声明放入 ID 令牌和访问令牌。
* 阻止 JWT 标记处理程序中角色的默认映射。

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

#### <a name="profile-service"></a>配置文件服务

在服务器应用中，创建一个 `ProfileService` 实现。

*ProfileService.cs*：

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

在服务器应用中，在中注册配置文件服务 `Startup.ConfigureServices` ：

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>使用授权机制

在客户端应用程序中，组件授权方法此时可以正常工作。 组件中的任何授权机制都可以使用角色来授权用户：

* [AuthorizeView 组件](xref:security/blazor/index#authorizeview-component)（示例： `<AuthorizeView Roles="admin">` ）
* [ `[Authorize]` attribute 指令](xref:security/blazor/index#authorize-attribute)（ <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ）（示例： `@attribute [Authorize(Roles = "admin")]` ）
* [过程逻辑](xref:security/blazor/index#procedural-logic)（示例： `if (user.IsInRole("admin")) { ... }` ）

  支持多个角色测试：

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

`User.Identity.Name`在客户端应用程序中填充用户的用户名，该用户名通常为登录电子邮件地址。

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>其他资源

* [部署到 Azure App Service](xref:security/authentication/identity/spa#deploy-to-production)
* [从 Key Vault 导入证书（Azure 文档）](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:security/blazor/webassembly/additional-scenarios>
* [使用安全的默认客户端的应用中未经身份验证或未授权的 web API 请求](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
