---
title: 在 ASP.NET Core 中使用 LoggerMessage 的高性能日志记录
author: rick-anderson
description: 了解如何使用 LoggerMessage 创建可缓存的委托。对于高性能日志记录方案，这些委托需要更少的对象分配。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: 9bd31682e9fb816110e814790a93ab53b1454cc9
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634015"
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a><span data-ttu-id="5c0fe-103">在 ASP.NET Core 中使用 LoggerMessage 的高性能日志记录</span><span class="sxs-lookup"><span data-stu-id="5c0fe-103">High-performance logging with LoggerMessage in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5c0fe-104"><xref:Microsoft.Extensions.Logging.LoggerMessage> 功能创建可缓存的委托，该功能比[记录器扩展方法](xref:Microsoft.Extensions.Logging.LoggerExtensions)（例如 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> 和 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>）需要的对象分配和计算开销少。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-104"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="5c0fe-105">对于高性能日志记录方案，请使用 <xref:Microsoft.Extensions.Logging.LoggerMessage> 模式。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-105">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="5c0fe-106">与记录器扩展方法相比，<xref:Microsoft.Extensions.Logging.LoggerMessage> 具有以下性能优势：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-106"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="5c0fe-107">记录器扩展方法需要将值类型（例如 `int`）“装箱”（转换）到 `object` 中。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-107">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="5c0fe-108"><xref:Microsoft.Extensions.Logging.LoggerMessage> 模式使用带强类型参数的静态 <xref:System.Action> 字段和扩展方法来避免装箱。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-108">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="5c0fe-109">记录器扩展方法每次写入日志消息时必须分析消息模板（命名的格式字符串）。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-109">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="5c0fe-110">如果已定义消息，那么 <xref:Microsoft.Extensions.Logging.LoggerMessage> 只需分析一次模板即可。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="5c0fe-111">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="5c0fe-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5c0fe-112">此示例应用通过基本引号跟踪系统演示 <xref:Microsoft.Extensions.Logging.LoggerMessage> 功能。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-112">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="5c0fe-113">此应用使用内存中数据库添加和删除引号。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-113">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="5c0fe-114">发生这些操作时，通过 <xref:Microsoft.Extensions.Logging.LoggerMessage> 模式生成日志消息。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-114">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="5c0fe-115">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="5c0fe-115">LoggerMessage.Define</span></span>

<span data-ttu-id="5c0fe-116">[Define（LogLevel、EventId、字符串）](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*)创建用于记录消息的 <xref:System.Action> 委托。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-116">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="5c0fe-117"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 重载允许向命名的格式字符串（模板）传递最多六个类型参数。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-117"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="5c0fe-118">提供给 <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 方法的字符串是一个模板，而不是内插字符串。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-118">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="5c0fe-119">占位符按照指定类型的顺序填充。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-119">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="5c0fe-120">模板中的占位符名称在各个模板中应当具备描述性和一致性。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-120">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="5c0fe-121">它们在结构化的日志数据中充当属性名称。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-121">They serve as property names within structured log data.</span></span> <span data-ttu-id="5c0fe-122">对于占位符名称，建议使用[帕斯卡拼写法](/dotnet/standard/design-guidelines/capitalization-conventions)。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-122">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="5c0fe-123">例如：`{Count}`、`{FirstName}`。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-123">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="5c0fe-124">每条日志消息都是一个 <xref:System.Action>，保存在由 [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) 创建的静态字段中。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-124">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="5c0fe-125">例如，示例应用创建一个字段来为索引页 (Internal/LoggerExtensions.cs) 描述 GET 请求的日志消息：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-125">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="5c0fe-126">对于 <xref:System.Action>，指定：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-126">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="5c0fe-127">日志级别。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-127">The log level.</span></span>
* <span data-ttu-id="5c0fe-128">具有静态扩展方法名称的唯一事件标识符 (<xref:Microsoft.Extensions.Logging.EventId>)。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-128">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="5c0fe-129">消息模板（命名的格式字符串）。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-129">The message template (named format string).</span></span> 

