---
title: 使用 JavaScript Services 在 ASP.NET Core 中创建单页应用程序
author: scottaddie
description: 了解使用 JavaScript Services 创建由 ASP.NET Core 提供支持的单页应用程序 (SPA) 的好处。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/spa-services
ms.openlocfilehash: 65bd5157bb3909f8352debcb1a6dfa7d888eec0e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769918"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="87542-103">使用 JavaScript Services 在 ASP.NET Core 中创建单页应用程序</span><span class="sxs-lookup"><span data-stu-id="87542-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="87542-104">作者：[Scott Addie](https://github.com/scottaddie) 和 [Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="87542-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="87542-105">单页应用程序 (SPA) 因其固有的丰富用户体验而成为一种常用的 Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="87542-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="87542-106">将客户端 SPA 框架或库（例如 [Angular](https://angular.io/) 或 [React](https://facebook.github.io/react/)）与服务器端框架（例如 ASP.NET Core）集成在一起可能会很困难。</span><span class="sxs-lookup"><span data-stu-id="87542-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="87542-107">开发 JavaScript Services 就是为了减少集成过程中的摩擦。</span><span class="sxs-lookup"><span data-stu-id="87542-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="87542-108">使用它可以在不同的客户端和服务器技术堆栈之间无缝操作。</span><span class="sxs-lookup"><span data-stu-id="87542-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="87542-109">本文所述的功能自 ASP.NET Core 3.0 起被弃用。</span><span class="sxs-lookup"><span data-stu-id="87542-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="87542-110">[Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet 包提供了一种更简单的 SPA 框架集成机制。</span><span class="sxs-lookup"><span data-stu-id="87542-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="87542-111">有关详细信息，请参阅 [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890)（[公告] 弃用 Microsoft.AspNetCore.SpaServices 和 Microsoft.AspNetCore.NodeServices）。</span><span class="sxs-lookup"><span data-stu-id="87542-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="87542-112">什么是 JavaScript Services</span><span class="sxs-lookup"><span data-stu-id="87542-112">What is JavaScript Services</span></span>

<span data-ttu-id="87542-113">JavaScript Services 是用于 ASP.NET Core 的客户端技术集合。</span><span class="sxs-lookup"><span data-stu-id="87542-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="87542-114">其目标是将 ASP.NET Core 定位为开发人员生成 SPA 时的首选服务器端平台。</span><span class="sxs-lookup"><span data-stu-id="87542-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="87542-115">JavaScript Services 由两个不同的 NuGet 包组成：</span><span class="sxs-lookup"><span data-stu-id="87542-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="87542-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="87542-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="87542-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="87542-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="87542-118">这些包在以下情况下很有用：</span><span class="sxs-lookup"><span data-stu-id="87542-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="87542-119">在服务器上运行 JavaScript</span><span class="sxs-lookup"><span data-stu-id="87542-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="87542-120">使用 SPA 框架或库</span><span class="sxs-lookup"><span data-stu-id="87542-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="87542-121">通过 Webpack 生成客户端资产</span><span class="sxs-lookup"><span data-stu-id="87542-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="87542-122">本文重点介绍了如何使用 SpaServices 包。</span><span class="sxs-lookup"><span data-stu-id="87542-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="87542-123">什么是 SpaServices</span><span class="sxs-lookup"><span data-stu-id="87542-123">What is SpaServices</span></span>

<span data-ttu-id="87542-124">创建 SpaServices 的目的是将 ASP.NET Core 定位为开发人员生成 SPA 时的首选服务器端平台。</span><span class="sxs-lookup"><span data-stu-id="87542-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="87542-125">使用 ASP.NET Core 开发 SPA 时不一定要使用 SpaServices，SpaServices 也不会将开发人员束缚在特定的客户端框架中。</span><span class="sxs-lookup"><span data-stu-id="87542-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="87542-126">SpaServices 可提供有用的基础结构，例如：</span><span class="sxs-lookup"><span data-stu-id="87542-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="87542-127">服务器端预呈现</span><span class="sxs-lookup"><span data-stu-id="87542-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="87542-128">Webpack 开发中间件</span><span class="sxs-lookup"><span data-stu-id="87542-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="87542-129">热模块更换</span><span class="sxs-lookup"><span data-stu-id="87542-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="87542-130">路由帮助程序</span><span class="sxs-lookup"><span data-stu-id="87542-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="87542-131">将这些基础结构组件结合使用时，可增强开发工作流和运行时体验。</span><span class="sxs-lookup"><span data-stu-id="87542-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="87542-132">这些组件也可单独使用。</span><span class="sxs-lookup"><span data-stu-id="87542-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="87542-133">使用 SpaServices 的先决条件</span><span class="sxs-lookup"><span data-stu-id="87542-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="87542-134">若要使用 SpaServices，请安装以下各项：</span><span class="sxs-lookup"><span data-stu-id="87542-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="87542-135">带有 npm 的 [Node.js](https://nodejs.org/)（版本 6 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="87542-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="87542-136">若要确保已安装并且可找到这些组件，请从命令行运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="87542-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="87542-137">如果部署到 Azure 网站，则无需执行任何操作 &mdash; 已经在服务器环境中安装 Node.js，并且 Node.js 可供使用。</span><span class="sxs-lookup"><span data-stu-id="87542-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="87542-138">在使用 Visual Studio 2017 的 Windows 上，通过选择“.NET Core 跨平台开发”工作负载来安装该 SDK  。</span><span class="sxs-lookup"><span data-stu-id="87542-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="87542-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet 包</span><span class="sxs-lookup"><span data-stu-id="87542-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="87542-140">服务器端预呈现</span><span class="sxs-lookup"><span data-stu-id="87542-140">Server-side prerendering</span></span>

<span data-ttu-id="87542-141">通用（也称为同构）应用程序是一种能够在服务器和客户端上运行的 JavaScript 应用程序。</span><span class="sxs-lookup"><span data-stu-id="87542-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="87542-142">Angular、React 和其他常用框架针对这种应用程序开发风格提供一个通用平台。</span><span class="sxs-lookup"><span data-stu-id="87542-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="87542-143">其思路是先通过 Node.js 在服务器上呈现框架组件，然后将进一步的执行任务委托给客户端。</span><span class="sxs-lookup"><span data-stu-id="87542-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="87542-144">SpaServices 提供的 ASP.NET Core [标记帮助程序](xref:mvc/views/tag-helpers/intro)通过调用服务器上的 JavaScript 函数来简化服务器端预呈现的实现。</span><span class="sxs-lookup"><span data-stu-id="87542-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="87542-145">服务器端预呈现的先决条件</span><span class="sxs-lookup"><span data-stu-id="87542-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="87542-146">安装 [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm 包：</span><span class="sxs-lookup"><span data-stu-id="87542-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="87542-147">服务器端预呈现配置</span><span class="sxs-lookup"><span data-stu-id="87542-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="87542-148">可以通过在项目的 *_ViewImports.cshtml* 文件中注册命名空间来发现标记帮助程序：</span><span class="sxs-lookup"><span data-stu-id="87542-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="87542-149">这些标记帮助程序通过在 Razor 视图中利用类似 HTML 的语法来抽象化与低级 API 直接通信的复杂性：</span><span class="sxs-lookup"><span data-stu-id="87542-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="87542-150">asp-prerender-module 标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="87542-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="87542-151">上面的代码示例中使用的 `asp-prerender-module` 标记帮助程序通过 Node.js 在服务器上执行 *ClientApp/dist/main-server.js*。</span><span class="sxs-lookup"><span data-stu-id="87542-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="87542-152">为清楚起见，*main-server.js* 文件是 [Webpack](https://webpack.github.io/) 生成过程中 TypeScript 到 JavaScript 转译任务的产物。</span><span class="sxs-lookup"><span data-stu-id="87542-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="87542-153">Webpack 定义了入口点别名 `main-server`；此别名的依赖项关系图遍历始于 *ClientApp/boot-server.ts* 文件：</span><span class="sxs-lookup"><span data-stu-id="87542-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="87542-154">在以下 Angular 示例中，*ClientApp/boot-server.ts* 文件利用 `createServerRenderer` 函数和 `RenderResult` npm 包的 `aspnet-prerendering` 类型通过 Node.js 来配置服务器呈现。</span><span class="sxs-lookup"><span data-stu-id="87542-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="87542-155">用于服务器端呈现的 HTML 标记传递到解析函数调用，该调用包装在强类型的 JavaScript `Promise` 对象中。</span><span class="sxs-lookup"><span data-stu-id="87542-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="87542-156">`Promise` 对象的意义在于，它以异步方式将 HTML 标记提供给页面，以注入到 DOM 的占位符元素中。</span><span class="sxs-lookup"><span data-stu-id="87542-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="87542-157">asp-prerender-data 标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="87542-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="87542-158">与 `asp-prerender-module` 标记帮助程序结合使用时，`asp-prerender-data` 标记帮助程序可用于将上下文信息从 Razor 视图传递到服务器端 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="87542-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="87542-159">例如，以下标记将用户数据传递到 `main-server` 模块：</span><span class="sxs-lookup"><span data-stu-id="87542-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="87542-160">收到的 `UserName` 参数使用内置的 JSON 序列化程序进行序列化，并存储在 `params.data` 对象中。</span><span class="sxs-lookup"><span data-stu-id="87542-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="87542-161">在以下 Angular 示例中，该数据用于在 `h1` 元素内构造个性化问候语：</span><span class="sxs-lookup"><span data-stu-id="87542-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="87542-162">在标记帮助程序中传递的属性名称用 **PascalCase** 表示法表示。</span><span class="sxs-lookup"><span data-stu-id="87542-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="87542-163">与之相反，JavaScript 用 **camelCase** 表示相同的属性名称。</span><span class="sxs-lookup"><span data-stu-id="87542-163">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="87542-164">默认的 JSON 序列化配置是造成这种差异的原因所在。</span><span class="sxs-lookup"><span data-stu-id="87542-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="87542-165">若要扩展上面的代码示例，可以通过解冻提供给 `globals` 函数的 `resolve` 属性，将数据从服务器传递到视图：</span><span class="sxs-lookup"><span data-stu-id="87542-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="87542-166">`postList` 对象中定义的 `globals` 数组附加到浏览器的全局 `window` 对象。</span><span class="sxs-lookup"><span data-stu-id="87542-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="87542-167">将此变量提升到全局范围可消除重复的工作，特别是在服务器上加载了一次数据，之后又在客户端上加载相同的数据时。</span><span class="sxs-lookup"><span data-stu-id="87542-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![附加到 window 对象的全局 postList 变量](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="87542-169">Webpack 开发中间件</span><span class="sxs-lookup"><span data-stu-id="87542-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="87542-170">[Webpack 开发中间件](https://webpack.js.org/guides/development/#using-webpack-dev-middleware)引入了简化的开发工作流，Webpack 可根据该工作流按需生成资源。</span><span class="sxs-lookup"><span data-stu-id="87542-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="87542-171">在浏览器中重新加载页面时，该中间件会自动编译并提供客户端资源。</span><span class="sxs-lookup"><span data-stu-id="87542-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="87542-172">另一种方法是在第三方依赖项或自定义代码发生更改时，通过项目的 npm 生成脚本手动调用 Webpack。</span><span class="sxs-lookup"><span data-stu-id="87542-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="87542-173">以下示例显示了 *package.json* 文件中的 npm 生成脚本：</span><span class="sxs-lookup"><span data-stu-id="87542-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="87542-174">Webpack 开发中间件的先决条件</span><span class="sxs-lookup"><span data-stu-id="87542-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="87542-175">安装 [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm 包：</span><span class="sxs-lookup"><span data-stu-id="87542-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="87542-176">Webpack 开发中间件配置</span><span class="sxs-lookup"><span data-stu-id="87542-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="87542-177">Webpack 开发中间件通过 *Startup.cs* 文件的 `Configure` 方法中的以下代码注册到 HTTP 请求管道中：</span><span class="sxs-lookup"><span data-stu-id="87542-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="87542-178">通过 `UseWebpackDevMiddleware` 扩展方法[注册静态文件托管](xref:fundamentals/static-files)之前，必须先调用 `UseStaticFiles` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="87542-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="87542-179">出于安全原因，仅在应用以开发模式运行时才注册该中间件。</span><span class="sxs-lookup"><span data-stu-id="87542-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="87542-180">*webpack.config.js* 文件的 `output.publicPath` 属性指示中间件监视 `dist` 文件夹中的更改：</span><span class="sxs-lookup"><span data-stu-id="87542-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="87542-181">热模块更换</span><span class="sxs-lookup"><span data-stu-id="87542-181">Hot Module Replacement</span></span>

<span data-ttu-id="87542-182">可将 Webpack 的[热模块更换](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) 功能视作 [Webpack 开发中间件](#webpack-dev-middleware)的进化版。</span><span class="sxs-lookup"><span data-stu-id="87542-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="87542-183">HMR 引入了所有相同的优点，但是通过在编译更改后自动更新页面内容，进一步简化了开发工作流。</span><span class="sxs-lookup"><span data-stu-id="87542-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="87542-184">不要将其与浏览器的刷新功能混淆，后者会干扰 SPA 的当前内存中状态和调试会话。</span><span class="sxs-lookup"><span data-stu-id="87542-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="87542-185">Webpack 开发中间件服务与浏览器之间有一个实时链接，这意味着系统会将更改推送到浏览器。</span><span class="sxs-lookup"><span data-stu-id="87542-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="87542-186">热模块更换的先决条件</span><span class="sxs-lookup"><span data-stu-id="87542-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="87542-187">安装 [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm 包：</span><span class="sxs-lookup"><span data-stu-id="87542-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="87542-188">热模块更换配置</span><span class="sxs-lookup"><span data-stu-id="87542-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="87542-189">必须在 `Configure` 方法中将 HMR 组件注册到 MVC 的 HTTP 请求管道：</span><span class="sxs-lookup"><span data-stu-id="87542-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="87542-190">与 [Webpack 开发中间件](#webpack-dev-middleware)一样，调用 `UseWebpackDevMiddleware` 扩展方法之前，必须先调用 `UseStaticFiles` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="87542-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="87542-191">出于安全原因，仅在应用以开发模式运行时才注册该中间件。</span><span class="sxs-lookup"><span data-stu-id="87542-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="87542-192">*webpack.config.js* 文件必须定义一个 `plugins` 数组，即便将其留空亦可：</span><span class="sxs-lookup"><span data-stu-id="87542-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="87542-193">在浏览器中加载应用后，开发人员工具的“控制台”选项卡会提供 HMR 激活确认：</span><span class="sxs-lookup"><span data-stu-id="87542-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![已连接热模块更换消息](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="87542-195">路由帮助程序</span><span class="sxs-lookup"><span data-stu-id="87542-195">Routing helpers</span></span>

<span data-ttu-id="87542-196">在大多数基于 ASP.NET Core 的 SPA 中，除服务器端路由外，通常还需要进行客户端路由。</span><span class="sxs-lookup"><span data-stu-id="87542-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="87542-197">SPA 和 MVC 路由系统可以独立工作而互不干扰。</span><span class="sxs-lookup"><span data-stu-id="87542-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="87542-198">但是，有一种极端情况带来了挑战：标识 404 HTTP 响应。</span><span class="sxs-lookup"><span data-stu-id="87542-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="87542-199">以使用 `/some/page` 的无扩展路由的情况为例。</span><span class="sxs-lookup"><span data-stu-id="87542-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="87542-200">假设请求的模式与服务器端路由不匹配，但与客户端路由匹配。</span><span class="sxs-lookup"><span data-stu-id="87542-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="87542-201">现在以针对 `/images/user-512.png` 的传入请求为例，该请求通常需要在服务器上查找映像文件。</span><span class="sxs-lookup"><span data-stu-id="87542-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="87542-202">如果请求的资源路径与任何服务器端路由或静态文件都不匹配，则客户端应用程序不太可能处理它 &mdash; 通常需要返回 404 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="87542-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="87542-203">路由帮助程序的先决条件</span><span class="sxs-lookup"><span data-stu-id="87542-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="87542-204">安装客户端路由 npm 包。</span><span class="sxs-lookup"><span data-stu-id="87542-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="87542-205">以 Angular 为例：</span><span class="sxs-lookup"><span data-stu-id="87542-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="87542-206">路由帮助程序配置</span><span class="sxs-lookup"><span data-stu-id="87542-206">Routing helpers configuration</span></span>

<span data-ttu-id="87542-207">在 `MapSpaFallbackRoute` 方法中使用名为 `Configure` 的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="87542-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="87542-208">系统按路由配置顺序评估路由。</span><span class="sxs-lookup"><span data-stu-id="87542-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="87542-209">因此，上面的代码示例中的 `default` 路由先用于模式匹配。</span><span class="sxs-lookup"><span data-stu-id="87542-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="87542-210">创建新项目</span><span class="sxs-lookup"><span data-stu-id="87542-210">Create a new project</span></span>

<span data-ttu-id="87542-211">JavaScript Services 提供预配置的应用程序模板。</span><span class="sxs-lookup"><span data-stu-id="87542-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="87542-212">在这些模板中，SpaServices 与各种框架和库（例如 Angular、React 和 Redux）结合使用。</span><span class="sxs-lookup"><span data-stu-id="87542-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="87542-213">可以通过使用 .NET Core CLI 运行以下命令来安装这些模板：</span><span class="sxs-lookup"><span data-stu-id="87542-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="87542-214">系统会显示可用 SPA 模板的列表：</span><span class="sxs-lookup"><span data-stu-id="87542-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="87542-215">模板</span><span class="sxs-lookup"><span data-stu-id="87542-215">Templates</span></span>                                 | <span data-ttu-id="87542-216">短名称</span><span class="sxs-lookup"><span data-stu-id="87542-216">Short Name</span></span> | <span data-ttu-id="87542-217">语言</span><span class="sxs-lookup"><span data-stu-id="87542-217">Language</span></span> | <span data-ttu-id="87542-218">Tags</span><span class="sxs-lookup"><span data-stu-id="87542-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="87542-219">含 Angular 的 MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87542-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="87542-220">angular</span><span class="sxs-lookup"><span data-stu-id="87542-220">angular</span></span>    | <span data-ttu-id="87542-221">[C#]</span><span class="sxs-lookup"><span data-stu-id="87542-221">[C#]</span></span>     | <span data-ttu-id="87542-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="87542-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="87542-223">含 React.js 的 MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87542-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="87542-224">react</span><span class="sxs-lookup"><span data-stu-id="87542-224">react</span></span>      | <span data-ttu-id="87542-225">[C#]</span><span class="sxs-lookup"><span data-stu-id="87542-225">[C#]</span></span>     | <span data-ttu-id="87542-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="87542-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="87542-227">含 React.js 和 Redux 的 MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87542-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="87542-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="87542-228">reactredux</span></span> | <span data-ttu-id="87542-229">[C#]</span><span class="sxs-lookup"><span data-stu-id="87542-229">[C#]</span></span>     | <span data-ttu-id="87542-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="87542-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="87542-231">若要使用其中一个 SPA 模板创建新项目，请在 **dotnet new** 命令中包含该模板的[短名称](/dotnet/core/tools/dotnet-new)。</span><span class="sxs-lookup"><span data-stu-id="87542-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="87542-232">以下命令将使用为服务器端配置的 ASP.NET Core MVC 创建 Angular 应用程序：</span><span class="sxs-lookup"><span data-stu-id="87542-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="87542-233">设置运行时配置模式</span><span class="sxs-lookup"><span data-stu-id="87542-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="87542-234">存在两种主要运行时配置模式：</span><span class="sxs-lookup"><span data-stu-id="87542-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="87542-235">**开发**：</span><span class="sxs-lookup"><span data-stu-id="87542-235">**Development**:</span></span>
  * <span data-ttu-id="87542-236">包含源映射以简化调试。</span><span class="sxs-lookup"><span data-stu-id="87542-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="87542-237">不优化客户端代码的性能。</span><span class="sxs-lookup"><span data-stu-id="87542-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="87542-238">**生产**：</span><span class="sxs-lookup"><span data-stu-id="87542-238">**Production**:</span></span>
  * <span data-ttu-id="87542-239">不包含源映射。</span><span class="sxs-lookup"><span data-stu-id="87542-239">Excludes source maps.</span></span>
  * <span data-ttu-id="87542-240">通过捆绑和缩小来优化客户端代码。</span><span class="sxs-lookup"><span data-stu-id="87542-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="87542-241">ASP.NET Core 使用名为 `ASPNETCORE_ENVIRONMENT` 的环境变量来存储配置模式。</span><span class="sxs-lookup"><span data-stu-id="87542-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="87542-242">有关详细信息，请参阅[设置环境](xref:fundamentals/environments#set-the-environment)。</span><span class="sxs-lookup"><span data-stu-id="87542-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="87542-243">使用 .NET Core CLI 运行</span><span class="sxs-lookup"><span data-stu-id="87542-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="87542-244">通过在项目根目录下运行以下命令来还原所需的 NuGet 和 npm 包：</span><span class="sxs-lookup"><span data-stu-id="87542-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="87542-245">生成并运行应用程序：</span><span class="sxs-lookup"><span data-stu-id="87542-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="87542-246">应用程序根据[运行时配置模式](#set-the-runtime-configuration-mode)在 localhost 上启动。</span><span class="sxs-lookup"><span data-stu-id="87542-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="87542-247">在浏览器中导航到 `http://localhost:5000` 会显示登陆页面。</span><span class="sxs-lookup"><span data-stu-id="87542-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="87542-248">使用 Visual Studio 2017 运行</span><span class="sxs-lookup"><span data-stu-id="87542-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="87542-249">打开由 *dotnet new* 命令生成的 [.csproj](/dotnet/core/tools/dotnet-new) 文件。</span><span class="sxs-lookup"><span data-stu-id="87542-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="87542-250">所需的 NuGet 和 npm 包在项目打开时会自动还原。</span><span class="sxs-lookup"><span data-stu-id="87542-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="87542-251">此还原过程可能需要几分钟的时间，应用程序在此过程完成后即可运行。</span><span class="sxs-lookup"><span data-stu-id="87542-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="87542-252">单击绿色的运行按钮或按 `Ctrl + F5`，浏览器将打开到应用程序的登陆页面。</span><span class="sxs-lookup"><span data-stu-id="87542-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="87542-253">应用程序根据[运行时配置模式](#set-the-runtime-configuration-mode)在 localhost 上运行。</span><span class="sxs-lookup"><span data-stu-id="87542-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="87542-254">测试应用</span><span class="sxs-lookup"><span data-stu-id="87542-254">Test the app</span></span>

<span data-ttu-id="87542-255">SpaServices 模板已预先配置为使用 [Karma](https://karma-runner.github.io/1.0/index.html) 和 [Jasmine](https://jasmine.github.io/) 运行客户端测试。</span><span class="sxs-lookup"><span data-stu-id="87542-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="87542-256">Jasmine 是适用于 JavaScript 的常用单元测试框架，而 Karma 是这些测试的测试运行程序。</span><span class="sxs-lookup"><span data-stu-id="87542-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="87542-257">Karma 配置为使用 [Webpack 开发中间件](#webpack-dev-middleware)，使开发人员无需在每次进行更改时都停止并运行测试。</span><span class="sxs-lookup"><span data-stu-id="87542-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="87542-258">无论是针对测试用例运行的代码还是测试用例本身，测试都会自动运行。</span><span class="sxs-lookup"><span data-stu-id="87542-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="87542-259">以 Angular 应用程序为例，系统已经为 `CounterComponent`counter.component.spec.ts*文件中的* 提供了两个 Jasmine 测试用例：</span><span class="sxs-lookup"><span data-stu-id="87542-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="87542-260">在 *ClientApp* 目录中打开命令提示符。</span><span class="sxs-lookup"><span data-stu-id="87542-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="87542-261">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="87542-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="87542-262">该脚本将启动 Karma 测试运行程序，而后者将读取 *karma.conf.js* 文件中定义的设置。</span><span class="sxs-lookup"><span data-stu-id="87542-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="87542-263">除其他设置外，*karma.conf.js* 还通过其 `files` 数组标识要执行的测试文件：</span><span class="sxs-lookup"><span data-stu-id="87542-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="87542-264">发布应用</span><span class="sxs-lookup"><span data-stu-id="87542-264">Publish the app</span></span>

<span data-ttu-id="87542-265">有关发布到 Azure 的详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/12474)。</span><span class="sxs-lookup"><span data-stu-id="87542-265">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="87542-266">将生成的客户端资产和已发布的 ASP.NET Core 项目组合成一个可即时部署的包的过程可能会很繁琐。</span><span class="sxs-lookup"><span data-stu-id="87542-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="87542-267">值得庆幸的是，SpaServices 可使用名为 `RunWebpack` 的自定义 MSBuild 目标来协调整个发布过程：</span><span class="sxs-lookup"><span data-stu-id="87542-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="87542-268">该 MSBuild 目标具有以下职责：</span><span class="sxs-lookup"><span data-stu-id="87542-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="87542-269">还原 npm 包。</span><span class="sxs-lookup"><span data-stu-id="87542-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="87542-270">创建第三方客户端资产的生产级生成。</span><span class="sxs-lookup"><span data-stu-id="87542-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="87542-271">创建自定义客户端资产的生产级生成。</span><span class="sxs-lookup"><span data-stu-id="87542-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="87542-272">将 Webpack 生成的资产复制到发布文件夹。</span><span class="sxs-lookup"><span data-stu-id="87542-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="87542-273">运行以下命令时将调用该 MSBuild 目标：</span><span class="sxs-lookup"><span data-stu-id="87542-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="87542-274">其他资源</span><span class="sxs-lookup"><span data-stu-id="87542-274">Additional resources</span></span>

* [<span data-ttu-id="87542-275">Angular 文档</span><span class="sxs-lookup"><span data-stu-id="87542-275">Angular Docs</span></span>](https://angular.io/docs)
