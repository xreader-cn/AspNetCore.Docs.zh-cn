---
title: ASP.NET Core Web API 中的自定义格式化程序
author: rick-anderson
description: 了解如何为 ASP.NET Core 中的 Web API 创建和使用自定义格式化程序。
ms.author: riande
ms.date: 02/08/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 89fbb9d52d99d0eff6656eb6a5a9b4e1c01bc65c
ms.sourcegitcommit: 1833870ad0845326fb764fef1b530a07b9b5b099
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2020
ms.locfileid: "85347081"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="c1026-103">ASP.NET Core Web API 中的自定义格式化程序</span><span class="sxs-lookup"><span data-stu-id="c1026-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="c1026-104">作者： [Kirk Larkin](https://twitter.com/serpent5)和[Tom Dykstra](https://github.com/tdykstra)。</span><span class="sxs-lookup"><span data-stu-id="c1026-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Tom Dykstra](https://github.com/tdykstra).</span></span>

<span data-ttu-id="c1026-105">ASP.NET Core MVC 使用输入和输出格式化程序支持 Web API 中的数据交换。</span><span class="sxs-lookup"><span data-stu-id="c1026-105">ASP.NET Core MVC supports data exchange in Web APIs using input and output formatters.</span></span> <span data-ttu-id="c1026-106">[模型绑定](xref:mvc/models/model-binding)使用输入格式化程序。</span><span class="sxs-lookup"><span data-stu-id="c1026-106">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="c1026-107">输出格式化程序用于[设置响应的格式](xref:web-api/advanced/formatting)。</span><span class="sxs-lookup"><span data-stu-id="c1026-107">Output formatters are used to [format responses](xref:web-api/advanced/formatting).</span></span>

<span data-ttu-id="c1026-108">该框架为 JSON 和 XML 提供内置的输入和输出格式化程序。</span><span class="sxs-lookup"><span data-stu-id="c1026-108">The framework provides built-in input and output formatters for JSON and XML.</span></span> <span data-ttu-id="c1026-109">它为纯文本提供内置的输出格式化程序，但不为纯文本提供输入格式化程序。</span><span class="sxs-lookup"><span data-stu-id="c1026-109">It provides a built-in output formatter for plain text, but doesn't provide an input formatter for plain text.</span></span>

<span data-ttu-id="c1026-110">本文展示如何通过创建自定义格式化程序，添加对其他格式的支持。</span><span class="sxs-lookup"><span data-stu-id="c1026-110">This article shows how to add support for additional formats by creating custom formatters.</span></span> <span data-ttu-id="c1026-111">有关自定义纯文本输入格式化程序的示例，请参阅 GitHub 上的[TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) 。</span><span class="sxs-lookup"><span data-stu-id="c1026-111">For an example of a custom plain text input formatter, see [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) on GitHub.</span></span>

<span data-ttu-id="c1026-112">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="c1026-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="c1026-113">何时使用自定义格式化程序</span><span class="sxs-lookup"><span data-stu-id="c1026-113">When to use custom formatters</span></span>

<span data-ttu-id="c1026-114">使用自定义格式化程序添加对不是由 bult 格式化程序处理的内容类型的支持。</span><span class="sxs-lookup"><span data-stu-id="c1026-114">Use a custom formatter to add support for a content type that isn't handled by the bult-in formatters.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="c1026-115">有关如何使用自定义格式化程序的概述</span><span class="sxs-lookup"><span data-stu-id="c1026-115">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="c1026-116">创建自定义格式化程序：</span><span class="sxs-lookup"><span data-stu-id="c1026-116">To create a custom formatter:</span></span>

* <span data-ttu-id="c1026-117">若要对发送到客户端的数据进行序列化，请创建输出格式化程序类。</span><span class="sxs-lookup"><span data-stu-id="c1026-117">For serializing data sent to the client, create an output formatter class.</span></span>
* <span data-ttu-id="c1026-118">对于从客户端收到的 deserialzing 数据，请创建一个输入格式化程序类。</span><span class="sxs-lookup"><span data-stu-id="c1026-118">For deserialzing data received from the client, create an input formatter class.</span></span>
* <span data-ttu-id="c1026-119">将格式化程序类的实例添加 `InputFormatters` 到 `OutputFormatters` [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions)中的和集合。</span><span class="sxs-lookup"><span data-stu-id="c1026-119">Add instances of formatter classes to the `InputFormatters` and `OutputFormatters` collections in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="c1026-120">如何创建自定义格式化程序类</span><span class="sxs-lookup"><span data-stu-id="c1026-120">How to create a custom formatter class</span></span>

<span data-ttu-id="c1026-121">若要创建格式化程序，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c1026-121">To create a formatter:</span></span>

* <span data-ttu-id="c1026-122">从相应的基类中派生类。</span><span class="sxs-lookup"><span data-stu-id="c1026-122">Derive the class from the appropriate base class.</span></span> <span data-ttu-id="c1026-123">该示例应用派生自 <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> 和 <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> 。</span><span class="sxs-lookup"><span data-stu-id="c1026-123">The sample app derives from <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> and <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter>.</span></span>
* <span data-ttu-id="c1026-124">在构造函数中指定有效的媒体类型和编码。</span><span class="sxs-lookup"><span data-stu-id="c1026-124">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="c1026-125">替代 <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> 和 <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> 方法。</span><span class="sxs-lookup"><span data-stu-id="c1026-125">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> and <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> methods.</span></span>
* <span data-ttu-id="c1026-126">替代 <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> 和 `WriteResponseBodyAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="c1026-126">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> and `WriteResponseBodyAsync` methods.</span></span>

<span data-ttu-id="c1026-127">下面的代码演示了 `VcardOutputFormatter` [示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample)中的类：</span><span class="sxs-lookup"><span data-stu-id="c1026-127">The following code shows the `VcardOutputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=snippet)]
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="c1026-128">从相应的基类中派生</span><span class="sxs-lookup"><span data-stu-id="c1026-128">Derive from the appropriate base class</span></span>