<span data-ttu-id="5c0fe-130">对示例应用的索引页的请求设置：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-130">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="5c0fe-131">将日志级别设置为 `Information`。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-131">Log level to `Information`.</span></span>
* <span data-ttu-id="5c0fe-132">将事件 ID 设置为具有 `IndexPageRequested` 方法名称的 `1`。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-132">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="5c0fe-133">将消息模板（命名的格式字符串）设置为字符串。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-133">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="5c0fe-134">结构化日志记录存储可以使用事件名称（当它获得事件 ID 时）来丰富日志记录。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-134">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="5c0fe-135">例如，[Serilog](https://github.com/serilog/serilog-extensions-logging) 使用该事件名称。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-135">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="5c0fe-136">通过强类型扩展方法调用 <xref:System.Action>。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-136">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="5c0fe-137">`IndexPageRequested` 方法在示例应用中记录索引页 GET 请求的消息：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-137">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="5c0fe-138">在 Pages/Index.cshtml.cs 的 `OnGetAsync` 方法中，在记录器上调用 `IndexPageRequested`：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-138">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="5c0fe-139">检查应用的控制台输出：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-139">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="5c0fe-140">要将参数传递给日志消息，创建静态字段时最多定义六种类型。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-140">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="5c0fe-141">通过为 <xref:System.Action> 字段定义 `string` 类型来添加引号时，示例应用会记录一个字符串：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-141">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="5c0fe-142">委托的日志消息模板从提供的类型接收其占位符值。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-142">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="5c0fe-143">示例应用定义一个委托，用于在 quote 参数是 `string` 的位置添加引号：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-143">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="5c0fe-144">用于添加引号的静态扩展方法 `QuoteAdded` 接收 quote 参数值并将其传递给 <xref:System.Action> 委托：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-144">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="5c0fe-145">在索引页的页面模型 (Pages/Index.cshtml.cs) 中，调用 `QuoteAdded` 来记录消息：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-145">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="5c0fe-146">检查应用的控制台输出：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-146">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="5c0fe-147">本示例应用实现用于删除引号的 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 模式。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-147">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="5c0fe-148">为成功的删除操作记录提示性信息。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-148">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="5c0fe-149">引发异常时，为删除操作记录错误消息。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-149">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="5c0fe-150">针对未成功的删除操作，日志消息包括异常堆栈跟踪 (Internal/LoggerExtensions.cs)：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-150">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="5c0fe-151">请注意异常如何传递到 `QuoteDeleteFailed` 中的委托：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-151">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="5c0fe-152">在索引页的页面模型中，成功删除引号时会在记录器上调用 `QuoteDeleted` 方法。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-152">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="5c0fe-153">如果找不到要删除的引号，则会引发 <xref:System.ArgumentNullException>。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-153">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="5c0fe-154">通过 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 语句捕获异常，并在 [catch](/dotnet/csharp/language-reference/keywords/try-catch) 块 (Pages/Index.cshtml.cs) 中调用记录器上的 `QuoteDeleteFailed` 方法来记录异常：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-154">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

<span data-ttu-id="5c0fe-155">成功删除引号时，检查应用的控制台输出：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-155">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="5c0fe-156">引号删除失败时，检查应用的控制台输出。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-156">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="5c0fe-157">请注意，异常包括在日志消息中：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-157">Note that the exception is included in the log message:</span></span>

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

## <a name="loggermessagedefinescope"></a><span data-ttu-id="5c0fe-158">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="5c0fe-158">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="5c0fe-159">[DefineScope（字符串）](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*)创建一个用于定义[日志作用域](xref:fundamentals/logging/index#log-scopes)的 <xref:System.Func%601> 委托。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-159">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="5c0fe-160"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 重载允许向命名的格式字符串（模板）传递最多三个类型参数。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-160"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="5c0fe-161"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 方法也一样，提供给 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 方法的字符串是一个模板，而不是内插字符串。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-161">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="5c0fe-162">占位符按照指定类型的顺序填充。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-162">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="5c0fe-163">模板中的占位符名称在各个模板中应当具备描述性和一致性。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-163">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="5c0fe-164">它们在结构化的日志数据中充当属性名称。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-164">They serve as property names within structured log data.</span></span> <span data-ttu-id="5c0fe-165">对于占位符名称，建议使用[帕斯卡拼写法](/dotnet/standard/design-guidelines/capitalization-conventions)。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-165">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="5c0fe-166">例如：`{Count}`、`{FirstName}`。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-166">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="5c0fe-167">使用 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 方法定义一个[日志作用域](xref:fundamentals/logging/index#log-scopes)，以应用到一系列日志消息中。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-167">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="5c0fe-168">示例应用含有一个“全部清除”按钮，用于删除数据库中的所有引号。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-168">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="5c0fe-169">通过一次删除一个引号来将其删除。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-169">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="5c0fe-170">每当删除一个引号时，都会在记录器上调用 `QuoteDeleted` 方法。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-170">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="5c0fe-171">在这些日志消息中会添加一个日志作用域。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-171">A log scope is added to these log messages.</span></span>

