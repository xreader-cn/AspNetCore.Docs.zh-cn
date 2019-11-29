---
title: .NET Core 和 ASP.NET Core 中的日志记录
author: rick-anderson
description: 了解如何使用由 Microsoft Extension.Logging NuGet 包提供的日志记录框架。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/19/2019
uid: fundamentals/logging/index
ms.openlocfilehash: 23ce2d09d2ce9f415ce71bcd7c21c29cb2a040fc
ms.sourcegitcommit: 918d7000b48a2892750264b852bad9e96a1165a7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2019
ms.locfileid: "74550365"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="b3408-103">.NET Core 和 ASP.NET Core 中的日志记录</span><span class="sxs-lookup"><span data-stu-id="b3408-103">Logging in .NET Core and ASP.NET Core</span></span>

<span data-ttu-id="b3408-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b3408-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b3408-105">.NET Core 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。</span><span class="sxs-lookup"><span data-stu-id="b3408-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="b3408-106">本文介绍了如何将日志记录 API 与内置提供程序一起使用。</span><span class="sxs-lookup"><span data-stu-id="b3408-106">This article shows how to use the logging API with built-in providers.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b3408-107">本文中所述的大多数代码示例都来自 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="b3408-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="b3408-108">这些代码片段的日志记录特定部分适用于任何使用[通用主机](xref:fundamentals/host/generic-host)的 .NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="b3408-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="b3408-109">有关如何在非 Web 控制台应用中使用通用主机的信息，请参阅[托管服务](xref:fundamentals/host/hosted-services)。</span><span class="sxs-lookup"><span data-stu-id="b3408-109">For information about how to use the Generic Host in non-web console apps, see [Hosted services](xref:fundamentals/host/hosted-services).</span></span>

