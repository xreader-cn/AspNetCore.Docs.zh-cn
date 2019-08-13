---
title: ASP.NET Core 中的日志记录
author: tdykstra
description: 了解 ASP.NET Core 中的记录框架。 发现内置日志记录提供程序，并详细了解常见第三方提供程序。
ms.author: tdykstra
ms.custom: mvc
ms.date: 07/11/2019
uid: fundamentals/logging/index
ms.openlocfilehash: 4fe677e69478284db2ccab655c35b5744b6f63f9
ms.sourcegitcommit: 059ab380744fa3be3b69aa90d431b563c57092cf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68410913"
---
# <a name="logging-in-aspnet-core"></a><span data-ttu-id="53dc3-104">ASP.NET Core 中的日志记录</span><span class="sxs-lookup"><span data-stu-id="53dc3-104">Logging in ASP.NET Core</span></span>

<span data-ttu-id="53dc3-105">作者：[Steve Smith](https://ardalis.com/) 和 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="53dc3-105">By [Steve Smith](https://ardalis.com/) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="53dc3-106">ASP.NET Core 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。</span><span class="sxs-lookup"><span data-stu-id="53dc3-106">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="53dc3-107">本文介绍了如何将日志记录 API 与内置提供程序一起使用。</span><span class="sxs-lookup"><span data-stu-id="53dc3-107">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="53dc3-108">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="53dc3-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="53dc3-109">添加提供程序</span><span class="sxs-lookup"><span data-stu-id="53dc3-109">Add providers</span></span>

<span data-ttu-id="53dc3-110">日志记录提供程序会显示或存储日志。</span><span class="sxs-lookup"><span data-stu-id="53dc3-110">A logging provider displays or stores logs.</span></span> <span data-ttu-id="53dc3-111">例如，控制台提供程序在控制台上显示日志，Azure Application Insights 提供程序会将这些日志存储在 Azure Application Insights 中。</span><span class="sxs-lookup"><span data-stu-id="53dc3-111">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="53dc3-112">可通过添加多个提供程序将日志发送到多个目标。</span><span class="sxs-lookup"><span data-stu-id="53dc3-112">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="53dc3-113">要添加提供程序，请在 Program.cs 中调用提供程序的 `Add{provider name}` 扩展方法  ：</span><span class="sxs-lookup"><span data-stu-id="53dc3-113">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="53dc3-114">前面的代码需要引用 `Microsoft.Extensions.Logging` 和 `Microsoft.Extensions.Configuration`。</span><span class="sxs-lookup"><span data-stu-id="53dc3-114">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="53dc3-115">默认项目模板调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>，该操作将添加以下日志记录提供程序：</span><span class="sxs-lookup"><span data-stu-id="53dc3-115">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="53dc3-116">控制台</span><span class="sxs-lookup"><span data-stu-id="53dc3-116">Console</span></span>
* <span data-ttu-id="53dc3-117">调试</span><span class="sxs-lookup"><span data-stu-id="53dc3-117">Debug</span></span>
* <span data-ttu-id="53dc3-118">EventSource（从 ASP.NET Core 2.2 开始）</span><span class="sxs-lookup"><span data-stu-id="53dc3-118">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="53dc3-119">如果使用 `CreateDefaultBuilder`，则可自行选择提供程序来替换默认提供程序。</span><span class="sxs-lookup"><span data-stu-id="53dc3-119">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="53dc3-120">调用 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>，然后添加所需的提供程序。</span><span class="sxs-lookup"><span data-stu-id="53dc3-120">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="53dc3-121">要使用提供程序，请安装其 NuGet 包，并在 <xref:Microsoft.Extensions.Logging.ILoggerFactory> 的实例上调用提供程序的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="53dc3-121">To use a provider, install its NuGet package and call the provider's extension method on an instance of <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_AddConsoleAndDebug&highlight=3,5-7)]

<span data-ttu-id="53dc3-122">ASP.NET Core [依赖关系注入](xref:fundamentals/dependency-injection) (DI) 将提供 `ILoggerFactory` 实例。</span><span class="sxs-lookup"><span data-stu-id="53dc3-122">ASP.NET Core [dependency injection (DI)](xref:fundamentals/dependency-injection) provides the `ILoggerFactory` instance.</span></span> <span data-ttu-id="53dc3-123">在 [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) 和 [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) 包中定义了 `AddConsole` 和 `AddDebug` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="53dc3-123">The `AddConsole` and `AddDebug` extension methods are defined in the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) and [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) packages.</span></span> <span data-ttu-id="53dc3-124">每个扩展方法都调用 `ILoggerFactory.AddProvider` 方法，传入提供程序的一个实例。</span><span class="sxs-lookup"><span data-stu-id="53dc3-124">Each extension method calls the `ILoggerFactory.AddProvider` method, passing in an instance of the provider.</span></span>

> [!NOTE]
> <span data-ttu-id="53dc3-125">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/1.x)在 `Startup.Configure` 方法中添加了日志提供程序。</span><span class="sxs-lookup"><span data-stu-id="53dc3-125">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/1.x) adds logging providers in the `Startup.Configure` method.</span></span> <span data-ttu-id="53dc3-126">要从先前执行的代码获取日志输出，请在 `Startup` 类构造函数中添加日志提供程序。</span><span class="sxs-lookup"><span data-stu-id="53dc3-126">To obtain log output from code that executes earlier, add logging providers in the `Startup` class constructor.</span></span>

::: moniker-end