<span data-ttu-id="5c0fe-172">在 appsettings.json 的控制台记录器部分启用 `IncludeScopes`：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-172">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-json[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="5c0fe-173">要创建日志作用域，请添加一个字段来保存该作用域的 <xref:System.Func%601> 委托。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-173">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="5c0fe-174">示例应用创建一个名为 `_allQuotesDeletedScope` (Internal/LoggerExtensions.cs) 的字段：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-174">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="5c0fe-175">使用 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 来创建委托。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-175">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="5c0fe-176">调用委托时最多可以指定三种类型作为模板参数使用。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-176">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="5c0fe-177">示例应用使用包含删除的引号数量的消息模板（`int` 类型）：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-177">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="5c0fe-178">为日志消息提供一种静态扩展方法。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-178">Provide a static extension method for the log message.</span></span> <span data-ttu-id="5c0fe-179">包含已命名属性的任何类型参数（这些参数出现在消息模板中）。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-179">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="5c0fe-180">示例应用采用引号的 `count`，以删除并返回 `_allQuotesDeletedScope`：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-180">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="5c0fe-181">该作用域将日志记录扩展调用包装在 [using](/dotnet/csharp/language-reference/keywords/using-statement) 块中：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-181">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="5c0fe-182">检查应用控制台输出中的日志消息。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-182">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="5c0fe-183">以下结果显示删除的三个引号，以及包含的日志作用域消息：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-183">The following result shows three quotes deleted with the log scope message included:</span></span>

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

<span data-ttu-id="5c0fe-184"><xref:Microsoft.Extensions.Logging.LoggerMessage> 功能创建可缓存的委托，该功能比[记录器扩展方法](xref:Microsoft.Extensions.Logging.LoggerExtensions)（例如 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> 和 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>）需要的对象分配和计算开销少。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-184"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="5c0fe-185">对于高性能日志记录方案，请使用 <xref:Microsoft.Extensions.Logging.LoggerMessage> 模式。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-185">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="5c0fe-186">与记录器扩展方法相比，<xref:Microsoft.Extensions.Logging.LoggerMessage> 具有以下性能优势：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-186"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="5c0fe-187">记录器扩展方法需要将值类型（例如 `int`）“装箱”（转换）到 `object` 中。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-187">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="5c0fe-188"><xref:Microsoft.Extensions.Logging.LoggerMessage> 模式使用带强类型参数的静态 <xref:System.Action> 字段和扩展方法来避免装箱。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-188">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="5c0fe-189">记录器扩展方法每次写入日志消息时必须分析消息模板（命名的格式字符串）。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-189">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="5c0fe-190">如果已定义消息，那么 <xref:Microsoft.Extensions.Logging.LoggerMessage> 只需分析一次模板即可。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="5c0fe-191">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="5c0fe-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5c0fe-192">此示例应用通过基本引号跟踪系统演示 <xref:Microsoft.Extensions.Logging.LoggerMessage> 功能。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-192">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="5c0fe-193">此应用使用内存中数据库添加和删除引号。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-193">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="5c0fe-194">发生这些操作时，通过 <xref:Microsoft.Extensions.Logging.LoggerMessage> 模式生成日志消息。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-194">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="5c0fe-195">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="5c0fe-195">LoggerMessage.Define</span></span>

