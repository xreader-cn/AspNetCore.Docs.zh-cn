---
title: .NET Core 和 ASP.NET Core 中的日志记录
author: rick-anderson
description: 了解如何使用由 Microsoft Extension.Logging NuGet 包提供的日志记录框架。
ms.author: riande
ms.custom: mvc
ms.date: 6/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: a4962c3132c60b68cb2d4b5a97e9b082aba15d15
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "87330709"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="1ad66-103">.NET Core 和 ASP.NET Core 中的日志记录</span><span class="sxs-lookup"><span data-stu-id="1ad66-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1ad66-104">作者：[Kirk Larkin](https://twitter.com/serpent5)、[Juergen Gutsch](https://github.com/JuergenGutsch) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1ad66-104">By [Kirk Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1ad66-105">.NET Core 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。</span><span class="sxs-lookup"><span data-stu-id="1ad66-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="1ad66-106">本文介绍了如何将日志记录 API 与内置提供程序一起使用。</span><span class="sxs-lookup"><span data-stu-id="1ad66-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="1ad66-107">本文中所述的大多数代码示例都来自 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="1ad66-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="1ad66-108">这些代码片段的日志记录特定部分适用于任何使用[通用主机](xref:fundamentals/host/generic-host)的 .NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="1ad66-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="1ad66-109">ASP.NET Core Web 应用模板使用通用主机。</span><span class="sxs-lookup"><span data-stu-id="1ad66-109">The ASP.NET Core web app templates use the Generic Host.</span></span>

<span data-ttu-id="1ad66-110">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="lp"></a>

## <a name="logging-providers"></a><span data-ttu-id="1ad66-111">日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="1ad66-111">Logging providers</span></span>

<span data-ttu-id="1ad66-112">日志记录提供程序存储日志，但显示日志的 `Console` 提供程序除外。</span><span class="sxs-lookup"><span data-stu-id="1ad66-112">Logging providers store logs, except for the `Console` provider which displays logs.</span></span> <span data-ttu-id="1ad66-113">例如，Azure Application Insights 提供程序将日志存储在 Azure Application Insights 中。</span><span class="sxs-lookup"><span data-stu-id="1ad66-113">For example, the Azure Application Insights provider stores logs in Azure Application Insights.</span></span> <span data-ttu-id="1ad66-114">可以启用多个提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-114">Multiple providers can be enabled.</span></span>

<span data-ttu-id="1ad66-115">默认 ASP.NET Core Web 应用模板：</span><span class="sxs-lookup"><span data-stu-id="1ad66-115">The default ASP.NET Core web app templates:</span></span>

* <span data-ttu-id="1ad66-116">使用[通用主机](xref:fundamentals/host/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-116">Use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>
* <span data-ttu-id="1ad66-117">调用 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>，这将添加以下日志记录提供程序：</span><span class="sxs-lookup"><span data-stu-id="1ad66-117">Call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>
  * [<span data-ttu-id="1ad66-118">控制台</span><span class="sxs-lookup"><span data-stu-id="1ad66-118">Console</span></span>](#console)
  * [<span data-ttu-id="1ad66-119">调试</span><span class="sxs-lookup"><span data-stu-id="1ad66-119">Debug</span></span>](#debug)
  * [<span data-ttu-id="1ad66-120">EventSource</span><span class="sxs-lookup"><span data-stu-id="1ad66-120">EventSource</span></span>](#event-source)
  * <span data-ttu-id="1ad66-121">[EventLog](#welog)：仅限 Windows</span><span class="sxs-lookup"><span data-stu-id="1ad66-121">[EventLog](#welog): Windows only</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_TemplateCode&highlight=9)]

<span data-ttu-id="1ad66-122">上面的代码显示了使用 ASP.NET Core Web 应用模板创建的 `Program` 类。</span><span class="sxs-lookup"><span data-stu-id="1ad66-122">The preceding code shows the `Program` class created with the ASP.NET Core web app templates.</span></span> <span data-ttu-id="1ad66-123">接下来的几节提供基于使用通用主机的 ASP.NET Core Web 应用模板的示例。</span><span class="sxs-lookup"><span data-stu-id="1ad66-123">The next several sections provide samples based on the ASP.NET Core web app templates, which use the Generic Host.</span></span> <span data-ttu-id="1ad66-124">本文档稍后将介绍[非托管控制台应用](#nhca)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-124">[Non-host console apps](#nhca) are discussed later in this document.</span></span>

<span data-ttu-id="1ad66-125">若要替代`Host.CreateDefaultBuilder` 添加的默认日志记录提供程序集，请调用 `ClearProviders` 并添加所需的日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-125">To override the default set of logging providers added by `Host.CreateDefaultBuilder`, call `ClearProviders` and add the required logging providers.</span></span> <span data-ttu-id="1ad66-126">例如，以下代码：</span><span class="sxs-lookup"><span data-stu-id="1ad66-126">For example, the following code:</span></span>

* <span data-ttu-id="1ad66-127">调用 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> 以从生成器中删除所有 <xref:Microsoft.Extensions.Logging.ILoggerProvider> 实例。</span><span class="sxs-lookup"><span data-stu-id="1ad66-127">Calls <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> to remove all the <xref:Microsoft.Extensions.Logging.ILoggerProvider> instances from the builder.</span></span>
* <span data-ttu-id="1ad66-128">添加[控制台](#console)日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-128">Adds the [Console](#console) logging provider.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_AddProvider&highlight=5-6)]

<span data-ttu-id="1ad66-129">有关其他提供程序，请参阅：</span><span class="sxs-lookup"><span data-stu-id="1ad66-129">For additional providers, see:</span></span>

* [<span data-ttu-id="1ad66-130">内置日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="1ad66-130">Built-in logging providers</span></span>](#bilp)
* <span data-ttu-id="1ad66-131">[第三方日志记录提供程序](#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-131">[Third-party logging providers](#third-party-logging-providers).</span></span>

## <a name="create-logs"></a><span data-ttu-id="1ad66-132">创建日志</span><span class="sxs-lookup"><span data-stu-id="1ad66-132">Create logs</span></span>

<span data-ttu-id="1ad66-133">若要创建日志，请使用[依赖项注入](xref:fundamentals/dependency-injection) (DI) 中的 <xref:Microsoft.Extensions.Logging.ILogger%601>对象。</span><span class="sxs-lookup"><span data-stu-id="1ad66-133">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object from [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="1ad66-134">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="1ad66-134">The following example:</span></span>

* <span data-ttu-id="1ad66-135">创建一个记录器 `ILogger<AboutModel>`，该记录器使用类型为 `AboutModel` 的完全限定名称的日志类别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-135">Creates a logger, `ILogger<AboutModel>`, which uses a log *category* of the fully qualified name of the type `AboutModel`.</span></span> <span data-ttu-id="1ad66-136">日志类别是与每个日志关联的字符串。</span><span class="sxs-lookup"><span data-stu-id="1ad66-136">The log category is a string that is associated with each log.</span></span>
* <span data-ttu-id="1ad66-137">调用 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> 以在 `Information` 级别登录。</span><span class="sxs-lookup"><span data-stu-id="1ad66-137">Calls <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> to log at the `Information` level.</span></span> <span data-ttu-id="1ad66-138">日志“级别”代表所记录事件的严重程度。</span><span class="sxs-lookup"><span data-stu-id="1ad66-138">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=5,14)]

<span data-ttu-id="1ad66-139">本文档稍后将更详细地介绍[级别](#log-level)和[类别](#log-category)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-139">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this document.</span></span>

<span data-ttu-id="1ad66-140">有关 Blazor 的信息，请参阅本文档中的[在 Blazor 和 Blazor WebAssembly](#clib) 中创建日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-140">For information on Blazor, see [Create logs in Blazor and Blazor WebAssembly](#clib) in this document.</span></span>

<span data-ttu-id="1ad66-141">[在 Main 和 Startup 中创建日志](#clms)介绍如何在 `Main` 和 `Startup` 中创建日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-141">[Create logs in Main and Startup](#clms) shows how to create logs in `Main` and `Startup`.</span></span>

## <a name="configure-logging"></a><span data-ttu-id="1ad66-142">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="1ad66-142">Configure logging</span></span>

<span data-ttu-id="1ad66-143">日志配置通常由 appsettings `{Environment}`.json 文件的 `Logging` 部分提供 。</span><span class="sxs-lookup"><span data-stu-id="1ad66-143">Logging configuration is commonly provided by the `Logging` section of *appsettings*.`{Environment}`*.json* files.</span></span> <span data-ttu-id="1ad66-144">以下 appsettings.Development.json 文件由 ASP.NET Core Web 应用模板生成：</span><span class="sxs-lookup"><span data-stu-id="1ad66-144">The following *appsettings.Development.json* file is generated by the ASP.NET Core web app templates:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Development.json)]

<span data-ttu-id="1ad66-145">在上述 JSON 中：</span><span class="sxs-lookup"><span data-stu-id="1ad66-145">In the preceding JSON:</span></span>

* <span data-ttu-id="1ad66-146">指定了 `"Default"`、`"Microsoft"` 和 `"Microsoft.Hosting.Lifetime"` 类别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-146">The `"Default"`, `"Microsoft"`, and `"Microsoft.Hosting.Lifetime"` categories are specified.</span></span>
* <span data-ttu-id="1ad66-147">`"Microsoft"` 类别适用于以 `"Microsoft"` 开头的所有类别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-147">The `"Microsoft"` category applies to all categories that start with `"Microsoft"`.</span></span> <span data-ttu-id="1ad66-148">例如，此设置适用于 `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` 类别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-148">For example, this setting applies to the `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` category.</span></span>
* <span data-ttu-id="1ad66-149">`"Microsoft"` 类别在日志级别 `Warning` 或更高级别记录。</span><span class="sxs-lookup"><span data-stu-id="1ad66-149">The `"Microsoft"` category logs at log level `Warning` and higher.</span></span>
* <span data-ttu-id="1ad66-150">`"Microsoft.Hosting.Lifetime"` 类别比 `"Microsoft"` 类别更具体，因此 `"Microsoft.Hosting.Lifetime"` 类别在日志级别“Information”和更高级别记录。</span><span class="sxs-lookup"><span data-stu-id="1ad66-150">The `"Microsoft.Hosting.Lifetime"` category is more specific than the `"Microsoft"` category, so the `"Microsoft.Hosting.Lifetime"` category logs at log level "Information" and higher.</span></span>
* <span data-ttu-id="1ad66-151">未指定特定的日志提供程序，因此 `LogLevel` 适用于所有启用的日志记录提供程序，但 [Windows EventLog](#welog) 除外。</span><span class="sxs-lookup"><span data-stu-id="1ad66-151">A specific log provider is not specified, so `LogLevel` applies to all the enabled logging providers except for the [Windows EventLog](#welog).</span></span>

<span data-ttu-id="1ad66-152">`Logging` 属性可以具有 <xref:Microsoft.Extensions.Logging.LogLevel> 和日志提供程序属性。</span><span class="sxs-lookup"><span data-stu-id="1ad66-152">The `Logging` property can have <xref:Microsoft.Extensions.Logging.LogLevel> and log provider properties.</span></span> <span data-ttu-id="1ad66-153">`LogLevel` 指定要针对所选类别进行记录的最低[级别](#log-level)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-153">The `LogLevel` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="1ad66-154">在前面的 JSON 中，指定了 `Information` 和 `Warning` 日志级别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-154">In the preceding JSON, `Information` and `Warning` log levels are specified.</span></span> <span data-ttu-id="1ad66-155">`LogLevel` 表示日志的严重性，范围为 0 到 6：</span><span class="sxs-lookup"><span data-stu-id="1ad66-155">`LogLevel` indicates the severity of the log and ranges from 0 to 6:</span></span>

<span data-ttu-id="1ad66-156">`Trace` = 0、`Debug` = 1、`Information` = 2、`Warning` = 3、`Error` = 4、`Critical` = 5 和 `None` = 6。</span><span class="sxs-lookup"><span data-stu-id="1ad66-156">`Trace` = 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5, and `None` = 6.</span></span>

<span data-ttu-id="1ad66-157">指定 `LogLevel` 时，将为指定级别和更高级别的消息启用日志记录。</span><span class="sxs-lookup"><span data-stu-id="1ad66-157">When a `LogLevel` is specified, logging is enabled for messages at the specified level and higher.</span></span> <span data-ttu-id="1ad66-158">在前面的 JSON 中，记录了 `Information` 及更高级别的 `Default` 类别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-158">In the preceding JSON, the `Default` category is logged for `Information` and higher.</span></span> <span data-ttu-id="1ad66-159">例如，记录了 `Information`、`Warning`、`Error` 和 `Critical` 消息。</span><span class="sxs-lookup"><span data-stu-id="1ad66-159">For example, `Information`, `Warning`, `Error`, and `Critical` messages are logged.</span></span> <span data-ttu-id="1ad66-160">如果未指定 `LogLevel`，则日志记录默认为 `Information` 级别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-160">If no `LogLevel` is specified, logging defaults to the `Information` level.</span></span> <span data-ttu-id="1ad66-161">有关详细信息，请参阅[日志级别](#llvl)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-161">For more information, see [Log levels](#llvl).</span></span>

<span data-ttu-id="1ad66-162">提供程序属性可以指定 `LogLevel` 属性。</span><span class="sxs-lookup"><span data-stu-id="1ad66-162">A provider property can specify a `LogLevel` property.</span></span> <span data-ttu-id="1ad66-163">提供程序下的 `LogLevel` 指定要为该提供程序记录的级别，并替代非提供程序日志设置。</span><span class="sxs-lookup"><span data-stu-id="1ad66-163">`LogLevel` under a provider specifies levels to log for that provider, and overrides the non-provider log settings.</span></span> <span data-ttu-id="1ad66-164">请考虑使用以下 appsettings.json 文件：</span><span class="sxs-lookup"><span data-stu-id="1ad66-164">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Prod2.json)]

<span data-ttu-id="1ad66-165">`Logging.{providername}.LogLevel` 中的设置将替代 `Logging.LogLevel` 中的设置。</span><span class="sxs-lookup"><span data-stu-id="1ad66-165">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="1ad66-166">在前面的 JSON 中，`Debug` 提供程序的默认日志级别设置为 `Information`：</span><span class="sxs-lookup"><span data-stu-id="1ad66-166">In the preceding JSON, the `Debug` provider's default log level is set to `Information`:</span></span>

`Logging:Debug:LogLevel:Default:Information`

<span data-ttu-id="1ad66-167">前面的设置为每个 `Logging:Debug:` 类别（`Microsoft.Hosting` 除外）指定 `Information` 日志级别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-167">The preceding setting specifies the `Information` log level for every `Logging:Debug:` category except `Microsoft.Hosting`.</span></span> <span data-ttu-id="1ad66-168">当列出特定类别时，该特定类别将替代默认类别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-168">When a specific category is listed, the specific category overrides the default category.</span></span> <span data-ttu-id="1ad66-169">在前面的 JSON 中，`Logging:Debug:LogLevel` 类别 `"Microsoft.Hosting"` 和 `"Default"` 替代 `Logging:LogLevel` 中的设置</span><span class="sxs-lookup"><span data-stu-id="1ad66-169">In the preceding JSON, the `Logging:Debug:LogLevel` categories `"Microsoft.Hosting"` and `"Default"` override the settings in `Logging:LogLevel`</span></span>

<span data-ttu-id="1ad66-170">可以为以下任何一项指定最低日志级别：</span><span class="sxs-lookup"><span data-stu-id="1ad66-170">The minimum log level can be specified for any of:</span></span>

* <span data-ttu-id="1ad66-171">特定提供程序：例如，`Logging:EventSource:LogLevel:Default:Information`</span><span class="sxs-lookup"><span data-stu-id="1ad66-171">Specific providers:  For example, `Logging:EventSource:LogLevel:Default:Information`</span></span>
* <span data-ttu-id="1ad66-172">特定类别：例如，`Logging:LogLevel:Microsoft:Warning`</span><span class="sxs-lookup"><span data-stu-id="1ad66-172">Specific categories: For example, `Logging:LogLevel:Microsoft:Warning`</span></span>
* <span data-ttu-id="1ad66-173">所有提供程序和所有类别：`Logging:LogLevel:Default:Warning`</span><span class="sxs-lookup"><span data-stu-id="1ad66-173">All providers and all categories: `Logging:LogLevel:Default:Warning`</span></span>

<span data-ttu-id="1ad66-174">低于最低级别的任何日志均不会执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1ad66-174">Any logs below the minimum level are ***not***:</span></span>

* <span data-ttu-id="1ad66-175">传递到提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-175">Passed to the provider.</span></span>
* <span data-ttu-id="1ad66-176">记录或显示。</span><span class="sxs-lookup"><span data-stu-id="1ad66-176">Logged or displayed.</span></span>

<span data-ttu-id="1ad66-177">要阻止所有日志，请指定 [LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-177">To suppress all logs, specify [LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel).</span></span> <span data-ttu-id="1ad66-178">`LogLevel.None` 的值为 6，该值高于 `LogLevel.Critical` (5)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-178">`LogLevel.None` has a value of 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="1ad66-179">如果提供程序支持[日志作用域](#logscopes)，则 `IncludeScopes` 将指示是否启用这些域。</span><span class="sxs-lookup"><span data-stu-id="1ad66-179">If a provider supports [log scopes](#logscopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="1ad66-180">有关详细信息，请参阅[日志范围](#logscopes)</span><span class="sxs-lookup"><span data-stu-id="1ad66-180">For more information, see [log scopes](#logscopes)</span></span>

<span data-ttu-id="1ad66-181">以下 appsettings.json 文件包含默认情况下启用的所有提供程序：</span><span class="sxs-lookup"><span data-stu-id="1ad66-181">The following *appsettings.json* file contains all the providers enabled by default:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Production.json)]

<span data-ttu-id="1ad66-182">在上述示例中：</span><span class="sxs-lookup"><span data-stu-id="1ad66-182">In the preceding sample:</span></span>

* <span data-ttu-id="1ad66-183">类别和级别不是建议的值。</span><span class="sxs-lookup"><span data-stu-id="1ad66-183">The categories and levels are not suggested values.</span></span> <span data-ttu-id="1ad66-184">提供该示例是为了显示所有默认提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-184">The sample is provided to show all the default providers.</span></span>
* <span data-ttu-id="1ad66-185">`Logging.{providername}.LogLevel` 中的设置将替代 `Logging.LogLevel` 中的设置。</span><span class="sxs-lookup"><span data-stu-id="1ad66-185">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="1ad66-186">例如，`Debug.LogLevel.Default` 中的级别将替代 `LogLevel.Default` 中的级别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-186">For example, the level in `Debug.LogLevel.Default` overrides the level in `LogLevel.Default`.</span></span>
* <span data-ttu-id="1ad66-187">将使用每个默认提供程序别名。</span><span class="sxs-lookup"><span data-stu-id="1ad66-187">Each default provider *alias* is used.</span></span> <span data-ttu-id="1ad66-188">每个提供程序都定义了一个别名；可在配置中使用该别名来代替完全限定的类型名称。</span><span class="sxs-lookup"><span data-stu-id="1ad66-188">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span> <span data-ttu-id="1ad66-189">内置提供程序别名包括：</span><span class="sxs-lookup"><span data-stu-id="1ad66-189">The built-in providers aliases are:</span></span>
  * <span data-ttu-id="1ad66-190">控制台</span><span class="sxs-lookup"><span data-stu-id="1ad66-190">Console</span></span>
  * <span data-ttu-id="1ad66-191">调试</span><span class="sxs-lookup"><span data-stu-id="1ad66-191">Debug</span></span>
  * <span data-ttu-id="1ad66-192">EventSource</span><span class="sxs-lookup"><span data-stu-id="1ad66-192">EventSource</span></span>
  * <span data-ttu-id="1ad66-193">EventLog</span><span class="sxs-lookup"><span data-stu-id="1ad66-193">EventLog</span></span>
  * <span data-ttu-id="1ad66-194">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="1ad66-194">AzureAppServicesFile</span></span>
  * <span data-ttu-id="1ad66-195">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="1ad66-195">AzureAppServicesBlob</span></span>
  * <span data-ttu-id="1ad66-196">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="1ad66-196">ApplicationInsights</span></span>

## <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a><span data-ttu-id="1ad66-197">通过命令行、环境变量和其他配置设置日志级别</span><span class="sxs-lookup"><span data-stu-id="1ad66-197">Set log level by command line, environment variables, and other configuration</span></span>

<span data-ttu-id="1ad66-198">日志级别可以由任何[配置提供程序](xref:fundamentals/configuration/index)设置。</span><span class="sxs-lookup"><span data-stu-id="1ad66-198">Log level can be set by any of the [configuration providers](xref:fundamentals/configuration/index).</span></span> 

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="1ad66-199">以下命令：</span><span class="sxs-lookup"><span data-stu-id="1ad66-199">The following commands:</span></span>

* <span data-ttu-id="1ad66-200">在 Windows 上，将环境密钥 `Logging:LogLevel:Microsoft` 设置为值 `Information`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-200">Set the environment key `Logging:LogLevel:Microsoft` to a value of `Information` on Windows.</span></span>
* <span data-ttu-id="1ad66-201">使用通过 ASP.NET Core Web 应用模板创建的应用时，请测试设置。</span><span class="sxs-lookup"><span data-stu-id="1ad66-201">Test the settings when using an app created with the ASP.NET Core web application templates.</span></span> <span data-ttu-id="1ad66-202">使用 `set` 之后，必须在项目目录中运行 `dotnet run` 命令。</span><span class="sxs-lookup"><span data-stu-id="1ad66-202">The `dotnet run` command must be run in the project directory after using `set`.</span></span>

```cmd
set Logging__LogLevel__Microsoft=Information
dotnet run
```

<span data-ttu-id="1ad66-203">前面的环境设置：</span><span class="sxs-lookup"><span data-stu-id="1ad66-203">The preceding environment setting:</span></span>

* <span data-ttu-id="1ad66-204">仅在进程中设置，这些进程是从设置进程的命令窗口启动的。</span><span class="sxs-lookup"><span data-stu-id="1ad66-204">Is only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="1ad66-205">不由使用 Visual Studio 启动的浏览器读取。</span><span class="sxs-lookup"><span data-stu-id="1ad66-205">Isn't read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="1ad66-206">以下 [setx](/windows-server/administration/windows-commands/setx) 命令还可以在 Windows 上设置环境键和值。</span><span class="sxs-lookup"><span data-stu-id="1ad66-206">The following [setx](/windows-server/administration/windows-commands/setx) command also sets the environment key and value on Windows.</span></span> <span data-ttu-id="1ad66-207">与 `set` 不同，`setx` 设置是持久的。</span><span class="sxs-lookup"><span data-stu-id="1ad66-207">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="1ad66-208">`/M` 开关在系统环境中设置变量。</span><span class="sxs-lookup"><span data-stu-id="1ad66-208">The `/M` switch sets the variable in the system environment.</span></span> <span data-ttu-id="1ad66-209">如果未使用 `/M`，则设置用户环境变量。</span><span class="sxs-lookup"><span data-stu-id="1ad66-209">If `/M` isn't used, a user environment variable is set.</span></span>

```cmd
setx Logging__LogLevel__Microsoft=Information /M
```

<span data-ttu-id="1ad66-210">在 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)上，选择“设置”>“配置”页面上的“新应用程序设置” 。</span><span class="sxs-lookup"><span data-stu-id="1ad66-210">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="1ad66-211">Azure 应用服务应用程序设置：</span><span class="sxs-lookup"><span data-stu-id="1ad66-211">Azure App Service application settings are:</span></span>

* <span data-ttu-id="1ad66-212">已静态加密且通过加密的通道进行传输。</span><span class="sxs-lookup"><span data-stu-id="1ad66-212">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="1ad66-213">已作为环境变量公开。</span><span class="sxs-lookup"><span data-stu-id="1ad66-213">Exposed as environment variables.</span></span>

<span data-ttu-id="1ad66-214">有关详细信息，请参阅 [Azure 应用：使用 Azure 门户替代应用配置](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-214">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="1ad66-215">有关使用环境变量设置 ASP.NET Core 配置值的详细信息，请参阅[环境变量](xref:fundamentals/configuration/index#environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-215">For more information on setting ASP.NET Core configuration values using environment variables, see [environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span> <span data-ttu-id="1ad66-216">有关使用其他配置源（包括命令行、Azure Key Vault、Azure 应用配置、其他文件格式等）的信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="1ad66-216">For information on using other configuration sources, including the command line, Azure Key Vault, Azure App Configuration, other file formats, and more, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="1ad66-217">如何应用筛选规则</span><span class="sxs-lookup"><span data-stu-id="1ad66-217">How filtering rules are applied</span></span>

<span data-ttu-id="1ad66-218">创建 <xref:Microsoft.Extensions.Logging.ILogger%601> 对象时，<xref:Microsoft.Extensions.Logging.ILoggerFactory> 对象将根据提供程序选择一条规则，将其应用于该记录器。</span><span class="sxs-lookup"><span data-stu-id="1ad66-218">When an <xref:Microsoft.Extensions.Logging.ILogger%601> object is created, the <xref:Microsoft.Extensions.Logging.ILoggerFactory> object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="1ad66-219">将按所选规则筛选 `ILogger` 实例写入的所有消息。</span><span class="sxs-lookup"><span data-stu-id="1ad66-219">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="1ad66-220">从可用规则中为每个提供程序和类别对选择最具体的规则。</span><span class="sxs-lookup"><span data-stu-id="1ad66-220">The most specific rule for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="1ad66-221">在为给定的类别创建 `ILogger` 时，以下算法将用于每个提供程序：</span><span class="sxs-lookup"><span data-stu-id="1ad66-221">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="1ad66-222">选择匹配提供程序或其别名的所有规则。</span><span class="sxs-lookup"><span data-stu-id="1ad66-222">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="1ad66-223">如果找不到任何匹配项，则选择提供程序为空的所有规则。</span><span class="sxs-lookup"><span data-stu-id="1ad66-223">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="1ad66-224">根据上一步的结果，选择具有最长匹配类别前缀的规则。</span><span class="sxs-lookup"><span data-stu-id="1ad66-224">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="1ad66-225">如果找不到任何匹配项，则选择未指定类别的所有规则。</span><span class="sxs-lookup"><span data-stu-id="1ad66-225">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="1ad66-226">如果选择了多条规则，则采用最后一条  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-226">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="1ad66-227">如果未选择任何规则，则使用 `MinimumLevel`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-227">If no rules are selected, use `MinimumLevel`.</span></span>

<a name="dnrvs"></a>

## <a name="logging-output-from-dotnet-run-and-visual-studio"></a><span data-ttu-id="1ad66-228">记录来自 dotnet run 和 Visual Studio 的输出</span><span class="sxs-lookup"><span data-stu-id="1ad66-228">Logging output from dotnet run and Visual Studio</span></span>

<span data-ttu-id="1ad66-229">将显示使用[默认日志记录提供程序](#lp)创建的日志：</span><span class="sxs-lookup"><span data-stu-id="1ad66-229">Logs created with the [default logging providers](#lp) are displayed:</span></span>

* <span data-ttu-id="1ad66-230">在 Visual Studio 中</span><span class="sxs-lookup"><span data-stu-id="1ad66-230">In Visual Studio</span></span>
  * <span data-ttu-id="1ad66-231">在调试时，在“调试输出”窗口中。</span><span class="sxs-lookup"><span data-stu-id="1ad66-231">In the Debug output window when debugging.</span></span>
  * <span data-ttu-id="1ad66-232">在“ASP.NET Core Web 服务器”窗口中。</span><span class="sxs-lookup"><span data-stu-id="1ad66-232">In the ASP.NET Core Web Server window.</span></span>
* <span data-ttu-id="1ad66-233">使用 `dotnet run` 运行应用时，在控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="1ad66-233">In the console window when the app is run with `dotnet run`.</span></span>

<span data-ttu-id="1ad66-234">以“Microsoft”类别开头的日志来自 ASP.NET Core 框架代码。</span><span class="sxs-lookup"><span data-stu-id="1ad66-234">Logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="1ad66-235">ASP.NET Core 和应用程序代码使用相同的日志记录 API 和提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-235">ASP.NET Core and application code use the same logging API and providers.</span></span>

<a name="lcat"></a>

## <a name="log-category"></a><span data-ttu-id="1ad66-236">日志类别</span><span class="sxs-lookup"><span data-stu-id="1ad66-236">Log category</span></span>

<span data-ttu-id="1ad66-237">创建 `ILogger` 对象时，将指定类别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-237">When an `ILogger` object is created, a *category* is specified.</span></span> <span data-ttu-id="1ad66-238">该类别包含在由此 `ILogger` 实例创建的每条日志消息中。</span><span class="sxs-lookup"><span data-stu-id="1ad66-238">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="1ad66-239">类别字符串是任意的，但约定将使用类名称。</span><span class="sxs-lookup"><span data-stu-id="1ad66-239">The category string is arbitrary, but the convention is to use the class name.</span></span> <span data-ttu-id="1ad66-240">例如，在控制器中，名称可能为 `"TodoApi.Controllers.TodoController"`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-240">For example, in a controller the name might be `"TodoApi.Controllers.TodoController"`.</span></span> <span data-ttu-id="1ad66-241">ASP.NET Core Web 应用使用 `ILogger<T>` 自动获取使用完全限定类型名称 `T` 作为类别的 `ILogger` 实例：</span><span class="sxs-lookup"><span data-stu-id="1ad66-241">The ASP.NET Core web apps use `ILogger<T>` to automatically get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="1ad66-242">要显式指定类别，请调用 `ILoggerFactory.CreateLogger`：</span><span class="sxs-lookup"><span data-stu-id="1ad66-242">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Contact.cshtml.cs?name=snippet)]

<span data-ttu-id="1ad66-243">`ILogger<T>` 相当于使用 `T` 的完全限定类型名称来调用 `CreateLogger`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-243">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

<a name="llvl"></a>

## <a name="log-level"></a><span data-ttu-id="1ad66-244">日志级别</span><span class="sxs-lookup"><span data-stu-id="1ad66-244">Log level</span></span>

<span data-ttu-id="1ad66-245">下表列出了 <xref:Microsoft.Extensions.Logging.LogLevel> 值、方便的 `Log{LogLevel}` 扩展方法以及建议的用法：</span><span class="sxs-lookup"><span data-stu-id="1ad66-245">The following table lists the <xref:Microsoft.Extensions.Logging.LogLevel> values, the convenience `Log{LogLevel}` extension method, and the suggested usage:</span></span>

| <span data-ttu-id="1ad66-246">LogLevel</span><span class="sxs-lookup"><span data-stu-id="1ad66-246">LogLevel</span></span> | <span data-ttu-id="1ad66-247">“值”</span><span class="sxs-lookup"><span data-stu-id="1ad66-247">Value</span></span> | <span data-ttu-id="1ad66-248">方法</span><span class="sxs-lookup"><span data-stu-id="1ad66-248">Method</span></span> | <span data-ttu-id="1ad66-249">描述</span><span class="sxs-lookup"><span data-stu-id="1ad66-249">Description</span></span> |
| -------- | ----- | ------ | ----------- |
| [<span data-ttu-id="1ad66-250">Trace</span><span class="sxs-lookup"><span data-stu-id="1ad66-250">Trace</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="1ad66-251">0</span><span class="sxs-lookup"><span data-stu-id="1ad66-251">0</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | <span data-ttu-id="1ad66-252">包含最详细的消息。</span><span class="sxs-lookup"><span data-stu-id="1ad66-252">Contain the most detailed messages.</span></span> <span data-ttu-id="1ad66-253">这些消息可能包含敏感的应用数据。</span><span class="sxs-lookup"><span data-stu-id="1ad66-253">These messages may contain sensitive app data.</span></span> <span data-ttu-id="1ad66-254">这些消息默认情况下处于禁用状态，并且不应在生产中启用。</span><span class="sxs-lookup"><span data-stu-id="1ad66-254">These messages are disabled by default and should ***not*** be enabled in production.</span></span> |
| [<span data-ttu-id="1ad66-255">调试</span><span class="sxs-lookup"><span data-stu-id="1ad66-255">Debug</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="1ad66-256">1</span><span class="sxs-lookup"><span data-stu-id="1ad66-256">1</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | <span data-ttu-id="1ad66-257">用于调试和开发。</span><span class="sxs-lookup"><span data-stu-id="1ad66-257">For debugging and development.</span></span> <span data-ttu-id="1ad66-258">由于量大，请在生产中小心使用。</span><span class="sxs-lookup"><span data-stu-id="1ad66-258">Use with caution in production due to the high volume.</span></span> |
| [<span data-ttu-id="1ad66-259">信息</span><span class="sxs-lookup"><span data-stu-id="1ad66-259">Information</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="1ad66-260">2</span><span class="sxs-lookup"><span data-stu-id="1ad66-260">2</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | <span data-ttu-id="1ad66-261">跟踪应用的常规流。</span><span class="sxs-lookup"><span data-stu-id="1ad66-261">Tracks the general flow of the app.</span></span> <span data-ttu-id="1ad66-262">可能具有长期值。</span><span class="sxs-lookup"><span data-stu-id="1ad66-262">May have long-term value.</span></span> |
| [<span data-ttu-id="1ad66-263">警告</span><span class="sxs-lookup"><span data-stu-id="1ad66-263">Warning</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="1ad66-264">3</span><span class="sxs-lookup"><span data-stu-id="1ad66-264">3</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | <span data-ttu-id="1ad66-265">对于异常事件或意外事件。</span><span class="sxs-lookup"><span data-stu-id="1ad66-265">For abnormal or unexpected events.</span></span> <span data-ttu-id="1ad66-266">通常包括不会导致应用失败的错误或情况。</span><span class="sxs-lookup"><span data-stu-id="1ad66-266">Typically includes errors or conditions that don't cause the app to fail.</span></span> |
| [<span data-ttu-id="1ad66-267">错误</span><span class="sxs-lookup"><span data-stu-id="1ad66-267">Error</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="1ad66-268">4</span><span class="sxs-lookup"><span data-stu-id="1ad66-268">4</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | <span data-ttu-id="1ad66-269">表示无法处理的错误和异常。</span><span class="sxs-lookup"><span data-stu-id="1ad66-269">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="1ad66-270">这些消息表示当前操作或请求失败，而不是整个应用失败。</span><span class="sxs-lookup"><span data-stu-id="1ad66-270">These messages indicate a failure in the current operation or request, not an app-wide failure.</span></span> |
| [<span data-ttu-id="1ad66-271">严重</span><span class="sxs-lookup"><span data-stu-id="1ad66-271">Critical</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="1ad66-272">5</span><span class="sxs-lookup"><span data-stu-id="1ad66-272">5</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | <span data-ttu-id="1ad66-273">需要立即关注的失败。</span><span class="sxs-lookup"><span data-stu-id="1ad66-273">For failures that require immediate attention.</span></span> <span data-ttu-id="1ad66-274">例如数据丢失、磁盘空间不足。</span><span class="sxs-lookup"><span data-stu-id="1ad66-274">Examples: data loss scenarios, out of disk space.</span></span> |
| [<span data-ttu-id="1ad66-275">无</span><span class="sxs-lookup"><span data-stu-id="1ad66-275">None</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="1ad66-276">6</span><span class="sxs-lookup"><span data-stu-id="1ad66-276">6</span></span> | | <span data-ttu-id="1ad66-277">指定日志记录类别不应写入任何消息。</span><span class="sxs-lookup"><span data-stu-id="1ad66-277">Specifies that a logging category should not write any messages.</span></span> |

<span data-ttu-id="1ad66-278">在上表中，`LogLevel` 按严重性由低到高的顺序列出。</span><span class="sxs-lookup"><span data-stu-id="1ad66-278">In the previous table, the `LogLevel` is listed from lowest to highest severity.</span></span>

<span data-ttu-id="1ad66-279">[Log](xref:Microsoft.Extensions.Logging.LoggerExtensions) 方法的第一个参数 <xref:Microsoft.Extensions.Logging.LogLevel> 指示日志的严重性。</span><span class="sxs-lookup"><span data-stu-id="1ad66-279">The [Log](xref:Microsoft.Extensions.Logging.LoggerExtensions) method's first parameter, <xref:Microsoft.Extensions.Logging.LogLevel>, indicates the severity of the log.</span></span> <span data-ttu-id="1ad66-280">大多数开发人员调用 [Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) 扩展方法，而不调用 `Log(LogLevel, ...)`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-280">Rather than calling `Log(LogLevel, ...)`, most developers call the [Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) extension methods.</span></span> <span data-ttu-id="1ad66-281">`Log{LogLevel}` 扩展方法[调用 Log 方法并指定 LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-281">The `Log{LogLevel}` extension methods [call the Log method and specify the LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span> <span data-ttu-id="1ad66-282">例如，以下两个日志记录调用功能相同，并生成相同的日志：</span><span class="sxs-lookup"><span data-stu-id="1ad66-282">For example, the following two logging calls are functionally equivalent and produce the same log:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet0&highlight=6-7)]

<span data-ttu-id="1ad66-283">`MyLogEvents.TestItem` 是事件 ID。</span><span class="sxs-lookup"><span data-stu-id="1ad66-283">`MyLogEvents.TestItem` is the event ID.</span></span> <span data-ttu-id="1ad66-284">`MyLogEvents` 是示例应用的一部分，并显示在[日志事件 ID](#leid) 部分中。</span><span class="sxs-lookup"><span data-stu-id="1ad66-284">`MyLogEvents` is part of the sample app and is displayed in the [Log event ID](#leid) section.</span></span>

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="1ad66-285">下面的代码会创建 `Information` 和 `Warning` 日志：</span><span class="sxs-lookup"><span data-stu-id="1ad66-285">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="1ad66-286">在前面的代码中，第一个 `Log{LogLevel}` 参数 `MyLogEvents.GetItem` 是[日志事件 ID](#leid)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-286">In the preceding code, the first `Log{LogLevel}` parameter,`MyLogEvents.GetItem`, is the [Log event ID](#leid).</span></span> <span data-ttu-id="1ad66-287">第二个参数是消息模板，其中的占位符用于填写剩余方法形参提供的实参值。</span><span class="sxs-lookup"><span data-stu-id="1ad66-287">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="1ad66-288">本文档后面的[消息模板](#lmt)部分介绍了方法参数。</span><span class="sxs-lookup"><span data-stu-id="1ad66-288">The method parameters are explained in the [message template](#lmt) section later in this document.</span></span>

<span data-ttu-id="1ad66-289">调用相应的 `Log{LogLevel}` 方法，以控制写入到特定存储介质的日志输出量。</span><span class="sxs-lookup"><span data-stu-id="1ad66-289">Call the appropriate `Log{LogLevel}` method to control how much log output is written to a particular storage medium.</span></span> <span data-ttu-id="1ad66-290">例如：</span><span class="sxs-lookup"><span data-stu-id="1ad66-290">For example:</span></span>

* <span data-ttu-id="1ad66-291">生产中：</span><span class="sxs-lookup"><span data-stu-id="1ad66-291">In production:</span></span>
  * <span data-ttu-id="1ad66-292">在 `Trace` 或 `Information` 级别记录日志会产生大量详细的日志消息。</span><span class="sxs-lookup"><span data-stu-id="1ad66-292">Logging at the `Trace` or `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="1ad66-293">为了控制成本且不超过数据存储限制，请将 `Trace` 和 `Information` 级别消息记录到容量大、成本低的数据存储中。</span><span class="sxs-lookup"><span data-stu-id="1ad66-293">To control costs and not exceed data storage limits, log `Trace` and `Information` level messages to a high-volume, low-cost data store.</span></span> <span data-ttu-id="1ad66-294">考虑将 `Trace` 和 `Information` 限制为特定类别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-294">Consider limiting `Trace` and `Information` to specific categories.</span></span>
  * <span data-ttu-id="1ad66-295">从 `Warning` 到 `Critical` 级别的日志记录应该很少产生日志消息。</span><span class="sxs-lookup"><span data-stu-id="1ad66-295">Logging at `Warning` through `Critical` levels should produce few log messages.</span></span>
    * <span data-ttu-id="1ad66-296">成本和存储限制通常不是问题。</span><span class="sxs-lookup"><span data-stu-id="1ad66-296">Costs and storage limits usually aren't a concern.</span></span>
    * <span data-ttu-id="1ad66-297">很少有日志可以为数据存储选择提供更大的灵活性。</span><span class="sxs-lookup"><span data-stu-id="1ad66-297">Few logs allow more flexibility in data store choices.</span></span>
* <span data-ttu-id="1ad66-298">在开发过程中：</span><span class="sxs-lookup"><span data-stu-id="1ad66-298">In development:</span></span>
  * <span data-ttu-id="1ad66-299">设置为 `Warning`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-299">Set to `Warning`.</span></span>
  * <span data-ttu-id="1ad66-300">在进行故障排除时，添加 `Trace` 或 `Information` 消息。</span><span class="sxs-lookup"><span data-stu-id="1ad66-300">Add `Trace` or `Information` messages when troubleshooting.</span></span> <span data-ttu-id="1ad66-301">若要限制输出，请仅对正在调查的类别设置 `Trace` 或 `Information`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-301">To limit output, set `Trace` or `Information` only for the categories under investigation.</span></span>

<span data-ttu-id="1ad66-302">ASP.NET Core 为框架事件写入日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-302">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="1ad66-303">例如，考虑以下对象的日志输出：</span><span class="sxs-lookup"><span data-stu-id="1ad66-303">For example, consider the log output for:</span></span>

* <span data-ttu-id="1ad66-304">使用 ASP.NET Core 模板创建的 Razor Pages 应用。</span><span class="sxs-lookup"><span data-stu-id="1ad66-304">A Razor Pages app created with the ASP.NET Core templates.</span></span>
* <span data-ttu-id="1ad66-305">日志记录设置为 `Logging:Console:LogLevel:Microsoft:Information`</span><span class="sxs-lookup"><span data-stu-id="1ad66-305">Logging set to `Logging:Console:LogLevel:Microsoft:Information`</span></span>
* <span data-ttu-id="1ad66-306">导航到“隐私”页面：</span><span class="sxs-lookup"><span data-stu-id="1ad66-306">Navigation to the Privacy page:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/Privacy
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint '/Privacy'
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[3]
      Route matched with {page = "/Privacy"}. Executing page /Privacy
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[101]
      Executing handler method DefaultRP.Pages.PrivacyModel.OnGet - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[102]
      Executed handler method OnGet, returned result .
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[103]
      Executing an implicit handler method - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[104]
      Executed an implicit handler method, returned result Microsoft.AspNetCore.Mvc.RazorPages.PageResult.
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[4]
      Executed page /Privacy in 74.5188ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint '/Privacy'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 149.3023ms 200 text/html; charset=utf-8
```

<span data-ttu-id="1ad66-307">以下 JSON 设置了 `Logging:Console:LogLevel:Microsoft:Information`：</span><span class="sxs-lookup"><span data-stu-id="1ad66-307">The following JSON sets `Logging:Console:LogLevel:Microsoft:Information`:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<a name="leid"></a>

## <a name="log-event-id"></a><span data-ttu-id="1ad66-308">日志事件 ID</span><span class="sxs-lookup"><span data-stu-id="1ad66-308">Log event ID</span></span>

<span data-ttu-id="1ad66-309">每个日志都可指定一个事件 ID  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-309">Each log can specify an *event ID*.</span></span> <span data-ttu-id="1ad66-310">示例应用使用 `MyLogEvents` 类来定义事件 ID：</span><span class="sxs-lookup"><span data-stu-id="1ad66-310">The sample app uses the `MyLogEvents` class to define event IDs:</span></span>

<!-- Review: to bad there is no way to use an enum for event ID's -->
[!code-csharp[](index/samples/3.x/TodoApiDTO/Models/MyLogEvents.cs?name=snippet_LoggingEvents)]

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="1ad66-311">事件 ID 与一组事件相关联。</span><span class="sxs-lookup"><span data-stu-id="1ad66-311">An event ID associates a set of events.</span></span> <span data-ttu-id="1ad66-312">例如，与在页面上显示项列表相关的所有日志可能是 1001。</span><span class="sxs-lookup"><span data-stu-id="1ad66-312">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="1ad66-313">日志记录提供程序可将事件 ID 存储在 ID 字段中，存储在日志记录消息中，或者不进行存储。</span><span class="sxs-lookup"><span data-stu-id="1ad66-313">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="1ad66-314">调试提供程序不显示事件 ID。</span><span class="sxs-lookup"><span data-stu-id="1ad66-314">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="1ad66-315">控制台提供程序在类别后的括号中显示事件 ID：</span><span class="sxs-lookup"><span data-stu-id="1ad66-315">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoItemsController[1002]
      Getting item 1
warn: TodoApi.Controllers.TodoItemsController[4000]
      Get(1) NOT FOUND
```

<span data-ttu-id="1ad66-316">一些日志记录提供程序将事件 ID 存储在一个字段中，该字段允许对 ID 进行筛选。</span><span class="sxs-lookup"><span data-stu-id="1ad66-316">Some logging providers store the event ID in a field, which allows for filtering on the ID.</span></span>

<a name="lmt"></a>

## <a name="log-message-template"></a><span data-ttu-id="1ad66-317">日志消息模板</span><span class="sxs-lookup"><span data-stu-id="1ad66-317">Log message template</span></span>
<!-- Review, Each log API uses a message template. -->
<span data-ttu-id="1ad66-318">每个日志 API 都使用一个消息模板。</span><span class="sxs-lookup"><span data-stu-id="1ad66-318">Each log API uses a message template.</span></span> <span data-ttu-id="1ad66-319">消息模板可包含要填写参数的占位符。</span><span class="sxs-lookup"><span data-stu-id="1ad66-319">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="1ad66-320">请在占位符中使用名称而不是数字。</span><span class="sxs-lookup"><span data-stu-id="1ad66-320">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="1ad66-321">占位符的顺序（而非其名称）决定了为其提供值的参数。</span><span class="sxs-lookup"><span data-stu-id="1ad66-321">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="1ad66-322">在以下代码中，消息模板中的参数名称不按顺序排列：</span><span class="sxs-lookup"><span data-stu-id="1ad66-322">In the following code, the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="1ad66-323">上面的代码按顺序通过参数值创建日志消息：</span><span class="sxs-lookup"><span data-stu-id="1ad66-323">The preceding code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: param1, param2
```

<span data-ttu-id="1ad66-324">此方法允许日志记录提供程序实现[语义或结构化日志记录](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-324">This approach allows logging providers to implement [semantic or structured logging](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging).</span></span> <span data-ttu-id="1ad66-325">参数本身会传递给日志记录系统，而不仅仅是格式化的消息模板。</span><span class="sxs-lookup"><span data-stu-id="1ad66-325">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="1ad66-326">这使日志记录提供程序可以将参数值存储为字段。</span><span class="sxs-lookup"><span data-stu-id="1ad66-326">This enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="1ad66-327">例如，考虑使用以下记录器方法：</span><span class="sxs-lookup"><span data-stu-id="1ad66-327">For example, consider the following logger method:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="1ad66-328">例如，登录到 Azure 表存储时：</span><span class="sxs-lookup"><span data-stu-id="1ad66-328">For example, when logging to Azure Table Storage:</span></span>

* <span data-ttu-id="1ad66-329">每个 Azure 表实体都可以有 `ID` 和 `RequestTime` 属性。</span><span class="sxs-lookup"><span data-stu-id="1ad66-329">Each Azure Table entity can have `ID` and `RequestTime` properties.</span></span>
* <span data-ttu-id="1ad66-330">具有属性的表简化了对记录数据的查询。</span><span class="sxs-lookup"><span data-stu-id="1ad66-330">Tables with properties simplify queries on logged data.</span></span> <span data-ttu-id="1ad66-331">例如，查询可以找到特定 `RequestTime` 范围内的所有日志，而不必分析文本消息中的时间。</span><span class="sxs-lookup"><span data-stu-id="1ad66-331">For example, a query can find all logs within a particular `RequestTime` range without having to parse the time out of the text message.</span></span>

## <a name="log-exceptions"></a><span data-ttu-id="1ad66-332">记录异常</span><span class="sxs-lookup"><span data-stu-id="1ad66-332">Log exceptions</span></span>

<span data-ttu-id="1ad66-333">记录器方法的重载采用异常参数：</span><span class="sxs-lookup"><span data-stu-id="1ad66-333">The logger methods have overloads that take an exception parameter:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Exp)]

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="1ad66-334">异常日志记录是特定于提供程序的。</span><span class="sxs-lookup"><span data-stu-id="1ad66-334">Exception logging is provider-specific.</span></span>

### <a name="default-log-level"></a><span data-ttu-id="1ad66-335">默认日志级别</span><span class="sxs-lookup"><span data-stu-id="1ad66-335">Default log level</span></span>

<span data-ttu-id="1ad66-336">如果未设置默认日志级别，则默认的日志级别值为 `Information`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-336">If the default log level is not set, the default log level value is `Information`.</span></span>

<span data-ttu-id="1ad66-337">例如，考虑以下 Web 应用：</span><span class="sxs-lookup"><span data-stu-id="1ad66-337">For example, consider the following web app:</span></span>

* <span data-ttu-id="1ad66-338">使用 ASP.NET Web 应用模板创建的应用。</span><span class="sxs-lookup"><span data-stu-id="1ad66-338">Created with the ASP.NET web app templates.</span></span>
* <span data-ttu-id="1ad66-339">已删除 appsettings.json 和 appsettings.Development.json 或对其进行重命名 。</span><span class="sxs-lookup"><span data-stu-id="1ad66-339">*appsettings.json* and *appsettings.Development.json* deleted or renamed.</span></span>

<span data-ttu-id="1ad66-340">使用上述设置，导航到隐私或主页会生成许多 `Trace`、`Debug` 和 `Information` 消息，并在类别名称中包含 `Microsoft`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-340">With the preceding setup, navigating to the privacy or home page produces many `Trace`, `Debug`, and `Information`  messages with `Microsoft` in the category name.</span></span>

<span data-ttu-id="1ad66-341">如果未在配置中设置默认日志级别，以下代码会设置默认日志级别：</span><span class="sxs-lookup"><span data-stu-id="1ad66-341">The following code sets the default log level when the default log level is not set in configuration:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_MinLevel&highlight=10)]

<!-- review required: I say this a couple times -->
<span data-ttu-id="1ad66-342">通常，日志级别应在配置中指定，而不是在代码中指定。</span><span class="sxs-lookup"><span data-stu-id="1ad66-342">Generally, log levels should be specified in configuration and not code.</span></span>

### <a name="filter-function"></a><span data-ttu-id="1ad66-343">筛选器函数</span><span class="sxs-lookup"><span data-stu-id="1ad66-343">Filter function</span></span>

<span data-ttu-id="1ad66-344">对配置或代码没有向其分配规则的所有提供程序和类别调用筛选器函数：</span><span class="sxs-lookup"><span data-stu-id="1ad66-344">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterFunction)]

<span data-ttu-id="1ad66-345">如果类别包含 `Controller` 或 `Microsoft`，并且日志级别为 `Information` 或更高级别，以上代码会显示控制台日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-345">The preceding code displays console logs when the category contains `Controller` or `Microsoft` and the log level is `Information` or higher.</span></span>

<span data-ttu-id="1ad66-346">通常，日志级别应在配置中指定，而不是在代码中指定。</span><span class="sxs-lookup"><span data-stu-id="1ad66-346">Generally, log levels should be specified in configuration and not code.</span></span>

## <a name="aspnet-core-and-ef-core-categories"></a><span data-ttu-id="1ad66-347">ASP.NET Core 和 EF Core 类别</span><span class="sxs-lookup"><span data-stu-id="1ad66-347">ASP.NET Core and EF Core categories</span></span>

<span data-ttu-id="1ad66-348">下表包含 ASP.NET Core 和 Entity Framework Core 使用的一些类别，并带有有关日志的注释：</span><span class="sxs-lookup"><span data-stu-id="1ad66-348">The following table contains some categories used by ASP.NET Core and Entity Framework Core, with notes about the logs:</span></span>

| <span data-ttu-id="1ad66-349">类别</span><span class="sxs-lookup"><span data-stu-id="1ad66-349">Category</span></span>                            | <span data-ttu-id="1ad66-350">说明</span><span class="sxs-lookup"><span data-stu-id="1ad66-350">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="1ad66-351">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="1ad66-351">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="1ad66-352">常规 ASP.NET Core 诊断。</span><span class="sxs-lookup"><span data-stu-id="1ad66-352">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="1ad66-353">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="1ad66-353">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="1ad66-354">考虑、找到并使用了哪些密钥。</span><span class="sxs-lookup"><span data-stu-id="1ad66-354">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="1ad66-355">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="1ad66-355">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="1ad66-356">所允许的主机。</span><span class="sxs-lookup"><span data-stu-id="1ad66-356">Hosts allowed.</span></span> |
| <span data-ttu-id="1ad66-357">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="1ad66-357">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="1ad66-358">HTTP 请求完成的时间和启动时间。</span><span class="sxs-lookup"><span data-stu-id="1ad66-358">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="1ad66-359">加载了哪些承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="1ad66-359">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="1ad66-360">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="1ad66-360">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="1ad66-361">MVC 和 Razor 诊断。</span><span class="sxs-lookup"><span data-stu-id="1ad66-361">MVC and Razor diagnostics.</span></span> <span data-ttu-id="1ad66-362">模型绑定、筛选器执行、视图编译和操作选择。</span><span class="sxs-lookup"><span data-stu-id="1ad66-362">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="1ad66-363">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="1ad66-363">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="1ad66-364">路由匹配信息。</span><span class="sxs-lookup"><span data-stu-id="1ad66-364">Route matching information.</span></span> |
| <span data-ttu-id="1ad66-365">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="1ad66-365">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="1ad66-366">连接启动、停止和保持活动响应。</span><span class="sxs-lookup"><span data-stu-id="1ad66-366">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="1ad66-367">HTTP 证书信息。</span><span class="sxs-lookup"><span data-stu-id="1ad66-367">HTTPS certificate information.</span></span> |
| <span data-ttu-id="1ad66-368">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="1ad66-368">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="1ad66-369">提供的文件。</span><span class="sxs-lookup"><span data-stu-id="1ad66-369">Files served.</span></span> |
| <span data-ttu-id="1ad66-370">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="1ad66-370">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="1ad66-371">常规 Entity Framework Core 诊断。</span><span class="sxs-lookup"><span data-stu-id="1ad66-371">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="1ad66-372">数据库活动和配置、更改检测、迁移。</span><span class="sxs-lookup"><span data-stu-id="1ad66-372">Database activity and configuration, change detection, migrations.</span></span> |

<span data-ttu-id="1ad66-373">若要在控制台窗口中查看更多类别，请将 appsettings.Development.json 设置为以下各项：</span><span class="sxs-lookup"><span data-stu-id="1ad66-373">To view more categories in the console window, set **appsettings.Development.json** to the following:</span></span>

[!code-json[](index/samples/3.x/MyMain/appsettings.Trace.json)]

<!-- Review: What other providers support scopes? Console is not generally used in staging/production  -->

<a name="logscopes"></a>

## <a name="log-scopes"></a><span data-ttu-id="1ad66-374">日志作用域</span><span class="sxs-lookup"><span data-stu-id="1ad66-374">Log scopes</span></span>

 <span data-ttu-id="1ad66-375">“作用域”可对一组逻辑操作分组  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-375">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="1ad66-376">此分组可用于将相同的数据附加到作为集合的一部分而创建的每个日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-376">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="1ad66-377">例如，在处理事务期间创建的每个日志都可包括事务 ID。</span><span class="sxs-lookup"><span data-stu-id="1ad66-377">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="1ad66-378">范围：</span><span class="sxs-lookup"><span data-stu-id="1ad66-378">A scope:</span></span>

* <span data-ttu-id="1ad66-379">是 <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> 方法返回的 <xref:System.IDisposable> 类型。</span><span class="sxs-lookup"><span data-stu-id="1ad66-379">Is an <xref:System.IDisposable> type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method.</span></span>
* <span data-ttu-id="1ad66-380">持续到处置完毕。</span><span class="sxs-lookup"><span data-stu-id="1ad66-380">Lasts until it's disposed.</span></span>

<span data-ttu-id="1ad66-381">以下提供程序支持范围：</span><span class="sxs-lookup"><span data-stu-id="1ad66-381">The following providers support scopes:</span></span>

* `Console`
* [<span data-ttu-id="1ad66-382">AzureAppServicesFile 和 AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="1ad66-382">AzureAppServicesFile and AzureAppServicesBlob</span></span>](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

<span data-ttu-id="1ad66-383">要使用作用域，请在 `using` 块中包装记录器调用：</span><span class="sxs-lookup"><span data-stu-id="1ad66-383">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Scopes)]

<span data-ttu-id="1ad66-384">以下 JSON 为控制台提供程序启用范围：</span><span class="sxs-lookup"><span data-stu-id="1ad66-384">The following JSON enables scopes for the console provider:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Scopes.json)]

<span data-ttu-id="1ad66-385">下列代码为控制台提供程序启用作用域：</span><span class="sxs-lookup"><span data-stu-id="1ad66-385">The following code enables scopes for the console provider:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_Scopes)]

<span data-ttu-id="1ad66-386">通常，日志记录应在配置中指定，而不是在代码中指定。</span><span class="sxs-lookup"><span data-stu-id="1ad66-386">Generally, logging should be specified in configuration and not code.</span></span>

<a name="bilp"></a>

## <a name="built-in-logging-providers"></a><span data-ttu-id="1ad66-387">内置日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="1ad66-387">Built-in logging providers</span></span>

<span data-ttu-id="1ad66-388">ASP.NET Core 包含以下日志记录提供程序：</span><span class="sxs-lookup"><span data-stu-id="1ad66-388">ASP.NET Core includes the following logging providers:</span></span>

* [<span data-ttu-id="1ad66-389">控制台</span><span class="sxs-lookup"><span data-stu-id="1ad66-389">Console</span></span>](#console)
* [<span data-ttu-id="1ad66-390">调试</span><span class="sxs-lookup"><span data-stu-id="1ad66-390">Debug</span></span>](#debug)
* [<span data-ttu-id="1ad66-391">EventSource</span><span class="sxs-lookup"><span data-stu-id="1ad66-391">EventSource</span></span>](#event-source)
* [<span data-ttu-id="1ad66-392">EventLog</span><span class="sxs-lookup"><span data-stu-id="1ad66-392">EventLog</span></span>](#welog)
* [<span data-ttu-id="1ad66-393">AzureAppServicesFile 和 AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="1ad66-393">AzureAppServicesFile and AzureAppServicesBlob</span></span>](#azure-app-service)
* [<span data-ttu-id="1ad66-394">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="1ad66-394">ApplicationInsights</span></span>](#azure-application-insights)

<span data-ttu-id="1ad66-395">要了解 `stdout` 以及如何通过 ASP.NET Core 模块调试日志记录，请参阅 <xref:test/troubleshoot-azure-iis> 和 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="1ad66-395">For information on `stdout` and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console"></a><span data-ttu-id="1ad66-396">控制台</span><span class="sxs-lookup"><span data-stu-id="1ad66-396">Console</span></span>

<span data-ttu-id="1ad66-397">`Console` 提供程序将输出记录到控制台。</span><span class="sxs-lookup"><span data-stu-id="1ad66-397">The `Console` provider logs output to the console.</span></span> <span data-ttu-id="1ad66-398">如需详细了解如何在开发环境中查看 `Console` 日志，请参阅[记录来自 dotnet run 和 Visual Studio 的输出](#dnrvs)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-398">For more information on viewing `Console` logs in development, see [Logging output from dotnet run and Visual Studio](#dnrvs).</span></span>

### <a name="debug"></a><span data-ttu-id="1ad66-399">调试</span><span class="sxs-lookup"><span data-stu-id="1ad66-399">Debug</span></span>

<span data-ttu-id="1ad66-400">`Debug` 提供程序通过使用 [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) 类来写入日志输出。</span><span class="sxs-lookup"><span data-stu-id="1ad66-400">The `Debug` provider writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class.</span></span> <span data-ttu-id="1ad66-401">对 `System.Diagnostics.Debug.WriteLine` 的调用写入到 `Debug` 提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-401">Calls to `System.Diagnostics.Debug.WriteLine` write to the `Debug` provider.</span></span>

<span data-ttu-id="1ad66-402">在 Linux 上，`Debug` 提供程序日志位置取决于分发，并且可以是以下位置之一：</span><span class="sxs-lookup"><span data-stu-id="1ad66-402">On Linux, the `Debug` provider log location is distribution-dependent and may be one of the following:</span></span>

* <span data-ttu-id="1ad66-403">*/var/log/message*</span><span class="sxs-lookup"><span data-stu-id="1ad66-403">*/var/log/message*</span></span>
* <span data-ttu-id="1ad66-404">*/var/log/syslog*</span><span class="sxs-lookup"><span data-stu-id="1ad66-404">*/var/log/syslog*</span></span>

### <a name="event-source"></a><span data-ttu-id="1ad66-405">事件来源</span><span class="sxs-lookup"><span data-stu-id="1ad66-405">Event Source</span></span>

<span data-ttu-id="1ad66-406">`EventSource` 提供程序写入名称为 `Microsoft-Extensions-Logging` 的跨平台事件源。</span><span class="sxs-lookup"><span data-stu-id="1ad66-406">The `EventSource` provider writes to a cross-platform event source with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="1ad66-407">在 Windows 上，提供程序使用的是 [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-407">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="1ad66-408">dotnet 跟踪工具</span><span class="sxs-lookup"><span data-stu-id="1ad66-408">dotnet trace tooling</span></span>

<span data-ttu-id="1ad66-409">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) 工具是一种跨平台 CLI 全局工具，可用于收集正在运行的进程的 .NET Core 跟踪。</span><span class="sxs-lookup"><span data-stu-id="1ad66-409">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="1ad66-410">该工具会使用 <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> 收集 <xref:Microsoft.Extensions.Logging.EventSource> 提供程序数据。</span><span class="sxs-lookup"><span data-stu-id="1ad66-410">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="1ad66-411">有关安装说明，请参阅 [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-411">See [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) for installation instructions.</span></span>

<span data-ttu-id="1ad66-412">使用 dotnet 跟踪工具从应用中收集跟踪：</span><span class="sxs-lookup"><span data-stu-id="1ad66-412">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="1ad66-413">使用 `dotnet run` 命令运行此应用。</span><span class="sxs-lookup"><span data-stu-id="1ad66-413">Run the app with the `dotnet run` command.</span></span>
1. <span data-ttu-id="1ad66-414">确定 .NET Core 应用的进程标识符 (PID)：</span><span class="sxs-lookup"><span data-stu-id="1ad66-414">Determine the process identifier (PID) of the .NET Core app:</span></span>
   * <span data-ttu-id="1ad66-415">在 Windows 上，使用下述方法之一：</span><span class="sxs-lookup"><span data-stu-id="1ad66-415">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="1ad66-416">任务管理器 (Ctrl+Alt+Del)</span><span class="sxs-lookup"><span data-stu-id="1ad66-416">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="1ad66-417">tasklist 命令</span><span class="sxs-lookup"><span data-stu-id="1ad66-417">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="1ad66-418">Get-Process Powershell 命令</span><span class="sxs-lookup"><span data-stu-id="1ad66-418">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="1ad66-419">在 Linux 上，使用 [pidof 命令](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-419">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="1ad66-420">找到进程的 PID，它与应用的程序集的名称相同。</span><span class="sxs-lookup"><span data-stu-id="1ad66-420">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="1ad66-421">执行 `dotnet trace` 命令。</span><span class="sxs-lookup"><span data-stu-id="1ad66-421">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="1ad66-422">常规命令语法：</span><span class="sxs-lookup"><span data-stu-id="1ad66-422">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="1ad66-423">使用 PowerShell 命令行界面时，将 `--providers` 值用单引号 (`'`) 引起来：</span><span class="sxs-lookup"><span data-stu-id="1ad66-423">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="1ad66-424">在非 Windows 平台上，添加 `-f speedscope` 选项，将输出跟踪文件更改为 `speedscope`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-424">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="1ad66-425">关键字</span><span class="sxs-lookup"><span data-stu-id="1ad66-425">Keyword</span></span> | <span data-ttu-id="1ad66-426">说明</span><span class="sxs-lookup"><span data-stu-id="1ad66-426">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="1ad66-427">1</span><span class="sxs-lookup"><span data-stu-id="1ad66-427">1</span></span>       | <span data-ttu-id="1ad66-428">记录有关 `LoggingEventSource` 的 meta 事件。</span><span class="sxs-lookup"><span data-stu-id="1ad66-428">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="1ad66-429">请不要从 `ILogger` 记录事件。</span><span class="sxs-lookup"><span data-stu-id="1ad66-429">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="1ad66-430">2</span><span class="sxs-lookup"><span data-stu-id="1ad66-430">2</span></span>       | <span data-ttu-id="1ad66-431">在调用 `ILogger.Log()` 时启用 `Message` 事件。</span><span class="sxs-lookup"><span data-stu-id="1ad66-431">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="1ad66-432">以编程（未格式化）方式提供信息。</span><span class="sxs-lookup"><span data-stu-id="1ad66-432">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="1ad66-433">4</span><span class="sxs-lookup"><span data-stu-id="1ad66-433">4</span></span>       | <span data-ttu-id="1ad66-434">在调用 `ILogger.Log()` 时启用 `FormatMessage` 事件。</span><span class="sxs-lookup"><span data-stu-id="1ad66-434">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="1ad66-435">提供格式化字符串版本的信息。</span><span class="sxs-lookup"><span data-stu-id="1ad66-435">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="1ad66-436">8</span><span class="sxs-lookup"><span data-stu-id="1ad66-436">8</span></span>       | <span data-ttu-id="1ad66-437">在调用 `ILogger.Log()` 时启用 `MessageJson` 事件。</span><span class="sxs-lookup"><span data-stu-id="1ad66-437">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="1ad66-438">提供参数的 JSON 表示形式。</span><span class="sxs-lookup"><span data-stu-id="1ad66-438">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="1ad66-439">事件级别</span><span class="sxs-lookup"><span data-stu-id="1ad66-439">Event Level</span></span> | <span data-ttu-id="1ad66-440">描述</span><span class="sxs-lookup"><span data-stu-id="1ad66-440">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="1ad66-441">0</span><span class="sxs-lookup"><span data-stu-id="1ad66-441">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="1ad66-442">1</span><span class="sxs-lookup"><span data-stu-id="1ad66-442">1</span></span>           | `Critical`      |
   | <span data-ttu-id="1ad66-443">2</span><span class="sxs-lookup"><span data-stu-id="1ad66-443">2</span></span>           | `Error`         |
   | <span data-ttu-id="1ad66-444">3</span><span class="sxs-lookup"><span data-stu-id="1ad66-444">3</span></span>           | `Warning`       |
   | <span data-ttu-id="1ad66-445">4</span><span class="sxs-lookup"><span data-stu-id="1ad66-445">4</span></span>           | `Informational` |
   | <span data-ttu-id="1ad66-446">5</span><span class="sxs-lookup"><span data-stu-id="1ad66-446">5</span></span>           | `Verbose`       |

   <span data-ttu-id="1ad66-447">`{Logger Category}` 和 `{Event Level}` 的 `FilterSpecs` 条目表示其他日志筛选条件。</span><span class="sxs-lookup"><span data-stu-id="1ad66-447">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="1ad66-448">请用分号 (`;`) 分隔 `FilterSpecs` 条目。</span><span class="sxs-lookup"><span data-stu-id="1ad66-448">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="1ad66-449">下例使用 Windows 命令界面（`--providers` 值不用单引号引起来）  ：</span><span class="sxs-lookup"><span data-stu-id="1ad66-449">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="1ad66-450">上面的命令会激活：</span><span class="sxs-lookup"><span data-stu-id="1ad66-450">The preceding command activates:</span></span>

   * <span data-ttu-id="1ad66-451">事件源记录器，它用于为错误 (`2`) 生成格式化字符串 (`4`)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-451">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="1ad66-452">`Informational` 日志记录级别 (`4`) 的 `Microsoft.AspNetCore.Hosting` 日志记录。</span><span class="sxs-lookup"><span data-stu-id="1ad66-452">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="1ad66-453">通过按 Enter 键或 Ctrl+C 停止 dotnet 跟踪工具。</span><span class="sxs-lookup"><span data-stu-id="1ad66-453">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="1ad66-454">跟踪使用名称 trace.nettrace 保存在执行 `dotnet trace` 命令的文件夹中  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-454">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="1ad66-455">使用[预览](#perfview)功能打开跟踪。</span><span class="sxs-lookup"><span data-stu-id="1ad66-455">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="1ad66-456">打开 trace.nettrace 文件并浏览跟踪事件  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-456">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="1ad66-457">如果应用不使用 `CreateDefaultBuilder` 生成主机，则请向应用的日志记录配置添加[事件源提供程序](#event-source-provider)</span><span class="sxs-lookup"><span data-stu-id="1ad66-457">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

<span data-ttu-id="1ad66-458">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="1ad66-458">For more information, see:</span></span>

* <span data-ttu-id="1ad66-459">[跟踪性能分析实用工具 (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace)（.NET Core 文档）</span><span class="sxs-lookup"><span data-stu-id="1ad66-459">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="1ad66-460">[跟踪性能分析实用工具 (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md)（dotnet/诊断 GitHub 存储库文档）</span><span class="sxs-lookup"><span data-stu-id="1ad66-460">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="1ad66-461">[LoggingEventSource 类](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource)（.NET API 浏览器）</span><span class="sxs-lookup"><span data-stu-id="1ad66-461">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="1ad66-462">[LoggingEventSource 参考源 (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs)：要获得其他版本的参考源，请将分支更改为 `release/{Version}`，其中 `{Version}` 是所需的 ASP.NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="1ad66-462">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="1ad66-463">[Perfview](#perfview)：适用于查看事件源跟踪。</span><span class="sxs-lookup"><span data-stu-id="1ad66-463">[Perfview](#perfview): Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="1ad66-464">Perfview</span><span class="sxs-lookup"><span data-stu-id="1ad66-464">Perfview</span></span>

<span data-ttu-id="1ad66-465">使用 [PerfView 实用工具](https://github.com/Microsoft/perfview)收集和查看日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-465">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="1ad66-466">虽然其他工具也可以查看 ETW 日志，但在处理由 ASP.NET Core 发出的 ETW 事件时，使用 PerfView 能获得最佳体验。</span><span class="sxs-lookup"><span data-stu-id="1ad66-466">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="1ad66-467">要将 PerfView 配置为收集此提供程序记录的事件，请向 Additional Providers 列表添加字符串 `*Microsoft-Extensions-Logging` 。</span><span class="sxs-lookup"><span data-stu-id="1ad66-467">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="1ad66-468">请勿遗漏字符串起始处的 `*`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-468">Don't miss the `*` at the start of the string.</span></span>

<a name="welog"></a>

### <a name="windows-eventlog"></a><span data-ttu-id="1ad66-469">Windows 事件日志</span><span class="sxs-lookup"><span data-stu-id="1ad66-469">Windows EventLog</span></span>

<span data-ttu-id="1ad66-470">`EventLog` 提供程序将日志输出发送到 Windows 事件日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-470">The `EventLog` provider sends log output to the Windows Event Log.</span></span> <span data-ttu-id="1ad66-471">与其他提供程序不同，`EventLog` 提供程序不继承默认的非提供程序设置。</span><span class="sxs-lookup"><span data-stu-id="1ad66-471">Unlike the other providers, the `EventLog` provider does ***not*** inherit the default non-provider settings.</span></span> <span data-ttu-id="1ad66-472">如果未指定 `EventLog` 日志设置，则它们[默认为 LogLevel.Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-472">If `EventLog` log settings aren't specified, they [default to LogLevel.Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).</span></span>

<span data-ttu-id="1ad66-473">若要记录低于 <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> 的事件，请显式设置日志级别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-473">To log events lower than <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType>, explicitly set the log level.</span></span> <span data-ttu-id="1ad66-474">以下示例将事件日志的默认日志级别设置为 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>：</span><span class="sxs-lookup"><span data-stu-id="1ad66-474">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

<span data-ttu-id="1ad66-475">[AddEventLog 重载](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)可以传入 <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>。</span><span class="sxs-lookup"><span data-stu-id="1ad66-475">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) can pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="1ad66-476">如果为 `null` 或未指定，则使用以下默认设置：</span><span class="sxs-lookup"><span data-stu-id="1ad66-476">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="1ad66-477">`LogName`：“Application”</span><span class="sxs-lookup"><span data-stu-id="1ad66-477">`LogName`: "Application"</span></span>
* <span data-ttu-id="1ad66-478">`SourceName`：“.NET Runtime”</span><span class="sxs-lookup"><span data-stu-id="1ad66-478">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="1ad66-479">`MachineName`：使用本地计算机名称。</span><span class="sxs-lookup"><span data-stu-id="1ad66-479">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="1ad66-480">以下代码将 `SourceName` 从默认值 `".NET Runtime"` 更改为 `MyLogs`：</span><span class="sxs-lookup"><span data-stu-id="1ad66-480">The following code changes the `SourceName` from the default value of `".NET Runtime"` to `MyLogs`:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippetEventLog)]

### <a name="azure-app-service"></a><span data-ttu-id="1ad66-481">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="1ad66-481">Azure App Service</span></span>

<span data-ttu-id="1ad66-482">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 提供程序包将日志写入 Azure App Service 应用的文件系统，以及 Azure 存储帐户中的 [blob 存储](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-482">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

<span data-ttu-id="1ad66-483">共享框架中不包括该提供程序包。</span><span class="sxs-lookup"><span data-stu-id="1ad66-483">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="1ad66-484">若要使用提供程序，请将提供程序包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="1ad66-484">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="1ad66-485">要配置提供程序设置，请使用 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> 和 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="1ad66-485">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_AzLogOptions)]

<span data-ttu-id="1ad66-486">部署到 Azure 应用服务时，应用使用 Azure 门户的“应用服务”页面的[应用服务日志](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows)部分中的设置。</span><span class="sxs-lookup"><span data-stu-id="1ad66-486">When deployed to Azure App Service, the app uses the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="1ad66-487">更新以下设置后，更改立即生效，无需重启或重新部署应用。</span><span class="sxs-lookup"><span data-stu-id="1ad66-487">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="1ad66-488">应用程序日志记录(Filesystem)</span><span class="sxs-lookup"><span data-stu-id="1ad66-488">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="1ad66-489">应用程序日志记录(Blob)</span><span class="sxs-lookup"><span data-stu-id="1ad66-489">**Application Logging (Blob)**</span></span>

<span data-ttu-id="1ad66-490">日志文件的默认位置是 D:\\home\\LogFiles\\Application 文件夹，默认文件名为 diagnostics-yyyymmdd.txt   。</span><span class="sxs-lookup"><span data-stu-id="1ad66-490">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="1ad66-491">默认文件大小上限为 10 MB，默认最大保留文件数为 2。</span><span class="sxs-lookup"><span data-stu-id="1ad66-491">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="1ad66-492">默认 blob 名为 {app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-492">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="1ad66-493">仅当项目在 Azure 环境中运行时，此提供程序才记录日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-493">This provider only logs when the project runs in the Azure environment.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="1ad66-494">Azure 日志流式处理</span><span class="sxs-lookup"><span data-stu-id="1ad66-494">Azure log streaming</span></span>

<span data-ttu-id="1ad66-495">Azure 日志流式处理支持从以下位置实时查看日志活动：</span><span class="sxs-lookup"><span data-stu-id="1ad66-495">Azure log streaming supports viewing log activity in real time from:</span></span>

* <span data-ttu-id="1ad66-496">应用服务器</span><span class="sxs-lookup"><span data-stu-id="1ad66-496">The app server</span></span>
* <span data-ttu-id="1ad66-497">Web 服务器</span><span class="sxs-lookup"><span data-stu-id="1ad66-497">The web server</span></span>
* <span data-ttu-id="1ad66-498">请求跟踪失败</span><span class="sxs-lookup"><span data-stu-id="1ad66-498">Failed request tracing</span></span>

<span data-ttu-id="1ad66-499">要配置 Azure 日志流式处理，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1ad66-499">To configure Azure log streaming:</span></span>

* <span data-ttu-id="1ad66-500">从应用的门户页导航到“应用服务日志”页。</span><span class="sxs-lookup"><span data-stu-id="1ad66-500">Navigate to the **App Service logs** page from the app's portal page.</span></span>
* <span data-ttu-id="1ad66-501">将“应用程序日志记录(Filesystem)”设置为“开”   。</span><span class="sxs-lookup"><span data-stu-id="1ad66-501">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="1ad66-502">选择日志级别  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-502">Choose the log **Level**.</span></span> <span data-ttu-id="1ad66-503">此设置仅适用于 Azure 日志流式处理。</span><span class="sxs-lookup"><span data-stu-id="1ad66-503">This setting only applies to Azure log streaming.</span></span>

<span data-ttu-id="1ad66-504">导航到“日志流”页面以查看日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-504">Navigate to the **Log Stream** page to view logs.</span></span> <span data-ttu-id="1ad66-505">记录的消息使用 `ILogger` 接口进行记录。</span><span class="sxs-lookup"><span data-stu-id="1ad66-505">The logged messages are logged with the `ILogger` interface.</span></span>

### <a name="azure-application-insights"></a><span data-ttu-id="1ad66-506">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="1ad66-506">Azure Application Insights</span></span>

<span data-ttu-id="1ad66-507">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) 提供程序包将日志写入 [Azure Application Insights](/azure/azure-monitor/app/cloudservices)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-507">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to [Azure Application Insights](/azure/azure-monitor/app/cloudservices).</span></span> <span data-ttu-id="1ad66-508">Application Insights 是一项服务，可监视 Web 应用并提供用于查询和分析遥测数据的工具。</span><span class="sxs-lookup"><span data-stu-id="1ad66-508">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="1ad66-509">如果使用此提供程序，则可以使用 Application Insights 工具来查询和分析日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-509">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="1ad66-510">日志记录提供程序作为 [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)（这是提供 ASP.NET Core 的所有可用遥测的包）的依赖项包括在内。</span><span class="sxs-lookup"><span data-stu-id="1ad66-510">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="1ad66-511">如果使用此包，则无需安装提供程序包。</span><span class="sxs-lookup"><span data-stu-id="1ad66-511">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="1ad66-512">[Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) 包适用于 ASP.NET 4.x，而不适用于 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="1ad66-512">The [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package is for ASP.NET 4.x, not ASP.NET Core.</span></span>

<span data-ttu-id="1ad66-513">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="1ad66-513">For more information, see the following resources:</span></span>

* [<span data-ttu-id="1ad66-514">Application Insights 概述</span><span class="sxs-lookup"><span data-stu-id="1ad66-514">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="1ad66-515">[用于 ASP.NET Core 应用程序的 Application Insights](/azure/azure-monitor/app/asp-net-core) - 如果想要实现各种 Application Insights 遥测以及日志记录，请从这里开始。</span><span class="sxs-lookup"><span data-stu-id="1ad66-515">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="1ad66-516">[.NET Core ILogger 日志的 ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - 如果想要在没有其他 Application Insights 遥测的情况下实现日志记录提供程序，请从这里开始。</span><span class="sxs-lookup"><span data-stu-id="1ad66-516">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="1ad66-517">[Application Insights 日志记录适配器](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-517">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="1ad66-518">[安装、配置和初始化 Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn 网站上的交互式教程。</span><span class="sxs-lookup"><span data-stu-id="1ad66-518">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="1ad66-519">第三方日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="1ad66-519">Third-party logging providers</span></span>

<span data-ttu-id="1ad66-520">适用于 ASP.NET Core 的第三方日志记录框架：</span><span class="sxs-lookup"><span data-stu-id="1ad66-520">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="1ad66-521">[elmah.io](https://elmah.io/)（[GitHub 存储库](https://github.com/elmahio/Elmah.Io.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-521">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="1ad66-522">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html)（[GitHub 存储库](https://github.com/mattwcole/gelf-extensions-logging)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-522">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="1ad66-523">[JSNLog](https://jsnlog.com/)（[GitHub 存储库](https://github.com/mperdeck/jsnlog)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-523">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="1ad66-524">[KissLog.net](https://kisslog.net/)（[GitHub 存储库](https://github.com/catalingavan/KissLog-net)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-524">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="1ad66-525">[Log4Net](https://logging.apache.org/log4net/)（[GitHub 存储库](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-525">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="1ad66-526">[Loggr](https://loggr.net/)（[GitHub 存储库](https://github.com/imobile3/Loggr.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-526">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="1ad66-527">[NLog](https://nlog-project.org/)（[GitHub 存储库](https://github.com/NLog/NLog.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-527">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="1ad66-528">[PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/)（[GitHub 存储库](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-528">[PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([GitHub repo](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))</span></span>
* <span data-ttu-id="1ad66-529">[Sentry](https://sentry.io/welcome/)（[GitHub 存储库](https://github.com/getsentry/sentry-dotnet)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-529">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="1ad66-530">[Serilog](https://serilog.net/)（[GitHub 存储库](https://github.com/serilog/serilog-aspnetcore)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-530">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="1ad66-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging)（[Github 存储库](https://github.com/googleapis/google-cloud-dotnet)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="1ad66-532">某些第三方框架可以执行[语义日志记录（又称结构化日志记录）](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-532">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="1ad66-533">使用第三方框架类似于使用以下内置提供程序之一：</span><span class="sxs-lookup"><span data-stu-id="1ad66-533">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="1ad66-534">将 NuGet 包添加到你的项目。</span><span class="sxs-lookup"><span data-stu-id="1ad66-534">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="1ad66-535">调用日志记录框架提供的 `ILoggerFactory` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="1ad66-535">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="1ad66-536">有关详细信息，请参阅各提供程序的相关文档。</span><span class="sxs-lookup"><span data-stu-id="1ad66-536">For more information, see each provider's documentation.</span></span> <span data-ttu-id="1ad66-537">Microsoft 不支持第三方日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-537">Third-party logging providers aren't supported by Microsoft.</span></span>

<a name="nhca"></a>

## <a name="non-host-console-app"></a><span data-ttu-id="1ad66-538">非托管控制台应用</span><span class="sxs-lookup"><span data-stu-id="1ad66-538">Non-host console app</span></span>

<span data-ttu-id="1ad66-539">要通过示例了解如何在非 Web 控制台应用中使用一般主机，请参阅[后台任务示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) 的 Program.cs 文件 (<xref:fundamentals/host/hosted-services>)  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-539">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="1ad66-540">对于没有通用主机的应用，日志记录代码在[添加提供程序](#add-providers)和[创建记录器](#create-logs)的方式上有所不同。</span><span class="sxs-lookup"><span data-stu-id="1ad66-540">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> 

### <a name="logging-providers"></a><span data-ttu-id="1ad66-541">日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="1ad66-541">Logging providers</span></span>

<span data-ttu-id="1ad66-542">在非主机控制台应用中，在创建 `LoggerFactory` 时调用提供程序的 `Add{provider name}` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="1ad66-542">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11-12)]

### <a name="create-logs"></a><span data-ttu-id="1ad66-543">创建日志</span><span class="sxs-lookup"><span data-stu-id="1ad66-543">Create logs</span></span>

<span data-ttu-id="1ad66-544">若要创建日志，请使用 <xref:Microsoft.Extensions.Logging.ILogger%601> 对象。</span><span class="sxs-lookup"><span data-stu-id="1ad66-544">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="1ad66-545">使用 `LoggerFactory` 创建一个 `ILogger`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-545">Use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="1ad66-546">以下示例创建类别为 `LoggingConsoleApp.Program` 的记录器。</span><span class="sxs-lookup"><span data-stu-id="1ad66-546">The following example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=14)]

<span data-ttu-id="1ad66-547">在以下 ASP.NET CORE 示例中，记录器用于创建级别为 `Information` 的日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-547">In the following ASP.NET CORE examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="1ad66-548">日志“级别”代表所记录事件的严重程度。</span><span class="sxs-lookup"><span data-stu-id="1ad66-548">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=15)]

<span data-ttu-id="1ad66-549">本文档中详细介绍了[级别](#log-level)和[类别](#log-category)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-549">[Levels](#log-level) and [categories](#log-category) are explained in more detail in this document.</span></span>

<a name="lhc"></a>

## <a name="log-during-host-construction"></a><span data-ttu-id="1ad66-550">主机构造过程中的日志</span><span class="sxs-lookup"><span data-stu-id="1ad66-550">Log during host construction</span></span>

<span data-ttu-id="1ad66-551">不直接支持在主机构造期间进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="1ad66-551">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="1ad66-552">但是，可以使用单独的记录器。</span><span class="sxs-lookup"><span data-stu-id="1ad66-552">However, a separate logger can be used.</span></span> <span data-ttu-id="1ad66-553">在以下示例中，[Serilog](https://serilog.net/) 记录器用于登录 `CreateHostBuilder`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-553">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="1ad66-554">`AddSerilog` 使用 `Log.Logger` 中指定的静态配置：</span><span class="sxs-lookup"><span data-stu-id="1ad66-554">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

<a name="csdi"></a>

## <a name="configure-a-service-that-depends-on-ilogger"></a><span data-ttu-id="1ad66-555">配置依赖于 ILogger 的服务</span><span class="sxs-lookup"><span data-stu-id="1ad66-555">Configure a service that depends on ILogger</span></span>

<span data-ttu-id="1ad66-556">由于为 Web 主机创建了单独的 DI 容器，所以在 ASP.NET Core 的早期版本中，构造函数将记录器注入到 `Startup` 工作。</span><span class="sxs-lookup"><span data-stu-id="1ad66-556">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="1ad66-557">若要了解为何仅为通用主机创建一个容器，请参阅[重大更改公告](https://github.com/aspnet/Announcements/issues/353)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-557">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="1ad66-558">若要配置依赖于 `ILogger<T>` 的服务，请使用构造函数注入或提供工厂方法。</span><span class="sxs-lookup"><span data-stu-id="1ad66-558">To configure a service that depends on `ILogger<T>`, use constructor injection or provide a factory method.</span></span> <span data-ttu-id="1ad66-559">只有在没有其他选择的情况下，才建议使用工厂方法。</span><span class="sxs-lookup"><span data-stu-id="1ad66-559">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="1ad66-560">例如，假设某个服务需要由 DI 提供的 `ILogger<T>` 实例：</span><span class="sxs-lookup"><span data-stu-id="1ad66-560">For example, consider a service that needs an `ILogger<T>` instance provided by DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup2.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="1ad66-561">前面突出显示的代码是 [Func](/dotnet/api/system.func-2)，该代码在 DI 容器第一次需要构造 `MyService` 实例时运行。</span><span class="sxs-lookup"><span data-stu-id="1ad66-561">The preceding highlighted code is a [Func](/dotnet/api/system.func-2) that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="1ad66-562">可以用这种方式访问任何已注册的服务。</span><span class="sxs-lookup"><span data-stu-id="1ad66-562">You can access any of the registered services in this way.</span></span>

<a name="clms"></a>

## <a name="create-logs-in-main"></a><span data-ttu-id="1ad66-563">在 Main 中创建日志</span><span class="sxs-lookup"><span data-stu-id="1ad66-563">Create logs in Main</span></span>

<span data-ttu-id="1ad66-564">以下代码通过在构建主机之后从 DI 获取 `ILogger` 实例来登录 `Main`：</span><span class="sxs-lookup"><span data-stu-id="1ad66-564">The following code logs in `Main` by getting an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_LogProgram)]

### <a name="create-logs-in-startup"></a><span data-ttu-id="1ad66-565">启动时创建日志</span><span class="sxs-lookup"><span data-stu-id="1ad66-565">Create logs in Startup</span></span>

<span data-ttu-id="1ad66-566">以下代码在 `Startup.Configure` 中写入日志：</span><span class="sxs-lookup"><span data-stu-id="1ad66-566">The following code writes logs in `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Startup.cs?name=snippet_Configure)]

<span data-ttu-id="1ad66-567">不支持在 `Startup.ConfigureServices` 方法中完成 DI 容器设置之前就写入日志：</span><span class="sxs-lookup"><span data-stu-id="1ad66-567">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="1ad66-568">不支持将记录器注入到 `Startup` 构造函数中。</span><span class="sxs-lookup"><span data-stu-id="1ad66-568">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="1ad66-569">不支持将记录器注入到 `Startup.ConfigureServices` 方法签名中</span><span class="sxs-lookup"><span data-stu-id="1ad66-569">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="1ad66-570">这一限制的原因是，日志记录依赖于 DI 和配置，而配置又依赖于 DI。</span><span class="sxs-lookup"><span data-stu-id="1ad66-570">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="1ad66-571">在完成 `ConfigureServices` 之前，不会设置 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="1ad66-571">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="1ad66-572">有关配置依赖于 `ILogger<T>` 的服务或为什么在早期版本中可以使用构造函数将记录器注入 `Startup` 的信息，请参阅[配置依赖 ILogger 的服务](#csdi)</span><span class="sxs-lookup"><span data-stu-id="1ad66-572">For information on configuring a service that depends on `ILogger<T>` or why constructor injection of a logger into `Startup` worked in earlier versions, see [Configure a service that depends on ILogger](#csdi)</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="1ad66-573">没有异步记录器方法</span><span class="sxs-lookup"><span data-stu-id="1ad66-573">No asynchronous logger methods</span></span>

<span data-ttu-id="1ad66-574">日志记录应该会很快，不值得牺牲性能来使用异步代码。</span><span class="sxs-lookup"><span data-stu-id="1ad66-574">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="1ad66-575">如果日志记录数据存储很慢，请不要直接写入它。</span><span class="sxs-lookup"><span data-stu-id="1ad66-575">If a logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="1ad66-576">考虑先将日志消息写入快速存储，然后再将其移至慢速存储。</span><span class="sxs-lookup"><span data-stu-id="1ad66-576">Consider writing the log messages to a fast store initially, then moving them to the slow store later.</span></span> <span data-ttu-id="1ad66-577">例如，登录到 SQL Server 时，请勿直接使用 `Log` 方法登录，因为 `Log` 方法是同步的。</span><span class="sxs-lookup"><span data-stu-id="1ad66-577">For example, when logging to SQL Server, don't do so directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="1ad66-578">相反，你会将日志消息同步添加到内存中的队列，并让后台辅助线程从队列中拉出消息，以完成将数据推送到 SQL Server 的异步工作。</span><span class="sxs-lookup"><span data-stu-id="1ad66-578">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="1ad66-579">有关详细信息，请参阅[此](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub 问题。</span><span class="sxs-lookup"><span data-stu-id="1ad66-579">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

<a name="clib"></a>

## <a name="change-log-levels-in-a-running-app"></a><span data-ttu-id="1ad66-580">更改正在运行的应用中的日志级别</span><span class="sxs-lookup"><span data-stu-id="1ad66-580">Change log levels in a running app</span></span>

<span data-ttu-id="1ad66-581">不可使用日志记录 API 在应用运行时更改日志记录。</span><span class="sxs-lookup"><span data-stu-id="1ad66-581">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="1ad66-582">但是，一些配置提供程序可重新加载配置，这将对日志记录配置立即产生影响。</span><span class="sxs-lookup"><span data-stu-id="1ad66-582">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="1ad66-583">例如，[文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)默认情况下重载日志记录配置。</span><span class="sxs-lookup"><span data-stu-id="1ad66-583">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), reloads logging configuration by default.</span></span> <span data-ttu-id="1ad66-584">如果在应用运行时在代码中更改了配置，则该应用可调用 [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) 来更新应用的日志记录配置。</span><span class="sxs-lookup"><span data-stu-id="1ad66-584">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

## <a name="ilogger-and-iloggerfactory"></a><span data-ttu-id="1ad66-585">ILogger 和 ILoggerFactory</span><span class="sxs-lookup"><span data-stu-id="1ad66-585">ILogger and ILoggerFactory</span></span>

<span data-ttu-id="1ad66-586"><xref:Microsoft.Extensions.Logging.ILogger%601> 和 <xref:Microsoft.Extensions.Logging.ILoggerFactory> 接口和实现都包含在 .NET Core SDK 中。</span><span class="sxs-lookup"><span data-stu-id="1ad66-586">The <xref:Microsoft.Extensions.Logging.ILogger%601> and <xref:Microsoft.Extensions.Logging.ILoggerFactory> interfaces and implementations are included in the .NET Core SDK.</span></span> <span data-ttu-id="1ad66-587">它们还可以通过以下 NuGet 包获得：</span><span class="sxs-lookup"><span data-stu-id="1ad66-587">They are also available in the following NuGet packages:</span></span>  

* <span data-ttu-id="1ad66-588">这些接口位于 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 中。</span><span class="sxs-lookup"><span data-stu-id="1ad66-588">The interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).</span></span>
* <span data-ttu-id="1ad66-589">默认实现位于 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 中。</span><span class="sxs-lookup"><span data-stu-id="1ad66-589">The default implementations are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

<!-- review. Why would you want to hard code filtering rules in code? -->
<a name="fric"></a>

## <a name="apply-log-filter-rules-in-code"></a><span data-ttu-id="1ad66-590">在代码中应用日志筛选器规则</span><span class="sxs-lookup"><span data-stu-id="1ad66-590">Apply log filter rules in code</span></span>

<span data-ttu-id="1ad66-591">设置日志筛选器规则的首选方法是使用[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-591">The preferred approach for setting log filter rules is by using [Configuration](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="1ad66-592">下面的示例演示了如何在代码中注册筛选规则：</span><span class="sxs-lookup"><span data-stu-id="1ad66-592">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterInCode)]

<span data-ttu-id="1ad66-593">`logging.AddFilter("System", LogLevel.Debug)` 指定 `System` 类别和日志级别 `Debug`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-593">`logging.AddFilter("System", LogLevel.Debug)` specifies the `System` category and log level `Debug`.</span></span> <span data-ttu-id="1ad66-594">筛选器将应用于所有提供程序，因为未配置特定的提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-594">The filter is applied to all providers because a specific provider was not configured.</span></span>

<span data-ttu-id="1ad66-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)` 指定以下项：</span><span class="sxs-lookup"><span data-stu-id="1ad66-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)` specifies:</span></span>

* <span data-ttu-id="1ad66-596">`Debug` 日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-596">The `Debug` logging provider.</span></span>
* <span data-ttu-id="1ad66-597">日志级别 `Information` 及更高级别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-597">Log level `Information` and higher.</span></span>
* <span data-ttu-id="1ad66-598">以 `"Microsoft"` 开头的所有类别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-598">All categories starting with `"Microsoft"`.</span></span>

## <a name="create-a-custom-logger"></a><span data-ttu-id="1ad66-599">创建自定义记录器</span><span class="sxs-lookup"><span data-stu-id="1ad66-599">Create a custom logger</span></span>

<span data-ttu-id="1ad66-600">若要添加自定义记录器，请添加包含 <xref:Microsoft.Extensions.Logging.ILoggerFactory> 的 <xref:Microsoft.Extensions.Logging.ILoggerProvider>：</span><span class="sxs-lookup"><span data-stu-id="1ad66-600">To add a custom logger, add an <xref:Microsoft.Extensions.Logging.ILoggerProvider> with <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span></span>

```csharp
public void Configure(
    IApplicationBuilder app,
    IWebHostEnvironment env,
    ILoggerFactory loggerFactory)
{
    loggerFactory.AddProvider(new CustomLoggerProvider(new CustomLoggerConfiguration()));
```

<span data-ttu-id="1ad66-601">`ILoggerProvider` 创建一个或多个 `ILogger` 实例。</span><span class="sxs-lookup"><span data-stu-id="1ad66-601">The `ILoggerProvider` creates one or more `ILogger` instances.</span></span> <span data-ttu-id="1ad66-602">框架使用 `ILogger` 实例记录信息。</span><span class="sxs-lookup"><span data-stu-id="1ad66-602">The `ILogger` instances are used by the framework to log the information.</span></span>

### <a name="sample-custom-logger-configuration"></a><span data-ttu-id="1ad66-603">示例自定义记录器配置</span><span class="sxs-lookup"><span data-stu-id="1ad66-603">Sample custom logger configuration</span></span>

<span data-ttu-id="1ad66-604">示例：</span><span class="sxs-lookup"><span data-stu-id="1ad66-604">The sample:</span></span>

* <span data-ttu-id="1ad66-605">设计为非常基本的示例，可通过事件 ID 和日志级别设置日志控制台的颜色。</span><span class="sxs-lookup"><span data-stu-id="1ad66-605">Is designed to be a very basic sample that sets the color of the log console by event ID and log level.</span></span> <span data-ttu-id="1ad66-606">记录器通常不会随事件 ID 改变，也不特定于日志级别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-606">Loggers generally don't change by event ID and are not specific to log level.</span></span>
* <span data-ttu-id="1ad66-607">使用以下配置类型为每个日志级别和事件 ID 创建不同的颜色控制台条目：</span><span class="sxs-lookup"><span data-stu-id="1ad66-607">Creates different color console entries per log level and event ID using the following configuration type:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerConfiguration.cs?name=snippet)]

<span data-ttu-id="1ad66-608">前面的代码将默认级别设置为 `Warning`，并将颜色设置为 `Yellow`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-608">The preceding code sets the default level to `Warning` and the color to `Yellow`.</span></span> <span data-ttu-id="1ad66-609">如果 `EventId` 设置为 0，我们将记录所有事件。</span><span class="sxs-lookup"><span data-stu-id="1ad66-609">If the `EventId` is set to 0, we will log all events.</span></span>

### <a name="create-the-custom-logger"></a><span data-ttu-id="1ad66-610">创建自定义记录器</span><span class="sxs-lookup"><span data-stu-id="1ad66-610">Create the custom logger</span></span>

<span data-ttu-id="1ad66-611">`ILogger` 实现类别名称通常是日志记录源。</span><span class="sxs-lookup"><span data-stu-id="1ad66-611">The `ILogger` implementation category name is typically the logging source.</span></span> <span data-ttu-id="1ad66-612">例如，创建记录器的类型：</span><span class="sxs-lookup"><span data-stu-id="1ad66-612">For example, the type where the logger is created:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLogger.cs?name=snippet)]

<span data-ttu-id="1ad66-613">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="1ad66-613">The preceding code:</span></span>

* <span data-ttu-id="1ad66-614">为每个类别名称创建一个记录器实例。</span><span class="sxs-lookup"><span data-stu-id="1ad66-614">Creates a logger instance per category name.</span></span>
* <span data-ttu-id="1ad66-615">在 `IsEnabled` 中检查 `logLevel == _config.LogLevel`，因此每个 `logLevel` 都有一个唯一的记录器。</span><span class="sxs-lookup"><span data-stu-id="1ad66-615">Checks `logLevel == _config.LogLevel` in `IsEnabled`, so each `logLevel` has a unique logger.</span></span> <span data-ttu-id="1ad66-616">通常，还应为所有更高的日志级别启用记录器：</span><span class="sxs-lookup"><span data-stu-id="1ad66-616">Generally, loggers should also be enabled for all higher log levels:</span></span>

```csharp
public bool IsEnabled(LogLevel logLevel)
{
    return logLevel >= _config.LogLevel;
}
```

### <a name="create-the-custom-loggerprovider"></a><span data-ttu-id="1ad66-617">创建自定义 LoggerProvider</span><span class="sxs-lookup"><span data-stu-id="1ad66-617">Create the custom LoggerProvider</span></span>

<span data-ttu-id="1ad66-618">`LoggerProvider` 是创建记录器实例的类。</span><span class="sxs-lookup"><span data-stu-id="1ad66-618">The `LoggerProvider` is the class that creates the logger instances.</span></span> <span data-ttu-id="1ad66-619">也许不需要为每个类别创建记录器实例，但这对于某些记录器（例如 NLog 或 log4net）是需要的。</span><span class="sxs-lookup"><span data-stu-id="1ad66-619">Maybe it is not needed to create a logger instance per category, but this makes sense for some Loggers, like NLog or log4net.</span></span> <span data-ttu-id="1ad66-620">这样，你还可以按需为每个类别选择不同的日志记录输出目标：</span><span class="sxs-lookup"><span data-stu-id="1ad66-620">Doing this you are also able to choose different logging output targets per category if needed:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerProvider.cs?name=snippet)]

<span data-ttu-id="1ad66-621">在前面的代码中，<xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> 为每个类别名称创建 `ColorConsoleLogger` 的单个实例并将其存储在 [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2) 中；</span><span class="sxs-lookup"><span data-stu-id="1ad66-621">In the preceding code, <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> creates a single instance of the `ColorConsoleLogger` per category name and stores it in the [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2);</span></span>

### <a name="usage-and-registration-of-the-custom-logger"></a><span data-ttu-id="1ad66-622">自定义记录器的使用和注册</span><span class="sxs-lookup"><span data-stu-id="1ad66-622">Usage and registration of the custom logger</span></span>

<span data-ttu-id="1ad66-623">在 `Startup.Configure` 中注册记录器：</span><span class="sxs-lookup"><span data-stu-id="1ad66-623">Register the logger in the `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/Startup.cs?name=snippet)]

<span data-ttu-id="1ad66-624">对于前面的代码，为 `ILoggerFactory` 提供至少一个扩展方法：</span><span class="sxs-lookup"><span data-stu-id="1ad66-624">For the preceding code, provide at least one extension method for the `ILoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerExtensions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="1ad66-625">其他资源</span><span class="sxs-lookup"><span data-stu-id="1ad66-625">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
* <span data-ttu-id="1ad66-626">记录错误应在 [github.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues) 存储库中创建。</span><span class="sxs-lookup"><span data-stu-id="1ad66-626">Logging bugs should be created in the [github.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues) repo.</span></span>
* <xref:blazor/fundamentals/logging>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1ad66-627">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="1ad66-627">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="1ad66-628">.NET Core 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。</span><span class="sxs-lookup"><span data-stu-id="1ad66-628">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="1ad66-629">本文介绍了如何将日志记录 API 与内置提供程序一起使用。</span><span class="sxs-lookup"><span data-stu-id="1ad66-629">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="1ad66-630">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-630">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="1ad66-631">添加提供程序</span><span class="sxs-lookup"><span data-stu-id="1ad66-631">Add providers</span></span>

<span data-ttu-id="1ad66-632">日志记录提供程序会显示或存储日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-632">A logging provider displays or stores logs.</span></span> <span data-ttu-id="1ad66-633">例如，控制台提供程序在控制台上显示日志，Azure Application Insights 提供程序会将这些日志存储在 Azure Application Insights 中。</span><span class="sxs-lookup"><span data-stu-id="1ad66-633">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="1ad66-634">可通过添加多个提供程序将日志发送到多个目标。</span><span class="sxs-lookup"><span data-stu-id="1ad66-634">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="1ad66-635">要添加提供程序，请在 Program.cs 中调用提供程序的 `Add{provider name}` 扩展方法  ：</span><span class="sxs-lookup"><span data-stu-id="1ad66-635">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="1ad66-636">前面的代码需要引用 `Microsoft.Extensions.Logging` 和 `Microsoft.Extensions.Configuration`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-636">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="1ad66-637">默认项目模板调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>，该操作将添加以下日志记录提供程序：</span><span class="sxs-lookup"><span data-stu-id="1ad66-637">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="1ad66-638">控制台</span><span class="sxs-lookup"><span data-stu-id="1ad66-638">Console</span></span>
* <span data-ttu-id="1ad66-639">调试</span><span class="sxs-lookup"><span data-stu-id="1ad66-639">Debug</span></span>
* <span data-ttu-id="1ad66-640">EventSource（从 ASP.NET Core 2.2 开始）</span><span class="sxs-lookup"><span data-stu-id="1ad66-640">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="1ad66-641">如果使用 `CreateDefaultBuilder`，则可自行选择提供程序来替换默认提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-641">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="1ad66-642">调用 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>，然后添加所需的提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-642">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="1ad66-643">详细了解[内置日志记录提供程序](#built-in-logging-providers)，以及本文稍后部分介绍的[第三方日志记录提供程序](#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-643">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="1ad66-644">创建日志</span><span class="sxs-lookup"><span data-stu-id="1ad66-644">Create logs</span></span>

<span data-ttu-id="1ad66-645">若要创建日志，请使用 <xref:Microsoft.Extensions.Logging.ILogger%601> 对象。</span><span class="sxs-lookup"><span data-stu-id="1ad66-645">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="1ad66-646">在 Web 应用或托管服务中，由依赖关系注入 (DI) 获取 `ILogger`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-646">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="1ad66-647">在非主机控制台应用中，使用 `LoggerFactory` 来创建 `ILogger`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-647">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="1ad66-648">下面的 ASP.NET Core 示例创建了一个以 `TodoApiSample.Pages.AboutModel` 为类别的记录器。</span><span class="sxs-lookup"><span data-stu-id="1ad66-648">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="1ad66-649">日志“类别”是与每个日志关联的字符串  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-649">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="1ad66-650">DI 提供的 `ILogger<T>` 实例创建日志，该日志以类型 `T` 的完全限定名称为类别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-650">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="1ad66-651">在下面的 ASP.NET Core 和控制台应用示例中，记录器用于创建以 `Information` 为级别的日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-651">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="1ad66-652">日志“级别”代表所记录事件的严重程度  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-652">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="1ad66-653">本文稍后部分将更详细地介绍[级别](#log-level)和[类别](#log-category)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-653">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="1ad66-654">启动时创建日志</span><span class="sxs-lookup"><span data-stu-id="1ad66-654">Create logs in Startup</span></span>

<span data-ttu-id="1ad66-655">要将日志写入 `Startup` 类，构造函数签名需包含 `ILogger` 参数：</span><span class="sxs-lookup"><span data-stu-id="1ad66-655">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="1ad66-656">在 Program 类中创建日志</span><span class="sxs-lookup"><span data-stu-id="1ad66-656">Create logs in the Program class</span></span>

<span data-ttu-id="1ad66-657">要将日志写入 `Program` 类，请从 DI 获取 `ILogger` 实例：</span><span class="sxs-lookup"><span data-stu-id="1ad66-657">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="1ad66-658">不直接支持在主机构造期间进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="1ad66-658">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="1ad66-659">但是，可以使用单独的记录器。</span><span class="sxs-lookup"><span data-stu-id="1ad66-659">However, a separate logger can be used.</span></span> <span data-ttu-id="1ad66-660">在以下示例中，[Serilog](https://serilog.net/) 记录器用于登录 `CreateWebHostBuilder`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-660">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="1ad66-661">`AddSerilog` 使用 `Log.Logger` 中指定的静态配置：</span><span class="sxs-lookup"><span data-stu-id="1ad66-661">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="1ad66-662">没有异步记录器方法</span><span class="sxs-lookup"><span data-stu-id="1ad66-662">No asynchronous logger methods</span></span>

<span data-ttu-id="1ad66-663">日志记录应该会很快，不值得牺牲性能来使用异步代码。</span><span class="sxs-lookup"><span data-stu-id="1ad66-663">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="1ad66-664">如果你的日志数据存储很慢，请不要直接写入它。</span><span class="sxs-lookup"><span data-stu-id="1ad66-664">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="1ad66-665">首先考虑将日志消息写入快速存储，稍后再将其变为慢速存储。</span><span class="sxs-lookup"><span data-stu-id="1ad66-665">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="1ad66-666">例如，如果你要记录到 SQL Server，你可能不想直接在 `Log` 方法中记录，因为 `Log` 方法是同步的。</span><span class="sxs-lookup"><span data-stu-id="1ad66-666">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="1ad66-667">相反，你会将日志消息同步添加到内存中的队列，并让后台辅助线程从队列中拉出消息，以完成将数据推送到 SQL Server 的异步工作。</span><span class="sxs-lookup"><span data-stu-id="1ad66-667">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="1ad66-668">有关详细信息，请参阅[此](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub 问题。</span><span class="sxs-lookup"><span data-stu-id="1ad66-668">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="1ad66-669">Configuration</span><span class="sxs-lookup"><span data-stu-id="1ad66-669">Configuration</span></span>

<span data-ttu-id="1ad66-670">日志记录提供程序配置由一个或多个配置提供程序提供：</span><span class="sxs-lookup"><span data-stu-id="1ad66-670">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="1ad66-671">文件格式（INI、JSON 和 XML）。</span><span class="sxs-lookup"><span data-stu-id="1ad66-671">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="1ad66-672">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="1ad66-672">Command-line arguments.</span></span>
* <span data-ttu-id="1ad66-673">环境变量。</span><span class="sxs-lookup"><span data-stu-id="1ad66-673">Environment variables.</span></span>
* <span data-ttu-id="1ad66-674">内存中的 .NET 对象。</span><span class="sxs-lookup"><span data-stu-id="1ad66-674">In-memory .NET objects.</span></span>
* <span data-ttu-id="1ad66-675">未加密的[机密管理器](xref:security/app-secrets)存储。</span><span class="sxs-lookup"><span data-stu-id="1ad66-675">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="1ad66-676">加密的用户存储，如 [Azure Key Vault](xref:security/key-vault-configuration)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-676">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="1ad66-677">（已安装或已创建的）自定义提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-677">Custom providers (installed or created).</span></span>

<span data-ttu-id="1ad66-678">例如，日志记录配置通常由应用设置文件的 `Logging` 部分提供。</span><span class="sxs-lookup"><span data-stu-id="1ad66-678">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="1ad66-679">以下示例显示了典型 *appsettings.Development.json* 文件的内容：</span><span class="sxs-lookup"><span data-stu-id="1ad66-679">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="1ad66-680">`Logging` 属性可具有 `LogLevel` 和日志提供程序属性（显示控制台）。</span><span class="sxs-lookup"><span data-stu-id="1ad66-680">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="1ad66-681">`Logging` 下的 `LogLevel` 属性指定了用于记录所选类别的最低[级别](#log-level)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-681">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="1ad66-682">在本例中，`System` 和 `Microsoft` 类别在 `Information` 级别记录，其他均在 `Debug` 级别记录。</span><span class="sxs-lookup"><span data-stu-id="1ad66-682">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="1ad66-683">`Logging` 下的其他属性均指定了日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-683">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="1ad66-684">本示例针对控制台提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-684">The example is for the Console provider.</span></span> <span data-ttu-id="1ad66-685">如果提供程序支持[日志作用域](#log-scopes)，则 `IncludeScopes` 将指示是否启用这些域。</span><span class="sxs-lookup"><span data-stu-id="1ad66-685">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="1ad66-686">提供程序属性（例如本例的 `Console`）也可指定 `LogLevel` 属性。</span><span class="sxs-lookup"><span data-stu-id="1ad66-686">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="1ad66-687">提供程序下的 `LogLevel` 指定了该提供程序记录的级别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-687">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="1ad66-688">如果在 `Logging.{providername}.LogLevel` 中指定了级别，则这些级别将重写 `Logging.LogLevel` 中设置的所有内容。</span><span class="sxs-lookup"><span data-stu-id="1ad66-688">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span> <span data-ttu-id="1ad66-689">例如，考虑以下 JSON：</span><span class="sxs-lookup"><span data-stu-id="1ad66-689">For example, consider the following JSON:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<span data-ttu-id="1ad66-690">在前面的 JSON 中，`Console` 提供程序设置将替代前面的（默认）日志级别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-690">In the preceding JSON, the `Console` provider settings overrides the preceding (default) log level.</span></span>

<span data-ttu-id="1ad66-691">不可使用日志记录 API 在应用运行时更改日志记录。</span><span class="sxs-lookup"><span data-stu-id="1ad66-691">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="1ad66-692">但是，一些配置提供程序可重新加载配置，这将对日志记录配置立即产生影响。</span><span class="sxs-lookup"><span data-stu-id="1ad66-692">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="1ad66-693">例如，[文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)会默认重新加载日志记录配置，该程序由 `CreateDefaultBuilder` 添加用来读取设置文件。</span><span class="sxs-lookup"><span data-stu-id="1ad66-693">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="1ad66-694">如果在应用运行时在代码中更改了配置，则该应用可调用 [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) 来更新应用的日志记录配置。</span><span class="sxs-lookup"><span data-stu-id="1ad66-694">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="1ad66-695">若要了解如何实现配置提供程序，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="1ad66-695">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="1ad66-696">日志记录输出示例</span><span class="sxs-lookup"><span data-stu-id="1ad66-696">Sample logging output</span></span>

<span data-ttu-id="1ad66-697">使用上一部分中显示的示例代码从命令行运行应用时，将在控制台中看到日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-697">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="1ad66-698">以下是控制台输出示例：</span><span class="sxs-lookup"><span data-stu-id="1ad66-698">Here's an example of console output:</span></span>

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

<span data-ttu-id="1ad66-699">通过向 `http://localhost:5000/api/todo/0` 处的示例应用发出 HTTP Get 请求来生成前述日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-699">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="1ad66-700">在 Visual Studio 中运行示例应用时，“调试”窗口中将显示如下日志：</span><span class="sxs-lookup"><span data-stu-id="1ad66-700">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="1ad66-701">由上一部分所示的 `ILogger` 调用创建的日志以“TodoApi”开头。</span><span class="sxs-lookup"><span data-stu-id="1ad66-701">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="1ad66-702">以“Microsoft”类别开头的日志来自 ASP.NET Core 框架代码。</span><span class="sxs-lookup"><span data-stu-id="1ad66-702">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="1ad66-703">ASP.NET Core 和应用程序代码使用相同的日志记录 API 和提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-703">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="1ad66-704">本文余下部分将介绍有关日志记录的某些详细信息及选项。</span><span class="sxs-lookup"><span data-stu-id="1ad66-704">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="1ad66-705">NuGet 包</span><span class="sxs-lookup"><span data-stu-id="1ad66-705">NuGet packages</span></span>

<span data-ttu-id="1ad66-706">`ILogger` 和 `ILoggerFactory` 接口位于 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 中，其默认实现位于 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 中。</span><span class="sxs-lookup"><span data-stu-id="1ad66-706">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="1ad66-707">日志类别</span><span class="sxs-lookup"><span data-stu-id="1ad66-707">Log category</span></span>

<span data-ttu-id="1ad66-708">创建 `ILogger` 对象后，将为其指定“类别”  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-708">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="1ad66-709">该类别包含在由此 `ILogger` 实例创建的每条日志消息中。</span><span class="sxs-lookup"><span data-stu-id="1ad66-709">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="1ad66-710">类别可以是任何字符串，但约定需使用类名，例如“TodoApi.Controllers.TodoController”。</span><span class="sxs-lookup"><span data-stu-id="1ad66-710">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="1ad66-711">使用 `ILogger<T>` 获取一个 `ILogger` 实例，该实例使用 `T` 的完全限定类型名称作为类别：</span><span class="sxs-lookup"><span data-stu-id="1ad66-711">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="1ad66-712">要显式指定类别，请调用 `ILoggerFactory.CreateLogger`：</span><span class="sxs-lookup"><span data-stu-id="1ad66-712">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="1ad66-713">`ILogger<T>` 相当于使用 `T` 的完全限定类型名称来调用 `CreateLogger`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-713">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="1ad66-714">日志级别</span><span class="sxs-lookup"><span data-stu-id="1ad66-714">Log level</span></span>

<span data-ttu-id="1ad66-715">每个日志都指定了一个 <xref:Microsoft.Extensions.Logging.LogLevel> 值。</span><span class="sxs-lookup"><span data-stu-id="1ad66-715">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="1ad66-716">日志级别指示严重性或重要程度。</span><span class="sxs-lookup"><span data-stu-id="1ad66-716">The log level indicates the severity or importance.</span></span> <span data-ttu-id="1ad66-717">例如，可在方法正常结束时写入 `Information` 日志，在方法返回“404 找不到”状态代码时写入 `Warning` 日志  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-717">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="1ad66-718">下面的代码会创建 `Information` 和 `Warning` 日志：</span><span class="sxs-lookup"><span data-stu-id="1ad66-718">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="1ad66-719">在前面的代码中，`MyLogEvents.GetItem` 和 `MyLogEvents.GetItemNotFound` 参数是[日志事件 ID](#log-event-id)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-719">In the preceding code, the `MyLogEvents.GetItem` and `MyLogEvents.GetItemNotFound` parameters are the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="1ad66-720">第二个参数是消息模板，其中的占位符用于填写剩余方法形参提供的实参值。</span><span class="sxs-lookup"><span data-stu-id="1ad66-720">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="1ad66-721">本文的[日志消息模板部分](#lmt)将介绍方法参数。</span><span class="sxs-lookup"><span data-stu-id="1ad66-721">The method parameters are explained in the [Log message template section](#lmt) in this article.</span></span>

<span data-ttu-id="1ad66-722">在方法名称中包含级别的日志方法（例如 `LogInformation` 和 `LogWarning`）是 [ILogger 的扩展方法](xref:Microsoft.Extensions.Logging.LoggerExtensions)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-722">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="1ad66-723">这些方法会调用可接受 `LogLevel` 参数的 `Log` 方法。</span><span class="sxs-lookup"><span data-stu-id="1ad66-723">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="1ad66-724">可直接调用 `Log` 方法而不调用其中某个扩展方法，但语法相对复杂。</span><span class="sxs-lookup"><span data-stu-id="1ad66-724">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="1ad66-725">有关详细信息，请参阅 <xref:Microsoft.Extensions.Logging.ILogger> 和[记录器扩展源代码](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-725">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="1ad66-726">ASP.NET Core 定义了以下日志级别（按严重性从低到高排列）。</span><span class="sxs-lookup"><span data-stu-id="1ad66-726">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="1ad66-727">跟踪 = 0</span><span class="sxs-lookup"><span data-stu-id="1ad66-727">Trace = 0</span></span>

  <span data-ttu-id="1ad66-728">有关通常仅用于调试的信息。</span><span class="sxs-lookup"><span data-stu-id="1ad66-728">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="1ad66-729">这些消息可能包含敏感应用程序数据，因此不得在生产环境中启用它们。</span><span class="sxs-lookup"><span data-stu-id="1ad66-729">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="1ad66-730">默认情况下禁用。</span><span class="sxs-lookup"><span data-stu-id="1ad66-730">*Disabled by default.*</span></span>

* <span data-ttu-id="1ad66-731">调试 = 1</span><span class="sxs-lookup"><span data-stu-id="1ad66-731">Debug = 1</span></span>

  <span data-ttu-id="1ad66-732">有关在开发和调试中可能有用的信息。</span><span class="sxs-lookup"><span data-stu-id="1ad66-732">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="1ad66-733">示例：`Entering method Configure with flag set to true.` 由于日志数量过多，因此仅当执行故障排除时，才在生产中启用 `Debug` 级别日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-733">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="1ad66-734">信息 = 2</span><span class="sxs-lookup"><span data-stu-id="1ad66-734">Information = 2</span></span>

  <span data-ttu-id="1ad66-735">用于跟踪应用的常规流。</span><span class="sxs-lookup"><span data-stu-id="1ad66-735">For tracking the general flow of the app.</span></span> <span data-ttu-id="1ad66-736">这些日志通常有长期价值。</span><span class="sxs-lookup"><span data-stu-id="1ad66-736">These logs typically have some long-term value.</span></span> <span data-ttu-id="1ad66-737">示例：`Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="1ad66-737">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="1ad66-738">警告 = 3</span><span class="sxs-lookup"><span data-stu-id="1ad66-738">Warning = 3</span></span>

  <span data-ttu-id="1ad66-739">表示应用流中的异常或意外事件。</span><span class="sxs-lookup"><span data-stu-id="1ad66-739">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="1ad66-740">可能包括不会中断应用运行但仍需调查的错误或其他条件。</span><span class="sxs-lookup"><span data-stu-id="1ad66-740">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="1ad66-741">`Warning` 日志级别常用于已处理的异常。</span><span class="sxs-lookup"><span data-stu-id="1ad66-741">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="1ad66-742">示例：`FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="1ad66-742">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="1ad66-743">错误 = 4</span><span class="sxs-lookup"><span data-stu-id="1ad66-743">Error = 4</span></span>

  <span data-ttu-id="1ad66-744">表示无法处理的错误和异常。</span><span class="sxs-lookup"><span data-stu-id="1ad66-744">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="1ad66-745">这些消息指示的是当前活动或操作（例如当前 HTTP 请求）中的失败，而不是整个应用中的失败。</span><span class="sxs-lookup"><span data-stu-id="1ad66-745">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="1ad66-746">日志消息示例：`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="1ad66-746">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="1ad66-747">严重 = 5</span><span class="sxs-lookup"><span data-stu-id="1ad66-747">Critical = 5</span></span>

  <span data-ttu-id="1ad66-748">需要立即关注的失败。</span><span class="sxs-lookup"><span data-stu-id="1ad66-748">For failures that require immediate attention.</span></span> <span data-ttu-id="1ad66-749">例如数据丢失、磁盘空间不足。</span><span class="sxs-lookup"><span data-stu-id="1ad66-749">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="1ad66-750">使用日志级别控制写入到特定存储介质或显示窗口的日志输出量。</span><span class="sxs-lookup"><span data-stu-id="1ad66-750">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="1ad66-751">例如：</span><span class="sxs-lookup"><span data-stu-id="1ad66-751">For example:</span></span>

* <span data-ttu-id="1ad66-752">生产中：</span><span class="sxs-lookup"><span data-stu-id="1ad66-752">In production:</span></span>
  * <span data-ttu-id="1ad66-753">如果通过 `Information` 级别在 `Trace` 处记录，则会生成大量详细的日志消息。</span><span class="sxs-lookup"><span data-stu-id="1ad66-753">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="1ad66-754">为控制成本且不超过数据存储限制，请通过 `Information` 级别消息将 `Trace` 记录到容量大、成本低的数据存储中。</span><span class="sxs-lookup"><span data-stu-id="1ad66-754">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="1ad66-755">如果通过 `Critical` 级别在 `Warning` 处记录，通常生成的日志消息更少且更小。</span><span class="sxs-lookup"><span data-stu-id="1ad66-755">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="1ad66-756">因此，成本和存储限制通常不是问题，而这使得在选择数据信息时更为灵活。</span><span class="sxs-lookup"><span data-stu-id="1ad66-756">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="1ad66-757">在开发过程中：</span><span class="sxs-lookup"><span data-stu-id="1ad66-757">During development:</span></span>
  * <span data-ttu-id="1ad66-758">通过 `Critical` 消息将 `Warning` 记录到控制台。</span><span class="sxs-lookup"><span data-stu-id="1ad66-758">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="1ad66-759">在疑难解答时通过 `Information` 消息添加 `Trace`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-759">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="1ad66-760">本文稍后的[日志筛选](#log-filtering)部分介绍如何控制提供程序处理的日志级别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-760">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="1ad66-761">ASP.NET Core 为框架事件写入日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-761">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="1ad66-762">本文前面部分提供的日志示例排除了低于 `Information` 级别的日志，因此未创建 `Debug` 或 `Trace` 级别日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-762">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="1ad66-763">以下示例介绍了通过运行配置为显示 `Debug` 日志的示例应用而生成的控制台日志：</span><span class="sxs-lookup"><span data-stu-id="1ad66-763">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="1ad66-764">日志事件 ID</span><span class="sxs-lookup"><span data-stu-id="1ad66-764">Log event ID</span></span>

<span data-ttu-id="1ad66-765">每个日志都可指定一个事件 ID  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-765">Each log can specify an *event ID*.</span></span> <span data-ttu-id="1ad66-766">该示例应用通过使用本地定义的 `LoggingEvents` 类来执行此操作：</span><span class="sxs-lookup"><span data-stu-id="1ad66-766">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="1ad66-767">事件 ID 与一组事件相关联。</span><span class="sxs-lookup"><span data-stu-id="1ad66-767">An event ID associates a set of events.</span></span> <span data-ttu-id="1ad66-768">例如，与在页面上显示项列表相关的所有日志可能是 1001。</span><span class="sxs-lookup"><span data-stu-id="1ad66-768">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="1ad66-769">日志记录提供程序可将事件 ID 存储在 ID 字段中，存储在日志记录消息中，或者不进行存储。</span><span class="sxs-lookup"><span data-stu-id="1ad66-769">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="1ad66-770">调试提供程序不显示事件 ID。</span><span class="sxs-lookup"><span data-stu-id="1ad66-770">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="1ad66-771">控制台提供程序在类别后的括号中显示事件 ID：</span><span class="sxs-lookup"><span data-stu-id="1ad66-771">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="1ad66-772">日志消息模板</span><span class="sxs-lookup"><span data-stu-id="1ad66-772">Log message template</span></span>

<span data-ttu-id="1ad66-773">每个日志都会指定一个消息模板。</span><span class="sxs-lookup"><span data-stu-id="1ad66-773">Each log specifies a message template.</span></span> <span data-ttu-id="1ad66-774">消息模板可包含要填写参数的占位符。</span><span class="sxs-lookup"><span data-stu-id="1ad66-774">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="1ad66-775">请在占位符中使用名称而不是数字。</span><span class="sxs-lookup"><span data-stu-id="1ad66-775">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="1ad66-776">占位符的顺序（而非其名称）决定了为其提供值的参数。</span><span class="sxs-lookup"><span data-stu-id="1ad66-776">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="1ad66-777">在以下代码中，请注意消息模板中的参数名称未按顺序排列：</span><span class="sxs-lookup"><span data-stu-id="1ad66-777">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="1ad66-778">此代码创建了一个参数值按顺序排列的日志消息：</span><span class="sxs-lookup"><span data-stu-id="1ad66-778">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="1ad66-779">日志记录框架按此方式工作，这样日志记录提供程序即可实现[语义日志记录，也称为结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-779">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="1ad66-780">参数本身会传递给日志记录系统，而不仅仅是格式化的消息模板。</span><span class="sxs-lookup"><span data-stu-id="1ad66-780">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="1ad66-781">通过此信息，日志记录提供程序能够将参数值存储为字段。</span><span class="sxs-lookup"><span data-stu-id="1ad66-781">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="1ad66-782">例如，假设记录器方法调用如下所示：</span><span class="sxs-lookup"><span data-stu-id="1ad66-782">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="1ad66-783">如果要将日志发送到 Azure 表存储，则每个 Azure 表实体都可具有 `ID` 和 `RequestTime` 属性，这简化了对日志数据的查询。</span><span class="sxs-lookup"><span data-stu-id="1ad66-783">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="1ad66-784">无需分析文本消息的超时，查询即可找到特定 `RequestTime` 范围内的全部日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-784">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="1ad66-785">日志记录异常</span><span class="sxs-lookup"><span data-stu-id="1ad66-785">Logging exceptions</span></span>

<span data-ttu-id="1ad66-786">记录器方法有可传入异常的重载，如下方示例所示：</span><span class="sxs-lookup"><span data-stu-id="1ad66-786">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="1ad66-787">不同的提供程序处理异常信息的方式不同。</span><span class="sxs-lookup"><span data-stu-id="1ad66-787">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="1ad66-788">以下是上示代码的调试提供程序输出示例。</span><span class="sxs-lookup"><span data-stu-id="1ad66-788">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="1ad66-789">日志筛选</span><span class="sxs-lookup"><span data-stu-id="1ad66-789">Log filtering</span></span>

<span data-ttu-id="1ad66-790">可为特定或所有提供程序和类别指定最低日志级别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-790">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="1ad66-791">最低级别以下的日志不会传递给该提供程序，因此不会显示或存储它们。</span><span class="sxs-lookup"><span data-stu-id="1ad66-791">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="1ad66-792">要禁止显示所有日志，可将 `LogLevel.None` 指定为最低日志级别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-792">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="1ad66-793">`LogLevel.None` 的整数值为 6，它大于 `LogLevel.Critical` (5)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-793">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="1ad66-794">在配置中创建筛选规则</span><span class="sxs-lookup"><span data-stu-id="1ad66-794">Create filter rules in configuration</span></span>

<span data-ttu-id="1ad66-795">项目模板代码调用 `CreateDefaultBuilder` 来为 Console、Debug 和 EventSource（ASP.NET Core 2.2 或更高版本）提供程序设置日志记录。</span><span class="sxs-lookup"><span data-stu-id="1ad66-795">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="1ad66-796">正如[本文前面部分](#configuration)所述，`CreateDefaultBuilder` 方法设置日志记录以在 `Logging` 部分查找配置。</span><span class="sxs-lookup"><span data-stu-id="1ad66-796">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="1ad66-797">配置数据按提供程序和类别指定最低日志级别，如下方示例所示：</span><span class="sxs-lookup"><span data-stu-id="1ad66-797">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="1ad66-798">此 JSON 将创建 6 条筛选规则：1 条用于调试提供程序， 4 条用于控制台提供程序， 1 条用于所有提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-798">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="1ad66-799">创建 `ILogger` 对象时，为每个提供程序选择一个规则。</span><span class="sxs-lookup"><span data-stu-id="1ad66-799">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="1ad66-800">代码中的筛选规则</span><span class="sxs-lookup"><span data-stu-id="1ad66-800">Filter rules in code</span></span>

<span data-ttu-id="1ad66-801">下面的示例演示了如何在代码中注册筛选规则：</span><span class="sxs-lookup"><span data-stu-id="1ad66-801">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="1ad66-802">第二个 `AddFilter` 使用类型名称来指定调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-802">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="1ad66-803">第一个 `AddFilter` 应用于全部提供程序，因为它未指定提供程序类型。</span><span class="sxs-lookup"><span data-stu-id="1ad66-803">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="1ad66-804">如何应用筛选规则</span><span class="sxs-lookup"><span data-stu-id="1ad66-804">How filtering rules are applied</span></span>

<span data-ttu-id="1ad66-805">先前示例中显示的配置数据和 `AddFilter` 代码会创建下表所示的规则。</span><span class="sxs-lookup"><span data-stu-id="1ad66-805">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="1ad66-806">前六条由配置示例创建，后两条由代码示例创建。</span><span class="sxs-lookup"><span data-stu-id="1ad66-806">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="1ad66-807">数字</span><span class="sxs-lookup"><span data-stu-id="1ad66-807">Number</span></span> | <span data-ttu-id="1ad66-808">提供程序</span><span class="sxs-lookup"><span data-stu-id="1ad66-808">Provider</span></span>      | <span data-ttu-id="1ad66-809">类别的开头为...</span><span class="sxs-lookup"><span data-stu-id="1ad66-809">Categories that begin with ...</span></span>          | <span data-ttu-id="1ad66-810">最低日志级别</span><span class="sxs-lookup"><span data-stu-id="1ad66-810">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="1ad66-811">1</span><span class="sxs-lookup"><span data-stu-id="1ad66-811">1</span></span>      | <span data-ttu-id="1ad66-812">调试</span><span class="sxs-lookup"><span data-stu-id="1ad66-812">Debug</span></span>         | <span data-ttu-id="1ad66-813">全部类别</span><span class="sxs-lookup"><span data-stu-id="1ad66-813">All categories</span></span>                          | <span data-ttu-id="1ad66-814">信息</span><span class="sxs-lookup"><span data-stu-id="1ad66-814">Information</span></span>       |
| <span data-ttu-id="1ad66-815">2</span><span class="sxs-lookup"><span data-stu-id="1ad66-815">2</span></span>      | <span data-ttu-id="1ad66-816">控制台</span><span class="sxs-lookup"><span data-stu-id="1ad66-816">Console</span></span>       | <span data-ttu-id="1ad66-817">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="1ad66-817">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="1ad66-818">警告</span><span class="sxs-lookup"><span data-stu-id="1ad66-818">Warning</span></span>           |
| <span data-ttu-id="1ad66-819">3</span><span class="sxs-lookup"><span data-stu-id="1ad66-819">3</span></span>      | <span data-ttu-id="1ad66-820">控制台</span><span class="sxs-lookup"><span data-stu-id="1ad66-820">Console</span></span>       | <span data-ttu-id="1ad66-821">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="1ad66-821">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="1ad66-822">调试</span><span class="sxs-lookup"><span data-stu-id="1ad66-822">Debug</span></span>             |
| <span data-ttu-id="1ad66-823">4</span><span class="sxs-lookup"><span data-stu-id="1ad66-823">4</span></span>      | <span data-ttu-id="1ad66-824">控制台</span><span class="sxs-lookup"><span data-stu-id="1ad66-824">Console</span></span>       | <span data-ttu-id="1ad66-825">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="1ad66-825">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="1ad66-826">错误</span><span class="sxs-lookup"><span data-stu-id="1ad66-826">Error</span></span>             |
| <span data-ttu-id="1ad66-827">5</span><span class="sxs-lookup"><span data-stu-id="1ad66-827">5</span></span>      | <span data-ttu-id="1ad66-828">控制台</span><span class="sxs-lookup"><span data-stu-id="1ad66-828">Console</span></span>       | <span data-ttu-id="1ad66-829">全部类别</span><span class="sxs-lookup"><span data-stu-id="1ad66-829">All categories</span></span>                          | <span data-ttu-id="1ad66-830">信息</span><span class="sxs-lookup"><span data-stu-id="1ad66-830">Information</span></span>       |
| <span data-ttu-id="1ad66-831">6</span><span class="sxs-lookup"><span data-stu-id="1ad66-831">6</span></span>      | <span data-ttu-id="1ad66-832">全部提供程序</span><span class="sxs-lookup"><span data-stu-id="1ad66-832">All providers</span></span> | <span data-ttu-id="1ad66-833">全部类别</span><span class="sxs-lookup"><span data-stu-id="1ad66-833">All categories</span></span>                          | <span data-ttu-id="1ad66-834">调试</span><span class="sxs-lookup"><span data-stu-id="1ad66-834">Debug</span></span>             |
| <span data-ttu-id="1ad66-835">7</span><span class="sxs-lookup"><span data-stu-id="1ad66-835">7</span></span>      | <span data-ttu-id="1ad66-836">全部提供程序</span><span class="sxs-lookup"><span data-stu-id="1ad66-836">All providers</span></span> | <span data-ttu-id="1ad66-837">System</span><span class="sxs-lookup"><span data-stu-id="1ad66-837">System</span></span>                                  | <span data-ttu-id="1ad66-838">调试</span><span class="sxs-lookup"><span data-stu-id="1ad66-838">Debug</span></span>             |
| <span data-ttu-id="1ad66-839">8</span><span class="sxs-lookup"><span data-stu-id="1ad66-839">8</span></span>      | <span data-ttu-id="1ad66-840">调试</span><span class="sxs-lookup"><span data-stu-id="1ad66-840">Debug</span></span>         | <span data-ttu-id="1ad66-841">Microsoft</span><span class="sxs-lookup"><span data-stu-id="1ad66-841">Microsoft</span></span>                               | <span data-ttu-id="1ad66-842">跟踪</span><span class="sxs-lookup"><span data-stu-id="1ad66-842">Trace</span></span>             |

<span data-ttu-id="1ad66-843">创建 `ILogger` 对象时，`ILoggerFactory` 对象将根据提供程序选择一条规则，将其应用于该记录器。</span><span class="sxs-lookup"><span data-stu-id="1ad66-843">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="1ad66-844">将按所选规则筛选 `ILogger` 实例写入的所有消息。</span><span class="sxs-lookup"><span data-stu-id="1ad66-844">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="1ad66-845">从可用规则中为每个提供程序和类别对选择最具体的规则。</span><span class="sxs-lookup"><span data-stu-id="1ad66-845">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="1ad66-846">在为给定的类别创建 `ILogger` 时，以下算法将用于每个提供程序：</span><span class="sxs-lookup"><span data-stu-id="1ad66-846">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="1ad66-847">选择匹配提供程序或其别名的所有规则。</span><span class="sxs-lookup"><span data-stu-id="1ad66-847">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="1ad66-848">如果找不到任何匹配项，则选择提供程序为空的所有规则。</span><span class="sxs-lookup"><span data-stu-id="1ad66-848">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="1ad66-849">根据上一步的结果，选择具有最长匹配类别前缀的规则。</span><span class="sxs-lookup"><span data-stu-id="1ad66-849">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="1ad66-850">如果找不到任何匹配项，则选择未指定类别的所有规则。</span><span class="sxs-lookup"><span data-stu-id="1ad66-850">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="1ad66-851">如果选择了多条规则，则采用最后一条  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-851">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="1ad66-852">如果未选择任何规则，则使用 `MinimumLevel`。</span><span class="sxs-lookup"><span data-stu-id="1ad66-852">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="1ad66-853">使用前面的规则列表，假设你为类别“Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine”创建 `ILogger` 对象：</span><span class="sxs-lookup"><span data-stu-id="1ad66-853">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="1ad66-854">对于调试提供程序，规则 1、6 和 8 适用。</span><span class="sxs-lookup"><span data-stu-id="1ad66-854">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="1ad66-855">规则 8 最为具体，因此选择了它。</span><span class="sxs-lookup"><span data-stu-id="1ad66-855">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="1ad66-856">对于控制台提供程序，符合的有规则 3、规则 4、规则 5 和规则 6。</span><span class="sxs-lookup"><span data-stu-id="1ad66-856">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="1ad66-857">规则 3 最为具体。</span><span class="sxs-lookup"><span data-stu-id="1ad66-857">Rule 3 is most specific.</span></span>

<span data-ttu-id="1ad66-858">生成的 `ILogger` 实例将 `Trace` 级别及更高级别的日志发送到调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-858">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="1ad66-859">`Debug` 级别及更高级别的日志会发送到控制台提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-859">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="1ad66-860">提供程序别名</span><span class="sxs-lookup"><span data-stu-id="1ad66-860">Provider aliases</span></span>

<span data-ttu-id="1ad66-861">每个提供程序都定义了一个别名；可在配置中使用该别名来代替完全限定的类型名称  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-861">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="1ad66-862">对于内置提供程序，请使用以下别名：</span><span class="sxs-lookup"><span data-stu-id="1ad66-862">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="1ad66-863">控制台</span><span class="sxs-lookup"><span data-stu-id="1ad66-863">Console</span></span>
* <span data-ttu-id="1ad66-864">调试</span><span class="sxs-lookup"><span data-stu-id="1ad66-864">Debug</span></span>
* <span data-ttu-id="1ad66-865">EventSource</span><span class="sxs-lookup"><span data-stu-id="1ad66-865">EventSource</span></span>
* <span data-ttu-id="1ad66-866">EventLog</span><span class="sxs-lookup"><span data-stu-id="1ad66-866">EventLog</span></span>
* <span data-ttu-id="1ad66-867">TraceSource</span><span class="sxs-lookup"><span data-stu-id="1ad66-867">TraceSource</span></span>
* <span data-ttu-id="1ad66-868">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="1ad66-868">AzureAppServicesFile</span></span>
* <span data-ttu-id="1ad66-869">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="1ad66-869">AzureAppServicesBlob</span></span>
* <span data-ttu-id="1ad66-870">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="1ad66-870">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="1ad66-871">默认最低级别</span><span class="sxs-lookup"><span data-stu-id="1ad66-871">Default minimum level</span></span>

<span data-ttu-id="1ad66-872">仅当配置或代码中的规则对给定提供程序和类别都不适用时，最低级别设置才会生效。</span><span class="sxs-lookup"><span data-stu-id="1ad66-872">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="1ad66-873">下面的示例演示如何设置最低级别：</span><span class="sxs-lookup"><span data-stu-id="1ad66-873">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="1ad66-874">如果没有明确设置最低级别，则默认值为 `Information`，它表示 `Trace` 和 `Debug` 日志将被忽略。</span><span class="sxs-lookup"><span data-stu-id="1ad66-874">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="1ad66-875">筛选器函数</span><span class="sxs-lookup"><span data-stu-id="1ad66-875">Filter functions</span></span>

<span data-ttu-id="1ad66-876">对配置或代码没有向其分配规则的所有提供程序和类别调用筛选器函数。</span><span class="sxs-lookup"><span data-stu-id="1ad66-876">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="1ad66-877">函数中的代码可访问提供程序类型、类别和日志级别。</span><span class="sxs-lookup"><span data-stu-id="1ad66-877">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="1ad66-878">例如：</span><span class="sxs-lookup"><span data-stu-id="1ad66-878">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="1ad66-879">系统类别和级别</span><span class="sxs-lookup"><span data-stu-id="1ad66-879">System categories and levels</span></span>

<span data-ttu-id="1ad66-880">下面是 ASP.NET Core 和 Entity Framework Core 使用的一些类别，备注中说明了可从这些类别获取的具体日志：</span><span class="sxs-lookup"><span data-stu-id="1ad66-880">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="1ad66-881">类别</span><span class="sxs-lookup"><span data-stu-id="1ad66-881">Category</span></span>                            | <span data-ttu-id="1ad66-882">说明</span><span class="sxs-lookup"><span data-stu-id="1ad66-882">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="1ad66-883">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="1ad66-883">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="1ad66-884">常规 ASP.NET Core 诊断。</span><span class="sxs-lookup"><span data-stu-id="1ad66-884">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="1ad66-885">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="1ad66-885">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="1ad66-886">考虑、找到并使用了哪些密钥。</span><span class="sxs-lookup"><span data-stu-id="1ad66-886">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="1ad66-887">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="1ad66-887">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="1ad66-888">所允许的主机。</span><span class="sxs-lookup"><span data-stu-id="1ad66-888">Hosts allowed.</span></span> |
| <span data-ttu-id="1ad66-889">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="1ad66-889">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="1ad66-890">HTTP 请求完成的时间和启动时间。</span><span class="sxs-lookup"><span data-stu-id="1ad66-890">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="1ad66-891">加载了哪些承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="1ad66-891">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="1ad66-892">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="1ad66-892">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="1ad66-893">MVC 和 Razor 诊断。</span><span class="sxs-lookup"><span data-stu-id="1ad66-893">MVC and Razor diagnostics.</span></span> <span data-ttu-id="1ad66-894">模型绑定、筛选器执行、视图编译和操作选择。</span><span class="sxs-lookup"><span data-stu-id="1ad66-894">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="1ad66-895">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="1ad66-895">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="1ad66-896">路由匹配信息。</span><span class="sxs-lookup"><span data-stu-id="1ad66-896">Route matching information.</span></span> |
| <span data-ttu-id="1ad66-897">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="1ad66-897">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="1ad66-898">连接启动、停止和保持活动响应。</span><span class="sxs-lookup"><span data-stu-id="1ad66-898">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="1ad66-899">HTTP 证书信息。</span><span class="sxs-lookup"><span data-stu-id="1ad66-899">HTTPS certificate information.</span></span> |
| <span data-ttu-id="1ad66-900">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="1ad66-900">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="1ad66-901">提供的文件。</span><span class="sxs-lookup"><span data-stu-id="1ad66-901">Files served.</span></span> |
| <span data-ttu-id="1ad66-902">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="1ad66-902">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="1ad66-903">常规 Entity Framework Core 诊断。</span><span class="sxs-lookup"><span data-stu-id="1ad66-903">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="1ad66-904">数据库活动和配置、更改检测、迁移。</span><span class="sxs-lookup"><span data-stu-id="1ad66-904">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="1ad66-905">日志作用域</span><span class="sxs-lookup"><span data-stu-id="1ad66-905">Log scopes</span></span>

 <span data-ttu-id="1ad66-906">“作用域”可对一组逻辑操作分组  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-906">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="1ad66-907">此分组可用于将相同的数据附加到作为集合的一部分而创建的每个日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-907">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="1ad66-908">例如，在处理事务期间创建的每个日志都可包括事务 ID。</span><span class="sxs-lookup"><span data-stu-id="1ad66-908">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="1ad66-909">范围是由 <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> 方法返回的 `IDisposable` 类型，持续至释放为止。</span><span class="sxs-lookup"><span data-stu-id="1ad66-909">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="1ad66-910">要使用作用域，请在 `using` 块中包装记录器调用：</span><span class="sxs-lookup"><span data-stu-id="1ad66-910">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="1ad66-911">下列代码为控制台提供程序启用作用域：</span><span class="sxs-lookup"><span data-stu-id="1ad66-911">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="1ad66-912">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="1ad66-912">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="1ad66-913">要启用基于作用域的日志记录，必须先配置 `IncludeScopes` 控制台记录器选项。</span><span class="sxs-lookup"><span data-stu-id="1ad66-913">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="1ad66-914">若要了解关配置，请参阅[配置](#configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="1ad66-914">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="1ad66-915">每条日志消息都包含作用域内的信息：</span><span class="sxs-lookup"><span data-stu-id="1ad66-915">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="1ad66-916">内置日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="1ad66-916">Built-in logging providers</span></span>

<span data-ttu-id="1ad66-917">ASP.NET Core 提供以下提供程序：</span><span class="sxs-lookup"><span data-stu-id="1ad66-917">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="1ad66-918">控制台</span><span class="sxs-lookup"><span data-stu-id="1ad66-918">Console</span></span>](#console-provider)
* [<span data-ttu-id="1ad66-919">调试</span><span class="sxs-lookup"><span data-stu-id="1ad66-919">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="1ad66-920">EventSource</span><span class="sxs-lookup"><span data-stu-id="1ad66-920">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="1ad66-921">EventLog</span><span class="sxs-lookup"><span data-stu-id="1ad66-921">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="1ad66-922">TraceSource</span><span class="sxs-lookup"><span data-stu-id="1ad66-922">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="1ad66-923">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="1ad66-923">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="1ad66-924">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="1ad66-924">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="1ad66-925">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="1ad66-925">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="1ad66-926">要通过 ASP.NET Core 模块了解 stdout 和调试日志记录，请参阅 <xref:test/troubleshoot-azure-iis> 和 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="1ad66-926">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="1ad66-927">控制台提供程序</span><span class="sxs-lookup"><span data-stu-id="1ad66-927">Console provider</span></span>

<span data-ttu-id="1ad66-928">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 提供程序包向控制台发送日志输出。</span><span class="sxs-lookup"><span data-stu-id="1ad66-928">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="1ad66-929">要查看控制台日志记录输出，请在项目文件夹中打开命令提示符并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1ad66-929">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="1ad66-930">调试提供程序</span><span class="sxs-lookup"><span data-stu-id="1ad66-930">Debug provider</span></span>

<span data-ttu-id="1ad66-931">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 提供程序包使用 [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) 类（`Debug.WriteLine` 方法调用）来写入日志输出。</span><span class="sxs-lookup"><span data-stu-id="1ad66-931">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="1ad66-932">在 Linux 中，此提供程序将日志写入 /var/log/message  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-932">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="1ad66-933">事件源提供程序</span><span class="sxs-lookup"><span data-stu-id="1ad66-933">Event Source provider</span></span>

<span data-ttu-id="1ad66-934">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) 提供程序包会使用名称 `Microsoft-Extensions-Logging` 跨平台写入事件源。</span><span class="sxs-lookup"><span data-stu-id="1ad66-934">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="1ad66-935">在 Windows 上，提供程序使用的是 [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-935">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="1ad66-936">在调用 `CreateDefaultBuilder` 来生成主机时，会自动添加事件源提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-936">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="1ad66-937">使用 [PerfView 实用工具](https://github.com/Microsoft/perfview)收集和查看日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-937">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="1ad66-938">虽然其他工具也可以查看 ETW 日志，但在处理由 ASP.NET Core 发出的 ETW 事件时，使用 PerfView 能获得最佳体验。</span><span class="sxs-lookup"><span data-stu-id="1ad66-938">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="1ad66-939">要将 PerfView 配置为收集此提供程序记录的事件，请向 Additional Providers 列表添加字符串 `*Microsoft-Extensions-Logging` 。</span><span class="sxs-lookup"><span data-stu-id="1ad66-939">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="1ad66-940">（请勿遗漏字符串起始处的星号。）</span><span class="sxs-lookup"><span data-stu-id="1ad66-940">(Don't miss the asterisk at the start of the string.)</span></span>

![其他 Perfview 提供程序](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="1ad66-942">Windows EventLog 提供程序</span><span class="sxs-lookup"><span data-stu-id="1ad66-942">Windows EventLog provider</span></span>

<span data-ttu-id="1ad66-943">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 提供程序包向 Windows 事件日志发送日志输出。</span><span class="sxs-lookup"><span data-stu-id="1ad66-943">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="1ad66-944">[AddEventLog 重载](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)允许传入 <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>。</span><span class="sxs-lookup"><span data-stu-id="1ad66-944">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="1ad66-945">如果为 `null` 或未指定，则使用以下默认设置：</span><span class="sxs-lookup"><span data-stu-id="1ad66-945">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="1ad66-946">`LogName`：“Application”</span><span class="sxs-lookup"><span data-stu-id="1ad66-946">`LogName`: "Application"</span></span>
* <span data-ttu-id="1ad66-947">`SourceName`：“.NET Runtime”</span><span class="sxs-lookup"><span data-stu-id="1ad66-947">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="1ad66-948">`MachineName`：使用本地计算机名称。</span><span class="sxs-lookup"><span data-stu-id="1ad66-948">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="1ad66-949">为[警告等级和更高等级](#log-level)记录事件。</span><span class="sxs-lookup"><span data-stu-id="1ad66-949">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="1ad66-950">以下示例将事件日志的默认日志级别设置为 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>：</span><span class="sxs-lookup"><span data-stu-id="1ad66-950">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="1ad66-951">TraceSource 提供程序</span><span class="sxs-lookup"><span data-stu-id="1ad66-951">TraceSource provider</span></span>

<span data-ttu-id="1ad66-952">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) 提供程序包使用 <xref:System.Diagnostics.TraceSource> 库和提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-952">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="1ad66-953">[AddTraceSource 重载](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) 允许传入资源开关和跟踪侦听器。</span><span class="sxs-lookup"><span data-stu-id="1ad66-953">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="1ad66-954">要使用此提供程序，应用必须在 .NET Framework（而非 .NET Core）上运行。</span><span class="sxs-lookup"><span data-stu-id="1ad66-954">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="1ad66-955">提供程序可将消息路由到多个[侦听器](/dotnet/framework/debug-trace-profile/trace-listeners)，例如示例应用中使用的 <xref:System.Diagnostics.TextWriterTraceListener>。</span><span class="sxs-lookup"><span data-stu-id="1ad66-955">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="1ad66-956">Azure 应用服务提供程序</span><span class="sxs-lookup"><span data-stu-id="1ad66-956">Azure App Service provider</span></span>

<span data-ttu-id="1ad66-957">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 提供程序包将日志写入 Azure App Service 应用的文件系统，以及 Azure 存储帐户中的 [blob 存储](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-957">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="1ad66-958">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中不包括此提供程序包。</span><span class="sxs-lookup"><span data-stu-id="1ad66-958">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="1ad66-959">如果面向 .NET Framework 或引用 `Microsoft.AspNetCore.App` 元包，请向项目添加提供程序包。</span><span class="sxs-lookup"><span data-stu-id="1ad66-959">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="1ad66-960"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> 重载允许传入 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>。</span><span class="sxs-lookup"><span data-stu-id="1ad66-960">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="1ad66-961">设置对象可以覆盖默认设置，例如日志记录输出模板、blob 名称和文件大小限制。</span><span class="sxs-lookup"><span data-stu-id="1ad66-961">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="1ad66-962">（“输出模板”是一个消息模板，除了通过 `ILogger` 方法调用提供的内容之外，还可将其应用于所有日志。  ）</span><span class="sxs-lookup"><span data-stu-id="1ad66-962">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="1ad66-963">在部署应用服务应用时，应用程序将采用 Azure 门户中“应用服务”页面下的[应用服务日志](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag)部分的设置  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-963">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="1ad66-964">更新以下设置后，更改立即生效，无需重启或重新部署应用。</span><span class="sxs-lookup"><span data-stu-id="1ad66-964">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="1ad66-965">应用程序日志记录(Filesystem)</span><span class="sxs-lookup"><span data-stu-id="1ad66-965">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="1ad66-966">应用程序日志记录(Blob)</span><span class="sxs-lookup"><span data-stu-id="1ad66-966">**Application Logging (Blob)**</span></span>

<span data-ttu-id="1ad66-967">日志文件的默认位置是 D:\\home\\LogFiles\\Application 文件夹，默认文件名为 diagnostics-yyyymmdd.txt   。</span><span class="sxs-lookup"><span data-stu-id="1ad66-967">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="1ad66-968">默认文件大小上限为 10 MB，默认最大保留文件数为 2。</span><span class="sxs-lookup"><span data-stu-id="1ad66-968">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="1ad66-969">默认 blob 名为 {app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-969">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="1ad66-970">该提供程序仅当项目在 Azure 环境中运行时有效。</span><span class="sxs-lookup"><span data-stu-id="1ad66-970">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="1ad66-971">项目在本地运行时，该提供程序无效 &mdash; 它不会写入本地文件或 blob 的本地开发存储。</span><span class="sxs-lookup"><span data-stu-id="1ad66-971">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="1ad66-972">Azure 日志流式处理</span><span class="sxs-lookup"><span data-stu-id="1ad66-972">Azure log streaming</span></span>

<span data-ttu-id="1ad66-973">通过 Azure 日志流式处理，可从以下位置实时查看日志活动：</span><span class="sxs-lookup"><span data-stu-id="1ad66-973">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="1ad66-974">应用服务器</span><span class="sxs-lookup"><span data-stu-id="1ad66-974">The app server</span></span>
* <span data-ttu-id="1ad66-975">Web 服务器</span><span class="sxs-lookup"><span data-stu-id="1ad66-975">The web server</span></span>
* <span data-ttu-id="1ad66-976">请求跟踪失败</span><span class="sxs-lookup"><span data-stu-id="1ad66-976">Failed request tracing</span></span>

<span data-ttu-id="1ad66-977">要配置 Azure 日志流式处理，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1ad66-977">To configure Azure log streaming:</span></span>

* <span data-ttu-id="1ad66-978">从应用的门户页导航到“应用服务日志”页  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-978">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="1ad66-979">将“应用程序日志记录(Filesystem)”设置为“开”   。</span><span class="sxs-lookup"><span data-stu-id="1ad66-979">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="1ad66-980">选择日志级别  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-980">Choose the log **Level**.</span></span> <span data-ttu-id="1ad66-981">此设置仅适用于 Azure 日志流，不适用于应用中的其他日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-981">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="1ad66-982">导航到“日志流”页面来查看应用消息  。</span><span class="sxs-lookup"><span data-stu-id="1ad66-982">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="1ad66-983">它们由应用通过 `ILogger` 接口记录。</span><span class="sxs-lookup"><span data-stu-id="1ad66-983">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="1ad66-984">Azure Application Insights 跟踪日志记录</span><span class="sxs-lookup"><span data-stu-id="1ad66-984">Azure Application Insights trace logging</span></span>

<span data-ttu-id="1ad66-985">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) 提供程序包将日志写入 Azure Application Insights。</span><span class="sxs-lookup"><span data-stu-id="1ad66-985">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="1ad66-986">Application Insights 是一项服务，可监视 Web 应用并提供用于查询和分析遥测数据的工具。</span><span class="sxs-lookup"><span data-stu-id="1ad66-986">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="1ad66-987">如果使用此提供程序，则可以使用 Application Insights 工具来查询和分析日志。</span><span class="sxs-lookup"><span data-stu-id="1ad66-987">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="1ad66-988">日志记录提供程序作为 [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)（这是提供 ASP.NET Core 的所有可用遥测的包）的依赖项包括在内。</span><span class="sxs-lookup"><span data-stu-id="1ad66-988">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="1ad66-989">如果使用此包，则无需安装提供程序包。</span><span class="sxs-lookup"><span data-stu-id="1ad66-989">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="1ad66-990">请勿使用用于 ASP.NET 4.x 的 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) 包&mdash;。</span><span class="sxs-lookup"><span data-stu-id="1ad66-990">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="1ad66-991">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="1ad66-991">For more information, see the following resources:</span></span>

* [<span data-ttu-id="1ad66-992">Application Insights 概述</span><span class="sxs-lookup"><span data-stu-id="1ad66-992">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="1ad66-993">[用于 ASP.NET Core 应用程序的 Application Insights](/azure/azure-monitor/app/asp-net-core) - 如果想要实现各种 Application Insights 遥测以及日志记录，请从这里开始。</span><span class="sxs-lookup"><span data-stu-id="1ad66-993">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="1ad66-994">[.NET Core ILogger 日志的 ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - 如果想要在没有其他 Application Insights 遥测的情况下实现日志记录提供程序，请从这里开始。</span><span class="sxs-lookup"><span data-stu-id="1ad66-994">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="1ad66-995">[Application Insights 日志记录适配器](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-995">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="1ad66-996">[安装、配置和初始化 Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn 网站上的交互式教程。</span><span class="sxs-lookup"><span data-stu-id="1ad66-996">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="1ad66-997">第三方日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="1ad66-997">Third-party logging providers</span></span>

<span data-ttu-id="1ad66-998">适用于 ASP.NET Core 的第三方日志记录框架：</span><span class="sxs-lookup"><span data-stu-id="1ad66-998">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="1ad66-999">[elmah.io](https://elmah.io/)（[GitHub 存储库](https://github.com/elmahio/Elmah.Io.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-999">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="1ad66-1000">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html)（[GitHub 存储库](https://github.com/mattwcole/gelf-extensions-logging)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-1000">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="1ad66-1001">[JSNLog](https://jsnlog.com/)（[GitHub 存储库](https://github.com/mperdeck/jsnlog)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-1001">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="1ad66-1002">[KissLog.net](https://kisslog.net/)（[GitHub 存储库](https://github.com/catalingavan/KissLog-net)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-1002">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="1ad66-1003">[Log4Net](https://logging.apache.org/log4net/)（[GitHub 存储库](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-1003">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="1ad66-1004">[Loggr](https://loggr.net/)（[GitHub 存储库](https://github.com/imobile3/Loggr.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-1004">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="1ad66-1005">[NLog](https://nlog-project.org/)（[GitHub 存储库](https://github.com/NLog/NLog.Extensions.Logging)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-1005">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="1ad66-1006">[Sentry](https://sentry.io/welcome/)（[GitHub 存储库](https://github.com/getsentry/sentry-dotnet)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-1006">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="1ad66-1007">[Serilog](https://serilog.net/)（[GitHub 存储库](https://github.com/serilog/serilog-aspnetcore)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-1007">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="1ad66-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging)（[Github 存储库](https://github.com/googleapis/google-cloud-dotnet)）</span><span class="sxs-lookup"><span data-stu-id="1ad66-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="1ad66-1009">某些第三方框架可以执行[语义日志记录（又称结构化日志记录）](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="1ad66-1009">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="1ad66-1010">使用第三方框架类似于使用以下内置提供程序之一：</span><span class="sxs-lookup"><span data-stu-id="1ad66-1010">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="1ad66-1011">将 NuGet 包添加到你的项目。</span><span class="sxs-lookup"><span data-stu-id="1ad66-1011">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="1ad66-1012">调用日志记录框架提供的 `ILoggerFactory` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="1ad66-1012">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="1ad66-1013">有关详细信息，请参阅各提供程序的相关文档。</span><span class="sxs-lookup"><span data-stu-id="1ad66-1013">For more information, see each provider's documentation.</span></span> <span data-ttu-id="1ad66-1014">Microsoft 不支持第三方日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ad66-1014">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1ad66-1015">其他资源</span><span class="sxs-lookup"><span data-stu-id="1ad66-1015">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