<span data-ttu-id="53dc3-127">详细了解[内置日志记录提供程序](#built-in-logging-providers)，以及本文稍后部分介绍的[第三方日志记录提供程序](#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="53dc3-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="53dc3-128">创建日志</span><span class="sxs-lookup"><span data-stu-id="53dc3-128">Create logs</span></span>

<span data-ttu-id="53dc3-129">从 DI 中获取 <xref:Microsoft.Extensions.Logging.ILogger%601> 对象。</span><span class="sxs-lookup"><span data-stu-id="53dc3-129">Get an <xref:Microsoft.Extensions.Logging.ILogger%601> object from DI.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="53dc3-130">以下控制器示例会创建 `Information` 和 `Warning` 日志。</span><span class="sxs-lookup"><span data-stu-id="53dc3-130">The following controller example creates `Information` and `Warning` logs.</span></span> <span data-ttu-id="53dc3-131">类别为 `TodoApiSample.Controllers.TodoController`（示例应用中 `TodoController` 的完全限定类名）  ：</span><span class="sxs-lookup"><span data-stu-id="53dc3-131">The *category* is `TodoApiSample.Controllers.TodoController` (the fully qualified class name of `TodoController` in the sample app):</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=4,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="53dc3-132">以下 Razor 页面示例会创建“级别”为 `Information` 且“类别”为 `TodoApiSample.Pages.AboutModel` 的日志   ：</span><span class="sxs-lookup"><span data-stu-id="53dc3-132">The following Razor Pages example creates logs with `Information` as the *level* and `TodoApiSample.Pages.AboutModel` as the *category*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3, 7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="53dc3-133">上述示例创建了“级别”为 `Information` 和 `Warning`，且“类别”为 `TodoController` 的日志   。</span><span class="sxs-lookup"><span data-stu-id="53dc3-133">The preceding example creates logs with `Information` and `Warning` as the *level* and `TodoController` class as the *category*.</span></span> 

::: moniker-end

<span data-ttu-id="53dc3-134">日志“级别”代表所记录事件的严重程度  。</span><span class="sxs-lookup"><span data-stu-id="53dc3-134">The Log *level* indicates the severity of the logged event.</span></span> <span data-ttu-id="53dc3-135">日志“类别”是与每个日志关联的字符串  。</span><span class="sxs-lookup"><span data-stu-id="53dc3-135">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="53dc3-136">`ILogger<T>` 实例会创建“类别”为类型 `T` 的完全限定名称的日志。</span><span class="sxs-lookup"><span data-stu-id="53dc3-136">The `ILogger<T>` instance creates logs that have the fully qualified name of type `T` as the category.</span></span> <span data-ttu-id="53dc3-137">本文稍后部分将更详细地介绍[级别](#log-level)和[类别](#log-category)。</span><span class="sxs-lookup"><span data-stu-id="53dc3-137">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span> 

::: moniker range=">= aspnetcore-2.0"

### <a name="create-logs-in-startup"></a><span data-ttu-id="53dc3-138">启动时创建日志</span><span class="sxs-lookup"><span data-stu-id="53dc3-138">Create logs in Startup</span></span>

<span data-ttu-id="53dc3-139">要将日志写入 `Startup` 类，构造函数签名需包含 `ILogger` 参数：</span><span class="sxs-lookup"><span data-stu-id="53dc3-139">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-program"></a><span data-ttu-id="53dc3-140">在程序中创建日志</span><span class="sxs-lookup"><span data-stu-id="53dc3-140">Create logs in Program</span></span>

<span data-ttu-id="53dc3-141">要将日志写入 `Program` 类，请从 DI 获取 `ILogger` 实例：</span><span class="sxs-lookup"><span data-stu-id="53dc3-141">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="53dc3-142">没有异步记录器方法</span><span class="sxs-lookup"><span data-stu-id="53dc3-142">No asynchronous logger methods</span></span>

<span data-ttu-id="53dc3-143">日志记录应该会很快，不值得牺牲性能来使用异步代码。</span><span class="sxs-lookup"><span data-stu-id="53dc3-143">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="53dc3-144">如果你的日志数据存储很慢，请不要直接写入它。</span><span class="sxs-lookup"><span data-stu-id="53dc3-144">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="53dc3-145">首先考虑将日志消息写入快速存储，稍后再将其变为慢速存储。</span><span class="sxs-lookup"><span data-stu-id="53dc3-145">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="53dc3-146">例如，如果你要记录到 SQL Server，你可能不想直接在 `Log` 方法中记录，因为 `Log` 方法是同步的。</span><span class="sxs-lookup"><span data-stu-id="53dc3-146">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="53dc3-147">相反，你会将日志消息同步添加到内存中的队列，并让后台辅助线程从队列中拉出消息，以完成将数据推送到 SQL Server 的异步工作。</span><span class="sxs-lookup"><span data-stu-id="53dc3-147">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span>

## <a name="configuration"></a><span data-ttu-id="53dc3-148">Configuration</span><span class="sxs-lookup"><span data-stu-id="53dc3-148">Configuration</span></span>

<span data-ttu-id="53dc3-149">日志记录提供程序配置由一个或多个配置提供程序提供：</span><span class="sxs-lookup"><span data-stu-id="53dc3-149">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="53dc3-150">文件格式（INI、JSON 和 XML）。</span><span class="sxs-lookup"><span data-stu-id="53dc3-150">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="53dc3-151">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="53dc3-151">Command-line arguments.</span></span>
* <span data-ttu-id="53dc3-152">环境变量。</span><span class="sxs-lookup"><span data-stu-id="53dc3-152">Environment variables.</span></span>
* <span data-ttu-id="53dc3-153">内存中的 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="53dc3-153">In-memory .NET objects.</span></span>
* <span data-ttu-id="53dc3-154">未加密的[机密管理器](xref:security/app-secrets)存储。</span><span class="sxs-lookup"><span data-stu-id="53dc3-154">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="53dc3-155">加密的用户存储，如 [Azure Key Vault](xref:security/key-vault-configuration)。</span><span class="sxs-lookup"><span data-stu-id="53dc3-155">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="53dc3-156">（已安装或已创建的）自定义提供程序。</span><span class="sxs-lookup"><span data-stu-id="53dc3-156">Custom providers (installed or created).</span></span>

<span data-ttu-id="53dc3-157">例如，日志记录配置通常由应用设置文件的 `Logging` 部分提供。</span><span class="sxs-lookup"><span data-stu-id="53dc3-157">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="53dc3-158">以下示例显示了典型 *appsettings.Development.json* 文件的内容：</span><span class="sxs-lookup"><span data-stu-id="53dc3-158">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="53dc3-159">`Logging` 属性可具有 `LogLevel` 和日志提供程序属性（显示控制台）。</span><span class="sxs-lookup"><span data-stu-id="53dc3-159">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="53dc3-160">`Logging` 下的 `LogLevel` 属性指定了用于记录所选类别的最低[级别](#log-level)。</span><span class="sxs-lookup"><span data-stu-id="53dc3-160">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="53dc3-161">在本例中，`System` 和 `Microsoft` 类别在 `Information` 级别记录，其他均在 `Debug` 级别记录。</span><span class="sxs-lookup"><span data-stu-id="53dc3-161">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="53dc3-162">`Logging` 下的其他属性均指定了日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="53dc3-162">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="53dc3-163">本示例针对控制台提供程序。</span><span class="sxs-lookup"><span data-stu-id="53dc3-163">The example is for the Console provider.</span></span> <span data-ttu-id="53dc3-164">如果提供程序支持[日志作用域](#log-scopes)，则 `IncludeScopes` 将指示是否启用这些域。</span><span class="sxs-lookup"><span data-stu-id="53dc3-164">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="53dc3-165">提供程序属性（例如本例的 `Console`）也可指定 `LogLevel` 属性。</span><span class="sxs-lookup"><span data-stu-id="53dc3-165">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="53dc3-166">提供程序下的 `LogLevel` 指定了该提供程序记录的级别。</span><span class="sxs-lookup"><span data-stu-id="53dc3-166">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="53dc3-167">如果在 `Logging.{providername}.LogLevel` 中指定了级别，则这些级别将重写 `Logging.LogLevel` 中设置的所有内容。</span><span class="sxs-lookup"><span data-stu-id="53dc3-167">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

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

<span data-ttu-id="53dc3-168">`LogLevel` 项表示日志名称。</span><span class="sxs-lookup"><span data-stu-id="53dc3-168">`LogLevel` keys represent log names.</span></span> <span data-ttu-id="53dc3-169">`Default` 项适用于未显式列出的日志。</span><span class="sxs-lookup"><span data-stu-id="53dc3-169">The `Default` key applies to logs not explicitly listed.</span></span> <span data-ttu-id="53dc3-170">其值表示应用于给定日志的[日志级别](#log-level)。</span><span class="sxs-lookup"><span data-stu-id="53dc3-170">The value represents the [log level](#log-level) applied to the given log.</span></span>

::: moniker-end

<span data-ttu-id="53dc3-171">若要了解如何实现配置提供程序，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="53dc3-171">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="53dc3-172">日志记录输出示例</span><span class="sxs-lookup"><span data-stu-id="53dc3-172">Sample logging output</span></span>

<span data-ttu-id="53dc3-173">使用上一部分中显示的示例代码从命令行运行应用时，将在控制台中看到日志。</span><span class="sxs-lookup"><span data-stu-id="53dc3-173">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="53dc3-174">以下是控制台输出示例：</span><span class="sxs-lookup"><span data-stu-id="53dc3-174">Here's an example of console output:</span></span>

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

<span data-ttu-id="53dc3-175">通过向 `http://localhost:5000/api/todo/0` 处的示例应用发出 HTTP Get 请求来生成前述日志。</span><span class="sxs-lookup"><span data-stu-id="53dc3-175">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="53dc3-176">在 Visual Studio 中运行示例应用时，“调试”窗口中将显示如下日志：</span><span class="sxs-lookup"><span data-stu-id="53dc3-176">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="53dc3-177">由上一部分所示的 `ILogger` 调用创建的日志是以“TodoApi.Controllers.TodoController”开头的。</span><span class="sxs-lookup"><span data-stu-id="53dc3-177">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi.Controllers.TodoController".</span></span> <span data-ttu-id="53dc3-178">以“Microsoft”类别开头的日志来自 ASP.NET Core 框架代码。</span><span class="sxs-lookup"><span data-stu-id="53dc3-178">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="53dc3-179">ASP.NET Core 和应用程序代码使用相同的日志记录 API 和提供程序。</span><span class="sxs-lookup"><span data-stu-id="53dc3-179">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="53dc3-180">本文余下部分将介绍有关日志记录的某些详细信息及选项。</span><span class="sxs-lookup"><span data-stu-id="53dc3-180">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="53dc3-181">NuGet 包</span><span class="sxs-lookup"><span data-stu-id="53dc3-181">NuGet packages</span></span>

<span data-ttu-id="53dc3-182">`ILogger` 和 `ILoggerFactory` 接口位于 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 中，其默认实现位于 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 中。</span><span class="sxs-lookup"><span data-stu-id="53dc3-182">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="53dc3-183">日志类别</span><span class="sxs-lookup"><span data-stu-id="53dc3-183">Log category</span></span>

<span data-ttu-id="53dc3-184">创建 `ILogger` 对象后，将为其指定“类别”  。</span><span class="sxs-lookup"><span data-stu-id="53dc3-184">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="53dc3-185">该类别包含在由此 `ILogger` 实例创建的每条日志消息中。</span><span class="sxs-lookup"><span data-stu-id="53dc3-185">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="53dc3-186">类别可以是任何字符串，但约定需使用类名，例如“TodoApi.Controllers.TodoController”。</span><span class="sxs-lookup"><span data-stu-id="53dc3-186">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="53dc3-187">使用 `ILogger<T>` 获取一个 `ILogger` 实例，该实例使用 `T` 的完全限定类型名称作为类别：</span><span class="sxs-lookup"><span data-stu-id="53dc3-187">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

<span data-ttu-id="53dc3-188">要显式指定类别，请调用 `ILoggerFactory.CreateLogger`：</span><span class="sxs-lookup"><span data-stu-id="53dc3-188">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

<span data-ttu-id="53dc3-189">`ILogger<T>` 相当于使用 `T` 的完全限定类型名称来调用 `CreateLogger`。</span><span class="sxs-lookup"><span data-stu-id="53dc3-189">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="53dc3-190">日志级别</span><span class="sxs-lookup"><span data-stu-id="53dc3-190">Log level</span></span>

<span data-ttu-id="53dc3-191">每个日志都指定了一个 <xref:Microsoft.Extensions.Logging.LogLevel> 值。</span><span class="sxs-lookup"><span data-stu-id="53dc3-191">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="53dc3-192">日志级别指示严重性或重要程度。</span><span class="sxs-lookup"><span data-stu-id="53dc3-192">The log level indicates the severity or importance.</span></span> <span data-ttu-id="53dc3-193">例如，可在方法正常结束时写入 `Information` 日志，在方法返回“404 找不到”状态代码时写入 `Warning` 日志  。</span><span class="sxs-lookup"><span data-stu-id="53dc3-193">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="53dc3-194">下面的代码会创建 `Information` 和 `Warning` 日志：</span><span class="sxs-lookup"><span data-stu-id="53dc3-194">The following code creates `Information` and `Warning` logs:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="53dc3-195">在上述代码中，第一个参数是[日志事件 ID](#log-event-id)。</span><span class="sxs-lookup"><span data-stu-id="53dc3-195">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="53dc3-196">第二个参数是消息模板，其中的占位符用于填写剩余方法形参提供的实参值。</span><span class="sxs-lookup"><span data-stu-id="53dc3-196">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="53dc3-197">稍后将在本文的[消息模板部分](#log-message-template)介绍方法参数。</span><span class="sxs-lookup"><span data-stu-id="53dc3-197">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="53dc3-198">在方法名称中包含级别的日志方法（例如 `LogInformation` 和 `LogWarning`）是 [ILogger 的扩展方法](xref:Microsoft.Extensions.Logging.LoggerExtensions)。</span><span class="sxs-lookup"><span data-stu-id="53dc3-198">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="53dc3-199">这些方法会调用可接受 `LogLevel` 参数的 `Log` 方法。</span><span class="sxs-lookup"><span data-stu-id="53dc3-199">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="53dc3-200">可直接调用 `Log` 方法而不调用其中某个扩展方法，但语法相对复杂。</span><span class="sxs-lookup"><span data-stu-id="53dc3-200">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="53dc3-201">有关详细信息，请参阅 <xref:Microsoft.Extensions.Logging.ILogger> 和[记录器扩展源代码](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)。</span><span class="sxs-lookup"><span data-stu-id="53dc3-201">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="53dc3-202">ASP.NET Core 定义了以下日志级别（按严重性从低到高排列）。</span><span class="sxs-lookup"><span data-stu-id="53dc3-202">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="53dc3-203">跟踪 = 0</span><span class="sxs-lookup"><span data-stu-id="53dc3-203">Trace = 0</span></span>

  <span data-ttu-id="53dc3-204">有关通常仅用于调试的信息。</span><span class="sxs-lookup"><span data-stu-id="53dc3-204">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="53dc3-205">这些消息可能包含敏感应用程序数据，因此不得在生产环境中启用它们。</span><span class="sxs-lookup"><span data-stu-id="53dc3-205">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="53dc3-206">默认情况下禁用。 </span><span class="sxs-lookup"><span data-stu-id="53dc3-206">*Disabled by default.*</span></span>

* <span data-ttu-id="53dc3-207">调试 = 1</span><span class="sxs-lookup"><span data-stu-id="53dc3-207">Debug = 1</span></span>

  <span data-ttu-id="53dc3-208">有关在开发和调试中可能有用的信息。</span><span class="sxs-lookup"><span data-stu-id="53dc3-208">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="53dc3-209">示例:`Entering method Configure with flag set to true.` 由于日志数量过多，因此仅当执行故障排除时，才在生产中启用 `Debug` 级别日志。</span><span class="sxs-lookup"><span data-stu-id="53dc3-209">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="53dc3-210">信息 = 2</span><span class="sxs-lookup"><span data-stu-id="53dc3-210">Information = 2</span></span>

  <span data-ttu-id="53dc3-211">用于跟踪应用的常规流。</span><span class="sxs-lookup"><span data-stu-id="53dc3-211">For tracking the general flow of the app.</span></span> <span data-ttu-id="53dc3-212">这些日志通常有长期价值。</span><span class="sxs-lookup"><span data-stu-id="53dc3-212">These logs typically have some long-term value.</span></span> <span data-ttu-id="53dc3-213">示例：`Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="53dc3-213">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="53dc3-214">警告 = 3</span><span class="sxs-lookup"><span data-stu-id="53dc3-214">Warning = 3</span></span>

  <span data-ttu-id="53dc3-215">表示应用流中的异常或意外事件。</span><span class="sxs-lookup"><span data-stu-id="53dc3-215">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="53dc3-216">可能包括不会中断应用运行但仍需调查的错误或其他条件。</span><span class="sxs-lookup"><span data-stu-id="53dc3-216">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="53dc3-217">`Warning` 日志级别常用于已处理的异常。</span><span class="sxs-lookup"><span data-stu-id="53dc3-217">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="53dc3-218">示例：`FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="53dc3-218">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="53dc3-219">错误 = 4</span><span class="sxs-lookup"><span data-stu-id="53dc3-219">Error = 4</span></span>

  <span data-ttu-id="53dc3-220">表示无法处理的错误和异常。</span><span class="sxs-lookup"><span data-stu-id="53dc3-220">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="53dc3-221">这些消息指示的是当前活动或操作（例如当前 HTTP 请求）中的失败，而不是整个应用中的失败。</span><span class="sxs-lookup"><span data-stu-id="53dc3-221">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="53dc3-222">日志消息示例：`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="53dc3-222">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="53dc3-223">严重 = 5</span><span class="sxs-lookup"><span data-stu-id="53dc3-223">Critical = 5</span></span>

  <span data-ttu-id="53dc3-224">需要立即关注的失败。</span><span class="sxs-lookup"><span data-stu-id="53dc3-224">For failures that require immediate attention.</span></span> <span data-ttu-id="53dc3-225">例如数据丢失、磁盘空间不足。</span><span class="sxs-lookup"><span data-stu-id="53dc3-225">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="53dc3-226">使用日志级别控制写入到特定存储介质或显示窗口的日志输出量。</span><span class="sxs-lookup"><span data-stu-id="53dc3-226">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="53dc3-227">例如:</span><span class="sxs-lookup"><span data-stu-id="53dc3-227">For example:</span></span>

* <span data-ttu-id="53dc3-228">在生产中，通过 `Information` 级别将 `Trace` 发送到卷数据存储。</span><span class="sxs-lookup"><span data-stu-id="53dc3-228">In production, send `Trace` through `Information` level to a volume data store.</span></span> <span data-ttu-id="53dc3-229">通过 `Critical` 级别将 `Warning` 发送到值数据存储。</span><span class="sxs-lookup"><span data-stu-id="53dc3-229">Send `Warning` through `Critical` to a value data store.</span></span>
* <span data-ttu-id="53dc3-230">在开发过程中，通过`Critical` 级别将 `Warning` 发送到控制台，并在进行故障排除时通过 `Information` 级别添加 `Trace`。</span><span class="sxs-lookup"><span data-stu-id="53dc3-230">During development, send `Warning` through `Critical` to the console, and add `Trace` through `Information` when troubleshooting.</span></span>

<span data-ttu-id="53dc3-231">本文稍后的[日志筛选](#log-filtering)部分介绍如何控制提供程序处理的日志级别。</span><span class="sxs-lookup"><span data-stu-id="53dc3-231">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="53dc3-232">ASP.NET Core 为框架事件写入日志。</span><span class="sxs-lookup"><span data-stu-id="53dc3-232">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="53dc3-233">本文前面部分提供的日志示例排除了低于 `Information` 级别的日志，因此未创建 `Debug` 或 `Trace` 级别日志。</span><span class="sxs-lookup"><span data-stu-id="53dc3-233">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="53dc3-234">以下示例介绍了通过运行配置为显示 `Debug` 日志的示例应用而生成的控制台日志：</span><span class="sxs-lookup"><span data-stu-id="53dc3-234">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="53dc3-235">日志事件 ID</span><span class="sxs-lookup"><span data-stu-id="53dc3-235">Log event ID</span></span>

<span data-ttu-id="53dc3-236">每个日志都可指定一个事件 ID  。</span><span class="sxs-lookup"><span data-stu-id="53dc3-236">Each log can specify an *event ID*.</span></span> <span data-ttu-id="53dc3-237">该示例应用通过使用本地定义的 `LoggingEvents` 类来执行此操作：</span><span class="sxs-lookup"><span data-stu-id="53dc3-237">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

<span data-ttu-id="53dc3-238">事件 ID 与一组事件相关联。</span><span class="sxs-lookup"><span data-stu-id="53dc3-238">An event ID associates a set of events.</span></span> <span data-ttu-id="53dc3-239">例如，与在页面上显示项列表相关的所有日志可能是 1001。</span><span class="sxs-lookup"><span data-stu-id="53dc3-239">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="53dc3-240">日志记录提供程序可将事件 ID 存储在 ID 字段中，存储在日志记录消息中，或者不进行存储。</span><span class="sxs-lookup"><span data-stu-id="53dc3-240">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="53dc3-241">调试提供程序不显示事件 ID。</span><span class="sxs-lookup"><span data-stu-id="53dc3-241">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="53dc3-242">控制台提供程序在类别后的括号中显示事件 ID：</span><span class="sxs-lookup"><span data-stu-id="53dc3-242">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="53dc3-243">日志消息模板</span><span class="sxs-lookup"><span data-stu-id="53dc3-243">Log message template</span></span>

<span data-ttu-id="53dc3-244">每个日志都会指定一个消息模板。</span><span class="sxs-lookup"><span data-stu-id="53dc3-244">Each log specifies a message template.</span></span> <span data-ttu-id="53dc3-245">消息模板可包含要填写参数的占位符。</span><span class="sxs-lookup"><span data-stu-id="53dc3-245">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="53dc3-246">请在占位符中使用名称而不是数字。</span><span class="sxs-lookup"><span data-stu-id="53dc3-246">Use names for the placeholders, not numbers.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="53dc3-247">占位符的顺序（而非其名称）决定了为其提供值的参数。</span><span class="sxs-lookup"><span data-stu-id="53dc3-247">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="53dc3-248">在以下代码中，请注意消息模板中的参数名称未按顺序排列：</span><span class="sxs-lookup"><span data-stu-id="53dc3-248">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="53dc3-249">此代码创建了一个参数值按顺序排列的日志消息：</span><span class="sxs-lookup"><span data-stu-id="53dc3-249">This code creates a log message with the parameter values in sequence:</span></span>

```
Parameter values: parm1, parm2
```

<span data-ttu-id="53dc3-250">日志记录框架按此方式工作，这样日志记录提供程序即可实现[语义日志记录，也称为结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="53dc3-250">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="53dc3-251">参数本身会传递给日志记录系统，而不仅仅是格式化的消息模板。</span><span class="sxs-lookup"><span data-stu-id="53dc3-251">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="53dc3-252">通过此信息，日志记录提供程序能够将参数值存储为字段。</span><span class="sxs-lookup"><span data-stu-id="53dc3-252">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="53dc3-253">例如，假设记录器方法调用如下所示：</span><span class="sxs-lookup"><span data-stu-id="53dc3-253">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="53dc3-254">如果要将日志发送到 Azure 表存储，则每个 Azure 表实体都可具有 `ID` 和 `RequestTime` 属性，这简化了对日志数据的查询。</span><span class="sxs-lookup"><span data-stu-id="53dc3-254">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="53dc3-255">无需分析文本消息的超时，查询即可找到特定 `RequestTime` 范围内的全部日志。</span><span class="sxs-lookup"><span data-stu-id="53dc3-255">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="53dc3-256">日志记录异常</span><span class="sxs-lookup"><span data-stu-id="53dc3-256">Logging exceptions</span></span>

<span data-ttu-id="53dc3-257">记录器方法有可传入异常的重载，如下方示例所示：</span><span class="sxs-lookup"><span data-stu-id="53dc3-257">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

<span data-ttu-id="53dc3-258">不同的提供程序处理异常信息的方式不同。</span><span class="sxs-lookup"><span data-stu-id="53dc3-258">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="53dc3-259">以下是上示代码的调试提供程序输出示例。</span><span class="sxs-lookup"><span data-stu-id="53dc3-259">Here's an example of Debug provider output from the code shown above.</span></span>

```
TodoApi.Controllers.TodoController:Warning: GetById(036dd898-fb01-47e8-9a65-f92eb73cf924) NOT FOUND

System.Exception: Item not found exception.
 at TodoApi.Controllers.TodoController.GetById(String id) in C:\logging\sample\src\TodoApi\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="53dc3-260">日志筛选</span><span class="sxs-lookup"><span data-stu-id="53dc3-260">Log filtering</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="53dc3-261">可为特定或所有提供程序和类别指定最低日志级别。</span><span class="sxs-lookup"><span data-stu-id="53dc3-261">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="53dc3-262">最低级别以下的日志不会传递给该提供程序，因此不会显示或存储它们。</span><span class="sxs-lookup"><span data-stu-id="53dc3-262">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="53dc3-263">要禁止显示所有日志，可将 `LogLevel.None` 指定为最低日志级别。</span><span class="sxs-lookup"><span data-stu-id="53dc3-263">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="53dc3-264">`LogLevel.None` 的整数值为 6，它大于 `LogLevel.Critical` (5)。</span><span class="sxs-lookup"><span data-stu-id="53dc3-264">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="53dc3-265">在配置中创建筛选规则</span><span class="sxs-lookup"><span data-stu-id="53dc3-265">Create filter rules in configuration</span></span>

<span data-ttu-id="53dc3-266">项目模板代码调用 `CreateDefaultBuilder` 来为控制台和调试提供程序设置日志记录。</span><span class="sxs-lookup"><span data-stu-id="53dc3-266">The project template code calls `CreateDefaultBuilder` to set up logging for the Console and Debug providers.</span></span> <span data-ttu-id="53dc3-267">`CreateDefaultBuilder` 方法还使用如下所示的代码，设置日志记录以查找 `Logging` 部分的配置：</span><span class="sxs-lookup"><span data-stu-id="53dc3-267">The `CreateDefaultBuilder` method also sets up logging to look for configuration in a `Logging` section, using code like the following:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=17)]

<span data-ttu-id="53dc3-268">配置数据按提供程序和类别指定最低日志级别，如下方示例所示：</span><span class="sxs-lookup"><span data-stu-id="53dc3-268">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="53dc3-269">此 JSON 将创建 6 条筛选规则：1 条用于调试提供程序， 4 条用于控制台提供程序， 1 条用于所有提供程序。</span><span class="sxs-lookup"><span data-stu-id="53dc3-269">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="53dc3-270">创建 `ILogger` 对象时，为每个提供程序选择一个规则。</span><span class="sxs-lookup"><span data-stu-id="53dc3-270">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="53dc3-271">代码中的筛选规则</span><span class="sxs-lookup"><span data-stu-id="53dc3-271">Filter rules in code</span></span>

<span data-ttu-id="53dc3-272">下面的示例演示了如何在代码中注册筛选规则：</span><span class="sxs-lookup"><span data-stu-id="53dc3-272">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="53dc3-273">第二个 `AddFilter` 使用类型名称来指定调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="53dc3-273">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="53dc3-274">第一个 `AddFilter` 应用于全部提供程序，因为它未指定提供程序类型。</span><span class="sxs-lookup"><span data-stu-id="53dc3-274">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="53dc3-275">如何应用筛选规则</span><span class="sxs-lookup"><span data-stu-id="53dc3-275">How filtering rules are applied</span></span>

<span data-ttu-id="53dc3-276">先前示例中显示的配置数据和 `AddFilter` 代码会创建下表所示的规则。</span><span class="sxs-lookup"><span data-stu-id="53dc3-276">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="53dc3-277">前六条由配置示例创建，后两条由代码示例创建。</span><span class="sxs-lookup"><span data-stu-id="53dc3-277">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="53dc3-278">数字</span><span class="sxs-lookup"><span data-stu-id="53dc3-278">Number</span></span> | <span data-ttu-id="53dc3-279">提供程序</span><span class="sxs-lookup"><span data-stu-id="53dc3-279">Provider</span></span>      | <span data-ttu-id="53dc3-280">类别的开头为...</span><span class="sxs-lookup"><span data-stu-id="53dc3-280">Categories that begin with ...</span></span>          | <span data-ttu-id="53dc3-281">最低日志级别</span><span class="sxs-lookup"><span data-stu-id="53dc3-281">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="53dc3-282">1</span><span class="sxs-lookup"><span data-stu-id="53dc3-282">1</span></span>      | <span data-ttu-id="53dc3-283">调试</span><span class="sxs-lookup"><span data-stu-id="53dc3-283">Debug</span></span>         | <span data-ttu-id="53dc3-284">全部类别</span><span class="sxs-lookup"><span data-stu-id="53dc3-284">All categories</span></span>                          | <span data-ttu-id="53dc3-285">信息</span><span class="sxs-lookup"><span data-stu-id="53dc3-285">Information</span></span>       |
| <span data-ttu-id="53dc3-286">2</span><span class="sxs-lookup"><span data-stu-id="53dc3-286">2</span></span>      | <span data-ttu-id="53dc3-287">控制台</span><span class="sxs-lookup"><span data-stu-id="53dc3-287">Console</span></span>       | <span data-ttu-id="53dc3-288">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="53dc3-288">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="53dc3-289">警告</span><span class="sxs-lookup"><span data-stu-id="53dc3-289">Warning</span></span>           |
| <span data-ttu-id="53dc3-290">3</span><span class="sxs-lookup"><span data-stu-id="53dc3-290">3</span></span>      | <span data-ttu-id="53dc3-291">控制台</span><span class="sxs-lookup"><span data-stu-id="53dc3-291">Console</span></span>       | <span data-ttu-id="53dc3-292">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="53dc3-292">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="53dc3-293">调试</span><span class="sxs-lookup"><span data-stu-id="53dc3-293">Debug</span></span>             |
| <span data-ttu-id="53dc3-294">4</span><span class="sxs-lookup"><span data-stu-id="53dc3-294">4</span></span>      | <span data-ttu-id="53dc3-295">控制台</span><span class="sxs-lookup"><span data-stu-id="53dc3-295">Console</span></span>       | <span data-ttu-id="53dc3-296">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="53dc3-296">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="53dc3-297">Error</span><span class="sxs-lookup"><span data-stu-id="53dc3-297">Error</span></span>             |
| <span data-ttu-id="53dc3-298">5</span><span class="sxs-lookup"><span data-stu-id="53dc3-298">5</span></span>      | <span data-ttu-id="53dc3-299">控制台</span><span class="sxs-lookup"><span data-stu-id="53dc3-299">Console</span></span>       | <span data-ttu-id="53dc3-300">全部类别</span><span class="sxs-lookup"><span data-stu-id="53dc3-300">All categories</span></span>                          | <span data-ttu-id="53dc3-301">信息</span><span class="sxs-lookup"><span data-stu-id="53dc3-301">Information</span></span>       |
| <span data-ttu-id="53dc3-302">6</span><span class="sxs-lookup"><span data-stu-id="53dc3-302">6</span></span>      | <span data-ttu-id="53dc3-303">全部提供程序</span><span class="sxs-lookup"><span data-stu-id="53dc3-303">All providers</span></span> | <span data-ttu-id="53dc3-304">全部类别</span><span class="sxs-lookup"><span data-stu-id="53dc3-304">All categories</span></span>                          | <span data-ttu-id="53dc3-305">调试</span><span class="sxs-lookup"><span data-stu-id="53dc3-305">Debug</span></span>             |
| <span data-ttu-id="53dc3-306">7</span><span class="sxs-lookup"><span data-stu-id="53dc3-306">7</span></span>      | <span data-ttu-id="53dc3-307">全部提供程序</span><span class="sxs-lookup"><span data-stu-id="53dc3-307">All providers</span></span> | <span data-ttu-id="53dc3-308">System</span><span class="sxs-lookup"><span data-stu-id="53dc3-308">System</span></span>                                  | <span data-ttu-id="53dc3-309">调试</span><span class="sxs-lookup"><span data-stu-id="53dc3-309">Debug</span></span>             |
| <span data-ttu-id="53dc3-310">8</span><span class="sxs-lookup"><span data-stu-id="53dc3-310">8</span></span>      | <span data-ttu-id="53dc3-311">调试</span><span class="sxs-lookup"><span data-stu-id="53dc3-311">Debug</span></span>         | <span data-ttu-id="53dc3-312">Microsoft</span><span class="sxs-lookup"><span data-stu-id="53dc3-312">Microsoft</span></span>                               | <span data-ttu-id="53dc3-313">跟踪</span><span class="sxs-lookup"><span data-stu-id="53dc3-313">Trace</span></span>             |

<span data-ttu-id="53dc3-314">创建 `ILogger` 对象时，`ILoggerFactory` 对象将根据提供程序选择一条规则，将其应用于该记录器。</span><span class="sxs-lookup"><span data-stu-id="53dc3-314">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="53dc3-315">将按所选规则筛选 `ILogger` 实例写入的所有消息。</span><span class="sxs-lookup"><span data-stu-id="53dc3-315">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="53dc3-316">从可用规则中为每个提供程序和类别对选择最具体的规则。</span><span class="sxs-lookup"><span data-stu-id="53dc3-316">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="53dc3-317">在为给定的类别创建 `ILogger` 时，以下算法将用于每个提供程序：</span><span class="sxs-lookup"><span data-stu-id="53dc3-317">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="53dc3-318">选择匹配提供程序或其别名的所有规则。</span><span class="sxs-lookup"><span data-stu-id="53dc3-318">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="53dc3-319">如果找不到任何匹配项，则选择提供程序为空的所有规则。</span><span class="sxs-lookup"><span data-stu-id="53dc3-319">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="53dc3-320">根据上一步的结果，选择具有最长匹配类别前缀的规则。</span><span class="sxs-lookup"><span data-stu-id="53dc3-320">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="53dc3-321">如果找不到任何匹配项，则选择未指定类别的所有规则。</span><span class="sxs-lookup"><span data-stu-id="53dc3-321">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="53dc3-322">如果选择了多条规则，则采用最后一条  。</span><span class="sxs-lookup"><span data-stu-id="53dc3-322">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="53dc3-323">如果未选择任何规则，则使用 `MinimumLevel`。</span><span class="sxs-lookup"><span data-stu-id="53dc3-323">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="53dc3-324">假设你使用上述规则列表为类别“Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine”创建了 `ILogger` 对象：</span><span class="sxs-lookup"><span data-stu-id="53dc3-324">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="53dc3-325">对于调试提供程序，规则 1、6 和 8 适用。</span><span class="sxs-lookup"><span data-stu-id="53dc3-325">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="53dc3-326">规则 8 最为具体，因此选择了它。</span><span class="sxs-lookup"><span data-stu-id="53dc3-326">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="53dc3-327">对于控制台提供程序，符合的有规则 3、规则 4、规则 5 和规则 6。</span><span class="sxs-lookup"><span data-stu-id="53dc3-327">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="53dc3-328">规则 3 最为具体。</span><span class="sxs-lookup"><span data-stu-id="53dc3-328">Rule 3 is most specific.</span></span>

<span data-ttu-id="53dc3-329">生成的 `ILogger` 实例将 `Trace` 级别及更高级别的日志发送到调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="53dc3-329">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="53dc3-330">`Debug` 级别及更高级别的日志会发送到控制台提供程序。</span><span class="sxs-lookup"><span data-stu-id="53dc3-330">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="53dc3-331">提供程序别名</span><span class="sxs-lookup"><span data-stu-id="53dc3-331">Provider aliases</span></span>

<span data-ttu-id="53dc3-332">每个提供程序都定义了一个别名；可在配置中使用该别名来代替完全限定的类型名称  。</span><span class="sxs-lookup"><span data-stu-id="53dc3-332">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="53dc3-333">对于内置提供程序，请使用以下别名：</span><span class="sxs-lookup"><span data-stu-id="53dc3-333">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="53dc3-334">控制台</span><span class="sxs-lookup"><span data-stu-id="53dc3-334">Console</span></span>
* <span data-ttu-id="53dc3-335">调试</span><span class="sxs-lookup"><span data-stu-id="53dc3-335">Debug</span></span>
* <span data-ttu-id="53dc3-336">EventSource</span><span class="sxs-lookup"><span data-stu-id="53dc3-336">EventSource</span></span>
* <span data-ttu-id="53dc3-337">EventLog</span><span class="sxs-lookup"><span data-stu-id="53dc3-337">EventLog</span></span>
* <span data-ttu-id="53dc3-338">TraceSource</span><span class="sxs-lookup"><span data-stu-id="53dc3-338">TraceSource</span></span>
* <span data-ttu-id="53dc3-339">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="53dc3-339">AzureAppServicesFile</span></span>
* <span data-ttu-id="53dc3-340">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="53dc3-340">AzureAppServicesBlob</span></span>
* <span data-ttu-id="53dc3-341">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="53dc3-341">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="53dc3-342">默认最低级别</span><span class="sxs-lookup"><span data-stu-id="53dc3-342">Default minimum level</span></span>

<span data-ttu-id="53dc3-343">仅当配置或代码中的规则对给定提供程序和类别都不适用时，最低级别设置才会生效。</span><span class="sxs-lookup"><span data-stu-id="53dc3-343">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="53dc3-344">下面的示例演示如何设置最低级别：</span><span class="sxs-lookup"><span data-stu-id="53dc3-344">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="53dc3-345">如果没有明确设置最低级别，则默认值为 `Information`，它表示 `Trace` 和 `Debug` 日志将被忽略。</span><span class="sxs-lookup"><span data-stu-id="53dc3-345">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="53dc3-346">筛选器函数</span><span class="sxs-lookup"><span data-stu-id="53dc3-346">Filter functions</span></span>

<span data-ttu-id="53dc3-347">对配置或代码没有向其分配规则的所有提供程序和类别调用筛选器函数。</span><span class="sxs-lookup"><span data-stu-id="53dc3-347">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="53dc3-348">函数中的代码可访问提供程序类型、类别和日志级别。</span><span class="sxs-lookup"><span data-stu-id="53dc3-348">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="53dc3-349">例如:</span><span class="sxs-lookup"><span data-stu-id="53dc3-349">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="53dc3-350">通过某些日志记录提供程序，可根据日志级别和类别指定何时向存储介质写入日志、何时忽略日志。</span><span class="sxs-lookup"><span data-stu-id="53dc3-350">Some logging providers let you specify when logs should be written to a storage medium or ignored based on log level and category.</span></span>

<span data-ttu-id="53dc3-351">`AddConsole` 和 `AddDebug` 扩展方法提供了接受筛选条件的重载。</span><span class="sxs-lookup"><span data-stu-id="53dc3-351">The `AddConsole` and `AddDebug` extension methods provide overloads that accept filtering criteria.</span></span> <span data-ttu-id="53dc3-352">下列示例代码将使控制台提供程序忽略低于 `Warning` 级别的日志，而使调试提供程序忽略由框架创建的日志。</span><span class="sxs-lookup"><span data-stu-id="53dc3-352">The following sample code causes the console provider to ignore logs below `Warning` level, while the Debug provider ignores logs that the framework creates.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_AddConsoleAndDebugWithFilter&highlight=6-7)]

<span data-ttu-id="53dc3-353">`AddEventLog` 方法拥有接受 `EventLogSettings` 实例的重载，该实例的 `Filter` 属性中可能包含筛选函数。</span><span class="sxs-lookup"><span data-stu-id="53dc3-353">The `AddEventLog` method has an overload that takes an `EventLogSettings` instance, which may contain a filtering function in its `Filter` property.</span></span> <span data-ttu-id="53dc3-354">TraceSource 提供程序不提供以上任何重载，因为其日志记录级别和其他参数都以它使用的 `SourceSwitch` 和 `TraceListener` 为依据。</span><span class="sxs-lookup"><span data-stu-id="53dc3-354">The TraceSource provider doesn't provide any of those overloads, since its logging level and other parameters are based on the `SourceSwitch` and `TraceListener` it uses.</span></span>

<span data-ttu-id="53dc3-355">可使用 `WithFilter` 扩展方法为所有通过 `ILoggerFactory` 实例注册的提供程序设置筛选规则。</span><span class="sxs-lookup"><span data-stu-id="53dc3-355">To set filtering rules for all providers that are registered with an `ILoggerFactory` instance, use the `WithFilter` extension method.</span></span> <span data-ttu-id="53dc3-356">下面的示例将（类别以“Microsoft”或“System”开头的）框架日志限制为警告，并在调试级别记录应用程序代码创建的日志。</span><span class="sxs-lookup"><span data-stu-id="53dc3-356">The example below limits framework logs (category begins with "Microsoft" or "System") to warnings while logging at debug level for logs created by application code.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_FactoryFilter&highlight=6-11)]

