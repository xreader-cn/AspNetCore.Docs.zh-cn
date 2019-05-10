---
title: 使用 JavaScriptServices 在 ASP.NET Core 中创建单页应用程序
author: scottaddie
description: 了解在 ASP.NET Core 中使用 JavaScriptServices 创建单页应用程序 (SPA) 的好处。
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 08/02/2017
uid: client-side/spa-services
ms.openlocfilehash: ee772e67ef14608bcc6e3498ade00424ff6090e5
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64893944"
---
# <a name="use-javascriptservices-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="fe294-103">使用 JavaScriptServices 在 ASP.NET Core 中创建单页应用程序</span><span class="sxs-lookup"><span data-stu-id="fe294-103">Use JavaScriptServices to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="fe294-104">通过[Scott Addie](https://github.com/scottaddie)和[Fiyaz Hasan](http://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="fe294-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](http://fiyazhasan.me/)</span></span>

<span data-ttu-id="fe294-105">单页应用程序 (SPA) 因其固有的丰富用户体验而成为一种常用的 Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="fe294-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="fe294-106">将客户端 SPA 框架或库（例如 [Angular](https://angular.io/) 或 [React](https://facebook.github.io/react/)）与服务器端框架（例如 ASP.NET Core）集成可能很困难。</span><span class="sxs-lookup"><span data-stu-id="fe294-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks like ASP.NET Core can be difficult.</span></span> <span data-ttu-id="fe294-107">开发 [JavaScriptServices](https://github.com/aspnet/JavaScriptServices) 的目的是减少集成过程中的问题。</span><span class="sxs-lookup"><span data-stu-id="fe294-107">[JavaScriptServices](https://github.com/aspnet/JavaScriptServices) was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="fe294-108">使用它可以在不同的客户端和服务器技术堆栈之间进行无缝操作。</span><span class="sxs-lookup"><span data-stu-id="fe294-108">It enables seamless operation between the different client and server technology stacks.</span></span>

<a name="what-is-js-services"></a>

## <a name="what-is-javascriptservices"></a><span data-ttu-id="fe294-109">什么是 JavaScriptServices</span><span class="sxs-lookup"><span data-stu-id="fe294-109">What is JavaScriptServices</span></span>

<span data-ttu-id="fe294-110">JavaScriptServices 是 ASP.NET Core 的客户端技术的集合。</span><span class="sxs-lookup"><span data-stu-id="fe294-110">JavaScriptServices is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="fe294-111">其目标是将 ASP.NET Core 定位为开发人员用于构建 SPA 的首选服务器端平台。</span><span class="sxs-lookup"><span data-stu-id="fe294-111">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="fe294-112">JavaScriptServices 包含三个不同的 NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="fe294-112">JavaScriptServices consists of three distinct NuGet packages:</span></span>

* <span data-ttu-id="fe294-113">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="fe294-113">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="fe294-114">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="fe294-114">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>
* <span data-ttu-id="fe294-115">[Microsoft.AspNetCore.SpaTemplates](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaTemplates/) (SpaTemplates)</span><span class="sxs-lookup"><span data-stu-id="fe294-115">[Microsoft.AspNetCore.SpaTemplates](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaTemplates/) (SpaTemplates)</span></span>

<span data-ttu-id="fe294-116">这些包适用于以下情况：</span><span class="sxs-lookup"><span data-stu-id="fe294-116">These packages are useful if you:</span></span>

* <span data-ttu-id="fe294-117">在服务器上运行 JavaScript</span><span class="sxs-lookup"><span data-stu-id="fe294-117">Run JavaScript on the server</span></span>
* <span data-ttu-id="fe294-118">使用 SPA 框架或库</span><span class="sxs-lookup"><span data-stu-id="fe294-118">Use a SPA framework or library</span></span>
* <span data-ttu-id="fe294-119">生成客户端的资产的 Webpack</span><span class="sxs-lookup"><span data-stu-id="fe294-119">Build client-side assets with Webpack</span></span>

<span data-ttu-id="fe294-120">在本文的重点放在使用 SpaServices 包。</span><span class="sxs-lookup"><span data-stu-id="fe294-120">Much of the focus in this article is placed on using the SpaServices package.</span></span>

<a name="what-is-spa-services"></a>

## <a name="what-is-spaservices"></a><span data-ttu-id="fe294-121">什么是 SpaServices</span><span class="sxs-lookup"><span data-stu-id="fe294-121">What is SpaServices</span></span>

<span data-ttu-id="fe294-122">创建 SpaServices 是为了将 ASP.NET Core 定位为开发人员构建 SPA 的首选服务器端平台。</span><span class="sxs-lookup"><span data-stu-id="fe294-122">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="fe294-123">SpaServices 不是使用 ASP.NET Core 开发 SPA 所必需的，它也不会将你锁定到特定的客户端框架。</span><span class="sxs-lookup"><span data-stu-id="fe294-123">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock you into a particular client framework.</span></span>

<span data-ttu-id="fe294-124">SpaServices 提供有用的基础结构，例如：</span><span class="sxs-lookup"><span data-stu-id="fe294-124">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="fe294-125">服务器端预呈现</span><span class="sxs-lookup"><span data-stu-id="fe294-125">Server-side prerendering</span></span>](#server-prerendering)
* [<span data-ttu-id="fe294-126">Webpack 开发中间件</span><span class="sxs-lookup"><span data-stu-id="fe294-126">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="fe294-127">热模块更换</span><span class="sxs-lookup"><span data-stu-id="fe294-127">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="fe294-128">路由帮助程序</span><span class="sxs-lookup"><span data-stu-id="fe294-128">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="fe294-129">总体来说，这些基础结构组件增强了开发工作流和运行时体验。</span><span class="sxs-lookup"><span data-stu-id="fe294-129">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="fe294-130">组件可单独采用。</span><span class="sxs-lookup"><span data-stu-id="fe294-130">The components can be adopted individually.</span></span>

<a name="spa-services-prereqs"></a>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="fe294-131">使用 SpaServices 的先决条件</span><span class="sxs-lookup"><span data-stu-id="fe294-131">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="fe294-132">若要使用 SpaServices，安装以下组件：</span><span class="sxs-lookup"><span data-stu-id="fe294-132">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="fe294-133">[Node.js](https://nodejs.org/) （6 或更高版本） 与 npm</span><span class="sxs-lookup"><span data-stu-id="fe294-133">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>
  * <span data-ttu-id="fe294-134">若要验证这些组件安装，并可找到，运行以下命令从命令行：</span><span class="sxs-lookup"><span data-stu-id="fe294-134">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

<span data-ttu-id="fe294-135">注意:如果要部署到 Azure 网站，您不需要此处执行任何操作&mdash;Node.js 已安装并且可用的服务器环境中。</span><span class="sxs-lookup"><span data-stu-id="fe294-135">Note: If you're deploying to an Azure web site, you don't need to do anything here &mdash; Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="fe294-136">如果你使用 Visual Studio 2017 在 Windows 上，通过选择安装 SDK **.NET Core 跨平台开发**工作负荷。</span><span class="sxs-lookup"><span data-stu-id="fe294-136">If you're on Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="fe294-137">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet 包</span><span class="sxs-lookup"><span data-stu-id="fe294-137">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

<a name="server-prerendering"></a>

## <a name="server-side-prerendering"></a><span data-ttu-id="fe294-138">服务器端预呈现</span><span class="sxs-lookup"><span data-stu-id="fe294-138">Server-side prerendering</span></span>

<span data-ttu-id="fe294-139">通用（也称“同构”）应用程序是在服务器和客户端上都能运行的 JavaScript 应用程序。</span><span class="sxs-lookup"><span data-stu-id="fe294-139">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="fe294-140">Angular、React 和其他常用框架提供了一个适合此应用程序开发风格的通用平台。</span><span class="sxs-lookup"><span data-stu-id="fe294-140">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="fe294-141">这其中的理念是，先通过 Node.js 在服务器上呈现框架组件，然后将下一步的执行操作委托到客户端。</span><span class="sxs-lookup"><span data-stu-id="fe294-141">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="fe294-142">ASP.NET Core[标记帮助程序](xref:mvc/views/tag-helpers/intro)由 SpaServices 简化通过调用服务器上的 JavaScript 函数的服务器端预呈现的实现。</span><span class="sxs-lookup"><span data-stu-id="fe294-142">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="fe294-143">系统必备</span><span class="sxs-lookup"><span data-stu-id="fe294-143">Prerequisites</span></span>

<span data-ttu-id="fe294-144">安装以下组件：</span><span class="sxs-lookup"><span data-stu-id="fe294-144">Install the following:</span></span>

* <span data-ttu-id="fe294-145">[aspnet 预呈现](https://www.npmjs.com/package/aspnet-prerendering)npm 包：</span><span class="sxs-lookup"><span data-stu-id="fe294-145">[aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

    ```console
    npm i -S aspnet-prerendering
    ```

### <a name="configuration"></a><span data-ttu-id="fe294-146">配置</span><span class="sxs-lookup"><span data-stu-id="fe294-146">Configuration</span></span>

<span data-ttu-id="fe294-147">标记帮助程序在项目的供发现通过命名空间注册 *_ViewImports.cshtml*文件：</span><span class="sxs-lookup"><span data-stu-id="fe294-147">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="fe294-148">这些标记帮助程序抽象出与低级 Api 直接通信通过利用 Razor 视图中的类似于 HTML 的语法的复杂性：</span><span class="sxs-lookup"><span data-stu-id="fe294-148">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="the-asp-prerender-module-tag-helper"></a><span data-ttu-id="fe294-149">`asp-prerender-module`标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="fe294-149">The `asp-prerender-module` Tag Helper</span></span>

<span data-ttu-id="fe294-150">`asp-prerender-module`标记帮助程序，使用在前面的代码示例中，执行*ClientApp/dist/main-server.js*通过 Node.js 服务器上。</span><span class="sxs-lookup"><span data-stu-id="fe294-150">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="fe294-151">为清晰起见*main server.js*文件是一个项目中的 TypeScript JavaScript 的转译任务[Webpack](http://webpack.github.io/)生成过程。</span><span class="sxs-lookup"><span data-stu-id="fe294-151">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](http://webpack.github.io/) build process.</span></span> <span data-ttu-id="fe294-152">Webpack 定义入口点的别名`main-server`; 并遍历此别名的依赖项关系图的开始处*ClientApp/启动 server.ts*文件：</span><span class="sxs-lookup"><span data-stu-id="fe294-152">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="fe294-153">在下述 Angular 示例中， *ClientApp/boot-server.ts*文件利用`aspnet-prerendering`npm 包的`createServerRenderer`函数和`RenderResult`类型通过 Node.js 来配置服务器呈现。</span><span class="sxs-lookup"><span data-stu-id="fe294-153">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="fe294-154">需要在服务器端呈现的 HTML 标记会传递给一个解析函数调用，该调用包装在强类型化的 JavaScript `Promise` 对象中。</span><span class="sxs-lookup"><span data-stu-id="fe294-154">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="fe294-155">`Promise`对象的意义在于，它以异步方式将 HTML 标记提供给页面，以便该标记能够注入到 DOM 的占位符元素中。</span><span class="sxs-lookup"><span data-stu-id="fe294-155">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="the-asp-prerender-data-tag-helper"></a><span data-ttu-id="fe294-156">`asp-prerender-data`标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="fe294-156">The `asp-prerender-data` Tag Helper</span></span>

<span data-ttu-id="fe294-157">当结合`asp-prerender-module`标记帮助程序`asp-prerender-data`标记帮助程序可用于将从 Razor 视图的上下文信息传递到服务器端 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="fe294-157">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="fe294-158">例如，以下标记将传递到的用户数据`main-server`模块：</span><span class="sxs-lookup"><span data-stu-id="fe294-158">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="fe294-159">收到`UserName`参数使用内置的 JSON 序列化程序序列化并存储在`params.data`对象。</span><span class="sxs-lookup"><span data-stu-id="fe294-159">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="fe294-160">在以下 Angular 示例中，数据用于在 `h1`元素中构造个性化的问候语：</span><span class="sxs-lookup"><span data-stu-id="fe294-160">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="fe294-161">注意:标记帮助程序中传递的属性名称表示与**pascal 命名法**表示法。</span><span class="sxs-lookup"><span data-stu-id="fe294-161">Note: Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="fe294-162">为 JavaScript，其中，相同的属性名称表示与对比**驼峰式大小写**。</span><span class="sxs-lookup"><span data-stu-id="fe294-162">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="fe294-163">默认 JSON 序列化配置负责这种差异。</span><span class="sxs-lookup"><span data-stu-id="fe294-163">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="fe294-164">若要展开在前面的代码示例时，数据可从服务器到视图通过传递 hydrating`globals`属性提供给`resolve`函数：</span><span class="sxs-lookup"><span data-stu-id="fe294-164">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="fe294-165">`postList`内部定义的数组`globals`对象附加到浏览器的全局`window`对象。</span><span class="sxs-lookup"><span data-stu-id="fe294-165">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="fe294-166">为全局作用域此变量提升可消除重复工作，特别是因为它与加载一次在服务器上，再次在客户端上的相同数据。</span><span class="sxs-lookup"><span data-stu-id="fe294-166">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![附加到窗口对象的全局 postList 变量](spa-services/_static/global_variable.png)

<a name="webpack-dev-middleware"></a>

## <a name="webpack-dev-middleware"></a><span data-ttu-id="fe294-168">Webpack 开发中间件</span><span class="sxs-lookup"><span data-stu-id="fe294-168">Webpack Dev Middleware</span></span>

<span data-ttu-id="fe294-169">[Webpack 开发中间件](https://webpack.github.io/docs/webpack-dev-middleware.html)引入了 Webpack 按需生成资源的由此简化了的开发工作流。</span><span class="sxs-lookup"><span data-stu-id="fe294-169">[Webpack Dev Middleware](https://webpack.github.io/docs/webpack-dev-middleware.html) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="fe294-170">中间件会自动编译并在浏览器中重新加载页面时提供客户端的资源。</span><span class="sxs-lookup"><span data-stu-id="fe294-170">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="fe294-171">另一种方法是手动 Webpack 调用通过项目的 npm 生成脚本的第三方依赖项或自定义代码发生更改时。</span><span class="sxs-lookup"><span data-stu-id="fe294-171">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="fe294-172">Npm 生成脚本*package.json*文件显示在下面的示例：</span><span class="sxs-lookup"><span data-stu-id="fe294-172">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="prerequisites"></a><span data-ttu-id="fe294-173">系统必备</span><span class="sxs-lookup"><span data-stu-id="fe294-173">Prerequisites</span></span>

<span data-ttu-id="fe294-174">安装以下组件：</span><span class="sxs-lookup"><span data-stu-id="fe294-174">Install the following:</span></span>

* <span data-ttu-id="fe294-175">[aspnet webpack](https://www.npmjs.com/package/aspnet-webpack) npm 包：</span><span class="sxs-lookup"><span data-stu-id="fe294-175">[aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

    ```console
    npm i -D aspnet-webpack
    ```

### <a name="configuration"></a><span data-ttu-id="fe294-176">配置</span><span class="sxs-lookup"><span data-stu-id="fe294-176">Configuration</span></span>

<span data-ttu-id="fe294-177">到 HTTP 请求管道中的以下代码通过注册 Webpack 开发中间件*Startup.cs*文件的`Configure`方法：</span><span class="sxs-lookup"><span data-stu-id="fe294-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="fe294-178">`UseWebpackDevMiddleware`前必须调用扩展方法[注册静态文件托管](xref:fundamentals/static-files)通过`UseStaticFiles`扩展方法。</span><span class="sxs-lookup"><span data-stu-id="fe294-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="fe294-179">出于安全原因，仅当应用程序在开发模式下运行时注册该中间件。</span><span class="sxs-lookup"><span data-stu-id="fe294-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="fe294-180">*Webpack.config.js*文件的`output.publicPath`属性会指示要观看的中间件`dist`文件夹的更改：</span><span class="sxs-lookup"><span data-stu-id="fe294-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

<a name="hot-module-replacement"></a>

## <a name="hot-module-replacement"></a><span data-ttu-id="fe294-181">热模块更换</span><span class="sxs-lookup"><span data-stu-id="fe294-181">Hot Module Replacement</span></span>

<span data-ttu-id="fe294-182">Webpack 的思考[动态模块更换](https://webpack.js.org/concepts/hot-module-replacement/)(HMR) 功能作为一种演变[Webpack 开发中间件](#webpack-dev-middleware)。</span><span class="sxs-lookup"><span data-stu-id="fe294-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="fe294-183">HMR 引入了完全相同的好处，但它进一步简化开发工作流通过自动编译所做的更改后更新页面内容。</span><span class="sxs-lookup"><span data-stu-id="fe294-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="fe294-184">不要混淆这与刷新浏览器中，这会干扰的当前内存中状态和 SPA 的调试会话。</span><span class="sxs-lookup"><span data-stu-id="fe294-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="fe294-185">没有 Webpack 开发中间件服务和浏览器中，这意味着更改推送到浏览器之间的活动链接。</span><span class="sxs-lookup"><span data-stu-id="fe294-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="fe294-186">系统必备</span><span class="sxs-lookup"><span data-stu-id="fe294-186">Prerequisites</span></span>

<span data-ttu-id="fe294-187">安装以下组件：</span><span class="sxs-lookup"><span data-stu-id="fe294-187">Install the following:</span></span>

* <span data-ttu-id="fe294-188">[webpack 的热的中间件](https://www.npmjs.com/package/webpack-hot-middleware)npm 包：</span><span class="sxs-lookup"><span data-stu-id="fe294-188">[webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

    ```console
    npm i -D webpack-hot-middleware
    ```

### <a name="configuration"></a><span data-ttu-id="fe294-189">配置</span><span class="sxs-lookup"><span data-stu-id="fe294-189">Configuration</span></span>

<span data-ttu-id="fe294-190">HMR 组件必须注册到 MVC 的 HTTP 请求管道中`Configure`方法：</span><span class="sxs-lookup"><span data-stu-id="fe294-190">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="fe294-191">作为是如此[Webpack 开发中间件](#webpack-dev-middleware)，则`UseWebpackDevMiddleware`前必须调用扩展方法`UseStaticFiles`扩展方法。</span><span class="sxs-lookup"><span data-stu-id="fe294-191">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="fe294-192">出于安全原因，仅当应用程序在开发模式下运行时注册该中间件。</span><span class="sxs-lookup"><span data-stu-id="fe294-192">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="fe294-193">*Webpack.config.js*文件必须定义`plugins`，即使它保留为空数组：</span><span class="sxs-lookup"><span data-stu-id="fe294-193">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="fe294-194">加载后在浏览器中的应用，开发人员工具的控制台选项卡提供了 HMR 激活的确认：</span><span class="sxs-lookup"><span data-stu-id="fe294-194">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![热模块更换已连接的消息](spa-services/_static/hmr_connected.png)

<a name="routing-helpers"></a>

## <a name="routing-helpers"></a><span data-ttu-id="fe294-196">路由帮助程序</span><span class="sxs-lookup"><span data-stu-id="fe294-196">Routing helpers</span></span>

<span data-ttu-id="fe294-197">在大多数基于 ASP.NET Core 的 Spa，需要将客户端的路由除了服务器端的路由。</span><span class="sxs-lookup"><span data-stu-id="fe294-197">In most ASP.NET Core-based SPAs, you'll want client-side routing in addition to server-side routing.</span></span> <span data-ttu-id="fe294-198">SPA 和 MVC 路由系统可以不受干扰地独立工作。</span><span class="sxs-lookup"><span data-stu-id="fe294-198">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="fe294-199">没有，但是，一个边缘事例造成面临的难题： 标识 404 HTTP 响应。</span><span class="sxs-lookup"><span data-stu-id="fe294-199">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="fe294-200">请考虑在该方案中的无扩展名路由`/some/page`使用。</span><span class="sxs-lookup"><span data-stu-id="fe294-200">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="fe294-201">假定该请求不模式匹配的服务器端的路由，但其模式匹配的客户端的路由。</span><span class="sxs-lookup"><span data-stu-id="fe294-201">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="fe294-202">现在，考虑的传入请求`/images/user-512.png`，这通常需要查找服务器上的图像文件。</span><span class="sxs-lookup"><span data-stu-id="fe294-202">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="fe294-203">如果该请求的资源路径不符合的任何服务器端的路由或静态文件，它不大可能的客户端应用程序对其进行处理，你通常想要返回 404 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="fe294-203">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it — you generally want to return a 404 HTTP status code.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="fe294-204">系统必备</span><span class="sxs-lookup"><span data-stu-id="fe294-204">Prerequisites</span></span>

<span data-ttu-id="fe294-205">安装以下组件：</span><span class="sxs-lookup"><span data-stu-id="fe294-205">Install the following:</span></span>

* <span data-ttu-id="fe294-206">客户端的路由 npm 包。</span><span class="sxs-lookup"><span data-stu-id="fe294-206">The client-side routing npm package.</span></span> <span data-ttu-id="fe294-207">使用 Angular 作为示例：</span><span class="sxs-lookup"><span data-stu-id="fe294-207">Using Angular as an example:</span></span>

    ```console
    npm i -S @angular/router
    ```

### <a name="configuration"></a><span data-ttu-id="fe294-208">配置</span><span class="sxs-lookup"><span data-stu-id="fe294-208">Configuration</span></span>

<span data-ttu-id="fe294-209">名为的扩展方法`MapSpaFallbackRoute`中使用`Configure`方法：</span><span class="sxs-lookup"><span data-stu-id="fe294-209">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="fe294-210">提示:路由配置它们的顺序计算。</span><span class="sxs-lookup"><span data-stu-id="fe294-210">Tip: Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="fe294-211">因此，`default`进行模式匹配第一次使用在前面的代码示例中的路由。</span><span class="sxs-lookup"><span data-stu-id="fe294-211">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

<a name="new-project-creation"></a>

## <a name="creating-a-new-project"></a><span data-ttu-id="fe294-212">创建新的项目</span><span class="sxs-lookup"><span data-stu-id="fe294-212">Creating a new project</span></span>

<span data-ttu-id="fe294-213">JavaScriptServices 提供了预配置的应用程序模板。</span><span class="sxs-lookup"><span data-stu-id="fe294-213">JavaScriptServices provides pre-configured application templates.</span></span> <span data-ttu-id="fe294-214">SpaServices 在这些模板中与不同的框架和库（例如 Angular、React 和 Redux）配合使用。</span><span class="sxs-lookup"><span data-stu-id="fe294-214">SpaServices is used in these templates, in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="fe294-215">可以通过.NET Core CLI 安装这些模板，通过运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="fe294-215">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```console
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="fe294-216">显示可用的 SPA 模板的列表：</span><span class="sxs-lookup"><span data-stu-id="fe294-216">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="fe294-217">模板</span><span class="sxs-lookup"><span data-stu-id="fe294-217">Templates</span></span>                                 | <span data-ttu-id="fe294-218">短名称</span><span class="sxs-lookup"><span data-stu-id="fe294-218">Short Name</span></span> | <span data-ttu-id="fe294-219">语言</span><span class="sxs-lookup"><span data-stu-id="fe294-219">Language</span></span> | <span data-ttu-id="fe294-220">Tags</span><span class="sxs-lookup"><span data-stu-id="fe294-220">Tags</span></span>        |
|:------------------------------------------|:-----------|:---------|:------------|
| <span data-ttu-id="fe294-221">带 Angular 的 MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fe294-221">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="fe294-222">angular</span><span class="sxs-lookup"><span data-stu-id="fe294-222">angular</span></span>    | <span data-ttu-id="fe294-223">[C#]</span><span class="sxs-lookup"><span data-stu-id="fe294-223">[C#]</span></span>     | <span data-ttu-id="fe294-224">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="fe294-224">Web/MVC/SPA</span></span> |
| <span data-ttu-id="fe294-225">带有 React.js 的 MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fe294-225">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="fe294-226">react</span><span class="sxs-lookup"><span data-stu-id="fe294-226">react</span></span>      | <span data-ttu-id="fe294-227">[C#]</span><span class="sxs-lookup"><span data-stu-id="fe294-227">[C#]</span></span>     | <span data-ttu-id="fe294-228">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="fe294-228">Web/MVC/SPA</span></span> |
| <span data-ttu-id="fe294-229">含 React.js 和 Redux 的 MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fe294-229">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="fe294-230">reactredux</span><span class="sxs-lookup"><span data-stu-id="fe294-230">reactredux</span></span> | <span data-ttu-id="fe294-231">[C#]</span><span class="sxs-lookup"><span data-stu-id="fe294-231">[C#]</span></span>     | <span data-ttu-id="fe294-232">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="fe294-232">Web/MVC/SPA</span></span> |

<span data-ttu-id="fe294-233">若要使用 SPA 模板之一创建新项目，请在 [dotnet new](/dotnet/core/tools/dotnet-new) 命令中包括模板的**短名称**。</span><span class="sxs-lookup"><span data-stu-id="fe294-233">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="fe294-234">以下命令创建 Angular 应用程序，并为服务器端配置 ASP.NET Core MVC：
</span><span class="sxs-lookup"><span data-stu-id="fe294-234">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```console
dotnet new angular
```

<a name="runtime-config-mode"></a>

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="fe294-235">设置运行时配置模式</span><span class="sxs-lookup"><span data-stu-id="fe294-235">Set the runtime configuration mode</span></span>

<span data-ttu-id="fe294-236">存在两种主要的运行时配置模式：</span><span class="sxs-lookup"><span data-stu-id="fe294-236">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="fe294-237">**开发**:</span><span class="sxs-lookup"><span data-stu-id="fe294-237">**Development**:</span></span>
  * <span data-ttu-id="fe294-238">包括源映射，以便进行调试。</span><span class="sxs-lookup"><span data-stu-id="fe294-238">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="fe294-239">不会优化性能的客户端代码。</span><span class="sxs-lookup"><span data-stu-id="fe294-239">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="fe294-240">**生产**:</span><span class="sxs-lookup"><span data-stu-id="fe294-240">**Production**:</span></span>
  * <span data-ttu-id="fe294-241">不包括源映射。</span><span class="sxs-lookup"><span data-stu-id="fe294-241">Excludes source maps.</span></span>
  * <span data-ttu-id="fe294-242">可优化通过捆绑和缩小客户端代码。</span><span class="sxs-lookup"><span data-stu-id="fe294-242">Optimizes the client-side code via bundling & minification.</span></span>

<span data-ttu-id="fe294-243">ASP.NET Core 使用名为的环境变量`ASPNETCORE_ENVIRONMENT`来存储配置模式。</span><span class="sxs-lookup"><span data-stu-id="fe294-243">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="fe294-244">请参阅 **[将环境设置](xref:fundamentals/environments#set-the-environment)** 有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="fe294-244">See **[Set the environment](xref:fundamentals/environments#set-the-environment)** for more information.</span></span>

### <a name="running-with-net-core-cli"></a><span data-ttu-id="fe294-245">运行使用.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="fe294-245">Running with .NET Core CLI</span></span>

<span data-ttu-id="fe294-246">通过在项目根目录运行以下命令还原所需的 NuGet 和 npm 包：</span><span class="sxs-lookup"><span data-stu-id="fe294-246">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```console
dotnet restore && npm i
```

<span data-ttu-id="fe294-247">生成并运行应用程序：</span><span class="sxs-lookup"><span data-stu-id="fe294-247">Build and run the application:</span></span>

```console
dotnet run
```

<span data-ttu-id="fe294-248">在应用程序根据本地主机上启动[运行时配置模式](#runtime-config-mode)。</span><span class="sxs-lookup"><span data-stu-id="fe294-248">The application starts on localhost according to the [runtime configuration mode](#runtime-config-mode).</span></span> <span data-ttu-id="fe294-249">导航到 `http://localhost:5000` 在浏览器中显示的登录页。</span><span class="sxs-lookup"><span data-stu-id="fe294-249">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="running-with-visual-studio-2017"></a><span data-ttu-id="fe294-250">运行使用 Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="fe294-250">Running with Visual Studio 2017</span></span>

<span data-ttu-id="fe294-251">打开 *.csproj*生成的文件[dotnet 新](/dotnet/core/tools/dotnet-new)命令。</span><span class="sxs-lookup"><span data-stu-id="fe294-251">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="fe294-252">在项目中打开时自动还原所需的 NuGet 和 npm 包。</span><span class="sxs-lookup"><span data-stu-id="fe294-252">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="fe294-253">此还原过程可能需要几分钟时间，并在应用程序已准备好在它完成后运行。</span><span class="sxs-lookup"><span data-stu-id="fe294-253">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="fe294-254">单击绿色的运行的按钮或按`Ctrl + F5`，并在浏览器打开到应用程序的登录页。</span><span class="sxs-lookup"><span data-stu-id="fe294-254">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="fe294-255">应用程序运行于 localhost 根据[运行时配置模式](#runtime-config-mode)。</span><span class="sxs-lookup"><span data-stu-id="fe294-255">The application runs on localhost according to the [runtime configuration mode](#runtime-config-mode).</span></span>

<a name="app-testing"></a>

## <a name="testing-the-app"></a><span data-ttu-id="fe294-256">测试应用程序</span><span class="sxs-lookup"><span data-stu-id="fe294-256">Testing the app</span></span>

<span data-ttu-id="fe294-257">SpaServices 模板是预配置为运行客户端的测试使用[Karma](https://karma-runner.github.io/1.0/index.html)并[Jasmine](https://jasmine.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="fe294-257">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="fe294-258">Jasmine 是常用的单元测试框架，适用于 JavaScript，而 Karma 是这些测试的测试运行程序。</span><span class="sxs-lookup"><span data-stu-id="fe294-258">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="fe294-259">Karma 配置为使用[Webpack 开发中间件](#webpack-dev-middleware)这样开发人员不需要停止并运行测试，每次进行更改。</span><span class="sxs-lookup"><span data-stu-id="fe294-259">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="fe294-260">无论是针对测试用例或测试用例本身运行的代码，则将自动运行测试。</span><span class="sxs-lookup"><span data-stu-id="fe294-260">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="fe294-261">以 Angular 应用程序为例，我们在 `CounterComponent`文件中为 *counter.component.spec.ts*提供了两个 Jasmine 测试用例：</span><span class="sxs-lookup"><span data-stu-id="fe294-261">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="fe294-262">打开命令提示符中*ClientApp*目录。</span><span class="sxs-lookup"><span data-stu-id="fe294-262">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="fe294-263">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="fe294-263">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="fe294-264">该脚本将启动 Karma 测试运行程序，其内容中定义的设置*karma.conf.js*文件。</span><span class="sxs-lookup"><span data-stu-id="fe294-264">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="fe294-265">在其他设置*karma.conf.js*识别的测试文件，通过执行其`files`数组：</span><span class="sxs-lookup"><span data-stu-id="fe294-265">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

<a name="app-publishing"></a>

## <a name="publishing-the-application"></a><span data-ttu-id="fe294-266">发布应用程序</span><span class="sxs-lookup"><span data-stu-id="fe294-266">Publishing the application</span></span>

<span data-ttu-id="fe294-267">将生成的客户端的资产和已发布的 ASP.NET Core 项目合并为随时可部署的包可能会很麻烦。</span><span class="sxs-lookup"><span data-stu-id="fe294-267">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="fe294-268">幸运的是，SpaServices 协调与名为的自定义 MSBuild 目标的整个发布过程`RunWebpack`:</span><span class="sxs-lookup"><span data-stu-id="fe294-268">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="fe294-269">MSBuild 目标具有下列职责：</span><span class="sxs-lookup"><span data-stu-id="fe294-269">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="fe294-270">还原 npm 包</span><span class="sxs-lookup"><span data-stu-id="fe294-270">Restore the npm packages</span></span>
1. <span data-ttu-id="fe294-271">创建第三方、 客户端的资产的生产级版本</span><span class="sxs-lookup"><span data-stu-id="fe294-271">Create a production-grade build of the third-party, client-side assets</span></span>
1. <span data-ttu-id="fe294-272">创建自定义客户端的资产的生产级版本</span><span class="sxs-lookup"><span data-stu-id="fe294-272">Create a production-grade build of the custom client-side assets</span></span>
1. <span data-ttu-id="fe294-273">Webpack 生成资产复制到 publish 文件夹</span><span class="sxs-lookup"><span data-stu-id="fe294-273">Copy the Webpack-generated assets to the publish folder</span></span>

<span data-ttu-id="fe294-274">运行时，会调用 MSBuild 目标：</span><span class="sxs-lookup"><span data-stu-id="fe294-274">The MSBuild target is invoked when running:</span></span>

```console
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="fe294-275">其他资源</span><span class="sxs-lookup"><span data-stu-id="fe294-275">Additional resources</span></span>

* [<span data-ttu-id="fe294-276">Angular 文档</span><span class="sxs-lookup"><span data-stu-id="fe294-276">Angular Docs</span></span>](https://angular.io/docs)
