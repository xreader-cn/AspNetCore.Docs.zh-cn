---
title: ASP.NET Core 中的日志记录
author: tdykstra
description: 了解 ASP.NET Core 中的记录框架。 发现内置日志记录提供程序，并详细了解常见第三方提供程序。
ms.author: tdykstra
ms.custom: mvc
ms.date: 01/14/2019
uid: fundamentals/logging/index
ms.openlocfilehash: 81620f0c844f3dbb1a2da0e9f1c319f87d9790b6
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667695"
---
# <a name="logging-in-aspnet-core"></a><span data-ttu-id="33f8e-104">ASP.NET Core 中的日志记录</span><span class="sxs-lookup"><span data-stu-id="33f8e-104">Logging in ASP.NET Core</span></span>

<span data-ttu-id="33f8e-105">作者：[Steve Smith](https://ardalis.com/) 和 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="33f8e-105">By [Steve Smith](https://ardalis.com/) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="33f8e-106">ASP.NET Core 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。</span><span class="sxs-lookup"><span data-stu-id="33f8e-106">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="33f8e-107">本文介绍了如何将日志记录 API 与内置提供程序一起使用。</span><span class="sxs-lookup"><span data-stu-id="33f8e-107">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="33f8e-108">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="33f8e-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="33f8e-109">添加提供程序</span><span class="sxs-lookup"><span data-stu-id="33f8e-109">Add providers</span></span>

<span data-ttu-id="33f8e-110">日志记录提供程序会显示或存储日志。</span><span class="sxs-lookup"><span data-stu-id="33f8e-110">A logging provider displays or stores logs.</span></span> <span data-ttu-id="33f8e-111">例如，控制台提供程序在控制台上显示日志，Azure Application Insights 提供程序会将这些日志存储在 Azure Application Insights 中。</span><span class="sxs-lookup"><span data-stu-id="33f8e-111">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="33f8e-112">可通过添加多个提供程序将日志发送到多个目标。</span><span class="sxs-lookup"><span data-stu-id="33f8e-112">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="33f8e-113">要添加提供程序，请在 Program.cs 中调用提供程序的 `Add{provider name}` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="33f8e-113">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=17-19)]

<span data-ttu-id="33f8e-114">默认项目模板调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 扩展方法，该操作将添加以下日志记录提供程序：</span><span class="sxs-lookup"><span data-stu-id="33f8e-114">The default project template calls the <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> extension method, which adds the following logging providers:</span></span>

* <span data-ttu-id="33f8e-115">控制台</span><span class="sxs-lookup"><span data-stu-id="33f8e-115">Console</span></span>
* <span data-ttu-id="33f8e-116">调试</span><span class="sxs-lookup"><span data-stu-id="33f8e-116">Debug</span></span>
* <span data-ttu-id="33f8e-117">EventSource（启动位置：ASP.NET Core 2.2）</span><span class="sxs-lookup"><span data-stu-id="33f8e-117">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="33f8e-118">如果使用 `CreateDefaultBuilder`，则可自行选择提供程序来替换默认提供程序。</span><span class="sxs-lookup"><span data-stu-id="33f8e-118">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="33f8e-119">调用 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>，然后添加所需的提供程序。</span><span class="sxs-lookup"><span data-stu-id="33f8e-119">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="33f8e-120">要使用提供程序，请安装其 NuGet 包，并在 <xref:Microsoft.Extensions.Logging.ILoggerFactory> 的实例上调用提供程序的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="33f8e-120">To use a provider, install its NuGet package and call the provider's extension method on an instance of <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample//Startup.cs?name=snippet_AddConsoleAndDebug&highlight=3,5-7)]

<span data-ttu-id="33f8e-121">ASP.NET Core [依赖关系注入](xref:fundamentals/dependency-injection) (DI) 将提供 `ILoggerFactory` 实例。</span><span class="sxs-lookup"><span data-stu-id="33f8e-121">ASP.NET Core [dependency injection (DI)](xref:fundamentals/dependency-injection) provides the `ILoggerFactory` instance.</span></span> <span data-ttu-id="33f8e-122">在 [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) 和 [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) 包中定义了 `AddConsole` 和 `AddDebug` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="33f8e-122">The `AddConsole` and `AddDebug` extension methods are defined in the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) and [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) packages.</span></span> <span data-ttu-id="33f8e-123">每个扩展方法都调用 `ILoggerFactory.AddProvider` 方法，传入提供程序的一个实例。</span><span class="sxs-lookup"><span data-stu-id="33f8e-123">Each extension method calls the `ILoggerFactory.AddProvider` method, passing in an instance of the provider.</span></span>

> [!NOTE]
> <span data-ttu-id="33f8e-124">[示例应用](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/1.x)在 `Startup.Configure` 方法中添加了日志提供程序。</span><span class="sxs-lookup"><span data-stu-id="33f8e-124">The [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/1.x) adds logging providers in the `Startup.Configure` method.</span></span> <span data-ttu-id="33f8e-125">要从先前执行的代码获取日志输出，请在 `Startup` 类构造函数中添加日志提供程序。</span><span class="sxs-lookup"><span data-stu-id="33f8e-125">To obtain log output from code that executes earlier, add logging providers in the `Startup` class constructor.</span></span>

::: moniker-end

<span data-ttu-id="33f8e-126">详细了解[内置日志记录提供程序](#built-in-logging-providers)，以及本文稍后部分介绍的[第三方日志记录提供程序](#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="33f8e-126">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="33f8e-127">创建日志</span><span class="sxs-lookup"><span data-stu-id="33f8e-127">Create logs</span></span>

<span data-ttu-id="33f8e-128">从 DI 中获取 <xref:Microsoft.Extensions.Logging.ILogger`1> 对象。</span><span class="sxs-lookup"><span data-stu-id="33f8e-128">Get an <xref:Microsoft.Extensions.Logging.ILogger`1> object from DI.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="33f8e-129">以下控制器示例会创建 `Information` 和 `Warning` 日志。</span><span class="sxs-lookup"><span data-stu-id="33f8e-129">The following controller example creates `Information` and `Warning` logs.</span></span> <span data-ttu-id="33f8e-130">类别为 `TodoApiSample.Controllers.TodoController`（示例应用中 `TodoController` 的完全限定类名）：</span><span class="sxs-lookup"><span data-stu-id="33f8e-130">The *category* is `TodoApiSample.Controllers.TodoController` (the fully qualified class name of `TodoController` in the sample app):</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=4,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="33f8e-131">以下 Razor 页面示例会创建“级别”为 `Information` 且“类别”为 `TodoApiSample.Pages.AboutModel` 的日志：</span><span class="sxs-lookup"><span data-stu-id="33f8e-131">The following Razor Pages example creates logs with `Information` as the *level* and `TodoApiSample.Pages.AboutModel` as the *category*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3, 7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="33f8e-132">上述示例创建了“级别”为 `Information` 和 `Warning`，且“类别”为 `TodoController` 的日志。</span><span class="sxs-lookup"><span data-stu-id="33f8e-132">The preceding example creates logs with `Information` and `Warning` as the *level* and `TodoController` class as the *category*.</span></span> 

::: moniker-end

<span data-ttu-id="33f8e-133">日志“级别”代表所记录事件的严重程度。</span><span class="sxs-lookup"><span data-stu-id="33f8e-133">The Log *level* indicates the severity of the logged event.</span></span> <span data-ttu-id="33f8e-134">日志“类别”是与每个日志关联的字符串。</span><span class="sxs-lookup"><span data-stu-id="33f8e-134">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="33f8e-135">`ILogger<T>` 实例会创建“类别”为类型 `T` 的完全限定名称的日志。</span><span class="sxs-lookup"><span data-stu-id="33f8e-135">The `ILogger<T>` instance creates logs that have the fully qualified name of type `T` as the category.</span></span> <span data-ttu-id="33f8e-136">本文稍后部分将更详细地介绍[级别](#log-level)和[类别](#log-category)。</span><span class="sxs-lookup"><span data-stu-id="33f8e-136">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span> 

::: moniker range=">= aspnetcore-2.0"

### <a name="create-logs-in-startup"></a><span data-ttu-id="33f8e-137">启动时创建日志</span><span class="sxs-lookup"><span data-stu-id="33f8e-137">Create logs in Startup</span></span>

<span data-ttu-id="33f8e-138">要将日志写入 `Startup` 类，构造函数签名需包含 `ILogger` 参数：</span><span class="sxs-lookup"><span data-stu-id="33f8e-138">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,19,26)]

### <a name="create-logs-in-program"></a><span data-ttu-id="33f8e-139">在程序中创建日志</span><span class="sxs-lookup"><span data-stu-id="33f8e-139">Create logs in Program</span></span>

<span data-ttu-id="33f8e-140">要将日志写入 `Program` 类，请从 DI 获取 `ILogger` 实例：</span><span class="sxs-lookup"><span data-stu-id="33f8e-140">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="33f8e-141">没有异步记录器方法</span><span class="sxs-lookup"><span data-stu-id="33f8e-141">No asynchronous logger methods</span></span>

<span data-ttu-id="33f8e-142">日志记录应该会很快，不值得牺牲性能来使用异步代码。</span><span class="sxs-lookup"><span data-stu-id="33f8e-142">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="33f8e-143">如果你的日志数据存储很慢，请不要直接写入它。</span><span class="sxs-lookup"><span data-stu-id="33f8e-143">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="33f8e-144">首先考虑将日志消息写入快速存储，售后再将其变为慢速存储。</span><span class="sxs-lookup"><span data-stu-id="33f8e-144">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="33f8e-145">例如，记录到由另一进程读取和暂留以减缓存储的消息队列。</span><span class="sxs-lookup"><span data-stu-id="33f8e-145">For example, log to a message queue that's read and persisted to slow storage by another process.</span></span>