<span data-ttu-id="53dc3-357">要防止写入任何日志，请将 `LogLevel.None` 指定为最低日志级别。</span><span class="sxs-lookup"><span data-stu-id="53dc3-357">To prevent any logs from being written, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="53dc3-358">`LogLevel.None` 的整数值为 6，它大于 `LogLevel.Critical` (5)。</span><span class="sxs-lookup"><span data-stu-id="53dc3-358">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="53dc3-359">`WithFilter` 扩展方法由 [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) NuGet 包提供。</span><span class="sxs-lookup"><span data-stu-id="53dc3-359">The `WithFilter` extension method is provided by the [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) NuGet package.</span></span> <span data-ttu-id="53dc3-360">该方法返回一个新的 `ILoggerFactory` 实例，该实例将筛选传递给注册的所有记录器提供程序的日志消息。</span><span class="sxs-lookup"><span data-stu-id="53dc3-360">The method returns a new `ILoggerFactory` instance that will filter the log messages passed to all logger providers registered with it.</span></span> <span data-ttu-id="53dc3-361">它不会影响其他任何 `ILoggerFactory` 实例，包括原始 `ILoggerFactory` 实例。</span><span class="sxs-lookup"><span data-stu-id="53dc3-361">It doesn't affect any other `ILoggerFactory` instances, including the original `ILoggerFactory` instance.</span></span>

