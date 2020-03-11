---
title: ASP.NET Core Web API 中的自定义格式化程序
author: rick-anderson
description: 了解如何为 ASP.NET Core 中的 Web API 创建和使用自定义格式化程序。
ms.author: riande
ms.date: 02/08/2017
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: dd25cda460ba758cd07de094eaadd1f2d8c28657
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654954"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="1cc21-103">ASP.NET Core Web API 中的自定义格式化程序</span><span class="sxs-lookup"><span data-stu-id="1cc21-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="1cc21-104">作者：[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="1cc21-104">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="1cc21-105">ASP.NET Core MVC 使用输入和输出格式化程序支持 Web API 中的数据交换。</span><span class="sxs-lookup"><span data-stu-id="1cc21-105">ASP.NET Core MVC supports data exchange in Web APIs using input and output formatters.</span></span> <span data-ttu-id="1cc21-106">[模型绑定](xref:mvc/models/model-binding)使用输入格式化程序。</span><span class="sxs-lookup"><span data-stu-id="1cc21-106">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="1cc21-107">[格式响应](xref:web-api/advanced/formatting)使用输出格式化程序。</span><span class="sxs-lookup"><span data-stu-id="1cc21-107">Output formatters are used to [format responses](xref:web-api/advanced/formatting).</span></span>

<span data-ttu-id="1cc21-108">该框架为 JSON 和 XML 提供内置的输入和输出格式化程序。</span><span class="sxs-lookup"><span data-stu-id="1cc21-108">The framework provides built-in input and output formatters for JSON and XML.</span></span> <span data-ttu-id="1cc21-109">它为纯文本提供内置的输出格式化程序，但不为纯文本提供输入格式化程序。</span><span class="sxs-lookup"><span data-stu-id="1cc21-109">It provides a built-in output formatter for plain text, but doesn't provide an input formatter for plain text.</span></span>

<span data-ttu-id="1cc21-110">本文展示如何通过创建自定义格式化程序，添加对其他格式的支持。</span><span class="sxs-lookup"><span data-stu-id="1cc21-110">This article shows how to add support for additional formats by creating custom formatters.</span></span> <span data-ttu-id="1cc21-111">有关纯文本的自定义输入格式化程序的示例，请参阅 GitHub 上的 [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs)。</span><span class="sxs-lookup"><span data-stu-id="1cc21-111">For an example of a custom input formatter for plain text, see [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) on GitHub.</span></span>

<span data-ttu-id="1cc21-112">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1cc21-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="1cc21-113">何时使用自定义格式化程序</span><span class="sxs-lookup"><span data-stu-id="1cc21-113">When to use custom formatters</span></span>

<span data-ttu-id="1cc21-114">如果希望[内容协商](xref:web-api/advanced/formatting#content-negotiation)过程支持内置格式化程序所不支持的内容类型，可使用自定义格式化程序。</span><span class="sxs-lookup"><span data-stu-id="1cc21-114">Use a custom formatter when you want the [content negotiation](xref:web-api/advanced/formatting#content-negotiation) process to support a content type that isn't supported by the built-in formatters.</span></span>

<span data-ttu-id="1cc21-115">例如，如果 Web API 的某些客户端可以处理 [Protobuf](https://github.com/google/protobuf) 格式，你可能想在这些客户端上使用 Protobuf，因为它更高效。</span><span class="sxs-lookup"><span data-stu-id="1cc21-115">For example, if some of the clients for your web API can handle the [Protobuf](https://github.com/google/protobuf) format, you might want to use Protobuf with those clients because it's more efficient.</span></span> <span data-ttu-id="1cc21-116">或者，你可能希望 Web API 使用 [vCard](https://wikipedia.org/wiki/VCard) 格式发送联系人姓名和地址，这种格式经常用于交换联系人数据。</span><span class="sxs-lookup"><span data-stu-id="1cc21-116">Or you might want your web API to send contact names and addresses in [vCard](https://wikipedia.org/wiki/VCard) format, a commonly used format for exchanging contact data.</span></span> <span data-ttu-id="1cc21-117">本文提供的示例应用可实现简单的 vCard 格式化程序。</span><span class="sxs-lookup"><span data-stu-id="1cc21-117">The sample app provided with this article implements a simple vCard formatter.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="1cc21-118">有关如何使用自定义格式化程序的概述</span><span class="sxs-lookup"><span data-stu-id="1cc21-118">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="1cc21-119">创建和使用自定义格式化程序的步骤如下：</span><span class="sxs-lookup"><span data-stu-id="1cc21-119">Here are the steps to create and use a custom formatter:</span></span>

* <span data-ttu-id="1cc21-120">如果想对要发送到客户端的数据进行序列化，则创建输出格式化程序类。</span><span class="sxs-lookup"><span data-stu-id="1cc21-120">Create an output formatter class if you want to serialize data to send to the client.</span></span>
* <span data-ttu-id="1cc21-121">如果想对从客户端接收的数据进行反序列化，则创建输入格式化程序类。</span><span class="sxs-lookup"><span data-stu-id="1cc21-121">Create an input formatter class if you want to deserialize data received from the client.</span></span>
* <span data-ttu-id="1cc21-122">将格式化程序的实例添加到 `InputFormatters`MvcOptions`OutputFormatters` 中的 [ 和 ](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions) 集合。</span><span class="sxs-lookup"><span data-stu-id="1cc21-122">Add instances of your formatters to the `InputFormatters` and `OutputFormatters` collections in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span></span>

<span data-ttu-id="1cc21-123">以下部分针对其中每个步骤提供了指南和代码示例。</span><span class="sxs-lookup"><span data-stu-id="1cc21-123">The following sections provide guidance and code examples for each of these steps.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="1cc21-124">如何创建自定义格式化程序类</span><span class="sxs-lookup"><span data-stu-id="1cc21-124">How to create a custom formatter class</span></span>

<span data-ttu-id="1cc21-125">若要创建格式化程序，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1cc21-125">To create a formatter:</span></span>

* <span data-ttu-id="1cc21-126">从相应的基类中派生类。</span><span class="sxs-lookup"><span data-stu-id="1cc21-126">Derive the class from the appropriate base class.</span></span>
* <span data-ttu-id="1cc21-127">在构造函数中指定有效的媒体类型和编码。</span><span class="sxs-lookup"><span data-stu-id="1cc21-127">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="1cc21-128">重写 `CanReadType`/`CanWriteType` 方法</span><span class="sxs-lookup"><span data-stu-id="1cc21-128">Override `CanReadType`/`CanWriteType` methods</span></span>
* <span data-ttu-id="1cc21-129">重写 `ReadRequestBodyAsync`/`WriteResponseBodyAsync` 方法</span><span class="sxs-lookup"><span data-stu-id="1cc21-129">Override `ReadRequestBodyAsync`/`WriteResponseBodyAsync` methods</span></span>
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="1cc21-130">从相应的基类中派生</span><span class="sxs-lookup"><span data-stu-id="1cc21-130">Derive from the appropriate base class</span></span>

<span data-ttu-id="1cc21-131">对于文本媒体类型（例如，vCard），从 [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) 或 [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) 基类派生。</span><span class="sxs-lookup"><span data-stu-id="1cc21-131">For text media types (for example, vCard), derive from the [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) or [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) base class.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

<span data-ttu-id="1cc21-132">有关输入格式化程序示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)。</span><span class="sxs-lookup"><span data-stu-id="1cc21-132">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

<span data-ttu-id="1cc21-133">对于二进制类型，从 [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) 或 [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) 基类派生。</span><span class="sxs-lookup"><span data-stu-id="1cc21-133">For binary types, derive from the [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) or [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="1cc21-134">指定有效的媒体类型和编码</span><span class="sxs-lookup"><span data-stu-id="1cc21-134">Specify valid media types and encodings</span></span>

<span data-ttu-id="1cc21-135">在构造函数中，通过添加到 `SupportedMediaTypes` 和 `SupportedEncodings` 集合来指定有效的媒体类型和编码。</span><span class="sxs-lookup"><span data-stu-id="1cc21-135">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

<span data-ttu-id="1cc21-136">有关输入格式化程序示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)。</span><span class="sxs-lookup"><span data-stu-id="1cc21-136">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

> [!NOTE]
> <span data-ttu-id="1cc21-137">不能在格式化程序类中执行构造函数依赖关系注入。</span><span class="sxs-lookup"><span data-stu-id="1cc21-137">You can't do constructor dependency injection in a formatter class.</span></span> <span data-ttu-id="1cc21-138">例如，不能通过向构造函数添加记录器参数来获取记录器。</span><span class="sxs-lookup"><span data-stu-id="1cc21-138">For example, you can't get a logger by adding a logger parameter to the constructor.</span></span> <span data-ttu-id="1cc21-139">若要访问服务，必须使用传递到方法的上下文对象。</span><span class="sxs-lookup"><span data-stu-id="1cc21-139">To access services, you have to use the context object that gets passed in to your methods.</span></span> <span data-ttu-id="1cc21-140">[下面](#read-write)的代码示例展示了如何执行此操作。</span><span class="sxs-lookup"><span data-stu-id="1cc21-140">A code example [below](#read-write) shows how to do this.</span></span>

### <a name="override-canreadtypecanwritetype"></a><span data-ttu-id="1cc21-141">重写 CanReadType/CanWriteType</span><span class="sxs-lookup"><span data-stu-id="1cc21-141">Override CanReadType/CanWriteType</span></span>

<span data-ttu-id="1cc21-142">通过重写 `CanReadType` 或 `CanWriteType` 方法，指定可反序列化为或从其序列化的类型。</span><span class="sxs-lookup"><span data-stu-id="1cc21-142">Specify the type you can deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="1cc21-143">例如，可能只能从 `Contact` 类型创建 vCard 文本，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="1cc21-143">For example, you might only be able to create vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

<span data-ttu-id="1cc21-144">有关输入格式化程序示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)。</span><span class="sxs-lookup"><span data-stu-id="1cc21-144">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="1cc21-145">CanWriteResult 方法</span><span class="sxs-lookup"><span data-stu-id="1cc21-145">The CanWriteResult method</span></span>

<span data-ttu-id="1cc21-146">在某些情况下，必须重写 `CanWriteResult`，而不是 `CanWriteType`。</span><span class="sxs-lookup"><span data-stu-id="1cc21-146">In some scenarios you have to override `CanWriteResult` instead of `CanWriteType`.</span></span> <span data-ttu-id="1cc21-147">如果满足以下条件，则使用 `CanWriteResult`：</span><span class="sxs-lookup"><span data-stu-id="1cc21-147">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="1cc21-148">操作方法返回模型类。</span><span class="sxs-lookup"><span data-stu-id="1cc21-148">Your action method returns a model class.</span></span>
* <span data-ttu-id="1cc21-149">具有可能在运行时返回的派生类。</span><span class="sxs-lookup"><span data-stu-id="1cc21-149">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="1cc21-150">需要知道操作在运行时返回了哪个派生类。</span><span class="sxs-lookup"><span data-stu-id="1cc21-150">You need to know at runtime which derived class was returned by the action.</span></span>

<span data-ttu-id="1cc21-151">例如，假设操作方法签名返回 `Person` 类型，但它可能返回从 `Student` 派生的 `Instructor` 或 `Person` 类型。</span><span class="sxs-lookup"><span data-stu-id="1cc21-151">For example, suppose your action method signature returns a `Person` type, but it may return a `Student` or `Instructor` type that derives from `Person`.</span></span> <span data-ttu-id="1cc21-152">如果希望格式化程序仅处理 `Student` 对象，请检查提供给 [ 方法的上下文对象中的](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object)对象`CanWriteResult`类型。</span><span class="sxs-lookup"><span data-stu-id="1cc21-152">If you want your formatter to handle only `Student` objects, check the type of [Object](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="1cc21-153">请注意，当操作方法返回 `CanWriteResult` 时，不必使用 `IActionResult`；在这种情况下，`CanWriteType` 方法可接收运行时类型。</span><span class="sxs-lookup"><span data-stu-id="1cc21-153">Note that it's not necessary to use `CanWriteResult` when the action method returns `IActionResult`; in that case, the `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a><span data-ttu-id="1cc21-154">重写 ReadRequestBodyAsync/WriteResponseBodyAsync</span><span class="sxs-lookup"><span data-stu-id="1cc21-154">Override ReadRequestBodyAsync/WriteResponseBodyAsync</span></span>

<span data-ttu-id="1cc21-155">实际的反序列化或序列化工作在 `ReadRequestBodyAsync` 或 `WriteResponseBodyAsync` 中执行。</span><span class="sxs-lookup"><span data-stu-id="1cc21-155">You do the actual work of deserializing or serializing in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="1cc21-156">以下示例中突出显示的行展示了如何从依赖关系注入容器中获取服务（不能从构造函数参数中获取它们）。</span><span class="sxs-lookup"><span data-stu-id="1cc21-156">The highlighted lines in the following example show how to get services from the dependency injection container (you can't get them from constructor parameters).</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

<span data-ttu-id="1cc21-157">有关输入格式化程序示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)。</span><span class="sxs-lookup"><span data-stu-id="1cc21-157">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="1cc21-158">如何将 MVC 配置为使用自定义格式化程序</span><span class="sxs-lookup"><span data-stu-id="1cc21-158">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="1cc21-159">若要使用自定义格式化程序，请将格式化程序类的实例添加到 `InputFormatters` 或 `OutputFormatters` 集合。</span><span class="sxs-lookup"><span data-stu-id="1cc21-159">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

<span data-ttu-id="1cc21-160">按格式化程序的插入顺序对其进行计算。</span><span class="sxs-lookup"><span data-stu-id="1cc21-160">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="1cc21-161">第一个优先。</span><span class="sxs-lookup"><span data-stu-id="1cc21-161">The first one takes precedence.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1cc21-162">后续步骤</span><span class="sxs-lookup"><span data-stu-id="1cc21-162">Next steps</span></span>

* <span data-ttu-id="1cc21-163">[此文档的示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)，它可实现简单的 vCard 输入和输出格式化程序。</span><span class="sxs-lookup"><span data-stu-id="1cc21-163">[Sample app for this doc](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), which implements simple vCard input and output formatters.</span></span> <span data-ttu-id="1cc21-164">该应用可读取和写入与以下示例类似的 vCard：</span><span class="sxs-lookup"><span data-stu-id="1cc21-164">The apps reads and writes vCards that look like the following example:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
UID:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

<span data-ttu-id="1cc21-165">若要查看 vCard 输出，请运行该应用程序，并向 `http://localhost:63313/api/contacts/`（从 Visual Studio 运行时）或 `http://localhost:5000/api/contacts/`（从命令行运行时）发送具有 Accept 标头“text/vcard”的 Get 请求。</span><span class="sxs-lookup"><span data-stu-id="1cc21-165">To see vCard output, run the application and send a Get request with Accept header "text/vcard" to `http://localhost:63313/api/contacts/` (when running from Visual Studio) or `http://localhost:5000/api/contacts/` (when running from the command line).</span></span>

<span data-ttu-id="1cc21-166">若要将 vCard 添加到内存中联系人集合，请向相同的 URL 发送具有 Content-Type 标头“text/vcard”且正文中包含 vCard 文本的 Post 请求，格式化方式与上面的示例类似。</span><span class="sxs-lookup"><span data-stu-id="1cc21-166">To add a vCard to the in-memory collection of contacts, send a Post request to the same URL, with Content-Type header "text/vcard" and with vCard text in the body, formatted like the example above.</span></span>