<span data-ttu-id="c1026-129">对于文本媒体类型（例如，vCard），从 [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) 或 [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) 基类派生。</span><span class="sxs-lookup"><span data-stu-id="c1026-129">For text media types (for example, vCard), derive from the [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) or [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) base class.</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

<span data-ttu-id="c1026-130">对于二进制类型，从 [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) 或 [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) 基类派生。</span><span class="sxs-lookup"><span data-stu-id="c1026-130">For binary types, derive from the [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) or [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="c1026-131">指定有效的媒体类型和编码</span><span class="sxs-lookup"><span data-stu-id="c1026-131">Specify valid media types and encodings</span></span>

<span data-ttu-id="c1026-132">在构造函数中，通过添加到 `SupportedMediaTypes` 和 `SupportedEncodings` 集合来指定有效的媒体类型和编码。</span><span class="sxs-lookup"><span data-stu-id="c1026-132">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=ctor)]

<span data-ttu-id="c1026-133">格式化程序类**不**能将构造函数注入用于其依赖项。</span><span class="sxs-lookup"><span data-stu-id="c1026-133">A formatter class can **not** use constructor injection for its dependencies.</span></span> <span data-ttu-id="c1026-134">例如， `ILogger<VcardOutputFormatter>` 不能作为参数添加到构造函数。</span><span class="sxs-lookup"><span data-stu-id="c1026-134">For example, `ILogger<VcardOutputFormatter>` cannot be added as a parameter to the constructor.</span></span> <span data-ttu-id="c1026-135">若要访问服务，请使用传递给方法的上下文对象。</span><span class="sxs-lookup"><span data-stu-id="c1026-135">To access services, use the context object that gets passed in to the methods.</span></span> <span data-ttu-id="c1026-136">本文中的代码示例和[示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample)演示了如何执行此操作。</span><span class="sxs-lookup"><span data-stu-id="c1026-136">A code example in this article and the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample) show how to do this.</span></span>