::: moniker-end

## <a name="system-categories-and-levels"></a><span data-ttu-id="53dc3-362">系统类别和级别</span><span class="sxs-lookup"><span data-stu-id="53dc3-362">System categories and levels</span></span>

<span data-ttu-id="53dc3-363">下面是 ASP.NET Core 和 Entity Framework Core 使用的一些类别，备注中说明了可从这些类别获取的具体日志：</span><span class="sxs-lookup"><span data-stu-id="53dc3-363">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="53dc3-364">类别</span><span class="sxs-lookup"><span data-stu-id="53dc3-364">Category</span></span>                            | <span data-ttu-id="53dc3-365">说明</span><span class="sxs-lookup"><span data-stu-id="53dc3-365">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="53dc3-366">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="53dc3-366">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="53dc3-367">常规 ASP.NET Core 诊断。</span><span class="sxs-lookup"><span data-stu-id="53dc3-367">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="53dc3-368">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="53dc3-368">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="53dc3-369">考虑、找到并使用了哪些密钥。</span><span class="sxs-lookup"><span data-stu-id="53dc3-369">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="53dc3-370">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="53dc3-370">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="53dc3-371">所允许的主机。</span><span class="sxs-lookup"><span data-stu-id="53dc3-371">Hosts allowed.</span></span> |
| <span data-ttu-id="53dc3-372">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="53dc3-372">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="53dc3-373">HTTP 请求完成的时间和启动时间。</span><span class="sxs-lookup"><span data-stu-id="53dc3-373">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="53dc3-374">加载了哪些承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="53dc3-374">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="53dc3-375">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="53dc3-375">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="53dc3-376">MVC 和 Razor 诊断。</span><span class="sxs-lookup"><span data-stu-id="53dc3-376">MVC and Razor diagnostics.</span></span> <span data-ttu-id="53dc3-377">模型绑定、筛选器执行、视图编译和操作选择。</span><span class="sxs-lookup"><span data-stu-id="53dc3-377">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="53dc3-378">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="53dc3-378">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="53dc3-379">路由匹配信息。</span><span class="sxs-lookup"><span data-stu-id="53dc3-379">Route matching information.</span></span> |
| <span data-ttu-id="53dc3-380">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="53dc3-380">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="53dc3-381">连接启动、停止和保持活动响应。</span><span class="sxs-lookup"><span data-stu-id="53dc3-381">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="53dc3-382">HTTP 证书信息。</span><span class="sxs-lookup"><span data-stu-id="53dc3-382">HTTPS certificate information.</span></span> |
| <span data-ttu-id="53dc3-383">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="53dc3-383">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="53dc3-384">提供的文件。</span><span class="sxs-lookup"><span data-stu-id="53dc3-384">Files served.</span></span> |
| <span data-ttu-id="53dc3-385">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="53dc3-385">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="53dc3-386">常规 Entity Framework Core 诊断。</span><span class="sxs-lookup"><span data-stu-id="53dc3-386">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="53dc3-387">数据库活动和配置、更改检测、迁移。</span><span class="sxs-lookup"><span data-stu-id="53dc3-387">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="53dc3-388">日志作用域</span><span class="sxs-lookup"><span data-stu-id="53dc3-388">Log scopes</span></span>

 <span data-ttu-id="53dc3-389">“作用域”可对一组逻辑操作分组  。</span><span class="sxs-lookup"><span data-stu-id="53dc3-389">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="53dc3-390">此分组可用于将相同的数据附加到作为集合的一部分而创建的每个日志。</span><span class="sxs-lookup"><span data-stu-id="53dc3-390">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="53dc3-391">例如，在处理事务期间创建的每个日志都可包括事务 ID。</span><span class="sxs-lookup"><span data-stu-id="53dc3-391">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="53dc3-392">范围是由 <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> 方法返回的 `IDisposable` 类型，持续至释放为止。</span><span class="sxs-lookup"><span data-stu-id="53dc3-392">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="53dc3-393">要使用作用域，请在 `using` 块中包装记录器调用：</span><span class="sxs-lookup"><span data-stu-id="53dc3-393">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="53dc3-394">下列代码为控制台提供程序启用作用域：</span><span class="sxs-lookup"><span data-stu-id="53dc3-394">The following code enables scopes for the console provider:</span></span>

