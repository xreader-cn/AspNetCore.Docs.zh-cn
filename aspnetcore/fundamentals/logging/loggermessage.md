---
title: 在 ASP.NET Core 中使用 LoggerMessage 的高性能日志记录
author: guardrex
description: 了解如何使用 LoggerMessage 创建可缓存的委托。对于高性能日志记录方案，这些委托需要更少的对象分配。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2019
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: 56c60fe405660ff39e2696de591449c25f669de2
ms.sourcegitcommit: 0774a61a3a6c1412a7da0e7d932dc60c506441fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70059031"
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a><span data-ttu-id="10e65-103">在 ASP.NET Core 中使用 LoggerMessage 的高性能日志记录</span><span class="sxs-lookup"><span data-stu-id="10e65-103">High-performance logging with LoggerMessage in ASP.NET Core</span></span>

<span data-ttu-id="10e65-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="10e65-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="10e65-105"><xref:Microsoft.Extensions.Logging.LoggerMessage> 功能创建可缓存的委托，该功能比[记录器扩展方法](xref:Microsoft.Extensions.Logging.LoggerExtensions)（例如 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> 和 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>）需要的对象分配和计算开销少。</span><span class="sxs-lookup"><span data-stu-id="10e65-105"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="10e65-106">对于高性能日志记录方案，请使用 <xref:Microsoft.Extensions.Logging.LoggerMessage> 模式。</span><span class="sxs-lookup"><span data-stu-id="10e65-106">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="10e65-107">与记录器扩展方法相比，<xref:Microsoft.Extensions.Logging.LoggerMessage> 具有以下性能优势：</span><span class="sxs-lookup"><span data-stu-id="10e65-107"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="10e65-108">记录器扩展方法需要将值类型（例如 `int`）“装箱”（转换）到 `object` 中。</span><span class="sxs-lookup"><span data-stu-id="10e65-108">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="10e65-109"><xref:Microsoft.Extensions.Logging.LoggerMessage> 模式使用带强类型参数的静态 <xref:System.Action> 字段和扩展方法来避免装箱。</span><span class="sxs-lookup"><span data-stu-id="10e65-109">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="10e65-110">记录器扩展方法每次写入日志消息时必须分析消息模板（命名的格式字符串）。</span><span class="sxs-lookup"><span data-stu-id="10e65-110">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="10e65-111">如果已定义消息，那么 <xref:Microsoft.Extensions.Logging.LoggerMessage> 只需分析一次模板即可。</span><span class="sxs-lookup"><span data-stu-id="10e65-111"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="10e65-112">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="10e65-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="10e65-113">此示例应用通过基本引号跟踪系统演示 <xref:Microsoft.Extensions.Logging.LoggerMessage> 功能。</span><span class="sxs-lookup"><span data-stu-id="10e65-113">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="10e65-114">此应用使用内存中数据库添加和删除引号。</span><span class="sxs-lookup"><span data-stu-id="10e65-114">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="10e65-115">发生这些操作时，通过 <xref:Microsoft.Extensions.Logging.LoggerMessage> 模式生成日志消息。</span><span class="sxs-lookup"><span data-stu-id="10e65-115">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="10e65-116">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="10e65-116">LoggerMessage.Define</span></span>

