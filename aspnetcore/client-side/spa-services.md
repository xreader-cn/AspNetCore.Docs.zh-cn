---
title: 使用 JavaScript 服务在 ASP.NET Core 中创建单页面应用程序
author: scottaddie
description: 了解使用 JavaScript 服务创建由 ASP.NET Core 支持的单页面应用程序（SPA）的好处。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: 7aff46f739239246191763e0590046b2d9995922
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080510"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="595ca-103">使用 JavaScript 服务在 ASP.NET Core 中创建单页面应用程序</span><span class="sxs-lookup"><span data-stu-id="595ca-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="595ca-104">通过[Scott Addie](https://github.com/scottaddie)和[Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="595ca-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="595ca-105">单页应用程序 (SPA) 因其固有的丰富用户体验而成为一种常用的 Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="595ca-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="595ca-106">将客户端 SPA 框架或库（如[角度](https://angular.io/)或[响应](https://facebook.github.io/react/)）与服务器端框架（如 ASP.NET Core）相集成可能比较困难。</span><span class="sxs-lookup"><span data-stu-id="595ca-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="595ca-107">开发 JavaScript 服务是为了减少集成过程中的摩擦。</span><span class="sxs-lookup"><span data-stu-id="595ca-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="595ca-108">使用它可以在不同的客户端和服务器技术堆栈之间进行无缝操作。</span><span class="sxs-lookup"><span data-stu-id="595ca-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="595ca-109">本文中所述的功能从 ASP.NET Core 3.0 过时。</span><span class="sxs-lookup"><span data-stu-id="595ca-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="595ca-110">[SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet 包中提供了更简单的 SPA 框架集成机制。</span><span class="sxs-lookup"><span data-stu-id="595ca-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="595ca-111">有关详细信息，请参阅[[公告] Obsoleting AspNetCore. SpaServices 和 AspNetCore。](https://github.com/aspnet/AspNetCore/issues/12890)</span><span class="sxs-lookup"><span data-stu-id="595ca-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/aspnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="595ca-112">什么是 JavaScript 服务</span><span class="sxs-lookup"><span data-stu-id="595ca-112">What is JavaScript Services</span></span>

<span data-ttu-id="595ca-113">JavaScript 服务是用于 ASP.NET Core 的客户端技术的集合。</span><span class="sxs-lookup"><span data-stu-id="595ca-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="595ca-114">其目标是将 ASP.NET Core 定位为开发人员用于构建 SPA 的首选服务器端平台。</span><span class="sxs-lookup"><span data-stu-id="595ca-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="595ca-115">JavaScript 服务由两个不同的 NuGet 包组成：</span><span class="sxs-lookup"><span data-stu-id="595ca-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="595ca-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="595ca-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="595ca-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="595ca-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="595ca-118">这些包在以下情况下很有用：</span><span class="sxs-lookup"><span data-stu-id="595ca-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="595ca-119">在服务器上运行 JavaScript</span><span class="sxs-lookup"><span data-stu-id="595ca-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="595ca-120">使用 SPA 框架或库</span><span class="sxs-lookup"><span data-stu-id="595ca-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="595ca-121">生成客户端的资产的 Webpack</span><span class="sxs-lookup"><span data-stu-id="595ca-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="595ca-122">在本文的重点放在使用 SpaServices 包。</span><span class="sxs-lookup"><span data-stu-id="595ca-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="595ca-123">什么是 SpaServices</span><span class="sxs-lookup"><span data-stu-id="595ca-123">What is SpaServices</span></span>

<span data-ttu-id="595ca-124">创建 SpaServices 是为了将 ASP.NET Core 定位为开发人员构建 SPA 的首选服务器端平台。</span><span class="sxs-lookup"><span data-stu-id="595ca-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="595ca-125">SpaServices 不需要使用 ASP.NET Core 开发 Spa，它也不会将开发人员锁定到特定的客户端框架。</span><span class="sxs-lookup"><span data-stu-id="595ca-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="595ca-126">SpaServices 提供有用的基础结构，例如：</span><span class="sxs-lookup"><span data-stu-id="595ca-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="595ca-127">服务器端预呈现</span><span class="sxs-lookup"><span data-stu-id="595ca-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="595ca-128">Webpack 开发中间件</span><span class="sxs-lookup"><span data-stu-id="595ca-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="595ca-129">热模块更换</span><span class="sxs-lookup"><span data-stu-id="595ca-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="595ca-130">路由帮助程序</span><span class="sxs-lookup"><span data-stu-id="595ca-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="595ca-131">总体来说，这些基础结构组件增强了开发工作流和运行时体验。</span><span class="sxs-lookup"><span data-stu-id="595ca-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="595ca-132">组件可单独采用。</span><span class="sxs-lookup"><span data-stu-id="595ca-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="595ca-133">使用 SpaServices 的先决条件</span><span class="sxs-lookup"><span data-stu-id="595ca-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="595ca-134">若要使用 SpaServices，安装以下组件：</span><span class="sxs-lookup"><span data-stu-id="595ca-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="595ca-135">[Node.js](https://nodejs.org/) （6 或更高版本） 与 npm</span><span class="sxs-lookup"><span data-stu-id="595ca-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="595ca-136">若要验证这些组件安装，并可找到，运行以下命令从命令行：</span><span class="sxs-lookup"><span data-stu-id="595ca-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="595ca-137">如果要部署到 Azure 网站，则无需&mdash;执行任何操作 node.js 已安装，并且在服务器环境中可用。</span><span class="sxs-lookup"><span data-stu-id="595ca-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="595ca-138">在使用 Visual Studio 2017 的 Windows 上，通过选择 **.Net Core 跨平台开发**工作负载来安装 SDK。</span><span class="sxs-lookup"><span data-stu-id="595ca-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="595ca-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet 包</span><span class="sxs-lookup"><span data-stu-id="595ca-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="595ca-140">服务器端预呈现</span><span class="sxs-lookup"><span data-stu-id="595ca-140">Server-side prerendering</span></span>

<span data-ttu-id="595ca-141">通用（也称“同构”）应用程序是在服务器和客户端上都能运行的 JavaScript 应用程序。</span><span class="sxs-lookup"><span data-stu-id="595ca-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="595ca-142">Angular、React 和其他常用框架提供了一个适合此应用程序开发风格的通用平台。</span><span class="sxs-lookup"><span data-stu-id="595ca-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="595ca-143">这其中的理念是，先通过 Node.js 在服务器上呈现框架组件，然后将下一步的执行操作委托到客户端。</span><span class="sxs-lookup"><span data-stu-id="595ca-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="595ca-144">ASP.NET Core[标记帮助程序](xref:mvc/views/tag-helpers/intro)由 SpaServices 简化通过调用服务器上的 JavaScript 函数的服务器端预呈现的实现。</span><span class="sxs-lookup"><span data-stu-id="595ca-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="595ca-145">服务器端预呈现必备组件</span><span class="sxs-lookup"><span data-stu-id="595ca-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="595ca-146">安装[aspnet 预呈现的](https://www.npmjs.com/package/aspnet-prerendering)npm 包：</span><span class="sxs-lookup"><span data-stu-id="595ca-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="595ca-147">服务器端预呈现配置</span><span class="sxs-lookup"><span data-stu-id="595ca-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="595ca-148">标记帮助程序在项目的供发现通过命名空间注册 *_ViewImports.cshtml*文件：</span><span class="sxs-lookup"><span data-stu-id="595ca-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="595ca-149">这些标记帮助程序抽象出与低级 Api 直接通信通过利用 Razor 视图中的类似于 HTML 的语法的复杂性：</span><span class="sxs-lookup"><span data-stu-id="595ca-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="595ca-150">asp 预呈现模块标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="595ca-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="595ca-151">`asp-prerender-module`标记帮助程序，使用在前面的代码示例中，执行*ClientApp/dist/main-server.js*通过 Node.js 服务器上。</span><span class="sxs-lookup"><span data-stu-id="595ca-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="595ca-152">为清晰起见*main server.js*文件是一个项目中的 TypeScript JavaScript 的转译任务[Webpack](https://webpack.github.io/)生成过程。</span><span class="sxs-lookup"><span data-stu-id="595ca-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="595ca-153">Webpack 定义入口点的别名`main-server`; 并遍历此别名的依赖项关系图的开始处*ClientApp/启动 server.ts*文件：</span><span class="sxs-lookup"><span data-stu-id="595ca-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="595ca-154">在下述 Angular 示例中， *ClientApp/boot-server.ts*文件利用`aspnet-prerendering`npm 包的`createServerRenderer`函数和`RenderResult`类型通过 Node.js 来配置服务器呈现。</span><span class="sxs-lookup"><span data-stu-id="595ca-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="595ca-155">需要在服务器端呈现的 HTML 标记会传递给一个解析函数调用，该调用包装在强类型化的 JavaScript `Promise` 对象中。</span><span class="sxs-lookup"><span data-stu-id="595ca-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="595ca-156">`Promise`对象的意义在于，它以异步方式将 HTML 标记提供给页面，以便该标记能够注入到 DOM 的占位符元素中。</span><span class="sxs-lookup"><span data-stu-id="595ca-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="595ca-157">asp 预呈现-数据标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="595ca-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="595ca-158">当结合`asp-prerender-module`标记帮助程序`asp-prerender-data`标记帮助程序可用于将从 Razor 视图的上下文信息传递到服务器端 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="595ca-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="595ca-159">例如，以下标记将传递到的用户数据`main-server`模块：</span><span class="sxs-lookup"><span data-stu-id="595ca-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="595ca-160">收到`UserName`参数使用内置的 JSON 序列化程序序列化并存储在`params.data`对象。</span><span class="sxs-lookup"><span data-stu-id="595ca-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="595ca-161">在以下 Angular 示例中，数据用于在 `h1`元素中构造个性化的问候语：</span><span class="sxs-lookup"><span data-stu-id="595ca-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="595ca-162">在标记帮助器中传递的属性名称用**PascalCase**表示法表示。</span><span class="sxs-lookup"><span data-stu-id="595ca-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="595ca-163">为 JavaScript，其中，相同的属性名称表示与对比**驼峰式大小写**。</span><span class="sxs-lookup"><span data-stu-id="595ca-163">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="595ca-164">默认 JSON 序列化配置负责这种差异。</span><span class="sxs-lookup"><span data-stu-id="595ca-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="595ca-165">若要展开在前面的代码示例时，数据可从服务器到视图通过传递 hydrating`globals`属性提供给`resolve`函数：</span><span class="sxs-lookup"><span data-stu-id="595ca-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="595ca-166">`postList`内部定义的数组`globals`对象附加到浏览器的全局`window`对象。</span><span class="sxs-lookup"><span data-stu-id="595ca-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="595ca-167">为全局作用域此变量提升可消除重复工作，特别是因为它与加载一次在服务器上，再次在客户端上的相同数据。</span><span class="sxs-lookup"><span data-stu-id="595ca-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![附加到窗口对象的全局 postList 变量](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="595ca-169">Webpack 开发中间件</span><span class="sxs-lookup"><span data-stu-id="595ca-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="595ca-170">[Webpack 开发中间件](https://webpack.js.org/guides/development/#using-webpack-dev-middleware)引入了 Webpack 按需生成资源的由此简化了的开发工作流。</span><span class="sxs-lookup"><span data-stu-id="595ca-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="595ca-171">中间件会自动编译并在浏览器中重新加载页面时提供客户端的资源。</span><span class="sxs-lookup"><span data-stu-id="595ca-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="595ca-172">另一种方法是手动 Webpack 调用通过项目的 npm 生成脚本的第三方依赖项或自定义代码发生更改时。</span><span class="sxs-lookup"><span data-stu-id="595ca-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="595ca-173">Npm 生成脚本*package.json*文件显示在下面的示例：</span><span class="sxs-lookup"><span data-stu-id="595ca-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="595ca-174">Webpack Dev 中间件先决条件</span><span class="sxs-lookup"><span data-stu-id="595ca-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="595ca-175">安装[webpack](https://www.npmjs.com/package/aspnet-webpack) npm 包：</span><span class="sxs-lookup"><span data-stu-id="595ca-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="595ca-176">Webpack Dev 中间件配置</span><span class="sxs-lookup"><span data-stu-id="595ca-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="595ca-177">到 HTTP 请求管道中的以下代码通过注册 Webpack 开发中间件*Startup.cs*文件的`Configure`方法：</span><span class="sxs-lookup"><span data-stu-id="595ca-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="595ca-178">`UseWebpackDevMiddleware`前必须调用扩展方法[注册静态文件托管](xref:fundamentals/static-files)通过`UseStaticFiles`扩展方法。</span><span class="sxs-lookup"><span data-stu-id="595ca-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="595ca-179">出于安全原因，仅当应用程序在开发模式下运行时注册该中间件。</span><span class="sxs-lookup"><span data-stu-id="595ca-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="595ca-180">*Webpack.config.js*文件的`output.publicPath`属性会指示要观看的中间件`dist`文件夹的更改：</span><span class="sxs-lookup"><span data-stu-id="595ca-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="595ca-181">热模块更换</span><span class="sxs-lookup"><span data-stu-id="595ca-181">Hot Module Replacement</span></span>

<span data-ttu-id="595ca-182">Webpack 的思考[动态模块更换](https://webpack.js.org/concepts/hot-module-replacement/)(HMR) 功能作为一种演变[Webpack 开发中间件](#webpack-dev-middleware)。</span><span class="sxs-lookup"><span data-stu-id="595ca-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="595ca-183">HMR 引入了完全相同的好处，但它进一步简化开发工作流通过自动编译所做的更改后更新页面内容。</span><span class="sxs-lookup"><span data-stu-id="595ca-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="595ca-184">不要混淆这与刷新浏览器中，这会干扰的当前内存中状态和 SPA 的调试会话。</span><span class="sxs-lookup"><span data-stu-id="595ca-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="595ca-185">没有 Webpack 开发中间件服务和浏览器中，这意味着更改推送到浏览器之间的活动链接。</span><span class="sxs-lookup"><span data-stu-id="595ca-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="595ca-186">热模块替换先决条件</span><span class="sxs-lookup"><span data-stu-id="595ca-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="595ca-187">安装[webpack-](https://www.npmjs.com/package/webpack-hot-middleware) npm 包：</span><span class="sxs-lookup"><span data-stu-id="595ca-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="595ca-188">热模块替换配置</span><span class="sxs-lookup"><span data-stu-id="595ca-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="595ca-189">HMR 组件必须注册到 MVC 的 HTTP 请求管道中`Configure`方法：</span><span class="sxs-lookup"><span data-stu-id="595ca-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="595ca-190">作为是如此[Webpack 开发中间件](#webpack-dev-middleware)，则`UseWebpackDevMiddleware`前必须调用扩展方法`UseStaticFiles`扩展方法。</span><span class="sxs-lookup"><span data-stu-id="595ca-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="595ca-191">出于安全原因，仅当应用程序在开发模式下运行时注册该中间件。</span><span class="sxs-lookup"><span data-stu-id="595ca-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="595ca-192">*Webpack.config.js*文件必须定义`plugins`，即使它保留为空数组：</span><span class="sxs-lookup"><span data-stu-id="595ca-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="595ca-193">加载后在浏览器中的应用，开发人员工具的控制台选项卡提供了 HMR 激活的确认：</span><span class="sxs-lookup"><span data-stu-id="595ca-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![热模块更换已连接的消息](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="595ca-195">路由帮助程序</span><span class="sxs-lookup"><span data-stu-id="595ca-195">Routing helpers</span></span>

<span data-ttu-id="595ca-196">在大多数基于 ASP.NET Core 的 Spa 中，通常还需要客户端路由，而不是服务器端路由。</span><span class="sxs-lookup"><span data-stu-id="595ca-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="595ca-197">SPA 和 MVC 路由系统可以不受干扰地独立工作。</span><span class="sxs-lookup"><span data-stu-id="595ca-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="595ca-198">没有，但是，一个边缘事例造成面临的难题： 标识 404 HTTP 响应。</span><span class="sxs-lookup"><span data-stu-id="595ca-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="595ca-199">请考虑在该方案中的无扩展名路由`/some/page`使用。</span><span class="sxs-lookup"><span data-stu-id="595ca-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="595ca-200">假定该请求不模式匹配的服务器端的路由，但其模式匹配的客户端的路由。</span><span class="sxs-lookup"><span data-stu-id="595ca-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="595ca-201">现在，考虑的传入请求`/images/user-512.png`，这通常需要查找服务器上的图像文件。</span><span class="sxs-lookup"><span data-stu-id="595ca-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="595ca-202">如果请求的资源路径与任何服务器端路由或静态文件都不匹配，则客户端应用程序可能会处理它，但&mdash;通常不需要返回 404 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="595ca-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="595ca-203">路由帮助程序先决条件</span><span class="sxs-lookup"><span data-stu-id="595ca-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="595ca-204">安装客户端路由 npm 包。</span><span class="sxs-lookup"><span data-stu-id="595ca-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="595ca-205">使用 Angular 作为示例：</span><span class="sxs-lookup"><span data-stu-id="595ca-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="595ca-206">路由帮助器配置</span><span class="sxs-lookup"><span data-stu-id="595ca-206">Routing helpers configuration</span></span>

<span data-ttu-id="595ca-207">名为的扩展方法`MapSpaFallbackRoute`中使用`Configure`方法：</span><span class="sxs-lookup"><span data-stu-id="595ca-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="595ca-208">路由按其配置顺序进行评估。</span><span class="sxs-lookup"><span data-stu-id="595ca-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="595ca-209">因此，`default`进行模式匹配第一次使用在前面的代码示例中的路由。</span><span class="sxs-lookup"><span data-stu-id="595ca-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="595ca-210">创建新项目</span><span class="sxs-lookup"><span data-stu-id="595ca-210">Create a new project</span></span>

<span data-ttu-id="595ca-211">JavaScript 服务提供预配置的应用程序模板。</span><span class="sxs-lookup"><span data-stu-id="595ca-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="595ca-212">在这些模板中，SpaServices 与不同的框架和库（如角度、反应和 Redux）一起使用。</span><span class="sxs-lookup"><span data-stu-id="595ca-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="595ca-213">可以通过.NET Core CLI 安装这些模板，通过运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="595ca-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="595ca-214">显示可用的 SPA 模板的列表：</span><span class="sxs-lookup"><span data-stu-id="595ca-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="595ca-215">模板</span><span class="sxs-lookup"><span data-stu-id="595ca-215">Templates</span></span>                                 | <span data-ttu-id="595ca-216">短名称</span><span class="sxs-lookup"><span data-stu-id="595ca-216">Short Name</span></span> | <span data-ttu-id="595ca-217">语言</span><span class="sxs-lookup"><span data-stu-id="595ca-217">Language</span></span> | <span data-ttu-id="595ca-218">Tags</span><span class="sxs-lookup"><span data-stu-id="595ca-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="595ca-219">带 Angular 的 MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="595ca-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="595ca-220">angular</span><span class="sxs-lookup"><span data-stu-id="595ca-220">angular</span></span>    | <span data-ttu-id="595ca-221">[C#]</span><span class="sxs-lookup"><span data-stu-id="595ca-221">[C#]</span></span>     | <span data-ttu-id="595ca-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="595ca-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="595ca-223">带有 React.js 的 MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="595ca-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="595ca-224">react</span><span class="sxs-lookup"><span data-stu-id="595ca-224">react</span></span>      | <span data-ttu-id="595ca-225">[C#]</span><span class="sxs-lookup"><span data-stu-id="595ca-225">[C#]</span></span>     | <span data-ttu-id="595ca-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="595ca-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="595ca-227">含 React.js 和 Redux 的 MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="595ca-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="595ca-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="595ca-228">reactredux</span></span> | <span data-ttu-id="595ca-229">[C#]</span><span class="sxs-lookup"><span data-stu-id="595ca-229">[C#]</span></span>     | <span data-ttu-id="595ca-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="595ca-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="595ca-231">若要使用 SPA 模板之一创建新项目，请在 [dotnet new](/dotnet/core/tools/dotnet-new) 命令中包括模板的**短名称**。</span><span class="sxs-lookup"><span data-stu-id="595ca-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="595ca-232">以下命令创建 Angular 应用程序，并为服务器端配置 ASP.NET Core MVC：</span><span class="sxs-lookup"><span data-stu-id="595ca-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="595ca-233">设置运行时配置模式</span><span class="sxs-lookup"><span data-stu-id="595ca-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="595ca-234">存在两种主要的运行时配置模式：</span><span class="sxs-lookup"><span data-stu-id="595ca-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="595ca-235">**开发**:</span><span class="sxs-lookup"><span data-stu-id="595ca-235">**Development**:</span></span>
  * <span data-ttu-id="595ca-236">包括源映射，以便进行调试。</span><span class="sxs-lookup"><span data-stu-id="595ca-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="595ca-237">不会优化性能的客户端代码。</span><span class="sxs-lookup"><span data-stu-id="595ca-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="595ca-238">**生产**:</span><span class="sxs-lookup"><span data-stu-id="595ca-238">**Production**:</span></span>
  * <span data-ttu-id="595ca-239">不包括源映射。</span><span class="sxs-lookup"><span data-stu-id="595ca-239">Excludes source maps.</span></span>
  * <span data-ttu-id="595ca-240">通过绑定和缩减优化客户端代码。</span><span class="sxs-lookup"><span data-stu-id="595ca-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="595ca-241">ASP.NET Core 使用名为的环境变量`ASPNETCORE_ENVIRONMENT`来存储配置模式。</span><span class="sxs-lookup"><span data-stu-id="595ca-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="595ca-242">有关详细信息，请参阅[设置环境](xref:fundamentals/environments#set-the-environment)。</span><span class="sxs-lookup"><span data-stu-id="595ca-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="595ca-243">运行 .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="595ca-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="595ca-244">通过在项目根目录运行以下命令还原所需的 NuGet 和 npm 包：</span><span class="sxs-lookup"><span data-stu-id="595ca-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="595ca-245">生成并运行应用程序：</span><span class="sxs-lookup"><span data-stu-id="595ca-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="595ca-246">在应用程序根据本地主机上启动[运行时配置模式](#set-the-runtime-configuration-mode)。</span><span class="sxs-lookup"><span data-stu-id="595ca-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="595ca-247">导航到 `http://localhost:5000` 在浏览器中显示的登录页。</span><span class="sxs-lookup"><span data-stu-id="595ca-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="595ca-248">运行 Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="595ca-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="595ca-249">打开 *.csproj*生成的文件[dotnet 新](/dotnet/core/tools/dotnet-new)命令。</span><span class="sxs-lookup"><span data-stu-id="595ca-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="595ca-250">在项目中打开时自动还原所需的 NuGet 和 npm 包。</span><span class="sxs-lookup"><span data-stu-id="595ca-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="595ca-251">此还原过程可能需要几分钟时间，并在应用程序已准备好在它完成后运行。</span><span class="sxs-lookup"><span data-stu-id="595ca-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="595ca-252">单击绿色的运行的按钮或按`Ctrl + F5`，并在浏览器打开到应用程序的登录页。</span><span class="sxs-lookup"><span data-stu-id="595ca-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="595ca-253">应用程序运行于 localhost 根据[运行时配置模式](#set-the-runtime-configuration-mode)。</span><span class="sxs-lookup"><span data-stu-id="595ca-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="595ca-254">测试应用</span><span class="sxs-lookup"><span data-stu-id="595ca-254">Test the app</span></span>

<span data-ttu-id="595ca-255">SpaServices 模板是预配置为运行客户端的测试使用[Karma](https://karma-runner.github.io/1.0/index.html)并[Jasmine](https://jasmine.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="595ca-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="595ca-256">Jasmine 是常用的单元测试框架，适用于 JavaScript，而 Karma 是这些测试的测试运行程序。</span><span class="sxs-lookup"><span data-stu-id="595ca-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="595ca-257">Karma 配置为使用[Webpack 开发中间件](#webpack-dev-middleware)这样开发人员不需要停止并运行测试，每次进行更改。</span><span class="sxs-lookup"><span data-stu-id="595ca-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="595ca-258">无论是针对测试用例或测试用例本身运行的代码，则将自动运行测试。</span><span class="sxs-lookup"><span data-stu-id="595ca-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="595ca-259">以 Angular 应用程序为例，我们在 `CounterComponent`文件中为 *counter.component.spec.ts*提供了两个 Jasmine 测试用例：</span><span class="sxs-lookup"><span data-stu-id="595ca-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="595ca-260">打开命令提示符中*ClientApp*目录。</span><span class="sxs-lookup"><span data-stu-id="595ca-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="595ca-261">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="595ca-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="595ca-262">该脚本将启动 Karma 测试运行程序，其内容中定义的设置*karma.conf.js*文件。</span><span class="sxs-lookup"><span data-stu-id="595ca-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="595ca-263">在其他设置*karma.conf.js*识别的测试文件，通过执行其`files`数组：</span><span class="sxs-lookup"><span data-stu-id="595ca-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="595ca-264">发布应用</span><span class="sxs-lookup"><span data-stu-id="595ca-264">Publish the app</span></span>

<span data-ttu-id="595ca-265">将生成的客户端的资产和已发布的 ASP.NET Core 项目合并为随时可部署的包可能会很麻烦。</span><span class="sxs-lookup"><span data-stu-id="595ca-265">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="595ca-266">幸运的是，SpaServices 协调与名为的自定义 MSBuild 目标的整个发布过程`RunWebpack`:</span><span class="sxs-lookup"><span data-stu-id="595ca-266">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="595ca-267">MSBuild 目标具有下列职责：</span><span class="sxs-lookup"><span data-stu-id="595ca-267">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="595ca-268">还原 npm 包。</span><span class="sxs-lookup"><span data-stu-id="595ca-268">Restore the npm packages.</span></span>
1. <span data-ttu-id="595ca-269">创建第三方客户端资产的生产级生成。</span><span class="sxs-lookup"><span data-stu-id="595ca-269">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="595ca-270">创建自定义客户端资产的生产级生成。</span><span class="sxs-lookup"><span data-stu-id="595ca-270">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="595ca-271">将 Webpack 生成的资产复制到 "发布" 文件夹。</span><span class="sxs-lookup"><span data-stu-id="595ca-271">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="595ca-272">运行时，会调用 MSBuild 目标：</span><span class="sxs-lookup"><span data-stu-id="595ca-272">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="595ca-273">其他资源</span><span class="sxs-lookup"><span data-stu-id="595ca-273">Additional resources</span></span>

* [<span data-ttu-id="595ca-274">Angular 文档</span><span class="sxs-lookup"><span data-stu-id="595ca-274">Angular Docs</span></span>](https://angular.io/docs)
