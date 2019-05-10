---
title: 在 ASP.NET Core 上的单一页面应用的身份验证简介
author: javiercn
description: 使用与托管 ASP.NET Core 应用在单页面应用程序的标识。
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: 4afc9ac0a3c54b452c6a1b23e4de31d7e2fc5284
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894144"
---
# <a name="authentication-and-authorization-for-spas"></a>身份验证和授权 Spa

ASP.NET Core 3.0 或更高版本提供了单一页面应用 (Spa) 中的身份验证 API 授权使用的支持。 ASP.NET Core 标识进行身份验证和存储用户结合[IdentityServer](https://identityserver.io/)实现 Open ID Connect。

身份验证参数已添加到**Angular**并**做出反应**项目模板类似于中的身份验证参数**Web 应用程序 （模型-视图-控制器）** (MVC) 和**Web 应用程序**（Razor 页面） 项目模板。 允许的参数值为**无**并**单个**。 **React.js 和 Redux**项目模板不支持这一次身份验证参数。

## <a name="create-an-app-with-api-authorization-support"></a>API 身份验证支持使用创建应用

通过 Angular 和 React Spa，可以使用用户身份验证和授权。 打开命令外壳，并运行以下命令：

**Angular**:

```console
dotnet new angular -o <output_directory_name> -au Individual
```

**React**:

```console
dotnet new react -o <output_directory_name> -au Individual
```

上述命令创建与 ASP.NET Core 应用*ClientApp*目录，其中包含 SPA。

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>应用程序的 ASP.NET Core 组件的一般说明

以下部分介绍向项目添加内容时，则包括身份验证支持：

### <a name="startup-class"></a>启动类

`Startup`类具有以下添加内容：

* 内部`Startup.ConfigureServices`方法：
  * 默认值 UI 标识：

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer 具有附加`AddApiAuthorization`帮助器方法，该设置一些默认 IdentityServer 之上的 ASP.NET Core 约定：

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * 使用其他身份验证`AddIdentityServerJwt`IdentityServer 生成配置的应用可验证的 JWT 令牌的帮助器方法：

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* 内部`Startup.Configure`方法：
  * 负责验证请求凭据和在请求上下文上设置用户身份验证中间件：

    ```csharp
    app.UseAuthentication();
    ```

  * 公开的 Open ID Connect 终结点 IdentityServer 中间件：

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

此帮助器方法配置 IdentityServer 便使用我们支持的配置。 IdentityServer 是一个功能强大且可扩展的框架，用于处理应用程序安全问题。 同时，该代理公开针对最常见的方案不必要的复杂性。 因此，一组约定和配置选项被提供给您被认为是很好的起点。 一旦你的身份验证需求的变化，IdentityServer 的完整功能是仍可用于自定义身份验证，以满足您的需要。

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

此帮助器方法将应用程序的策略方案配置为默认身份验证处理程序。 策略配置为让标识处理所有请求路由到的标识 URL 空间中任何子路径"/ 标识"。 `JwtBearerHandler`处理所有其他请求。 此外，此方法注册`<<ApplicationName>>API`IdentityServer 具有默认作用域的 API 资源`<<ApplicationName>>API`并配置 JWT 持有者令牌中间件 IdentityServer 由应用程序颁发的令牌进行验证。

### <a name="sampledatacontroller"></a>SampleDataController

在中*Controllers\SampleDataController.cs*文件中，请注意`[Authorize]`属性应用于类，该值指示用户需要经过授权基于访问的资源的默认策略。 默认授权策略配置为使用默认的身份验证方案，通过设置恰好`AddIdentityServerJwt`上面，使已提到的策略方案`JwtBearerHandler`配置通过此类帮助器方法的默认处理程序向应用程序的请求。

### <a name="applicationdbcontext"></a>ApplicationDbContext

在*Data\ApplicationDbContext.cs*文件中，请注意，相同`DbContext`中标识用于它所扩展的异常`ApiAuthorizationDbContext`(更派生的类从`IdentityDbContext`) 包含用于 IdentityServer 的架构。

若要获取的数据库架构的完全控制权，从一个可用的标识继承`DbContext`类，并配置要通过调用包含标识架构的上下文`builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)`上`OnModelCreating`方法。

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

在中*Controllers\OidcConfigurationController.cs*文件中，请注意，预配为客户端需要使用的 OIDC 参数提供服务的终结点。

### <a name="appsettingsjson"></a>appsettings.json

在中*appsettings.json*文件的项目根目录中，新增了一个`IdentityServer`描述的列表的部分配置客户端。 在以下示例中，没有单个客户端。 客户端名称对应于应用程序名称和 oauth 的约定映射`ClientId`参数。 该配置文件指示要配置的应用程序类型。 它是简化配置过程的服务器的驱动器约定在内部使用。 有多个配置文件，如中所述[应用程序配置文件](#application-profiles)部分。

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appsettings.Development.json

在*appsettings。Development.json*文件的项目根目录中，没有`IdentityServer`部分描述用于令牌签名的密钥。 在部署到生产环境时，密钥需设置和应用程序，以及部署中所述[部署到生产](#deploy-to-production)部分。

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>在 Angular 应用的一般说明

身份验证和授权 API 支持在 Angular 模板在其自己 Angular 模块中驻留*ClientApp\src\api 授权*目录。 该模块组成的以下元素：

* 3 个组件：
  * *login.component.ts*:处理应用程序的登录流。
  * *logout.component.ts*:处理应用程序的注销流。
  * *登录名 menu.component.ts*:一个小组件，显示以下集的链接之一：
    * 用户配置文件管理和注销时用户进行身份验证的链接。
    * 注册和登录时用户未经身份验证的链接。
* 路由防护`AuthorizeGuard`，可以添加到路由并要求用户进行身份验证，才能访问该路由。
* HTTP 侦听器`AuthorizeInterceptor`，将访问令牌附加到传出 HTTP 请求时对用户身份验证以 API 为目标。
* 一种服务`AuthorizeService`，处理身份验证过程的较低级别的详细信息，并公开有关身份验证的用户使用应用程序的其余部分的信息。
* Angular 模块，它定义了路由，与应用程序的身份验证部分相关联。 它公开登录菜单组件、 侦听器、 防护和应用程序的其余部分使用的服务。

## <a name="general-description-of-the-react-app"></a>React 应用的常规说明

身份验证和 React 模板中的 API 授权支持驻留在*ClientApp\src\components\api 授权*目录。 它由以下元素组成：

* 4 个组件：
  * *Login.js*:处理应用程序的登录流。
  * *Logout.js*:处理应用程序的注销流。
  * *LoginMenu.js*:一个小组件，显示以下集的链接之一：
    * 用户配置文件管理和注销时用户进行身份验证的链接。
    * 注册和登录时用户未经身份验证的链接。
  * *AuthorizeRoute.js*:需要用户进行身份验证之前呈现组件的路由组件所示`Component`参数。
* 导出`authService`类的实例`AuthorizeService`，处理身份验证过程的较低级别的详细信息，并公开有关身份验证的用户使用应用程序的其余部分的信息。

现在，已了解该解决方案的主要组件，您可以深入地了解应用程序的各个方案。

## <a name="require-authorization-on-a-new-api"></a>需要在上一个新的 API 进行授权

默认情况下，系统配置为轻松地要求新的 Api 的授权。 若要执行此操作，创建新的控制器，并添加`[Authorize]`属性到控制器类或控制器中的任何操作。

## <a name="protect-a-client-side-route-angular"></a>保护客户端的路由 (Angular)

保护客户端的路由是通过向临界条件运行时配置的路由的列表中添加 authorize 防护。 例如，可以看到如何`fetch-data`主应用 Angular 模块内配置路由：

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

务必提到保护路由不保护实际的终结点 (这仍然需要`[Authorize]`特性应用于它)，但它仅会阻止用户未经身份验证时，导航到给定的客户端的路由。

## <a name="authenticate-api-requests-angular"></a>API 请求 (Angular) 进行身份验证

通过由应用定义的 HTTP 客户端侦听器使用自动完成与应用一起托管的 Api 的请求进行身份验证。

## <a name="protect-a-client-side-route-react"></a>保护客户端的路由 (React)

使用保护客户端的路由`AuthorizeRoute`组件而不是纯`Route`组件。 例如，请注意如何`fetch-data`中配置路由`App`组件：

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

保护一个路由：

* 不会保护实际的终结点 (这仍然需要`[Authorize]`特性应用于它)。
* 仅当未经身份验证时，导航到给定的客户端的路由阻止用户。

## <a name="authenticate-api-requests-react"></a>API 请求 (React) 进行身份验证

通过 React 的请求进行身份验证通过首先导入`authService`实例与`AuthorizeService`。 从检索访问令牌`authService`并附加到请求，如下所示。 在 React 组件，这项工作通常是`componentDidMount`生命周期方法，或作为某些用户交互的结果。

### <a name="import-the-authservice-into-your-component"></a>AuthService 导入你的组件

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>检索并将访问令牌附加到响应

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

若要将应用部署到生产环境，需要将其预配的以下资源：

* 使用数据库来存储标识用户帐户和 IdentityServer 授予。
* 生产证书，用于为令牌签名。
  * 此证书; 没有特定要求它可以是自签名的证书或证书的 CA 颁发机构通过预配。
  * 它可以通过 PowerShell 或 OpenSSL 等标准工具生成。
  * 可以安装到目标计算机上的证书存储或作为部署 *.pfx*使用强密码的文件。

### <a name="example-deploy-to-azure-websites"></a>示例:将部署到 Azure 网站

本部分介绍如何将应用部署到 Azure 网站使用的证书存储中存储的证书。 若要修改应用程序，若要从证书存储加载证书，需要上至少应为标准级别时在稍后的步骤中配置应用服务计划。 在应用中*appsettings.json*文件中，修改`IdentityServer`部分以包括关键详细信息：

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

* 证书上的 name 属性对应于该证书的可分辨主题。
* 存储位置表示加载的证书的位置 (`CurrentUser`或`LocalMachine`)。
* 存储名称表示存储证书的证书存储区的名称。 在这种情况下，它指向个人用户存储。

若要部署到 Azure 网站，部署应用程序中的步骤[将应用部署到 Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)创建所需的 Azure 资源和将应用部署到生产环境。

后按照前面的说明，该应用程序部署到 Azure，但尚不起作用。 应用程序使用的证书仍需要进行设置。 查找要使用的证书的指纹，然后按照步骤中所述[加载证书](/azure/app-service/app-service-web-ssl-cert-load#load-your-certificates)。

虽然这些步骤提到 SSL，则**私用证书**用于上传要与该应用程序使用的预配的证书门户上的部分。

此步骤后，重新启动应用程序，它应能正常运行。

## <a name="other-configuration-options"></a>其他配置选项

对 API 授权支持基于 IdentityServer 具有一系列约定、 默认值和增强功能，以简化 Spa 的体验。 不用说，IdentityServer 的完整功能后，在后台的 ASP.NET Core 集成不涵盖你的方案。 ASP.NET Core 支持侧重于"第一方"应用程序，其中创建和部署我们的组织的所有应用。 在这种情况下，不是等同意或联合身份验证提供支持。 对于这些方案中，使用 IdentityServer 并遵循其文档。

### <a name="application-profiles"></a>应用程序配置文件

应用程序配置文件是预定义的应用，以进一步定义其参数的配置。 在此期间，支持以下配置文件：

* `IdentityServerSPA`：表示 SPA IdentityServer 作为一个单元一起托管。
  * `redirect_uri`默认为`/authentication/login-callback`。
  * `post_logout_redirect_uri`默认为`/authentication/logout-callback`。
  * 作用域组包括`openid`， `profile`，并为应用程序中的 Api 定义每个作用域。
  * 是允许的 OIDC 响应类型套`id_token token`或每个单独 (`id_token`， `token`)。
  * 允许的响应模式是`fragment`。
* `SPA`：表示不托管与 IdentityServer SPA。
  * 作用域组包括`openid`， `profile`，并为应用程序中的 Api 定义每个作用域。
  * 是允许的 OIDC 响应类型套`id_token token`或每个单独 (`id_token`， `token`)。
  * 允许的响应模式是`fragment`。
* `IdentityServerJwt`：表示与 IdentityServer 一起托管的 API。
  * 应用程序配置为具有单个作用域，默认值为应用程序名称。
* `API`：表示与 IdentityServer 不托管的 API。
  * 应用程序配置为具有单个作用域，默认值为应用程序名称。

### <a name="configuration-through-appsettings"></a>通过 AppSettings 配置

通过将它们添加到的列表可以配置应用，通过配置系统`Clients`或`Resources`。

配置每个客户端`redirect_uri`和`post_logout_redirect_uri`属性，如下面的示例中所示：

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

在配置资源，可以配置资源的作用域，如下所示：

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

### <a name="configuration-through-code"></a>通过代码配置

您还可以配置的客户端和资源使用的重载通过代码`AddApiAuthorization`的进行的操作以配置选项。

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