### <a name="override-canreadtype-and-canwritetype"></a><span data-ttu-id="c1026-137">重写 CanReadType 和 CanWriteType</span><span class="sxs-lookup"><span data-stu-id="c1026-137">Override CanReadType and CanWriteType</span></span>

<span data-ttu-id="c1026-138">通过重写或方法指定要反序列化或序列化的类型 `CanReadType` `CanWriteType` 。</span><span class="sxs-lookup"><span data-stu-id="c1026-138">Specify the type to deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="c1026-139">例如，从某一类型创建 vCard 文本， `Contact` 反之亦然。</span><span class="sxs-lookup"><span data-stu-id="c1026-139">For example, creating vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="c1026-140">CanWriteResult 方法</span><span class="sxs-lookup"><span data-stu-id="c1026-140">The CanWriteResult method</span></span>

<span data-ttu-id="c1026-141">在某些情况下， `CanWriteResult` 必须重写而不是 `CanWriteType` 。</span><span class="sxs-lookup"><span data-stu-id="c1026-141">In some scenarios, `CanWriteResult` must be overridden rather than `CanWriteType`.</span></span> <span data-ttu-id="c1026-142">如果满足以下条件，则使用 `CanWriteResult`：</span><span class="sxs-lookup"><span data-stu-id="c1026-142">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="c1026-143">操作方法返回模型类。</span><span class="sxs-lookup"><span data-stu-id="c1026-143">The action method returns a model class.</span></span>
* <span data-ttu-id="c1026-144">具有可能在运行时返回的派生类。</span><span class="sxs-lookup"><span data-stu-id="c1026-144">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="c1026-145">操作返回的派生类必须在运行时已知。</span><span class="sxs-lookup"><span data-stu-id="c1026-145">The derived class returned by the action must be known at runtime.</span></span>

<span data-ttu-id="c1026-146">例如，假定操作方法：</span><span class="sxs-lookup"><span data-stu-id="c1026-146">For example, suppose the action method:</span></span>

* <span data-ttu-id="c1026-147">签名返回一个 `Person` 类型。</span><span class="sxs-lookup"><span data-stu-id="c1026-147">Signature returns a `Person` type.</span></span>
* <span data-ttu-id="c1026-148">可以返回 `Student` `Instructor` 派生自的或类型 `Person` 。</span><span class="sxs-lookup"><span data-stu-id="c1026-148">Can return a `Student` or `Instructor` type that derives from `Person`.</span></span> 

