---
title: 使用 JavaScript 服务在 ASP.NET Core 中创建单页面应用程序
author: scottaddie
description: 了解使用 JavaScript 服务创建由 ASP.NET Core 支持的单页面应用程序（SPA）的好处。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: caff1a735de3274b371f67e6e485dc42e579452c
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828212"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>使用 JavaScript 服务在 ASP.NET Core 中创建单页面应用程序

通过[Scott Addie](https://github.com/scottaddie)和[Fiyaz Hasan](https://fiyazhasan.me/)

单页应用程序 (SPA) 因其固有的丰富用户体验而成为一种常用的 Web 应用程序。 将客户端 SPA 框架或库（如[角度](https://angular.io/)或[响应](https://facebook.github.io/react/)）与服务器端框架（如 ASP.NET Core）相集成可能比较困难。 开发 JavaScript 服务是为了减少集成过程中的摩擦。 使用它可以在不同的客户端和服务器技术堆栈之间进行无缝操作。

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> 本文中所述的功能从 ASP.NET Core 3.0 过时。 [SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet 包中提供了更简单的 SPA 框架集成机制。 有关详细信息，请参阅[[公告] Obsoleting AspNetCore. SpaServices 和 AspNetCore。](https://github.com/dotnet/AspNetCore/issues/12890)

::: moniker-end

## <a name="what-is-javascript-services"></a>什么是 JavaScript 服务

JavaScript 服务是用于 ASP.NET Core 的客户端技术的集合。 其目标是将 ASP.NET Core 定位为开发人员用于构建 SPA 的首选服务器端平台。

JavaScript 服务由两个不同的 NuGet 包组成：

* [Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)
* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)

这些包在以下情况下很有用：

* 在服务器上运行 JavaScript
* 使用 SPA 框架或库
* 生成客户端的资产的 Webpack

在本文的重点放在使用 SpaServices 包。

## <a name="what-is-spaservices"></a>什么是 SpaServices

创建 SpaServices 是为了将 ASP.NET Core 定位为开发人员构建 SPA 的首选服务器端平台。 SpaServices 不需要使用 ASP.NET Core 开发 Spa，它也不会将开发人员锁定到特定的客户端框架。

SpaServices 提供有用的基础结构，例如：

* [服务器端预呈现](#server-side-prerendering)
* [Webpack 开发中间件](#webpack-dev-middleware)
* [热模块更换](#hot-module-replacement)
* [路由帮助程序](#routing-helpers)

总体来说，这些基础结构组件增强了开发工作流和运行时体验。 组件可单独采用。

## <a name="prerequisites-for-using-spaservices"></a>使用 SpaServices 的先决条件

若要使用 SpaServices，安装以下组件：

* [Node.js](https://nodejs.org/) （6 或更高版本） 与 npm

  * 若要验证这些组件安装，并可找到，运行以下命令从命令行：

    ```console
    node -v && npm -v
    ```

  * 如果部署到 Azure 网站，则不需要执行任何操作&mdash;在服务器环境中安装了 node.js。

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * 在使用 Visual Studio 2017 的 Windows 上，通过选择 **.Net Core 跨平台开发**工作负载来安装 SDK。

* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet 包

## <a name="server-side-prerendering"></a>服务器端预呈现

通用（也称“同构”）应用程序是在服务器和客户端上都能运行的 JavaScript 应用程序。 Angular、React 和其他常用框架提供了一个适合此应用程序开发风格的通用平台。 这其中的理念是，先通过 Node.js 在服务器上呈现框架组件，然后将下一步的执行操作委托到客户端。

ASP.NET Core[标记帮助程序](xref:mvc/views/tag-helpers/intro)由 SpaServices 简化通过调用服务器上的 JavaScript 函数的服务器端预呈现的实现。

### <a name="server-side-prerendering-prerequisites"></a>服务器端预呈现必备组件

安装[aspnet 预呈现的](https://www.npmjs.com/package/aspnet-prerendering)npm 包：

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>服务器端预呈现配置

标记帮助程序在项目的供发现通过命名空间注册 *_ViewImports.cshtml*文件：

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

这些标记帮助程序抽象出与低级 Api 直接通信通过利用 Razor 视图中的类似于 HTML 的语法的复杂性：

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>asp 预呈现模块标记帮助程序

`asp-prerender-module`标记帮助程序，使用在前面的代码示例中，执行*ClientApp/dist/main-server.js*通过 Node.js 服务器上。 为清晰起见*main server.js*文件是一个项目中的 TypeScript JavaScript 的转译任务[Webpack](https://webpack.github.io/)生成过程。 Webpack 定义入口点的别名`main-server`; 并遍历此别名的依赖项关系图的开始处*ClientApp/启动 server.ts*文件：

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

在下述 Angular 示例中， *ClientApp/boot-server.ts*文件利用`aspnet-prerendering`npm 包的`createServerRenderer`函数和`RenderResult`类型通过 Node.js 来配置服务器呈现。 需要在服务器端呈现的 HTML 标记会传递给一个解析函数调用，该调用包装在强类型化的 JavaScript `Promise` 对象中。 `Promise`对象的意义在于，它以异步方式将 HTML 标记提供给页面，以便该标记能够注入到 DOM 的占位符元素中。

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>asp 预呈现-数据标记帮助程序

当结合`asp-prerender-module`标记帮助程序`asp-prerender-data`标记帮助程序可用于将从 Razor 视图的上下文信息传递到服务器端 JavaScript。 例如，以下标记将传递到的用户数据`main-server`模块：

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

收到`UserName`参数使用内置的 JSON 序列化程序序列化并存储在`params.data`对象。 在以下 Angular 示例中，数据用于在 `h1`元素中构造个性化的问候语：

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

在标记帮助器中传递的属性名称用**PascalCase**表示法表示。 为 JavaScript，其中，相同的属性名称表示与对比**驼峰式大小写**。 默认 JSON 序列化配置负责这种差异。

若要展开在前面的代码示例时，数据可从服务器到视图通过传递 hydrating`globals`属性提供给`resolve`函数：

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

`postList`内部定义的数组`globals`对象附加到浏览器的全局`window`对象。 为全局作用域此变量提升可消除重复工作，特别是因为它与加载一次在服务器上，再次在客户端上的相同数据。

![附加到窗口对象的全局 postList 变量](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>Webpack 开发中间件

[Webpack 开发中间件](https://webpack.js.org/guides/development/#using-webpack-dev-middleware)引入了 Webpack 按需生成资源的由此简化了的开发工作流。 中间件会自动编译并在浏览器中重新加载页面时提供客户端的资源。 另一种方法是手动 Webpack 调用通过项目的 npm 生成脚本的第三方依赖项或自定义代码发生更改时。 Npm 生成脚本*package.json*文件显示在下面的示例：

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>Webpack Dev 中间件先决条件

安装[webpack](https://www.npmjs.com/package/aspnet-webpack) npm 包：

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>Webpack Dev 中间件配置

到 HTTP 请求管道中的以下代码通过注册 Webpack 开发中间件*Startup.cs*文件的`Configure`方法：

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

`UseWebpackDevMiddleware`前必须调用扩展方法[注册静态文件托管](xref:fundamentals/static-files)通过`UseStaticFiles`扩展方法。 出于安全原因，仅当应用程序在开发模式下运行时注册该中间件。

*Webpack.config.js*文件的`output.publicPath`属性会指示要观看的中间件`dist`文件夹的更改：

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>热模块更换

Webpack 的思考[动态模块更换](https://webpack.js.org/concepts/hot-module-replacement/)(HMR) 功能作为一种演变[Webpack 开发中间件](#webpack-dev-middleware)。 HMR 引入了完全相同的好处，但它进一步简化开发工作流通过自动编译所做的更改后更新页面内容。 不要混淆这与刷新浏览器中，这会干扰的当前内存中状态和 SPA 的调试会话。 没有 Webpack 开发中间件服务和浏览器中，这意味着更改推送到浏览器之间的活动链接。

### <a name="hot-module-replacement-prerequisites"></a>热模块替换先决条件

安装[webpack-](https://www.npmjs.com/package/webpack-hot-middleware) npm 包：

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>热模块替换配置

HMR 组件必须注册到 MVC 的 HTTP 请求管道中`Configure`方法：

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

作为是如此[Webpack 开发中间件](#webpack-dev-middleware)，则`UseWebpackDevMiddleware`前必须调用扩展方法`UseStaticFiles`扩展方法。 出于安全原因，仅当应用程序在开发模式下运行时注册该中间件。

*Webpack.config.js*文件必须定义`plugins`，即使它保留为空数组：

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

加载后在浏览器中的应用，开发人员工具的控制台选项卡提供了 HMR 激活的确认：

![热模块更换已连接的消息](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>路由帮助程序

在大多数基于 ASP.NET Core 的 Spa 中，通常还需要客户端路由，而不是服务器端路由。 SPA 和 MVC 路由系统可以不受干扰地独立工作。 没有，但是，一个边缘事例造成面临的难题： 标识 404 HTTP 响应。

请考虑在该方案中的无扩展名路由`/some/page`使用。 假定该请求不模式匹配的服务器端的路由，但其模式匹配的客户端的路由。 现在，考虑的传入请求`/images/user-512.png`，这通常需要查找服务器上的图像文件。 如果请求的资源路径与任何服务器端路由或静态文件都不匹配，则客户端应用程序可能会处理它，&mdash;通常返回 404 HTTP 状态代码。

### <a name="routing-helpers-prerequisites"></a>路由帮助程序先决条件

安装客户端路由 npm 包。 使用 Angular 作为示例：

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>路由帮助器配置

名为的扩展方法`MapSpaFallbackRoute`中使用`Configure`方法：

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

路由按其配置顺序进行评估。 因此，`default`进行模式匹配第一次使用在前面的代码示例中的路由。

## <a name="create-a-new-project"></a>创建新项目

JavaScript 服务提供预配置的应用程序模板。 在这些模板中，SpaServices 与不同的框架和库（如角度、反应和 Redux）一起使用。

可以通过.NET Core CLI 安装这些模板，通过运行以下命令：

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

显示可用的 SPA 模板的列表：

| 模板                                 | 短名称 | Language | Tags        |
| ------------------------------------------| :--------: | :------: | :---------: |
| 带 Angular 的 MVC ASP.NET Core             | angular    | [C#]     | Web/MVC/SPA |
| 带有 React.js 的 MVC ASP.NET Core            | react      | [C#]     | Web/MVC/SPA |
| 含 React.js 和 Redux 的 MVC ASP.NET Core  | reactredux | [C#]     | Web/MVC/SPA |

若要使用 SPA 模板之一创建新项目，请在 [dotnet new](/dotnet/core/tools/dotnet-new) 命令中包括模板的**短名称**。 以下命令创建 Angular 应用程序，并为服务器端配置 ASP.NET Core MVC：

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>设置运行时配置模式

存在两种主要的运行时配置模式：

* **开发**：
  * 包括源映射，以便进行调试。
  * 不会优化性能的客户端代码。
* **生产**：
  * 不包括源映射。
  * 通过绑定和缩减优化客户端代码。

ASP.NET Core 使用名为的环境变量`ASPNETCORE_ENVIRONMENT`来存储配置模式。 有关详细信息，请参阅[设置环境](xref:fundamentals/environments#set-the-environment)。

### <a name="run-with-net-core-cli"></a>运行 .NET Core CLI

通过在项目根目录运行以下命令还原所需的 NuGet 和 npm 包：

```dotnetcli
dotnet restore && npm i
```

生成并运行应用程序：

```dotnetcli
dotnet run
```

在应用程序根据本地主机上启动[运行时配置模式](#set-the-runtime-configuration-mode)。 导航到 `http://localhost:5000` 在浏览器中显示的登录页。

### <a name="run-with-visual-studio-2017"></a>运行 Visual Studio 2017

打开 *.csproj*生成的文件[dotnet 新](/dotnet/core/tools/dotnet-new)命令。 在项目中打开时自动还原所需的 NuGet 和 npm 包。 此还原过程可能需要几分钟时间，并在应用程序已准备好在它完成后运行。 单击绿色的运行的按钮或按`Ctrl + F5`，并在浏览器打开到应用程序的登录页。 应用程序运行于 localhost 根据[运行时配置模式](#set-the-runtime-configuration-mode)。

## <a name="test-the-app"></a>测试应用

SpaServices 模板是预配置为运行客户端的测试使用[Karma](https://karma-runner.github.io/1.0/index.html)并[Jasmine](https://jasmine.github.io/)。 Jasmine 是常用的单元测试框架，适用于 JavaScript，而 Karma 是这些测试的测试运行程序。 Karma 配置为使用[Webpack 开发中间件](#webpack-dev-middleware)这样开发人员不需要停止并运行测试，每次进行更改。 无论是针对测试用例或测试用例本身运行的代码，则将自动运行测试。

以 Angular 应用程序为例，我们在 `CounterComponent`文件中为 *counter.component.spec.ts*提供了两个 Jasmine 测试用例：

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

打开命令提示符中*ClientApp*目录。 运行下面的命令：

```console
npm test
```

该脚本将启动 Karma 测试运行程序，其内容中定义的设置*karma.conf.js*文件。 在其他设置*karma.conf.js*识别的测试文件，通过执行其`files`数组：

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>发布应用

有关发布到 Azure 的详细信息，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/12474)。

将生成的客户端的资产和已发布的 ASP.NET Core 项目合并为随时可部署的包可能会很麻烦。 幸运的是，SpaServices 协调与名为的自定义 MSBuild 目标的整个发布过程`RunWebpack`:

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

MSBuild 目标具有下列职责：

1. 还原 npm 包。
1. 创建第三方客户端资产的生产级生成。
1. 创建自定义客户端资产的生产级生成。
1. 将 Webpack 生成的资产复制到 "发布" 文件夹。

运行时，会调用 MSBuild 目标：

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a>其他资源

* [Angular 文档](https://angular.io/docs)
