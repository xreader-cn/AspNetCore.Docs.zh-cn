---
title: .NET Core 和 ASP.NET Core 中的日志记录
author: tdykstra
description: 了解如何使用由 Microsoft Extension.Logging NuGet 包提供的日志记录框架。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
uid: fundamentals/logging/index
ms.openlocfilehash: bb38ebca3c7b9bb4c28a52c0dad80be9669e1b40
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71924877"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="c1d0a-103">.NET Core 和 ASP.NET Core 中的日志记录</span><span class="sxs-lookup"><span data-stu-id="c1d0a-103">Logging in .NET Core and ASP.NET Core</span></span>

<span data-ttu-id="c1d0a-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="c1d0a-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="c1d0a-105">.NET Core 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="c1d0a-106">本文介绍了如何将日志记录 API 与内置提供程序一起使用。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-106">This article shows how to use the logging API with built-in providers.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c1d0a-107">本文中所述的大多数代码示例都来自 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="c1d0a-108">这些代码片段的日志记录特定部分适用于任何使用[通用主机](xref:fundamentals/host/generic-host)的 .NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="c1d0a-109">有关如何在非 Web 控制台应用中使用通用主机的信息，请参阅[托管服务](xref:fundamentals/host/hosted-services)。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-109">For information about how to use the Generic Host in non-web console apps, see [Hosted services](xref:fundamentals/host/hosted-services).</span></span>