<span data-ttu-id="5c0fe-196">[Define（LogLevel、EventId、字符串）](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*)创建用于记录消息的 <xref:System.Action> 委托。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-196">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="5c0fe-197"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 重载允许向命名的格式字符串（模板）传递最多六个类型参数。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-197"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="5c0fe-198">提供给 <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 方法的字符串是一个模板，而不是内插字符串。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-198">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="5c0fe-199">占位符按照指定类型的顺序填充。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-199">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="5c0fe-200">模板中的占位符名称在各个模板中应当具备描述性和一致性。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-200">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="5c0fe-201">它们在结构化的日志数据中充当属性名称。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-201">They serve as property names within structured log data.</span></span> <span data-ttu-id="5c0fe-202">对于占位符名称，建议使用[帕斯卡拼写法](/dotnet/standard/design-guidelines/capitalization-conventions)。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-202">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="5c0fe-203">例如：`{Count}`、`{FirstName}`。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-203">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="5c0fe-204">每条日志消息都是一个 <xref:System.Action>，保存在由 [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) 创建的静态字段中。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-204">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="5c0fe-205">例如，示例应用创建一个字段来为索引页 (Internal/LoggerExtensions.cs) 描述 GET 请求的日志消息：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-205">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="5c0fe-206">对于 <xref:System.Action>，指定：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-206">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="5c0fe-207">日志级别。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-207">The log level.</span></span>
* <span data-ttu-id="5c0fe-208">具有静态扩展方法名称的唯一事件标识符 (<xref:Microsoft.Extensions.Logging.EventId>)。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-208">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="5c0fe-209">消息模板（命名的格式字符串）。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-209">The message template (named format string).</span></span> 

<span data-ttu-id="5c0fe-210">对示例应用的索引页的请求设置：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-210">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="5c0fe-211">将日志级别设置为 `Information`。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-211">Log level to `Information`.</span></span>
* <span data-ttu-id="5c0fe-212">将事件 ID 设置为具有 `IndexPageRequested` 方法名称的 `1`。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-212">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="5c0fe-213">将消息模板（命名的格式字符串）设置为字符串。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-213">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="5c0fe-214">结构化日志记录存储可以使用事件名称（当它获得事件 ID 时）来丰富日志记录。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-214">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="5c0fe-215">例如，[Serilog](https://github.com/serilog/serilog-extensions-logging) 使用该事件名称。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-215">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="5c0fe-216">通过强类型扩展方法调用 <xref:System.Action>。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-216">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="5c0fe-217">`IndexPageRequested` 方法在示例应用中记录索引页 GET 请求的消息：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-217">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="5c0fe-218">在 Pages/Index.cshtml.cs 的 `OnGetAsync` 方法中，在记录器上调用 `IndexPageRequested`：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-218">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="5c0fe-219">检查应用的控制台输出：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-219">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="5c0fe-220">要将参数传递给日志消息，创建静态字段时最多定义六种类型。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-220">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="5c0fe-221">通过为 <xref:System.Action> 字段定义 `string` 类型来添加引号时，示例应用会记录一个字符串：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-221">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="5c0fe-222">委托的日志消息模板从提供的类型接收其占位符值。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-222">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="5c0fe-223">示例应用定义一个委托，用于在 quote 参数是 `string` 的位置添加引号：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-223">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="5c0fe-224">用于添加引号的静态扩展方法 `QuoteAdded` 接收 quote 参数值并将其传递给 <xref:System.Action> 委托：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-224">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="5c0fe-225">在索引页的页面模型 (Pages/Index.cshtml.cs) 中，调用 `QuoteAdded` 来记录消息：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-225">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="5c0fe-226">检查应用的控制台输出：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-226">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="5c0fe-227">本示例应用实现用于删除引号的 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 模式。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-227">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="5c0fe-228">为成功的删除操作记录提示性信息。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-228">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="5c0fe-229">引发异常时，为删除操作记录错误消息。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-229">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="5c0fe-230">针对未成功的删除操作，日志消息包括异常堆栈跟踪 (Internal/LoggerExtensions.cs)：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-230">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="5c0fe-231">请注意异常如何传递到 `QuoteDeleteFailed` 中的委托：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-231">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="5c0fe-232">在索引页的页面模型中，成功删除引号时会在记录器上调用 `QuoteDeleted` 方法。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-232">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="5c0fe-233">如果找不到要删除的引号，则会引发 <xref:System.ArgumentNullException>。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-233">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="5c0fe-234">通过 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 语句捕获异常，并在 [catch](/dotnet/csharp/language-reference/keywords/try-catch) 块 (Pages/Index.cshtml.cs) 中调用记录器上的 `QuoteDeleteFailed` 方法来记录异常：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-234">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