<span data-ttu-id="10e65-117">[Define（LogLevel、EventId、字符串）](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*)创建用于记录消息的 <xref:System.Action> 委托。</span><span class="sxs-lookup"><span data-stu-id="10e65-117">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="10e65-118"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 重载允许向命名的格式字符串（模板）传递最多六个类型参数。</span><span class="sxs-lookup"><span data-stu-id="10e65-118"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="10e65-119">提供给 <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 方法的字符串是一个模板，而不是内插字符串。</span><span class="sxs-lookup"><span data-stu-id="10e65-119">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="10e65-120">占位符按照指定类型的顺序填充。</span><span class="sxs-lookup"><span data-stu-id="10e65-120">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="10e65-121">模板中的占位符名称在各个模板中应当具备描述性和一致性。</span><span class="sxs-lookup"><span data-stu-id="10e65-121">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="10e65-122">它们在结构化的日志数据中充当属性名称。</span><span class="sxs-lookup"><span data-stu-id="10e65-122">They serve as property names within structured log data.</span></span> <span data-ttu-id="10e65-123">对于占位符名称，建议使用[帕斯卡拼写法](/dotnet/standard/design-guidelines/capitalization-conventions)。</span><span class="sxs-lookup"><span data-stu-id="10e65-123">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="10e65-124">例如：`{Count}`、`{FirstName}`。</span><span class="sxs-lookup"><span data-stu-id="10e65-124">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="10e65-125">每条日志消息都是一个 <xref:System.Action>，保存在由 [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) 创建的静态字段中。</span><span class="sxs-lookup"><span data-stu-id="10e65-125">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="10e65-126">例如，示例应用创建一个字段来为索引页 (Internal/LoggerExtensions.cs  ) 描述 GET 请求的日志消息：</span><span class="sxs-lookup"><span data-stu-id="10e65-126">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="10e65-127">对于 <xref:System.Action>，指定：</span><span class="sxs-lookup"><span data-stu-id="10e65-127">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="10e65-128">日志级别。</span><span class="sxs-lookup"><span data-stu-id="10e65-128">The log level.</span></span>
* <span data-ttu-id="10e65-129">具有静态扩展方法名称的唯一事件标识符 (<xref:Microsoft.Extensions.Logging.EventId>)。</span><span class="sxs-lookup"><span data-stu-id="10e65-129">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="10e65-130">消息模板（命名的格式字符串）。</span><span class="sxs-lookup"><span data-stu-id="10e65-130">The message template (named format string).</span></span> 