## <a name="configuration"></a><span data-ttu-id="33f8e-146">配置</span><span class="sxs-lookup"><span data-stu-id="33f8e-146">Configuration</span></span>

<span data-ttu-id="33f8e-147">日志记录提供程序配置由一个或多个配置提供程序提供：</span><span class="sxs-lookup"><span data-stu-id="33f8e-147">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="33f8e-148">文件格式（INI、JSON 和 XML）。</span><span class="sxs-lookup"><span data-stu-id="33f8e-148">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="33f8e-149">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="33f8e-149">Command-line arguments.</span></span>
* <span data-ttu-id="33f8e-150">环境变量。</span><span class="sxs-lookup"><span data-stu-id="33f8e-150">Environment variables.</span></span>
* <span data-ttu-id="33f8e-151">内存中的 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="33f8e-151">In-memory .NET objects.</span></span>
* <span data-ttu-id="33f8e-152">未加密的[机密管理器](xref:security/app-secrets)存储。</span><span class="sxs-lookup"><span data-stu-id="33f8e-152">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="33f8e-153">加密的用户存储，如 [Azure Key Vault](xref:security/key-vault-configuration)。</span><span class="sxs-lookup"><span data-stu-id="33f8e-153">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="33f8e-154">（已安装或已创建的）自定义提供程序。</span><span class="sxs-lookup"><span data-stu-id="33f8e-154">Custom providers (installed or created).</span></span>

<span data-ttu-id="33f8e-155">例如，日志记录配置通常由应用设置文件的 `Logging` 部分提供。</span><span class="sxs-lookup"><span data-stu-id="33f8e-155">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="33f8e-156">以下示例显示了典型 *appsettings.Development.json* 文件的内容：</span><span class="sxs-lookup"><span data-stu-id="33f8e-156">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

::: moniker range=">= aspnetcore-2.1"

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="33f8e-157">`Logging` 属性可具有 `LogLevel` 和日志提供程序属性（显示控制台）。</span><span class="sxs-lookup"><span data-stu-id="33f8e-157">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="33f8e-158">`Logging` 下的 `LogLevel` 属性指定了用于记录所选类别的最低[级别](#log-level)。</span><span class="sxs-lookup"><span data-stu-id="33f8e-158">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="33f8e-159">在本例中，`System` 和 `Microsoft` 类别在 `Information` 级别记录，其他均在 `Debug` 级别记录。</span><span class="sxs-lookup"><span data-stu-id="33f8e-159">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="33f8e-160">`Logging` 下的其他属性均指定了日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="33f8e-160">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="33f8e-161">本示例针对控制台提供程序。</span><span class="sxs-lookup"><span data-stu-id="33f8e-161">The example is for the Console provider.</span></span> <span data-ttu-id="33f8e-162">如果提供程序支持[日志作用域](#log-scopes)，则 `IncludeScopes` 将指示是否启用这些域。</span><span class="sxs-lookup"><span data-stu-id="33f8e-162">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="33f8e-163">提供程序属性（例如本例的 `Console`）也可指定 `LogLevel` 属性。</span><span class="sxs-lookup"><span data-stu-id="33f8e-163">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="33f8e-164">提供程序下的 `LogLevel` 指定了该提供程序记录的级别。</span><span class="sxs-lookup"><span data-stu-id="33f8e-164">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="33f8e-165">如果在 `Logging.{providername}.LogLevel` 中指定了级别，则这些级别将重写 `Logging.LogLevel` 中设置的所有内容。</span><span class="sxs-lookup"><span data-stu-id="33f8e-165">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    }
  }
}
```

<span data-ttu-id="33f8e-166">`LogLevel` 项表示日志名称。</span><span class="sxs-lookup"><span data-stu-id="33f8e-166">`LogLevel` keys represent log names.</span></span> <span data-ttu-id="33f8e-167">`Default` 项适用于未显式列出的日志。</span><span class="sxs-lookup"><span data-stu-id="33f8e-167">The `Default` key applies to logs not explicitly listed.</span></span> <span data-ttu-id="33f8e-168">其值表示应用于给定日志的[日志级别](#log-level)。</span><span class="sxs-lookup"><span data-stu-id="33f8e-168">The value represents the [log level](#log-level) applied to the given log.</span></span>

::: moniker-end

<span data-ttu-id="33f8e-169">若要了解如何实现配置提供程序，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="33f8e-169">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="33f8e-170">日志记录输出示例</span><span class="sxs-lookup"><span data-stu-id="33f8e-170">Sample logging output</span></span>

<span data-ttu-id="33f8e-171">使用上一部分中显示的示例代码从命令行运行应用时，将在控制台中看到日志。</span><span class="sxs-lookup"><span data-stu-id="33f8e-171">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="33f8e-172">以下是控制台输出示例：</span><span class="sxs-lookup"><span data-stu-id="33f8e-172">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

<span data-ttu-id="33f8e-173">通过向 `http://localhost:5000/api/todo/0` 处的示例应用发出 HTTP Get 请求来生成前述日志。</span><span class="sxs-lookup"><span data-stu-id="33f8e-173">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="33f8e-174">在 Visual Studio 中运行示例应用时，“调试”窗口中将显示如下日志：</span><span class="sxs-lookup"><span data-stu-id="33f8e-174">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>


