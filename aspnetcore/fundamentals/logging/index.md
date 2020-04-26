---
title: .NET Core 和 ASP.NET Core 中的日志记录
author: rick-anderson
description: 了解如何使用由 Microsoft Extension.Logging NuGet 包提供的日志记录框架。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/23/2020
uid: fundamentals/logging/index
ms.openlocfilehash: 7be8cef3377132ed43efde209db67401d7bdb6dc
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110910"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="f179e-103">.NET Core 和 ASP.NET Core 中的日志记录</span><span class="sxs-lookup"><span data-stu-id="f179e-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f179e-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f179e-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f179e-105">.NET Core 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。</span><span class="sxs-lookup"><span data-stu-id="f179e-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="f179e-106">本文介绍了如何将日志记录 API 与内置提供程序一起使用。</span><span class="sxs-lookup"><span data-stu-id="f179e-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="f179e-107">本文中所述的大多数代码示例都来自 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="f179e-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="f179e-108">这些代码片段的日志记录特定部分适用于任何使用[通用主机](xref:fundamentals/host/generic-host)的 .NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="f179e-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="f179e-109">要通过示例了解如何在非 Web 控制台应用中使用一般主机，请参阅[后台任务示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) 的 Program.cs 文件 (<xref:fundamentals/host/hosted-services>)  。</span><span class="sxs-lookup"><span data-stu-id="f179e-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="f179e-110">对于没有通用主机的应用，日志记录代码在[添加提供程序](#add-providers)和[创建记录器](#create-logs)的方式上有所不同。</span><span class="sxs-lookup"><span data-stu-id="f179e-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="f179e-111">本文的这些部分显示了非主机代码示例。</span><span class="sxs-lookup"><span data-stu-id="f179e-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="f179e-112">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f179e-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="f179e-113">添加提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-113">Add providers</span></span>

<span data-ttu-id="f179e-114">日志记录提供程序会显示或存储日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="f179e-115">例如，控制台提供程序在控制台上显示日志，Azure Application Insights 提供程序会将这些日志存储在 Azure Application Insights 中。</span><span class="sxs-lookup"><span data-stu-id="f179e-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="f179e-116">可通过添加多个提供程序将日志发送到多个目标。</span><span class="sxs-lookup"><span data-stu-id="f179e-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="f179e-117">若要在使用通用主机的应用中添加提供程序，请在 Program.cs 中调用提供程序的 `Add{provider name}` 扩展方法  ：</span><span class="sxs-lookup"><span data-stu-id="f179e-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="f179e-118">在非主机控制台应用中，在创建 `LoggerFactory` 时调用提供程序的 `Add{provider name}` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="f179e-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="f179e-119">`LoggerFactory` 和 `AddConsole` 需要用于 `Microsoft.Extensions.Logging` 的 `using` 语句。</span><span class="sxs-lookup"><span data-stu-id="f179e-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="f179e-120">默认 ASP.NET Core 项目模板调用 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>，该操作将添加以下日志记录提供程序：</span><span class="sxs-lookup"><span data-stu-id="f179e-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="f179e-121">控制台</span><span class="sxs-lookup"><span data-stu-id="f179e-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="f179e-122">调试</span><span class="sxs-lookup"><span data-stu-id="f179e-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="f179e-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="f179e-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="f179e-124">[EventLog](#windows-eventlog-provider)（仅当在 Windows 上运行时）</span><span class="sxs-lookup"><span data-stu-id="f179e-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="f179e-125">可自行选择提供程序来替换默认提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="f179e-126">调用 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>，然后添加所需的提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="f179e-127">详细了解[内置日志记录提供程序](#built-in-logging-providers)，以及本文稍后部分介绍的[第三方日志记录提供程序](#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="f179e-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="f179e-128">创建日志</span><span class="sxs-lookup"><span data-stu-id="f179e-128">Create logs</span></span>

<span data-ttu-id="f179e-129">若要创建日志，请使用 <xref:Microsoft.Extensions.Logging.ILogger%601> 对象。</span><span class="sxs-lookup"><span data-stu-id="f179e-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="f179e-130">在 Web 应用或托管服务中，由依赖关系注入 (DI) 获取 `ILogger`。</span><span class="sxs-lookup"><span data-stu-id="f179e-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="f179e-131">在非主机控制台应用中，使用 `LoggerFactory` 来创建 `ILogger`。</span><span class="sxs-lookup"><span data-stu-id="f179e-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="f179e-132">下面的 ASP.NET Core 示例创建了一个以 `TodoApiSample.Pages.AboutModel` 为类别的记录器。</span><span class="sxs-lookup"><span data-stu-id="f179e-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="f179e-133">日志“类别”是与每个日志关联的字符串  。</span><span class="sxs-lookup"><span data-stu-id="f179e-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="f179e-134">DI 提供的 `ILogger<T>` 实例创建日志，该日志以类型 `T` 的完全限定名称为类别。</span><span class="sxs-lookup"><span data-stu-id="f179e-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="f179e-135">下面的非主机控制台应用示例创建了一个以 `LoggingConsoleApp.Program` 为类别的记录器。</span><span class="sxs-lookup"><span data-stu-id="f179e-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="f179e-136">在下面的 ASP.NET Core 和控制台应用示例中，记录器用于创建以 `Information` 为级别的日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="f179e-137">日志“级别”代表所记录事件的严重程度  。</span><span class="sxs-lookup"><span data-stu-id="f179e-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="f179e-138">本文稍后部分将更详细地介绍[级别](#log-level)和[类别](#log-category)。</span><span class="sxs-lookup"><span data-stu-id="f179e-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="f179e-139">在 Program 类中创建日志</span><span class="sxs-lookup"><span data-stu-id="f179e-139">Create logs in the Program class</span></span>

<span data-ttu-id="f179e-140">若要将日志写入 ASP.NET Core 应用的 `Program` 类中，请在生成主机后从 DI 获取 `ILogger`实例：</span><span class="sxs-lookup"><span data-stu-id="f179e-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="f179e-141">不直接支持在主机构造期间进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="f179e-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="f179e-142">但是，可以使用单独的记录器。</span><span class="sxs-lookup"><span data-stu-id="f179e-142">However, a separate logger can be used.</span></span> <span data-ttu-id="f179e-143">在以下示例中，[Serilog](https://serilog.net/) 记录器用于登录 `CreateHostBuilder`。</span><span class="sxs-lookup"><span data-stu-id="f179e-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="f179e-144">`AddSerilog` 使用 `Log.Logger` 中指定的静态配置：</span><span class="sxs-lookup"><span data-stu-id="f179e-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="f179e-145">在 Startup 类中创建日志</span><span class="sxs-lookup"><span data-stu-id="f179e-145">Create logs in the Startup class</span></span>

<span data-ttu-id="f179e-146">若要将日志写入 ASP.NET Core 应用的 `Startup.Configure` 方法中，请在方法签名中包含 `ILogger` 参数：</span><span class="sxs-lookup"><span data-stu-id="f179e-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="f179e-147">不支持在 `Startup.ConfigureServices` 方法中完成 DI 容器设置之前就写入日志：</span><span class="sxs-lookup"><span data-stu-id="f179e-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="f179e-148">不支持将记录器注入到 `Startup` 构造函数中。</span><span class="sxs-lookup"><span data-stu-id="f179e-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="f179e-149">不支持将记录器注入到 `Startup.ConfigureServices` 方法签名中</span><span class="sxs-lookup"><span data-stu-id="f179e-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="f179e-150">这一限制的原因是，日志记录依赖于 DI 和配置，而配置又依赖于 DI。</span><span class="sxs-lookup"><span data-stu-id="f179e-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="f179e-151">在完成 `ConfigureServices` 之前，不会设置 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="f179e-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="f179e-152">由于为 Web 主机创建了单独的 DI 容器，所以在 ASP.NET Core 的早期版本中，构造函数将记录器注入到 `Startup` 工作。</span><span class="sxs-lookup"><span data-stu-id="f179e-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="f179e-153">若要了解为何仅为通用主机创建一个容器，请参阅[重大更改公告](https://github.com/aspnet/Announcements/issues/353)。</span><span class="sxs-lookup"><span data-stu-id="f179e-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="f179e-154">如果需要配置依赖于 `ILogger<T>` 的服务，则仍可通过使用构造函数注入或提供工厂方法的方式来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="f179e-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="f179e-155">只有在没有其他选择的情况下，才建议使用工厂方法。</span><span class="sxs-lookup"><span data-stu-id="f179e-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="f179e-156">例如，假设你需要由 DI 为服务填充属性：</span><span class="sxs-lookup"><span data-stu-id="f179e-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="f179e-157">前面突出显示的代码是 `Func`，该代码在 DI 容器第一次需要构造 `MyService` 实例时运行。</span><span class="sxs-lookup"><span data-stu-id="f179e-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="f179e-158">可以用这种方式访问任何已注册的服务。</span><span class="sxs-lookup"><span data-stu-id="f179e-158">You can access any of the registered services in this way.</span></span>

### <a name="create-logs-in-blazor"></a><span data-ttu-id="f179e-159">在 Blazor 中创建日志</span><span class="sxs-lookup"><span data-stu-id="f179e-159">Create logs in Blazor</span></span>

#### <a name="blazor-webassembly"></a><span data-ttu-id="f179e-160">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="f179e-160">Blazor WebAssembly</span></span>

<span data-ttu-id="f179e-161">使用 `Program.Main` 中的 `WebAssemblyHostBuilder.Logging` 属性在 Blazor WebAssembly 应用中创建日志：</span><span class="sxs-lookup"><span data-stu-id="f179e-161">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="f179e-162">`Logging` 属性的类型为 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>，因此可在 <xref:Microsoft.Extensions.Logging.ILoggingBuilder> 上使用的所有扩展方法也可在 `Logging` 上使用。</span><span class="sxs-lookup"><span data-stu-id="f179e-162">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

#### <a name="log-in-razor-components"></a><span data-ttu-id="f179e-163">登录 Razor 组件</span><span class="sxs-lookup"><span data-stu-id="f179e-163">Log in Razor components</span></span>

<span data-ttu-id="f179e-164">记录器会采用应用启动配置。</span><span class="sxs-lookup"><span data-stu-id="f179e-164">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="f179e-165">要支持对 API 使用 IntelliSense 完成项（例如 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> 和 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>），需具备 <xref:Microsoft.Extensions.Logging> 的 `using` 指令。</span><span class="sxs-lookup"><span data-stu-id="f179e-165">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="f179e-166">下面的示例演示了如何使用 Razor 组件中的 <xref:Microsoft.Extensions.Logging.ILogger> 进行日志记录：</span><span class="sxs-lookup"><span data-stu-id="f179e-166">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

<span data-ttu-id="f179e-167">下面的示例演示了如何使用 Razor 组件中的 <xref:Microsoft.Extensions.Logging.ILoggerFactory> 进行日志记录：</span><span class="sxs-lookup"><span data-stu-id="f179e-167">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="f179e-168">没有异步记录器方法</span><span class="sxs-lookup"><span data-stu-id="f179e-168">No asynchronous logger methods</span></span>

<span data-ttu-id="f179e-169">日志记录应该会很快，不值得牺牲性能来使用异步代码。</span><span class="sxs-lookup"><span data-stu-id="f179e-169">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="f179e-170">如果你的日志数据存储很慢，请不要直接写入它。</span><span class="sxs-lookup"><span data-stu-id="f179e-170">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="f179e-171">首先考虑将日志消息写入快速存储，稍后再将其变为慢速存储。</span><span class="sxs-lookup"><span data-stu-id="f179e-171">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="f179e-172">例如，如果你要记录到 SQL Server，你可能不想直接在 `Log` 方法中记录，因为 `Log` 方法是同步的。</span><span class="sxs-lookup"><span data-stu-id="f179e-172">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="f179e-173">相反，你会将日志消息同步添加到内存中的队列，并让后台辅助线程从队列中拉出消息，以完成将数据推送到 SQL Server 的异步工作。</span><span class="sxs-lookup"><span data-stu-id="f179e-173">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="f179e-174">有关详细信息，请参阅[此](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub 问题。</span><span class="sxs-lookup"><span data-stu-id="f179e-174">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="f179e-175">Configuration</span><span class="sxs-lookup"><span data-stu-id="f179e-175">Configuration</span></span>

<span data-ttu-id="f179e-176">日志记录提供程序配置由一个或多个配置提供程序提供：</span><span class="sxs-lookup"><span data-stu-id="f179e-176">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="f179e-177">文件格式（INI、JSON 和 XML）。</span><span class="sxs-lookup"><span data-stu-id="f179e-177">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="f179e-178">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="f179e-178">Command-line arguments.</span></span>
* <span data-ttu-id="f179e-179">环境变量。</span><span class="sxs-lookup"><span data-stu-id="f179e-179">Environment variables.</span></span>
* <span data-ttu-id="f179e-180">内存中的 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="f179e-180">In-memory .NET objects.</span></span>
* <span data-ttu-id="f179e-181">未加密的[机密管理器](xref:security/app-secrets)存储。</span><span class="sxs-lookup"><span data-stu-id="f179e-181">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="f179e-182">加密的用户存储，如 [Azure Key Vault](xref:security/key-vault-configuration)。</span><span class="sxs-lookup"><span data-stu-id="f179e-182">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="f179e-183">（已安装或已创建的）自定义提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-183">Custom providers (installed or created).</span></span>

<span data-ttu-id="f179e-184">例如，日志记录配置通常由应用设置文件的 `Logging` 部分提供。</span><span class="sxs-lookup"><span data-stu-id="f179e-184">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="f179e-185">以下示例显示了典型 *appsettings.Development.json* 文件的内容：</span><span class="sxs-lookup"><span data-stu-id="f179e-185">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="f179e-186">`Logging` 属性可具有 `LogLevel` 和日志提供程序属性（显示控制台）。</span><span class="sxs-lookup"><span data-stu-id="f179e-186">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="f179e-187">`Logging` 下的 `LogLevel` 属性指定了用于记录所选类别的最低[级别](#log-level)。</span><span class="sxs-lookup"><span data-stu-id="f179e-187">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="f179e-188">在本例中，`System` 和 `Microsoft` 类别在 `Information` 级别记录，其他均在 `Debug` 级别记录。</span><span class="sxs-lookup"><span data-stu-id="f179e-188">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="f179e-189">`Logging` 下的其他属性均指定了日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-189">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="f179e-190">本示例针对控制台提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-190">The example is for the Console provider.</span></span> <span data-ttu-id="f179e-191">如果提供程序支持[日志作用域](#log-scopes)，则 `IncludeScopes` 将指示是否启用这些域。</span><span class="sxs-lookup"><span data-stu-id="f179e-191">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="f179e-192">提供程序属性（例如本例的 `Console`）也可指定 `LogLevel` 属性。</span><span class="sxs-lookup"><span data-stu-id="f179e-192">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="f179e-193">提供程序下的 `LogLevel` 指定了该提供程序记录的级别。</span><span class="sxs-lookup"><span data-stu-id="f179e-193">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="f179e-194">如果在 `Logging.{providername}.LogLevel` 中指定了级别，则这些级别将重写 `Logging.LogLevel` 中设置的所有内容。</span><span class="sxs-lookup"><span data-stu-id="f179e-194">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="f179e-195">不可使用日志记录 API 在应用运行时更改日志记录。</span><span class="sxs-lookup"><span data-stu-id="f179e-195">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="f179e-196">但是，一些配置提供程序可重新加载配置，这将对日志记录配置立即产生影响。</span><span class="sxs-lookup"><span data-stu-id="f179e-196">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="f179e-197">例如，[文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)会默认重新加载日志记录配置，该程序由 `CreateDefaultBuilder` 添加用来读取设置文件。</span><span class="sxs-lookup"><span data-stu-id="f179e-197">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="f179e-198">如果在应用运行时在代码中更改了配置，则该应用可调用 [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) 来更新应用的日志记录配置。</span><span class="sxs-lookup"><span data-stu-id="f179e-198">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="f179e-199">若要了解如何实现配置提供程序，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="f179e-199">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="f179e-200">日志记录输出示例</span><span class="sxs-lookup"><span data-stu-id="f179e-200">Sample logging output</span></span>

<span data-ttu-id="f179e-201">使用上一部分中显示的示例代码从命令行运行应用时，将在控制台中看到日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-201">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="f179e-202">以下是控制台输出示例：</span><span class="sxs-lookup"><span data-stu-id="f179e-202">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

<span data-ttu-id="f179e-203">通过向 `http://localhost:5000/api/todo/0` 处的示例应用发出 HTTP Get 请求来生成前述日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-203">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="f179e-204">在 Visual Studio 中运行示例应用时，“调试”窗口中将显示如下日志：</span><span class="sxs-lookup"><span data-stu-id="f179e-204">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

<span data-ttu-id="f179e-205">由上一部分所示的 `ILogger` 调用创建的日志以“TodoApiSample”开头。</span><span class="sxs-lookup"><span data-stu-id="f179e-205">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="f179e-206">以“Microsoft”类别开头的日志来自 ASP.NET Core 框架代码。</span><span class="sxs-lookup"><span data-stu-id="f179e-206">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="f179e-207">ASP.NET Core 和应用程序代码使用相同的日志记录 API 和提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-207">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="f179e-208">本文余下部分将介绍有关日志记录的某些详细信息及选项。</span><span class="sxs-lookup"><span data-stu-id="f179e-208">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="f179e-209">NuGet 包</span><span class="sxs-lookup"><span data-stu-id="f179e-209">NuGet packages</span></span>

<span data-ttu-id="f179e-210">`ILogger` 和 `ILoggerFactory` 接口位于 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 中，其默认实现位于 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 中。</span><span class="sxs-lookup"><span data-stu-id="f179e-210">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="f179e-211">日志类别</span><span class="sxs-lookup"><span data-stu-id="f179e-211">Log category</span></span>

<span data-ttu-id="f179e-212">创建 `ILogger` 对象后，将为其指定“类别”  。</span><span class="sxs-lookup"><span data-stu-id="f179e-212">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="f179e-213">该类别包含在由此 `ILogger` 实例创建的每条日志消息中。</span><span class="sxs-lookup"><span data-stu-id="f179e-213">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="f179e-214">类别可以是任何字符串，但约定需使用类名，例如“TodoApi.Controllers.TodoController”。</span><span class="sxs-lookup"><span data-stu-id="f179e-214">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="f179e-215">使用 `ILogger<T>` 获取一个 `ILogger` 实例，该实例使用 `T` 的完全限定类型名称作为类别：</span><span class="sxs-lookup"><span data-stu-id="f179e-215">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="f179e-216">要显式指定类别，请调用 `ILoggerFactory.CreateLogger`：</span><span class="sxs-lookup"><span data-stu-id="f179e-216">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="f179e-217">`ILogger<T>` 相当于使用 `T` 的完全限定类型名称来调用 `CreateLogger`。</span><span class="sxs-lookup"><span data-stu-id="f179e-217">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="f179e-218">日志级别</span><span class="sxs-lookup"><span data-stu-id="f179e-218">Log level</span></span>

<span data-ttu-id="f179e-219">每个日志都指定了一个 <xref:Microsoft.Extensions.Logging.LogLevel> 值。</span><span class="sxs-lookup"><span data-stu-id="f179e-219">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="f179e-220">日志级别指示严重性或重要程度。</span><span class="sxs-lookup"><span data-stu-id="f179e-220">The log level indicates the severity or importance.</span></span> <span data-ttu-id="f179e-221">例如，可在方法正常结束时写入 `Information` 日志，在方法返回“404 找不到”状态代码时写入 `Warning` 日志  。</span><span class="sxs-lookup"><span data-stu-id="f179e-221">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="f179e-222">下面的代码会创建 `Information` 和 `Warning` 日志：</span><span class="sxs-lookup"><span data-stu-id="f179e-222">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="f179e-223">在上述代码中，第一个参数是[日志事件 ID](#log-event-id)。</span><span class="sxs-lookup"><span data-stu-id="f179e-223">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="f179e-224">第二个参数是消息模板，其中的占位符用于填写剩余方法形参提供的实参值。</span><span class="sxs-lookup"><span data-stu-id="f179e-224">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="f179e-225">稍后将在本文的[消息模板部分](#log-message-template)介绍方法参数。</span><span class="sxs-lookup"><span data-stu-id="f179e-225">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="f179e-226">在方法名称中包含级别的日志方法（例如 `LogInformation` 和 `LogWarning`）是 [ILogger 的扩展方法](xref:Microsoft.Extensions.Logging.LoggerExtensions)。</span><span class="sxs-lookup"><span data-stu-id="f179e-226">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="f179e-227">这些方法会调用可接受 `LogLevel` 参数的 `Log` 方法。</span><span class="sxs-lookup"><span data-stu-id="f179e-227">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="f179e-228">可直接调用 `Log` 方法而不调用其中某个扩展方法，但语法相对复杂。</span><span class="sxs-lookup"><span data-stu-id="f179e-228">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="f179e-229">有关详细信息，请参阅 <xref:Microsoft.Extensions.Logging.ILogger> 和[记录器扩展源代码](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)。</span><span class="sxs-lookup"><span data-stu-id="f179e-229">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="f179e-230">ASP.NET Core 定义了以下日志级别（按严重性从低到高排列）。</span><span class="sxs-lookup"><span data-stu-id="f179e-230">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="f179e-231">跟踪 = 0</span><span class="sxs-lookup"><span data-stu-id="f179e-231">Trace = 0</span></span>

  <span data-ttu-id="f179e-232">有关通常仅用于调试的信息。</span><span class="sxs-lookup"><span data-stu-id="f179e-232">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="f179e-233">这些消息可能包含敏感应用程序数据，因此不得在生产环境中启用它们。</span><span class="sxs-lookup"><span data-stu-id="f179e-233">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="f179e-234">默认情况下禁用。 </span><span class="sxs-lookup"><span data-stu-id="f179e-234">*Disabled by default.*</span></span>

* <span data-ttu-id="f179e-235">调试 = 1</span><span class="sxs-lookup"><span data-stu-id="f179e-235">Debug = 1</span></span>

  <span data-ttu-id="f179e-236">有关在开发和调试中可能有用的信息。</span><span class="sxs-lookup"><span data-stu-id="f179e-236">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="f179e-237">示例：`Entering method Configure with flag set to true.` 由于日志数量过多，因此仅当执行故障排除时，才在生产中启用 `Debug` 级别日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-237">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="f179e-238">信息 = 2</span><span class="sxs-lookup"><span data-stu-id="f179e-238">Information = 2</span></span>

  <span data-ttu-id="f179e-239">用于跟踪应用的常规流。</span><span class="sxs-lookup"><span data-stu-id="f179e-239">For tracking the general flow of the app.</span></span> <span data-ttu-id="f179e-240">这些日志通常有长期价值。</span><span class="sxs-lookup"><span data-stu-id="f179e-240">These logs typically have some long-term value.</span></span> <span data-ttu-id="f179e-241">示例：`Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="f179e-241">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="f179e-242">警告 = 3</span><span class="sxs-lookup"><span data-stu-id="f179e-242">Warning = 3</span></span>

  <span data-ttu-id="f179e-243">表示应用流中的异常或意外事件。</span><span class="sxs-lookup"><span data-stu-id="f179e-243">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="f179e-244">可能包括不会中断应用运行但仍需调查的错误或其他条件。</span><span class="sxs-lookup"><span data-stu-id="f179e-244">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="f179e-245">`Warning` 日志级别常用于已处理的异常。</span><span class="sxs-lookup"><span data-stu-id="f179e-245">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="f179e-246">示例：`FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="f179e-246">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="f179e-247">错误 = 4</span><span class="sxs-lookup"><span data-stu-id="f179e-247">Error = 4</span></span>

  <span data-ttu-id="f179e-248">表示无法处理的错误和异常。</span><span class="sxs-lookup"><span data-stu-id="f179e-248">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="f179e-249">这些消息指示的是当前活动或操作（例如当前 HTTP 请求）中的失败，而不是整个应用中的失败。</span><span class="sxs-lookup"><span data-stu-id="f179e-249">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="f179e-250">日志消息示例：`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="f179e-250">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="f179e-251">严重 = 5</span><span class="sxs-lookup"><span data-stu-id="f179e-251">Critical = 5</span></span>

  <span data-ttu-id="f179e-252">需要立即关注的失败。</span><span class="sxs-lookup"><span data-stu-id="f179e-252">For failures that require immediate attention.</span></span> <span data-ttu-id="f179e-253">例如数据丢失、磁盘空间不足。</span><span class="sxs-lookup"><span data-stu-id="f179e-253">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="f179e-254">使用日志级别控制写入到特定存储介质或显示窗口的日志输出量。</span><span class="sxs-lookup"><span data-stu-id="f179e-254">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="f179e-255">例如：</span><span class="sxs-lookup"><span data-stu-id="f179e-255">For example:</span></span>

* <span data-ttu-id="f179e-256">生产中：</span><span class="sxs-lookup"><span data-stu-id="f179e-256">In production:</span></span>
  * <span data-ttu-id="f179e-257">如果通过 `Information` 级别在 `Trace` 处记录，则会生成大量详细的日志消息。</span><span class="sxs-lookup"><span data-stu-id="f179e-257">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="f179e-258">为控制成本且不超过数据存储限制，请通过 `Information` 级别消息将 `Trace` 记录到容量大、成本低的数据存储中。</span><span class="sxs-lookup"><span data-stu-id="f179e-258">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="f179e-259">如果通过 `Critical` 级别在 `Warning` 处记录，通常生成的日志消息更少且更小。</span><span class="sxs-lookup"><span data-stu-id="f179e-259">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="f179e-260">因此，成本和存储限制通常不是问题，而这使得在选择数据信息时更为灵活。</span><span class="sxs-lookup"><span data-stu-id="f179e-260">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="f179e-261">在开发过程中：</span><span class="sxs-lookup"><span data-stu-id="f179e-261">During development:</span></span>
  * <span data-ttu-id="f179e-262">通过 `Critical` 消息将 `Warning` 记录到控制台。</span><span class="sxs-lookup"><span data-stu-id="f179e-262">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="f179e-263">在疑难解答时通过 `Information` 消息添加 `Trace`。</span><span class="sxs-lookup"><span data-stu-id="f179e-263">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="f179e-264">本文稍后的[日志筛选](#log-filtering)部分介绍如何控制提供程序处理的日志级别。</span><span class="sxs-lookup"><span data-stu-id="f179e-264">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="f179e-265">ASP.NET Core 为框架事件写入日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-265">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="f179e-266">本文前面部分提供的日志示例排除了低于 `Information` 级别的日志，因此未创建 `Debug` 或 `Trace` 级别日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-266">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="f179e-267">以下示例介绍了通过运行配置为显示 `Debug` 日志的示例应用而生成的控制台日志：</span><span class="sxs-lookup"><span data-stu-id="f179e-267">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="f179e-268">日志事件 ID</span><span class="sxs-lookup"><span data-stu-id="f179e-268">Log event ID</span></span>

<span data-ttu-id="f179e-269">每个日志都可指定一个事件 ID  。</span><span class="sxs-lookup"><span data-stu-id="f179e-269">Each log can specify an *event ID*.</span></span> <span data-ttu-id="f179e-270">该示例应用通过使用本地定义的 `LoggingEvents` 类来执行此操作：</span><span class="sxs-lookup"><span data-stu-id="f179e-270">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="f179e-271">事件 ID 与一组事件相关联。</span><span class="sxs-lookup"><span data-stu-id="f179e-271">An event ID associates a set of events.</span></span> <span data-ttu-id="f179e-272">例如，与在页面上显示项列表相关的所有日志可能是 1001。</span><span class="sxs-lookup"><span data-stu-id="f179e-272">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="f179e-273">日志记录提供程序可将事件 ID 存储在 ID 字段中，存储在日志记录消息中，或者不进行存储。</span><span class="sxs-lookup"><span data-stu-id="f179e-273">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="f179e-274">调试提供程序不显示事件 ID。</span><span class="sxs-lookup"><span data-stu-id="f179e-274">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="f179e-275">控制台提供程序在类别后的括号中显示事件 ID：</span><span class="sxs-lookup"><span data-stu-id="f179e-275">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="f179e-276">日志消息模板</span><span class="sxs-lookup"><span data-stu-id="f179e-276">Log message template</span></span>

<span data-ttu-id="f179e-277">每个日志都会指定一个消息模板。</span><span class="sxs-lookup"><span data-stu-id="f179e-277">Each log specifies a message template.</span></span> <span data-ttu-id="f179e-278">消息模板可包含要填写参数的占位符。</span><span class="sxs-lookup"><span data-stu-id="f179e-278">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="f179e-279">请在占位符中使用名称而不是数字。</span><span class="sxs-lookup"><span data-stu-id="f179e-279">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="f179e-280">占位符的顺序（而非其名称）决定了为其提供值的参数。</span><span class="sxs-lookup"><span data-stu-id="f179e-280">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="f179e-281">在以下代码中，请注意消息模板中的参数名称未按顺序排列：</span><span class="sxs-lookup"><span data-stu-id="f179e-281">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="f179e-282">此代码创建了一个参数值按顺序排列的日志消息：</span><span class="sxs-lookup"><span data-stu-id="f179e-282">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="f179e-283">日志记录框架按此方式工作，这样日志记录提供程序即可实现[语义日志记录，也称为结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="f179e-283">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="f179e-284">参数本身会传递给日志记录系统，而不仅仅是格式化的消息模板。</span><span class="sxs-lookup"><span data-stu-id="f179e-284">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="f179e-285">通过此信息，日志记录提供程序能够将参数值存储为字段。</span><span class="sxs-lookup"><span data-stu-id="f179e-285">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="f179e-286">例如，假设记录器方法调用如下所示：</span><span class="sxs-lookup"><span data-stu-id="f179e-286">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="f179e-287">如果要将日志发送到 Azure 表存储，则每个 Azure 表实体都可具有 `ID` 和 `RequestTime` 属性，这简化了对日志数据的查询。</span><span class="sxs-lookup"><span data-stu-id="f179e-287">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="f179e-288">无需分析文本消息的超时，查询即可找到特定 `RequestTime` 范围内的全部日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-288">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="f179e-289">日志记录异常</span><span class="sxs-lookup"><span data-stu-id="f179e-289">Logging exceptions</span></span>

<span data-ttu-id="f179e-290">记录器方法有可传入异常的重载，如下方示例所示：</span><span class="sxs-lookup"><span data-stu-id="f179e-290">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="f179e-291">不同的提供程序处理异常信息的方式不同。</span><span class="sxs-lookup"><span data-stu-id="f179e-291">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="f179e-292">以下是上示代码的调试提供程序输出示例。</span><span class="sxs-lookup"><span data-stu-id="f179e-292">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="f179e-293">日志筛选</span><span class="sxs-lookup"><span data-stu-id="f179e-293">Log filtering</span></span>

<span data-ttu-id="f179e-294">可为特定或所有提供程序和类别指定最低日志级别。</span><span class="sxs-lookup"><span data-stu-id="f179e-294">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="f179e-295">最低级别以下的日志不会传递给该提供程序，因此不会显示或存储它们。</span><span class="sxs-lookup"><span data-stu-id="f179e-295">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="f179e-296">要禁止显示所有日志，可将 `LogLevel.None` 指定为最低日志级别。</span><span class="sxs-lookup"><span data-stu-id="f179e-296">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="f179e-297">`LogLevel.None` 的整数值为 6，它大于 `LogLevel.Critical` (5)。</span><span class="sxs-lookup"><span data-stu-id="f179e-297">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="f179e-298">在配置中创建筛选规则</span><span class="sxs-lookup"><span data-stu-id="f179e-298">Create filter rules in configuration</span></span>

<span data-ttu-id="f179e-299">项目模板代码调用 `CreateDefaultBuilder` 来为 Console、Debug 和 EventSource（ASP.NET Core 2.2 或更高版本）提供程序设置日志记录。</span><span class="sxs-lookup"><span data-stu-id="f179e-299">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="f179e-300">正如[本文前面部分](#configuration)所述，`CreateDefaultBuilder` 方法设置日志记录以在 `Logging` 部分查找配置。</span><span class="sxs-lookup"><span data-stu-id="f179e-300">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="f179e-301">配置数据按提供程序和类别指定最低日志级别，如下方示例所示：</span><span class="sxs-lookup"><span data-stu-id="f179e-301">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="f179e-302">此 JSON 将创建 6 条筛选规则：1 条用于调试提供程序， 4 条用于控制台提供程序， 1 条用于所有提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-302">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="f179e-303">创建 `ILogger` 对象时，为每个提供程序选择一个规则。</span><span class="sxs-lookup"><span data-stu-id="f179e-303">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="f179e-304">代码中的筛选规则</span><span class="sxs-lookup"><span data-stu-id="f179e-304">Filter rules in code</span></span>

<span data-ttu-id="f179e-305">下面的示例演示了如何在代码中注册筛选规则：</span><span class="sxs-lookup"><span data-stu-id="f179e-305">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="f179e-306">第二个 `AddFilter` 使用类型名称来指定调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-306">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="f179e-307">第一个 `AddFilter` 应用于全部提供程序，因为它未指定提供程序类型。</span><span class="sxs-lookup"><span data-stu-id="f179e-307">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="f179e-308">如何应用筛选规则</span><span class="sxs-lookup"><span data-stu-id="f179e-308">How filtering rules are applied</span></span>

<span data-ttu-id="f179e-309">先前示例中显示的配置数据和 `AddFilter` 代码会创建下表所示的规则。</span><span class="sxs-lookup"><span data-stu-id="f179e-309">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="f179e-310">前六条由配置示例创建，后两条由代码示例创建。</span><span class="sxs-lookup"><span data-stu-id="f179e-310">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="f179e-311">数字</span><span class="sxs-lookup"><span data-stu-id="f179e-311">Number</span></span> | <span data-ttu-id="f179e-312">提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-312">Provider</span></span>      | <span data-ttu-id="f179e-313">类别的开头为...</span><span class="sxs-lookup"><span data-stu-id="f179e-313">Categories that begin with ...</span></span>          | <span data-ttu-id="f179e-314">最低日志级别</span><span class="sxs-lookup"><span data-stu-id="f179e-314">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="f179e-315">1</span><span class="sxs-lookup"><span data-stu-id="f179e-315">1</span></span>      | <span data-ttu-id="f179e-316">调试</span><span class="sxs-lookup"><span data-stu-id="f179e-316">Debug</span></span>         | <span data-ttu-id="f179e-317">全部类别</span><span class="sxs-lookup"><span data-stu-id="f179e-317">All categories</span></span>                          | <span data-ttu-id="f179e-318">信息</span><span class="sxs-lookup"><span data-stu-id="f179e-318">Information</span></span>       |
| <span data-ttu-id="f179e-319">2</span><span class="sxs-lookup"><span data-stu-id="f179e-319">2</span></span>      | <span data-ttu-id="f179e-320">控制台</span><span class="sxs-lookup"><span data-stu-id="f179e-320">Console</span></span>       | <span data-ttu-id="f179e-321">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="f179e-321">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="f179e-322">警告</span><span class="sxs-lookup"><span data-stu-id="f179e-322">Warning</span></span>           |
| <span data-ttu-id="f179e-323">3</span><span class="sxs-lookup"><span data-stu-id="f179e-323">3</span></span>      | <span data-ttu-id="f179e-324">控制台</span><span class="sxs-lookup"><span data-stu-id="f179e-324">Console</span></span>       | <span data-ttu-id="f179e-325">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="f179e-325">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="f179e-326">调试</span><span class="sxs-lookup"><span data-stu-id="f179e-326">Debug</span></span>             |
| <span data-ttu-id="f179e-327">4</span><span class="sxs-lookup"><span data-stu-id="f179e-327">4</span></span>      | <span data-ttu-id="f179e-328">控制台</span><span class="sxs-lookup"><span data-stu-id="f179e-328">Console</span></span>       | <span data-ttu-id="f179e-329">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="f179e-329">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="f179e-330">错误</span><span class="sxs-lookup"><span data-stu-id="f179e-330">Error</span></span>             |
| <span data-ttu-id="f179e-331">5</span><span class="sxs-lookup"><span data-stu-id="f179e-331">5</span></span>      | <span data-ttu-id="f179e-332">控制台</span><span class="sxs-lookup"><span data-stu-id="f179e-332">Console</span></span>       | <span data-ttu-id="f179e-333">全部类别</span><span class="sxs-lookup"><span data-stu-id="f179e-333">All categories</span></span>                          | <span data-ttu-id="f179e-334">信息</span><span class="sxs-lookup"><span data-stu-id="f179e-334">Information</span></span>       |
| <span data-ttu-id="f179e-335">6</span><span class="sxs-lookup"><span data-stu-id="f179e-335">6</span></span>      | <span data-ttu-id="f179e-336">全部提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-336">All providers</span></span> | <span data-ttu-id="f179e-337">全部类别</span><span class="sxs-lookup"><span data-stu-id="f179e-337">All categories</span></span>                          | <span data-ttu-id="f179e-338">调试</span><span class="sxs-lookup"><span data-stu-id="f179e-338">Debug</span></span>             |
| <span data-ttu-id="f179e-339">7</span><span class="sxs-lookup"><span data-stu-id="f179e-339">7</span></span>      | <span data-ttu-id="f179e-340">全部提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-340">All providers</span></span> | <span data-ttu-id="f179e-341">System</span><span class="sxs-lookup"><span data-stu-id="f179e-341">System</span></span>                                  | <span data-ttu-id="f179e-342">调试</span><span class="sxs-lookup"><span data-stu-id="f179e-342">Debug</span></span>             |
| <span data-ttu-id="f179e-343">8</span><span class="sxs-lookup"><span data-stu-id="f179e-343">8</span></span>      | <span data-ttu-id="f179e-344">调试</span><span class="sxs-lookup"><span data-stu-id="f179e-344">Debug</span></span>         | <span data-ttu-id="f179e-345">Microsoft</span><span class="sxs-lookup"><span data-stu-id="f179e-345">Microsoft</span></span>                               | <span data-ttu-id="f179e-346">跟踪</span><span class="sxs-lookup"><span data-stu-id="f179e-346">Trace</span></span>             |

<span data-ttu-id="f179e-347">创建 `ILogger` 对象时，`ILoggerFactory` 对象将根据提供程序选择一条规则，将其应用于该记录器。</span><span class="sxs-lookup"><span data-stu-id="f179e-347">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="f179e-348">将按所选规则筛选 `ILogger` 实例写入的所有消息。</span><span class="sxs-lookup"><span data-stu-id="f179e-348">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="f179e-349">从可用规则中为每个提供程序和类别对选择最具体的规则。</span><span class="sxs-lookup"><span data-stu-id="f179e-349">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="f179e-350">在为给定的类别创建 `ILogger` 时，以下算法将用于每个提供程序：</span><span class="sxs-lookup"><span data-stu-id="f179e-350">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="f179e-351">选择匹配提供程序或其别名的所有规则。</span><span class="sxs-lookup"><span data-stu-id="f179e-351">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="f179e-352">如果找不到任何匹配项，则选择提供程序为空的所有规则。</span><span class="sxs-lookup"><span data-stu-id="f179e-352">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="f179e-353">根据上一步的结果，选择具有最长匹配类别前缀的规则。</span><span class="sxs-lookup"><span data-stu-id="f179e-353">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="f179e-354">如果找不到任何匹配项，则选择未指定类别的所有规则。</span><span class="sxs-lookup"><span data-stu-id="f179e-354">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="f179e-355">如果选择了多条规则，则采用最后一条  。</span><span class="sxs-lookup"><span data-stu-id="f179e-355">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="f179e-356">如果未选择任何规则，则使用 `MinimumLevel`。</span><span class="sxs-lookup"><span data-stu-id="f179e-356">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="f179e-357">假设你使用上述规则列表为类别“Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine”创建了 `ILogger` 对象：</span><span class="sxs-lookup"><span data-stu-id="f179e-357">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="f179e-358">对于调试提供程序，规则 1、6 和 8 适用。</span><span class="sxs-lookup"><span data-stu-id="f179e-358">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="f179e-359">规则 8 最为具体，因此选择了它。</span><span class="sxs-lookup"><span data-stu-id="f179e-359">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="f179e-360">对于控制台提供程序，符合的有规则 3、规则 4、规则 5 和规则 6。</span><span class="sxs-lookup"><span data-stu-id="f179e-360">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="f179e-361">规则 3 最为具体。</span><span class="sxs-lookup"><span data-stu-id="f179e-361">Rule 3 is most specific.</span></span>

<span data-ttu-id="f179e-362">生成的 `ILogger` 实例将 `Trace` 级别及更高级别的日志发送到调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-362">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="f179e-363">`Debug` 级别及更高级别的日志会发送到控制台提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-363">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="f179e-364">提供程序别名</span><span class="sxs-lookup"><span data-stu-id="f179e-364">Provider aliases</span></span>

<span data-ttu-id="f179e-365">每个提供程序都定义了一个别名；可在配置中使用该别名来代替完全限定的类型名称  。</span><span class="sxs-lookup"><span data-stu-id="f179e-365">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="f179e-366">对于内置提供程序，请使用以下别名：</span><span class="sxs-lookup"><span data-stu-id="f179e-366">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="f179e-367">控制台</span><span class="sxs-lookup"><span data-stu-id="f179e-367">Console</span></span>
* <span data-ttu-id="f179e-368">调试</span><span class="sxs-lookup"><span data-stu-id="f179e-368">Debug</span></span>
* <span data-ttu-id="f179e-369">EventSource</span><span class="sxs-lookup"><span data-stu-id="f179e-369">EventSource</span></span>
* <span data-ttu-id="f179e-370">EventLog</span><span class="sxs-lookup"><span data-stu-id="f179e-370">EventLog</span></span>
* <span data-ttu-id="f179e-371">TraceSource</span><span class="sxs-lookup"><span data-stu-id="f179e-371">TraceSource</span></span>
* <span data-ttu-id="f179e-372">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="f179e-372">AzureAppServicesFile</span></span>
* <span data-ttu-id="f179e-373">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="f179e-373">AzureAppServicesBlob</span></span>
* <span data-ttu-id="f179e-374">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="f179e-374">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="f179e-375">默认最低级别</span><span class="sxs-lookup"><span data-stu-id="f179e-375">Default minimum level</span></span>

<span data-ttu-id="f179e-376">仅当配置或代码中的规则对给定提供程序和类别都不适用时，最低级别设置才会生效。</span><span class="sxs-lookup"><span data-stu-id="f179e-376">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="f179e-377">下面的示例演示如何设置最低级别：</span><span class="sxs-lookup"><span data-stu-id="f179e-377">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="f179e-378">如果没有明确设置最低级别，则默认值为 `Information`，它表示 `Trace` 和 `Debug` 日志将被忽略。</span><span class="sxs-lookup"><span data-stu-id="f179e-378">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="f179e-379">筛选器函数</span><span class="sxs-lookup"><span data-stu-id="f179e-379">Filter functions</span></span>

<span data-ttu-id="f179e-380">对配置或代码没有向其分配规则的所有提供程序和类别调用筛选器函数。</span><span class="sxs-lookup"><span data-stu-id="f179e-380">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="f179e-381">函数中的代码可访问提供程序类型、类别和日志级别。</span><span class="sxs-lookup"><span data-stu-id="f179e-381">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="f179e-382">例如：</span><span class="sxs-lookup"><span data-stu-id="f179e-382">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="f179e-383">系统类别和级别</span><span class="sxs-lookup"><span data-stu-id="f179e-383">System categories and levels</span></span>

<span data-ttu-id="f179e-384">下面是 ASP.NET Core 和 Entity Framework Core 使用的一些类别，备注中说明了可从这些类别获取的具体日志：</span><span class="sxs-lookup"><span data-stu-id="f179e-384">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="f179e-385">类别</span><span class="sxs-lookup"><span data-stu-id="f179e-385">Category</span></span>                            | <span data-ttu-id="f179e-386">说明</span><span class="sxs-lookup"><span data-stu-id="f179e-386">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="f179e-387">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="f179e-387">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="f179e-388">常规 ASP.NET Core 诊断。</span><span class="sxs-lookup"><span data-stu-id="f179e-388">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="f179e-389">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="f179e-389">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="f179e-390">考虑、找到并使用了哪些密钥。</span><span class="sxs-lookup"><span data-stu-id="f179e-390">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="f179e-391">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="f179e-391">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="f179e-392">所允许的主机。</span><span class="sxs-lookup"><span data-stu-id="f179e-392">Hosts allowed.</span></span> |
| <span data-ttu-id="f179e-393">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="f179e-393">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="f179e-394">HTTP 请求完成的时间和启动时间。</span><span class="sxs-lookup"><span data-stu-id="f179e-394">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="f179e-395">加载了哪些承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="f179e-395">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="f179e-396">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="f179e-396">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="f179e-397">MVC 和 Razor 诊断。</span><span class="sxs-lookup"><span data-stu-id="f179e-397">MVC and Razor diagnostics.</span></span> <span data-ttu-id="f179e-398">模型绑定、筛选器执行、视图编译和操作选择。</span><span class="sxs-lookup"><span data-stu-id="f179e-398">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="f179e-399">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="f179e-399">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="f179e-400">路由匹配信息。</span><span class="sxs-lookup"><span data-stu-id="f179e-400">Route matching information.</span></span> |
| <span data-ttu-id="f179e-401">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="f179e-401">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="f179e-402">连接启动、停止和保持活动响应。</span><span class="sxs-lookup"><span data-stu-id="f179e-402">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="f179e-403">HTTP 证书信息。</span><span class="sxs-lookup"><span data-stu-id="f179e-403">HTTPS certificate information.</span></span> |
| <span data-ttu-id="f179e-404">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="f179e-404">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="f179e-405">提供的文件。</span><span class="sxs-lookup"><span data-stu-id="f179e-405">Files served.</span></span> |
| <span data-ttu-id="f179e-406">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="f179e-406">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="f179e-407">常规 Entity Framework Core 诊断。</span><span class="sxs-lookup"><span data-stu-id="f179e-407">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="f179e-408">数据库活动和配置、更改检测、迁移。</span><span class="sxs-lookup"><span data-stu-id="f179e-408">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="f179e-409">日志作用域</span><span class="sxs-lookup"><span data-stu-id="f179e-409">Log scopes</span></span>

 <span data-ttu-id="f179e-410">“作用域”可对一组逻辑操作分组  。</span><span class="sxs-lookup"><span data-stu-id="f179e-410">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="f179e-411">此分组可用于将相同的数据附加到作为集合的一部分而创建的每个日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-411">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="f179e-412">例如，在处理事务期间创建的每个日志都可包括事务 ID。</span><span class="sxs-lookup"><span data-stu-id="f179e-412">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="f179e-413">范围是由 <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> 方法返回的 `IDisposable` 类型，持续至释放为止。</span><span class="sxs-lookup"><span data-stu-id="f179e-413">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="f179e-414">要使用作用域，请在 `using` 块中包装记录器调用：</span><span class="sxs-lookup"><span data-stu-id="f179e-414">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="f179e-415">下列代码为控制台提供程序启用作用域：</span><span class="sxs-lookup"><span data-stu-id="f179e-415">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="f179e-416">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="f179e-416">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="f179e-417">要启用基于作用域的日志记录，必须先配置 `IncludeScopes` 控制台记录器选项。</span><span class="sxs-lookup"><span data-stu-id="f179e-417">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="f179e-418">若要了解关配置，请参阅[配置](#configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="f179e-418">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="f179e-419">每条日志消息都包含作用域内的信息：</span><span class="sxs-lookup"><span data-stu-id="f179e-419">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="f179e-420">内置日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-420">Built-in logging providers</span></span>

<span data-ttu-id="f179e-421">ASP.NET Core 提供以下提供程序：</span><span class="sxs-lookup"><span data-stu-id="f179e-421">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="f179e-422">控制台</span><span class="sxs-lookup"><span data-stu-id="f179e-422">Console</span></span>](#console-provider)
* [<span data-ttu-id="f179e-423">调试</span><span class="sxs-lookup"><span data-stu-id="f179e-423">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="f179e-424">EventSource</span><span class="sxs-lookup"><span data-stu-id="f179e-424">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="f179e-425">EventLog</span><span class="sxs-lookup"><span data-stu-id="f179e-425">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="f179e-426">TraceSource</span><span class="sxs-lookup"><span data-stu-id="f179e-426">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="f179e-427">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="f179e-427">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="f179e-428">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="f179e-428">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="f179e-429">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="f179e-429">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="f179e-430">要通过 ASP.NET Core 模块了解 stdout 和调试日志记录，请参阅 <xref:test/troubleshoot-azure-iis> 和 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="f179e-430">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="f179e-431">控制台提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-431">Console provider</span></span>

<span data-ttu-id="f179e-432">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 提供程序包向控制台发送日志输出。</span><span class="sxs-lookup"><span data-stu-id="f179e-432">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="f179e-433">要查看控制台日志记录输出，请在项目文件夹中打开命令提示符并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f179e-433">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="f179e-434">调试提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-434">Debug provider</span></span>

<span data-ttu-id="f179e-435">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 提供程序包使用 [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) 类（`Debug.WriteLine` 方法调用）来写入日志输出。</span><span class="sxs-lookup"><span data-stu-id="f179e-435">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="f179e-436">在 Linux 中，此提供程序将日志写入 /var/log/message  。</span><span class="sxs-lookup"><span data-stu-id="f179e-436">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="f179e-437">事件源提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-437">Event Source provider</span></span>

<span data-ttu-id="f179e-438">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) 提供程序包会使用名称 `Microsoft-Extensions-Logging` 跨平台写入事件源。</span><span class="sxs-lookup"><span data-stu-id="f179e-438">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="f179e-439">在 Windows 上，提供程序使用的是 [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)。</span><span class="sxs-lookup"><span data-stu-id="f179e-439">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="f179e-440">在调用 `CreateDefaultBuilder` 来生成主机时，会自动添加事件源提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-440">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="f179e-441">dotnet 跟踪工具</span><span class="sxs-lookup"><span data-stu-id="f179e-441">dotnet trace tooling</span></span>

<span data-ttu-id="f179e-442">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) 工具是一种跨平台 CLI 全局工具，可用于收集正在运行的进程的 .NET Core 跟踪。</span><span class="sxs-lookup"><span data-stu-id="f179e-442">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="f179e-443">该工具会使用 <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> 收集 <xref:Microsoft.Extensions.Logging.EventSource> 提供程序数据。</span><span class="sxs-lookup"><span data-stu-id="f179e-443">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="f179e-444">使用以下命令安装 dotnet 跟踪工具：</span><span class="sxs-lookup"><span data-stu-id="f179e-444">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="f179e-445">使用 dotnet 跟踪工具从应用中收集跟踪：</span><span class="sxs-lookup"><span data-stu-id="f179e-445">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="f179e-446">如果应用不使用 `CreateDefaultBuilder` 生成主机，则请向应用的日志记录配置添加[事件源提供程序](#event-source-provider)</span><span class="sxs-lookup"><span data-stu-id="f179e-446">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="f179e-447">使用 `dotnet run` 命令运行此应用。</span><span class="sxs-lookup"><span data-stu-id="f179e-447">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="f179e-448">确定 .NET Core 应用的进程标识符 (PID)：</span><span class="sxs-lookup"><span data-stu-id="f179e-448">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="f179e-449">在 Windows 上，使用下述方法之一：</span><span class="sxs-lookup"><span data-stu-id="f179e-449">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="f179e-450">任务管理器 (Ctrl+Alt+Del)</span><span class="sxs-lookup"><span data-stu-id="f179e-450">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="f179e-451">tasklist 命令</span><span class="sxs-lookup"><span data-stu-id="f179e-451">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="f179e-452">Get-Process Powershell 命令</span><span class="sxs-lookup"><span data-stu-id="f179e-452">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="f179e-453">在 Linux 上，使用 [pidof 命令](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)。</span><span class="sxs-lookup"><span data-stu-id="f179e-453">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="f179e-454">找到进程的 PID，它与应用的程序集的名称相同。</span><span class="sxs-lookup"><span data-stu-id="f179e-454">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="f179e-455">执行 `dotnet trace` 命令。</span><span class="sxs-lookup"><span data-stu-id="f179e-455">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="f179e-456">常规命令语法：</span><span class="sxs-lookup"><span data-stu-id="f179e-456">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="f179e-457">使用 PowerShell 命令行界面时，将 `--providers` 值用单引号 (`'`) 引起来：</span><span class="sxs-lookup"><span data-stu-id="f179e-457">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="f179e-458">在非 Windows 平台上，添加 `-f speedscope` 选项，将输出跟踪文件更改为 `speedscope`。</span><span class="sxs-lookup"><span data-stu-id="f179e-458">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="f179e-459">关键字</span><span class="sxs-lookup"><span data-stu-id="f179e-459">Keyword</span></span> | <span data-ttu-id="f179e-460">描述</span><span class="sxs-lookup"><span data-stu-id="f179e-460">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="f179e-461">1</span><span class="sxs-lookup"><span data-stu-id="f179e-461">1</span></span>       | <span data-ttu-id="f179e-462">记录有关 `LoggingEventSource` 的 meta 事件。</span><span class="sxs-lookup"><span data-stu-id="f179e-462">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="f179e-463">请不要从 `ILogger` 记录事件。</span><span class="sxs-lookup"><span data-stu-id="f179e-463">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="f179e-464">2</span><span class="sxs-lookup"><span data-stu-id="f179e-464">2</span></span>       | <span data-ttu-id="f179e-465">在调用 `ILogger.Log()` 时启用 `Message` 事件。</span><span class="sxs-lookup"><span data-stu-id="f179e-465">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="f179e-466">以编程（未格式化）方式提供信息。</span><span class="sxs-lookup"><span data-stu-id="f179e-466">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="f179e-467">4</span><span class="sxs-lookup"><span data-stu-id="f179e-467">4</span></span>       | <span data-ttu-id="f179e-468">在调用 `ILogger.Log()` 时启用 `FormatMessage` 事件。</span><span class="sxs-lookup"><span data-stu-id="f179e-468">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="f179e-469">提供格式化字符串版本的信息。</span><span class="sxs-lookup"><span data-stu-id="f179e-469">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="f179e-470">8</span><span class="sxs-lookup"><span data-stu-id="f179e-470">8</span></span>       | <span data-ttu-id="f179e-471">在调用 `ILogger.Log()` 时启用 `MessageJson` 事件。</span><span class="sxs-lookup"><span data-stu-id="f179e-471">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="f179e-472">提供参数的 JSON 表示形式。</span><span class="sxs-lookup"><span data-stu-id="f179e-472">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="f179e-473">事件级别</span><span class="sxs-lookup"><span data-stu-id="f179e-473">Event Level</span></span> | <span data-ttu-id="f179e-474">描述</span><span class="sxs-lookup"><span data-stu-id="f179e-474">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="f179e-475">0</span><span class="sxs-lookup"><span data-stu-id="f179e-475">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="f179e-476">1</span><span class="sxs-lookup"><span data-stu-id="f179e-476">1</span></span>           | `Critical`      |
   | <span data-ttu-id="f179e-477">2</span><span class="sxs-lookup"><span data-stu-id="f179e-477">2</span></span>           | `Error`         |
   | <span data-ttu-id="f179e-478">3</span><span class="sxs-lookup"><span data-stu-id="f179e-478">3</span></span>           | `Warning`       |
   | <span data-ttu-id="f179e-479">4</span><span class="sxs-lookup"><span data-stu-id="f179e-479">4</span></span>           | `Informational` |
   | <span data-ttu-id="f179e-480">5</span><span class="sxs-lookup"><span data-stu-id="f179e-480">5</span></span>           | `Verbose`       |

   <span data-ttu-id="f179e-481">`{Logger Category}` 和 `{Event Level}` 的 `FilterSpecs` 条目表示其他日志筛选条件。</span><span class="sxs-lookup"><span data-stu-id="f179e-481">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="f179e-482">请用分号 (`;`) 分隔 `FilterSpecs` 条目。</span><span class="sxs-lookup"><span data-stu-id="f179e-482">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="f179e-483">下例使用 Windows 命令界面（`--providers` 值不用单引号引起来）  ：</span><span class="sxs-lookup"><span data-stu-id="f179e-483">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="f179e-484">上面的命令会激活：</span><span class="sxs-lookup"><span data-stu-id="f179e-484">The preceding command activates:</span></span>

   * <span data-ttu-id="f179e-485">事件源记录器，它用于为错误 (`2`) 生成格式化字符串 (`4`)。</span><span class="sxs-lookup"><span data-stu-id="f179e-485">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="f179e-486">`Informational` 日志记录级别 (`4`) 的 `Microsoft.AspNetCore.Hosting` 日志记录。</span><span class="sxs-lookup"><span data-stu-id="f179e-486">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="f179e-487">通过按 Enter 键或 Ctrl+C 停止 dotnet 跟踪工具。</span><span class="sxs-lookup"><span data-stu-id="f179e-487">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="f179e-488">跟踪使用名称 trace.nettrace 保存在执行 `dotnet trace` 命令的文件夹中  。</span><span class="sxs-lookup"><span data-stu-id="f179e-488">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="f179e-489">使用[预览](#perfview)功能打开跟踪。</span><span class="sxs-lookup"><span data-stu-id="f179e-489">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="f179e-490">打开 trace.nettrace 文件并浏览跟踪事件  。</span><span class="sxs-lookup"><span data-stu-id="f179e-490">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="f179e-491">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="f179e-491">For more information, see:</span></span>

* <span data-ttu-id="f179e-492">[跟踪性能分析实用工具 (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace)（.NET Core 文档）</span><span class="sxs-lookup"><span data-stu-id="f179e-492">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="f179e-493">[跟踪性能分析实用工具 (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md)（dotnet/诊断 GitHub 存储库文档）</span><span class="sxs-lookup"><span data-stu-id="f179e-493">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="f179e-494">[LoggingEventSource 类](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource)（.NET API 浏览器）</span><span class="sxs-lookup"><span data-stu-id="f179e-494">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="f179e-495">[LoggingEventSource 引用源 (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; 若要获取不同版本的引用源，请将分支更改为 `release/{Version}`，其中 `{Version}` 是所需的 ASP.NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="f179e-495">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="f179e-496">[预览](#perfview) &ndash; 用于查看事件源跟踪。</span><span class="sxs-lookup"><span data-stu-id="f179e-496">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="f179e-497">Perfview</span><span class="sxs-lookup"><span data-stu-id="f179e-497">Perfview</span></span>

<span data-ttu-id="f179e-498">使用 [PerfView 实用工具](https://github.com/Microsoft/perfview)收集和查看日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-498">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="f179e-499">虽然其他工具也可以查看 ETW 日志，但在处理由 ASP.NET Core 发出的 ETW 事件时，使用 PerfView 能获得最佳体验。</span><span class="sxs-lookup"><span data-stu-id="f179e-499">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="f179e-500">要将 PerfView 配置为收集此提供程序记录的事件，请向 Additional Providers 列表添加字符串 `*Microsoft-Extensions-Logging` 。</span><span class="sxs-lookup"><span data-stu-id="f179e-500">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="f179e-501">（请勿遗漏字符串起始处的星号。）</span><span class="sxs-lookup"><span data-stu-id="f179e-501">(Don't miss the asterisk at the start of the string.)</span></span>

![其他 Perfview 提供程序](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="f179e-503">Windows EventLog 提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-503">Windows EventLog provider</span></span>

<span data-ttu-id="f179e-504">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 提供程序包向 Windows 事件日志发送日志输出。</span><span class="sxs-lookup"><span data-stu-id="f179e-504">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="f179e-505">[AddEventLog 重载](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)允许传入 <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>。</span><span class="sxs-lookup"><span data-stu-id="f179e-505">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="f179e-506">如果为 `null` 或未指定，则使用以下默认设置：</span><span class="sxs-lookup"><span data-stu-id="f179e-506">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="f179e-507">`LogName` &ndash; "Application"</span><span class="sxs-lookup"><span data-stu-id="f179e-507">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="f179e-508">`SourceName` &ndash; ".NET Runtime"</span><span class="sxs-lookup"><span data-stu-id="f179e-508">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="f179e-509">`MachineName` &ndash; local machine</span><span class="sxs-lookup"><span data-stu-id="f179e-509">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="f179e-510">为[警告等级和更高等级](#log-level)记录事件。</span><span class="sxs-lookup"><span data-stu-id="f179e-510">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="f179e-511">若要记录低于 `Warning` 的事件，请显式设置日志级别。</span><span class="sxs-lookup"><span data-stu-id="f179e-511">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="f179e-512">例如，将以下内容添加到 appsettings 文件中  ：</span><span class="sxs-lookup"><span data-stu-id="f179e-512">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="f179e-513">TraceSource 提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-513">TraceSource provider</span></span>

<span data-ttu-id="f179e-514">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) 提供程序包使用 <xref:System.Diagnostics.TraceSource> 库和提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-514">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="f179e-515">[AddTraceSource 重载](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) 允许传入资源开关和跟踪侦听器。</span><span class="sxs-lookup"><span data-stu-id="f179e-515">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="f179e-516">要使用此提供程序，应用必须在 .NET Framework（而非 .NET Core）上运行。</span><span class="sxs-lookup"><span data-stu-id="f179e-516">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="f179e-517">提供程序可将消息路由到多个[侦听器](/dotnet/framework/debug-trace-profile/trace-listeners)，例如示例应用中使用的 <xref:System.Diagnostics.TextWriterTraceListener>。</span><span class="sxs-lookup"><span data-stu-id="f179e-517">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="f179e-518">Azure 应用服务提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-518">Azure App Service provider</span></span>

<span data-ttu-id="f179e-519">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 提供程序包将日志写入 Azure App Service 应用的文件系统，以及 Azure 存储帐户中的 [blob 存储](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)。</span><span class="sxs-lookup"><span data-stu-id="f179e-519">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="f179e-520">共享框架中不包括该提供程序包。</span><span class="sxs-lookup"><span data-stu-id="f179e-520">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="f179e-521">若要使用提供程序，请将提供程序包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="f179e-521">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="f179e-522">要配置提供程序设置，请使用 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> 和 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="f179e-522">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="f179e-523">在部署应用服务应用时，应用程序将采用 Azure 门户中“应用服务”页面下的[应用服务日志](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag)部分的设置  。</span><span class="sxs-lookup"><span data-stu-id="f179e-523">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="f179e-524">更新以下设置后，更改立即生效，无需重启或重新部署应用。</span><span class="sxs-lookup"><span data-stu-id="f179e-524">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="f179e-525">应用程序日志记录(Filesystem) </span><span class="sxs-lookup"><span data-stu-id="f179e-525">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="f179e-526">应用程序日志记录(Blob) </span><span class="sxs-lookup"><span data-stu-id="f179e-526">**Application Logging (Blob)**</span></span>

<span data-ttu-id="f179e-527">日志文件的默认位置是 D:\\home\\LogFiles\\Application 文件夹，默认文件名为 diagnostics-yyyymmdd.txt   。</span><span class="sxs-lookup"><span data-stu-id="f179e-527">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="f179e-528">默认文件大小上限为 10 MB，默认最大保留文件数为 2。</span><span class="sxs-lookup"><span data-stu-id="f179e-528">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="f179e-529">默认 blob 名为 {app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt  。</span><span class="sxs-lookup"><span data-stu-id="f179e-529">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="f179e-530">该提供程序仅当项目在 Azure 环境中运行时有效。</span><span class="sxs-lookup"><span data-stu-id="f179e-530">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="f179e-531">项目在本地运行时，该提供程序无效 &mdash; 它不会写入本地文件或 blob 的本地开发存储。</span><span class="sxs-lookup"><span data-stu-id="f179e-531">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="f179e-532">Azure 日志流式处理</span><span class="sxs-lookup"><span data-stu-id="f179e-532">Azure log streaming</span></span>

<span data-ttu-id="f179e-533">通过 Azure 日志流式处理，可从以下位置实时查看日志活动：</span><span class="sxs-lookup"><span data-stu-id="f179e-533">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="f179e-534">应用服务器</span><span class="sxs-lookup"><span data-stu-id="f179e-534">The app server</span></span>
* <span data-ttu-id="f179e-535">Web 服务器</span><span class="sxs-lookup"><span data-stu-id="f179e-535">The web server</span></span>
* <span data-ttu-id="f179e-536">请求跟踪失败</span><span class="sxs-lookup"><span data-stu-id="f179e-536">Failed request tracing</span></span>

<span data-ttu-id="f179e-537">要配置 Azure 日志流式处理，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f179e-537">To configure Azure log streaming:</span></span>

* <span data-ttu-id="f179e-538">从应用的门户页导航到“应用服务日志”页  。</span><span class="sxs-lookup"><span data-stu-id="f179e-538">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="f179e-539">将“应用程序日志记录(Filesystem)”设置为“开”   。</span><span class="sxs-lookup"><span data-stu-id="f179e-539">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="f179e-540">选择日志级别  。</span><span class="sxs-lookup"><span data-stu-id="f179e-540">Choose the log **Level**.</span></span> <span data-ttu-id="f179e-541">此设置仅适用于 Azure 日志流，不适用于应用中的其他日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-541">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="f179e-542">导航到“日志流”页面来查看应用消息  。</span><span class="sxs-lookup"><span data-stu-id="f179e-542">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="f179e-543">它们由应用通过 `ILogger` 接口记录。</span><span class="sxs-lookup"><span data-stu-id="f179e-543">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="f179e-544">Azure Application Insights 跟踪日志记录</span><span class="sxs-lookup"><span data-stu-id="f179e-544">Azure Application Insights trace logging</span></span>

<span data-ttu-id="f179e-545">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) 提供程序包将日志写入 Azure Application Insights。</span><span class="sxs-lookup"><span data-stu-id="f179e-545">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="f179e-546">Application Insights 是一项服务，可监视 Web 应用并提供用于查询和分析遥测数据的工具。</span><span class="sxs-lookup"><span data-stu-id="f179e-546">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="f179e-547">如果使用此提供程序，则可以使用 Application Insights 工具来查询和分析日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-547">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="f179e-548">日志记录提供程序作为 [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)（这是提供 ASP.NET Core 的所有可用遥测的包）的依赖项包括在内。</span><span class="sxs-lookup"><span data-stu-id="f179e-548">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="f179e-549">如果使用此包，则无需安装提供程序包。</span><span class="sxs-lookup"><span data-stu-id="f179e-549">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="f179e-550">请勿使用用于 ASP.NET 4.x 的 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) 包&mdash;。</span><span class="sxs-lookup"><span data-stu-id="f179e-550">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="f179e-551">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="f179e-551">For more information, see the following resources:</span></span>

* [<span data-ttu-id="f179e-552">Application Insights 概述</span><span class="sxs-lookup"><span data-stu-id="f179e-552">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="f179e-553">[用于 ASP.NET Core 应用程序的 Application Insights](/azure/azure-monitor/app/asp-net-core) - 如果想要实现各种 Application Insights 遥测以及日志记录，请从这里开始。</span><span class="sxs-lookup"><span data-stu-id="f179e-553">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="f179e-554">[.NET Core ILogger 日志的 ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - 如果想要在没有其他 Application Insights 遥测的情况下实现日志记录提供程序，请从这里开始。</span><span class="sxs-lookup"><span data-stu-id="f179e-554">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="f179e-555">[Application Insights 日志记录适配器](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)。</span><span class="sxs-lookup"><span data-stu-id="f179e-555">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="f179e-556">[安装、配置和初始化 Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn 网站上的交互式教程。</span><span class="sxs-lookup"><span data-stu-id="f179e-556">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="f179e-557">第三方日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-557">Third-party logging providers</span></span>

<span data-ttu-id="f179e-558">适用于 ASP.NET Core 的第三方日志记录框架：</span><span class="sxs-lookup"><span data-stu-id="f179e-558">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="f179e-559">[elmah.io](https://elmah.io/)（[GitHub 存储库](https://github.com/elmahio/Elmah.Io.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="f179e-559">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="f179e-560">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html)（[GitHub 存储库](https://github.com/mattwcole/gelf-extensions-logging)）</span><span class="sxs-lookup"><span data-stu-id="f179e-560">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="f179e-561">[JSNLog](https://jsnlog.com/)（[GitHub 存储库](https://github.com/mperdeck/jsnlog)）</span><span class="sxs-lookup"><span data-stu-id="f179e-561">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="f179e-562">[KissLog.net](https://kisslog.net/)（[GitHub 存储库](https://github.com/catalingavan/KissLog-net)）</span><span class="sxs-lookup"><span data-stu-id="f179e-562">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="f179e-563">[Log4Net](https://logging.apache.org/log4net/)（[GitHub 存储库](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore)）</span><span class="sxs-lookup"><span data-stu-id="f179e-563">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="f179e-564">[Loggr](https://loggr.net/)（[GitHub 存储库](https://github.com/imobile3/Loggr.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="f179e-564">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="f179e-565">[NLog](https://nlog-project.org/)（[GitHub 存储库](https://github.com/NLog/NLog.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="f179e-565">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="f179e-566">[Sentry](https://sentry.io/welcome/)（[GitHub 存储库](https://github.com/getsentry/sentry-dotnet)）</span><span class="sxs-lookup"><span data-stu-id="f179e-566">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="f179e-567">[Serilog](https://serilog.net/)（[GitHub 存储库](https://github.com/serilog/serilog-aspnetcore)）</span><span class="sxs-lookup"><span data-stu-id="f179e-567">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="f179e-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging)（[Github 存储库](https://github.com/googleapis/google-cloud-dotnet)）</span><span class="sxs-lookup"><span data-stu-id="f179e-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="f179e-569">某些第三方框架可以执行[语义日志记录（又称结构化日志记录）](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="f179e-569">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="f179e-570">使用第三方框架类似于使用以下内置提供程序之一：</span><span class="sxs-lookup"><span data-stu-id="f179e-570">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="f179e-571">将 NuGet 包添加到你的项目。</span><span class="sxs-lookup"><span data-stu-id="f179e-571">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="f179e-572">调用日志记录框架提供的 `ILoggerFactory` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="f179e-572">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="f179e-573">有关详细信息，请参阅各提供程序的相关文档。</span><span class="sxs-lookup"><span data-stu-id="f179e-573">For more information, see each provider's documentation.</span></span> <span data-ttu-id="f179e-574">Microsoft 不支持第三方日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-574">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f179e-575">其他资源</span><span class="sxs-lookup"><span data-stu-id="f179e-575">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f179e-576">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f179e-576">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f179e-577">.NET Core 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。</span><span class="sxs-lookup"><span data-stu-id="f179e-577">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="f179e-578">本文介绍了如何将日志记录 API 与内置提供程序一起使用。</span><span class="sxs-lookup"><span data-stu-id="f179e-578">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="f179e-579">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f179e-579">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="f179e-580">添加提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-580">Add providers</span></span>

<span data-ttu-id="f179e-581">日志记录提供程序会显示或存储日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-581">A logging provider displays or stores logs.</span></span> <span data-ttu-id="f179e-582">例如，控制台提供程序在控制台上显示日志，Azure Application Insights 提供程序会将这些日志存储在 Azure Application Insights 中。</span><span class="sxs-lookup"><span data-stu-id="f179e-582">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="f179e-583">可通过添加多个提供程序将日志发送到多个目标。</span><span class="sxs-lookup"><span data-stu-id="f179e-583">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="f179e-584">要添加提供程序，请在 Program.cs 中调用提供程序的 `Add{provider name}` 扩展方法  ：</span><span class="sxs-lookup"><span data-stu-id="f179e-584">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="f179e-585">前面的代码需要引用 `Microsoft.Extensions.Logging` 和 `Microsoft.Extensions.Configuration`。</span><span class="sxs-lookup"><span data-stu-id="f179e-585">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="f179e-586">默认项目模板调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>，该操作将添加以下日志记录提供程序：</span><span class="sxs-lookup"><span data-stu-id="f179e-586">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="f179e-587">控制台</span><span class="sxs-lookup"><span data-stu-id="f179e-587">Console</span></span>
* <span data-ttu-id="f179e-588">调试</span><span class="sxs-lookup"><span data-stu-id="f179e-588">Debug</span></span>
* <span data-ttu-id="f179e-589">EventSource（从 ASP.NET Core 2.2 开始）</span><span class="sxs-lookup"><span data-stu-id="f179e-589">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="f179e-590">如果使用 `CreateDefaultBuilder`，则可自行选择提供程序来替换默认提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-590">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="f179e-591">调用 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>，然后添加所需的提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-591">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="f179e-592">详细了解[内置日志记录提供程序](#built-in-logging-providers)，以及本文稍后部分介绍的[第三方日志记录提供程序](#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="f179e-592">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="f179e-593">创建日志</span><span class="sxs-lookup"><span data-stu-id="f179e-593">Create logs</span></span>

<span data-ttu-id="f179e-594">若要创建日志，请使用 <xref:Microsoft.Extensions.Logging.ILogger%601> 对象。</span><span class="sxs-lookup"><span data-stu-id="f179e-594">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="f179e-595">在 Web 应用或托管服务中，由依赖关系注入 (DI) 获取 `ILogger`。</span><span class="sxs-lookup"><span data-stu-id="f179e-595">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="f179e-596">在非主机控制台应用中，使用 `LoggerFactory` 来创建 `ILogger`。</span><span class="sxs-lookup"><span data-stu-id="f179e-596">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="f179e-597">下面的 ASP.NET Core 示例创建了一个以 `TodoApiSample.Pages.AboutModel` 为类别的记录器。</span><span class="sxs-lookup"><span data-stu-id="f179e-597">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="f179e-598">日志“类别”是与每个日志关联的字符串  。</span><span class="sxs-lookup"><span data-stu-id="f179e-598">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="f179e-599">DI 提供的 `ILogger<T>` 实例创建日志，该日志以类型 `T` 的完全限定名称为类别。</span><span class="sxs-lookup"><span data-stu-id="f179e-599">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="f179e-600">在下面的 ASP.NET Core 和控制台应用示例中，记录器用于创建以 `Information` 为级别的日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-600">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="f179e-601">日志“级别”代表所记录事件的严重程度  。</span><span class="sxs-lookup"><span data-stu-id="f179e-601">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="f179e-602">本文稍后部分将更详细地介绍[级别](#log-level)和[类别](#log-category)。</span><span class="sxs-lookup"><span data-stu-id="f179e-602">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="f179e-603">启动时创建日志</span><span class="sxs-lookup"><span data-stu-id="f179e-603">Create logs in Startup</span></span>

<span data-ttu-id="f179e-604">要将日志写入 `Startup` 类，构造函数签名需包含 `ILogger` 参数：</span><span class="sxs-lookup"><span data-stu-id="f179e-604">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="f179e-605">在 Program 类中创建日志</span><span class="sxs-lookup"><span data-stu-id="f179e-605">Create logs in the Program class</span></span>

<span data-ttu-id="f179e-606">要将日志写入 `Program` 类，请从 DI 获取 `ILogger` 实例：</span><span class="sxs-lookup"><span data-stu-id="f179e-606">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="f179e-607">不直接支持在主机构造期间进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="f179e-607">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="f179e-608">但是，可以使用单独的记录器。</span><span class="sxs-lookup"><span data-stu-id="f179e-608">However, a separate logger can be used.</span></span> <span data-ttu-id="f179e-609">在以下示例中，[Serilog](https://serilog.net/) 记录器用于登录 `CreateWebHostBuilder`。</span><span class="sxs-lookup"><span data-stu-id="f179e-609">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="f179e-610">`AddSerilog` 使用 `Log.Logger` 中指定的静态配置：</span><span class="sxs-lookup"><span data-stu-id="f179e-610">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="f179e-611">没有异步记录器方法</span><span class="sxs-lookup"><span data-stu-id="f179e-611">No asynchronous logger methods</span></span>

<span data-ttu-id="f179e-612">日志记录应该会很快，不值得牺牲性能来使用异步代码。</span><span class="sxs-lookup"><span data-stu-id="f179e-612">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="f179e-613">如果你的日志数据存储很慢，请不要直接写入它。</span><span class="sxs-lookup"><span data-stu-id="f179e-613">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="f179e-614">首先考虑将日志消息写入快速存储，稍后再将其变为慢速存储。</span><span class="sxs-lookup"><span data-stu-id="f179e-614">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="f179e-615">例如，如果你要记录到 SQL Server，你可能不想直接在 `Log` 方法中记录，因为 `Log` 方法是同步的。</span><span class="sxs-lookup"><span data-stu-id="f179e-615">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="f179e-616">相反，你会将日志消息同步添加到内存中的队列，并让后台辅助线程从队列中拉出消息，以完成将数据推送到 SQL Server 的异步工作。</span><span class="sxs-lookup"><span data-stu-id="f179e-616">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="f179e-617">有关详细信息，请参阅[此](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub 问题。</span><span class="sxs-lookup"><span data-stu-id="f179e-617">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="f179e-618">Configuration</span><span class="sxs-lookup"><span data-stu-id="f179e-618">Configuration</span></span>

<span data-ttu-id="f179e-619">日志记录提供程序配置由一个或多个配置提供程序提供：</span><span class="sxs-lookup"><span data-stu-id="f179e-619">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="f179e-620">文件格式（INI、JSON 和 XML）。</span><span class="sxs-lookup"><span data-stu-id="f179e-620">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="f179e-621">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="f179e-621">Command-line arguments.</span></span>
* <span data-ttu-id="f179e-622">环境变量。</span><span class="sxs-lookup"><span data-stu-id="f179e-622">Environment variables.</span></span>
* <span data-ttu-id="f179e-623">内存中的 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="f179e-623">In-memory .NET objects.</span></span>
* <span data-ttu-id="f179e-624">未加密的[机密管理器](xref:security/app-secrets)存储。</span><span class="sxs-lookup"><span data-stu-id="f179e-624">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="f179e-625">加密的用户存储，如 [Azure Key Vault](xref:security/key-vault-configuration)。</span><span class="sxs-lookup"><span data-stu-id="f179e-625">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="f179e-626">（已安装或已创建的）自定义提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-626">Custom providers (installed or created).</span></span>

<span data-ttu-id="f179e-627">例如，日志记录配置通常由应用设置文件的 `Logging` 部分提供。</span><span class="sxs-lookup"><span data-stu-id="f179e-627">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="f179e-628">以下示例显示了典型 *appsettings.Development.json* 文件的内容：</span><span class="sxs-lookup"><span data-stu-id="f179e-628">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="f179e-629">`Logging` 属性可具有 `LogLevel` 和日志提供程序属性（显示控制台）。</span><span class="sxs-lookup"><span data-stu-id="f179e-629">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="f179e-630">`Logging` 下的 `LogLevel` 属性指定了用于记录所选类别的最低[级别](#log-level)。</span><span class="sxs-lookup"><span data-stu-id="f179e-630">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="f179e-631">在本例中，`System` 和 `Microsoft` 类别在 `Information` 级别记录，其他均在 `Debug` 级别记录。</span><span class="sxs-lookup"><span data-stu-id="f179e-631">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="f179e-632">`Logging` 下的其他属性均指定了日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-632">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="f179e-633">本示例针对控制台提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-633">The example is for the Console provider.</span></span> <span data-ttu-id="f179e-634">如果提供程序支持[日志作用域](#log-scopes)，则 `IncludeScopes` 将指示是否启用这些域。</span><span class="sxs-lookup"><span data-stu-id="f179e-634">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="f179e-635">提供程序属性（例如本例的 `Console`）也可指定 `LogLevel` 属性。</span><span class="sxs-lookup"><span data-stu-id="f179e-635">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="f179e-636">提供程序下的 `LogLevel` 指定了该提供程序记录的级别。</span><span class="sxs-lookup"><span data-stu-id="f179e-636">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="f179e-637">如果在 `Logging.{providername}.LogLevel` 中指定了级别，则这些级别将重写 `Logging.LogLevel` 中设置的所有内容。</span><span class="sxs-lookup"><span data-stu-id="f179e-637">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="f179e-638">不可使用日志记录 API 在应用运行时更改日志记录。</span><span class="sxs-lookup"><span data-stu-id="f179e-638">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="f179e-639">但是，一些配置提供程序可重新加载配置，这将对日志记录配置立即产生影响。</span><span class="sxs-lookup"><span data-stu-id="f179e-639">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="f179e-640">例如，[文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)会默认重新加载日志记录配置，该程序由 `CreateDefaultBuilder` 添加用来读取设置文件。</span><span class="sxs-lookup"><span data-stu-id="f179e-640">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="f179e-641">如果在应用运行时在代码中更改了配置，则该应用可调用 [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) 来更新应用的日志记录配置。</span><span class="sxs-lookup"><span data-stu-id="f179e-641">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="f179e-642">若要了解如何实现配置提供程序，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="f179e-642">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="f179e-643">日志记录输出示例</span><span class="sxs-lookup"><span data-stu-id="f179e-643">Sample logging output</span></span>

<span data-ttu-id="f179e-644">使用上一部分中显示的示例代码从命令行运行应用时，将在控制台中看到日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-644">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="f179e-645">以下是控制台输出示例：</span><span class="sxs-lookup"><span data-stu-id="f179e-645">Here's an example of console output:</span></span>

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

<span data-ttu-id="f179e-646">通过向 `http://localhost:5000/api/todo/0` 处的示例应用发出 HTTP Get 请求来生成前述日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-646">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="f179e-647">在 Visual Studio 中运行示例应用时，“调试”窗口中将显示如下日志：</span><span class="sxs-lookup"><span data-stu-id="f179e-647">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="f179e-648">由上一部分所示的 `ILogger` 调用创建的日志以“TodoApi”开头。</span><span class="sxs-lookup"><span data-stu-id="f179e-648">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="f179e-649">以“Microsoft”类别开头的日志来自 ASP.NET Core 框架代码。</span><span class="sxs-lookup"><span data-stu-id="f179e-649">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="f179e-650">ASP.NET Core 和应用程序代码使用相同的日志记录 API 和提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-650">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="f179e-651">本文余下部分将介绍有关日志记录的某些详细信息及选项。</span><span class="sxs-lookup"><span data-stu-id="f179e-651">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="f179e-652">NuGet 包</span><span class="sxs-lookup"><span data-stu-id="f179e-652">NuGet packages</span></span>

<span data-ttu-id="f179e-653">`ILogger` 和 `ILoggerFactory` 接口位于 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 中，其默认实现位于 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 中。</span><span class="sxs-lookup"><span data-stu-id="f179e-653">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="f179e-654">日志类别</span><span class="sxs-lookup"><span data-stu-id="f179e-654">Log category</span></span>

<span data-ttu-id="f179e-655">创建 `ILogger` 对象后，将为其指定“类别”  。</span><span class="sxs-lookup"><span data-stu-id="f179e-655">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="f179e-656">该类别包含在由此 `ILogger` 实例创建的每条日志消息中。</span><span class="sxs-lookup"><span data-stu-id="f179e-656">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="f179e-657">类别可以是任何字符串，但约定需使用类名，例如“TodoApi.Controllers.TodoController”。</span><span class="sxs-lookup"><span data-stu-id="f179e-657">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="f179e-658">使用 `ILogger<T>` 获取一个 `ILogger` 实例，该实例使用 `T` 的完全限定类型名称作为类别：</span><span class="sxs-lookup"><span data-stu-id="f179e-658">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="f179e-659">要显式指定类别，请调用 `ILoggerFactory.CreateLogger`：</span><span class="sxs-lookup"><span data-stu-id="f179e-659">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="f179e-660">`ILogger<T>` 相当于使用 `T` 的完全限定类型名称来调用 `CreateLogger`。</span><span class="sxs-lookup"><span data-stu-id="f179e-660">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="f179e-661">日志级别</span><span class="sxs-lookup"><span data-stu-id="f179e-661">Log level</span></span>

<span data-ttu-id="f179e-662">每个日志都指定了一个 <xref:Microsoft.Extensions.Logging.LogLevel> 值。</span><span class="sxs-lookup"><span data-stu-id="f179e-662">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="f179e-663">日志级别指示严重性或重要程度。</span><span class="sxs-lookup"><span data-stu-id="f179e-663">The log level indicates the severity or importance.</span></span> <span data-ttu-id="f179e-664">例如，可在方法正常结束时写入 `Information` 日志，在方法返回“404 找不到”状态代码时写入 `Warning` 日志  。</span><span class="sxs-lookup"><span data-stu-id="f179e-664">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="f179e-665">下面的代码会创建 `Information` 和 `Warning` 日志：</span><span class="sxs-lookup"><span data-stu-id="f179e-665">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="f179e-666">在上述代码中，第一个参数是[日志事件 ID](#log-event-id)。</span><span class="sxs-lookup"><span data-stu-id="f179e-666">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="f179e-667">第二个参数是消息模板，其中的占位符用于填写剩余方法形参提供的实参值。</span><span class="sxs-lookup"><span data-stu-id="f179e-667">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="f179e-668">稍后将在本文的[消息模板部分](#log-message-template)介绍方法参数。</span><span class="sxs-lookup"><span data-stu-id="f179e-668">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="f179e-669">在方法名称中包含级别的日志方法（例如 `LogInformation` 和 `LogWarning`）是 [ILogger 的扩展方法](xref:Microsoft.Extensions.Logging.LoggerExtensions)。</span><span class="sxs-lookup"><span data-stu-id="f179e-669">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="f179e-670">这些方法会调用可接受 `LogLevel` 参数的 `Log` 方法。</span><span class="sxs-lookup"><span data-stu-id="f179e-670">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="f179e-671">可直接调用 `Log` 方法而不调用其中某个扩展方法，但语法相对复杂。</span><span class="sxs-lookup"><span data-stu-id="f179e-671">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="f179e-672">有关详细信息，请参阅 <xref:Microsoft.Extensions.Logging.ILogger> 和[记录器扩展源代码](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)。</span><span class="sxs-lookup"><span data-stu-id="f179e-672">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="f179e-673">ASP.NET Core 定义了以下日志级别（按严重性从低到高排列）。</span><span class="sxs-lookup"><span data-stu-id="f179e-673">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="f179e-674">跟踪 = 0</span><span class="sxs-lookup"><span data-stu-id="f179e-674">Trace = 0</span></span>

  <span data-ttu-id="f179e-675">有关通常仅用于调试的信息。</span><span class="sxs-lookup"><span data-stu-id="f179e-675">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="f179e-676">这些消息可能包含敏感应用程序数据，因此不得在生产环境中启用它们。</span><span class="sxs-lookup"><span data-stu-id="f179e-676">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="f179e-677">默认情况下禁用。 </span><span class="sxs-lookup"><span data-stu-id="f179e-677">*Disabled by default.*</span></span>

* <span data-ttu-id="f179e-678">调试 = 1</span><span class="sxs-lookup"><span data-stu-id="f179e-678">Debug = 1</span></span>

  <span data-ttu-id="f179e-679">有关在开发和调试中可能有用的信息。</span><span class="sxs-lookup"><span data-stu-id="f179e-679">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="f179e-680">示例：`Entering method Configure with flag set to true.` 由于日志数量过多，因此仅当执行故障排除时，才在生产中启用 `Debug` 级别日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-680">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="f179e-681">信息 = 2</span><span class="sxs-lookup"><span data-stu-id="f179e-681">Information = 2</span></span>

  <span data-ttu-id="f179e-682">用于跟踪应用的常规流。</span><span class="sxs-lookup"><span data-stu-id="f179e-682">For tracking the general flow of the app.</span></span> <span data-ttu-id="f179e-683">这些日志通常有长期价值。</span><span class="sxs-lookup"><span data-stu-id="f179e-683">These logs typically have some long-term value.</span></span> <span data-ttu-id="f179e-684">示例：`Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="f179e-684">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="f179e-685">警告 = 3</span><span class="sxs-lookup"><span data-stu-id="f179e-685">Warning = 3</span></span>

  <span data-ttu-id="f179e-686">表示应用流中的异常或意外事件。</span><span class="sxs-lookup"><span data-stu-id="f179e-686">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="f179e-687">可能包括不会中断应用运行但仍需调查的错误或其他条件。</span><span class="sxs-lookup"><span data-stu-id="f179e-687">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="f179e-688">`Warning` 日志级别常用于已处理的异常。</span><span class="sxs-lookup"><span data-stu-id="f179e-688">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="f179e-689">示例：`FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="f179e-689">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="f179e-690">错误 = 4</span><span class="sxs-lookup"><span data-stu-id="f179e-690">Error = 4</span></span>

  <span data-ttu-id="f179e-691">表示无法处理的错误和异常。</span><span class="sxs-lookup"><span data-stu-id="f179e-691">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="f179e-692">这些消息指示的是当前活动或操作（例如当前 HTTP 请求）中的失败，而不是整个应用中的失败。</span><span class="sxs-lookup"><span data-stu-id="f179e-692">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="f179e-693">日志消息示例：`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="f179e-693">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="f179e-694">严重 = 5</span><span class="sxs-lookup"><span data-stu-id="f179e-694">Critical = 5</span></span>

  <span data-ttu-id="f179e-695">需要立即关注的失败。</span><span class="sxs-lookup"><span data-stu-id="f179e-695">For failures that require immediate attention.</span></span> <span data-ttu-id="f179e-696">例如数据丢失、磁盘空间不足。</span><span class="sxs-lookup"><span data-stu-id="f179e-696">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="f179e-697">使用日志级别控制写入到特定存储介质或显示窗口的日志输出量。</span><span class="sxs-lookup"><span data-stu-id="f179e-697">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="f179e-698">例如：</span><span class="sxs-lookup"><span data-stu-id="f179e-698">For example:</span></span>

* <span data-ttu-id="f179e-699">生产中：</span><span class="sxs-lookup"><span data-stu-id="f179e-699">In production:</span></span>
  * <span data-ttu-id="f179e-700">如果通过 `Information` 级别在 `Trace` 处记录，则会生成大量详细的日志消息。</span><span class="sxs-lookup"><span data-stu-id="f179e-700">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="f179e-701">为控制成本且不超过数据存储限制，请通过 `Information` 级别消息将 `Trace` 记录到容量大、成本低的数据存储中。</span><span class="sxs-lookup"><span data-stu-id="f179e-701">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="f179e-702">如果通过 `Critical` 级别在 `Warning` 处记录，通常生成的日志消息更少且更小。</span><span class="sxs-lookup"><span data-stu-id="f179e-702">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="f179e-703">因此，成本和存储限制通常不是问题，而这使得在选择数据信息时更为灵活。</span><span class="sxs-lookup"><span data-stu-id="f179e-703">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="f179e-704">在开发过程中：</span><span class="sxs-lookup"><span data-stu-id="f179e-704">During development:</span></span>
  * <span data-ttu-id="f179e-705">通过 `Critical` 消息将 `Warning` 记录到控制台。</span><span class="sxs-lookup"><span data-stu-id="f179e-705">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="f179e-706">在疑难解答时通过 `Information` 消息添加 `Trace`。</span><span class="sxs-lookup"><span data-stu-id="f179e-706">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="f179e-707">本文稍后的[日志筛选](#log-filtering)部分介绍如何控制提供程序处理的日志级别。</span><span class="sxs-lookup"><span data-stu-id="f179e-707">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="f179e-708">ASP.NET Core 为框架事件写入日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-708">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="f179e-709">本文前面部分提供的日志示例排除了低于 `Information` 级别的日志，因此未创建 `Debug` 或 `Trace` 级别日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-709">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="f179e-710">以下示例介绍了通过运行配置为显示 `Debug` 日志的示例应用而生成的控制台日志：</span><span class="sxs-lookup"><span data-stu-id="f179e-710">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="f179e-711">日志事件 ID</span><span class="sxs-lookup"><span data-stu-id="f179e-711">Log event ID</span></span>

<span data-ttu-id="f179e-712">每个日志都可指定一个事件 ID  。</span><span class="sxs-lookup"><span data-stu-id="f179e-712">Each log can specify an *event ID*.</span></span> <span data-ttu-id="f179e-713">该示例应用通过使用本地定义的 `LoggingEvents` 类来执行此操作：</span><span class="sxs-lookup"><span data-stu-id="f179e-713">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="f179e-714">事件 ID 与一组事件相关联。</span><span class="sxs-lookup"><span data-stu-id="f179e-714">An event ID associates a set of events.</span></span> <span data-ttu-id="f179e-715">例如，与在页面上显示项列表相关的所有日志可能是 1001。</span><span class="sxs-lookup"><span data-stu-id="f179e-715">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="f179e-716">日志记录提供程序可将事件 ID 存储在 ID 字段中，存储在日志记录消息中，或者不进行存储。</span><span class="sxs-lookup"><span data-stu-id="f179e-716">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="f179e-717">调试提供程序不显示事件 ID。</span><span class="sxs-lookup"><span data-stu-id="f179e-717">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="f179e-718">控制台提供程序在类别后的括号中显示事件 ID：</span><span class="sxs-lookup"><span data-stu-id="f179e-718">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="f179e-719">日志消息模板</span><span class="sxs-lookup"><span data-stu-id="f179e-719">Log message template</span></span>

<span data-ttu-id="f179e-720">每个日志都会指定一个消息模板。</span><span class="sxs-lookup"><span data-stu-id="f179e-720">Each log specifies a message template.</span></span> <span data-ttu-id="f179e-721">消息模板可包含要填写参数的占位符。</span><span class="sxs-lookup"><span data-stu-id="f179e-721">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="f179e-722">请在占位符中使用名称而不是数字。</span><span class="sxs-lookup"><span data-stu-id="f179e-722">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="f179e-723">占位符的顺序（而非其名称）决定了为其提供值的参数。</span><span class="sxs-lookup"><span data-stu-id="f179e-723">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="f179e-724">在以下代码中，请注意消息模板中的参数名称未按顺序排列：</span><span class="sxs-lookup"><span data-stu-id="f179e-724">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="f179e-725">此代码创建了一个参数值按顺序排列的日志消息：</span><span class="sxs-lookup"><span data-stu-id="f179e-725">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="f179e-726">日志记录框架按此方式工作，这样日志记录提供程序即可实现[语义日志记录，也称为结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="f179e-726">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="f179e-727">参数本身会传递给日志记录系统，而不仅仅是格式化的消息模板。</span><span class="sxs-lookup"><span data-stu-id="f179e-727">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="f179e-728">通过此信息，日志记录提供程序能够将参数值存储为字段。</span><span class="sxs-lookup"><span data-stu-id="f179e-728">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="f179e-729">例如，假设记录器方法调用如下所示：</span><span class="sxs-lookup"><span data-stu-id="f179e-729">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="f179e-730">如果要将日志发送到 Azure 表存储，则每个 Azure 表实体都可具有 `ID` 和 `RequestTime` 属性，这简化了对日志数据的查询。</span><span class="sxs-lookup"><span data-stu-id="f179e-730">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="f179e-731">无需分析文本消息的超时，查询即可找到特定 `RequestTime` 范围内的全部日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-731">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="f179e-732">日志记录异常</span><span class="sxs-lookup"><span data-stu-id="f179e-732">Logging exceptions</span></span>

<span data-ttu-id="f179e-733">记录器方法有可传入异常的重载，如下方示例所示：</span><span class="sxs-lookup"><span data-stu-id="f179e-733">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="f179e-734">不同的提供程序处理异常信息的方式不同。</span><span class="sxs-lookup"><span data-stu-id="f179e-734">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="f179e-735">以下是上示代码的调试提供程序输出示例。</span><span class="sxs-lookup"><span data-stu-id="f179e-735">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="f179e-736">日志筛选</span><span class="sxs-lookup"><span data-stu-id="f179e-736">Log filtering</span></span>

<span data-ttu-id="f179e-737">可为特定或所有提供程序和类别指定最低日志级别。</span><span class="sxs-lookup"><span data-stu-id="f179e-737">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="f179e-738">最低级别以下的日志不会传递给该提供程序，因此不会显示或存储它们。</span><span class="sxs-lookup"><span data-stu-id="f179e-738">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="f179e-739">要禁止显示所有日志，可将 `LogLevel.None` 指定为最低日志级别。</span><span class="sxs-lookup"><span data-stu-id="f179e-739">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="f179e-740">`LogLevel.None` 的整数值为 6，它大于 `LogLevel.Critical` (5)。</span><span class="sxs-lookup"><span data-stu-id="f179e-740">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="f179e-741">在配置中创建筛选规则</span><span class="sxs-lookup"><span data-stu-id="f179e-741">Create filter rules in configuration</span></span>

<span data-ttu-id="f179e-742">项目模板代码调用 `CreateDefaultBuilder` 来为 Console、Debug 和 EventSource（ASP.NET Core 2.2 或更高版本）提供程序设置日志记录。</span><span class="sxs-lookup"><span data-stu-id="f179e-742">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="f179e-743">正如[本文前面部分](#configuration)所述，`CreateDefaultBuilder` 方法设置日志记录以在 `Logging` 部分查找配置。</span><span class="sxs-lookup"><span data-stu-id="f179e-743">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="f179e-744">配置数据按提供程序和类别指定最低日志级别，如下方示例所示：</span><span class="sxs-lookup"><span data-stu-id="f179e-744">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="f179e-745">此 JSON 将创建 6 条筛选规则：1 条用于调试提供程序， 4 条用于控制台提供程序， 1 条用于所有提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-745">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="f179e-746">创建 `ILogger` 对象时，为每个提供程序选择一个规则。</span><span class="sxs-lookup"><span data-stu-id="f179e-746">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="f179e-747">代码中的筛选规则</span><span class="sxs-lookup"><span data-stu-id="f179e-747">Filter rules in code</span></span>

<span data-ttu-id="f179e-748">下面的示例演示了如何在代码中注册筛选规则：</span><span class="sxs-lookup"><span data-stu-id="f179e-748">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="f179e-749">第二个 `AddFilter` 使用类型名称来指定调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-749">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="f179e-750">第一个 `AddFilter` 应用于全部提供程序，因为它未指定提供程序类型。</span><span class="sxs-lookup"><span data-stu-id="f179e-750">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="f179e-751">如何应用筛选规则</span><span class="sxs-lookup"><span data-stu-id="f179e-751">How filtering rules are applied</span></span>

<span data-ttu-id="f179e-752">先前示例中显示的配置数据和 `AddFilter` 代码会创建下表所示的规则。</span><span class="sxs-lookup"><span data-stu-id="f179e-752">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="f179e-753">前六条由配置示例创建，后两条由代码示例创建。</span><span class="sxs-lookup"><span data-stu-id="f179e-753">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="f179e-754">数字</span><span class="sxs-lookup"><span data-stu-id="f179e-754">Number</span></span> | <span data-ttu-id="f179e-755">提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-755">Provider</span></span>      | <span data-ttu-id="f179e-756">类别的开头为...</span><span class="sxs-lookup"><span data-stu-id="f179e-756">Categories that begin with ...</span></span>          | <span data-ttu-id="f179e-757">最低日志级别</span><span class="sxs-lookup"><span data-stu-id="f179e-757">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="f179e-758">1</span><span class="sxs-lookup"><span data-stu-id="f179e-758">1</span></span>      | <span data-ttu-id="f179e-759">调试</span><span class="sxs-lookup"><span data-stu-id="f179e-759">Debug</span></span>         | <span data-ttu-id="f179e-760">全部类别</span><span class="sxs-lookup"><span data-stu-id="f179e-760">All categories</span></span>                          | <span data-ttu-id="f179e-761">信息</span><span class="sxs-lookup"><span data-stu-id="f179e-761">Information</span></span>       |
| <span data-ttu-id="f179e-762">2</span><span class="sxs-lookup"><span data-stu-id="f179e-762">2</span></span>      | <span data-ttu-id="f179e-763">控制台</span><span class="sxs-lookup"><span data-stu-id="f179e-763">Console</span></span>       | <span data-ttu-id="f179e-764">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="f179e-764">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="f179e-765">警告</span><span class="sxs-lookup"><span data-stu-id="f179e-765">Warning</span></span>           |
| <span data-ttu-id="f179e-766">3</span><span class="sxs-lookup"><span data-stu-id="f179e-766">3</span></span>      | <span data-ttu-id="f179e-767">控制台</span><span class="sxs-lookup"><span data-stu-id="f179e-767">Console</span></span>       | <span data-ttu-id="f179e-768">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="f179e-768">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="f179e-769">调试</span><span class="sxs-lookup"><span data-stu-id="f179e-769">Debug</span></span>             |
| <span data-ttu-id="f179e-770">4</span><span class="sxs-lookup"><span data-stu-id="f179e-770">4</span></span>      | <span data-ttu-id="f179e-771">控制台</span><span class="sxs-lookup"><span data-stu-id="f179e-771">Console</span></span>       | <span data-ttu-id="f179e-772">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="f179e-772">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="f179e-773">错误</span><span class="sxs-lookup"><span data-stu-id="f179e-773">Error</span></span>             |
| <span data-ttu-id="f179e-774">5</span><span class="sxs-lookup"><span data-stu-id="f179e-774">5</span></span>      | <span data-ttu-id="f179e-775">控制台</span><span class="sxs-lookup"><span data-stu-id="f179e-775">Console</span></span>       | <span data-ttu-id="f179e-776">全部类别</span><span class="sxs-lookup"><span data-stu-id="f179e-776">All categories</span></span>                          | <span data-ttu-id="f179e-777">信息</span><span class="sxs-lookup"><span data-stu-id="f179e-777">Information</span></span>       |
| <span data-ttu-id="f179e-778">6</span><span class="sxs-lookup"><span data-stu-id="f179e-778">6</span></span>      | <span data-ttu-id="f179e-779">全部提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-779">All providers</span></span> | <span data-ttu-id="f179e-780">全部类别</span><span class="sxs-lookup"><span data-stu-id="f179e-780">All categories</span></span>                          | <span data-ttu-id="f179e-781">调试</span><span class="sxs-lookup"><span data-stu-id="f179e-781">Debug</span></span>             |
| <span data-ttu-id="f179e-782">7</span><span class="sxs-lookup"><span data-stu-id="f179e-782">7</span></span>      | <span data-ttu-id="f179e-783">全部提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-783">All providers</span></span> | <span data-ttu-id="f179e-784">System</span><span class="sxs-lookup"><span data-stu-id="f179e-784">System</span></span>                                  | <span data-ttu-id="f179e-785">调试</span><span class="sxs-lookup"><span data-stu-id="f179e-785">Debug</span></span>             |
| <span data-ttu-id="f179e-786">8</span><span class="sxs-lookup"><span data-stu-id="f179e-786">8</span></span>      | <span data-ttu-id="f179e-787">调试</span><span class="sxs-lookup"><span data-stu-id="f179e-787">Debug</span></span>         | <span data-ttu-id="f179e-788">Microsoft</span><span class="sxs-lookup"><span data-stu-id="f179e-788">Microsoft</span></span>                               | <span data-ttu-id="f179e-789">跟踪</span><span class="sxs-lookup"><span data-stu-id="f179e-789">Trace</span></span>             |

<span data-ttu-id="f179e-790">创建 `ILogger` 对象时，`ILoggerFactory` 对象将根据提供程序选择一条规则，将其应用于该记录器。</span><span class="sxs-lookup"><span data-stu-id="f179e-790">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="f179e-791">将按所选规则筛选 `ILogger` 实例写入的所有消息。</span><span class="sxs-lookup"><span data-stu-id="f179e-791">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="f179e-792">从可用规则中为每个提供程序和类别对选择最具体的规则。</span><span class="sxs-lookup"><span data-stu-id="f179e-792">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="f179e-793">在为给定的类别创建 `ILogger` 时，以下算法将用于每个提供程序：</span><span class="sxs-lookup"><span data-stu-id="f179e-793">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="f179e-794">选择匹配提供程序或其别名的所有规则。</span><span class="sxs-lookup"><span data-stu-id="f179e-794">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="f179e-795">如果找不到任何匹配项，则选择提供程序为空的所有规则。</span><span class="sxs-lookup"><span data-stu-id="f179e-795">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="f179e-796">根据上一步的结果，选择具有最长匹配类别前缀的规则。</span><span class="sxs-lookup"><span data-stu-id="f179e-796">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="f179e-797">如果找不到任何匹配项，则选择未指定类别的所有规则。</span><span class="sxs-lookup"><span data-stu-id="f179e-797">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="f179e-798">如果选择了多条规则，则采用最后一条  。</span><span class="sxs-lookup"><span data-stu-id="f179e-798">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="f179e-799">如果未选择任何规则，则使用 `MinimumLevel`。</span><span class="sxs-lookup"><span data-stu-id="f179e-799">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="f179e-800">假设你使用上述规则列表为类别“Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine”创建了 `ILogger` 对象：</span><span class="sxs-lookup"><span data-stu-id="f179e-800">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="f179e-801">对于调试提供程序，规则 1、6 和 8 适用。</span><span class="sxs-lookup"><span data-stu-id="f179e-801">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="f179e-802">规则 8 最为具体，因此选择了它。</span><span class="sxs-lookup"><span data-stu-id="f179e-802">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="f179e-803">对于控制台提供程序，符合的有规则 3、规则 4、规则 5 和规则 6。</span><span class="sxs-lookup"><span data-stu-id="f179e-803">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="f179e-804">规则 3 最为具体。</span><span class="sxs-lookup"><span data-stu-id="f179e-804">Rule 3 is most specific.</span></span>

<span data-ttu-id="f179e-805">生成的 `ILogger` 实例将 `Trace` 级别及更高级别的日志发送到调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-805">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="f179e-806">`Debug` 级别及更高级别的日志会发送到控制台提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-806">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="f179e-807">提供程序别名</span><span class="sxs-lookup"><span data-stu-id="f179e-807">Provider aliases</span></span>

<span data-ttu-id="f179e-808">每个提供程序都定义了一个别名；可在配置中使用该别名来代替完全限定的类型名称  。</span><span class="sxs-lookup"><span data-stu-id="f179e-808">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="f179e-809">对于内置提供程序，请使用以下别名：</span><span class="sxs-lookup"><span data-stu-id="f179e-809">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="f179e-810">控制台</span><span class="sxs-lookup"><span data-stu-id="f179e-810">Console</span></span>
* <span data-ttu-id="f179e-811">调试</span><span class="sxs-lookup"><span data-stu-id="f179e-811">Debug</span></span>
* <span data-ttu-id="f179e-812">EventSource</span><span class="sxs-lookup"><span data-stu-id="f179e-812">EventSource</span></span>
* <span data-ttu-id="f179e-813">EventLog</span><span class="sxs-lookup"><span data-stu-id="f179e-813">EventLog</span></span>
* <span data-ttu-id="f179e-814">TraceSource</span><span class="sxs-lookup"><span data-stu-id="f179e-814">TraceSource</span></span>
* <span data-ttu-id="f179e-815">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="f179e-815">AzureAppServicesFile</span></span>
* <span data-ttu-id="f179e-816">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="f179e-816">AzureAppServicesBlob</span></span>
* <span data-ttu-id="f179e-817">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="f179e-817">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="f179e-818">默认最低级别</span><span class="sxs-lookup"><span data-stu-id="f179e-818">Default minimum level</span></span>

<span data-ttu-id="f179e-819">仅当配置或代码中的规则对给定提供程序和类别都不适用时，最低级别设置才会生效。</span><span class="sxs-lookup"><span data-stu-id="f179e-819">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="f179e-820">下面的示例演示如何设置最低级别：</span><span class="sxs-lookup"><span data-stu-id="f179e-820">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="f179e-821">如果没有明确设置最低级别，则默认值为 `Information`，它表示 `Trace` 和 `Debug` 日志将被忽略。</span><span class="sxs-lookup"><span data-stu-id="f179e-821">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="f179e-822">筛选器函数</span><span class="sxs-lookup"><span data-stu-id="f179e-822">Filter functions</span></span>

<span data-ttu-id="f179e-823">对配置或代码没有向其分配规则的所有提供程序和类别调用筛选器函数。</span><span class="sxs-lookup"><span data-stu-id="f179e-823">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="f179e-824">函数中的代码可访问提供程序类型、类别和日志级别。</span><span class="sxs-lookup"><span data-stu-id="f179e-824">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="f179e-825">例如：</span><span class="sxs-lookup"><span data-stu-id="f179e-825">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="f179e-826">系统类别和级别</span><span class="sxs-lookup"><span data-stu-id="f179e-826">System categories and levels</span></span>

<span data-ttu-id="f179e-827">下面是 ASP.NET Core 和 Entity Framework Core 使用的一些类别，备注中说明了可从这些类别获取的具体日志：</span><span class="sxs-lookup"><span data-stu-id="f179e-827">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="f179e-828">类别</span><span class="sxs-lookup"><span data-stu-id="f179e-828">Category</span></span>                            | <span data-ttu-id="f179e-829">说明</span><span class="sxs-lookup"><span data-stu-id="f179e-829">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="f179e-830">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="f179e-830">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="f179e-831">常规 ASP.NET Core 诊断。</span><span class="sxs-lookup"><span data-stu-id="f179e-831">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="f179e-832">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="f179e-832">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="f179e-833">考虑、找到并使用了哪些密钥。</span><span class="sxs-lookup"><span data-stu-id="f179e-833">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="f179e-834">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="f179e-834">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="f179e-835">所允许的主机。</span><span class="sxs-lookup"><span data-stu-id="f179e-835">Hosts allowed.</span></span> |
| <span data-ttu-id="f179e-836">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="f179e-836">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="f179e-837">HTTP 请求完成的时间和启动时间。</span><span class="sxs-lookup"><span data-stu-id="f179e-837">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="f179e-838">加载了哪些承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="f179e-838">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="f179e-839">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="f179e-839">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="f179e-840">MVC 和 Razor 诊断。</span><span class="sxs-lookup"><span data-stu-id="f179e-840">MVC and Razor diagnostics.</span></span> <span data-ttu-id="f179e-841">模型绑定、筛选器执行、视图编译和操作选择。</span><span class="sxs-lookup"><span data-stu-id="f179e-841">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="f179e-842">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="f179e-842">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="f179e-843">路由匹配信息。</span><span class="sxs-lookup"><span data-stu-id="f179e-843">Route matching information.</span></span> |
| <span data-ttu-id="f179e-844">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="f179e-844">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="f179e-845">连接启动、停止和保持活动响应。</span><span class="sxs-lookup"><span data-stu-id="f179e-845">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="f179e-846">HTTP 证书信息。</span><span class="sxs-lookup"><span data-stu-id="f179e-846">HTTPS certificate information.</span></span> |
| <span data-ttu-id="f179e-847">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="f179e-847">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="f179e-848">提供的文件。</span><span class="sxs-lookup"><span data-stu-id="f179e-848">Files served.</span></span> |
| <span data-ttu-id="f179e-849">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="f179e-849">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="f179e-850">常规 Entity Framework Core 诊断。</span><span class="sxs-lookup"><span data-stu-id="f179e-850">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="f179e-851">数据库活动和配置、更改检测、迁移。</span><span class="sxs-lookup"><span data-stu-id="f179e-851">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="f179e-852">日志作用域</span><span class="sxs-lookup"><span data-stu-id="f179e-852">Log scopes</span></span>

 <span data-ttu-id="f179e-853">“作用域”可对一组逻辑操作分组  。</span><span class="sxs-lookup"><span data-stu-id="f179e-853">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="f179e-854">此分组可用于将相同的数据附加到作为集合的一部分而创建的每个日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-854">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="f179e-855">例如，在处理事务期间创建的每个日志都可包括事务 ID。</span><span class="sxs-lookup"><span data-stu-id="f179e-855">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="f179e-856">范围是由 <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> 方法返回的 `IDisposable` 类型，持续至释放为止。</span><span class="sxs-lookup"><span data-stu-id="f179e-856">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="f179e-857">要使用作用域，请在 `using` 块中包装记录器调用：</span><span class="sxs-lookup"><span data-stu-id="f179e-857">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="f179e-858">下列代码为控制台提供程序启用作用域：</span><span class="sxs-lookup"><span data-stu-id="f179e-858">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="f179e-859">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="f179e-859">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="f179e-860">要启用基于作用域的日志记录，必须先配置 `IncludeScopes` 控制台记录器选项。</span><span class="sxs-lookup"><span data-stu-id="f179e-860">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="f179e-861">若要了解关配置，请参阅[配置](#configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="f179e-861">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="f179e-862">每条日志消息都包含作用域内的信息：</span><span class="sxs-lookup"><span data-stu-id="f179e-862">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="f179e-863">内置日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-863">Built-in logging providers</span></span>

<span data-ttu-id="f179e-864">ASP.NET Core 提供以下提供程序：</span><span class="sxs-lookup"><span data-stu-id="f179e-864">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="f179e-865">控制台</span><span class="sxs-lookup"><span data-stu-id="f179e-865">Console</span></span>](#console-provider)
* [<span data-ttu-id="f179e-866">调试</span><span class="sxs-lookup"><span data-stu-id="f179e-866">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="f179e-867">EventSource</span><span class="sxs-lookup"><span data-stu-id="f179e-867">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="f179e-868">EventLog</span><span class="sxs-lookup"><span data-stu-id="f179e-868">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="f179e-869">TraceSource</span><span class="sxs-lookup"><span data-stu-id="f179e-869">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="f179e-870">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="f179e-870">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="f179e-871">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="f179e-871">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="f179e-872">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="f179e-872">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="f179e-873">要通过 ASP.NET Core 模块了解 stdout 和调试日志记录，请参阅 <xref:test/troubleshoot-azure-iis> 和 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="f179e-873">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="f179e-874">控制台提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-874">Console provider</span></span>

<span data-ttu-id="f179e-875">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 提供程序包向控制台发送日志输出。</span><span class="sxs-lookup"><span data-stu-id="f179e-875">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="f179e-876">要查看控制台日志记录输出，请在项目文件夹中打开命令提示符并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f179e-876">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="f179e-877">调试提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-877">Debug provider</span></span>

<span data-ttu-id="f179e-878">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 提供程序包使用 [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) 类（`Debug.WriteLine` 方法调用）来写入日志输出。</span><span class="sxs-lookup"><span data-stu-id="f179e-878">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="f179e-879">在 Linux 中，此提供程序将日志写入 /var/log/message  。</span><span class="sxs-lookup"><span data-stu-id="f179e-879">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="f179e-880">事件源提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-880">Event Source provider</span></span>

<span data-ttu-id="f179e-881">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) 提供程序包会使用名称 `Microsoft-Extensions-Logging` 跨平台写入事件源。</span><span class="sxs-lookup"><span data-stu-id="f179e-881">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="f179e-882">在 Windows 上，提供程序使用的是 [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)。</span><span class="sxs-lookup"><span data-stu-id="f179e-882">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="f179e-883">在调用 `CreateDefaultBuilder` 来生成主机时，会自动添加事件源提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-883">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="f179e-884">使用 [PerfView 实用工具](https://github.com/Microsoft/perfview)收集和查看日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-884">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="f179e-885">虽然其他工具也可以查看 ETW 日志，但在处理由 ASP.NET Core 发出的 ETW 事件时，使用 PerfView 能获得最佳体验。</span><span class="sxs-lookup"><span data-stu-id="f179e-885">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="f179e-886">要将 PerfView 配置为收集此提供程序记录的事件，请向 Additional Providers 列表添加字符串 `*Microsoft-Extensions-Logging` 。</span><span class="sxs-lookup"><span data-stu-id="f179e-886">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="f179e-887">（请勿遗漏字符串起始处的星号。）</span><span class="sxs-lookup"><span data-stu-id="f179e-887">(Don't miss the asterisk at the start of the string.)</span></span>

![其他 Perfview 提供程序](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="f179e-889">Windows EventLog 提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-889">Windows EventLog provider</span></span>

<span data-ttu-id="f179e-890">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 提供程序包向 Windows 事件日志发送日志输出。</span><span class="sxs-lookup"><span data-stu-id="f179e-890">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="f179e-891">[AddEventLog 重载](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)允许传入 <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>。</span><span class="sxs-lookup"><span data-stu-id="f179e-891">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="f179e-892">如果为 `null` 或未指定，则使用以下默认设置：</span><span class="sxs-lookup"><span data-stu-id="f179e-892">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="f179e-893">`LogName` &ndash; "Application"</span><span class="sxs-lookup"><span data-stu-id="f179e-893">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="f179e-894">`SourceName` &ndash; ".NET Runtime"</span><span class="sxs-lookup"><span data-stu-id="f179e-894">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="f179e-895">`MachineName` &ndash; local machine</span><span class="sxs-lookup"><span data-stu-id="f179e-895">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="f179e-896">为[警告等级和更高等级](#log-level)记录事件。</span><span class="sxs-lookup"><span data-stu-id="f179e-896">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="f179e-897">若要记录低于 `Warning` 的事件，请显式设置日志级别。</span><span class="sxs-lookup"><span data-stu-id="f179e-897">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="f179e-898">例如，将以下内容添加到 appsettings 文件中  ：</span><span class="sxs-lookup"><span data-stu-id="f179e-898">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="f179e-899">TraceSource 提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-899">TraceSource provider</span></span>

<span data-ttu-id="f179e-900">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) 提供程序包使用 <xref:System.Diagnostics.TraceSource> 库和提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-900">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="f179e-901">[AddTraceSource 重载](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) 允许传入资源开关和跟踪侦听器。</span><span class="sxs-lookup"><span data-stu-id="f179e-901">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="f179e-902">要使用此提供程序，应用必须在 .NET Framework（而非 .NET Core）上运行。</span><span class="sxs-lookup"><span data-stu-id="f179e-902">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="f179e-903">提供程序可将消息路由到多个[侦听器](/dotnet/framework/debug-trace-profile/trace-listeners)，例如示例应用中使用的 <xref:System.Diagnostics.TextWriterTraceListener>。</span><span class="sxs-lookup"><span data-stu-id="f179e-903">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="f179e-904">Azure 应用服务提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-904">Azure App Service provider</span></span>

<span data-ttu-id="f179e-905">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 提供程序包将日志写入 Azure App Service 应用的文件系统，以及 Azure 存储帐户中的 [blob 存储](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)。</span><span class="sxs-lookup"><span data-stu-id="f179e-905">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="f179e-906">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中不包括此提供程序包。</span><span class="sxs-lookup"><span data-stu-id="f179e-906">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="f179e-907">如果面向 .NET Framework 或引用 `Microsoft.AspNetCore.App` 元包，请向项目添加提供程序包。</span><span class="sxs-lookup"><span data-stu-id="f179e-907">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="f179e-908"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> 重载允许传入 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>。</span><span class="sxs-lookup"><span data-stu-id="f179e-908">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="f179e-909">设置对象可以覆盖默认设置，例如日志记录输出模板、blob 名称和文件大小限制。</span><span class="sxs-lookup"><span data-stu-id="f179e-909">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="f179e-910">（“输出模板”是一个消息模板，除了通过 `ILogger` 方法调用提供的内容之外，还可将其应用于所有日志。  ）</span><span class="sxs-lookup"><span data-stu-id="f179e-910">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="f179e-911">在部署应用服务应用时，应用程序将采用 Azure 门户中“应用服务”页面下的[应用服务日志](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag)部分的设置  。</span><span class="sxs-lookup"><span data-stu-id="f179e-911">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="f179e-912">更新以下设置后，更改立即生效，无需重启或重新部署应用。</span><span class="sxs-lookup"><span data-stu-id="f179e-912">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="f179e-913">应用程序日志记录(Filesystem) </span><span class="sxs-lookup"><span data-stu-id="f179e-913">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="f179e-914">应用程序日志记录(Blob) </span><span class="sxs-lookup"><span data-stu-id="f179e-914">**Application Logging (Blob)**</span></span>

<span data-ttu-id="f179e-915">日志文件的默认位置是 D:\\home\\LogFiles\\Application 文件夹，默认文件名为 diagnostics-yyyymmdd.txt   。</span><span class="sxs-lookup"><span data-stu-id="f179e-915">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="f179e-916">默认文件大小上限为 10 MB，默认最大保留文件数为 2。</span><span class="sxs-lookup"><span data-stu-id="f179e-916">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="f179e-917">默认 blob 名为 {app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt  。</span><span class="sxs-lookup"><span data-stu-id="f179e-917">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="f179e-918">该提供程序仅当项目在 Azure 环境中运行时有效。</span><span class="sxs-lookup"><span data-stu-id="f179e-918">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="f179e-919">项目在本地运行时，该提供程序无效 &mdash; 它不会写入本地文件或 blob 的本地开发存储。</span><span class="sxs-lookup"><span data-stu-id="f179e-919">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="f179e-920">Azure 日志流式处理</span><span class="sxs-lookup"><span data-stu-id="f179e-920">Azure log streaming</span></span>

<span data-ttu-id="f179e-921">通过 Azure 日志流式处理，可从以下位置实时查看日志活动：</span><span class="sxs-lookup"><span data-stu-id="f179e-921">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="f179e-922">应用服务器</span><span class="sxs-lookup"><span data-stu-id="f179e-922">The app server</span></span>
* <span data-ttu-id="f179e-923">Web 服务器</span><span class="sxs-lookup"><span data-stu-id="f179e-923">The web server</span></span>
* <span data-ttu-id="f179e-924">请求跟踪失败</span><span class="sxs-lookup"><span data-stu-id="f179e-924">Failed request tracing</span></span>

<span data-ttu-id="f179e-925">要配置 Azure 日志流式处理，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f179e-925">To configure Azure log streaming:</span></span>

* <span data-ttu-id="f179e-926">从应用的门户页导航到“应用服务日志”页  。</span><span class="sxs-lookup"><span data-stu-id="f179e-926">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="f179e-927">将“应用程序日志记录(Filesystem)”设置为“开”   。</span><span class="sxs-lookup"><span data-stu-id="f179e-927">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="f179e-928">选择日志级别  。</span><span class="sxs-lookup"><span data-stu-id="f179e-928">Choose the log **Level**.</span></span> <span data-ttu-id="f179e-929">此设置仅适用于 Azure 日志流，不适用于应用中的其他日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-929">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="f179e-930">导航到“日志流”页面来查看应用消息  。</span><span class="sxs-lookup"><span data-stu-id="f179e-930">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="f179e-931">它们由应用通过 `ILogger` 接口记录。</span><span class="sxs-lookup"><span data-stu-id="f179e-931">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="f179e-932">Azure Application Insights 跟踪日志记录</span><span class="sxs-lookup"><span data-stu-id="f179e-932">Azure Application Insights trace logging</span></span>

<span data-ttu-id="f179e-933">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) 提供程序包将日志写入 Azure Application Insights。</span><span class="sxs-lookup"><span data-stu-id="f179e-933">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="f179e-934">Application Insights 是一项服务，可监视 Web 应用并提供用于查询和分析遥测数据的工具。</span><span class="sxs-lookup"><span data-stu-id="f179e-934">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="f179e-935">如果使用此提供程序，则可以使用 Application Insights 工具来查询和分析日志。</span><span class="sxs-lookup"><span data-stu-id="f179e-935">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="f179e-936">日志记录提供程序作为 [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)（这是提供 ASP.NET Core 的所有可用遥测的包）的依赖项包括在内。</span><span class="sxs-lookup"><span data-stu-id="f179e-936">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="f179e-937">如果使用此包，则无需安装提供程序包。</span><span class="sxs-lookup"><span data-stu-id="f179e-937">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="f179e-938">请勿使用用于 ASP.NET 4.x 的 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) 包&mdash;。</span><span class="sxs-lookup"><span data-stu-id="f179e-938">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="f179e-939">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="f179e-939">For more information, see the following resources:</span></span>

* [<span data-ttu-id="f179e-940">Application Insights 概述</span><span class="sxs-lookup"><span data-stu-id="f179e-940">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="f179e-941">[用于 ASP.NET Core 应用程序的 Application Insights](/azure/azure-monitor/app/asp-net-core) - 如果想要实现各种 Application Insights 遥测以及日志记录，请从这里开始。</span><span class="sxs-lookup"><span data-stu-id="f179e-941">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="f179e-942">[.NET Core ILogger 日志的 ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - 如果想要在没有其他 Application Insights 遥测的情况下实现日志记录提供程序，请从这里开始。</span><span class="sxs-lookup"><span data-stu-id="f179e-942">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="f179e-943">[Application Insights 日志记录适配器](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)。</span><span class="sxs-lookup"><span data-stu-id="f179e-943">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="f179e-944">[安装、配置和初始化 Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn 网站上的交互式教程。</span><span class="sxs-lookup"><span data-stu-id="f179e-944">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="f179e-945">第三方日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="f179e-945">Third-party logging providers</span></span>

<span data-ttu-id="f179e-946">适用于 ASP.NET Core 的第三方日志记录框架：</span><span class="sxs-lookup"><span data-stu-id="f179e-946">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="f179e-947">[elmah.io](https://elmah.io/)（[GitHub 存储库](https://github.com/elmahio/Elmah.Io.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="f179e-947">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="f179e-948">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html)（[GitHub 存储库](https://github.com/mattwcole/gelf-extensions-logging)）</span><span class="sxs-lookup"><span data-stu-id="f179e-948">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="f179e-949">[JSNLog](https://jsnlog.com/)（[GitHub 存储库](https://github.com/mperdeck/jsnlog)）</span><span class="sxs-lookup"><span data-stu-id="f179e-949">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="f179e-950">[KissLog.net](https://kisslog.net/)（[GitHub 存储库](https://github.com/catalingavan/KissLog-net)）</span><span class="sxs-lookup"><span data-stu-id="f179e-950">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="f179e-951">[Log4Net](https://logging.apache.org/log4net/)（[GitHub 存储库](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore)）</span><span class="sxs-lookup"><span data-stu-id="f179e-951">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="f179e-952">[Loggr](https://loggr.net/)（[GitHub 存储库](https://github.com/imobile3/Loggr.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="f179e-952">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="f179e-953">[NLog](https://nlog-project.org/)（[GitHub 存储库](https://github.com/NLog/NLog.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="f179e-953">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="f179e-954">[Sentry](https://sentry.io/welcome/)（[GitHub 存储库](https://github.com/getsentry/sentry-dotnet)）</span><span class="sxs-lookup"><span data-stu-id="f179e-954">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="f179e-955">[Serilog](https://serilog.net/)（[GitHub 存储库](https://github.com/serilog/serilog-aspnetcore)）</span><span class="sxs-lookup"><span data-stu-id="f179e-955">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="f179e-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging)（[Github 存储库](https://github.com/googleapis/google-cloud-dotnet)）</span><span class="sxs-lookup"><span data-stu-id="f179e-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="f179e-957">某些第三方框架可以执行[语义日志记录（又称结构化日志记录）](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="f179e-957">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="f179e-958">使用第三方框架类似于使用以下内置提供程序之一：</span><span class="sxs-lookup"><span data-stu-id="f179e-958">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="f179e-959">将 NuGet 包添加到你的项目。</span><span class="sxs-lookup"><span data-stu-id="f179e-959">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="f179e-960">调用日志记录框架提供的 `ILoggerFactory` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="f179e-960">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="f179e-961">有关详细信息，请参阅各提供程序的相关文档。</span><span class="sxs-lookup"><span data-stu-id="f179e-961">For more information, see each provider's documentation.</span></span> <span data-ttu-id="f179e-962">Microsoft 不支持第三方日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="f179e-962">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f179e-963">其他资源</span><span class="sxs-lookup"><span data-stu-id="f179e-963">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