<span data-ttu-id="c1026-149">要使格式化程序仅处理 `Student` 对象，请在提供给方法的上下文对象中检查[对象](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object)的类型 `CanWriteResult` 。</span><span class="sxs-lookup"><span data-stu-id="c1026-149">For the formatter to handle only `Student` objects, check the type of [Object](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="c1026-150">当操作方法返回时 `IActionResult` ：</span><span class="sxs-lookup"><span data-stu-id="c1026-150">When the action method returns `IActionResult`:</span></span>

* <span data-ttu-id="c1026-151">不需要使用 `CanWriteResult` 。</span><span class="sxs-lookup"><span data-stu-id="c1026-151">It's not necessary to use `CanWriteResult`.</span></span>
* <span data-ttu-id="c1026-152">`CanWriteType`方法接收运行时类型。</span><span class="sxs-lookup"><span data-stu-id="c1026-152">The `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a><span data-ttu-id="c1026-153">重写 ReadRequestBodyAsync 和 WriteResponseBodyAsync</span><span class="sxs-lookup"><span data-stu-id="c1026-153">Override ReadRequestBodyAsync and WriteResponseBodyAsync</span></span>

<span data-ttu-id="c1026-154">反序列化或序列化在或中执行 `ReadRequestBodyAsync` `WriteResponseBodyAsync` 。</span><span class="sxs-lookup"><span data-stu-id="c1026-154">Deserialization or serialization is performed in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="c1026-155">下面的示例演示如何从依赖关系注入容器中获取服务。</span><span class="sxs-lookup"><span data-stu-id="c1026-155">The following example shows how to get services from the dependency injection container.</span></span> <span data-ttu-id="c1026-156">无法从构造函数参数获取服务。</span><span class="sxs-lookup"><span data-stu-id="c1026-156">Services can't be obtained from constructor parameters.</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=writeresponse)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="c1026-157">如何将 MVC 配置为使用自定义格式化程序</span><span class="sxs-lookup"><span data-stu-id="c1026-157">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="c1026-158">若要使用自定义格式化程序，请将格式化程序类的实例添加到 `InputFormatters` 或 `OutputFormatters` 集合。</span><span class="sxs-lookup"><span data-stu-id="c1026-158">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/3.1sample/Startup.cs?name=mvcoptions)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

<span data-ttu-id="c1026-159">按格式化程序的插入顺序对其进行计算。</span><span class="sxs-lookup"><span data-stu-id="c1026-159">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="c1026-160">第一个优先。</span><span class="sxs-lookup"><span data-stu-id="c1026-160">The first one takes precedence.</span></span>

## <a name="the-completed-vcardinputformatter-class"></a><span data-ttu-id="c1026-161">已完成 `VcardInputFormatter` 类</span><span class="sxs-lookup"><span data-stu-id="c1026-161">The completed `VcardInputFormatter` class</span></span>

<span data-ttu-id="c1026-162">下面的代码演示了 `VcardInputFormatter` [示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample)中的类：</span><span class="sxs-lookup"><span data-stu-id="c1026-162">The following code shows the `VcardInputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardInputFormatter.cs?name=snippet)]

## <a name="test-the-app"></a><span data-ttu-id="c1026-163">测试应用</span><span class="sxs-lookup"><span data-stu-id="c1026-163">Test the app</span></span>

<span data-ttu-id="c1026-164">[运行本文的示例应用程序](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)，它实现基本 vCard 输入和输出格式化程序。</span><span class="sxs-lookup"><span data-stu-id="c1026-164">[Run the sample app for this article](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), which implements basic vCard input and output formatters.</span></span> <span data-ttu-id="c1026-165">此应用程序读取和写入电子名片，如下所示：</span><span class="sxs-lookup"><span data-stu-id="c1026-165">The app reads and writes vCards similar to the following:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

<span data-ttu-id="c1026-166">若要查看 vCard 输出，请运行应用，并将 Get 请求发送到 Accept 标头 `text/vcard` `https://localhost:5001/api/contacts` 。</span><span class="sxs-lookup"><span data-stu-id="c1026-166">To see vCard output, run the app and send a Get request with Accept header `text/vcard` to `https://localhost:5001/api/contacts`.</span></span>

<span data-ttu-id="c1026-167">将 vCard 添加到内存中的联系人集合：</span><span class="sxs-lookup"><span data-stu-id="c1026-167">To add a vCard to the in-memory collection of contacts:</span></span>

* <span data-ttu-id="c1026-168">`Post` `/api/contacts` 使用 Postman 之类的工具将请求发送到。</span><span class="sxs-lookup"><span data-stu-id="c1026-168">Send a `Post` request to `/api/contacts` with a tool like Postman.</span></span>
* <span data-ttu-id="c1026-169">将 `Content-Type` 标头设置为 `text/vcard`。</span><span class="sxs-lookup"><span data-stu-id="c1026-169">Set the `Content-Type` header to `text/vcard`.</span></span>
* <span data-ttu-id="c1026-170">设置 `vCard` 正文中的文本，格式与前面的示例类似。</span><span class="sxs-lookup"><span data-stu-id="c1026-170">Set `vCard` text in the body, formatted like the preceding example.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c1026-171">其他资源</span><span class="sxs-lookup"><span data-stu-id="c1026-171">Additional resources</span></span>

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