```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="33f8e-175">由上一部分所示的 `ILogger` 调用创建的日志是以“TodoApi.Controllers.TodoController”开头的。</span><span class="sxs-lookup"><span data-stu-id="33f8e-175">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi.Controllers.TodoController".</span></span> <span data-ttu-id="33f8e-176">以“Microsoft”类别开头的日志来自 ASP.NET Core 框架代码。</span><span class="sxs-lookup"><span data-stu-id="33f8e-176">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="33f8e-177">ASP.NET Core 和应用程序代码使用相同的日志记录 API 和提供程序。</span><span class="sxs-lookup"><span data-stu-id="33f8e-177">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="33f8e-178">本文余下部分将介绍有关日志记录的某些详细信息及选项。</span><span class="sxs-lookup"><span data-stu-id="33f8e-178">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="33f8e-179">NuGet 包</span><span class="sxs-lookup"><span data-stu-id="33f8e-179">NuGet packages</span></span>

<span data-ttu-id="33f8e-180">`ILogger` 和 `ILoggerFactory` 接口位于 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 中，其默认实现位于 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 中。</span><span class="sxs-lookup"><span data-stu-id="33f8e-180">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="33f8e-181">日志类别</span><span class="sxs-lookup"><span data-stu-id="33f8e-181">Log category</span></span>

<span data-ttu-id="33f8e-182">创建 `ILogger` 对象后，将为其指定“类别”。</span><span class="sxs-lookup"><span data-stu-id="33f8e-182">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="33f8e-183">该类别包含在由此 `Ilogger` 实例创建的每条日志消息中。</span><span class="sxs-lookup"><span data-stu-id="33f8e-183">That category is included with each log message created by that instance of `Ilogger`.</span></span> <span data-ttu-id="33f8e-184">类别可以是任何字符串，但约定需使用类名，例如“TodoApi.Controllers.TodoController”。</span><span class="sxs-lookup"><span data-stu-id="33f8e-184">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="33f8e-185">使用 `ILogger<T>` 获取一个 `ILogger` 实例，该实例使用 `T` 的完全限定类型名称作为类别：</span><span class="sxs-lookup"><span data-stu-id="33f8e-185">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

<span data-ttu-id="33f8e-186">要显式指定类别，请调用 `ILoggerFactory.CreateLogger`：</span><span class="sxs-lookup"><span data-stu-id="33f8e-186">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

<span data-ttu-id="33f8e-187">`ILogger<T>` 相当于使用 `T` 的完全限定类型名称来调用 `CreateLogger`。</span><span class="sxs-lookup"><span data-stu-id="33f8e-187">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="33f8e-188">日志级别</span><span class="sxs-lookup"><span data-stu-id="33f8e-188">Log level</span></span>

<span data-ttu-id="33f8e-189">每个日志都指定了一个 <xref:Microsoft.Extensions.Logging.LogLevel> 值。</span><span class="sxs-lookup"><span data-stu-id="33f8e-189">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="33f8e-190">日志级别指示严重性或重要程度。</span><span class="sxs-lookup"><span data-stu-id="33f8e-190">The log level indicates the severity or importance.</span></span> <span data-ttu-id="33f8e-191">例如，可在方法正常结束时写入 `Information` 日志，在方法返回“404 找不到”状态代码时写入 `Warning` 日志。</span><span class="sxs-lookup"><span data-stu-id="33f8e-191">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="33f8e-192">下面的代码会创建 `Information` 和 `Warning` 日志：</span><span class="sxs-lookup"><span data-stu-id="33f8e-192">The following code creates `Information` and `Warning` logs:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="33f8e-193">在上述代码中，第一个参数是[日志事件 ID](#log-event-id)。</span><span class="sxs-lookup"><span data-stu-id="33f8e-193">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="33f8e-194">第二个参数是消息模板，其中的占位符用于填写剩余方法形参提供的实参值。</span><span class="sxs-lookup"><span data-stu-id="33f8e-194">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="33f8e-195">稍后将在本文的[消息模板部分](#log-message-template)介绍方法参数。</span><span class="sxs-lookup"><span data-stu-id="33f8e-195">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="33f8e-196">在方法名称中包含级别的日志方法（例如 `LogInformation` 和 `LogWarning`）是 [ILogger 的扩展方法](xref:Microsoft.Extensions.Logging.LoggerExtensions)。</span><span class="sxs-lookup"><span data-stu-id="33f8e-196">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="33f8e-197">这些方法会调用可接受 `LogLevel` 参数的 `Log` 方法。</span><span class="sxs-lookup"><span data-stu-id="33f8e-197">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="33f8e-198">可直接调用 `Log` 方法而不调用其中某个扩展方法，但语法相对复杂。</span><span class="sxs-lookup"><span data-stu-id="33f8e-198">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="33f8e-199">有关详细信息，请参阅 <xref:Microsoft.Extensions.Logging.ILogger> 和[记录器扩展源代码](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)。</span><span class="sxs-lookup"><span data-stu-id="33f8e-199">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="33f8e-200">ASP.NET Core 定义了以下日志级别（按严重性从低到高排列）。</span><span class="sxs-lookup"><span data-stu-id="33f8e-200">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="33f8e-201">跟踪 = 0</span><span class="sxs-lookup"><span data-stu-id="33f8e-201">Trace = 0</span></span>

  <span data-ttu-id="33f8e-202">有关通常仅用于调试的信息。</span><span class="sxs-lookup"><span data-stu-id="33f8e-202">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="33f8e-203">这些消息可能包含敏感应用程序数据，因此不得在生产环境中启用它们。</span><span class="sxs-lookup"><span data-stu-id="33f8e-203">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="33f8e-204">默认情况下禁用。</span><span class="sxs-lookup"><span data-stu-id="33f8e-204">*Disabled by default.*</span></span>

* <span data-ttu-id="33f8e-205">调试 = 1</span><span class="sxs-lookup"><span data-stu-id="33f8e-205">Debug = 1</span></span>

  <span data-ttu-id="33f8e-206">有关在开发和调试中可能有用的信息。</span><span class="sxs-lookup"><span data-stu-id="33f8e-206">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="33f8e-207">示例:`Entering method Configure with flag set to true.` 由于日志数量过多，因此仅当执行故障排除时，才在生产中启用 `Debug` 级别日志。</span><span class="sxs-lookup"><span data-stu-id="33f8e-207">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="33f8e-208">信息 = 2</span><span class="sxs-lookup"><span data-stu-id="33f8e-208">Information = 2</span></span>

  <span data-ttu-id="33f8e-209">用于跟踪应用的常规流。</span><span class="sxs-lookup"><span data-stu-id="33f8e-209">For tracking the general flow of the app.</span></span> <span data-ttu-id="33f8e-210">这些日志通常有长期价值。</span><span class="sxs-lookup"><span data-stu-id="33f8e-210">These logs typically have some long-term value.</span></span> <span data-ttu-id="33f8e-211">示例：`Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="33f8e-211">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="33f8e-212">警告 = 3</span><span class="sxs-lookup"><span data-stu-id="33f8e-212">Warning = 3</span></span>

  <span data-ttu-id="33f8e-213">表示应用流中的异常或意外事件。</span><span class="sxs-lookup"><span data-stu-id="33f8e-213">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="33f8e-214">可能包括不会中断应用运行但仍需调查的错误或其他条件。</span><span class="sxs-lookup"><span data-stu-id="33f8e-214">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="33f8e-215">`Warning` 日志级别常用于已处理的异常。</span><span class="sxs-lookup"><span data-stu-id="33f8e-215">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="33f8e-216">示例：`FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="33f8e-216">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="33f8e-217">错误 = 4</span><span class="sxs-lookup"><span data-stu-id="33f8e-217">Error = 4</span></span>

  <span data-ttu-id="33f8e-218">表示无法处理的错误和异常。</span><span class="sxs-lookup"><span data-stu-id="33f8e-218">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="33f8e-219">这些消息指示的是当前活动或操作（例如当前 HTTP 请求）中的失败，而不是整个应用中的失败。</span><span class="sxs-lookup"><span data-stu-id="33f8e-219">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="33f8e-220">日志消息示例：`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="33f8e-220">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="33f8e-221">严重 = 5</span><span class="sxs-lookup"><span data-stu-id="33f8e-221">Critical = 5</span></span>

  <span data-ttu-id="33f8e-222">需要立即关注的失败。</span><span class="sxs-lookup"><span data-stu-id="33f8e-222">For failures that require immediate attention.</span></span> <span data-ttu-id="33f8e-223">例如数据丢失、磁盘空间不足。</span><span class="sxs-lookup"><span data-stu-id="33f8e-223">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="33f8e-224">使用日志级别控制写入到特定存储介质或显示窗口的日志输出量。</span><span class="sxs-lookup"><span data-stu-id="33f8e-224">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="33f8e-225">例如:</span><span class="sxs-lookup"><span data-stu-id="33f8e-225">For example:</span></span>

* <span data-ttu-id="33f8e-226">在生产中，通过 `Information` 级别将 `Trace` 发送到卷数据存储。</span><span class="sxs-lookup"><span data-stu-id="33f8e-226">In production, send `Trace` through `Information` level to a volume data store.</span></span> <span data-ttu-id="33f8e-227">通过 `Critical` 级别将 `Warning` 发送到值数据存储。</span><span class="sxs-lookup"><span data-stu-id="33f8e-227">Send `Warning` through `Critical` to a value data store.</span></span>
* <span data-ttu-id="33f8e-228">在开发过程中，通过`Critical` 级别将 `Warning` 发送到控制台，并在进行故障排除时通过 `Information` 级别添加 `Trace`。</span><span class="sxs-lookup"><span data-stu-id="33f8e-228">During development, send `Warning` through `Critical` to the console, and add `Trace` through `Information` when troubleshooting.</span></span>