<span data-ttu-id="b3408-110">对于没有通用主机的应用，日志记录代码在[添加提供程序](#add-providers)和[创建记录器](#create-logs)的方式上有所不同。</span><span class="sxs-lookup"><span data-stu-id="b3408-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="b3408-111">本文的这些部分显示了非主机代码示例。</span><span class="sxs-lookup"><span data-stu-id="b3408-111">Non-host code examples are shown in those sections of the article.</span></span>

::: moniker-end

<span data-ttu-id="b3408-112">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="b3408-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="b3408-113">添加提供程序</span><span class="sxs-lookup"><span data-stu-id="b3408-113">Add providers</span></span>

<span data-ttu-id="b3408-114">日志记录提供程序会显示或存储日志。</span><span class="sxs-lookup"><span data-stu-id="b3408-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="b3408-115">例如，控制台提供程序在控制台上显示日志，Azure Application Insights 提供程序会将这些日志存储在 Azure Application Insights 中。</span><span class="sxs-lookup"><span data-stu-id="b3408-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="b3408-116">可通过添加多个提供程序将日志发送到多个目标。</span><span class="sxs-lookup"><span data-stu-id="b3408-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b3408-117">若要在使用通用主机的应用中添加提供程序，请在 Program.cs 中调用提供程序的 `Add{provider name}` 扩展方法  ：</span><span class="sxs-lookup"><span data-stu-id="b3408-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="b3408-118">在非主机控制台应用中，在创建 `LoggerFactory` 时调用提供程序的 `Add{provider name}` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="b3408-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="b3408-119">`LoggerFactory` 和 `AddConsole` 需要用于 `Microsoft.Extensions.Logging` 的 `using` 语句。</span><span class="sxs-lookup"><span data-stu-id="b3408-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="b3408-120">默认 ASP.NET Core 项目模板调用 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>，该操作将添加以下日志记录提供程序：</span><span class="sxs-lookup"><span data-stu-id="b3408-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="b3408-121">控制台</span><span class="sxs-lookup"><span data-stu-id="b3408-121">Console</span></span>
* <span data-ttu-id="b3408-122">调试</span><span class="sxs-lookup"><span data-stu-id="b3408-122">Debug</span></span>
* <span data-ttu-id="b3408-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="b3408-123">EventSource</span></span>
* <span data-ttu-id="b3408-124">EventLog（仅当在 Windows 上运行时）</span><span class="sxs-lookup"><span data-stu-id="b3408-124">EventLog (only when running on Windows)</span></span>

<span data-ttu-id="b3408-125">可自行选择提供程序来替换默认提供程序。</span><span class="sxs-lookup"><span data-stu-id="b3408-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="b3408-126">调用 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>，然后添加所需的提供程序。</span><span class="sxs-lookup"><span data-stu-id="b3408-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0 "

<span data-ttu-id="b3408-127">要添加提供程序，请在 Program.cs 中调用提供程序的 `Add{provider name}` 扩展方法  ：</span><span class="sxs-lookup"><span data-stu-id="b3408-127">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="b3408-128">前面的代码需要引用 `Microsoft.Extensions.Logging` 和 `Microsoft.Extensions.Configuration`。</span><span class="sxs-lookup"><span data-stu-id="b3408-128">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="b3408-129">默认项目模板调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>，该操作将添加以下日志记录提供程序：</span><span class="sxs-lookup"><span data-stu-id="b3408-129">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="b3408-130">控制台</span><span class="sxs-lookup"><span data-stu-id="b3408-130">Console</span></span>
* <span data-ttu-id="b3408-131">调试</span><span class="sxs-lookup"><span data-stu-id="b3408-131">Debug</span></span>
* <span data-ttu-id="b3408-132">EventSource（从 ASP.NET Core 2.2 开始）</span><span class="sxs-lookup"><span data-stu-id="b3408-132">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="b3408-133">如果使用 `CreateDefaultBuilder`，则可自行选择提供程序来替换默认提供程序。</span><span class="sxs-lookup"><span data-stu-id="b3408-133">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="b3408-134">调用 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>，然后添加所需的提供程序。</span><span class="sxs-lookup"><span data-stu-id="b3408-134">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

<span data-ttu-id="b3408-135">详细了解[内置日志记录提供程序](#built-in-logging-providers)，以及本文稍后部分介绍的[第三方日志记录提供程序](#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="b3408-135">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="b3408-136">创建日志</span><span class="sxs-lookup"><span data-stu-id="b3408-136">Create logs</span></span>

<span data-ttu-id="b3408-137">若要创建日志，请使用 <xref:Microsoft.Extensions.Logging.ILogger%601> 对象。</span><span class="sxs-lookup"><span data-stu-id="b3408-137">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="b3408-138">在 Web 应用或托管服务中，由依赖关系注入 (DI) 获取 `ILogger`。</span><span class="sxs-lookup"><span data-stu-id="b3408-138">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="b3408-139">在非主机控制台应用中，使用 `LoggerFactory` 来创建 `ILogger`。</span><span class="sxs-lookup"><span data-stu-id="b3408-139">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="b3408-140">下面的 ASP.NET Core 示例创建了一个以 `TodoApiSample.Pages.AboutModel` 为类别的记录器。</span><span class="sxs-lookup"><span data-stu-id="b3408-140">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="b3408-141">日志“类别”是与每个日志关联的字符串  。</span><span class="sxs-lookup"><span data-stu-id="b3408-141">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="b3408-142">DI 提供的 `ILogger<T>` 实例创建日志，该日志以类型 `T` 的完全限定名称为类别。</span><span class="sxs-lookup"><span data-stu-id="b3408-142">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="b3408-143">下面的非主机控制台应用示例创建了一个以 `LoggingConsoleApp.Program` 为类别的记录器。</span><span class="sxs-lookup"><span data-stu-id="b3408-143">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

::: moniker-end

<span data-ttu-id="b3408-144">在下面的 ASP.NET Core 和控制台应用示例中，记录器用于创建以 `Information` 为级别的日志。</span><span class="sxs-lookup"><span data-stu-id="b3408-144">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="b3408-145">日志“级别”代表所记录事件的严重程度  。</span><span class="sxs-lookup"><span data-stu-id="b3408-145">The Log *level* indicates the severity of the logged event.</span></span> 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

<span data-ttu-id="b3408-146">本文稍后部分将更详细地介绍[级别](#log-level)和[类别](#log-category)。</span><span class="sxs-lookup"><span data-stu-id="b3408-146">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span> 

::: moniker range=">= aspnetcore-3.0"

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="b3408-147">在 Program 类中创建日志</span><span class="sxs-lookup"><span data-stu-id="b3408-147">Create logs in the Program class</span></span>

<span data-ttu-id="b3408-148">若要将日志写入 ASP.NET Core 应用的 `Program` 类中，请在生成主机后从 DI 获取 `ILogger`实例：</span><span class="sxs-lookup"><span data-stu-id="b3408-148">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="b3408-149">不直接支持在主机构造期间进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="b3408-149">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="b3408-150">但是，可以使用单独的记录器。</span><span class="sxs-lookup"><span data-stu-id="b3408-150">However, a separate logger can be used.</span></span> <span data-ttu-id="b3408-151">在以下示例中，[Serilog](https://serilog.net/) 记录器用于登录 `CreateHostBuilder`。</span><span class="sxs-lookup"><span data-stu-id="b3408-151">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="b3408-152">`AddSerilog` 使用 `Log.Logger` 中指定的静态配置：</span><span class="sxs-lookup"><span data-stu-id="b3408-152">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="b3408-153">在 Startup 类中创建日志</span><span class="sxs-lookup"><span data-stu-id="b3408-153">Create logs in the Startup class</span></span>

<span data-ttu-id="b3408-154">若要将日志写入 ASP.NET Core 应用的 `Startup.Configure` 方法中，请在方法签名中包含 `ILogger` 参数：</span><span class="sxs-lookup"><span data-stu-id="b3408-154">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="b3408-155">不支持在 `Startup.ConfigureServices` 方法中完成 DI 容器设置之前就写入日志：</span><span class="sxs-lookup"><span data-stu-id="b3408-155">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="b3408-156">不支持将记录器注入到 `Startup` 构造函数中。</span><span class="sxs-lookup"><span data-stu-id="b3408-156">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="b3408-157">不支持将记录器注入到 `Startup.ConfigureServices` 方法签名中</span><span class="sxs-lookup"><span data-stu-id="b3408-157">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="b3408-158">这一限制的原因是，日志记录依赖于 DI 和配置，而配置又依赖于 DI。</span><span class="sxs-lookup"><span data-stu-id="b3408-158">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="b3408-159">在完成 `ConfigureServices` 之前，不会设置 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="b3408-159">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="b3408-160">由于为 Web 主机创建了单独的 DI 容器，所以在 ASP.NET Core 的早期版本中，构造函数将记录器注入到 `Startup` 工作。</span><span class="sxs-lookup"><span data-stu-id="b3408-160">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="b3408-161">若要了解为何仅为通用主机创建一个容器，请参阅[重大更改公告](https://github.com/aspnet/Announcements/issues/353)。</span><span class="sxs-lookup"><span data-stu-id="b3408-161">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="b3408-162">如果需要配置依赖于 `ILogger<T>` 的服务，则仍可通过使用构造函数注入或提供工厂方法的方式来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="b3408-162">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="b3408-163">只有在没有其他选择的情况下，才建议使用工厂方法。</span><span class="sxs-lookup"><span data-stu-id="b3408-163">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="b3408-164">例如，假设你需要由 DI 为服务填充属性：</span><span class="sxs-lookup"><span data-stu-id="b3408-164">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="b3408-165">前面突出显示的代码是 `Func`，该代码在 DI 容器第一次需要构造 `MyService` 实例时运行。</span><span class="sxs-lookup"><span data-stu-id="b3408-165">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="b3408-166">可以用这种方式访问任何已注册的服务。</span><span class="sxs-lookup"><span data-stu-id="b3408-166">You can access any of the registered services in this way.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="create-logs-in-startup"></a><span data-ttu-id="b3408-167">启动时创建日志</span><span class="sxs-lookup"><span data-stu-id="b3408-167">Create logs in Startup</span></span>

<span data-ttu-id="b3408-168">要将日志写入 `Startup` 类，构造函数签名需包含 `ILogger` 参数：</span><span class="sxs-lookup"><span data-stu-id="b3408-168">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="b3408-169">在 Program 类中创建日志</span><span class="sxs-lookup"><span data-stu-id="b3408-169">Create logs in the Program class</span></span>

<span data-ttu-id="b3408-170">要将日志写入 `Program` 类，请从 DI 获取 `ILogger` 实例：</span><span class="sxs-lookup"><span data-stu-id="b3408-170">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="b3408-171">不直接支持在主机构造期间进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="b3408-171">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="b3408-172">但是，可以使用单独的记录器。</span><span class="sxs-lookup"><span data-stu-id="b3408-172">However, a separate logger can be used.</span></span> <span data-ttu-id="b3408-173">在以下示例中，[Serilog](https://serilog.net/) 记录器用于登录 `CreateWebHostBuilder`。</span><span class="sxs-lookup"><span data-stu-id="b3408-173">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="b3408-174">`AddSerilog` 使用 `Log.Logger` 中指定的静态配置：</span><span class="sxs-lookup"><span data-stu-id="b3408-174">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="b3408-175">没有异步记录器方法</span><span class="sxs-lookup"><span data-stu-id="b3408-175">No asynchronous logger methods</span></span>

<span data-ttu-id="b3408-176">日志记录应该会很快，不值得牺牲性能来使用异步代码。</span><span class="sxs-lookup"><span data-stu-id="b3408-176">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="b3408-177">如果你的日志数据存储很慢，请不要直接写入它。</span><span class="sxs-lookup"><span data-stu-id="b3408-177">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="b3408-178">首先考虑将日志消息写入快速存储，稍后再将其变为慢速存储。</span><span class="sxs-lookup"><span data-stu-id="b3408-178">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="b3408-179">例如，如果你要记录到 SQL Server，你可能不想直接在 `Log` 方法中记录，因为 `Log` 方法是同步的。</span><span class="sxs-lookup"><span data-stu-id="b3408-179">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="b3408-180">相反，你会将日志消息同步添加到内存中的队列，并让后台辅助线程从队列中拉出消息，以完成将数据推送到 SQL Server 的异步工作。</span><span class="sxs-lookup"><span data-stu-id="b3408-180">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="b3408-181">有关详细信息，请参阅[此](https://github.com/aspnet/AspNetCore.Docs/issues/11801) GitHub 问题。</span><span class="sxs-lookup"><span data-stu-id="b3408-181">For more information, see [this](https://github.com/aspnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="b3408-182">配置</span><span class="sxs-lookup"><span data-stu-id="b3408-182">Configuration</span></span>

<span data-ttu-id="b3408-183">日志记录提供程序配置由一个或多个配置提供程序提供：</span><span class="sxs-lookup"><span data-stu-id="b3408-183">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="b3408-184">文件格式（INI、JSON 和 XML）。</span><span class="sxs-lookup"><span data-stu-id="b3408-184">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="b3408-185">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="b3408-185">Command-line arguments.</span></span>
* <span data-ttu-id="b3408-186">环境变量。</span><span class="sxs-lookup"><span data-stu-id="b3408-186">Environment variables.</span></span>
* <span data-ttu-id="b3408-187">内存中的 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="b3408-187">In-memory .NET objects.</span></span>
* <span data-ttu-id="b3408-188">未加密的[机密管理器](xref:security/app-secrets)存储。</span><span class="sxs-lookup"><span data-stu-id="b3408-188">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="b3408-189">加密的用户存储，如 [Azure Key Vault](xref:security/key-vault-configuration)。</span><span class="sxs-lookup"><span data-stu-id="b3408-189">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="b3408-190">（已安装或已创建的）自定义提供程序。</span><span class="sxs-lookup"><span data-stu-id="b3408-190">Custom providers (installed or created).</span></span>

<span data-ttu-id="b3408-191">例如，日志记录配置通常由应用设置文件的 `Logging` 部分提供。</span><span class="sxs-lookup"><span data-stu-id="b3408-191">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="b3408-192">以下示例显示了典型 *appsettings.Development.json* 文件的内容：</span><span class="sxs-lookup"><span data-stu-id="b3408-192">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="b3408-193">`Logging` 属性可具有 `LogLevel` 和日志提供程序属性（显示控制台）。</span><span class="sxs-lookup"><span data-stu-id="b3408-193">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="b3408-194">`Logging` 下的 `LogLevel` 属性指定了用于记录所选类别的最低[级别](#log-level)。</span><span class="sxs-lookup"><span data-stu-id="b3408-194">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="b3408-195">在本例中，`System` 和 `Microsoft` 类别在 `Information` 级别记录，其他均在 `Debug` 级别记录。</span><span class="sxs-lookup"><span data-stu-id="b3408-195">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="b3408-196">`Logging` 下的其他属性均指定了日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="b3408-196">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="b3408-197">本示例针对控制台提供程序。</span><span class="sxs-lookup"><span data-stu-id="b3408-197">The example is for the Console provider.</span></span> <span data-ttu-id="b3408-198">如果提供程序支持[日志作用域](#log-scopes)，则 `IncludeScopes` 将指示是否启用这些域。</span><span class="sxs-lookup"><span data-stu-id="b3408-198">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="b3408-199">提供程序属性（例如本例的 `Console`）也可指定 `LogLevel` 属性。</span><span class="sxs-lookup"><span data-stu-id="b3408-199">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="b3408-200">提供程序下的 `LogLevel` 指定了该提供程序记录的级别。</span><span class="sxs-lookup"><span data-stu-id="b3408-200">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="b3408-201">如果在 `Logging.{providername}.LogLevel` 中指定了级别，则这些级别将重写 `Logging.LogLevel` 中设置的所有内容。</span><span class="sxs-lookup"><span data-stu-id="b3408-201">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="b3408-202">不可使用日志记录 API 在应用运行时更改日志记录。</span><span class="sxs-lookup"><span data-stu-id="b3408-202">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="b3408-203">但是，一些配置提供程序可重新加载配置，这将对日志记录配置立即产生影响。</span><span class="sxs-lookup"><span data-stu-id="b3408-203">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="b3408-204">例如，[文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)会默认重新加载日志记录配置，该程序由 `CreateDefaultBuilder` 添加用来读取设置文件。</span><span class="sxs-lookup"><span data-stu-id="b3408-204">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="b3408-205">如果在应用运行时在代码中更改了配置，则该应用可调用 [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) 来更新应用的日志记录配置。</span><span class="sxs-lookup"><span data-stu-id="b3408-205">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="b3408-206">若要了解如何实现配置提供程序，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="b3408-206">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="b3408-207">日志记录输出示例</span><span class="sxs-lookup"><span data-stu-id="b3408-207">Sample logging output</span></span>

<span data-ttu-id="b3408-208">使用上一部分中显示的示例代码从命令行运行应用时，将在控制台中看到日志。</span><span class="sxs-lookup"><span data-stu-id="b3408-208">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="b3408-209">以下是控制台输出示例：</span><span class="sxs-lookup"><span data-stu-id="b3408-209">Here's an example of console output:</span></span>

::: moniker range=">= aspnetcore-3.0"

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

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

::: moniker-end

<span data-ttu-id="b3408-210">通过向 `http://localhost:5000/api/todo/0` 处的示例应用发出 HTTP Get 请求来生成前述日志。</span><span class="sxs-lookup"><span data-stu-id="b3408-210">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="b3408-211">在 Visual Studio 中运行示例应用时，“调试”窗口中将显示如下日志：</span><span class="sxs-lookup"><span data-stu-id="b3408-211">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

::: moniker range=">= aspnetcore-3.0"

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

<span data-ttu-id="b3408-212">由上一部分所示的 `ILogger` 调用创建的日志以“TodoApiSample”开头。</span><span class="sxs-lookup"><span data-stu-id="b3408-212">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="b3408-213">以“Microsoft”类别开头的日志来自 ASP.NET Core 框架代码。</span><span class="sxs-lookup"><span data-stu-id="b3408-213">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="b3408-214">ASP.NET Core 和应用程序代码使用相同的日志记录 API 和提供程序。</span><span class="sxs-lookup"><span data-stu-id="b3408-214">ASP.NET Core and application code are using the same logging API and providers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="b3408-215">由上一部分所示的 `ILogger` 调用创建的日志以“TodoApi”开头。</span><span class="sxs-lookup"><span data-stu-id="b3408-215">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="b3408-216">以“Microsoft”类别开头的日志来自 ASP.NET Core 框架代码。</span><span class="sxs-lookup"><span data-stu-id="b3408-216">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="b3408-217">ASP.NET Core 和应用程序代码使用相同的日志记录 API 和提供程序。</span><span class="sxs-lookup"><span data-stu-id="b3408-217">ASP.NET Core and application code are using the same logging API and providers.</span></span>

::: moniker-end

<span data-ttu-id="b3408-218">本文余下部分将介绍有关日志记录的某些详细信息及选项。</span><span class="sxs-lookup"><span data-stu-id="b3408-218">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="b3408-219">NuGet 包</span><span class="sxs-lookup"><span data-stu-id="b3408-219">NuGet packages</span></span>

<span data-ttu-id="b3408-220">`ILogger` 和 `ILoggerFactory` 接口位于 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 中，其默认实现位于 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 中。</span><span class="sxs-lookup"><span data-stu-id="b3408-220">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="b3408-221">日志类别</span><span class="sxs-lookup"><span data-stu-id="b3408-221">Log category</span></span>

<span data-ttu-id="b3408-222">创建 `ILogger` 对象后，将为其指定“类别”  。</span><span class="sxs-lookup"><span data-stu-id="b3408-222">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="b3408-223">该类别包含在由此 `ILogger` 实例创建的每条日志消息中。</span><span class="sxs-lookup"><span data-stu-id="b3408-223">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="b3408-224">类别可以是任何字符串，但约定需使用类名，例如“TodoApi.Controllers.TodoController”。</span><span class="sxs-lookup"><span data-stu-id="b3408-224">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="b3408-225">使用 `ILogger<T>` 获取一个 `ILogger` 实例，该实例使用 `T` 的完全限定类型名称作为类别：</span><span class="sxs-lookup"><span data-stu-id="b3408-225">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

<span data-ttu-id="b3408-226">要显式指定类别，请调用 `ILoggerFactory.CreateLogger`：</span><span class="sxs-lookup"><span data-stu-id="b3408-226">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

<span data-ttu-id="b3408-227">`ILogger<T>` 相当于使用 `T` 的完全限定类型名称来调用 `CreateLogger`。</span><span class="sxs-lookup"><span data-stu-id="b3408-227">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="b3408-228">日志级别</span><span class="sxs-lookup"><span data-stu-id="b3408-228">Log level</span></span>

<span data-ttu-id="b3408-229">每个日志都指定了一个 <xref:Microsoft.Extensions.Logging.LogLevel> 值。</span><span class="sxs-lookup"><span data-stu-id="b3408-229">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="b3408-230">日志级别指示严重性或重要程度。</span><span class="sxs-lookup"><span data-stu-id="b3408-230">The log level indicates the severity or importance.</span></span> <span data-ttu-id="b3408-231">例如，可在方法正常结束时写入 `Information` 日志，在方法返回“404 找不到”状态代码时写入 `Warning` 日志  。</span><span class="sxs-lookup"><span data-stu-id="b3408-231">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="b3408-232">下面的代码会创建 `Information` 和 `Warning` 日志：</span><span class="sxs-lookup"><span data-stu-id="b3408-232">The following code creates `Information` and `Warning` logs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="b3408-233">在上述代码中，第一个参数是[日志事件 ID](#log-event-id)。</span><span class="sxs-lookup"><span data-stu-id="b3408-233">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="b3408-234">第二个参数是消息模板，其中的占位符用于填写剩余方法形参提供的实参值。</span><span class="sxs-lookup"><span data-stu-id="b3408-234">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="b3408-235">稍后将在本文的[消息模板部分](#log-message-template)介绍方法参数。</span><span class="sxs-lookup"><span data-stu-id="b3408-235">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="b3408-236">在方法名称中包含级别的日志方法（例如 `LogInformation` 和 `LogWarning`）是 [ILogger 的扩展方法](xref:Microsoft.Extensions.Logging.LoggerExtensions)。</span><span class="sxs-lookup"><span data-stu-id="b3408-236">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="b3408-237">这些方法会调用可接受 `LogLevel` 参数的 `Log` 方法。</span><span class="sxs-lookup"><span data-stu-id="b3408-237">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="b3408-238">可直接调用 `Log` 方法而不调用其中某个扩展方法，但语法相对复杂。</span><span class="sxs-lookup"><span data-stu-id="b3408-238">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="b3408-239">有关详细信息，请参阅 <xref:Microsoft.Extensions.Logging.ILogger> 和[记录器扩展源代码](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)。</span><span class="sxs-lookup"><span data-stu-id="b3408-239">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="b3408-240">ASP.NET Core 定义了以下日志级别（按严重性从低到高排列）。</span><span class="sxs-lookup"><span data-stu-id="b3408-240">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="b3408-241">跟踪 = 0</span><span class="sxs-lookup"><span data-stu-id="b3408-241">Trace = 0</span></span>

  <span data-ttu-id="b3408-242">有关通常仅用于调试的信息。</span><span class="sxs-lookup"><span data-stu-id="b3408-242">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="b3408-243">这些消息可能包含敏感应用程序数据，因此不得在生产环境中启用它们。</span><span class="sxs-lookup"><span data-stu-id="b3408-243">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="b3408-244">默认情况下禁用。 </span><span class="sxs-lookup"><span data-stu-id="b3408-244">*Disabled by default.*</span></span>

* <span data-ttu-id="b3408-245">调试 = 1</span><span class="sxs-lookup"><span data-stu-id="b3408-245">Debug = 1</span></span>

  <span data-ttu-id="b3408-246">有关在开发和调试中可能有用的信息。</span><span class="sxs-lookup"><span data-stu-id="b3408-246">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="b3408-247">示例：`Entering method Configure with flag set to true.` 由于日志数量过多，因此仅当执行故障排除时，才在生产中启用 `Debug` 级别日志。</span><span class="sxs-lookup"><span data-stu-id="b3408-247">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="b3408-248">信息 = 2</span><span class="sxs-lookup"><span data-stu-id="b3408-248">Information = 2</span></span>

  <span data-ttu-id="b3408-249">用于跟踪应用的常规流。</span><span class="sxs-lookup"><span data-stu-id="b3408-249">For tracking the general flow of the app.</span></span> <span data-ttu-id="b3408-250">这些日志通常有长期价值。</span><span class="sxs-lookup"><span data-stu-id="b3408-250">These logs typically have some long-term value.</span></span> <span data-ttu-id="b3408-251">示例：`Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="b3408-251">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="b3408-252">警告 = 3</span><span class="sxs-lookup"><span data-stu-id="b3408-252">Warning = 3</span></span>

  <span data-ttu-id="b3408-253">表示应用流中的异常或意外事件。</span><span class="sxs-lookup"><span data-stu-id="b3408-253">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="b3408-254">可能包括不会中断应用运行但仍需调查的错误或其他条件。</span><span class="sxs-lookup"><span data-stu-id="b3408-254">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="b3408-255">`Warning` 日志级别常用于已处理的异常。</span><span class="sxs-lookup"><span data-stu-id="b3408-255">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="b3408-256">示例：`FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="b3408-256">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="b3408-257">错误 = 4</span><span class="sxs-lookup"><span data-stu-id="b3408-257">Error = 4</span></span>

  <span data-ttu-id="b3408-258">表示无法处理的错误和异常。</span><span class="sxs-lookup"><span data-stu-id="b3408-258">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="b3408-259">这些消息指示的是当前活动或操作（例如当前 HTTP 请求）中的失败，而不是整个应用中的失败。</span><span class="sxs-lookup"><span data-stu-id="b3408-259">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="b3408-260">日志消息示例：`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="b3408-260">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="b3408-261">严重 = 5</span><span class="sxs-lookup"><span data-stu-id="b3408-261">Critical = 5</span></span>

  <span data-ttu-id="b3408-262">需要立即关注的失败。</span><span class="sxs-lookup"><span data-stu-id="b3408-262">For failures that require immediate attention.</span></span> <span data-ttu-id="b3408-263">例如数据丢失、磁盘空间不足。</span><span class="sxs-lookup"><span data-stu-id="b3408-263">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="b3408-264">使用日志级别控制写入到特定存储介质或显示窗口的日志输出量。</span><span class="sxs-lookup"><span data-stu-id="b3408-264">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="b3408-265">例如:</span><span class="sxs-lookup"><span data-stu-id="b3408-265">For example:</span></span>

* <span data-ttu-id="b3408-266">生产中：</span><span class="sxs-lookup"><span data-stu-id="b3408-266">In production:</span></span>
  * <span data-ttu-id="b3408-267">如果通过 `Information` 级别在 `Trace` 处记录，则会生成大量详细的日志消息。</span><span class="sxs-lookup"><span data-stu-id="b3408-267">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="b3408-268">为控制成本且不超过数据存储限制，请通过 `Information` 级别消息将 `Trace` 记录到容量大、成本低的数据存储中。</span><span class="sxs-lookup"><span data-stu-id="b3408-268">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="b3408-269">如果通过 `Critical` 级别在 `Warning` 处记录，通常生成的日志消息更少且更小。</span><span class="sxs-lookup"><span data-stu-id="b3408-269">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="b3408-270">因此，成本和存储限制通常不是问题，而这使得在选择数据信息时更为灵活。</span><span class="sxs-lookup"><span data-stu-id="b3408-270">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="b3408-271">在开发过程中：</span><span class="sxs-lookup"><span data-stu-id="b3408-271">During development:</span></span>
  * <span data-ttu-id="b3408-272">通过 `Critical` 消息将 `Warning` 记录到控制台。</span><span class="sxs-lookup"><span data-stu-id="b3408-272">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="b3408-273">在疑难解答时通过 `Information` 消息添加 `Trace`。</span><span class="sxs-lookup"><span data-stu-id="b3408-273">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="b3408-274">本文稍后的[日志筛选](#log-filtering)部分介绍如何控制提供程序处理的日志级别。</span><span class="sxs-lookup"><span data-stu-id="b3408-274">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="b3408-275">ASP.NET Core 为框架事件写入日志。</span><span class="sxs-lookup"><span data-stu-id="b3408-275">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="b3408-276">本文前面部分提供的日志示例排除了低于 `Information` 级别的日志，因此未创建 `Debug` 或 `Trace` 级别日志。</span><span class="sxs-lookup"><span data-stu-id="b3408-276">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="b3408-277">以下示例介绍了通过运行配置为显示 `Debug` 日志的示例应用而生成的控制台日志：</span><span class="sxs-lookup"><span data-stu-id="b3408-277">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

::: moniker range=">= aspnetcore-3.0"

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

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

::: moniker-end

## <a name="log-event-id"></a><span data-ttu-id="b3408-278">日志事件 ID</span><span class="sxs-lookup"><span data-stu-id="b3408-278">Log event ID</span></span>

<span data-ttu-id="b3408-279">每个日志都可指定一个事件 ID  。</span><span class="sxs-lookup"><span data-stu-id="b3408-279">Each log can specify an *event ID*.</span></span> <span data-ttu-id="b3408-280">该示例应用通过使用本地定义的 `LoggingEvents` 类来执行此操作：</span><span class="sxs-lookup"><span data-stu-id="b3408-280">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

<span data-ttu-id="b3408-281">事件 ID 与一组事件相关联。</span><span class="sxs-lookup"><span data-stu-id="b3408-281">An event ID associates a set of events.</span></span> <span data-ttu-id="b3408-282">例如，与在页面上显示项列表相关的所有日志可能是 1001。</span><span class="sxs-lookup"><span data-stu-id="b3408-282">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="b3408-283">日志记录提供程序可将事件 ID 存储在 ID 字段中，存储在日志记录消息中，或者不进行存储。</span><span class="sxs-lookup"><span data-stu-id="b3408-283">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="b3408-284">调试提供程序不显示事件 ID。</span><span class="sxs-lookup"><span data-stu-id="b3408-284">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="b3408-285">控制台提供程序在类别后的括号中显示事件 ID：</span><span class="sxs-lookup"><span data-stu-id="b3408-285">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="b3408-286">日志消息模板</span><span class="sxs-lookup"><span data-stu-id="b3408-286">Log message template</span></span>

<span data-ttu-id="b3408-287">每个日志都会指定一个消息模板。</span><span class="sxs-lookup"><span data-stu-id="b3408-287">Each log specifies a message template.</span></span> <span data-ttu-id="b3408-288">消息模板可包含要填写参数的占位符。</span><span class="sxs-lookup"><span data-stu-id="b3408-288">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="b3408-289">请在占位符中使用名称而不是数字。</span><span class="sxs-lookup"><span data-stu-id="b3408-289">Use names for the placeholders, not numbers.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="b3408-290">占位符的顺序（而非其名称）决定了为其提供值的参数。</span><span class="sxs-lookup"><span data-stu-id="b3408-290">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="b3408-291">在以下代码中，请注意消息模板中的参数名称未按顺序排列：</span><span class="sxs-lookup"><span data-stu-id="b3408-291">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="b3408-292">此代码创建了一个参数值按顺序排列的日志消息：</span><span class="sxs-lookup"><span data-stu-id="b3408-292">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="b3408-293">日志记录框架按此方式工作，这样日志记录提供程序即可实现[语义日志记录，也称为结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="b3408-293">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="b3408-294">参数本身会传递给日志记录系统，而不仅仅是格式化的消息模板。</span><span class="sxs-lookup"><span data-stu-id="b3408-294">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="b3408-295">通过此信息，日志记录提供程序能够将参数值存储为字段。</span><span class="sxs-lookup"><span data-stu-id="b3408-295">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="b3408-296">例如，假设记录器方法调用如下所示：</span><span class="sxs-lookup"><span data-stu-id="b3408-296">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="b3408-297">如果要将日志发送到 Azure 表存储，则每个 Azure 表实体都可具有 `ID` 和 `RequestTime` 属性，这简化了对日志数据的查询。</span><span class="sxs-lookup"><span data-stu-id="b3408-297">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="b3408-298">无需分析文本消息的超时，查询即可找到特定 `RequestTime` 范围内的全部日志。</span><span class="sxs-lookup"><span data-stu-id="b3408-298">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="b3408-299">日志记录异常</span><span class="sxs-lookup"><span data-stu-id="b3408-299">Logging exceptions</span></span>

<span data-ttu-id="b3408-300">记录器方法有可传入异常的重载，如下方示例所示：</span><span class="sxs-lookup"><span data-stu-id="b3408-300">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

<span data-ttu-id="b3408-301">不同的提供程序处理异常信息的方式不同。</span><span class="sxs-lookup"><span data-stu-id="b3408-301">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="b3408-302">以下是上示代码的调试提供程序输出示例。</span><span class="sxs-lookup"><span data-stu-id="b3408-302">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="b3408-303">日志筛选</span><span class="sxs-lookup"><span data-stu-id="b3408-303">Log filtering</span></span>

<span data-ttu-id="b3408-304">可为特定或所有提供程序和类别指定最低日志级别。</span><span class="sxs-lookup"><span data-stu-id="b3408-304">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="b3408-305">最低级别以下的日志不会传递给该提供程序，因此不会显示或存储它们。</span><span class="sxs-lookup"><span data-stu-id="b3408-305">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="b3408-306">要禁止显示所有日志，可将 `LogLevel.None` 指定为最低日志级别。</span><span class="sxs-lookup"><span data-stu-id="b3408-306">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="b3408-307">`LogLevel.None` 的整数值为 6，它大于 `LogLevel.Critical` (5)。</span><span class="sxs-lookup"><span data-stu-id="b3408-307">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="b3408-308">在配置中创建筛选规则</span><span class="sxs-lookup"><span data-stu-id="b3408-308">Create filter rules in configuration</span></span>

<span data-ttu-id="b3408-309">项目模板代码调用 `CreateDefaultBuilder` 来为 Console、Debug 和 EventSource（ASP.NET Core 2.2 或更高版本）提供程序设置日志记录。</span><span class="sxs-lookup"><span data-stu-id="b3408-309">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="b3408-310">正如[本文前面部分](#configuration)所述，`CreateDefaultBuilder` 方法设置日志记录以在 `Logging` 部分查找配置。</span><span class="sxs-lookup"><span data-stu-id="b3408-310">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="b3408-311">配置数据按提供程序和类别指定最低日志级别，如下方示例所示：</span><span class="sxs-lookup"><span data-stu-id="b3408-311">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

::: moniker-end

<span data-ttu-id="b3408-312">此 JSON 将创建 6 条筛选规则：1 条用于调试提供程序， 4 条用于控制台提供程序， 1 条用于所有提供程序。</span><span class="sxs-lookup"><span data-stu-id="b3408-312">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="b3408-313">创建 `ILogger` 对象时，为每个提供程序选择一个规则。</span><span class="sxs-lookup"><span data-stu-id="b3408-313">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="b3408-314">代码中的筛选规则</span><span class="sxs-lookup"><span data-stu-id="b3408-314">Filter rules in code</span></span>

<span data-ttu-id="b3408-315">下面的示例演示了如何在代码中注册筛选规则：</span><span class="sxs-lookup"><span data-stu-id="b3408-315">The following example shows how to register filter rules in code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

<span data-ttu-id="b3408-316">第二个 `AddFilter` 使用类型名称来指定调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="b3408-316">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="b3408-317">第一个 `AddFilter` 应用于全部提供程序，因为它未指定提供程序类型。</span><span class="sxs-lookup"><span data-stu-id="b3408-317">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="b3408-318">如何应用筛选规则</span><span class="sxs-lookup"><span data-stu-id="b3408-318">How filtering rules are applied</span></span>

<span data-ttu-id="b3408-319">先前示例中显示的配置数据和 `AddFilter` 代码会创建下表所示的规则。</span><span class="sxs-lookup"><span data-stu-id="b3408-319">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="b3408-320">前六条由配置示例创建，后两条由代码示例创建。</span><span class="sxs-lookup"><span data-stu-id="b3408-320">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="b3408-321">数字</span><span class="sxs-lookup"><span data-stu-id="b3408-321">Number</span></span> | <span data-ttu-id="b3408-322">提供程序</span><span class="sxs-lookup"><span data-stu-id="b3408-322">Provider</span></span>      | <span data-ttu-id="b3408-323">类别的开头为...</span><span class="sxs-lookup"><span data-stu-id="b3408-323">Categories that begin with ...</span></span>          | <span data-ttu-id="b3408-324">最低日志级别</span><span class="sxs-lookup"><span data-stu-id="b3408-324">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="b3408-325">1</span><span class="sxs-lookup"><span data-stu-id="b3408-325">1</span></span>      | <span data-ttu-id="b3408-326">调试</span><span class="sxs-lookup"><span data-stu-id="b3408-326">Debug</span></span>         | <span data-ttu-id="b3408-327">全部类别</span><span class="sxs-lookup"><span data-stu-id="b3408-327">All categories</span></span>                          | <span data-ttu-id="b3408-328">信息</span><span class="sxs-lookup"><span data-stu-id="b3408-328">Information</span></span>       |
| <span data-ttu-id="b3408-329">2</span><span class="sxs-lookup"><span data-stu-id="b3408-329">2</span></span>      | <span data-ttu-id="b3408-330">控制台</span><span class="sxs-lookup"><span data-stu-id="b3408-330">Console</span></span>       | <span data-ttu-id="b3408-331">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="b3408-331">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="b3408-332">警告</span><span class="sxs-lookup"><span data-stu-id="b3408-332">Warning</span></span>           |
| <span data-ttu-id="b3408-333">3</span><span class="sxs-lookup"><span data-stu-id="b3408-333">3</span></span>      | <span data-ttu-id="b3408-334">控制台</span><span class="sxs-lookup"><span data-stu-id="b3408-334">Console</span></span>       | <span data-ttu-id="b3408-335">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="b3408-335">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="b3408-336">调试</span><span class="sxs-lookup"><span data-stu-id="b3408-336">Debug</span></span>             |
| <span data-ttu-id="b3408-337">4</span><span class="sxs-lookup"><span data-stu-id="b3408-337">4</span></span>      | <span data-ttu-id="b3408-338">控制台</span><span class="sxs-lookup"><span data-stu-id="b3408-338">Console</span></span>       | <span data-ttu-id="b3408-339">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="b3408-339">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="b3408-340">错误</span><span class="sxs-lookup"><span data-stu-id="b3408-340">Error</span></span>             |
| <span data-ttu-id="b3408-341">5</span><span class="sxs-lookup"><span data-stu-id="b3408-341">5</span></span>      | <span data-ttu-id="b3408-342">控制台</span><span class="sxs-lookup"><span data-stu-id="b3408-342">Console</span></span>       | <span data-ttu-id="b3408-343">全部类别</span><span class="sxs-lookup"><span data-stu-id="b3408-343">All categories</span></span>                          | <span data-ttu-id="b3408-344">信息</span><span class="sxs-lookup"><span data-stu-id="b3408-344">Information</span></span>       |
| <span data-ttu-id="b3408-345">6</span><span class="sxs-lookup"><span data-stu-id="b3408-345">6</span></span>      | <span data-ttu-id="b3408-346">全部提供程序</span><span class="sxs-lookup"><span data-stu-id="b3408-346">All providers</span></span> | <span data-ttu-id="b3408-347">全部类别</span><span class="sxs-lookup"><span data-stu-id="b3408-347">All categories</span></span>                          | <span data-ttu-id="b3408-348">调试</span><span class="sxs-lookup"><span data-stu-id="b3408-348">Debug</span></span>             |
| <span data-ttu-id="b3408-349">7</span><span class="sxs-lookup"><span data-stu-id="b3408-349">7</span></span>      | <span data-ttu-id="b3408-350">全部提供程序</span><span class="sxs-lookup"><span data-stu-id="b3408-350">All providers</span></span> | <span data-ttu-id="b3408-351">System</span><span class="sxs-lookup"><span data-stu-id="b3408-351">System</span></span>                                  | <span data-ttu-id="b3408-352">调试</span><span class="sxs-lookup"><span data-stu-id="b3408-352">Debug</span></span>             |
| <span data-ttu-id="b3408-353">8</span><span class="sxs-lookup"><span data-stu-id="b3408-353">8</span></span>      | <span data-ttu-id="b3408-354">调试</span><span class="sxs-lookup"><span data-stu-id="b3408-354">Debug</span></span>         | <span data-ttu-id="b3408-355">Microsoft</span><span class="sxs-lookup"><span data-stu-id="b3408-355">Microsoft</span></span>                               | <span data-ttu-id="b3408-356">跟踪</span><span class="sxs-lookup"><span data-stu-id="b3408-356">Trace</span></span>             |

<span data-ttu-id="b3408-357">创建 `ILogger` 对象时，`ILoggerFactory` 对象将根据提供程序选择一条规则，将其应用于该记录器。</span><span class="sxs-lookup"><span data-stu-id="b3408-357">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="b3408-358">将按所选规则筛选 `ILogger` 实例写入的所有消息。</span><span class="sxs-lookup"><span data-stu-id="b3408-358">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="b3408-359">从可用规则中为每个提供程序和类别对选择最具体的规则。</span><span class="sxs-lookup"><span data-stu-id="b3408-359">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="b3408-360">在为给定的类别创建 `ILogger` 时，以下算法将用于每个提供程序：</span><span class="sxs-lookup"><span data-stu-id="b3408-360">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="b3408-361">选择匹配提供程序或其别名的所有规则。</span><span class="sxs-lookup"><span data-stu-id="b3408-361">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="b3408-362">如果找不到任何匹配项，则选择提供程序为空的所有规则。</span><span class="sxs-lookup"><span data-stu-id="b3408-362">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="b3408-363">根据上一步的结果，选择具有最长匹配类别前缀的规则。</span><span class="sxs-lookup"><span data-stu-id="b3408-363">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="b3408-364">如果找不到任何匹配项，则选择未指定类别的所有规则。</span><span class="sxs-lookup"><span data-stu-id="b3408-364">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="b3408-365">如果选择了多条规则，则采用最后一条  。</span><span class="sxs-lookup"><span data-stu-id="b3408-365">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="b3408-366">如果未选择任何规则，则使用 `MinimumLevel`。</span><span class="sxs-lookup"><span data-stu-id="b3408-366">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="b3408-367">假设你使用上述规则列表为类别“Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine”创建了 `ILogger` 对象：</span><span class="sxs-lookup"><span data-stu-id="b3408-367">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="b3408-368">对于调试提供程序，规则 1、6 和 8 适用。</span><span class="sxs-lookup"><span data-stu-id="b3408-368">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="b3408-369">规则 8 最为具体，因此选择了它。</span><span class="sxs-lookup"><span data-stu-id="b3408-369">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="b3408-370">对于控制台提供程序，符合的有规则 3、规则 4、规则 5 和规则 6。</span><span class="sxs-lookup"><span data-stu-id="b3408-370">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="b3408-371">规则 3 最为具体。</span><span class="sxs-lookup"><span data-stu-id="b3408-371">Rule 3 is most specific.</span></span>

<span data-ttu-id="b3408-372">生成的 `ILogger` 实例将 `Trace` 级别及更高级别的日志发送到调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="b3408-372">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="b3408-373">`Debug` 级别及更高级别的日志会发送到控制台提供程序。</span><span class="sxs-lookup"><span data-stu-id="b3408-373">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="b3408-374">提供程序别名</span><span class="sxs-lookup"><span data-stu-id="b3408-374">Provider aliases</span></span>

<span data-ttu-id="b3408-375">每个提供程序都定义了一个别名；可在配置中使用该别名来代替完全限定的类型名称  。</span><span class="sxs-lookup"><span data-stu-id="b3408-375">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="b3408-376">对于内置提供程序，请使用以下别名：</span><span class="sxs-lookup"><span data-stu-id="b3408-376">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="b3408-377">控制台</span><span class="sxs-lookup"><span data-stu-id="b3408-377">Console</span></span>
* <span data-ttu-id="b3408-378">调试</span><span class="sxs-lookup"><span data-stu-id="b3408-378">Debug</span></span>
* <span data-ttu-id="b3408-379">EventSource</span><span class="sxs-lookup"><span data-stu-id="b3408-379">EventSource</span></span>
* <span data-ttu-id="b3408-380">EventLog</span><span class="sxs-lookup"><span data-stu-id="b3408-380">EventLog</span></span>
* <span data-ttu-id="b3408-381">TraceSource</span><span class="sxs-lookup"><span data-stu-id="b3408-381">TraceSource</span></span>
* <span data-ttu-id="b3408-382">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="b3408-382">AzureAppServicesFile</span></span>
* <span data-ttu-id="b3408-383">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="b3408-383">AzureAppServicesBlob</span></span>
* <span data-ttu-id="b3408-384">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="b3408-384">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="b3408-385">默认最低级别</span><span class="sxs-lookup"><span data-stu-id="b3408-385">Default minimum level</span></span>

<span data-ttu-id="b3408-386">仅当配置或代码中的规则对给定提供程序和类别都不适用时，最低级别设置才会生效。</span><span class="sxs-lookup"><span data-stu-id="b3408-386">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="b3408-387">下面的示例演示如何设置最低级别：</span><span class="sxs-lookup"><span data-stu-id="b3408-387">The following example shows how to set the minimum level:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

<span data-ttu-id="b3408-388">如果没有明确设置最低级别，则默认值为 `Information`，它表示 `Trace` 和 `Debug` 日志将被忽略。</span><span class="sxs-lookup"><span data-stu-id="b3408-388">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="b3408-389">筛选器函数</span><span class="sxs-lookup"><span data-stu-id="b3408-389">Filter functions</span></span>

<span data-ttu-id="b3408-390">对配置或代码没有向其分配规则的所有提供程序和类别调用筛选器函数。</span><span class="sxs-lookup"><span data-stu-id="b3408-390">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="b3408-391">函数中的代码可访问提供程序类型、类别和日志级别。</span><span class="sxs-lookup"><span data-stu-id="b3408-391">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="b3408-392">例如:</span><span class="sxs-lookup"><span data-stu-id="b3408-392">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

## <a name="system-categories-and-levels"></a><span data-ttu-id="b3408-393">系统类别和级别</span><span class="sxs-lookup"><span data-stu-id="b3408-393">System categories and levels</span></span>

<span data-ttu-id="b3408-394">下面是 ASP.NET Core 和 Entity Framework Core 使用的一些类别，备注中说明了可从这些类别获取的具体日志：</span><span class="sxs-lookup"><span data-stu-id="b3408-394">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="b3408-395">类别</span><span class="sxs-lookup"><span data-stu-id="b3408-395">Category</span></span>                            | <span data-ttu-id="b3408-396">说明</span><span class="sxs-lookup"><span data-stu-id="b3408-396">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="b3408-397">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="b3408-397">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="b3408-398">常规 ASP.NET Core 诊断。</span><span class="sxs-lookup"><span data-stu-id="b3408-398">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="b3408-399">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="b3408-399">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="b3408-400">考虑、找到并使用了哪些密钥。</span><span class="sxs-lookup"><span data-stu-id="b3408-400">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="b3408-401">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="b3408-401">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="b3408-402">所允许的主机。</span><span class="sxs-lookup"><span data-stu-id="b3408-402">Hosts allowed.</span></span> |
| <span data-ttu-id="b3408-403">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="b3408-403">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="b3408-404">HTTP 请求完成的时间和启动时间。</span><span class="sxs-lookup"><span data-stu-id="b3408-404">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="b3408-405">加载了哪些承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="b3408-405">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="b3408-406">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="b3408-406">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="b3408-407">MVC 和 Razor 诊断。</span><span class="sxs-lookup"><span data-stu-id="b3408-407">MVC and Razor diagnostics.</span></span> <span data-ttu-id="b3408-408">模型绑定、筛选器执行、视图编译和操作选择。</span><span class="sxs-lookup"><span data-stu-id="b3408-408">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="b3408-409">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="b3408-409">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="b3408-410">路由匹配信息。</span><span class="sxs-lookup"><span data-stu-id="b3408-410">Route matching information.</span></span> |
| <span data-ttu-id="b3408-411">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="b3408-411">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="b3408-412">连接启动、停止和保持活动响应。</span><span class="sxs-lookup"><span data-stu-id="b3408-412">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="b3408-413">HTTP 证书信息。</span><span class="sxs-lookup"><span data-stu-id="b3408-413">HTTPS certificate information.</span></span> |
| <span data-ttu-id="b3408-414">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="b3408-414">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="b3408-415">提供的文件。</span><span class="sxs-lookup"><span data-stu-id="b3408-415">Files served.</span></span> |
| <span data-ttu-id="b3408-416">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="b3408-416">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="b3408-417">常规 Entity Framework Core 诊断。</span><span class="sxs-lookup"><span data-stu-id="b3408-417">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="b3408-418">数据库活动和配置、更改检测、迁移。</span><span class="sxs-lookup"><span data-stu-id="b3408-418">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="b3408-419">日志作用域</span><span class="sxs-lookup"><span data-stu-id="b3408-419">Log scopes</span></span>

 <span data-ttu-id="b3408-420">“作用域”可对一组逻辑操作分组  。</span><span class="sxs-lookup"><span data-stu-id="b3408-420">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="b3408-421">此分组可用于将相同的数据附加到作为集合的一部分而创建的每个日志。</span><span class="sxs-lookup"><span data-stu-id="b3408-421">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="b3408-422">例如，在处理事务期间创建的每个日志都可包括事务 ID。</span><span class="sxs-lookup"><span data-stu-id="b3408-422">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="b3408-423">范围是由 <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> 方法返回的 `IDisposable` 类型，持续至释放为止。</span><span class="sxs-lookup"><span data-stu-id="b3408-423">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="b3408-424">要使用作用域，请在 `using` 块中包装记录器调用：</span><span class="sxs-lookup"><span data-stu-id="b3408-424">Use a scope by wrapping logger calls in a `using` block:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

<span data-ttu-id="b3408-425">下列代码为控制台提供程序启用作用域：</span><span class="sxs-lookup"><span data-stu-id="b3408-425">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="b3408-426">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="b3408-426">*Program.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="b3408-427">要启用基于作用域的日志记录，必须先配置 `IncludeScopes` 控制台记录器选项。</span><span class="sxs-lookup"><span data-stu-id="b3408-427">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="b3408-428">若要了解关配置，请参阅[配置](#configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="b3408-428">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="b3408-429">每条日志消息都包含作用域内的信息：</span><span class="sxs-lookup"><span data-stu-id="b3408-429">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="b3408-430">内置日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="b3408-430">Built-in logging providers</span></span>

<span data-ttu-id="b3408-431">ASP.NET Core 提供以下提供程序：</span><span class="sxs-lookup"><span data-stu-id="b3408-431">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="b3408-432">控制台</span><span class="sxs-lookup"><span data-stu-id="b3408-432">Console</span></span>](#console-provider)
* [<span data-ttu-id="b3408-433">调试</span><span class="sxs-lookup"><span data-stu-id="b3408-433">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="b3408-434">EventSource</span><span class="sxs-lookup"><span data-stu-id="b3408-434">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="b3408-435">EventLog</span><span class="sxs-lookup"><span data-stu-id="b3408-435">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="b3408-436">TraceSource</span><span class="sxs-lookup"><span data-stu-id="b3408-436">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="b3408-437">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="b3408-437">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="b3408-438">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="b3408-438">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="b3408-439">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="b3408-439">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="b3408-440">要通过 ASP.NET Core 模块了解 stdout 和调试日志记录，请参阅 <xref:test/troubleshoot-azure-iis> 和 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="b3408-440">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="b3408-441">控制台提供程序</span><span class="sxs-lookup"><span data-stu-id="b3408-441">Console provider</span></span>

<span data-ttu-id="b3408-442">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 提供程序包向控制台发送日志输出。</span><span class="sxs-lookup"><span data-stu-id="b3408-442">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="b3408-443">要查看控制台日志记录输出，请在项目文件夹中打开命令提示符并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b3408-443">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="b3408-444">调试提供程序</span><span class="sxs-lookup"><span data-stu-id="b3408-444">Debug provider</span></span>

<span data-ttu-id="b3408-445">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 提供程序包使用 [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) 类（`Debug.WriteLine` 方法调用）来写入日志输出。</span><span class="sxs-lookup"><span data-stu-id="b3408-445">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="b3408-446">在 Linux 中，此提供程序将日志写入 /var/log/message  。</span><span class="sxs-lookup"><span data-stu-id="b3408-446">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="b3408-447">事件源提供程序</span><span class="sxs-lookup"><span data-stu-id="b3408-447">Event Source provider</span></span>

<span data-ttu-id="b3408-448">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) 提供程序包会使用名称 `Microsoft-Extensions-Logging` 跨平台写入事件源。</span><span class="sxs-lookup"><span data-stu-id="b3408-448">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="b3408-449">在 Windows 上，提供程序使用的是 [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)。</span><span class="sxs-lookup"><span data-stu-id="b3408-449">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="b3408-450">在调用 `CreateDefaultBuilder` 来生成主机时，会自动添加事件源提供程序。</span><span class="sxs-lookup"><span data-stu-id="b3408-450">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

::: moniker range=">= aspnetcore-3.0"

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="b3408-451">dotnet 跟踪工具</span><span class="sxs-lookup"><span data-stu-id="b3408-451">dotnet trace tooling</span></span>

<span data-ttu-id="b3408-452">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) 工具是一种跨平台 CLI 全局工具，可用于收集正在运行的进程的 .NET Core 跟踪。</span><span class="sxs-lookup"><span data-stu-id="b3408-452">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="b3408-453">该工具会使用 <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> 收集 <xref:Microsoft.Extensions.Logging.EventSource> 提供程序数据。</span><span class="sxs-lookup"><span data-stu-id="b3408-453">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="b3408-454">使用以下命令安装 dotnet 跟踪工具：</span><span class="sxs-lookup"><span data-stu-id="b3408-454">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="b3408-455">使用 dotnet 跟踪工具从应用中收集跟踪：</span><span class="sxs-lookup"><span data-stu-id="b3408-455">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="b3408-456">如果应用不使用 `CreateDefaultBuilder` 生成主机，则请向应用的日志记录配置添加[事件源提供程序](#event-source-provider)</span><span class="sxs-lookup"><span data-stu-id="b3408-456">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="b3408-457">使用 `dotnet run` 命令运行此应用。</span><span class="sxs-lookup"><span data-stu-id="b3408-457">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="b3408-458">确定 .NET Core 应用的进程标识符 (PID)：</span><span class="sxs-lookup"><span data-stu-id="b3408-458">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="b3408-459">在 Windows 上，使用下述方法之一：</span><span class="sxs-lookup"><span data-stu-id="b3408-459">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="b3408-460">任务管理器 (Ctrl+Alt+Del)</span><span class="sxs-lookup"><span data-stu-id="b3408-460">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="b3408-461">tasklist 命令</span><span class="sxs-lookup"><span data-stu-id="b3408-461">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="b3408-462">Get-Process Powershell 命令</span><span class="sxs-lookup"><span data-stu-id="b3408-462">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="b3408-463">在 Linux 上，使用 [pidof 命令](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)。</span><span class="sxs-lookup"><span data-stu-id="b3408-463">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="b3408-464">找到进程的 PID，它与应用的程序集的名称相同。</span><span class="sxs-lookup"><span data-stu-id="b3408-464">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="b3408-465">执行 `dotnet trace` 命令。</span><span class="sxs-lookup"><span data-stu-id="b3408-465">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="b3408-466">常规命令语法：</span><span class="sxs-lookup"><span data-stu-id="b3408-466">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="b3408-467">使用 PowerShell 命令行界面时，将 `--providers` 值用单引号 (`'`) 引起来：</span><span class="sxs-lookup"><span data-stu-id="b3408-467">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="b3408-468">在非 Windows 平台上，添加 `-f speedscope` 选项，将输出跟踪文件更改为 `speedscope`。</span><span class="sxs-lookup"><span data-stu-id="b3408-468">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="b3408-469">关键字</span><span class="sxs-lookup"><span data-stu-id="b3408-469">Keyword</span></span> | <span data-ttu-id="b3408-470">说明</span><span class="sxs-lookup"><span data-stu-id="b3408-470">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="b3408-471">1</span><span class="sxs-lookup"><span data-stu-id="b3408-471">1</span></span>       | <span data-ttu-id="b3408-472">记录有关 `LoggingEventSource` 的 meta 事件。</span><span class="sxs-lookup"><span data-stu-id="b3408-472">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="b3408-473">请不要从 `ILogger` 记录事件。</span><span class="sxs-lookup"><span data-stu-id="b3408-473">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="b3408-474">2</span><span class="sxs-lookup"><span data-stu-id="b3408-474">2</span></span>       | <span data-ttu-id="b3408-475">在调用 `ILogger.Log()` 时启用 `Message` 事件。</span><span class="sxs-lookup"><span data-stu-id="b3408-475">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="b3408-476">以编程（未格式化）方式提供信息。</span><span class="sxs-lookup"><span data-stu-id="b3408-476">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="b3408-477">4</span><span class="sxs-lookup"><span data-stu-id="b3408-477">4</span></span>       | <span data-ttu-id="b3408-478">在调用 `ILogger.Log()` 时启用 `FormatMessage` 事件。</span><span class="sxs-lookup"><span data-stu-id="b3408-478">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="b3408-479">提供格式化字符串版本的信息。</span><span class="sxs-lookup"><span data-stu-id="b3408-479">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="b3408-480">8</span><span class="sxs-lookup"><span data-stu-id="b3408-480">8</span></span>       | <span data-ttu-id="b3408-481">在调用 `ILogger.Log()` 时启用 `MessageJson` 事件。</span><span class="sxs-lookup"><span data-stu-id="b3408-481">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="b3408-482">提供参数的 JSON 表示形式。</span><span class="sxs-lookup"><span data-stu-id="b3408-482">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="b3408-483">事件级别</span><span class="sxs-lookup"><span data-stu-id="b3408-483">Event Level</span></span> | <span data-ttu-id="b3408-484">说明</span><span class="sxs-lookup"><span data-stu-id="b3408-484">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="b3408-485">0</span><span class="sxs-lookup"><span data-stu-id="b3408-485">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="b3408-486">1</span><span class="sxs-lookup"><span data-stu-id="b3408-486">1</span></span>           | `Critical`      |
   | <span data-ttu-id="b3408-487">2</span><span class="sxs-lookup"><span data-stu-id="b3408-487">2</span></span>           | `Error`         |
   | <span data-ttu-id="b3408-488">3</span><span class="sxs-lookup"><span data-stu-id="b3408-488">3</span></span>           | `Warning`       |
   | <span data-ttu-id="b3408-489">4</span><span class="sxs-lookup"><span data-stu-id="b3408-489">4</span></span>           | `Informational` |
   | <span data-ttu-id="b3408-490">5</span><span class="sxs-lookup"><span data-stu-id="b3408-490">5</span></span>           | `Verbose`       |

   <span data-ttu-id="b3408-491">`{Logger Category}` 和 `{Event Level}` 的 `FilterSpecs` 条目表示其他日志筛选条件。</span><span class="sxs-lookup"><span data-stu-id="b3408-491">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="b3408-492">请用分号 (`;`) 分隔 `FilterSpecs` 条目。</span><span class="sxs-lookup"><span data-stu-id="b3408-492">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="b3408-493">下例使用 Windows 命令界面（`--providers` 值不用单引号引起来）  ：</span><span class="sxs-lookup"><span data-stu-id="b3408-493">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="b3408-494">上面的命令会激活：</span><span class="sxs-lookup"><span data-stu-id="b3408-494">The preceding command activates:</span></span>

   * <span data-ttu-id="b3408-495">事件源记录器，它用于为错误 (`2`) 生成格式化字符串 (`4`)。</span><span class="sxs-lookup"><span data-stu-id="b3408-495">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="b3408-496">`Informational` 日志记录级别 (`4`) 的 `Microsoft.AspNetCore.Hosting` 日志记录。</span><span class="sxs-lookup"><span data-stu-id="b3408-496">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="b3408-497">通过按 Enter 键或 Ctrl+C 停止 dotnet 跟踪工具。</span><span class="sxs-lookup"><span data-stu-id="b3408-497">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="b3408-498">跟踪使用名称 trace.nettrace 保存在执行 `dotnet trace` 命令的文件夹中  。</span><span class="sxs-lookup"><span data-stu-id="b3408-498">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="b3408-499">使用[预览](#perfview)功能打开跟踪。</span><span class="sxs-lookup"><span data-stu-id="b3408-499">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="b3408-500">打开 trace.nettrace 文件并浏览跟踪事件  。</span><span class="sxs-lookup"><span data-stu-id="b3408-500">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="b3408-501">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="b3408-501">For more information, see:</span></span>

* <span data-ttu-id="b3408-502">[跟踪性能分析实用工具 (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace)（.NET Core 文档）</span><span class="sxs-lookup"><span data-stu-id="b3408-502">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="b3408-503">[跟踪性能分析实用工具 (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md)（dotnet/诊断 GitHub 存储库文档）</span><span class="sxs-lookup"><span data-stu-id="b3408-503">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="b3408-504">[LoggingEventSource 类](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource)（.NET API 浏览器）</span><span class="sxs-lookup"><span data-stu-id="b3408-504">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="b3408-505">[LoggingEventSource 引用源 (3.0)](https://github.com/aspnet/Extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; 要获取不同版本的引用源，请将分支更改为 `release/{Version}`，其中 `{Version}` 是所需的 ASP.NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="b3408-505">[LoggingEventSource reference source (3.0)](https://github.com/aspnet/Extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="b3408-506">[预览](#perfview) &ndash; 用于查看事件源跟踪。</span><span class="sxs-lookup"><span data-stu-id="b3408-506">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="b3408-507">Perfview</span><span class="sxs-lookup"><span data-stu-id="b3408-507">Perfview</span></span>

::: moniker-end

<span data-ttu-id="b3408-508">使用 [PerfView 实用工具](https://github.com/Microsoft/perfview)收集和查看日志。</span><span class="sxs-lookup"><span data-stu-id="b3408-508">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="b3408-509">虽然其他工具也可以查看 ETW 日志，但在处理由 ASP.NET Core 发出的 ETW 事件时，使用 PerfView 能获得最佳体验。</span><span class="sxs-lookup"><span data-stu-id="b3408-509">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="b3408-510">要将 PerfView 配置为收集此提供程序记录的事件，请向 Additional Providers 列表添加字符串 `*Microsoft-Extensions-Logging`  。</span><span class="sxs-lookup"><span data-stu-id="b3408-510">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="b3408-511">（请勿遗漏字符串起始处的星号。）</span><span class="sxs-lookup"><span data-stu-id="b3408-511">(Don't miss the asterisk at the start of the string.)</span></span>

![其他 Perfview 提供程序](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="b3408-513">Windows EventLog 提供程序</span><span class="sxs-lookup"><span data-stu-id="b3408-513">Windows EventLog provider</span></span>

<span data-ttu-id="b3408-514">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 提供程序包向 Windows 事件日志发送日志输出。</span><span class="sxs-lookup"><span data-stu-id="b3408-514">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="b3408-515">[AddEventLog 重载](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)允许传入 <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>。</span><span class="sxs-lookup"><span data-stu-id="b3408-515">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span>

### <a name="tracesource-provider"></a><span data-ttu-id="b3408-516">TraceSource 提供程序</span><span class="sxs-lookup"><span data-stu-id="b3408-516">TraceSource provider</span></span>

<span data-ttu-id="b3408-517">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) 提供程序包使用 <xref:System.Diagnostics.TraceSource> 库和提供程序。</span><span class="sxs-lookup"><span data-stu-id="b3408-517">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="b3408-518">[AddTraceSource 重载](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) 允许传入资源开关和跟踪侦听器。</span><span class="sxs-lookup"><span data-stu-id="b3408-518">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="b3408-519">要使用此提供程序，应用必须在 .NET Framework（而非 .NET Core）上运行。</span><span class="sxs-lookup"><span data-stu-id="b3408-519">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="b3408-520">提供程序可将消息路由到多个[侦听器](/dotnet/framework/debug-trace-profile/trace-listeners)，例如示例应用中使用的 <xref:System.Diagnostics.TextWriterTraceListener>。</span><span class="sxs-lookup"><span data-stu-id="b3408-520">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="b3408-521">Azure 应用服务提供程序</span><span class="sxs-lookup"><span data-stu-id="b3408-521">Azure App Service provider</span></span>

<span data-ttu-id="b3408-522">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 提供程序包将日志写入 Azure App Service 应用的文件系统，以及 Azure 存储帐户中的 [blob 存储](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)。</span><span class="sxs-lookup"><span data-stu-id="b3408-522">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b3408-523">共享框架中不包括该提供程序包。</span><span class="sxs-lookup"><span data-stu-id="b3408-523">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="b3408-524">若要使用提供程序，请将提供程序包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="b3408-524">To use the provider, add the provider package to the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b3408-525">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中不包括此提供程序包。</span><span class="sxs-lookup"><span data-stu-id="b3408-525">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="b3408-526">如果面向 .NET Framework 或引用 `Microsoft.AspNetCore.App` 元包，请向项目添加提供程序包。</span><span class="sxs-lookup"><span data-stu-id="b3408-526">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b3408-527">要配置提供程序设置，请使用 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> 和 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="b3408-527">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="b3408-528">要配置提供程序设置，请使用 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> 和 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="b3408-528">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="b3408-529"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> 重载允许传入 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>。</span><span class="sxs-lookup"><span data-stu-id="b3408-529">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="b3408-530">设置对象可以覆盖默认设置，例如日志记录输出模板、blob 名称和文件大小限制。</span><span class="sxs-lookup"><span data-stu-id="b3408-530">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="b3408-531">（“输出模板”是一个消息模板，除了通过 `ILogger` 方法调用提供的内容之外，还可将其应用于所有日志。  ）</span><span class="sxs-lookup"><span data-stu-id="b3408-531">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

::: moniker-end

<span data-ttu-id="b3408-532">在部署应用服务应用时，应用程序将采用 Azure 门户中“应用服务”页面下的[应用服务日志](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag)部分的设置  。</span><span class="sxs-lookup"><span data-stu-id="b3408-532">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="b3408-533">更新以下设置后，更改立即生效，无需重启或重新部署应用。</span><span class="sxs-lookup"><span data-stu-id="b3408-533">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="b3408-534">应用程序日志记录(Filesystem) </span><span class="sxs-lookup"><span data-stu-id="b3408-534">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="b3408-535">应用程序日志记录(Blob) </span><span class="sxs-lookup"><span data-stu-id="b3408-535">**Application Logging (Blob)**</span></span>

<span data-ttu-id="b3408-536">日志文件的默认位置是 D:\\home\\LogFiles\\Application 文件夹，默认文件名为 diagnostics-yyyymmdd.txt   。</span><span class="sxs-lookup"><span data-stu-id="b3408-536">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="b3408-537">默认文件大小上限为 10 MB，默认最大保留文件数为 2。</span><span class="sxs-lookup"><span data-stu-id="b3408-537">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="b3408-538">默认 blob 名为 {app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt  。</span><span class="sxs-lookup"><span data-stu-id="b3408-538">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="b3408-539">该提供程序仅当项目在 Azure 环境中运行时有效。</span><span class="sxs-lookup"><span data-stu-id="b3408-539">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="b3408-540">项目在本地运行时，该提供程序无效 &mdash; 它不会写入本地文件或 blob 的本地开发存储。</span><span class="sxs-lookup"><span data-stu-id="b3408-540">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="b3408-541">Azure 日志流式处理</span><span class="sxs-lookup"><span data-stu-id="b3408-541">Azure log streaming</span></span>

<span data-ttu-id="b3408-542">通过 Azure 日志流式处理，可从以下位置实时查看日志活动：</span><span class="sxs-lookup"><span data-stu-id="b3408-542">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="b3408-543">应用服务器</span><span class="sxs-lookup"><span data-stu-id="b3408-543">The app server</span></span>
* <span data-ttu-id="b3408-544">Web 服务器</span><span class="sxs-lookup"><span data-stu-id="b3408-544">The web server</span></span>
* <span data-ttu-id="b3408-545">请求跟踪失败</span><span class="sxs-lookup"><span data-stu-id="b3408-545">Failed request tracing</span></span>

<span data-ttu-id="b3408-546">要配置 Azure 日志流式处理，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b3408-546">To configure Azure log streaming:</span></span>

* <span data-ttu-id="b3408-547">从应用的门户页导航到“应用服务日志”页  。</span><span class="sxs-lookup"><span data-stu-id="b3408-547">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="b3408-548">将“应用程序日志记录(Filesystem)”设置为“开”   。</span><span class="sxs-lookup"><span data-stu-id="b3408-548">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="b3408-549">选择日志级别  。</span><span class="sxs-lookup"><span data-stu-id="b3408-549">Choose the log **Level**.</span></span> <span data-ttu-id="b3408-550">此设置仅适用于 Azure 日志流，不适用于应用中的其他日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="b3408-550">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="b3408-551">导航到“日志流”页面来查看应用消息  。</span><span class="sxs-lookup"><span data-stu-id="b3408-551">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="b3408-552">它们由应用通过 `ILogger` 接口记录。</span><span class="sxs-lookup"><span data-stu-id="b3408-552">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="b3408-553">Azure Application Insights 跟踪日志记录</span><span class="sxs-lookup"><span data-stu-id="b3408-553">Azure Application Insights trace logging</span></span>

<span data-ttu-id="b3408-554">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) 提供程序包将日志写入 Azure Application Insights。</span><span class="sxs-lookup"><span data-stu-id="b3408-554">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="b3408-555">Application Insights 是一项服务，可监视 Web 应用并提供用于查询和分析遥测数据的工具。</span><span class="sxs-lookup"><span data-stu-id="b3408-555">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="b3408-556">如果使用此提供程序，则可以使用 Application Insights 工具来查询和分析日志。</span><span class="sxs-lookup"><span data-stu-id="b3408-556">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="b3408-557">日志记录提供程序作为 [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)（这是提供 ASP.NET Core 的所有可用遥测的包）的依赖项包括在内。</span><span class="sxs-lookup"><span data-stu-id="b3408-557">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="b3408-558">如果使用此包，则无需安装提供程序包。</span><span class="sxs-lookup"><span data-stu-id="b3408-558">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="b3408-559">请勿使用用于 ASP.NET 4.x 的 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) 包&mdash;。</span><span class="sxs-lookup"><span data-stu-id="b3408-559">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="b3408-560">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="b3408-560">For more information, see the following resources:</span></span>

* [<span data-ttu-id="b3408-561">Application Insights 概述</span><span class="sxs-lookup"><span data-stu-id="b3408-561">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="b3408-562">[用于 ASP.NET Core 应用程序的 Application Insights](/azure/azure-monitor/app/asp-net-core) - 如果想要实现各种 Application Insights 遥测以及日志记录，请从这里开始。</span><span class="sxs-lookup"><span data-stu-id="b3408-562">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="b3408-563">[.NET Core ILogger 日志的 ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - 如果想要在没有其他 Application Insights 遥测的情况下实现日志记录提供程序，请从这里开始。</span><span class="sxs-lookup"><span data-stu-id="b3408-563">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="b3408-564">[Application Insights 日志记录适配器](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)。</span><span class="sxs-lookup"><span data-stu-id="b3408-564">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="b3408-565">[安装、配置和初始化 Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn 网站上的交互式教程。</span><span class="sxs-lookup"><span data-stu-id="b3408-565">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="b3408-566">第三方日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="b3408-566">Third-party logging providers</span></span>

<span data-ttu-id="b3408-567">适用于 ASP.NET Core 的第三方日志记录框架：</span><span class="sxs-lookup"><span data-stu-id="b3408-567">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="b3408-568">[elmah.io](https://elmah.io/)（[GitHub 存储库](https://github.com/elmahio/Elmah.Io.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="b3408-568">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="b3408-569">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html)（[GitHub 存储库](https://github.com/mattwcole/gelf-extensions-logging)）</span><span class="sxs-lookup"><span data-stu-id="b3408-569">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="b3408-570">[JSNLog](https://jsnlog.com/)（[GitHub 存储库](https://github.com/mperdeck/jsnlog)）</span><span class="sxs-lookup"><span data-stu-id="b3408-570">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="b3408-571">[KissLog.net](https://kisslog.net/)（[GitHub 存储库](https://github.com/catalingavan/KissLog-net)）</span><span class="sxs-lookup"><span data-stu-id="b3408-571">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="b3408-572">[Log4Net](https://logging.apache.org/log4net/)（[GitHub 存储库](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore)）</span><span class="sxs-lookup"><span data-stu-id="b3408-572">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="b3408-573">[Loggr](https://loggr.net/)（[GitHub 存储库](https://github.com/imobile3/Loggr.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="b3408-573">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="b3408-574">[NLog](https://nlog-project.org/)（[GitHub 存储库](https://github.com/NLog/NLog.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="b3408-574">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="b3408-575">[Sentry](https://sentry.io/welcome/)（[GitHub 存储库](https://github.com/getsentry/sentry-dotnet)）</span><span class="sxs-lookup"><span data-stu-id="b3408-575">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="b3408-576">[Serilog](https://serilog.net/)（[GitHub 存储库](https://github.com/serilog/serilog-aspnetcore)）</span><span class="sxs-lookup"><span data-stu-id="b3408-576">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="b3408-577">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging)（[Github 存储库](https://github.com/googleapis/google-cloud-dotnet)）</span><span class="sxs-lookup"><span data-stu-id="b3408-577">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="b3408-578">某些第三方框架可以执行[语义日志记录（又称结构化日志记录）](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="b3408-578">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="b3408-579">使用第三方框架类似于使用以下内置提供程序之一：</span><span class="sxs-lookup"><span data-stu-id="b3408-579">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="b3408-580">将 NuGet 包添加到你的项目。</span><span class="sxs-lookup"><span data-stu-id="b3408-580">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="b3408-581">调用日志记录框架提供的 `ILoggerFactory` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="b3408-581">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="b3408-582">有关详细信息，请参阅各提供程序的相关文档。</span><span class="sxs-lookup"><span data-stu-id="b3408-582">For more information, see each provider's documentation.</span></span> <span data-ttu-id="b3408-583">Microsoft 不支持第三方日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="b3408-583">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b3408-584">其他资源</span><span class="sxs-lookup"><span data-stu-id="b3408-584">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
