---
title: 将 HTTP 处理程序和模块迁移到 ASP.NET Core 中间件
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/http-modules
ms.openlocfilehash: c2b49976d2063679eab2403aae432660e8c8932d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775409"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="e0a46-102">将 HTTP 处理程序和模块迁移到 ASP.NET Core 中间件</span><span class="sxs-lookup"><span data-stu-id="e0a46-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="e0a46-103">本文介绍如何将现有[的 ASP.NET HTTP 模块和处理程序从 system.webserver](/iis/configuration/system.webserver/)迁移到 ASP.NET Core[中间件](xref:fundamentals/middleware/index)。</span><span class="sxs-lookup"><span data-stu-id="e0a46-103">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="e0a46-104">模块和处理程序被</span><span class="sxs-lookup"><span data-stu-id="e0a46-104">Modules and handlers revisited</span></span>

<span data-ttu-id="e0a46-105">在继续 ASP.NET Core 中间件之前，让我们先回顾一下 HTTP 模块和处理程序的工作原理：</span><span class="sxs-lookup"><span data-stu-id="e0a46-105">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![模块处理程序](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="e0a46-107">**处理程序是：**</span><span class="sxs-lookup"><span data-stu-id="e0a46-107">**Handlers are:**</span></span>

* <span data-ttu-id="e0a46-108">实现[IHttpHandler](/dotnet/api/system.web.ihttphandler)的类</span><span class="sxs-lookup"><span data-stu-id="e0a46-108">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="e0a46-109">用于处理具有给定文件名或扩展名的请求，如 *. report*</span><span class="sxs-lookup"><span data-stu-id="e0a46-109">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="e0a46-110">*在 web.config*中[配置](/iis/configuration/system.webserver/handlers/)</span><span class="sxs-lookup"><span data-stu-id="e0a46-110">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="e0a46-111">**模块为：**</span><span class="sxs-lookup"><span data-stu-id="e0a46-111">**Modules are:**</span></span>

* <span data-ttu-id="e0a46-112">实现[IHttpModule](/dotnet/api/system.web.ihttpmodule)的类</span><span class="sxs-lookup"><span data-stu-id="e0a46-112">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="e0a46-113">为每个请求调用</span><span class="sxs-lookup"><span data-stu-id="e0a46-113">Invoked for every request</span></span>

* <span data-ttu-id="e0a46-114">能够短路（停止进一步处理请求）</span><span class="sxs-lookup"><span data-stu-id="e0a46-114">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="e0a46-115">可以添加到 HTTP 响应，或创建自己的响应</span><span class="sxs-lookup"><span data-stu-id="e0a46-115">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="e0a46-116">*在 web.config*中[配置](/iis/configuration/system.webserver/modules/)</span><span class="sxs-lookup"><span data-stu-id="e0a46-116">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="e0a46-117">**模块处理传入请求的顺序由确定：**</span><span class="sxs-lookup"><span data-stu-id="e0a46-117">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="e0a46-118">[应用程序生命周期](https://msdn.microsoft.com/library/ms227673.aspx)，是 ASP.NET： [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest)、 [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)等触发的序列事件。每个模块都可以为一个或多个事件创建处理程序。</span><span class="sxs-lookup"><span data-stu-id="e0a46-118">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="e0a46-119">对于同一事件，为*在 web.config 中*配置它们的顺序。</span><span class="sxs-lookup"><span data-stu-id="e0a46-119">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="e0a46-120">除了模块外，还可以将生命周期事件的处理程序添加到*Global.asax.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="e0a46-120">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="e0a46-121">这些处理程序在配置的模块中的处理程序之后运行。</span><span class="sxs-lookup"><span data-stu-id="e0a46-121">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="e0a46-122">从处理程序和模块到中间件</span><span class="sxs-lookup"><span data-stu-id="e0a46-122">From handlers and modules to middleware</span></span>

<span data-ttu-id="e0a46-123">**中间件比 HTTP 模块和处理程序更简单：**</span><span class="sxs-lookup"><span data-stu-id="e0a46-123">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="e0a46-124">"模块"、"处理程序"、" *Global.asax.cs*"、 *"WEB.CONFIG" （IIS*配置除外）和 "应用程序生命周期" 消失</span><span class="sxs-lookup"><span data-stu-id="e0a46-124">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="e0a46-125">中间件已使用模块和处理程序的角色</span><span class="sxs-lookup"><span data-stu-id="e0a46-125">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="e0a46-126">中间件使用代码而不*是在 web.config 中进行配置*</span><span class="sxs-lookup"><span data-stu-id="e0a46-126">Middleware are configured using code rather than in *Web.config*</span></span>

* <span data-ttu-id="e0a46-127">通过[管道分支](xref:fundamentals/middleware/index#use-run-and-map)，你可以将请求发送到特定的中间件，不仅可以基于 URL，还可以发送到请求标头、查询字符串等。</span><span class="sxs-lookup"><span data-stu-id="e0a46-127">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

<span data-ttu-id="e0a46-128">**中间件非常类似于模块：**</span><span class="sxs-lookup"><span data-stu-id="e0a46-128">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="e0a46-129">为每个请求按原则调用</span><span class="sxs-lookup"><span data-stu-id="e0a46-129">Invoked in principle for every request</span></span>

* <span data-ttu-id="e0a46-130">无法将[请求传递给下一个中间件](#http-modules-shortcircuiting-middleware)来对请求进行短线路</span><span class="sxs-lookup"><span data-stu-id="e0a46-130">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="e0a46-131">能够创建自己的 HTTP 响应</span><span class="sxs-lookup"><span data-stu-id="e0a46-131">Able to create their own HTTP response</span></span>

<span data-ttu-id="e0a46-132">**中间件和模块按不同的顺序进行处理：**</span><span class="sxs-lookup"><span data-stu-id="e0a46-132">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="e0a46-133">中间件的顺序取决于它们插入请求管道的顺序，而模块的顺序主要基于[应用程序生命周期](https://msdn.microsoft.com/library/ms227673.aspx)事件</span><span class="sxs-lookup"><span data-stu-id="e0a46-133">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

* <span data-ttu-id="e0a46-134">响应的中间件顺序与请求的顺序相反，而对于请求和响应，模块的顺序是相同的。</span><span class="sxs-lookup"><span data-stu-id="e0a46-134">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="e0a46-135">请参阅[使用 IApplicationBuilder 创建中间件管道](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="e0a46-135">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![中间件](http-modules/_static/middleware.png)

<span data-ttu-id="e0a46-137">请注意，在上图中，身份验证中间件与请求短路。</span><span class="sxs-lookup"><span data-stu-id="e0a46-137">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="e0a46-138">将模块代码迁移到中间件</span><span class="sxs-lookup"><span data-stu-id="e0a46-138">Migrating module code to middleware</span></span>

<span data-ttu-id="e0a46-139">现有 HTTP 模块如下所示：</span><span class="sxs-lookup"><span data-stu-id="e0a46-139">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="e0a46-140">如[中间件](xref:fundamentals/middleware/index)页中所示，ASP.NET Core 中间件是一个类，该类`Invoke`公开采用`HttpContext`并返回的`Task`方法。</span><span class="sxs-lookup"><span data-stu-id="e0a46-140">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="e0a46-141">新的中间件将如下所示：</span><span class="sxs-lookup"><span data-stu-id="e0a46-141">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="e0a46-142">前面的中间件模板取自[编写中间件](xref:fundamentals/middleware/write)的部分。</span><span class="sxs-lookup"><span data-stu-id="e0a46-142">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="e0a46-143">*MyMiddlewareExtensions* helper 类使你可以更轻松地在`Startup`类中配置中间件。</span><span class="sxs-lookup"><span data-stu-id="e0a46-143">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="e0a46-144">`UseMyMiddleware`方法将中间件类添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="e0a46-144">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="e0a46-145">中间件的构造函数中插入了中间件所需的服务。</span><span class="sxs-lookup"><span data-stu-id="e0a46-145">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="e0a46-146">如果用户未获得授权，则模块可能会终止请求：</span><span class="sxs-lookup"><span data-stu-id="e0a46-146">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="e0a46-147">中间件通过不调用`Invoke`管道中的下一个中间件来处理这种情况。</span><span class="sxs-lookup"><span data-stu-id="e0a46-147">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="e0a46-148">请记住，这并不完全终止请求，因为当响应通过管道返回以前的中间件时，仍然会调用以前的。</span><span class="sxs-lookup"><span data-stu-id="e0a46-148">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="e0a46-149">当您将模块的功能迁移到新的中间件时，您可能会发现您的代码不`HttpContext`会进行编译，因为在 ASP.NET Core 中，类发生了重大更改。</span><span class="sxs-lookup"><span data-stu-id="e0a46-149">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="e0a46-150">[稍后](#migrating-to-the-new-httpcontext)，你将了解如何迁移到新的 ASP.NET Core HttpContext。</span><span class="sxs-lookup"><span data-stu-id="e0a46-150">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="e0a46-151">将模块插入迁移到请求管道中</span><span class="sxs-lookup"><span data-stu-id="e0a46-151">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="e0a46-152">HTTP 模块*通常使用 web.config*添加到请求管道：</span><span class="sxs-lookup"><span data-stu-id="e0a46-152">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="e0a46-153">通过在`Startup`类中将[新的中间件添加](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)到请求管道来转换此项：</span><span class="sxs-lookup"><span data-stu-id="e0a46-153">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="e0a46-154">插入新中间件的管道中的确切位置取决于它在*web.config 中的*模块列表中处理为模块`BeginRequest`（ `EndRequest`、等）的事件及其顺序。</span><span class="sxs-lookup"><span data-stu-id="e0a46-154">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="e0a46-155">如前所述，ASP.NET Core 中没有应用程序生命周期，中间件的处理响应顺序与模块使用的顺序不同。</span><span class="sxs-lookup"><span data-stu-id="e0a46-155">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="e0a46-156">这可能会使你的排序决策更具挑战性。</span><span class="sxs-lookup"><span data-stu-id="e0a46-156">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="e0a46-157">如果排序会成为一个问题，则可以将模块拆分为多个中间件组件，这些组件可以独立排序。</span><span class="sxs-lookup"><span data-stu-id="e0a46-157">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="e0a46-158">将处理程序代码迁移到中间件</span><span class="sxs-lookup"><span data-stu-id="e0a46-158">Migrating handler code to middleware</span></span>

<span data-ttu-id="e0a46-159">HTTP 处理程序如下所示：</span><span class="sxs-lookup"><span data-stu-id="e0a46-159">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="e0a46-160">在 ASP.NET Core 项目中，将其转换为类似于下面的中间件：</span><span class="sxs-lookup"><span data-stu-id="e0a46-160">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="e0a46-161">此中间件与与模块对应的中间件非常类似。</span><span class="sxs-lookup"><span data-stu-id="e0a46-161">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="e0a46-162">唯一的区别在于，这里不会调用`_next.Invoke(context)`。</span><span class="sxs-lookup"><span data-stu-id="e0a46-162">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="e0a46-163">这样做很有意义，因为处理程序位于请求管道的末尾，因此没有要调用的下一个中间件。</span><span class="sxs-lookup"><span data-stu-id="e0a46-163">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="e0a46-164">将处理程序插入迁移到请求管道中</span><span class="sxs-lookup"><span data-stu-id="e0a46-164">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="e0a46-165">配置 HTTP 处理程序*是在 web.config 中完成的，* 如下所示：</span><span class="sxs-lookup"><span data-stu-id="e0a46-165">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="e0a46-166">可以通过将新的处理程序中间件添加到`Startup`类中的请求管道来转换此转换，类似于从模块转换的中间件。</span><span class="sxs-lookup"><span data-stu-id="e0a46-166">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="e0a46-167">此方法的问题是，它会将所有请求发送到新的处理程序中间件。</span><span class="sxs-lookup"><span data-stu-id="e0a46-167">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="e0a46-168">但是，只需要具有给定扩展的请求来访问中间件。</span><span class="sxs-lookup"><span data-stu-id="e0a46-168">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="e0a46-169">这将为你提供与 HTTP 处理程序相同的功能。</span><span class="sxs-lookup"><span data-stu-id="e0a46-169">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="e0a46-170">一种解决方法是使用`MapWhen`扩展方法分支具有给定扩展的请求的管道。</span><span class="sxs-lookup"><span data-stu-id="e0a46-170">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="e0a46-171">可在添加其他中间件`Configure`的相同方法中执行此操作：</span><span class="sxs-lookup"><span data-stu-id="e0a46-171">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="e0a46-172">`MapWhen`采用以下参数：</span><span class="sxs-lookup"><span data-stu-id="e0a46-172">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="e0a46-173">一个采用的`HttpContext` lambda，如果请求`true`应向下分支，则返回。</span><span class="sxs-lookup"><span data-stu-id="e0a46-173">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="e0a46-174">这意味着，不仅可以根据请求的扩展来分支请求，还可以处理请求标头、查询字符串参数等。</span><span class="sxs-lookup"><span data-stu-id="e0a46-174">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="e0a46-175">一个采用`IApplicationBuilder`并添加分支的所有中间件的 lambda。</span><span class="sxs-lookup"><span data-stu-id="e0a46-175">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="e0a46-176">这意味着，可以将其他中间件添加到处理程序中间件前面的分支。</span><span class="sxs-lookup"><span data-stu-id="e0a46-176">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="e0a46-177">将在所有请求上调用分支之前添加到管道的中间件;该分支不会对它们产生任何影响。</span><span class="sxs-lookup"><span data-stu-id="e0a46-177">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="e0a46-178">使用 options 模式加载中间件选项</span><span class="sxs-lookup"><span data-stu-id="e0a46-178">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="e0a46-179">某些模块和处理程序具有*存储在 web.config 中的配置*选项。但是，在 ASP.NET Core*新的配置*模型用于替代 web.config。</span><span class="sxs-lookup"><span data-stu-id="e0a46-179">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="e0a46-180">新[配置系统](xref:fundamentals/configuration/index)提供以下选项来解决此类情况：</span><span class="sxs-lookup"><span data-stu-id="e0a46-180">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="e0a46-181">将选项直接注入中间件，如下[一节](#loading-middleware-options-through-direct-injection)所示。</span><span class="sxs-lookup"><span data-stu-id="e0a46-181">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="e0a46-182">使用[options 模式](xref:fundamentals/configuration/options)：</span><span class="sxs-lookup"><span data-stu-id="e0a46-182">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="e0a46-183">创建用于保存中间件选项的类，例如：</span><span class="sxs-lookup"><span data-stu-id="e0a46-183">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="e0a46-184">存储选项值</span><span class="sxs-lookup"><span data-stu-id="e0a46-184">Store the option values</span></span>

   <span data-ttu-id="e0a46-185">配置系统允许您将选项值存储在任何所需的位置。</span><span class="sxs-lookup"><span data-stu-id="e0a46-185">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="e0a46-186">但是，大多数站点都使用*appsettings*，因此我们将采取这种方法：</span><span class="sxs-lookup"><span data-stu-id="e0a46-186">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="e0a46-187">*MyMiddlewareOptionsSection*是部分名称。</span><span class="sxs-lookup"><span data-stu-id="e0a46-187">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="e0a46-188">它不必与 options 类的名称相同。</span><span class="sxs-lookup"><span data-stu-id="e0a46-188">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="e0a46-189">将选项值与 options 类相关联</span><span class="sxs-lookup"><span data-stu-id="e0a46-189">Associate the option values with the options class</span></span>

    <span data-ttu-id="e0a46-190">Options 模式使用 ASP.NET Core 的依赖项注入框架将选项类型（如`MyMiddlewareOptions`）与具有实际选项的`MyMiddlewareOptions`对象相关联。</span><span class="sxs-lookup"><span data-stu-id="e0a46-190">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="e0a46-191">更新你`Startup`的类：</span><span class="sxs-lookup"><span data-stu-id="e0a46-191">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="e0a46-192">如果使用的是*appsettings*，请将其添加到`Startup`构造函数中的配置生成器：</span><span class="sxs-lookup"><span data-stu-id="e0a46-192">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="e0a46-193">配置 options 服务：</span><span class="sxs-lookup"><span data-stu-id="e0a46-193">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="e0a46-194">将选项与 options 类相关联：</span><span class="sxs-lookup"><span data-stu-id="e0a46-194">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="e0a46-195">将选项注入中间件构造函数。</span><span class="sxs-lookup"><span data-stu-id="e0a46-195">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="e0a46-196">这类似于将选项注入控制器。</span><span class="sxs-lookup"><span data-stu-id="e0a46-196">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="e0a46-197">将[UseMiddleware](#http-modules-usemiddleware)中间件添加到中`IApplicationBuilder`的 UseMiddleware 扩展方法会处理依赖关系注入。</span><span class="sxs-lookup"><span data-stu-id="e0a46-197">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="e0a46-198">这并不限于`IOptions`对象。</span><span class="sxs-lookup"><span data-stu-id="e0a46-198">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="e0a46-199">中间件所需的任何其他对象都可以通过这种方式注入。</span><span class="sxs-lookup"><span data-stu-id="e0a46-199">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="e0a46-200">通过直接注入加载中间件选项</span><span class="sxs-lookup"><span data-stu-id="e0a46-200">Loading middleware options through direct injection</span></span>

<span data-ttu-id="e0a46-201">Options 模式的优点在于，它在选项值与其使用者之间产生松散耦合。</span><span class="sxs-lookup"><span data-stu-id="e0a46-201">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="e0a46-202">将选项类与实际选项值相关联后，任何其他类都可以通过依赖关系注入框架访问这些选项。</span><span class="sxs-lookup"><span data-stu-id="e0a46-202">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="e0a46-203">无需围绕选项值进行传递。</span><span class="sxs-lookup"><span data-stu-id="e0a46-203">There's no need to pass around options values.</span></span>

<span data-ttu-id="e0a46-204">如果要使用不同的选项两次使用同一中间件，则会出现这种情况。</span><span class="sxs-lookup"><span data-stu-id="e0a46-204">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="e0a46-205">例如，在不同的分支中使用的授权中间件允许不同角色。</span><span class="sxs-lookup"><span data-stu-id="e0a46-205">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="e0a46-206">不能将两个不同的选项对象与一个 options 类相关联。</span><span class="sxs-lookup"><span data-stu-id="e0a46-206">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="e0a46-207">解决方法是在`Startup`类中获取选项对象，并将其直接传递给中间件的每个实例。</span><span class="sxs-lookup"><span data-stu-id="e0a46-207">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="e0a46-208">将第二个键添加到*appsettings*</span><span class="sxs-lookup"><span data-stu-id="e0a46-208">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="e0a46-209">若要将第二组选项添加到*appsettings*文件，请使用新密钥来唯一标识它：</span><span class="sxs-lookup"><span data-stu-id="e0a46-209">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="e0a46-210">检索选项值并将其传递给中间件。</span><span class="sxs-lookup"><span data-stu-id="e0a46-210">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="e0a46-211">`Use...`扩展方法（将中间件添加到管道）是要传入选项值的逻辑位置：</span><span class="sxs-lookup"><span data-stu-id="e0a46-211">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="e0a46-212">启用中间件以采用 options 参数。</span><span class="sxs-lookup"><span data-stu-id="e0a46-212">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="e0a46-213">提供`Use...`扩展方法（采用 options 参数并将其传递给`UseMiddleware`）的重载。</span><span class="sxs-lookup"><span data-stu-id="e0a46-213">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="e0a46-214">当`UseMiddleware`用参数调用时，它会在实例化中间件对象时将参数传递给中间件构造函数。</span><span class="sxs-lookup"><span data-stu-id="e0a46-214">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="e0a46-215">请注意这如何包装`OptionsWrapper`对象中的选项对象。</span><span class="sxs-lookup"><span data-stu-id="e0a46-215">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="e0a46-216">这实现`IOptions`了中间件构造函数的预期。</span><span class="sxs-lookup"><span data-stu-id="e0a46-216">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="e0a46-217">迁移到新的 HttpContext</span><span class="sxs-lookup"><span data-stu-id="e0a46-217">Migrating to the new HttpContext</span></span>

<span data-ttu-id="e0a46-218">你以前看到，中间`Invoke`件中的方法采用类型`HttpContext`为的参数：</span><span class="sxs-lookup"><span data-stu-id="e0a46-218">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="e0a46-219">`HttpContext`在 ASP.NET Core 中进行了重大更改。</span><span class="sxs-lookup"><span data-stu-id="e0a46-219">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="e0a46-220">本部分演示如何将 system.servicemodel 最[常用的属性转换为新](/dotnet/api/system.web.httpcontext) `Microsoft.AspNetCore.Http.HttpContext`的。</span><span class="sxs-lookup"><span data-stu-id="e0a46-220">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="e0a46-221">HttpContext</span><span class="sxs-lookup"><span data-stu-id="e0a46-221">HttpContext</span></span>

<span data-ttu-id="e0a46-222">**HttpContext**会转换为：</span><span class="sxs-lookup"><span data-stu-id="e0a46-222">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="e0a46-223">**唯一的请求 ID （不含 System.web）**</span><span class="sxs-lookup"><span data-stu-id="e0a46-223">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="e0a46-224">提供每个请求的唯一 id。</span><span class="sxs-lookup"><span data-stu-id="e0a46-224">Gives you a unique id for each request.</span></span> <span data-ttu-id="e0a46-225">在日志中包含非常有用。</span><span class="sxs-lookup"><span data-stu-id="e0a46-225">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="e0a46-226">Httpcontext.current 请求</span><span class="sxs-lookup"><span data-stu-id="e0a46-226">HttpContext.Request</span></span>

<span data-ttu-id="e0a46-227">**HttpMethod**转换为：</span><span class="sxs-lookup"><span data-stu-id="e0a46-227">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="e0a46-228">**HttpContext**转换为：</span><span class="sxs-lookup"><span data-stu-id="e0a46-228">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="e0a46-229">**Httpcontext.current**转换为（ **RawUrl** ）：</span><span class="sxs-lookup"><span data-stu-id="e0a46-229">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="e0a46-230">**IsSecureConnection**转换为：</span><span class="sxs-lookup"><span data-stu-id="e0a46-230">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="e0a46-231">**UserHostAddress**转换为：</span><span class="sxs-lookup"><span data-stu-id="e0a46-231">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="e0a46-232">**Httpcontext.current**转换为：</span><span class="sxs-lookup"><span data-stu-id="e0a46-232">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="e0a46-233">**RequestContext RouteData**转换为：</span><span class="sxs-lookup"><span data-stu-id="e0a46-233">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="e0a46-234">**Httpcontext.current**转换为：</span><span class="sxs-lookup"><span data-stu-id="e0a46-234">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="e0a46-235">**UserAgent**转换为：</span><span class="sxs-lookup"><span data-stu-id="e0a46-235">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="e0a46-236">**UrlReferrer**转换为：</span><span class="sxs-lookup"><span data-stu-id="e0a46-236">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="e0a46-237">**HttpContext**转换为：</span><span class="sxs-lookup"><span data-stu-id="e0a46-237">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="e0a46-238">**Httpcontext.current**转换为：</span><span class="sxs-lookup"><span data-stu-id="e0a46-238">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="e0a46-239">仅当 content 子类型为*x-www-url 编码*或*窗体数据*时才读取窗体值。</span><span class="sxs-lookup"><span data-stu-id="e0a46-239">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="e0a46-240">**InputStream**转换为：</span><span class="sxs-lookup"><span data-stu-id="e0a46-240">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="e0a46-241">仅在管道末尾的处理程序类型中间件中使用此代码。</span><span class="sxs-lookup"><span data-stu-id="e0a46-241">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="e0a46-242">对于每个请求，可以读取上面所示的原始主体。</span><span class="sxs-lookup"><span data-stu-id="e0a46-242">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="e0a46-243">第一次读取后尝试读取正文的中间件将读取空正文。</span><span class="sxs-lookup"><span data-stu-id="e0a46-243">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="e0a46-244">这并不适用于读取如上所示的窗体，因为这是从缓冲区中完成的。</span><span class="sxs-lookup"><span data-stu-id="e0a46-244">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="e0a46-245">HttpContext 响应</span><span class="sxs-lookup"><span data-stu-id="e0a46-245">HttpContext.Response</span></span>

<span data-ttu-id="e0a46-246">**Httpcontext.current**转换为（ **StatusDescription** ）：</span><span class="sxs-lookup"><span data-stu-id="e0a46-246">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="e0a46-247">**ContentEncoding**和**httpcontext**转换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="e0a46-247">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="e0a46-248">**Httpcontext.current**还会转换为：</span><span class="sxs-lookup"><span data-stu-id="e0a46-248">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="e0a46-249">**Httpcontext.current**转换为：</span><span class="sxs-lookup"><span data-stu-id="e0a46-249">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="e0a46-250">**TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="e0a46-250">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="e0a46-251">[此处](../fundamentals/request-features.md#middleware-and-request-features)讨论了如何提供文件。</span><span class="sxs-lookup"><span data-stu-id="e0a46-251">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="e0a46-252">**Httpcontext.current 标头**</span><span class="sxs-lookup"><span data-stu-id="e0a46-252">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="e0a46-253">发送响应标头比较复杂，因为如果在将任何内容写入响应正文后设置这些标头，则不会发送这些标头。</span><span class="sxs-lookup"><span data-stu-id="e0a46-253">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="e0a46-254">解决方法是设置一个回调方法，该方法将在开始写入响应之前被调用。</span><span class="sxs-lookup"><span data-stu-id="e0a46-254">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="e0a46-255">最好在中间件中的`Invoke`方法的开头完成此操作。</span><span class="sxs-lookup"><span data-stu-id="e0a46-255">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="e0a46-256">这是设置响应标头的此回调方法。</span><span class="sxs-lookup"><span data-stu-id="e0a46-256">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="e0a46-257">下面的代码设置一个名`SetHeaders`为的回调方法：</span><span class="sxs-lookup"><span data-stu-id="e0a46-257">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="e0a46-258">`SetHeaders`回调方法将如下所示：</span><span class="sxs-lookup"><span data-stu-id="e0a46-258">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="e0a46-259">**HttpContext. Cookie**</span><span class="sxs-lookup"><span data-stu-id="e0a46-259">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="e0a46-260">Cookie 在*设置 cookie*响应标头中传递到浏览器。</span><span class="sxs-lookup"><span data-stu-id="e0a46-260">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="e0a46-261">因此，发送 cookie 需要与用于发送响应标头的回调相同：</span><span class="sxs-lookup"><span data-stu-id="e0a46-261">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="e0a46-262">`SetCookies`回调方法将如下所示：</span><span class="sxs-lookup"><span data-stu-id="e0a46-262">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="e0a46-263">其他资源</span><span class="sxs-lookup"><span data-stu-id="e0a46-263">Additional resources</span></span>

* [<span data-ttu-id="e0a46-264">HTTP 处理程序和 HTTP 模块概述</span><span class="sxs-lookup"><span data-stu-id="e0a46-264">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="e0a46-265">配置</span><span class="sxs-lookup"><span data-stu-id="e0a46-265">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="e0a46-266">应用程序启动</span><span class="sxs-lookup"><span data-stu-id="e0a46-266">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="e0a46-267">中间件</span><span class="sxs-lookup"><span data-stu-id="e0a46-267">Middleware</span></span>](xref:fundamentals/middleware/index)