<span data-ttu-id="33f8e-229">本文稍后的[日志筛选](#log-filtering)部分介绍如何控制提供程序处理的日志级别。</span><span class="sxs-lookup"><span data-stu-id="33f8e-229">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="33f8e-230">ASP.NET Core 为框架事件写入日志。</span><span class="sxs-lookup"><span data-stu-id="33f8e-230">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="33f8e-231">本文前面部分提供的日志示例排除了低于 `Information` 级别的日志，因此未创建 `Debug` 或 `Trace` 级别日志。</span><span class="sxs-lookup"><span data-stu-id="33f8e-231">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="33f8e-232">以下示例介绍了通过运行配置为显示 `Debug` 日志的示例应用而生成的控制台日志：</span><span class="sxs-lookup"><span data-stu-id="33f8e-232">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="33f8e-233">日志事件 ID</span><span class="sxs-lookup"><span data-stu-id="33f8e-233">Log event ID</span></span>

<span data-ttu-id="33f8e-234">每个日志都可指定一个事件 ID。</span><span class="sxs-lookup"><span data-stu-id="33f8e-234">Each log can specify an *event ID*.</span></span> <span data-ttu-id="33f8e-235">该示例应用通过使用本地定义的 `LoggingEvents` 类来执行此操作：</span><span class="sxs-lookup"><span data-stu-id="33f8e-235">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

<span data-ttu-id="33f8e-236">事件 ID 与一组事件相关联。</span><span class="sxs-lookup"><span data-stu-id="33f8e-236">An event ID associates a set of events.</span></span> <span data-ttu-id="33f8e-237">例如，与在页面上显示项列表相关的所有日志可能是 1001。</span><span class="sxs-lookup"><span data-stu-id="33f8e-237">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="33f8e-238">日志记录提供程序可将事件 ID 存储在 ID 字段中，存储在日志记录消息中，或者不进行存储。</span><span class="sxs-lookup"><span data-stu-id="33f8e-238">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="33f8e-239">调试提供程序不显示事件 ID。</span><span class="sxs-lookup"><span data-stu-id="33f8e-239">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="33f8e-240">控制台提供程序在类别后的括号中显示事件 ID：</span><span class="sxs-lookup"><span data-stu-id="33f8e-240">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="33f8e-241">日志消息模板</span><span class="sxs-lookup"><span data-stu-id="33f8e-241">Log message template</span></span>

<span data-ttu-id="33f8e-242">每个日志都会指定一个消息模板。</span><span class="sxs-lookup"><span data-stu-id="33f8e-242">Each log specifies a message template.</span></span> <span data-ttu-id="33f8e-243">消息模板可包含要填写参数的占位符。</span><span class="sxs-lookup"><span data-stu-id="33f8e-243">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="33f8e-244">请在占位符中使用名称而不是数字。</span><span class="sxs-lookup"><span data-stu-id="33f8e-244">Use names for the placeholders, not numbers.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="33f8e-245">占位符的顺序（而非其名称）决定了为其提供值的参数。</span><span class="sxs-lookup"><span data-stu-id="33f8e-245">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="33f8e-246">在以下代码中，请注意消息模板中的参数名称未按顺序排列：</span><span class="sxs-lookup"><span data-stu-id="33f8e-246">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="33f8e-247">此代码创建了一个参数值按顺序排列的日志消息：</span><span class="sxs-lookup"><span data-stu-id="33f8e-247">This code creates a log message with the parameter values in sequence:</span></span>

```
Parameter values: parm1, parm2
```

<span data-ttu-id="33f8e-248">日志记录框架按此方式工作，这样日志记录提供程序即可实现[语义日志记录，也称为结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="33f8e-248">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="33f8e-249">参数本身会传递给日志记录系统，而不仅仅是格式化的消息模板。</span><span class="sxs-lookup"><span data-stu-id="33f8e-249">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="33f8e-250">通过此信息，日志记录提供程序能够将参数值存储为字段。</span><span class="sxs-lookup"><span data-stu-id="33f8e-250">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="33f8e-251">例如，假设记录器方法调用如下所示：</span><span class="sxs-lookup"><span data-stu-id="33f8e-251">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="33f8e-252">如果要将日志发送到 Azure 表存储，则每个 Azure 表实体都可具有 `ID` 和 `RequestTime` 属性，这简化了对日志数据的查询。</span><span class="sxs-lookup"><span data-stu-id="33f8e-252">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="33f8e-253">无需分析文本消息的超时，查询即可找到特定 `RequestTime` 范围内的全部日志。</span><span class="sxs-lookup"><span data-stu-id="33f8e-253">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="33f8e-254">日志记录异常</span><span class="sxs-lookup"><span data-stu-id="33f8e-254">Logging exceptions</span></span>

<span data-ttu-id="33f8e-255">记录器方法有可传入异常的重载，如下方示例所示：</span><span class="sxs-lookup"><span data-stu-id="33f8e-255">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

<span data-ttu-id="33f8e-256">不同的提供程序处理异常信息的方式不同。</span><span class="sxs-lookup"><span data-stu-id="33f8e-256">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="33f8e-257">以下是上示代码的调试提供程序输出示例。</span><span class="sxs-lookup"><span data-stu-id="33f8e-257">Here's an example of Debug provider output from the code shown above.</span></span>

```
TodoApi.Controllers.TodoController:Warning: GetById(036dd898-fb01-47e8-9a65-f92eb73cf924) NOT FOUND

System.Exception: Item not found exception.
 at TodoApi.Controllers.TodoController.GetById(String id) in C:\logging\sample\src\TodoApi\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="33f8e-258">日志筛选</span><span class="sxs-lookup"><span data-stu-id="33f8e-258">Log filtering</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="33f8e-259">可为特定或所有提供程序和类别指定最低日志级别。</span><span class="sxs-lookup"><span data-stu-id="33f8e-259">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="33f8e-260">最低级别以下的日志不会传递给该提供程序，因此不会显示或存储它们。</span><span class="sxs-lookup"><span data-stu-id="33f8e-260">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="33f8e-261">要禁止显示所有日志，可将 `LogLevel.None` 指定为最低日志级别。</span><span class="sxs-lookup"><span data-stu-id="33f8e-261">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="33f8e-262">`LogLevel.None` 的整数值为 6，它大于 `LogLevel.Critical` (5)。</span><span class="sxs-lookup"><span data-stu-id="33f8e-262">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="33f8e-263">在配置中创建筛选规则</span><span class="sxs-lookup"><span data-stu-id="33f8e-263">Create filter rules in configuration</span></span>

<span data-ttu-id="33f8e-264">项目模板代码调用 `CreateDefaultBuilder` 来为控制台和调试提供程序设置日志记录。</span><span class="sxs-lookup"><span data-stu-id="33f8e-264">The project template code calls `CreateDefaultBuilder` to set up logging for the Console and Debug providers.</span></span> <span data-ttu-id="33f8e-265">`CreateDefaultBuilder` 方法还使用如下所示的代码，设置日志记录以查找 `Logging` 部分的配置：</span><span class="sxs-lookup"><span data-stu-id="33f8e-265">The `CreateDefaultBuilder` method also sets up logging to look for configuration in a `Logging` section, using code like the following:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=16)]

<span data-ttu-id="33f8e-266">配置数据按提供程序和类别指定最低日志级别，如下方示例所示：</span><span class="sxs-lookup"><span data-stu-id="33f8e-266">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="33f8e-267">此 JSON 将创建 6 条筛选规则：1 条用于调试提供程序， 4 条用于控制台提供程序， 1 条用于所有提供程序。</span><span class="sxs-lookup"><span data-stu-id="33f8e-267">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="33f8e-268">创建 `ILogger` 对象时，为每个提供程序选择一个规则。</span><span class="sxs-lookup"><span data-stu-id="33f8e-268">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="33f8e-269">代码中的筛选规则</span><span class="sxs-lookup"><span data-stu-id="33f8e-269">Filter rules in code</span></span>

<span data-ttu-id="33f8e-270">下面的示例演示了如何在代码中注册筛选规则：</span><span class="sxs-lookup"><span data-stu-id="33f8e-270">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="33f8e-271">第二个 `AddFilter` 使用类型名称来指定调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="33f8e-271">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="33f8e-272">第一个 `AddFilter` 应用于全部提供程序，因为它未指定提供程序类型。</span><span class="sxs-lookup"><span data-stu-id="33f8e-272">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="33f8e-273">如何应用筛选规则</span><span class="sxs-lookup"><span data-stu-id="33f8e-273">How filtering rules are applied</span></span>

<span data-ttu-id="33f8e-274">先前示例中显示的配置数据和 `AddFilter` 代码会创建下表所示的规则。</span><span class="sxs-lookup"><span data-stu-id="33f8e-274">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="33f8e-275">前六条由配置示例创建，后两条由代码示例创建。</span><span class="sxs-lookup"><span data-stu-id="33f8e-275">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="33f8e-276">数字</span><span class="sxs-lookup"><span data-stu-id="33f8e-276">Number</span></span> | <span data-ttu-id="33f8e-277">提供程序</span><span class="sxs-lookup"><span data-stu-id="33f8e-277">Provider</span></span>      | <span data-ttu-id="33f8e-278">类别的开头为...</span><span class="sxs-lookup"><span data-stu-id="33f8e-278">Categories that begin with ...</span></span>          | <span data-ttu-id="33f8e-279">最低日志级别</span><span class="sxs-lookup"><span data-stu-id="33f8e-279">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="33f8e-280">1</span><span class="sxs-lookup"><span data-stu-id="33f8e-280">1</span></span>      | <span data-ttu-id="33f8e-281">调试</span><span class="sxs-lookup"><span data-stu-id="33f8e-281">Debug</span></span>         | <span data-ttu-id="33f8e-282">全部类别</span><span class="sxs-lookup"><span data-stu-id="33f8e-282">All categories</span></span>                          | <span data-ttu-id="33f8e-283">信息</span><span class="sxs-lookup"><span data-stu-id="33f8e-283">Information</span></span>       |
| <span data-ttu-id="33f8e-284">2</span><span class="sxs-lookup"><span data-stu-id="33f8e-284">2</span></span>      | <span data-ttu-id="33f8e-285">控制台</span><span class="sxs-lookup"><span data-stu-id="33f8e-285">Console</span></span>       | <span data-ttu-id="33f8e-286">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="33f8e-286">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="33f8e-287">警告</span><span class="sxs-lookup"><span data-stu-id="33f8e-287">Warning</span></span>           |
| <span data-ttu-id="33f8e-288">3</span><span class="sxs-lookup"><span data-stu-id="33f8e-288">3</span></span>      | <span data-ttu-id="33f8e-289">控制台</span><span class="sxs-lookup"><span data-stu-id="33f8e-289">Console</span></span>       | <span data-ttu-id="33f8e-290">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="33f8e-290">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="33f8e-291">调试</span><span class="sxs-lookup"><span data-stu-id="33f8e-291">Debug</span></span>             |
| <span data-ttu-id="33f8e-292">4</span><span class="sxs-lookup"><span data-stu-id="33f8e-292">4</span></span>      | <span data-ttu-id="33f8e-293">控制台</span><span class="sxs-lookup"><span data-stu-id="33f8e-293">Console</span></span>       | <span data-ttu-id="33f8e-294">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="33f8e-294">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="33f8e-295">Error</span><span class="sxs-lookup"><span data-stu-id="33f8e-295">Error</span></span>             |
| <span data-ttu-id="33f8e-296">5</span><span class="sxs-lookup"><span data-stu-id="33f8e-296">5</span></span>      | <span data-ttu-id="33f8e-297">控制台</span><span class="sxs-lookup"><span data-stu-id="33f8e-297">Console</span></span>       | <span data-ttu-id="33f8e-298">全部类别</span><span class="sxs-lookup"><span data-stu-id="33f8e-298">All categories</span></span>                          | <span data-ttu-id="33f8e-299">信息</span><span class="sxs-lookup"><span data-stu-id="33f8e-299">Information</span></span>       |
| <span data-ttu-id="33f8e-300">6</span><span class="sxs-lookup"><span data-stu-id="33f8e-300">6</span></span>      | <span data-ttu-id="33f8e-301">全部提供程序</span><span class="sxs-lookup"><span data-stu-id="33f8e-301">All providers</span></span> | <span data-ttu-id="33f8e-302">全部类别</span><span class="sxs-lookup"><span data-stu-id="33f8e-302">All categories</span></span>                          | <span data-ttu-id="33f8e-303">调试</span><span class="sxs-lookup"><span data-stu-id="33f8e-303">Debug</span></span>             |
| <span data-ttu-id="33f8e-304">7</span><span class="sxs-lookup"><span data-stu-id="33f8e-304">7</span></span>      | <span data-ttu-id="33f8e-305">全部提供程序</span><span class="sxs-lookup"><span data-stu-id="33f8e-305">All providers</span></span> | <span data-ttu-id="33f8e-306">系统</span><span class="sxs-lookup"><span data-stu-id="33f8e-306">System</span></span>                                  | <span data-ttu-id="33f8e-307">调试</span><span class="sxs-lookup"><span data-stu-id="33f8e-307">Debug</span></span>             |
| <span data-ttu-id="33f8e-308">8</span><span class="sxs-lookup"><span data-stu-id="33f8e-308">8</span></span>      | <span data-ttu-id="33f8e-309">调试</span><span class="sxs-lookup"><span data-stu-id="33f8e-309">Debug</span></span>         | <span data-ttu-id="33f8e-310">Microsoft</span><span class="sxs-lookup"><span data-stu-id="33f8e-310">Microsoft</span></span>                               | <span data-ttu-id="33f8e-311">跟踪</span><span class="sxs-lookup"><span data-stu-id="33f8e-311">Trace</span></span>             |

<span data-ttu-id="33f8e-312">创建 `ILogger` 对象时，`ILoggerFactory` 对象将根据提供程序选择一条规则，将其应用于该记录器。</span><span class="sxs-lookup"><span data-stu-id="33f8e-312">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="33f8e-313">将按所选规则筛选 `ILogger` 实例写入的所有消息。</span><span class="sxs-lookup"><span data-stu-id="33f8e-313">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="33f8e-314">从可用规则中为每个提供程序和类别对选择最具体的规则。</span><span class="sxs-lookup"><span data-stu-id="33f8e-314">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="33f8e-315">在为给定的类别创建 `ILogger` 时，以下算法将用于每个提供程序：</span><span class="sxs-lookup"><span data-stu-id="33f8e-315">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="33f8e-316">选择匹配提供程序或其别名的所有规则。</span><span class="sxs-lookup"><span data-stu-id="33f8e-316">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="33f8e-317">如果找不到任何匹配项，则选择提供程序为空的所有规则。</span><span class="sxs-lookup"><span data-stu-id="33f8e-317">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="33f8e-318">根据上一步的结果，选择具有最长匹配类别前缀的规则。</span><span class="sxs-lookup"><span data-stu-id="33f8e-318">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="33f8e-319">如果找不到任何匹配项，则选择未指定类别的所有规则。</span><span class="sxs-lookup"><span data-stu-id="33f8e-319">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="33f8e-320">如果选择了多条规则，则采用最后一条。</span><span class="sxs-lookup"><span data-stu-id="33f8e-320">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="33f8e-321">如果未选择任何规则，则使用 `MinimumLevel`。</span><span class="sxs-lookup"><span data-stu-id="33f8e-321">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="33f8e-322">假设你使用上述规则列表为类别“Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine”创建了 `ILogger` 对象：</span><span class="sxs-lookup"><span data-stu-id="33f8e-322">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="33f8e-323">对于调试提供程序，规则 1、6 和 8 适用。</span><span class="sxs-lookup"><span data-stu-id="33f8e-323">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="33f8e-324">规则 8 最为具体，因此选择了它。</span><span class="sxs-lookup"><span data-stu-id="33f8e-324">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="33f8e-325">对于控制台提供程序，符合的有规则 3、规则 4、规则 5 和规则 6。</span><span class="sxs-lookup"><span data-stu-id="33f8e-325">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="33f8e-326">规则 3 最为具体。</span><span class="sxs-lookup"><span data-stu-id="33f8e-326">Rule 3 is most specific.</span></span>

<span data-ttu-id="33f8e-327">生成的 `ILogger` 实例将 `Trace` 级别及更高级别的日志发送到调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="33f8e-327">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="33f8e-328">`Debug` 级别及更高级别的日志会发送到控制台提供程序。</span><span class="sxs-lookup"><span data-stu-id="33f8e-328">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="33f8e-329">提供程序别名</span><span class="sxs-lookup"><span data-stu-id="33f8e-329">Provider aliases</span></span>

<span data-ttu-id="33f8e-330">每个提供程序都定义了一个别名；可在配置中使用该别名来代替完全限定的类型名称。</span><span class="sxs-lookup"><span data-stu-id="33f8e-330">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="33f8e-331">对于内置提供程序，请使用以下别名：</span><span class="sxs-lookup"><span data-stu-id="33f8e-331">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="33f8e-332">控制台</span><span class="sxs-lookup"><span data-stu-id="33f8e-332">Console</span></span>
* <span data-ttu-id="33f8e-333">调试</span><span class="sxs-lookup"><span data-stu-id="33f8e-333">Debug</span></span>
* <span data-ttu-id="33f8e-334">EventLog</span><span class="sxs-lookup"><span data-stu-id="33f8e-334">EventLog</span></span>
* <span data-ttu-id="33f8e-335">AzureAppServices</span><span class="sxs-lookup"><span data-stu-id="33f8e-335">AzureAppServices</span></span>
* <span data-ttu-id="33f8e-336">TraceSource</span><span class="sxs-lookup"><span data-stu-id="33f8e-336">TraceSource</span></span>
* <span data-ttu-id="33f8e-337">EventSource</span><span class="sxs-lookup"><span data-stu-id="33f8e-337">EventSource</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="33f8e-338">默认最低级别</span><span class="sxs-lookup"><span data-stu-id="33f8e-338">Default minimum level</span></span>

<span data-ttu-id="33f8e-339">仅当配置或代码中的规则对给定提供程序和类别都不适用时，最低级别设置才会生效。</span><span class="sxs-lookup"><span data-stu-id="33f8e-339">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="33f8e-340">下面的示例演示如何设置最低级别：</span><span class="sxs-lookup"><span data-stu-id="33f8e-340">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="33f8e-341">如果没有明确设置最低级别，则默认值为 `Information`，它表示 `Trace` 和 `Debug` 日志将被忽略。</span><span class="sxs-lookup"><span data-stu-id="33f8e-341">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="33f8e-342">筛选器函数</span><span class="sxs-lookup"><span data-stu-id="33f8e-342">Filter functions</span></span>

<span data-ttu-id="33f8e-343">对配置或代码没有向其分配规则的所有提供程序和类别调用筛选器函数。</span><span class="sxs-lookup"><span data-stu-id="33f8e-343">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="33f8e-344">函数中的代码可访问提供程序类型、类别和日志级别。</span><span class="sxs-lookup"><span data-stu-id="33f8e-344">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="33f8e-345">例如:</span><span class="sxs-lookup"><span data-stu-id="33f8e-345">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="33f8e-346">通过某些日志记录提供程序，可根据日志级别和类别指定何时向存储介质写入日志、何时忽略日志。</span><span class="sxs-lookup"><span data-stu-id="33f8e-346">Some logging providers let you specify when logs should be written to a storage medium or ignored based on log level and category.</span></span>

<span data-ttu-id="33f8e-347">`AddConsole` 和 `AddDebug` 扩展方法提供了接受筛选条件的重载。</span><span class="sxs-lookup"><span data-stu-id="33f8e-347">The `AddConsole` and `AddDebug` extension methods provide overloads that accept filtering criteria.</span></span> <span data-ttu-id="33f8e-348">下列示例代码将使控制台提供程序忽略低于 `Warning` 级别的日志，而使调试提供程序忽略由框架创建的日志。</span><span class="sxs-lookup"><span data-stu-id="33f8e-348">The following sample code causes the console provider to ignore logs below `Warning` level, while the Debug provider ignores logs that the framework creates.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_AddConsoleAndDebugWithFilter&highlight=6-7)]

<span data-ttu-id="33f8e-349">`AddEventLog` 方法拥有接受 `EventLogSettings` 实例的重载，该实例的 `Filter` 属性中可能包含筛选函数。</span><span class="sxs-lookup"><span data-stu-id="33f8e-349">The `AddEventLog` method has an overload that takes an `EventLogSettings` instance, which may contain a filtering function in its `Filter` property.</span></span> <span data-ttu-id="33f8e-350">TraceSource 提供程序不提供以上任何重载，因为其日志记录级别和其他参数都以它使用的 `SourceSwitch` 和 `TraceListener` 为依据。</span><span class="sxs-lookup"><span data-stu-id="33f8e-350">The TraceSource provider doesn't provide any of those overloads, since its logging level and other parameters are based on the `SourceSwitch` and `TraceListener` it uses.</span></span>

<span data-ttu-id="33f8e-351">可使用 `WithFilter` 扩展方法为所有通过 `ILoggerFactory` 实例注册的提供程序设置筛选规则。</span><span class="sxs-lookup"><span data-stu-id="33f8e-351">To set filtering rules for all providers that are registered with an `ILoggerFactory` instance, use the `WithFilter` extension method.</span></span> <span data-ttu-id="33f8e-352">下面的示例将（类别以“Microsoft”或“System”开头的）框架日志限制为警告，并在调试级别记录应用程序代码创建的日志。</span><span class="sxs-lookup"><span data-stu-id="33f8e-352">The example below limits framework logs (category begins with "Microsoft" or "System") to warnings while logging at debug level for logs created by application code.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_FactoryFilter&highlight=6-11)]

<span data-ttu-id="33f8e-353">要防止写入任何日志，请将 `LogLevel.None` 指定为最低日志级别。</span><span class="sxs-lookup"><span data-stu-id="33f8e-353">To prevent any logs from being written, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="33f8e-354">`LogLevel.None` 的整数值为 6，它大于 `LogLevel.Critical` (5)。</span><span class="sxs-lookup"><span data-stu-id="33f8e-354">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="33f8e-355">`WithFilter` 扩展方法由 [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) NuGet 包提供。</span><span class="sxs-lookup"><span data-stu-id="33f8e-355">The `WithFilter` extension method is provided by the [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) NuGet package.</span></span> <span data-ttu-id="33f8e-356">该方法返回一个新的 `ILoggerFactory` 实例，该实例将筛选传递给注册的所有记录器提供程序的日志消息。</span><span class="sxs-lookup"><span data-stu-id="33f8e-356">The method returns a new `ILoggerFactory` instance that will filter the log messages passed to all logger providers registered with it.</span></span> <span data-ttu-id="33f8e-357">它不会影响其他任何 `ILoggerFactory` 实例，包括原始 `ILoggerFactory` 实例。</span><span class="sxs-lookup"><span data-stu-id="33f8e-357">It doesn't affect any other `ILoggerFactory` instances, including the original `ILoggerFactory` instance.</span></span>

::: moniker-end

## <a name="system-categories-and-levels"></a><span data-ttu-id="33f8e-358">系统类别和级别</span><span class="sxs-lookup"><span data-stu-id="33f8e-358">System categories and levels</span></span>

<span data-ttu-id="33f8e-359">下面是 ASP.NET Core 和 Entity Framework Core 使用的一些类别，备注中说明了可从这些类别获取的具体日志：</span><span class="sxs-lookup"><span data-stu-id="33f8e-359">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="33f8e-360">类别</span><span class="sxs-lookup"><span data-stu-id="33f8e-360">Category</span></span>                            | <span data-ttu-id="33f8e-361">说明</span><span class="sxs-lookup"><span data-stu-id="33f8e-361">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="33f8e-362">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="33f8e-362">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="33f8e-363">常规 ASP.NET Core 诊断。</span><span class="sxs-lookup"><span data-stu-id="33f8e-363">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="33f8e-364">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="33f8e-364">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="33f8e-365">考虑、找到并使用了哪些密钥。</span><span class="sxs-lookup"><span data-stu-id="33f8e-365">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="33f8e-366">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="33f8e-366">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="33f8e-367">所允许的主机。</span><span class="sxs-lookup"><span data-stu-id="33f8e-367">Hosts allowed.</span></span> |
| <span data-ttu-id="33f8e-368">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="33f8e-368">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="33f8e-369">HTTP 请求完成的时间和启动时间。</span><span class="sxs-lookup"><span data-stu-id="33f8e-369">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="33f8e-370">加载了哪些承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="33f8e-370">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="33f8e-371">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="33f8e-371">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="33f8e-372">MVC 和 Razor 诊断。</span><span class="sxs-lookup"><span data-stu-id="33f8e-372">MVC and Razor diagnostics.</span></span> <span data-ttu-id="33f8e-373">模型绑定、筛选器执行、视图编译和操作选择。</span><span class="sxs-lookup"><span data-stu-id="33f8e-373">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="33f8e-374">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="33f8e-374">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="33f8e-375">路由匹配信息。</span><span class="sxs-lookup"><span data-stu-id="33f8e-375">Route matching information.</span></span> |
| <span data-ttu-id="33f8e-376">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="33f8e-376">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="33f8e-377">连接启动、停止和保持活动响应。</span><span class="sxs-lookup"><span data-stu-id="33f8e-377">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="33f8e-378">HTTP 证书信息。</span><span class="sxs-lookup"><span data-stu-id="33f8e-378">HTTPS certificate information.</span></span> |
| <span data-ttu-id="33f8e-379">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="33f8e-379">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="33f8e-380">提供的文件。</span><span class="sxs-lookup"><span data-stu-id="33f8e-380">Files served.</span></span> |
| <span data-ttu-id="33f8e-381">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="33f8e-381">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="33f8e-382">常规 Entity Framework Core 诊断。</span><span class="sxs-lookup"><span data-stu-id="33f8e-382">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="33f8e-383">数据库活动和配置、更改检测、迁移。</span><span class="sxs-lookup"><span data-stu-id="33f8e-383">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="33f8e-384">日志作用域</span><span class="sxs-lookup"><span data-stu-id="33f8e-384">Log scopes</span></span>

 <span data-ttu-id="33f8e-385">“作用域”可对一组逻辑操作分组。</span><span class="sxs-lookup"><span data-stu-id="33f8e-385">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="33f8e-386">此分组可用于将相同的数据附加到作为集合的一部分而创建的每个日志。</span><span class="sxs-lookup"><span data-stu-id="33f8e-386">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="33f8e-387">例如，在处理事务期间创建的每个日志都可包括事务 ID。</span><span class="sxs-lookup"><span data-stu-id="33f8e-387">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="33f8e-388">范围是由 <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> 方法返回的 `IDisposable` 类型，持续至释放为止。</span><span class="sxs-lookup"><span data-stu-id="33f8e-388">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="33f8e-389">要使用作用域，请在 `using` 块中包装记录器调用：</span><span class="sxs-lookup"><span data-stu-id="33f8e-389">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="33f8e-390">下列代码为控制台提供程序启用作用域：</span><span class="sxs-lookup"><span data-stu-id="33f8e-390">The following code enables scopes for the console provider:</span></span>

::: moniker range="> aspnetcore-2.0"

<span data-ttu-id="33f8e-391">Program.cs:</span><span class="sxs-lookup"><span data-stu-id="33f8e-391">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="33f8e-392">要启用基于作用域的日志记录，必须先配置 `IncludeScopes` 控制台记录器选项。</span><span class="sxs-lookup"><span data-stu-id="33f8e-392">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="33f8e-393">若要了解关配置，请参阅[配置](#configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="33f8e-393">For information on configuration, see the [Configuration](#configuration) section.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="33f8e-394">Program.cs:</span><span class="sxs-lookup"><span data-stu-id="33f8e-394">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="33f8e-395">要启用基于作用域的日志记录，必须先配置 `IncludeScopes` 控制台记录器选项。</span><span class="sxs-lookup"><span data-stu-id="33f8e-395">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="33f8e-396">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="33f8e-396">*Startup.cs*:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

<span data-ttu-id="33f8e-397">每条日志消息都包含作用域内的信息：</span><span class="sxs-lookup"><span data-stu-id="33f8e-397">Each log message includes the scoped information:</span></span>

```
info: TodoApi.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="33f8e-398">内置日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="33f8e-398">Built-in logging providers</span></span>

<span data-ttu-id="33f8e-399">ASP.NET Core 提供以下提供程序：</span><span class="sxs-lookup"><span data-stu-id="33f8e-399">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="33f8e-400">控制台</span><span class="sxs-lookup"><span data-stu-id="33f8e-400">Console</span></span>](#console-provider)
* [<span data-ttu-id="33f8e-401">调试</span><span class="sxs-lookup"><span data-stu-id="33f8e-401">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="33f8e-402">EventSource</span><span class="sxs-lookup"><span data-stu-id="33f8e-402">EventSource</span></span>](#eventsource-provider)
* [<span data-ttu-id="33f8e-403">EventLog</span><span class="sxs-lookup"><span data-stu-id="33f8e-403">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="33f8e-404">TraceSource</span><span class="sxs-lookup"><span data-stu-id="33f8e-404">TraceSource</span></span>](#tracesource-provider)

<span data-ttu-id="33f8e-405">本文稍后将介绍 [Azure 的日志记录](#logging-in-azure)选项。</span><span class="sxs-lookup"><span data-stu-id="33f8e-405">Options for [Logging in Azure](#logging-in-azure) are covered later in this article.</span></span>

<span data-ttu-id="33f8e-406">有关 stdout 日志记录的信息，请参阅 <xref:host-and-deploy/iis/troubleshoot#aspnet-core-module-stdout-log> 和 <xref:host-and-deploy/azure-apps/troubleshoot#aspnet-core-module-stdout-log>。</span><span class="sxs-lookup"><span data-stu-id="33f8e-406">For information about stdout logging, see <xref:host-and-deploy/iis/troubleshoot#aspnet-core-module-stdout-log> and <xref:host-and-deploy/azure-apps/troubleshoot#aspnet-core-module-stdout-log>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="33f8e-407">控制台提供程序</span><span class="sxs-lookup"><span data-stu-id="33f8e-407">Console provider</span></span>

<span data-ttu-id="33f8e-408">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 提供程序包向控制台发送日志输出。</span><span class="sxs-lookup"><span data-stu-id="33f8e-408">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddConsole();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddConsole();
```

<span data-ttu-id="33f8e-409">通过 [AddConsole 重载](xref:Microsoft.Extensions.Logging.ConsoleLoggerExtensions)，可传入一个最低日志级别、一个筛选器函数，以及一个用于指示作用域是否受支持的布尔值。</span><span class="sxs-lookup"><span data-stu-id="33f8e-409">[AddConsole overloads](xref:Microsoft.Extensions.Logging.ConsoleLoggerExtensions) let you pass in a minimum log level, a filter function, and a boolean that indicates whether scopes are supported.</span></span> <span data-ttu-id="33f8e-410">另一个选项是传递 `IConfiguration` 对象，可通过它来指定支持的作用域及日志记录级别。</span><span class="sxs-lookup"><span data-stu-id="33f8e-410">Another option is to pass in an `IConfiguration` object, which can specify scopes support and logging levels.</span></span>

<span data-ttu-id="33f8e-411">控制台提供程序对性能有重大影响，通常不适合在生产中使用。</span><span class="sxs-lookup"><span data-stu-id="33f8e-411">The console provider has a significant impact on performance and is generally not appropriate for use in production.</span></span>

<span data-ttu-id="33f8e-412">在 Visual Studio 中创建新项目时，`AddConsole` 方法如下所示：</span><span class="sxs-lookup"><span data-stu-id="33f8e-412">When you create a new project in Visual Studio, the `AddConsole` method looks like this:</span></span>

```csharp
loggerFactory.AddConsole(Configuration.GetSection("Logging"));
```

<span data-ttu-id="33f8e-413">此代码引用 appSettings.json 文件的 `Logging` 部分：</span><span class="sxs-lookup"><span data-stu-id="33f8e-413">This code refers to the `Logging` section of the *appSettings.json* file:</span></span>

[!code-json[](index/samples/1.x/TodoApiSample//appsettings.json)]

<span data-ttu-id="33f8e-414">所显示的设置将框架日志限制为警告，并允许在调试级别记录应用日志，如[日志筛选](#log-filtering)部分所述。</span><span class="sxs-lookup"><span data-stu-id="33f8e-414">The settings shown limit framework logs to warnings while allowing the app to log at debug level, as explained in the [Log filtering](#log-filtering) section.</span></span> <span data-ttu-id="33f8e-415">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="33f8e-415">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

::: moniker-end

<span data-ttu-id="33f8e-416">要查看控制台日志记录输出，请在项目文件夹中打开命令提示符并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="33f8e-416">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```console
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="33f8e-417">调试提供程序</span><span class="sxs-lookup"><span data-stu-id="33f8e-417">Debug provider</span></span>

<span data-ttu-id="33f8e-418">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 提供程序包使用 [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) 类（`Debug.WriteLine` 方法调用）来写入日志输出。</span><span class="sxs-lookup"><span data-stu-id="33f8e-418">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="33f8e-419">在 Linux 中，此提供程序将日志写入 /var/log/message。</span><span class="sxs-lookup"><span data-stu-id="33f8e-419">On Linux, this provider writes logs to */var/log/message*.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddDebug();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddDebug();
```

<span data-ttu-id="33f8e-420">[AddDebug 重载](xref:Microsoft.Extensions.Logging.DebugLoggerFactoryExtensions)允许传入最低日志级别或筛选器函数。</span><span class="sxs-lookup"><span data-stu-id="33f8e-420">[AddDebug overloads](xref:Microsoft.Extensions.Logging.DebugLoggerFactoryExtensions) let you pass in a minimum log level or a filter function.</span></span>

::: moniker-end

### <a name="eventsource-provider"></a><span data-ttu-id="33f8e-421">EventSource 提供程序</span><span class="sxs-lookup"><span data-stu-id="33f8e-421">EventSource provider</span></span>

<span data-ttu-id="33f8e-422">对于面向 ASP.NET Core 1.1.0 或更高版本的应用，[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) 提供程序包可实现事件跟踪。</span><span class="sxs-lookup"><span data-stu-id="33f8e-422">For apps that target ASP.NET Core 1.1.0 or later, the [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package can implement event tracing.</span></span> <span data-ttu-id="33f8e-423">在 Windows 中，它使用 [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)。</span><span class="sxs-lookup"><span data-stu-id="33f8e-423">On Windows, it uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span> <span data-ttu-id="33f8e-424">提供程序可跨平台使用，但尚无支持 Linux 或 macOS 的事件集合和显示工具。</span><span class="sxs-lookup"><span data-stu-id="33f8e-424">The provider is cross-platform, but there are no event collection and display tools yet for Linux or macOS.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventSourceLogger();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventSourceLogger();
```

::: moniker-end

<span data-ttu-id="33f8e-425">可使用 [PerfView 实用工具](https://github.com/Microsoft/perfview)收集和查看日志。</span><span class="sxs-lookup"><span data-stu-id="33f8e-425">A good way to collect and view logs is to use the [PerfView utility](https://github.com/Microsoft/perfview).</span></span> <span data-ttu-id="33f8e-426">虽然其他工具也可以查看 ETW 日志，但在处理由 ASP.NET 发出的 ETW 事件时，使用 PerfView 能获得最佳体验。</span><span class="sxs-lookup"><span data-stu-id="33f8e-426">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET.</span></span>

<span data-ttu-id="33f8e-427">要将 PerfView 配置为收集此提供程序记录的事件，请向 Additional Providers 列表添加字符串 `*Microsoft-Extensions-Logging`。</span><span class="sxs-lookup"><span data-stu-id="33f8e-427">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="33f8e-428">（请勿遗漏字符串起始处的星号。）</span><span class="sxs-lookup"><span data-stu-id="33f8e-428">(Don't miss the asterisk at the start of the string.)</span></span>

![其他 Perfview 提供程序](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="33f8e-430">Windows EventLog 提供程序</span><span class="sxs-lookup"><span data-stu-id="33f8e-430">Windows EventLog provider</span></span>

<span data-ttu-id="33f8e-431">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 提供程序包向 Windows 事件日志发送日志输出。</span><span class="sxs-lookup"><span data-stu-id="33f8e-431">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventLog();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventLog();
```

<span data-ttu-id="33f8e-432">[AddEventLog 重载](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)允许传入 `EventLogSettings` 或最低日志级别。</span><span class="sxs-lookup"><span data-stu-id="33f8e-432">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in `EventLogSettings` or a minimum log level.</span></span>

::: moniker-end

### <a name="tracesource-provider"></a><span data-ttu-id="33f8e-433">TraceSource 提供程序</span><span class="sxs-lookup"><span data-stu-id="33f8e-433">TraceSource provider</span></span>

<span data-ttu-id="33f8e-434">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) 提供程序包使用 <xref:System.Diagnostics.TraceSource> 库和提供程序。</span><span class="sxs-lookup"><span data-stu-id="33f8e-434">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddTraceSource(sourceSwitchName);
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddTraceSource(sourceSwitchName);
```

::: moniker-end

<span data-ttu-id="33f8e-435">[AddTraceSource 重载](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) 允许传入资源开关和跟踪侦听器。</span><span class="sxs-lookup"><span data-stu-id="33f8e-435">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="33f8e-436">要使用此提供程序，应用必须在 .NET Framework（而非 .NET Core）上运行。</span><span class="sxs-lookup"><span data-stu-id="33f8e-436">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="33f8e-437">提供程序可将消息路由到多个[侦听器](/dotnet/framework/debug-trace-profile/trace-listeners)，例如示例应用中使用的 <xref:System.Diagnostics.TextWriterTraceListener>。</span><span class="sxs-lookup"><span data-stu-id="33f8e-437">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="33f8e-438">以下示例配置了 `TraceSource` 提供程序，用于向控制台窗口记录 `Warning` 和更高级别的消息。</span><span class="sxs-lookup"><span data-stu-id="33f8e-438">The following example configures a `TraceSource` provider that logs `Warning` and higher messages to the console window.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_TraceSource&highlight=9-12)]

::: moniker-end

## <a name="logging-in-azure"></a><span data-ttu-id="33f8e-439">Azure 中的日志记录</span><span class="sxs-lookup"><span data-stu-id="33f8e-439">Logging in Azure</span></span>

<span data-ttu-id="33f8e-440">要了解 Azure 中的日志记录，请参阅下列部分：</span><span class="sxs-lookup"><span data-stu-id="33f8e-440">For information about logging in Azure, see the following sections:</span></span>

* [<span data-ttu-id="33f8e-441">Azure 应用服务提供程序</span><span class="sxs-lookup"><span data-stu-id="33f8e-441">Azure App Service provider</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="33f8e-442">Azure 日志流式处理</span><span class="sxs-lookup"><span data-stu-id="33f8e-442">Azure log streaming</span></span>](#azure-log-streaming)

::: moniker range=">= aspnetcore-1.1"

* [<span data-ttu-id="33f8e-443">Azure Application Insights 跟踪日志记录</span><span class="sxs-lookup"><span data-stu-id="33f8e-443">Azure Application Insights trace logging</span></span>](#azure-application-insights-trace-logging)

::: moniker-end

### <a name="azure-app-service-provider"></a><span data-ttu-id="33f8e-444">Azure 应用服务提供程序</span><span class="sxs-lookup"><span data-stu-id="33f8e-444">Azure App Service provider</span></span>

<span data-ttu-id="33f8e-445">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 提供程序包将日志写入 Azure App Service 应用的文件系统，以及 Azure 存储帐户中的 [blob 存储](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)。</span><span class="sxs-lookup"><span data-stu-id="33f8e-445">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span> <span data-ttu-id="33f8e-446">面向 .NET Core 1.1 或更高版本的应用可使用该提供程序包。</span><span class="sxs-lookup"><span data-stu-id="33f8e-446">The provider package is available for apps targeting .NET Core 1.1 or later.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="33f8e-447">如果面向 .NET Core，请注意以下几点：</span><span class="sxs-lookup"><span data-stu-id="33f8e-447">If targeting .NET Core, note the following points:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="33f8e-448">提供程序包随附在 ASP.NET Core [Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)中。</span><span class="sxs-lookup"><span data-stu-id="33f8e-448">The provider package is included in the ASP.NET Core [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="33f8e-449">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中不包括此提供程序包。</span><span class="sxs-lookup"><span data-stu-id="33f8e-449">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="33f8e-450">要使用提供程序，请安装该程序包。</span><span class="sxs-lookup"><span data-stu-id="33f8e-450">To use the provider, install the package.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="33f8e-451">不要显式调用 <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*>。</span><span class="sxs-lookup"><span data-stu-id="33f8e-451">Don't explicitly call <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*>.</span></span> <span data-ttu-id="33f8e-452">将应用部署到 Azure 应用服务时，会自动使提供程序对应用可用。</span><span class="sxs-lookup"><span data-stu-id="33f8e-452">The provider is automatically made available to the app when the app is deployed to Azure App Service.</span></span>

<span data-ttu-id="33f8e-453">如果面向 .NET Framework 或引用 `Microsoft.AspNetCore.App` 元包，请向项目添加提供程序包。</span><span class="sxs-lookup"><span data-stu-id="33f8e-453">If targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> <span data-ttu-id="33f8e-454">在 <xref:Microsoft.Extensions.Logging.ILoggerFactory> 实例上调用 `AddAzureWebAppDiagnostics`：</span><span class="sxs-lookup"><span data-stu-id="33f8e-454">Invoke `AddAzureWebAppDiagnostics` on an <xref:Microsoft.Extensions.Logging.ILoggerFactory> instance:</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range="= aspnetcore-1.1"

```csharp
loggerFactory.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="33f8e-455"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> 重载允许传入 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>。</span><span class="sxs-lookup"><span data-stu-id="33f8e-455">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="33f8e-456">设置对象可以覆盖默认设置，例如日志记录输出模板、blob 名称和文件大小限制。</span><span class="sxs-lookup"><span data-stu-id="33f8e-456">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="33f8e-457">（“输出模板”是一个消息模板，除了通过 `ILogger` 方法调用提供的内容之外，还可将其应用于所有日志。）</span><span class="sxs-lookup"><span data-stu-id="33f8e-457">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="33f8e-458">在部署应用服务应用时，应用程序将遵循 Azure 门户中“应用服务”页面下的[诊断日志](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag)部分的设置。</span><span class="sxs-lookup"><span data-stu-id="33f8e-458">When you deploy to an App Service app, the application honors the settings in the [Diagnostic Logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="33f8e-459">更新这些设置后，更改会立即生效，无需重新启动或重新部署应用。</span><span class="sxs-lookup"><span data-stu-id="33f8e-459">When these settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

![Azure 日志记录设置](index/_static/azure-logging-settings.png)

<span data-ttu-id="33f8e-461">日志文件的默认位置是 D:\\home\\LogFiles\\Application 文件夹，默认文件名为 diagnostics-yyyymmdd.txt。</span><span class="sxs-lookup"><span data-stu-id="33f8e-461">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="33f8e-462">默认文件大小上限为 10 MB，默认最大保留文件数为 2。</span><span class="sxs-lookup"><span data-stu-id="33f8e-462">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="33f8e-463">默认 blob 名为 {app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt。</span><span class="sxs-lookup"><span data-stu-id="33f8e-463">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span> <span data-ttu-id="33f8e-464">要详细了解默认行为，请参阅 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>。</span><span class="sxs-lookup"><span data-stu-id="33f8e-464">For more information about default behavior, see <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span>

<span data-ttu-id="33f8e-465">该提供程序仅当项目在 Azure 环境中运行时有效。</span><span class="sxs-lookup"><span data-stu-id="33f8e-465">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="33f8e-466">项目在本地运行时，该提供程序无效 &mdash; 它不会写入本地文件或 blob 的本地开发存储。</span><span class="sxs-lookup"><span data-stu-id="33f8e-466">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

::: moniker-end

### <a name="azure-log-streaming"></a><span data-ttu-id="33f8e-467">Azure 日志流式处理</span><span class="sxs-lookup"><span data-stu-id="33f8e-467">Azure log streaming</span></span>

<span data-ttu-id="33f8e-468">通过 Azure 日志流式处理，可从以下位置实时查看日志活动：</span><span class="sxs-lookup"><span data-stu-id="33f8e-468">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="33f8e-469">应用服务器</span><span class="sxs-lookup"><span data-stu-id="33f8e-469">The app server</span></span>
* <span data-ttu-id="33f8e-470">Web 服务器</span><span class="sxs-lookup"><span data-stu-id="33f8e-470">The web server</span></span>
* <span data-ttu-id="33f8e-471">请求跟踪失败</span><span class="sxs-lookup"><span data-stu-id="33f8e-471">Failed request tracing</span></span>

<span data-ttu-id="33f8e-472">要配置 Azure 日志流式处理，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="33f8e-472">To configure Azure log streaming:</span></span>

* <span data-ttu-id="33f8e-473">从应用的门户页导航到“诊断日志”页。</span><span class="sxs-lookup"><span data-stu-id="33f8e-473">Navigate to the **Diagnostics Logs** page from your app's portal page.</span></span>
* <span data-ttu-id="33f8e-474">将“应用程序日志记录(Filesystem)”设置为“开”。</span><span class="sxs-lookup"><span data-stu-id="33f8e-474">Set **Application Logging (Filesystem)** to **On**.</span></span>

![Azure 门户诊断日志页](index/_static/azure-diagnostic-logs.png)

<span data-ttu-id="33f8e-476">导航到“日志流式处理”页，查看应用消息。</span><span class="sxs-lookup"><span data-stu-id="33f8e-476">Navigate to the **Log Streaming** page to view app messages.</span></span> <span data-ttu-id="33f8e-477">它们由应用通过 `ILogger` 接口记录。</span><span class="sxs-lookup"><span data-stu-id="33f8e-477">They're logged by the app through the `ILogger` interface.</span></span>

![Azure 门户应用程序日志流式处理](index/_static/azure-log-streaming.png)

::: moniker range=">= aspnetcore-1.1"

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="33f8e-479">Azure Application Insights 跟踪日志记录</span><span class="sxs-lookup"><span data-stu-id="33f8e-479">Azure Application Insights trace logging</span></span>

<span data-ttu-id="33f8e-480">Application Insights SDK 可收集和报告 ASP.NET Core 日志记录基础结构生成的日志。</span><span class="sxs-lookup"><span data-stu-id="33f8e-480">The Application Insights SDK can collect and report logs generated by the ASP.NET Core logging infrastructure.</span></span> <span data-ttu-id="33f8e-481">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="33f8e-481">For more information, see the following resources:</span></span>

* [<span data-ttu-id="33f8e-482">Application Insights 概述</span><span class="sxs-lookup"><span data-stu-id="33f8e-482">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* [<span data-ttu-id="33f8e-483">用于 ASP.NET Core 的 Application Insights</span><span class="sxs-lookup"><span data-stu-id="33f8e-483">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* <span data-ttu-id="33f8e-484">[Application Insights 日志记录适配器](https://github.com/Microsoft/ApplicationInsights-dotnet-logging/blob/develop/README.md)。</span><span class="sxs-lookup"><span data-stu-id="33f8e-484">[Application Insights logging adapters](https://github.com/Microsoft/ApplicationInsights-dotnet-logging/blob/develop/README.md).</span></span>

::: moniker-end

## <a name="third-party-logging-providers"></a><span data-ttu-id="33f8e-485">第三方日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="33f8e-485">Third-party logging providers</span></span>

<span data-ttu-id="33f8e-486">适用于 ASP.NET Core 的第三方日志记录框架：</span><span class="sxs-lookup"><span data-stu-id="33f8e-486">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="33f8e-487">[elmah.io](https://elmah.io/)（[GitHub 存储库](https://github.com/elmahio/Elmah.Io.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="33f8e-487">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="33f8e-488">[Gelf](http://docs.graylog.org/en/2.3/pages/gelf.html)（[GitHub 存储库](https://github.com/mattwcole/gelf-extensions-logging)）</span><span class="sxs-lookup"><span data-stu-id="33f8e-488">[Gelf](http://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="33f8e-489">[JSNLog](http://jsnlog.com/)（[GitHub 存储库](https://github.com/mperdeck/jsnlog)）</span><span class="sxs-lookup"><span data-stu-id="33f8e-489">[JSNLog](http://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="33f8e-490">[KissLog.net](https://kisslog.net/)（[GitHub 存储库](https://github.com/catalingavan/KissLog-net)）</span><span class="sxs-lookup"><span data-stu-id="33f8e-490">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="33f8e-491">[Loggr](http://loggr.net/)（[GitHub 存储库](https://github.com/imobile3/Loggr.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="33f8e-491">[Loggr](http://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="33f8e-492">[NLog](http://nlog-project.org/)（[GitHub 存储库](https://github.com/NLog/NLog.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="33f8e-492">[NLog](http://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="33f8e-493">[Sentry](https://sentry.io/welcome/)（[GitHub 存储库](https://github.com/getsentry/sentry-dotnet)）</span><span class="sxs-lookup"><span data-stu-id="33f8e-493">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="33f8e-494">[Serilog](https://serilog.net/)（[GitHub 存储库](https://github.com/serilog/serilog-extensions-logging)）</span><span class="sxs-lookup"><span data-stu-id="33f8e-494">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-extensions-logging))</span></span>
* <span data-ttu-id="33f8e-495">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging)（[Github 存储库](https://github.com/googleapis/google-cloud-dotnet)）</span><span class="sxs-lookup"><span data-stu-id="33f8e-495">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="33f8e-496">某些第三方框架可以执行[语义日志记录（又称结构化日志记录）](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="33f8e-496">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="33f8e-497">使用第三方框架类似于使用以下内置提供程序之一：</span><span class="sxs-lookup"><span data-stu-id="33f8e-497">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="33f8e-498">将 NuGet 包添加到你的项目。</span><span class="sxs-lookup"><span data-stu-id="33f8e-498">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="33f8e-499">调用 `ILoggerFactory`。</span><span class="sxs-lookup"><span data-stu-id="33f8e-499">Call an `ILoggerFactory`.</span></span>

<span data-ttu-id="33f8e-500">有关详细信息，请参阅各提供程序的相关文档。</span><span class="sxs-lookup"><span data-stu-id="33f8e-500">For more information, see each provider's documentation.</span></span> <span data-ttu-id="33f8e-501">Microsoft 不支持第三方日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="33f8e-501">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="33f8e-502">其他资源</span><span class="sxs-lookup"><span data-stu-id="33f8e-502">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