::: moniker range="> aspnetcore-2.0"

<span data-ttu-id="53dc3-395">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="53dc3-395">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="53dc3-396">要启用基于作用域的日志记录，必须先配置 `IncludeScopes` 控制台记录器选项。</span><span class="sxs-lookup"><span data-stu-id="53dc3-396">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="53dc3-397">若要了解关配置，请参阅[配置](#configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="53dc3-397">For information on configuration, see the [Configuration](#configuration) section.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="53dc3-398">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="53dc3-398">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="53dc3-399">要启用基于作用域的日志记录，必须先配置 `IncludeScopes` 控制台记录器选项。</span><span class="sxs-lookup"><span data-stu-id="53dc3-399">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="53dc3-400">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="53dc3-400">*Startup.cs*:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

<span data-ttu-id="53dc3-401">每条日志消息都包含作用域内的信息：</span><span class="sxs-lookup"><span data-stu-id="53dc3-401">Each log message includes the scoped information:</span></span>

```
info: TodoApi.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="53dc3-402">内置日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="53dc3-402">Built-in logging providers</span></span>

<span data-ttu-id="53dc3-403">ASP.NET Core 提供以下提供程序：</span><span class="sxs-lookup"><span data-stu-id="53dc3-403">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="53dc3-404">控制台</span><span class="sxs-lookup"><span data-stu-id="53dc3-404">Console</span></span>](#console-provider)
* [<span data-ttu-id="53dc3-405">调试</span><span class="sxs-lookup"><span data-stu-id="53dc3-405">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="53dc3-406">EventSource</span><span class="sxs-lookup"><span data-stu-id="53dc3-406">EventSource</span></span>](#eventsource-provider)
* [<span data-ttu-id="53dc3-407">EventLog</span><span class="sxs-lookup"><span data-stu-id="53dc3-407">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="53dc3-408">TraceSource</span><span class="sxs-lookup"><span data-stu-id="53dc3-408">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="53dc3-409">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="53dc3-409">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="53dc3-410">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="53dc3-410">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="53dc3-411">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="53dc3-411">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="53dc3-412">要通过 ASP.NET Core 模块了解 stdout 和调试日志记录，请参阅 <xref:test/troubleshoot-azure-iis> 和 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="53dc3-412">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="53dc3-413">控制台提供程序</span><span class="sxs-lookup"><span data-stu-id="53dc3-413">Console provider</span></span>

<span data-ttu-id="53dc3-414">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 提供程序包向控制台发送日志输出。</span><span class="sxs-lookup"><span data-stu-id="53dc3-414">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddConsole();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddConsole();
```

<span data-ttu-id="53dc3-415">通过 [AddConsole 重载](xref:Microsoft.Extensions.Logging.ConsoleLoggerExtensions)，可传入一个最低日志级别、一个筛选器函数，以及一个用于指示作用域是否受支持的布尔值。</span><span class="sxs-lookup"><span data-stu-id="53dc3-415">[AddConsole overloads](xref:Microsoft.Extensions.Logging.ConsoleLoggerExtensions) let you pass in a minimum log level, a filter function, and a boolean that indicates whether scopes are supported.</span></span> <span data-ttu-id="53dc3-416">另一个选项是传递 `IConfiguration` 对象，可通过它来指定支持的作用域及日志记录级别。</span><span class="sxs-lookup"><span data-stu-id="53dc3-416">Another option is to pass in an `IConfiguration` object, which can specify scopes support and logging levels.</span></span>

<span data-ttu-id="53dc3-417">关于控制台提供程序选项，请参阅 <xref:Microsoft.Extensions.Logging.Console.ConsoleLoggerOptions>。</span><span class="sxs-lookup"><span data-stu-id="53dc3-417">For Console provider options, see <xref:Microsoft.Extensions.Logging.Console.ConsoleLoggerOptions>.</span></span>

<span data-ttu-id="53dc3-418">控制台提供程序对性能有重大影响，通常不适合在生产中使用。</span><span class="sxs-lookup"><span data-stu-id="53dc3-418">The console provider has a significant impact on performance and is generally not appropriate for use in production.</span></span>

<span data-ttu-id="53dc3-419">在 Visual Studio 中创建新项目时，`AddConsole` 方法如下所示：</span><span class="sxs-lookup"><span data-stu-id="53dc3-419">When you create a new project in Visual Studio, the `AddConsole` method looks like this:</span></span>

```csharp
loggerFactory.AddConsole(Configuration.GetSection("Logging"));
```

<span data-ttu-id="53dc3-420">此代码引用 appSettings.json 文件的 `Logging` 部分： </span><span class="sxs-lookup"><span data-stu-id="53dc3-420">This code refers to the `Logging` section of the *appSettings.json* file:</span></span>

[!code-json[](index/samples/1.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="53dc3-421">所显示的设置将框架日志限制为警告，并允许在调试级别记录应用日志，如[日志筛选](#log-filtering)部分所述。</span><span class="sxs-lookup"><span data-stu-id="53dc3-421">The settings shown limit framework logs to warnings while allowing the app to log at debug level, as explained in the [Log filtering](#log-filtering) section.</span></span> <span data-ttu-id="53dc3-422">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="53dc3-422">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

::: moniker-end

<span data-ttu-id="53dc3-423">要查看控制台日志记录输出，请在项目文件夹中打开命令提示符并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="53dc3-423">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```console
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="53dc3-424">调试提供程序</span><span class="sxs-lookup"><span data-stu-id="53dc3-424">Debug provider</span></span>

<span data-ttu-id="53dc3-425">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 提供程序包使用 [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) 类（`Debug.WriteLine` 方法调用）来写入日志输出。</span><span class="sxs-lookup"><span data-stu-id="53dc3-425">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="53dc3-426">在 Linux 中，此提供程序将日志写入 /var/log/message  。</span><span class="sxs-lookup"><span data-stu-id="53dc3-426">On Linux, this provider writes logs to */var/log/message*.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddDebug();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddDebug();
```

<span data-ttu-id="53dc3-427">[AddDebug 重载](xref:Microsoft.Extensions.Logging.DebugLoggerFactoryExtensions)允许传入最低日志级别或筛选器函数。</span><span class="sxs-lookup"><span data-stu-id="53dc3-427">[AddDebug overloads](xref:Microsoft.Extensions.Logging.DebugLoggerFactoryExtensions) let you pass in a minimum log level or a filter function.</span></span>

::: moniker-end

### <a name="eventsource-provider"></a><span data-ttu-id="53dc3-428">EventSource 提供程序</span><span class="sxs-lookup"><span data-stu-id="53dc3-428">EventSource provider</span></span>

<span data-ttu-id="53dc3-429">对于面向 ASP.NET Core 1.1.0 或更高版本的应用，[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) 提供程序包可实现事件跟踪。</span><span class="sxs-lookup"><span data-stu-id="53dc3-429">For apps that target ASP.NET Core 1.1.0 or later, the [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package can implement event tracing.</span></span> <span data-ttu-id="53dc3-430">在 Windows 中，它使用 [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)。</span><span class="sxs-lookup"><span data-stu-id="53dc3-430">On Windows, it uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span> <span data-ttu-id="53dc3-431">提供程序可跨平台使用，但尚无支持 Linux 或 macOS 的事件集合和显示工具。</span><span class="sxs-lookup"><span data-stu-id="53dc3-431">The provider is cross-platform, but there are no event collection and display tools yet for Linux or macOS.</span></span>

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

<span data-ttu-id="53dc3-432">可使用 [PerfView 实用工具](https://github.com/Microsoft/perfview)收集和查看日志。</span><span class="sxs-lookup"><span data-stu-id="53dc3-432">A good way to collect and view logs is to use the [PerfView utility](https://github.com/Microsoft/perfview).</span></span> <span data-ttu-id="53dc3-433">虽然其他工具也可以查看 ETW 日志，但在处理由 ASP.NET 发出的 ETW 事件时，使用 PerfView 能获得最佳体验。</span><span class="sxs-lookup"><span data-stu-id="53dc3-433">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET.</span></span>

<span data-ttu-id="53dc3-434">要将 PerfView 配置为收集此提供程序记录的事件，请向 Additional Providers 列表添加字符串 `*Microsoft-Extensions-Logging`  。</span><span class="sxs-lookup"><span data-stu-id="53dc3-434">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="53dc3-435">（请勿遗漏字符串起始处的星号。）</span><span class="sxs-lookup"><span data-stu-id="53dc3-435">(Don't miss the asterisk at the start of the string.)</span></span>

![其他 Perfview 提供程序](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="53dc3-437">Windows EventLog 提供程序</span><span class="sxs-lookup"><span data-stu-id="53dc3-437">Windows EventLog provider</span></span>

<span data-ttu-id="53dc3-438">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 提供程序包向 Windows 事件日志发送日志输出。</span><span class="sxs-lookup"><span data-stu-id="53dc3-438">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventLog();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventLog();
```

<span data-ttu-id="53dc3-439">[AddEventLog 重载](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)允许传入 `EventLogSettings` 或最低日志级别。</span><span class="sxs-lookup"><span data-stu-id="53dc3-439">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in `EventLogSettings` or a minimum log level.</span></span>

::: moniker-end

### <a name="tracesource-provider"></a><span data-ttu-id="53dc3-440">TraceSource 提供程序</span><span class="sxs-lookup"><span data-stu-id="53dc3-440">TraceSource provider</span></span>

<span data-ttu-id="53dc3-441">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) 提供程序包使用 <xref:System.Diagnostics.TraceSource> 库和提供程序。</span><span class="sxs-lookup"><span data-stu-id="53dc3-441">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

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

<span data-ttu-id="53dc3-442">[AddTraceSource 重载](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) 允许传入资源开关和跟踪侦听器。</span><span class="sxs-lookup"><span data-stu-id="53dc3-442">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="53dc3-443">要使用此提供程序，应用必须在 .NET Framework（而非 .NET Core）上运行。</span><span class="sxs-lookup"><span data-stu-id="53dc3-443">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="53dc3-444">提供程序可将消息路由到多个[侦听器](/dotnet/framework/debug-trace-profile/trace-listeners)，例如示例应用中使用的 <xref:System.Diagnostics.TextWriterTraceListener>。</span><span class="sxs-lookup"><span data-stu-id="53dc3-444">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="53dc3-445">以下示例配置了 `TraceSource` 提供程序，用于向控制台窗口记录 `Warning` 和更高级别的消息。</span><span class="sxs-lookup"><span data-stu-id="53dc3-445">The following example configures a `TraceSource` provider that logs `Warning` and higher messages to the console window.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_TraceSource&highlight=9-12)]

::: moniker-end

### <a name="azure-app-service-provider"></a><span data-ttu-id="53dc3-446">Azure 应用服务提供程序</span><span class="sxs-lookup"><span data-stu-id="53dc3-446">Azure App Service provider</span></span>

<span data-ttu-id="53dc3-447">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 提供程序包将日志写入 Azure App Service 应用的文件系统，以及 Azure 存储帐户中的 [blob 存储](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)。</span><span class="sxs-lookup"><span data-stu-id="53dc3-447">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span> <span data-ttu-id="53dc3-448">面向 .NET Core 1.1 或更高版本的应用可使用该提供程序包。</span><span class="sxs-lookup"><span data-stu-id="53dc3-448">The provider package is available for apps targeting .NET Core 1.1 or later.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="53dc3-449">如果面向 .NET Core，请注意以下几点：</span><span class="sxs-lookup"><span data-stu-id="53dc3-449">If targeting .NET Core, note the following points:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="53dc3-450">提供程序包随附在 ASP.NET Core [Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)中。</span><span class="sxs-lookup"><span data-stu-id="53dc3-450">The provider package is included in the ASP.NET Core [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="53dc3-451">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中不包括此提供程序包。</span><span class="sxs-lookup"><span data-stu-id="53dc3-451">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="53dc3-452">要使用提供程序，请安装该程序包。</span><span class="sxs-lookup"><span data-stu-id="53dc3-452">To use the provider, install the package.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="53dc3-453">如果面向 .NET Framework 或引用 `Microsoft.AspNetCore.App` 元包，请向项目添加提供程序包。</span><span class="sxs-lookup"><span data-stu-id="53dc3-453">If targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> <span data-ttu-id="53dc3-454">调用 `AddAzureWebAppDiagnostics`：</span><span class="sxs-lookup"><span data-stu-id="53dc3-454">Invoke `AddAzureWebAppDiagnostics`:</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range="= aspnetcore-1.1"

```csharp
loggerFactory.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="53dc3-455"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> 重载允许传入 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>。</span><span class="sxs-lookup"><span data-stu-id="53dc3-455">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="53dc3-456">设置对象可以覆盖默认设置，例如日志记录输出模板、blob 名称和文件大小限制。</span><span class="sxs-lookup"><span data-stu-id="53dc3-456">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="53dc3-457">（“输出模板”是一个消息模板，除了通过 `ILogger` 方法调用提供的内容之外，还可将其应用于所有日志。  ）</span><span class="sxs-lookup"><span data-stu-id="53dc3-457">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="53dc3-458">要配置提供程序设置，请使用 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> 和 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="53dc3-458">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

<span data-ttu-id="53dc3-459">在部署应用服务应用时，应用程序将采用 Azure 门户中“应用服务”页面下的[应用服务日志](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag)部分的设置  。</span><span class="sxs-lookup"><span data-stu-id="53dc3-459">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="53dc3-460">更新以下设置后，更改立即生效，无需重启或重新部署应用。</span><span class="sxs-lookup"><span data-stu-id="53dc3-460">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="53dc3-461">应用程序日志记录(Filesystem) </span><span class="sxs-lookup"><span data-stu-id="53dc3-461">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="53dc3-462">应用程序日志记录(Blob) </span><span class="sxs-lookup"><span data-stu-id="53dc3-462">**Application Logging (Blob)**</span></span>

<span data-ttu-id="53dc3-463">日志文件的默认位置是 D:\\home\\LogFiles\\Application 文件夹，默认文件名为 diagnostics-yyyymmdd.txt   。</span><span class="sxs-lookup"><span data-stu-id="53dc3-463">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="53dc3-464">默认文件大小上限为 10 MB，默认最大保留文件数为 2。</span><span class="sxs-lookup"><span data-stu-id="53dc3-464">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="53dc3-465">默认 blob 名为 {app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt  。</span><span class="sxs-lookup"><span data-stu-id="53dc3-465">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="53dc3-466">该提供程序仅当项目在 Azure 环境中运行时有效。</span><span class="sxs-lookup"><span data-stu-id="53dc3-466">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="53dc3-467">项目在本地运行时，该提供程序无效 &mdash; 它不会写入本地文件或 blob 的本地开发存储。</span><span class="sxs-lookup"><span data-stu-id="53dc3-467">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="53dc3-468">Azure 日志流式处理</span><span class="sxs-lookup"><span data-stu-id="53dc3-468">Azure log streaming</span></span>

<span data-ttu-id="53dc3-469">通过 Azure 日志流式处理，可从以下位置实时查看日志活动：</span><span class="sxs-lookup"><span data-stu-id="53dc3-469">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="53dc3-470">应用服务器</span><span class="sxs-lookup"><span data-stu-id="53dc3-470">The app server</span></span>
* <span data-ttu-id="53dc3-471">Web 服务器</span><span class="sxs-lookup"><span data-stu-id="53dc3-471">The web server</span></span>
* <span data-ttu-id="53dc3-472">请求跟踪失败</span><span class="sxs-lookup"><span data-stu-id="53dc3-472">Failed request tracing</span></span>

<span data-ttu-id="53dc3-473">要配置 Azure 日志流式处理，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="53dc3-473">To configure Azure log streaming:</span></span>

* <span data-ttu-id="53dc3-474">从应用的门户页导航到“应用服务日志”页  。</span><span class="sxs-lookup"><span data-stu-id="53dc3-474">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="53dc3-475">将“应用程序日志记录(Filesystem)”设置为“开”   。</span><span class="sxs-lookup"><span data-stu-id="53dc3-475">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="53dc3-476">选择日志级别  。</span><span class="sxs-lookup"><span data-stu-id="53dc3-476">Choose the log **Level**.</span></span>

<span data-ttu-id="53dc3-477">导航到“日志流”页面来查看应用消息  。</span><span class="sxs-lookup"><span data-stu-id="53dc3-477">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="53dc3-478">它们由应用通过 `ILogger` 接口记录。</span><span class="sxs-lookup"><span data-stu-id="53dc3-478">They're logged by the app through the `ILogger` interface.</span></span>

::: moniker range=">= aspnetcore-1.1"

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="53dc3-479">Azure Application Insights 跟踪日志记录</span><span class="sxs-lookup"><span data-stu-id="53dc3-479">Azure Application Insights trace logging</span></span>

<span data-ttu-id="53dc3-480">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) 提供程序包将日志写入 Azure Application Insights。</span><span class="sxs-lookup"><span data-stu-id="53dc3-480">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="53dc3-481">Application Insights 是一项服务，可监视 Web 应用并提供用于查询和分析遥测数据的工具。</span><span class="sxs-lookup"><span data-stu-id="53dc3-481">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="53dc3-482">如果使用此提供程序，则可以使用 Application Insights 工具来查询和分析日志。</span><span class="sxs-lookup"><span data-stu-id="53dc3-482">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="53dc3-483">日志记录提供程序作为 [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)（这是提供 ASP.NET Core 的所有可用遥测的包）的依赖项包括在内。</span><span class="sxs-lookup"><span data-stu-id="53dc3-483">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="53dc3-484">如果使用此包，则无需安装提供程序包。</span><span class="sxs-lookup"><span data-stu-id="53dc3-484">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="53dc3-485">请勿使用用于 ASP.NET 4.x 的 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) 包&mdash;。</span><span class="sxs-lookup"><span data-stu-id="53dc3-485">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="53dc3-486">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="53dc3-486">For more information, see the following resources:</span></span>

* [<span data-ttu-id="53dc3-487">Application Insights 概述</span><span class="sxs-lookup"><span data-stu-id="53dc3-487">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="53dc3-488">[用于 ASP.NET Core 应用程序的 Application Insights](/azure/azure-monitor/app/asp-net-core) - 如果想要实现各种 Application Insights 遥测以及日志记录，请从这里开始。</span><span class="sxs-lookup"><span data-stu-id="53dc3-488">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="53dc3-489">[.NET Core ILogger 日志的 ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - 如果想要在没有其他 Application Insights 遥测的情况下实现日志记录提供程序，请从这里开始。</span><span class="sxs-lookup"><span data-stu-id="53dc3-489">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="53dc3-490">[Application Insights 日志记录适配器](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)。</span><span class="sxs-lookup"><span data-stu-id="53dc3-490">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="53dc3-491">[安装、配置和初始化 Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn 网站上的交互式教程。</span><span class="sxs-lookup"><span data-stu-id="53dc3-491">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>
::: moniker-end

## <a name="third-party-logging-providers"></a><span data-ttu-id="53dc3-492">第三方日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="53dc3-492">Third-party logging providers</span></span>

<span data-ttu-id="53dc3-493">适用于 ASP.NET Core 的第三方日志记录框架：</span><span class="sxs-lookup"><span data-stu-id="53dc3-493">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="53dc3-494">[elmah.io](https://elmah.io/)（[GitHub 存储库](https://github.com/elmahio/Elmah.Io.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="53dc3-494">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="53dc3-495">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html)（[GitHub 存储库](https://github.com/mattwcole/gelf-extensions-logging)）</span><span class="sxs-lookup"><span data-stu-id="53dc3-495">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="53dc3-496">[JSNLog](https://jsnlog.com/)（[GitHub 存储库](https://github.com/mperdeck/jsnlog)）</span><span class="sxs-lookup"><span data-stu-id="53dc3-496">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="53dc3-497">[KissLog.net](https://kisslog.net/)（[GitHub 存储库](https://github.com/catalingavan/KissLog-net)）</span><span class="sxs-lookup"><span data-stu-id="53dc3-497">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="53dc3-498">[Loggr](https://loggr.net/)（[GitHub 存储库](https://github.com/imobile3/Loggr.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="53dc3-498">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="53dc3-499">[NLog](https://nlog-project.org/)（[GitHub 存储库](https://github.com/NLog/NLog.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="53dc3-499">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="53dc3-500">[Sentry](https://sentry.io/welcome/)（[GitHub 存储库](https://github.com/getsentry/sentry-dotnet)）</span><span class="sxs-lookup"><span data-stu-id="53dc3-500">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="53dc3-501">[Serilog](https://serilog.net/)（[GitHub 存储库](https://github.com/serilog/serilog-aspnetcore)）</span><span class="sxs-lookup"><span data-stu-id="53dc3-501">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="53dc3-502">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging)（[Github 存储库](https://github.com/googleapis/google-cloud-dotnet)）</span><span class="sxs-lookup"><span data-stu-id="53dc3-502">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="53dc3-503">某些第三方框架可以执行[语义日志记录（又称结构化日志记录）](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="53dc3-503">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="53dc3-504">使用第三方框架类似于使用以下内置提供程序之一：</span><span class="sxs-lookup"><span data-stu-id="53dc3-504">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="53dc3-505">将 NuGet 包添加到你的项目。</span><span class="sxs-lookup"><span data-stu-id="53dc3-505">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="53dc3-506">调用 `ILoggerFactory`。</span><span class="sxs-lookup"><span data-stu-id="53dc3-506">Call an `ILoggerFactory`.</span></span>

<span data-ttu-id="53dc3-507">有关详细信息，请参阅各提供程序的相关文档。</span><span class="sxs-lookup"><span data-stu-id="53dc3-507">For more information, see each provider's documentation.</span></span> <span data-ttu-id="53dc3-508">Microsoft 不支持第三方日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="53dc3-508">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="53dc3-509">其他资源</span><span class="sxs-lookup"><span data-stu-id="53dc3-509">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