<span data-ttu-id="10e65-131">对示例应用的索引页的请求设置：</span><span class="sxs-lookup"><span data-stu-id="10e65-131">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="10e65-132">将日志级别设置为 `Information`。</span><span class="sxs-lookup"><span data-stu-id="10e65-132">Log level to `Information`.</span></span>
* <span data-ttu-id="10e65-133">将事件 ID 设置为具有 `IndexPageRequested` 方法名称的 `1`。</span><span class="sxs-lookup"><span data-stu-id="10e65-133">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="10e65-134">将消息模板（命名的格式字符串）设置为字符串。</span><span class="sxs-lookup"><span data-stu-id="10e65-134">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="10e65-135">结构化日志记录存储可以使用事件名称（当它获得事件 ID 时）来丰富日志记录。</span><span class="sxs-lookup"><span data-stu-id="10e65-135">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="10e65-136">例如，[Serilog](https://github.com/serilog/serilog-extensions-logging) 使用该事件名称。</span><span class="sxs-lookup"><span data-stu-id="10e65-136">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="10e65-137">通过强类型扩展方法调用 <xref:System.Action>。</span><span class="sxs-lookup"><span data-stu-id="10e65-137">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="10e65-138">`IndexPageRequested` 方法在示例应用中记录索引页 GET 请求的消息：</span><span class="sxs-lookup"><span data-stu-id="10e65-138">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="10e65-139">在 Pages/Index.cshtml.cs  的 `OnGetAsync` 方法中，在记录器上调用 `IndexPageRequested`：</span><span class="sxs-lookup"><span data-stu-id="10e65-139">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="10e65-140">检查应用的控制台输出：</span><span class="sxs-lookup"><span data-stu-id="10e65-140">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="10e65-141">要将参数传递给日志消息，创建静态字段时最多定义六种类型。</span><span class="sxs-lookup"><span data-stu-id="10e65-141">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="10e65-142">通过为 <xref:System.Action> 字段定义 `string` 类型来添加引号时，示例应用会记录一个字符串：</span><span class="sxs-lookup"><span data-stu-id="10e65-142">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="10e65-143">委托的日志消息模板从提供的类型接收其占位符值。</span><span class="sxs-lookup"><span data-stu-id="10e65-143">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="10e65-144">示例应用定义一个委托，用于在 quote 参数是 `string` 的位置添加引号：</span><span class="sxs-lookup"><span data-stu-id="10e65-144">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="10e65-145">用于添加引号的静态扩展方法 `QuoteAdded` 接收 quote 参数值并将其传递给 <xref:System.Action> 委托：</span><span class="sxs-lookup"><span data-stu-id="10e65-145">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="10e65-146">在索引页的页面模型 (Pages/Index.cshtml.cs  ) 中，调用 `QuoteAdded` 来记录消息：</span><span class="sxs-lookup"><span data-stu-id="10e65-146">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="10e65-147">检查应用的控制台输出：</span><span class="sxs-lookup"><span data-stu-id="10e65-147">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="10e65-148">本示例应用实现用于删除引号的 [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) 模式。</span><span class="sxs-lookup"><span data-stu-id="10e65-148">The sample app implements a [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="10e65-149">为成功的删除操作记录提示性信息。</span><span class="sxs-lookup"><span data-stu-id="10e65-149">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="10e65-150">引发异常时，为删除操作记录错误消息。</span><span class="sxs-lookup"><span data-stu-id="10e65-150">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="10e65-151">针对未成功的删除操作，日志消息包括异常堆栈跟踪 (Internal/LoggerExtensions.cs  )：</span><span class="sxs-lookup"><span data-stu-id="10e65-151">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="10e65-152">请注意异常如何传递到 `QuoteDeleteFailed` 中的委托：</span><span class="sxs-lookup"><span data-stu-id="10e65-152">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="10e65-153">在索引页的页面模型中，成功删除引号时会在记录器上调用 `QuoteDeleted` 方法。</span><span class="sxs-lookup"><span data-stu-id="10e65-153">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="10e65-154">如果找不到要删除的引号，则会引发 <xref:System.ArgumentNullException>。</span><span class="sxs-lookup"><span data-stu-id="10e65-154">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="10e65-155">通过 [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) 语句捕获异常，并在 [catch](/dotnet/csharp/language-reference/keywords/try-catch) 块 (Pages/Index.cshtml.cs) 中调用记录器上的 `QuoteDeleteFailed` 方法来记录异常  ：</span><span class="sxs-lookup"><span data-stu-id="10e65-155">The exception is trapped by the [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

<span data-ttu-id="10e65-156">成功删除引号时，检查应用的控制台输出：</span><span class="sxs-lookup"><span data-stu-id="10e65-156">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="10e65-157">引号删除失败时，检查应用的控制台输出。</span><span class="sxs-lookup"><span data-stu-id="10e65-157">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="10e65-158">请注意，异常包括在日志消息中：</span><span class="sxs-lookup"><span data-stu-id="10e65-158">Note that the exception is included in the log message:</span></span>

```console
LoggerMessageSample.Pages.IndexModel: Error: Quote delete failed (Id = 999)

System.NullReferenceException: Object reference not set to an instance of an object.
   at lambda_method(Closure , ValueBuffer )
   at System.Linq.Enumerable.SelectEnumerableIterator`2.MoveNext()
   at Microsoft.EntityFrameworkCore.InMemory.Query.Internal.InMemoryShapedQueryCompilingExpressionVisitor.AsyncQueryingEnumerable`1.AsyncEnumerator.MoveNextAsync()
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at LoggerMessageSample.Pages.IndexModel.OnPostDeleteQuoteAsync(Int32 id) in c:\Users\guard\Documents\GitHub\Docs\aspnetcore\fundamentals\logging\loggermessage\samples\3.x\LoggerMessageSample\Pages\Index.cshtml.cs:line 77
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="10e65-159">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="10e65-159">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="10e65-160">[DefineScope（字符串）](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*)创建一个用于定义[日志作用域](xref:fundamentals/logging/index#log-scopes)的 <xref:System.Func%601> 委托。</span><span class="sxs-lookup"><span data-stu-id="10e65-160">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="10e65-161"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 重载允许向命名的格式字符串（模板）传递最多三个类型参数。</span><span class="sxs-lookup"><span data-stu-id="10e65-161"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="10e65-162"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 方法也一样，提供给 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 方法的字符串是一个模板，而不是内插字符串。</span><span class="sxs-lookup"><span data-stu-id="10e65-162">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="10e65-163">占位符按照指定类型的顺序填充。</span><span class="sxs-lookup"><span data-stu-id="10e65-163">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="10e65-164">模板中的占位符名称在各个模板中应当具备描述性和一致性。</span><span class="sxs-lookup"><span data-stu-id="10e65-164">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="10e65-165">它们在结构化的日志数据中充当属性名称。</span><span class="sxs-lookup"><span data-stu-id="10e65-165">They serve as property names within structured log data.</span></span> <span data-ttu-id="10e65-166">对于占位符名称，建议使用[帕斯卡拼写法](/dotnet/standard/design-guidelines/capitalization-conventions)。</span><span class="sxs-lookup"><span data-stu-id="10e65-166">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="10e65-167">例如：`{Count}`、`{FirstName}`。</span><span class="sxs-lookup"><span data-stu-id="10e65-167">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="10e65-168">使用 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 方法定义一个[日志作用域](xref:fundamentals/logging/index#log-scopes)，以应用到一系列日志消息中。</span><span class="sxs-lookup"><span data-stu-id="10e65-168">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="10e65-169">示例应用含有一个“全部清除”  按钮，用于删除数据库中的所有引号。</span><span class="sxs-lookup"><span data-stu-id="10e65-169">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="10e65-170">通过一次删除一个引号来将其删除。</span><span class="sxs-lookup"><span data-stu-id="10e65-170">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="10e65-171">每当删除一个引号时，都会在记录器上调用 `QuoteDeleted` 方法。</span><span class="sxs-lookup"><span data-stu-id="10e65-171">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="10e65-172">在这些日志消息中会添加一个日志作用域。</span><span class="sxs-lookup"><span data-stu-id="10e65-172">A log scope is added to these log messages.</span></span>

<span data-ttu-id="10e65-173">在 appsettings.json 的控制台记录器部分启用 `IncludeScopes`  ：</span><span class="sxs-lookup"><span data-stu-id="10e65-173">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="10e65-174">要创建日志作用域，请添加一个字段来保存该作用域的 <xref:System.Func%601> 委托。</span><span class="sxs-lookup"><span data-stu-id="10e65-174">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="10e65-175">示例应用创建一个名为 `_allQuotesDeletedScope` (Internal/LoggerExtensions.cs  ) 的字段：</span><span class="sxs-lookup"><span data-stu-id="10e65-175">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="10e65-176">使用 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 来创建委托。</span><span class="sxs-lookup"><span data-stu-id="10e65-176">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="10e65-177">调用委托时最多可以指定三种类型作为模板参数使用。</span><span class="sxs-lookup"><span data-stu-id="10e65-177">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="10e65-178">示例应用使用包含删除的引号数量的消息模板（`int` 类型）：</span><span class="sxs-lookup"><span data-stu-id="10e65-178">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="10e65-179">为日志消息提供一种静态扩展方法。</span><span class="sxs-lookup"><span data-stu-id="10e65-179">Provide a static extension method for the log message.</span></span> <span data-ttu-id="10e65-180">包含已命名属性的任何类型参数（这些参数出现在消息模板中）。</span><span class="sxs-lookup"><span data-stu-id="10e65-180">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="10e65-181">示例应用采用引号的 `count`，以删除并返回 `_allQuotesDeletedScope`：</span><span class="sxs-lookup"><span data-stu-id="10e65-181">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="10e65-182">该作用域将日志记录扩展调用包装在 [using](/dotnet/csharp/language-reference/keywords/using-statement) 块中：</span><span class="sxs-lookup"><span data-stu-id="10e65-182">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="10e65-183">检查应用控制台输出中的日志消息。</span><span class="sxs-lookup"><span data-stu-id="10e65-183">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="10e65-184">以下结果显示删除的三个引号，以及包含的日志作用域消息：</span><span class="sxs-lookup"><span data-stu-id="10e65-184">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="10e65-185"><xref:Microsoft.Extensions.Logging.LoggerMessage> 功能创建可缓存的委托，该功能比[记录器扩展方法](xref:Microsoft.Extensions.Logging.LoggerExtensions)（例如 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> 和 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>）需要的对象分配和计算开销少。</span><span class="sxs-lookup"><span data-stu-id="10e65-185"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="10e65-186">对于高性能日志记录方案，请使用 <xref:Microsoft.Extensions.Logging.LoggerMessage> 模式。</span><span class="sxs-lookup"><span data-stu-id="10e65-186">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="10e65-187">与记录器扩展方法相比，<xref:Microsoft.Extensions.Logging.LoggerMessage> 具有以下性能优势：</span><span class="sxs-lookup"><span data-stu-id="10e65-187"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="10e65-188">记录器扩展方法需要将值类型（例如 `int`）“装箱”（转换）到 `object` 中。</span><span class="sxs-lookup"><span data-stu-id="10e65-188">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="10e65-189"><xref:Microsoft.Extensions.Logging.LoggerMessage> 模式使用带强类型参数的静态 <xref:System.Action> 字段和扩展方法来避免装箱。</span><span class="sxs-lookup"><span data-stu-id="10e65-189">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="10e65-190">记录器扩展方法每次写入日志消息时必须分析消息模板（命名的格式字符串）。</span><span class="sxs-lookup"><span data-stu-id="10e65-190">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="10e65-191">如果已定义消息，那么 <xref:Microsoft.Extensions.Logging.LoggerMessage> 只需分析一次模板即可。</span><span class="sxs-lookup"><span data-stu-id="10e65-191"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="10e65-192">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="10e65-192">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="10e65-193">此示例应用通过基本引号跟踪系统演示 <xref:Microsoft.Extensions.Logging.LoggerMessage> 功能。</span><span class="sxs-lookup"><span data-stu-id="10e65-193">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="10e65-194">此应用使用内存中数据库添加和删除引号。</span><span class="sxs-lookup"><span data-stu-id="10e65-194">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="10e65-195">发生这些操作时，通过 <xref:Microsoft.Extensions.Logging.LoggerMessage> 模式生成日志消息。</span><span class="sxs-lookup"><span data-stu-id="10e65-195">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="10e65-196">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="10e65-196">LoggerMessage.Define</span></span>

<span data-ttu-id="10e65-197">[Define（LogLevel、EventId、字符串）](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*)创建用于记录消息的 <xref:System.Action> 委托。</span><span class="sxs-lookup"><span data-stu-id="10e65-197">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="10e65-198"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 重载允许向命名的格式字符串（模板）传递最多六个类型参数。</span><span class="sxs-lookup"><span data-stu-id="10e65-198"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="10e65-199">提供给 <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 方法的字符串是一个模板，而不是内插字符串。</span><span class="sxs-lookup"><span data-stu-id="10e65-199">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="10e65-200">占位符按照指定类型的顺序填充。</span><span class="sxs-lookup"><span data-stu-id="10e65-200">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="10e65-201">模板中的占位符名称在各个模板中应当具备描述性和一致性。</span><span class="sxs-lookup"><span data-stu-id="10e65-201">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="10e65-202">它们在结构化的日志数据中充当属性名称。</span><span class="sxs-lookup"><span data-stu-id="10e65-202">They serve as property names within structured log data.</span></span> <span data-ttu-id="10e65-203">对于占位符名称，建议使用[帕斯卡拼写法](/dotnet/standard/design-guidelines/capitalization-conventions)。</span><span class="sxs-lookup"><span data-stu-id="10e65-203">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="10e65-204">例如：`{Count}`、`{FirstName}`。</span><span class="sxs-lookup"><span data-stu-id="10e65-204">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="10e65-205">每条日志消息都是一个 <xref:System.Action>，保存在由 [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) 创建的静态字段中。</span><span class="sxs-lookup"><span data-stu-id="10e65-205">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="10e65-206">例如，示例应用创建一个字段来为索引页 (Internal/LoggerExtensions.cs  ) 描述 GET 请求的日志消息：</span><span class="sxs-lookup"><span data-stu-id="10e65-206">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="10e65-207">对于 <xref:System.Action>，指定：</span><span class="sxs-lookup"><span data-stu-id="10e65-207">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="10e65-208">日志级别。</span><span class="sxs-lookup"><span data-stu-id="10e65-208">The log level.</span></span>
* <span data-ttu-id="10e65-209">具有静态扩展方法名称的唯一事件标识符 (<xref:Microsoft.Extensions.Logging.EventId>)。</span><span class="sxs-lookup"><span data-stu-id="10e65-209">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="10e65-210">消息模板（命名的格式字符串）。</span><span class="sxs-lookup"><span data-stu-id="10e65-210">The message template (named format string).</span></span> 

<span data-ttu-id="10e65-211">对示例应用的索引页的请求设置：</span><span class="sxs-lookup"><span data-stu-id="10e65-211">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="10e65-212">将日志级别设置为 `Information`。</span><span class="sxs-lookup"><span data-stu-id="10e65-212">Log level to `Information`.</span></span>
* <span data-ttu-id="10e65-213">将事件 ID 设置为具有 `IndexPageRequested` 方法名称的 `1`。</span><span class="sxs-lookup"><span data-stu-id="10e65-213">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="10e65-214">将消息模板（命名的格式字符串）设置为字符串。</span><span class="sxs-lookup"><span data-stu-id="10e65-214">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="10e65-215">结构化日志记录存储可以使用事件名称（当它获得事件 ID 时）来丰富日志记录。</span><span class="sxs-lookup"><span data-stu-id="10e65-215">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="10e65-216">例如，[Serilog](https://github.com/serilog/serilog-extensions-logging) 使用该事件名称。</span><span class="sxs-lookup"><span data-stu-id="10e65-216">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="10e65-217">通过强类型扩展方法调用 <xref:System.Action>。</span><span class="sxs-lookup"><span data-stu-id="10e65-217">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="10e65-218">`IndexPageRequested` 方法在示例应用中记录索引页 GET 请求的消息：</span><span class="sxs-lookup"><span data-stu-id="10e65-218">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="10e65-219">在 Pages/Index.cshtml.cs  的 `OnGetAsync` 方法中，在记录器上调用 `IndexPageRequested`：</span><span class="sxs-lookup"><span data-stu-id="10e65-219">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="10e65-220">检查应用的控制台输出：</span><span class="sxs-lookup"><span data-stu-id="10e65-220">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="10e65-221">要将参数传递给日志消息，创建静态字段时最多定义六种类型。</span><span class="sxs-lookup"><span data-stu-id="10e65-221">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="10e65-222">通过为 <xref:System.Action> 字段定义 `string` 类型来添加引号时，示例应用会记录一个字符串：</span><span class="sxs-lookup"><span data-stu-id="10e65-222">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="10e65-223">委托的日志消息模板从提供的类型接收其占位符值。</span><span class="sxs-lookup"><span data-stu-id="10e65-223">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="10e65-224">示例应用定义一个委托，用于在 quote 参数是 `string` 的位置添加引号：</span><span class="sxs-lookup"><span data-stu-id="10e65-224">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="10e65-225">用于添加引号的静态扩展方法 `QuoteAdded` 接收 quote 参数值并将其传递给 <xref:System.Action> 委托：</span><span class="sxs-lookup"><span data-stu-id="10e65-225">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="10e65-226">在索引页的页面模型 (Pages/Index.cshtml.cs  ) 中，调用 `QuoteAdded` 来记录消息：</span><span class="sxs-lookup"><span data-stu-id="10e65-226">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="10e65-227">检查应用的控制台输出：</span><span class="sxs-lookup"><span data-stu-id="10e65-227">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="10e65-228">本示例应用实现用于删除引号的 [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) 模式。</span><span class="sxs-lookup"><span data-stu-id="10e65-228">The sample app implements a [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="10e65-229">为成功的删除操作记录提示性信息。</span><span class="sxs-lookup"><span data-stu-id="10e65-229">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="10e65-230">引发异常时，为删除操作记录错误消息。</span><span class="sxs-lookup"><span data-stu-id="10e65-230">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="10e65-231">针对未成功的删除操作，日志消息包括异常堆栈跟踪 (Internal/LoggerExtensions.cs  )：</span><span class="sxs-lookup"><span data-stu-id="10e65-231">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="10e65-232">请注意异常如何传递到 `QuoteDeleteFailed` 中的委托：</span><span class="sxs-lookup"><span data-stu-id="10e65-232">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="10e65-233">在索引页的页面模型中，成功删除引号时会在记录器上调用 `QuoteDeleted` 方法。</span><span class="sxs-lookup"><span data-stu-id="10e65-233">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="10e65-234">如果找不到要删除的引号，则会引发 <xref:System.ArgumentNullException>。</span><span class="sxs-lookup"><span data-stu-id="10e65-234">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="10e65-235">通过 [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) 语句捕获异常，并在 [catch](/dotnet/csharp/language-reference/keywords/try-catch) 块 (Pages/Index.cshtml.cs) 中调用记录器上的 `QuoteDeleteFailed` 方法来记录异常  ：</span><span class="sxs-lookup"><span data-stu-id="10e65-235">The exception is trapped by the [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

<span data-ttu-id="10e65-236">成功删除引号时，检查应用的控制台输出：</span><span class="sxs-lookup"><span data-stu-id="10e65-236">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="10e65-237">引号删除失败时，检查应用的控制台输出。</span><span class="sxs-lookup"><span data-stu-id="10e65-237">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="10e65-238">请注意，异常包括在日志消息中：</span><span class="sxs-lookup"><span data-stu-id="10e65-238">Note that the exception is included in the log message:</span></span>

```console
fail: LoggerMessageSample.Pages.IndexModel[5]
      => RequestId:0HL90M6E7PHK5:00000010 RequestPath:/ => /Index
      Quote delete failed (Id = 999)
System.ArgumentNullException: Value cannot be null.
Parameter name: entity
   at Microsoft.EntityFrameworkCore.Utilities.Check.NotNull[T]
       (T value, String parameterName)
   at Microsoft.EntityFrameworkCore.DbContext.Remove[TEntity](TEntity entity)
   at Microsoft.EntityFrameworkCore.Internal.InternalDbSet`1.Remove(TEntity entity)
   at LoggerMessageSample.Pages.IndexModel.<OnPostDeleteQuoteAsync>d__14.MoveNext() 
      in <PATH>\sample\Pages\Index.cshtml.cs:line 87
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="10e65-239">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="10e65-239">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="10e65-240">[DefineScope（字符串）](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*)创建一个用于定义[日志作用域](xref:fundamentals/logging/index#log-scopes)的 <xref:System.Func%601> 委托。</span><span class="sxs-lookup"><span data-stu-id="10e65-240">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="10e65-241"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 重载允许向命名的格式字符串（模板）传递最多三个类型参数。</span><span class="sxs-lookup"><span data-stu-id="10e65-241"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="10e65-242"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 方法也一样，提供给 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 方法的字符串是一个模板，而不是内插字符串。</span><span class="sxs-lookup"><span data-stu-id="10e65-242">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="10e65-243">占位符按照指定类型的顺序填充。</span><span class="sxs-lookup"><span data-stu-id="10e65-243">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="10e65-244">模板中的占位符名称在各个模板中应当具备描述性和一致性。</span><span class="sxs-lookup"><span data-stu-id="10e65-244">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="10e65-245">它们在结构化的日志数据中充当属性名称。</span><span class="sxs-lookup"><span data-stu-id="10e65-245">They serve as property names within structured log data.</span></span> <span data-ttu-id="10e65-246">对于占位符名称，建议使用[帕斯卡拼写法](/dotnet/standard/design-guidelines/capitalization-conventions)。</span><span class="sxs-lookup"><span data-stu-id="10e65-246">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="10e65-247">例如：`{Count}`、`{FirstName}`。</span><span class="sxs-lookup"><span data-stu-id="10e65-247">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="10e65-248">使用 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 方法定义一个[日志作用域](xref:fundamentals/logging/index#log-scopes)，以应用到一系列日志消息中。</span><span class="sxs-lookup"><span data-stu-id="10e65-248">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="10e65-249">示例应用含有一个“全部清除”  按钮，用于删除数据库中的所有引号。</span><span class="sxs-lookup"><span data-stu-id="10e65-249">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="10e65-250">通过一次删除一个引号来将其删除。</span><span class="sxs-lookup"><span data-stu-id="10e65-250">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="10e65-251">每当删除一个引号时，都会在记录器上调用 `QuoteDeleted` 方法。</span><span class="sxs-lookup"><span data-stu-id="10e65-251">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="10e65-252">在这些日志消息中会添加一个日志作用域。</span><span class="sxs-lookup"><span data-stu-id="10e65-252">A log scope is added to these log messages.</span></span>

<span data-ttu-id="10e65-253">在 appsettings.json 的控制台记录器部分启用 `IncludeScopes`  ：</span><span class="sxs-lookup"><span data-stu-id="10e65-253">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="10e65-254">要创建日志作用域，请添加一个字段来保存该作用域的 <xref:System.Func%601> 委托。</span><span class="sxs-lookup"><span data-stu-id="10e65-254">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="10e65-255">示例应用创建一个名为 `_allQuotesDeletedScope` (Internal/LoggerExtensions.cs  ) 的字段：</span><span class="sxs-lookup"><span data-stu-id="10e65-255">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="10e65-256">使用 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 来创建委托。</span><span class="sxs-lookup"><span data-stu-id="10e65-256">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="10e65-257">调用委托时最多可以指定三种类型作为模板参数使用。</span><span class="sxs-lookup"><span data-stu-id="10e65-257">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="10e65-258">示例应用使用包含删除的引号数量的消息模板（`int` 类型）：</span><span class="sxs-lookup"><span data-stu-id="10e65-258">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="10e65-259">为日志消息提供一种静态扩展方法。</span><span class="sxs-lookup"><span data-stu-id="10e65-259">Provide a static extension method for the log message.</span></span> <span data-ttu-id="10e65-260">包含已命名属性的任何类型参数（这些参数出现在消息模板中）。</span><span class="sxs-lookup"><span data-stu-id="10e65-260">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="10e65-261">示例应用采用引号的 `count`，以删除并返回 `_allQuotesDeletedScope`：</span><span class="sxs-lookup"><span data-stu-id="10e65-261">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="10e65-262">该作用域将日志记录扩展调用包装在 [using](/dotnet/csharp/language-reference/keywords/using-statement) 块中：</span><span class="sxs-lookup"><span data-stu-id="10e65-262">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="10e65-263">检查应用控制台输出中的日志消息。</span><span class="sxs-lookup"><span data-stu-id="10e65-263">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="10e65-264">以下结果显示删除的三个引号，以及包含的日志作用域消息：</span><span class="sxs-lookup"><span data-stu-id="10e65-264">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="10e65-265">其他资源</span><span class="sxs-lookup"><span data-stu-id="10e65-265">Additional resources</span></span>

* [<span data-ttu-id="10e65-266">日志记录</span><span class="sxs-lookup"><span data-stu-id="10e65-266">Logging</span></span>](xref:fundamentals/logging/index)
