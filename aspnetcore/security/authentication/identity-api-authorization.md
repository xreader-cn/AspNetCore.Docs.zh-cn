---
title: ASP.NET Core 上的单页面应用的身份验证简介
author: javiercn
description: 将标识用于在 ASP.NET Core 应用内托管的单页面应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: f58d92634ce1ef6110533d56c40b7520dda90514
ms.sourcegitcommit: 4818385c3cfe0805e15138a2c1785b62deeaab90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73897039"
---
# <a name="authentication-and-authorization-for-spas"></a>Spa 的身份验证和授权

ASP.NET Core 3.0 或更高版本通过支持 API 授权在单页面应用（Spa）中提供身份验证。 用于对用户进行身份验证和存储的 ASP.NET Core 标识与用于实现 Open ID Connect 的[IdentityServer](https://identityserver.io/)结合。

已将身份验证参数添加到 "**角度**" 和 "**响应**" 项目模板，该模板类似于 web 应用程序中的身份验证参数 **（模型-视图-控制器）** （MVC）和**web 应用程序**（Razor Pages）项目模板。 允许的参数值为**None**和**个体**。 目前，**反应和 Redux**项目模板不支持 authentication 参数。

## <a name="create-an-app-with-api-authorization-support"></a>使用 API 授权支持创建应用

用户身份验证和授权可用于角度和响应 Spa。 打开命令 shell，并运行以下命令：

**角**：

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

**响应**：

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

上述命令创建一个 ASP.NET Core 应用，其中包含包含 SPA 的*ClientApp*目录。

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>应用的 ASP.NET Core 组件的一般描述

以下各节介绍了在包括身份验证支持的情况下对项目添加的内容：

### <a name="startup-class"></a>Startup 类

`Startup` 类具有以下附加项：

* 在 `Startup.ConfigureServices` 方法中：
  * 具有默认 UI 的标识：

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * 使用其他 `AddApiAuthorization` 帮助器方法，在 IdentityServer 上设置一些默认的 ASP.NET Core 约定：

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * 使用附加的 `AddIdentityServerJwt` 帮助器方法进行身份验证，该方法将应用程序配置为验证 IdentityServer 生成的 JWT 令牌：

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* 在 `Startup.Configure` 方法中：
  * 负责验证请求凭据并在请求上下文上设置用户的身份验证中间件：

    ```csharp
    app.UseAuthentication();
    ```

  * 公开开放 ID 连接终结点的 IdentityServer 中间件：

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

此帮助器方法将 IdentityServer 配置为使用受支持的配置。 IdentityServer 是一个功能强大且可扩展的框架，用于处理应用安全问题。 同时，对于最常见的方案，这会造成不必要的复杂性。 因此，系统会为您提供一组约定和配置选项，这是一个很好的起点。 身份验证需要更改后，IdentityServer 的全部功能仍可用于自定义身份验证以满足你的需求。

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

此帮助器方法将应用程序的策略方案配置为默认的身份验证处理程序。 此策略配置为允许标识处理所有路由到标识 URL 空间 "/Identity" 中的子路径的请求。 `JwtBearerHandler` 处理所有其他请求。 此外，此方法向 IdentityServer 的默认作用 `<<ApplicationName>>API` 域注册 `<<ApplicationName>>API` API 资源，并配置 JWT 持有者令牌中间件来验证 IdentityServer 为应用颁发的令牌。

### <a name="weatherforecastcontroller"></a>WeatherForecastController

在*Controllers\WeatherForecastController.cs*文件中，请注意应用于类的 `[Authorize]` 属性，该属性指示用户需要根据默认策略进行授权才能访问资源。 默认授权策略将配置为使用默认身份验证方案，该方案通过 `AddIdentityServerJwt` 设置为上面提到的策略方案，使此类 helper 方法将 `JwtBearerHandler` 配置为请求的默认处理程序应用程序。

### <a name="applicationdbcontext"></a>ApplicationDbContext

在*Data\ApplicationDbContext.cs*文件中，请注意在标识中使用了相同的 `DbContext`，但它会将 `ApiAuthorizationDbContext` （从 `IdentityDbContext`派生的派生类）扩展为包含 IdentityServer 的架构。

若要完全控制数据库架构，请从某个可用的标识 `DbContext` 类继承，并通过在 `OnModelCreating` 方法上调用 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)`，将上下文配置为包含标识架构。

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

在*Controllers\OidcConfigurationController.cs*文件中，请注意为提供客户端需要使用的 OIDC 参数而设置的终结点。

### <a name="appsettingsjson"></a>appsettings.json

在项目根的*appsettings*文件中，有一个新的 `IdentityServer` 节，其中描述了已配置的客户端的列表。 在下面的示例中，有一个客户端。 客户端名称对应于应用名称，并按约定映射到 OAuth `ClientId` 参数。 配置文件指示正在配置的应用类型。 它在内部用于驱动约定，以简化服务器的配置过程。 有几个配置文件可用，如 "[应用程序配置文件](#application-profiles)" 部分中所述。

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appsettings.开发 json

在*appsettings 中。* 项目根的开发 json 文件，有一个 `IdentityServer` 部分描述用于对令牌进行签名的密钥。 部署到生产环境时，需要在应用中预配和部署密钥，如 "[部署到生产](#deploy-to-production)" 一节中所述。

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>角应用的一般说明

角度模板中的身份验证和 API 授权支持位于其自身的*ClientApp\src\api-authorization*目录中。 模块由以下元素组成：

* 3个组件：
  * *login. ts*：处理应用的登录流。
  * node.js：处理应用程序的注销*流。*
  * *login-menu. ts*：一个小组件，显示以下一组链接：
    * 用户进行身份验证时，用户配置文件管理和注销链接。
    * 用户未通过身份验证时的注册和登录链接。
* 路由防护 `AuthorizeGuard` 可以添加到路由，并在访问路由之前要求用户进行身份验证。
* 一个 HTTP 侦听器 `AuthorizeInterceptor`，它在用户进行身份验证时，将访问令牌附加到针对 API 的传出 HTTP 请求。
* 一种服务 `AuthorizeService`，用于处理身份验证过程的较低级别的详细信息，并向应用程序的其余部分提供有关使用情况的经过身份验证的用户的信息。
* 用于定义与应用的身份验证部分关联的路由的角模块。 它公开登录菜单组件、侦听器、防护和服务，以便从应用程序的其余部分使用。

## <a name="general-description-of-the-react-app"></a>响应应用的一般说明

响应模板中的身份验证和 API 授权支持位于*ClientApp\src\components\api-authorization*目录中。 它由以下元素组成：

* 4个组件：
  * *Login*：处理应用的登录流。
  * *Node.js*：处理应用程序的注销流。
  * *LoginMenu*：显示以下链接集之一的小组件：
    * 用户进行身份验证时，用户配置文件管理和注销链接。
    * 用户未通过身份验证时的注册和登录链接。
  * *AuthorizeRoute*：路由组件，需要先对用户进行身份验证，然后才能呈现 `Component` 参数中指示的组件。
* `AuthorizeService` 的已导出 `authService` 类的实例，用于处理身份验证过程的较低级别细节，并向应用程序的其余部分公开有关使用情况的已通过身份验证的用户的信息。

现在，你已了解解决方案的主要组件，可以更深入地了解应用程序的各个方案。

## <a name="require-authorization-on-a-new-api"></a>要求对新 API 进行授权

默认情况下，系统配置为轻松地要求对新 Api 进行授权。 为此，请创建新的控制器，并将 `[Authorize]` 特性添加到控制器类或控制器中的任何操作。

## <a name="customize-the-api-authentication-handler"></a>自定义 API 身份验证处理程序

若要自定义 API 的 JWT 处理程序的配置，请配置其 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> 实例：

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

API 的 JWT 处理程序会引发事件，这些事件可以使用 `JwtBearerEvents`来控制身份验证过程。 若要为 API 授权提供支持，`AddIdentityServerJwt` 会注册其自己的事件处理程序。

若要自定义事件的处理，请根据需要使用其他逻辑来包装现有的事件处理程序。 例如:

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

在前面的代码中，`OnTokenValidated` 事件处理程序将替换为自定义实现。 此实现：

1. 调用 API 授权支持提供的原始实现。
1. 运行自己的自定义逻辑。

## <a name="protect-a-client-side-route-angular"></a>保护客户端路线（角度）

通过将授权防护添加到在配置路由时要运行的防护列表，来保护客户端路由。 例如，可以在主应用角模块中查看如何配置 `fetch-data` 路由：

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

需要说明的是，保护路由不会保护实际终结点（仍需要应用 `[Authorize]` 属性），但它只会阻止用户在未通过身份验证时导航到给定的客户端路由。

## <a name="authenticate-api-requests-angular"></a>对 API 请求进行身份验证（角度）

对与应用程序一起托管的 Api 的请求进行身份验证是通过使用应用定义的 HTTP 客户端侦听器来自动完成的。

## <a name="protect-a-client-side-route-react"></a>保护客户端路由（响应）

使用 `AuthorizeRoute` 组件而不是纯 `Route` 组件来保护客户端路由。 例如，请注意在 `App` 组件中如何配置 `fetch-data` 路由：

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

保护路由：

* 不保护实际终结点（仍需要应用 `[Authorize]` 特性）。
* 仅阻止用户在未通过身份验证时导航到给定的客户端路由。

## <a name="authenticate-api-requests-react"></a>对 API 请求进行身份验证（响应）

通过首先从 `AuthorizeService`导入 `authService` 实例，对具有反应的请求进行身份验证。 将从 `authService` 检索访问令牌，并将其附加到请求，如下所示。 在响应组件中，这种工作通常是在 `componentDidMount` 生命周期方法中完成的，或者作为某些用户交互的结果。

### <a name="import-the-authservice-into-your-component"></a>将 authService 导入组件

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>检索访问令牌并将其附加到响应

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a>部署到生产环境

若要将应用部署到生产环境，需预配以下资源：

* 用于存储标识用户帐户和 IdentityServer 授予的数据库。
* 用于对令牌进行签名的生产证书。
  * 此证书没有特定要求;此证书可以是自签名证书，也可以是通过 CA 颁发机构预配的证书。
  * 它可通过 PowerShell 或 OpenSSL 等标准工具生成。
  * 可以将其安装到目标计算机上的证书存储中，或部署为带有强密码的 *.pfx*文件。

### <a name="example-deploy-to-azure-websites"></a>示例：部署到 Azure 网站

本部分介绍如何使用证书存储中存储的证书将应用部署到 Azure 网站。 若要修改应用以从证书存储区中加载证书，请在稍后的步骤中配置应用服务计划。 在应用的*appsettings*文件中，修改 `IdentityServer` 部分以包含关键详细信息：

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* 证书上的名称属性对应于证书的可分辨主题。
* 存储位置表示从何处加载证书（`CurrentUser` 或 `LocalMachine`）。
* 存储名称表示存储证书的证书存储区的名称。 在这种情况下，它指向个人用户存储区。

若要部署到 Azure 网站，请遵循将[应用程序部署到 azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)中的步骤部署应用，以创建所需的 Azure 资源，并将该应用部署到生产环境。

按照前面的说明操作后，应用程序将部署到 Azure，但尚不起作用。 仍需设置应用使用的证书。 找到要使用的证书的指纹，并按照[加载证书](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)中所述的步骤进行操作。

虽然这些步骤提及 SSL，但在门户上有一个 "**私有证书**" 部分，你可以在其中上传要与应用一起使用的预配证书。

完成此步骤后，重新启动应用程序并确保它正常运行。

## <a name="other-configuration-options"></a>其他配置选项

支持 API 授权构建在 IdentityServer 的基础之上，具有一组约定、默认值和增强功能，可简化 Spa 的体验。 不用说，如果 ASP.NET Core 集成不涵盖方案，则 IdentityServer 的全部功能都可在幕后使用。 ASP.NET Core 支持重点介绍 "第一方" 应用，其中的所有应用都是由我们的组织创建和部署的。 因此，不支持许可或联合等。 对于这些方案，请使用 IdentityServer 并按照其文档进行操作。

### <a name="application-profiles"></a>应用程序配置文件

应用程序配置文件是用于进一步定义其参数的应用的预定义配置。 目前支持以下配置文件：

* `IdentityServerSPA`：表示与 IdentityServer 一起托管的 SPA 作为单个单元。
  * `redirect_uri` 默认为 `/authentication/login-callback`。
  * `post_logout_redirect_uri` 默认为 `/authentication/logout-callback`。
  * 作用域集包括为应用中的 Api 定义的 `openid`、`profile`和每个作用域。
  * 允许的 OIDC 响应类型集为单独 `id_token token` 或每个响应类型（`id_token``token`）。
  * 允许的响应模式为 `fragment`。
* `SPA`：表示不与 IdentityServer 托管的 SPA。
  * 作用域集包括为应用中的 Api 定义的 `openid`、`profile`和每个作用域。
  * 允许的 OIDC 响应类型集为单独 `id_token token` 或每个响应类型（`id_token``token`）。
  * 允许的响应模式为 `fragment`。
* `IdentityServerJwt`：表示与 IdentityServer 一起托管的 API。
  * 应用配置为具有一个默认为应用名称的作用域。
* `API`：表示不与 IdentityServer 托管的 API。
  * 应用配置为具有一个默认为应用名称的作用域。

### <a name="configuration-through-appsettings"></a>通过 AppSettings 配置

通过配置系统将应用添加到 `Clients` 或 `Resources`的列表来配置这些应用。

配置每个客户端的 `redirect_uri` 和 `post_logout_redirect_uri` 属性，如以下示例中所示：

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

配置资源时，可以按如下所示配置资源的作用域：

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a>通过代码进行配置

你还可以通过代码使用采用操作来配置选项的 `AddApiAuthorization` 的重载来配置客户端和资源。

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a>其他资源

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