<span data-ttu-id="5c0fe-235">成功删除引号时，检查应用的控制台输出：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-235">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="5c0fe-236">引号删除失败时，检查应用的控制台输出。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-236">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="5c0fe-237">请注意，异常包括在日志消息中：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-237">Note that the exception is included in the log message:</span></span>

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

## <a name="loggermessagedefinescope"></a><span data-ttu-id="5c0fe-238">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="5c0fe-238">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="5c0fe-239">[DefineScope（字符串）](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*)创建一个用于定义[日志作用域](xref:fundamentals/logging/index#log-scopes)的 <xref:System.Func%601> 委托。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-239">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="5c0fe-240"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 重载允许向命名的格式字符串（模板）传递最多三个类型参数。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-240"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="5c0fe-241"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 方法也一样，提供给 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 方法的字符串是一个模板，而不是内插字符串。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-241">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="5c0fe-242">占位符按照指定类型的顺序填充。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-242">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="5c0fe-243">模板中的占位符名称在各个模板中应当具备描述性和一致性。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-243">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="5c0fe-244">它们在结构化的日志数据中充当属性名称。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-244">They serve as property names within structured log data.</span></span> <span data-ttu-id="5c0fe-245">对于占位符名称，建议使用[帕斯卡拼写法](/dotnet/standard/design-guidelines/capitalization-conventions)。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-245">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="5c0fe-246">例如：`{Count}`、`{FirstName}`。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-246">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="5c0fe-247">使用 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 方法定义一个[日志作用域](xref:fundamentals/logging/index#log-scopes)，以应用到一系列日志消息中。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-247">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="5c0fe-248">示例应用含有一个“全部清除”按钮，用于删除数据库中的所有引号。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-248">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="5c0fe-249">通过一次删除一个引号来将其删除。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-249">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="5c0fe-250">每当删除一个引号时，都会在记录器上调用 `QuoteDeleted` 方法。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-250">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="5c0fe-251">在这些日志消息中会添加一个日志作用域。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-251">A log scope is added to these log messages.</span></span>

<span data-ttu-id="5c0fe-252">在 appsettings.json 的控制台记录器部分启用 `IncludeScopes`：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-252">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-json[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="5c0fe-253">要创建日志作用域，请添加一个字段来保存该作用域的 <xref:System.Func%601> 委托。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-253">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="5c0fe-254">示例应用创建一个名为 `_allQuotesDeletedScope` (Internal/LoggerExtensions.cs) 的字段：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-254">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="5c0fe-255">使用 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 来创建委托。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-255">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="5c0fe-256">调用委托时最多可以指定三种类型作为模板参数使用。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-256">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="5c0fe-257">示例应用使用包含删除的引号数量的消息模板（`int` 类型）：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-257">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="5c0fe-258">为日志消息提供一种静态扩展方法。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-258">Provide a static extension method for the log message.</span></span> <span data-ttu-id="5c0fe-259">包含已命名属性的任何类型参数（这些参数出现在消息模板中）。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-259">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="5c0fe-260">示例应用采用引号的 `count`，以删除并返回 `_allQuotesDeletedScope`：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-260">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="5c0fe-261">该作用域将日志记录扩展调用包装在 [using](/dotnet/csharp/language-reference/keywords/using-statement) 块中：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-261">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="5c0fe-262">检查应用控制台输出中的日志消息。</span><span class="sxs-lookup"><span data-stu-id="5c0fe-262">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="5c0fe-263">以下结果显示删除的三个引号，以及包含的日志作用域消息：</span><span class="sxs-lookup"><span data-stu-id="5c0fe-263">The following result shows three quotes deleted with the log scope message included:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="5c0fe-264">其他资源</span><span class="sxs-lookup"><span data-stu-id="5c0fe-264">Additional resources</span></span>

* [<span data-ttu-id="5c0fe-265">日志记录</span><span class="sxs-lookup"><span data-stu-id="5c0fe-265">Logging</span></span>](xref:fundamentals/logging/index)