<span data-ttu-id="c1d0a-110">对于没有通用主机的应用，日志记录代码在[添加提供程序](#add-providers)和[创建记录器](#create-logs)的方式上有所不同。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="c1d0a-111">本文的这些部分显示了非主机代码示例。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-111">Non-host code examples are shown in those sections of the article.</span></span>

::: moniker-end

<span data-ttu-id="c1d0a-112">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="c1d0a-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="c1d0a-113">添加提供程序</span><span class="sxs-lookup"><span data-stu-id="c1d0a-113">Add providers</span></span>

<span data-ttu-id="c1d0a-114">日志记录提供程序会显示或存储日志。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="c1d0a-115">例如，控制台提供程序在控制台上显示日志，Azure Application Insights 提供程序会将这些日志存储在 Azure Application Insights 中。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="c1d0a-116">可通过添加多个提供程序将日志发送到多个目标。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c1d0a-117">若要在使用通用主机的应用中添加提供程序，请在 Program.cs 中调用提供程序的 `Add{provider name}` 扩展方法  ：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="c1d0a-118">在非主机控制台应用中，在创建 `LoggerFactory` 时调用提供程序的 `Add{provider name}` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="c1d0a-119">`LoggerFactory` 和 `AddConsole` 需要用于 `Microsoft.Extensions.Logging` 的 `using` 语句。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="c1d0a-120">默认 ASP.NET Core 项目模板调用 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>，该操作将添加以下日志记录提供程序：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="c1d0a-121">控制台</span><span class="sxs-lookup"><span data-stu-id="c1d0a-121">Console</span></span>
* <span data-ttu-id="c1d0a-122">调试</span><span class="sxs-lookup"><span data-stu-id="c1d0a-122">Debug</span></span>
* <span data-ttu-id="c1d0a-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="c1d0a-123">EventSource</span></span>
* <span data-ttu-id="c1d0a-124">EventLog（仅当在 Windows 上运行时）</span><span class="sxs-lookup"><span data-stu-id="c1d0a-124">EventLog (only when running on Windows)</span></span>

<span data-ttu-id="c1d0a-125">可自行选择提供程序来替换默认提供程序。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="c1d0a-126">调用 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>，然后添加所需的提供程序。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0 "

<span data-ttu-id="c1d0a-127">要添加提供程序，请在 Program.cs 中调用提供程序的 `Add{provider name}` 扩展方法  ：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-127">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="c1d0a-128">前面的代码需要引用 `Microsoft.Extensions.Logging` 和 `Microsoft.Extensions.Configuration`。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-128">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="c1d0a-129">默认项目模板调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>，该操作将添加以下日志记录提供程序：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-129">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="c1d0a-130">控制台</span><span class="sxs-lookup"><span data-stu-id="c1d0a-130">Console</span></span>
* <span data-ttu-id="c1d0a-131">调试</span><span class="sxs-lookup"><span data-stu-id="c1d0a-131">Debug</span></span>
* <span data-ttu-id="c1d0a-132">EventSource（从 ASP.NET Core 2.2 开始）</span><span class="sxs-lookup"><span data-stu-id="c1d0a-132">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="c1d0a-133">如果使用 `CreateDefaultBuilder`，则可自行选择提供程序来替换默认提供程序。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-133">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="c1d0a-134">调用 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>，然后添加所需的提供程序。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-134">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

<span data-ttu-id="c1d0a-135">详细了解[内置日志记录提供程序](#built-in-logging-providers)，以及本文稍后部分介绍的[第三方日志记录提供程序](#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-135">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="c1d0a-136">创建日志</span><span class="sxs-lookup"><span data-stu-id="c1d0a-136">Create logs</span></span>

<span data-ttu-id="c1d0a-137">若要创建日志，请使用 <xref:Microsoft.Extensions.Logging.ILogger%601> 对象。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-137">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="c1d0a-138">在 Web 应用或托管服务中，由依赖关系注入 (DI) 获取 `ILogger`。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-138">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="c1d0a-139">在非主机控制台应用中，使用 `LoggerFactory` 来创建 `ILogger`。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-139">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="c1d0a-140">下面的 ASP.NET Core 示例创建了一个以 `TodoApiSample.Pages.AboutModel` 为类别的记录器。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-140">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="c1d0a-141">日志“类别”是与每个日志关联的字符串  。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-141">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="c1d0a-142">DI 提供的 `ILogger<T>` 实例创建日志，该日志以类型 `T` 的完全限定名称为类别。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-142">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="c1d0a-143">下面的非主机控制台应用示例创建了一个以 `LoggingConsoleApp.Program` 为类别的记录器。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-143">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

::: moniker-end

<span data-ttu-id="c1d0a-144">在下面的 ASP.NET Core 和控制台应用示例中，记录器用于创建以 `Information` 为级别的日志。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-144">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="c1d0a-145">日志“级别”代表所记录事件的严重程度  。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-145">The Log *level* indicates the severity of the logged event.</span></span> 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

<span data-ttu-id="c1d0a-146">本文稍后部分将更详细地介绍[级别](#log-level)和[类别](#log-category)。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-146">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span> 

::: moniker range=">= aspnetcore-3.0"

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="c1d0a-147">在 Program 类中创建日志</span><span class="sxs-lookup"><span data-stu-id="c1d0a-147">Create logs in the Program class</span></span>

<span data-ttu-id="c1d0a-148">若要将日志写入 ASP.NET Core 应用的 `Program` 类中，请在生成主机后从 DI 获取 `ILogger`实例：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-148">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="c1d0a-149">在 Startup 类中创建日志</span><span class="sxs-lookup"><span data-stu-id="c1d0a-149">Create logs in the Startup class</span></span>

<span data-ttu-id="c1d0a-150">若要将日志写入 ASP.NET Core 应用的 `Startup.Configure` 方法中，请在方法签名中包含 `ILogger` 参数：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-150">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="c1d0a-151">不支持在 `Startup.ConfigureServices` 方法中完成 DI 容器设置之前就写入日志：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-151">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="c1d0a-152">不支持将记录器注入到 `Startup` 构造函数中。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-152">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="c1d0a-153">不支持将记录器注入到 `Startup.ConfigureServices` 方法签名中</span><span class="sxs-lookup"><span data-stu-id="c1d0a-153">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="c1d0a-154">这一限制的原因是，日志记录依赖于 DI 和配置，而配置又依赖于 DI。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-154">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="c1d0a-155">在完成 `ConfigureServices` 之前，不会设置 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-155">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="c1d0a-156">由于为 Web 主机创建了单独的 DI 容器，所以在 ASP.NET Core 的早期版本中，构造函数将记录器注入到 `Startup` 工作。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-156">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="c1d0a-157">若要了解为何仅为通用主机创建一个容器，请参阅[重大更改公告](https://github.com/aspnet/Announcements/issues/353)。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-157">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="c1d0a-158">如果需要配置依赖于 `ILogger<T>` 的服务，则仍可通过使用构造函数注入或提供工厂方法的方式来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-158">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="c1d0a-159">只有在没有其他选择的情况下，才建议使用工厂方法。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-159">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="c1d0a-160">例如，假设你需要由 DI 为服务填充属性：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-160">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="c1d0a-161">前面突出显示的代码是 `Func`，该代码在 DI 容器第一次需要构造 `MyService` 实例时运行。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-161">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="c1d0a-162">可以用这种方式访问任何已注册的服务。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-162">You can access any of the registered services in this way.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="create-logs-in-startup"></a><span data-ttu-id="c1d0a-163">启动时创建日志</span><span class="sxs-lookup"><span data-stu-id="c1d0a-163">Create logs in Startup</span></span>

<span data-ttu-id="c1d0a-164">要将日志写入 `Startup` 类，构造函数签名需包含 `ILogger` 参数：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-164">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="c1d0a-165">在 Program 类中创建日志</span><span class="sxs-lookup"><span data-stu-id="c1d0a-165">Create logs in the Program class</span></span>

<span data-ttu-id="c1d0a-166">要将日志写入 `Program` 类，请从 DI 获取 `ILogger` 实例：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-166">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="c1d0a-167">没有异步记录器方法</span><span class="sxs-lookup"><span data-stu-id="c1d0a-167">No asynchronous logger methods</span></span>

<span data-ttu-id="c1d0a-168">日志记录应该会很快，不值得牺牲性能来使用异步代码。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-168">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="c1d0a-169">如果你的日志数据存储很慢，请不要直接写入它。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-169">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="c1d0a-170">首先考虑将日志消息写入快速存储，稍后再将其变为慢速存储。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-170">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="c1d0a-171">例如，如果你要记录到 SQL Server，你可能不想直接在 `Log` 方法中记录，因为 `Log` 方法是同步的。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-171">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="c1d0a-172">相反，你会将日志消息同步添加到内存中的队列，并让后台辅助线程从队列中拉出消息，以完成将数据推送到 SQL Server 的异步工作。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-172">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span>

## <a name="configuration"></a><span data-ttu-id="c1d0a-173">配置</span><span class="sxs-lookup"><span data-stu-id="c1d0a-173">Configuration</span></span>

<span data-ttu-id="c1d0a-174">日志记录提供程序配置由一个或多个配置提供程序提供：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-174">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="c1d0a-175">文件格式（INI、JSON 和 XML）。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-175">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="c1d0a-176">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-176">Command-line arguments.</span></span>
* <span data-ttu-id="c1d0a-177">环境变量。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-177">Environment variables.</span></span>
* <span data-ttu-id="c1d0a-178">内存中的 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-178">In-memory .NET objects.</span></span>
* <span data-ttu-id="c1d0a-179">未加密的[机密管理器](xref:security/app-secrets)存储。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-179">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="c1d0a-180">加密的用户存储，如 [Azure Key Vault](xref:security/key-vault-configuration)。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-180">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="c1d0a-181">（已安装或已创建的）自定义提供程序。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-181">Custom providers (installed or created).</span></span>

<span data-ttu-id="c1d0a-182">例如，日志记录配置通常由应用设置文件的 `Logging` 部分提供。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-182">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="c1d0a-183">以下示例显示了典型 *appsettings.Development.json* 文件的内容：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-183">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="c1d0a-184">`Logging` 属性可具有 `LogLevel` 和日志提供程序属性（显示控制台）。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-184">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="c1d0a-185">`Logging` 下的 `LogLevel` 属性指定了用于记录所选类别的最低[级别](#log-level)。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-185">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="c1d0a-186">在本例中，`System` 和 `Microsoft` 类别在 `Information` 级别记录，其他均在 `Debug` 级别记录。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-186">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="c1d0a-187">`Logging` 下的其他属性均指定了日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-187">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="c1d0a-188">本示例针对控制台提供程序。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-188">The example is for the Console provider.</span></span> <span data-ttu-id="c1d0a-189">如果提供程序支持[日志作用域](#log-scopes)，则 `IncludeScopes` 将指示是否启用这些域。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-189">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="c1d0a-190">提供程序属性（例如本例的 `Console`）也可指定 `LogLevel` 属性。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-190">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="c1d0a-191">提供程序下的 `LogLevel` 指定了该提供程序记录的级别。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-191">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="c1d0a-192">如果在 `Logging.{providername}.LogLevel` 中指定了级别，则这些级别将重写 `Logging.LogLevel` 中设置的所有内容。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-192">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

::: moniker-end

<span data-ttu-id="c1d0a-193">若要了解如何实现配置提供程序，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-193">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="c1d0a-194">日志记录输出示例</span><span class="sxs-lookup"><span data-stu-id="c1d0a-194">Sample logging output</span></span>

<span data-ttu-id="c1d0a-195">使用上一部分中显示的示例代码从命令行运行应用时，将在控制台中看到日志。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-195">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="c1d0a-196">以下是控制台输出示例：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-196">Here's an example of console output:</span></span>

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

<span data-ttu-id="c1d0a-197">通过向 `http://localhost:5000/api/todo/0` 处的示例应用发出 HTTP Get 请求来生成前述日志。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-197">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="c1d0a-198">在 Visual Studio 中运行示例应用时，“调试”窗口中将显示如下日志：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-198">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

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

<span data-ttu-id="c1d0a-199">由上一部分所示的 `ILogger` 调用创建的日志以“TodoApiSample”开头。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-199">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="c1d0a-200">以“Microsoft”类别开头的日志来自 ASP.NET Core 框架代码。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-200">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="c1d0a-201">ASP.NET Core 和应用程序代码使用相同的日志记录 API 和提供程序。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-201">ASP.NET Core and application code are using the same logging API and providers.</span></span>

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

<span data-ttu-id="c1d0a-202">由上一部分所示的 `ILogger` 调用创建的日志以“TodoApi”开头。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-202">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="c1d0a-203">以“Microsoft”类别开头的日志来自 ASP.NET Core 框架代码。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-203">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="c1d0a-204">ASP.NET Core 和应用程序代码使用相同的日志记录 API 和提供程序。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-204">ASP.NET Core and application code are using the same logging API and providers.</span></span>

::: moniker-end

<span data-ttu-id="c1d0a-205">本文余下部分将介绍有关日志记录的某些详细信息及选项。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-205">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="c1d0a-206">NuGet 包</span><span class="sxs-lookup"><span data-stu-id="c1d0a-206">NuGet packages</span></span>

<span data-ttu-id="c1d0a-207">`ILogger` 和 `ILoggerFactory` 接口位于 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 中，其默认实现位于 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 中。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-207">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="c1d0a-208">日志类别</span><span class="sxs-lookup"><span data-stu-id="c1d0a-208">Log category</span></span>

<span data-ttu-id="c1d0a-209">创建 `ILogger` 对象后，将为其指定“类别”  。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-209">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="c1d0a-210">该类别包含在由此 `ILogger` 实例创建的每条日志消息中。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-210">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="c1d0a-211">类别可以是任何字符串，但约定需使用类名，例如“TodoApi.Controllers.TodoController”。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-211">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="c1d0a-212">使用 `ILogger<T>` 获取一个 `ILogger` 实例，该实例使用 `T` 的完全限定类型名称作为类别：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-212">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

<span data-ttu-id="c1d0a-213">要显式指定类别，请调用 `ILoggerFactory.CreateLogger`：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-213">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

<span data-ttu-id="c1d0a-214">`ILogger<T>` 相当于使用 `T` 的完全限定类型名称来调用 `CreateLogger`。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-214">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="c1d0a-215">日志级别</span><span class="sxs-lookup"><span data-stu-id="c1d0a-215">Log level</span></span>

<span data-ttu-id="c1d0a-216">每个日志都指定了一个 <xref:Microsoft.Extensions.Logging.LogLevel> 值。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-216">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="c1d0a-217">日志级别指示严重性或重要程度。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-217">The log level indicates the severity or importance.</span></span> <span data-ttu-id="c1d0a-218">例如，可在方法正常结束时写入 `Information` 日志，在方法返回“404 找不到”状态代码时写入 `Warning` 日志  。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-218">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="c1d0a-219">下面的代码会创建 `Information` 和 `Warning` 日志：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-219">The following code creates `Information` and `Warning` logs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="c1d0a-220">在上述代码中，第一个参数是[日志事件 ID](#log-event-id)。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-220">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="c1d0a-221">第二个参数是消息模板，其中的占位符用于填写剩余方法形参提供的实参值。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-221">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="c1d0a-222">稍后将在本文的[消息模板部分](#log-message-template)介绍方法参数。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-222">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="c1d0a-223">在方法名称中包含级别的日志方法（例如 `LogInformation` 和 `LogWarning`）是 [ILogger 的扩展方法](xref:Microsoft.Extensions.Logging.LoggerExtensions)。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-223">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="c1d0a-224">这些方法会调用可接受 `LogLevel` 参数的 `Log` 方法。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-224">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="c1d0a-225">可直接调用 `Log` 方法而不调用其中某个扩展方法，但语法相对复杂。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-225">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="c1d0a-226">有关详细信息，请参阅 <xref:Microsoft.Extensions.Logging.ILogger> 和[记录器扩展源代码](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-226">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="c1d0a-227">ASP.NET Core 定义了以下日志级别（按严重性从低到高排列）。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-227">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="c1d0a-228">跟踪 = 0</span><span class="sxs-lookup"><span data-stu-id="c1d0a-228">Trace = 0</span></span>

  <span data-ttu-id="c1d0a-229">有关通常仅用于调试的信息。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-229">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="c1d0a-230">这些消息可能包含敏感应用程序数据，因此不得在生产环境中启用它们。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-230">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="c1d0a-231">默认情况下禁用。 </span><span class="sxs-lookup"><span data-stu-id="c1d0a-231">*Disabled by default.*</span></span>

* <span data-ttu-id="c1d0a-232">调试 = 1</span><span class="sxs-lookup"><span data-stu-id="c1d0a-232">Debug = 1</span></span>

  <span data-ttu-id="c1d0a-233">有关在开发和调试中可能有用的信息。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-233">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="c1d0a-234">示例：`Entering method Configure with flag set to true.` 由于日志数量过多，因此仅当执行故障排除时，才在生产中启用 `Debug` 级别日志。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-234">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="c1d0a-235">信息 = 2</span><span class="sxs-lookup"><span data-stu-id="c1d0a-235">Information = 2</span></span>

  <span data-ttu-id="c1d0a-236">用于跟踪应用的常规流。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-236">For tracking the general flow of the app.</span></span> <span data-ttu-id="c1d0a-237">这些日志通常有长期价值。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-237">These logs typically have some long-term value.</span></span> <span data-ttu-id="c1d0a-238">示例：`Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="c1d0a-238">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="c1d0a-239">警告 = 3</span><span class="sxs-lookup"><span data-stu-id="c1d0a-239">Warning = 3</span></span>

  <span data-ttu-id="c1d0a-240">表示应用流中的异常或意外事件。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-240">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="c1d0a-241">可能包括不会中断应用运行但仍需调查的错误或其他条件。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-241">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="c1d0a-242">`Warning` 日志级别常用于已处理的异常。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-242">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="c1d0a-243">示例：`FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="c1d0a-243">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="c1d0a-244">错误 = 4</span><span class="sxs-lookup"><span data-stu-id="c1d0a-244">Error = 4</span></span>

  <span data-ttu-id="c1d0a-245">表示无法处理的错误和异常。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-245">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="c1d0a-246">这些消息指示的是当前活动或操作（例如当前 HTTP 请求）中的失败，而不是整个应用中的失败。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-246">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="c1d0a-247">日志消息示例：`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="c1d0a-247">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="c1d0a-248">严重 = 5</span><span class="sxs-lookup"><span data-stu-id="c1d0a-248">Critical = 5</span></span>

  <span data-ttu-id="c1d0a-249">需要立即关注的失败。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-249">For failures that require immediate attention.</span></span> <span data-ttu-id="c1d0a-250">例如数据丢失、磁盘空间不足。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-250">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="c1d0a-251">使用日志级别控制写入到特定存储介质或显示窗口的日志输出量。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-251">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="c1d0a-252">例如:</span><span class="sxs-lookup"><span data-stu-id="c1d0a-252">For example:</span></span>

* <span data-ttu-id="c1d0a-253">在生产中，通过 `Information` 级别将 `Trace` 发送到卷数据存储。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-253">In production, send `Trace` through `Information` level to a volume data store.</span></span> <span data-ttu-id="c1d0a-254">通过 `Critical` 级别将 `Warning` 发送到值数据存储。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-254">Send `Warning` through `Critical` to a value data store.</span></span>
* <span data-ttu-id="c1d0a-255">在开发过程中，通过`Critical` 级别将 `Warning` 发送到控制台，并在进行故障排除时通过 `Information` 级别添加 `Trace`。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-255">During development, send `Warning` through `Critical` to the console, and add `Trace` through `Information` when troubleshooting.</span></span>

<span data-ttu-id="c1d0a-256">本文稍后的[日志筛选](#log-filtering)部分介绍如何控制提供程序处理的日志级别。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-256">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="c1d0a-257">ASP.NET Core 为框架事件写入日志。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-257">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="c1d0a-258">本文前面部分提供的日志示例排除了低于 `Information` 级别的日志，因此未创建 `Debug` 或 `Trace` 级别日志。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-258">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="c1d0a-259">以下示例介绍了通过运行配置为显示 `Debug` 日志的示例应用而生成的控制台日志：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-259">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="c1d0a-260">日志事件 ID</span><span class="sxs-lookup"><span data-stu-id="c1d0a-260">Log event ID</span></span>

<span data-ttu-id="c1d0a-261">每个日志都可指定一个事件 ID  。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-261">Each log can specify an *event ID*.</span></span> <span data-ttu-id="c1d0a-262">该示例应用通过使用本地定义的 `LoggingEvents` 类来执行此操作：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-262">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

<span data-ttu-id="c1d0a-263">事件 ID 与一组事件相关联。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-263">An event ID associates a set of events.</span></span> <span data-ttu-id="c1d0a-264">例如，与在页面上显示项列表相关的所有日志可能是 1001。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-264">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="c1d0a-265">日志记录提供程序可将事件 ID 存储在 ID 字段中，存储在日志记录消息中，或者不进行存储。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-265">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="c1d0a-266">调试提供程序不显示事件 ID。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-266">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="c1d0a-267">控制台提供程序在类别后的括号中显示事件 ID：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-267">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="c1d0a-268">日志消息模板</span><span class="sxs-lookup"><span data-stu-id="c1d0a-268">Log message template</span></span>

<span data-ttu-id="c1d0a-269">每个日志都会指定一个消息模板。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-269">Each log specifies a message template.</span></span> <span data-ttu-id="c1d0a-270">消息模板可包含要填写参数的占位符。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-270">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="c1d0a-271">请在占位符中使用名称而不是数字。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-271">Use names for the placeholders, not numbers.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="c1d0a-272">占位符的顺序（而非其名称）决定了为其提供值的参数。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-272">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="c1d0a-273">在以下代码中，请注意消息模板中的参数名称未按顺序排列：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-273">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="c1d0a-274">此代码创建了一个参数值按顺序排列的日志消息：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-274">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="c1d0a-275">日志记录框架按此方式工作，这样日志记录提供程序即可实现[语义日志记录，也称为结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-275">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="c1d0a-276">参数本身会传递给日志记录系统，而不仅仅是格式化的消息模板。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-276">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="c1d0a-277">通过此信息，日志记录提供程序能够将参数值存储为字段。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-277">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="c1d0a-278">例如，假设记录器方法调用如下所示：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-278">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="c1d0a-279">如果要将日志发送到 Azure 表存储，则每个 Azure 表实体都可具有 `ID` 和 `RequestTime` 属性，这简化了对日志数据的查询。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-279">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="c1d0a-280">无需分析文本消息的超时，查询即可找到特定 `RequestTime` 范围内的全部日志。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-280">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="c1d0a-281">日志记录异常</span><span class="sxs-lookup"><span data-stu-id="c1d0a-281">Logging exceptions</span></span>

<span data-ttu-id="c1d0a-282">记录器方法有可传入异常的重载，如下方示例所示：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-282">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

<span data-ttu-id="c1d0a-283">不同的提供程序处理异常信息的方式不同。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-283">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="c1d0a-284">以下是上示代码的调试提供程序输出示例。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-284">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="c1d0a-285">日志筛选</span><span class="sxs-lookup"><span data-stu-id="c1d0a-285">Log filtering</span></span>

<span data-ttu-id="c1d0a-286">可为特定或所有提供程序和类别指定最低日志级别。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-286">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="c1d0a-287">最低级别以下的日志不会传递给该提供程序，因此不会显示或存储它们。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-287">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="c1d0a-288">要禁止显示所有日志，可将 `LogLevel.None` 指定为最低日志级别。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-288">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="c1d0a-289">`LogLevel.None` 的整数值为 6，它大于 `LogLevel.Critical` (5)。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-289">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="c1d0a-290">在配置中创建筛选规则</span><span class="sxs-lookup"><span data-stu-id="c1d0a-290">Create filter rules in configuration</span></span>

<span data-ttu-id="c1d0a-291">项目模板代码调用 `CreateDefaultBuilder` 来为控制台和调试提供程序设置日志记录。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-291">The project template code calls `CreateDefaultBuilder` to set up logging for the Console and Debug providers.</span></span> <span data-ttu-id="c1d0a-292">正如[本文前面部分](#configuration)所述，`CreateDefaultBuilder` 方法设置日志记录以在 `Logging` 部分查找配置。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-292">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="c1d0a-293">配置数据按提供程序和类别指定最低日志级别，如下方示例所示：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-293">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

::: moniker-end

<span data-ttu-id="c1d0a-294">此 JSON 将创建 6 条筛选规则：1 条用于调试提供程序， 4 条用于控制台提供程序， 1 条用于所有提供程序。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-294">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="c1d0a-295">创建 `ILogger` 对象时，为每个提供程序选择一个规则。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-295">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="c1d0a-296">代码中的筛选规则</span><span class="sxs-lookup"><span data-stu-id="c1d0a-296">Filter rules in code</span></span>

<span data-ttu-id="c1d0a-297">下面的示例演示了如何在代码中注册筛选规则：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-297">The following example shows how to register filter rules in code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

<span data-ttu-id="c1d0a-298">第二个 `AddFilter` 使用类型名称来指定调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-298">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="c1d0a-299">第一个 `AddFilter` 应用于全部提供程序，因为它未指定提供程序类型。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-299">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="c1d0a-300">如何应用筛选规则</span><span class="sxs-lookup"><span data-stu-id="c1d0a-300">How filtering rules are applied</span></span>

<span data-ttu-id="c1d0a-301">先前示例中显示的配置数据和 `AddFilter` 代码会创建下表所示的规则。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-301">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="c1d0a-302">前六条由配置示例创建，后两条由代码示例创建。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-302">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="c1d0a-303">数字</span><span class="sxs-lookup"><span data-stu-id="c1d0a-303">Number</span></span> | <span data-ttu-id="c1d0a-304">提供程序</span><span class="sxs-lookup"><span data-stu-id="c1d0a-304">Provider</span></span>      | <span data-ttu-id="c1d0a-305">类别的开头为...</span><span class="sxs-lookup"><span data-stu-id="c1d0a-305">Categories that begin with ...</span></span>          | <span data-ttu-id="c1d0a-306">最低日志级别</span><span class="sxs-lookup"><span data-stu-id="c1d0a-306">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="c1d0a-307">1</span><span class="sxs-lookup"><span data-stu-id="c1d0a-307">1</span></span>      | <span data-ttu-id="c1d0a-308">调试</span><span class="sxs-lookup"><span data-stu-id="c1d0a-308">Debug</span></span>         | <span data-ttu-id="c1d0a-309">全部类别</span><span class="sxs-lookup"><span data-stu-id="c1d0a-309">All categories</span></span>                          | <span data-ttu-id="c1d0a-310">信息</span><span class="sxs-lookup"><span data-stu-id="c1d0a-310">Information</span></span>       |
| <span data-ttu-id="c1d0a-311">2</span><span class="sxs-lookup"><span data-stu-id="c1d0a-311">2</span></span>      | <span data-ttu-id="c1d0a-312">控制台</span><span class="sxs-lookup"><span data-stu-id="c1d0a-312">Console</span></span>       | <span data-ttu-id="c1d0a-313">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="c1d0a-313">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="c1d0a-314">警告</span><span class="sxs-lookup"><span data-stu-id="c1d0a-314">Warning</span></span>           |
| <span data-ttu-id="c1d0a-315">3</span><span class="sxs-lookup"><span data-stu-id="c1d0a-315">3</span></span>      | <span data-ttu-id="c1d0a-316">控制台</span><span class="sxs-lookup"><span data-stu-id="c1d0a-316">Console</span></span>       | <span data-ttu-id="c1d0a-317">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="c1d0a-317">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="c1d0a-318">调试</span><span class="sxs-lookup"><span data-stu-id="c1d0a-318">Debug</span></span>             |
| <span data-ttu-id="c1d0a-319">4</span><span class="sxs-lookup"><span data-stu-id="c1d0a-319">4</span></span>      | <span data-ttu-id="c1d0a-320">控制台</span><span class="sxs-lookup"><span data-stu-id="c1d0a-320">Console</span></span>       | <span data-ttu-id="c1d0a-321">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="c1d0a-321">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="c1d0a-322">错误</span><span class="sxs-lookup"><span data-stu-id="c1d0a-322">Error</span></span>             |
| <span data-ttu-id="c1d0a-323">5</span><span class="sxs-lookup"><span data-stu-id="c1d0a-323">5</span></span>      | <span data-ttu-id="c1d0a-324">控制台</span><span class="sxs-lookup"><span data-stu-id="c1d0a-324">Console</span></span>       | <span data-ttu-id="c1d0a-325">全部类别</span><span class="sxs-lookup"><span data-stu-id="c1d0a-325">All categories</span></span>                          | <span data-ttu-id="c1d0a-326">信息</span><span class="sxs-lookup"><span data-stu-id="c1d0a-326">Information</span></span>       |
| <span data-ttu-id="c1d0a-327">6</span><span class="sxs-lookup"><span data-stu-id="c1d0a-327">6</span></span>      | <span data-ttu-id="c1d0a-328">全部提供程序</span><span class="sxs-lookup"><span data-stu-id="c1d0a-328">All providers</span></span> | <span data-ttu-id="c1d0a-329">全部类别</span><span class="sxs-lookup"><span data-stu-id="c1d0a-329">All categories</span></span>                          | <span data-ttu-id="c1d0a-330">调试</span><span class="sxs-lookup"><span data-stu-id="c1d0a-330">Debug</span></span>             |
| <span data-ttu-id="c1d0a-331">7</span><span class="sxs-lookup"><span data-stu-id="c1d0a-331">7</span></span>      | <span data-ttu-id="c1d0a-332">全部提供程序</span><span class="sxs-lookup"><span data-stu-id="c1d0a-332">All providers</span></span> | <span data-ttu-id="c1d0a-333">System</span><span class="sxs-lookup"><span data-stu-id="c1d0a-333">System</span></span>                                  | <span data-ttu-id="c1d0a-334">调试</span><span class="sxs-lookup"><span data-stu-id="c1d0a-334">Debug</span></span>             |
| <span data-ttu-id="c1d0a-335">8</span><span class="sxs-lookup"><span data-stu-id="c1d0a-335">8</span></span>      | <span data-ttu-id="c1d0a-336">调试</span><span class="sxs-lookup"><span data-stu-id="c1d0a-336">Debug</span></span>         | <span data-ttu-id="c1d0a-337">Microsoft</span><span class="sxs-lookup"><span data-stu-id="c1d0a-337">Microsoft</span></span>                               | <span data-ttu-id="c1d0a-338">跟踪</span><span class="sxs-lookup"><span data-stu-id="c1d0a-338">Trace</span></span>             |

<span data-ttu-id="c1d0a-339">创建 `ILogger` 对象时，`ILoggerFactory` 对象将根据提供程序选择一条规则，将其应用于该记录器。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-339">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="c1d0a-340">将按所选规则筛选 `ILogger` 实例写入的所有消息。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-340">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="c1d0a-341">从可用规则中为每个提供程序和类别对选择最具体的规则。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-341">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="c1d0a-342">在为给定的类别创建 `ILogger` 时，以下算法将用于每个提供程序：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-342">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="c1d0a-343">选择匹配提供程序或其别名的所有规则。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-343">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="c1d0a-344">如果找不到任何匹配项，则选择提供程序为空的所有规则。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-344">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="c1d0a-345">根据上一步的结果，选择具有最长匹配类别前缀的规则。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-345">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="c1d0a-346">如果找不到任何匹配项，则选择未指定类别的所有规则。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-346">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="c1d0a-347">如果选择了多条规则，则采用最后一条  。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-347">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="c1d0a-348">如果未选择任何规则，则使用 `MinimumLevel`。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-348">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="c1d0a-349">假设你使用上述规则列表为类别“Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine”创建了 `ILogger` 对象：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-349">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="c1d0a-350">对于调试提供程序，规则 1、6 和 8 适用。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-350">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="c1d0a-351">规则 8 最为具体，因此选择了它。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-351">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="c1d0a-352">对于控制台提供程序，符合的有规则 3、规则 4、规则 5 和规则 6。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-352">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="c1d0a-353">规则 3 最为具体。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-353">Rule 3 is most specific.</span></span>

<span data-ttu-id="c1d0a-354">生成的 `ILogger` 实例将 `Trace` 级别及更高级别的日志发送到调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-354">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="c1d0a-355">`Debug` 级别及更高级别的日志会发送到控制台提供程序。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-355">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="c1d0a-356">提供程序别名</span><span class="sxs-lookup"><span data-stu-id="c1d0a-356">Provider aliases</span></span>

<span data-ttu-id="c1d0a-357">每个提供程序都定义了一个别名；可在配置中使用该别名来代替完全限定的类型名称  。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-357">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="c1d0a-358">对于内置提供程序，请使用以下别名：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-358">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="c1d0a-359">控制台</span><span class="sxs-lookup"><span data-stu-id="c1d0a-359">Console</span></span>
* <span data-ttu-id="c1d0a-360">调试</span><span class="sxs-lookup"><span data-stu-id="c1d0a-360">Debug</span></span>
* <span data-ttu-id="c1d0a-361">EventSource</span><span class="sxs-lookup"><span data-stu-id="c1d0a-361">EventSource</span></span>
* <span data-ttu-id="c1d0a-362">EventLog</span><span class="sxs-lookup"><span data-stu-id="c1d0a-362">EventLog</span></span>
* <span data-ttu-id="c1d0a-363">TraceSource</span><span class="sxs-lookup"><span data-stu-id="c1d0a-363">TraceSource</span></span>
* <span data-ttu-id="c1d0a-364">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="c1d0a-364">AzureAppServicesFile</span></span>
* <span data-ttu-id="c1d0a-365">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="c1d0a-365">AzureAppServicesBlob</span></span>
* <span data-ttu-id="c1d0a-366">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="c1d0a-366">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="c1d0a-367">默认最低级别</span><span class="sxs-lookup"><span data-stu-id="c1d0a-367">Default minimum level</span></span>

<span data-ttu-id="c1d0a-368">仅当配置或代码中的规则对给定提供程序和类别都不适用时，最低级别设置才会生效。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-368">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="c1d0a-369">下面的示例演示如何设置最低级别：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-369">The following example shows how to set the minimum level:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

<span data-ttu-id="c1d0a-370">如果没有明确设置最低级别，则默认值为 `Information`，它表示 `Trace` 和 `Debug` 日志将被忽略。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-370">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="c1d0a-371">筛选器函数</span><span class="sxs-lookup"><span data-stu-id="c1d0a-371">Filter functions</span></span>

<span data-ttu-id="c1d0a-372">对配置或代码没有向其分配规则的所有提供程序和类别调用筛选器函数。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-372">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="c1d0a-373">函数中的代码可访问提供程序类型、类别和日志级别。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-373">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="c1d0a-374">例如:</span><span class="sxs-lookup"><span data-stu-id="c1d0a-374">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

## <a name="system-categories-and-levels"></a><span data-ttu-id="c1d0a-375">系统类别和级别</span><span class="sxs-lookup"><span data-stu-id="c1d0a-375">System categories and levels</span></span>

<span data-ttu-id="c1d0a-376">下面是 ASP.NET Core 和 Entity Framework Core 使用的一些类别，备注中说明了可从这些类别获取的具体日志：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-376">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="c1d0a-377">类别</span><span class="sxs-lookup"><span data-stu-id="c1d0a-377">Category</span></span>                            | <span data-ttu-id="c1d0a-378">说明</span><span class="sxs-lookup"><span data-stu-id="c1d0a-378">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="c1d0a-379">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="c1d0a-379">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="c1d0a-380">常规 ASP.NET Core 诊断。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-380">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="c1d0a-381">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="c1d0a-381">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="c1d0a-382">考虑、找到并使用了哪些密钥。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-382">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="c1d0a-383">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="c1d0a-383">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="c1d0a-384">所允许的主机。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-384">Hosts allowed.</span></span> |
| <span data-ttu-id="c1d0a-385">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="c1d0a-385">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="c1d0a-386">HTTP 请求完成的时间和启动时间。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-386">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="c1d0a-387">加载了哪些承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-387">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="c1d0a-388">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="c1d0a-388">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="c1d0a-389">MVC 和 Razor 诊断。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-389">MVC and Razor diagnostics.</span></span> <span data-ttu-id="c1d0a-390">模型绑定、筛选器执行、视图编译和操作选择。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-390">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="c1d0a-391">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="c1d0a-391">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="c1d0a-392">路由匹配信息。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-392">Route matching information.</span></span> |
| <span data-ttu-id="c1d0a-393">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="c1d0a-393">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="c1d0a-394">连接启动、停止和保持活动响应。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-394">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="c1d0a-395">HTTP 证书信息。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-395">HTTPS certificate information.</span></span> |
| <span data-ttu-id="c1d0a-396">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="c1d0a-396">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="c1d0a-397">提供的文件。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-397">Files served.</span></span> |
| <span data-ttu-id="c1d0a-398">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="c1d0a-398">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="c1d0a-399">常规 Entity Framework Core 诊断。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-399">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="c1d0a-400">数据库活动和配置、更改检测、迁移。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-400">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="c1d0a-401">日志作用域</span><span class="sxs-lookup"><span data-stu-id="c1d0a-401">Log scopes</span></span>

 <span data-ttu-id="c1d0a-402">“作用域”可对一组逻辑操作分组  。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-402">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="c1d0a-403">此分组可用于将相同的数据附加到作为集合的一部分而创建的每个日志。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-403">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="c1d0a-404">例如，在处理事务期间创建的每个日志都可包括事务 ID。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-404">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="c1d0a-405">范围是由 <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> 方法返回的 `IDisposable` 类型，持续至释放为止。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-405">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="c1d0a-406">要使用作用域，请在 `using` 块中包装记录器调用：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-406">Use a scope by wrapping logger calls in a `using` block:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

<span data-ttu-id="c1d0a-407">下列代码为控制台提供程序启用作用域：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-407">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="c1d0a-408">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="c1d0a-408">*Program.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="c1d0a-409">要启用基于作用域的日志记录，必须先配置 `IncludeScopes` 控制台记录器选项。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-409">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="c1d0a-410">若要了解关配置，请参阅[配置](#configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-410">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="c1d0a-411">每条日志消息都包含作用域内的信息：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-411">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="c1d0a-412">内置日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="c1d0a-412">Built-in logging providers</span></span>

<span data-ttu-id="c1d0a-413">ASP.NET Core 提供以下提供程序：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-413">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="c1d0a-414">控制台</span><span class="sxs-lookup"><span data-stu-id="c1d0a-414">Console</span></span>](#console-provider)
* [<span data-ttu-id="c1d0a-415">调试</span><span class="sxs-lookup"><span data-stu-id="c1d0a-415">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="c1d0a-416">EventSource</span><span class="sxs-lookup"><span data-stu-id="c1d0a-416">EventSource</span></span>](#eventsource-provider)
* [<span data-ttu-id="c1d0a-417">EventLog</span><span class="sxs-lookup"><span data-stu-id="c1d0a-417">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="c1d0a-418">TraceSource</span><span class="sxs-lookup"><span data-stu-id="c1d0a-418">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="c1d0a-419">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="c1d0a-419">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="c1d0a-420">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="c1d0a-420">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="c1d0a-421">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="c1d0a-421">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="c1d0a-422">要通过 ASP.NET Core 模块了解 stdout 和调试日志记录，请参阅 <xref:test/troubleshoot-azure-iis> 和 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-422">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="c1d0a-423">控制台提供程序</span><span class="sxs-lookup"><span data-stu-id="c1d0a-423">Console provider</span></span>

<span data-ttu-id="c1d0a-424">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 提供程序包向控制台发送日志输出。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-424">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="c1d0a-425">要查看控制台日志记录输出，请在项目文件夹中打开命令提示符并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-425">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="c1d0a-426">调试提供程序</span><span class="sxs-lookup"><span data-stu-id="c1d0a-426">Debug provider</span></span>

<span data-ttu-id="c1d0a-427">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 提供程序包使用 [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) 类（`Debug.WriteLine` 方法调用）来写入日志输出。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-427">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="c1d0a-428">在 Linux 中，此提供程序将日志写入 /var/log/message  。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-428">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="eventsource-provider"></a><span data-ttu-id="c1d0a-429">EventSource 提供程序</span><span class="sxs-lookup"><span data-stu-id="c1d0a-429">EventSource provider</span></span>

<span data-ttu-id="c1d0a-430">对于面向 ASP.NET Core 1.1.0 或更高版本的应用，[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) 提供程序包可实现事件跟踪。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-430">For apps that target ASP.NET Core 1.1.0 or later, the [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package can implement event tracing.</span></span> <span data-ttu-id="c1d0a-431">在 Windows 中，它使用 [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-431">On Windows, it uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span> <span data-ttu-id="c1d0a-432">提供程序可跨平台使用，但尚无支持 Linux 或 macOS 的事件集合和显示工具。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-432">The provider is cross-platform, but there are no event collection and display tools yet for Linux or macOS.</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="c1d0a-433">可使用 [PerfView 实用工具](https://github.com/Microsoft/perfview)收集和查看日志。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-433">A good way to collect and view logs is to use the [PerfView utility](https://github.com/Microsoft/perfview).</span></span> <span data-ttu-id="c1d0a-434">虽然其他工具也可以查看 ETW 日志，但在处理由 ASP.NET Core 发出的 ETW 事件时，使用 PerfView 能获得最佳体验。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-434">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="c1d0a-435">要将 PerfView 配置为收集此提供程序记录的事件，请向 Additional Providers 列表添加字符串 `*Microsoft-Extensions-Logging`  。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-435">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="c1d0a-436">（请勿遗漏字符串起始处的星号。）</span><span class="sxs-lookup"><span data-stu-id="c1d0a-436">(Don't miss the asterisk at the start of the string.)</span></span>

![其他 Perfview 提供程序](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="c1d0a-438">Windows EventLog 提供程序</span><span class="sxs-lookup"><span data-stu-id="c1d0a-438">Windows EventLog provider</span></span>

<span data-ttu-id="c1d0a-439">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 提供程序包向 Windows 事件日志发送日志输出。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-439">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="c1d0a-440">[AddEventLog 重载](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)允许传入 <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-440">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span>

### <a name="tracesource-provider"></a><span data-ttu-id="c1d0a-441">TraceSource 提供程序</span><span class="sxs-lookup"><span data-stu-id="c1d0a-441">TraceSource provider</span></span>

<span data-ttu-id="c1d0a-442">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) 提供程序包使用 <xref:System.Diagnostics.TraceSource> 库和提供程序。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-442">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="c1d0a-443">[AddTraceSource 重载](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) 允许传入资源开关和跟踪侦听器。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-443">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="c1d0a-444">要使用此提供程序，应用必须在 .NET Framework（而非 .NET Core）上运行。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-444">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="c1d0a-445">提供程序可将消息路由到多个[侦听器](/dotnet/framework/debug-trace-profile/trace-listeners)，例如示例应用中使用的 <xref:System.Diagnostics.TextWriterTraceListener>。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-445">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="c1d0a-446">Azure 应用服务提供程序</span><span class="sxs-lookup"><span data-stu-id="c1d0a-446">Azure App Service provider</span></span>

<span data-ttu-id="c1d0a-447">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 提供程序包将日志写入 Azure App Service 应用的文件系统，以及 Azure 存储帐户中的 [blob 存储](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-447">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c1d0a-448">共享框架中不包括该提供程序包。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-448">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="c1d0a-449">若要使用提供程序，请将提供程序包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-449">To use the provider, add the provider package to the project.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="c1d0a-450">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中不包括此提供程序包。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-450">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="c1d0a-451">如果面向 .NET Framework 或引用 `Microsoft.AspNetCore.App` 元包，请向项目添加提供程序包。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-451">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c1d0a-452">要配置提供程序设置，请使用 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> 和 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-452">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="c1d0a-453">要配置提供程序设置，请使用 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> 和 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-453">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="c1d0a-454"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> 重载允许传入 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-454">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="c1d0a-455">设置对象可以覆盖默认设置，例如日志记录输出模板、blob 名称和文件大小限制。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-455">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="c1d0a-456">（“输出模板”是一个消息模板，除了通过 `ILogger` 方法调用提供的内容之外，还可将其应用于所有日志。  ）</span><span class="sxs-lookup"><span data-stu-id="c1d0a-456">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

::: moniker-end

<span data-ttu-id="c1d0a-457">在部署应用服务应用时，应用程序将采用 Azure 门户中“应用服务”页面下的[应用服务日志](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag)部分的设置  。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-457">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="c1d0a-458">更新以下设置后，更改立即生效，无需重启或重新部署应用。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-458">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="c1d0a-459">应用程序日志记录(Filesystem) </span><span class="sxs-lookup"><span data-stu-id="c1d0a-459">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="c1d0a-460">应用程序日志记录(Blob) </span><span class="sxs-lookup"><span data-stu-id="c1d0a-460">**Application Logging (Blob)**</span></span>

<span data-ttu-id="c1d0a-461">日志文件的默认位置是 D:\\home\\LogFiles\\Application 文件夹，默认文件名为 diagnostics-yyyymmdd.txt   。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-461">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="c1d0a-462">默认文件大小上限为 10 MB，默认最大保留文件数为 2。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-462">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="c1d0a-463">默认 blob 名为 {app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt  。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-463">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="c1d0a-464">该提供程序仅当项目在 Azure 环境中运行时有效。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-464">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="c1d0a-465">项目在本地运行时，该提供程序无效 &mdash; 它不会写入本地文件或 blob 的本地开发存储。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-465">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="c1d0a-466">Azure 日志流式处理</span><span class="sxs-lookup"><span data-stu-id="c1d0a-466">Azure log streaming</span></span>

<span data-ttu-id="c1d0a-467">通过 Azure 日志流式处理，可从以下位置实时查看日志活动：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-467">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="c1d0a-468">应用服务器</span><span class="sxs-lookup"><span data-stu-id="c1d0a-468">The app server</span></span>
* <span data-ttu-id="c1d0a-469">Web 服务器</span><span class="sxs-lookup"><span data-stu-id="c1d0a-469">The web server</span></span>
* <span data-ttu-id="c1d0a-470">请求跟踪失败</span><span class="sxs-lookup"><span data-stu-id="c1d0a-470">Failed request tracing</span></span>

<span data-ttu-id="c1d0a-471">要配置 Azure 日志流式处理，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-471">To configure Azure log streaming:</span></span>

* <span data-ttu-id="c1d0a-472">从应用的门户页导航到“应用服务日志”页  。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-472">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="c1d0a-473">将“应用程序日志记录(Filesystem)”设置为“开”   。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-473">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="c1d0a-474">选择日志级别  。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-474">Choose the log **Level**.</span></span>

<span data-ttu-id="c1d0a-475">导航到“日志流”页面来查看应用消息  。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-475">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="c1d0a-476">它们由应用通过 `ILogger` 接口记录。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-476">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="c1d0a-477">Azure Application Insights 跟踪日志记录</span><span class="sxs-lookup"><span data-stu-id="c1d0a-477">Azure Application Insights trace logging</span></span>

<span data-ttu-id="c1d0a-478">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) 提供程序包将日志写入 Azure Application Insights。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-478">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="c1d0a-479">Application Insights 是一项服务，可监视 Web 应用并提供用于查询和分析遥测数据的工具。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-479">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="c1d0a-480">如果使用此提供程序，则可以使用 Application Insights 工具来查询和分析日志。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-480">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="c1d0a-481">日志记录提供程序作为 [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)（这是提供 ASP.NET Core 的所有可用遥测的包）的依赖项包括在内。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-481">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="c1d0a-482">如果使用此包，则无需安装提供程序包。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-482">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="c1d0a-483">请勿使用用于 ASP.NET 4.x 的 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) 包&mdash;。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-483">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="c1d0a-484">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-484">For more information, see the following resources:</span></span>

* [<span data-ttu-id="c1d0a-485">Application Insights 概述</span><span class="sxs-lookup"><span data-stu-id="c1d0a-485">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="c1d0a-486">[用于 ASP.NET Core 应用程序的 Application Insights](/azure/azure-monitor/app/asp-net-core) - 如果想要实现各种 Application Insights 遥测以及日志记录，请从这里开始。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-486">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="c1d0a-487">[.NET Core ILogger 日志的 ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - 如果想要在没有其他 Application Insights 遥测的情况下实现日志记录提供程序，请从这里开始。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-487">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="c1d0a-488">[Application Insights 日志记录适配器](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-488">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="c1d0a-489">[安装、配置和初始化 Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn 网站上的交互式教程。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-489">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="c1d0a-490">第三方日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="c1d0a-490">Third-party logging providers</span></span>

<span data-ttu-id="c1d0a-491">适用于 ASP.NET Core 的第三方日志记录框架：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-491">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="c1d0a-492">[elmah.io](https://elmah.io/)（[GitHub 存储库](https://github.com/elmahio/Elmah.Io.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="c1d0a-492">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="c1d0a-493">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html)（[GitHub 存储库](https://github.com/mattwcole/gelf-extensions-logging)）</span><span class="sxs-lookup"><span data-stu-id="c1d0a-493">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="c1d0a-494">[JSNLog](https://jsnlog.com/)（[GitHub 存储库](https://github.com/mperdeck/jsnlog)）</span><span class="sxs-lookup"><span data-stu-id="c1d0a-494">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="c1d0a-495">[KissLog.net](https://kisslog.net/)（[GitHub 存储库](https://github.com/catalingavan/KissLog-net)）</span><span class="sxs-lookup"><span data-stu-id="c1d0a-495">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="c1d0a-496">[Loggr](https://loggr.net/)（[GitHub 存储库](https://github.com/imobile3/Loggr.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="c1d0a-496">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="c1d0a-497">[NLog](https://nlog-project.org/)（[GitHub 存储库](https://github.com/NLog/NLog.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="c1d0a-497">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="c1d0a-498">[Sentry](https://sentry.io/welcome/)（[GitHub 存储库](https://github.com/getsentry/sentry-dotnet)）</span><span class="sxs-lookup"><span data-stu-id="c1d0a-498">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="c1d0a-499">[Serilog](https://serilog.net/)（[GitHub 存储库](https://github.com/serilog/serilog-aspnetcore)）</span><span class="sxs-lookup"><span data-stu-id="c1d0a-499">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="c1d0a-500">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging)（[Github 存储库](https://github.com/googleapis/google-cloud-dotnet)）</span><span class="sxs-lookup"><span data-stu-id="c1d0a-500">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="c1d0a-501">某些第三方框架可以执行[语义日志记录（又称结构化日志记录）](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-501">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="c1d0a-502">使用第三方框架类似于使用以下内置提供程序之一：</span><span class="sxs-lookup"><span data-stu-id="c1d0a-502">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="c1d0a-503">将 NuGet 包添加到你的项目。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-503">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="c1d0a-504">调用日志记录框架提供的 `ILoggerFactory` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-504">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="c1d0a-505">有关详细信息，请参阅各提供程序的相关文档。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-505">For more information, see each provider's documentation.</span></span> <span data-ttu-id="c1d0a-506">Microsoft 不支持第三方日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="c1d0a-506">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c1d0a-507">其他资源</span><span class="sxs-lookup"><span data-stu-id="c1d0a-507">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
