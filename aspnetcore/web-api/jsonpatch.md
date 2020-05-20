---
<span data-ttu-id="f21e7-101">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-102">'Blazor'</span></span>
- <span data-ttu-id="f21e7-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-103">'Identity'</span></span>
- <span data-ttu-id="f21e7-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-105">'Razor'</span></span>
- <span data-ttu-id="f21e7-106">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-106">'SignalR' uid:</span></span> 

---

# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="f21e7-107">ASP.NET Core Web API 中的 JSON 修补程序</span><span class="sxs-lookup"><span data-stu-id="f21e7-107">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="f21e7-108">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="f21e7-108">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f21e7-109">本文介绍如何处理 ASP.NET Core Web API 中的 JSON 修补程序请求。</span><span class="sxs-lookup"><span data-stu-id="f21e7-109">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="f21e7-110">包安装</span><span class="sxs-lookup"><span data-stu-id="f21e7-110">Package installation</span></span>

<span data-ttu-id="f21e7-111">若要在应用中启用 JSON 修补程序支持，请完成以下步骤：</span><span class="sxs-lookup"><span data-stu-id="f21e7-111">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="f21e7-112">安装 [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="f21e7-112">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="f21e7-113">更新项目的 `Startup.ConfigureServices` 要调用的方法 <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> 。</span><span class="sxs-lookup"><span data-stu-id="f21e7-113">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="f21e7-114">例如：</span><span class="sxs-lookup"><span data-stu-id="f21e7-114">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="f21e7-115">`AddNewtonsoftJson` 与 MVC 服务注册方法兼容：</span><span class="sxs-lookup"><span data-stu-id="f21e7-115">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="f21e7-116">JSON Patch、AddNewtonsoftJson 和 System.object</span><span class="sxs-lookup"><span data-stu-id="f21e7-116">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="f21e7-117">`AddNewtonsoftJson`替换 `System.Text.Json` 用于格式化**所有**JSON 内容的基于的输入和输出格式化程序。</span><span class="sxs-lookup"><span data-stu-id="f21e7-117">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="f21e7-118">若要使用添加对 JSON 修补程序的支持 `Newtonsoft.Json` ，而使其他格式化程序保持不变，请按如下所示更新项目的 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="f21e7-118">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="f21e7-119">前面的代码需要 `Microsoft.AspNetCore.Mvc.NewtonsoftJson` 包和以下 `using` 语句：</span><span class="sxs-lookup"><span data-stu-id="f21e7-119">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="f21e7-120">PATCH HTTP 请求方法</span><span class="sxs-lookup"><span data-stu-id="f21e7-120">PATCH HTTP request method</span></span>

<span data-ttu-id="f21e7-121">PUT 和 [PATCH](https://tools.ietf.org/html/rfc5789) 方法用于更新现有资源。</span><span class="sxs-lookup"><span data-stu-id="f21e7-121">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="f21e7-122">它们之间的区别是，PUT 会替换整个资源，而PATCH 仅指定更改。</span><span class="sxs-lookup"><span data-stu-id="f21e7-122">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="f21e7-123">JSON 修补程序</span><span class="sxs-lookup"><span data-stu-id="f21e7-123">JSON Patch</span></span>

<span data-ttu-id="f21e7-124">[JSON 修补程序](https://tools.ietf.org/html/rfc6902)是一种格式，用于指定要应用于资源的更新。</span><span class="sxs-lookup"><span data-stu-id="f21e7-124">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="f21e7-125">JSON 修补程序文档有一个\*\* 操作数组。</span><span class="sxs-lookup"><span data-stu-id="f21e7-125">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="f21e7-126">每个操作标识一种特定类型的更改。</span><span class="sxs-lookup"><span data-stu-id="f21e7-126">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="f21e7-127">此类更改的示例包括添加数组元素或替换属性值。</span><span class="sxs-lookup"><span data-stu-id="f21e7-127">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="f21e7-128">例如，以下 JSON 文档表示资源、资源的 JSON 修补文档和应用修补程序操作的结果。</span><span class="sxs-lookup"><span data-stu-id="f21e7-128">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="f21e7-129">资源示例</span><span class="sxs-lookup"><span data-stu-id="f21e7-129">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="f21e7-130">JSON 修补程序示例</span><span class="sxs-lookup"><span data-stu-id="f21e7-130">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="f21e7-131">在上述 JSON 中：</span><span class="sxs-lookup"><span data-stu-id="f21e7-131">In the preceding JSON:</span></span>

* <span data-ttu-id="f21e7-132">`op` 属性指示操作的类型。</span><span class="sxs-lookup"><span data-stu-id="f21e7-132">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="f21e7-133">`path` 属性指示要更新的元素。</span><span class="sxs-lookup"><span data-stu-id="f21e7-133">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="f21e7-134">`value` 属性提供新值。</span><span class="sxs-lookup"><span data-stu-id="f21e7-134">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="f21e7-135">修补程序之后的资源</span><span class="sxs-lookup"><span data-stu-id="f21e7-135">Resource after patch</span></span>

<span data-ttu-id="f21e7-136">下面是应用上述 JSON 修补程序文档后的资源：</span><span class="sxs-lookup"><span data-stu-id="f21e7-136">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="f21e7-137">通过向资源应用 JSON 修补程序文档所做的更改是原子的。</span><span class="sxs-lookup"><span data-stu-id="f21e7-137">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="f21e7-138">如果列表中的任何操作失败，则不会应用列表中的任何操作。</span><span class="sxs-lookup"><span data-stu-id="f21e7-138">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="f21e7-139">路径语法</span><span class="sxs-lookup"><span data-stu-id="f21e7-139">Path syntax</span></span>

<span data-ttu-id="f21e7-140">操作对象的[路径](https://tools.ietf.org/html/rfc6901)属性的级别之间有斜杠。</span><span class="sxs-lookup"><span data-stu-id="f21e7-140">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="f21e7-141">例如，`"/address/zipCode"` 。</span><span class="sxs-lookup"><span data-stu-id="f21e7-141">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="f21e7-142">使用从零开始的索引来指定数组元素。</span><span class="sxs-lookup"><span data-stu-id="f21e7-142">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="f21e7-143">`addresses` 数组的第一个元素将位于 `/addresses/0`。</span><span class="sxs-lookup"><span data-stu-id="f21e7-143">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="f21e7-144">若要到 `add` 数组的末尾，请使用连字符（ `-` ）而不是索引号： `/addresses/-` 。</span><span class="sxs-lookup"><span data-stu-id="f21e7-144">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="f21e7-145">操作</span><span class="sxs-lookup"><span data-stu-id="f21e7-145">Operations</span></span>

<span data-ttu-id="f21e7-146">下表显示了 [JSON 修补程序规范](https://tools.ietf.org/html/rfc6902)中定义的支持操作：</span><span class="sxs-lookup"><span data-stu-id="f21e7-146">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="f21e7-147">操作</span><span class="sxs-lookup"><span data-stu-id="f21e7-147">Operation</span></span>  | <span data-ttu-id="f21e7-148">说明</span><span class="sxs-lookup"><span data-stu-id="f21e7-148">Notes</span></span> |
|---
<span data-ttu-id="f21e7-149">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-149">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-150">'Blazor'</span></span>
- <span data-ttu-id="f21e7-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-151">'Identity'</span></span>
- <span data-ttu-id="f21e7-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-153">'Razor'</span></span>
- <span data-ttu-id="f21e7-154">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-155">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-155">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-156">'Blazor'</span></span>
- <span data-ttu-id="f21e7-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-157">'Identity'</span></span>
- <span data-ttu-id="f21e7-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-159">'Razor'</span></span>
- <span data-ttu-id="f21e7-160">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-161">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-161">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-162">'Blazor'</span></span>
- <span data-ttu-id="f21e7-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-163">'Identity'</span></span>
- <span data-ttu-id="f21e7-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-165">'Razor'</span></span>
- <span data-ttu-id="f21e7-166">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-166">'SignalR' uid:</span></span> 

<span data-ttu-id="f21e7-167">------|---
标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-167">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-168">'Blazor'</span></span>
- <span data-ttu-id="f21e7-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-169">'Identity'</span></span>
- <span data-ttu-id="f21e7-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-171">'Razor'</span></span>
- <span data-ttu-id="f21e7-172">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-173">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-173">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-174">'Blazor'</span></span>
- <span data-ttu-id="f21e7-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-175">'Identity'</span></span>
- <span data-ttu-id="f21e7-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-177">'Razor'</span></span>
- <span data-ttu-id="f21e7-178">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-179">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-179">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-180">'Blazor'</span></span>
- <span data-ttu-id="f21e7-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-181">'Identity'</span></span>
- <span data-ttu-id="f21e7-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-183">'Razor'</span></span>
- <span data-ttu-id="f21e7-184">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-185">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-185">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-186">'Blazor'</span></span>
- <span data-ttu-id="f21e7-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-187">'Identity'</span></span>
- <span data-ttu-id="f21e7-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-189">'Razor'</span></span>
- <span data-ttu-id="f21e7-190">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-191">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-191">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-192">'Blazor'</span></span>
- <span data-ttu-id="f21e7-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-193">'Identity'</span></span>
- <span data-ttu-id="f21e7-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-195">'Razor'</span></span>
- <span data-ttu-id="f21e7-196">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-197">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-197">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-198">'Blazor'</span></span>
- <span data-ttu-id="f21e7-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-199">'Identity'</span></span>
- <span data-ttu-id="f21e7-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-201">'Razor'</span></span>
- <span data-ttu-id="f21e7-202">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-203">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-203">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-204">'Blazor'</span></span>
- <span data-ttu-id="f21e7-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-205">'Identity'</span></span>
- <span data-ttu-id="f21e7-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-207">'Razor'</span></span>
- <span data-ttu-id="f21e7-208">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-209">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-209">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-210">'Blazor'</span></span>
- <span data-ttu-id="f21e7-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-211">'Identity'</span></span>
- <span data-ttu-id="f21e7-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-213">'Razor'</span></span>
- <span data-ttu-id="f21e7-214">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-215">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-215">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-216">'Blazor'</span></span>
- <span data-ttu-id="f21e7-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-217">'Identity'</span></span>
- <span data-ttu-id="f21e7-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-219">'Razor'</span></span>
- <span data-ttu-id="f21e7-220">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-221">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-221">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-222">'Blazor'</span></span>
- <span data-ttu-id="f21e7-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-223">'Identity'</span></span>
- <span data-ttu-id="f21e7-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-225">'Razor'</span></span>
- <span data-ttu-id="f21e7-226">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-227">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-227">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-228">'Blazor'</span></span>
- <span data-ttu-id="f21e7-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-229">'Identity'</span></span>
- <span data-ttu-id="f21e7-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-231">'Razor'</span></span>
- <span data-ttu-id="f21e7-232">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-233">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-233">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-234">'Blazor'</span></span>
- <span data-ttu-id="f21e7-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-235">'Identity'</span></span>
- <span data-ttu-id="f21e7-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-237">'Razor'</span></span>
- <span data-ttu-id="f21e7-238">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-239">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-239">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-240">'Blazor'</span></span>
- <span data-ttu-id="f21e7-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-241">'Identity'</span></span>
- <span data-ttu-id="f21e7-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-243">'Razor'</span></span>
- <span data-ttu-id="f21e7-244">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-245">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-245">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-246">'Blazor'</span></span>
- <span data-ttu-id="f21e7-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-247">'Identity'</span></span>
- <span data-ttu-id="f21e7-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-249">'Razor'</span></span>
- <span data-ttu-id="f21e7-250">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-250">'SignalR' uid:</span></span> 

<span data-ttu-id="f21e7-251">----------------| |`add`     |添加属性或数组元素。</span><span class="sxs-lookup"><span data-stu-id="f21e7-251">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="f21e7-252">对于 "现有属性：设置值"。 ||`remove`  |删除属性或数组元素。</span><span class="sxs-lookup"><span data-stu-id="f21e7-252">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="f21e7-253">| |`replace` |与 `remove` 后跟 `add` 同一位置。</span><span class="sxs-lookup"><span data-stu-id="f21e7-253">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="f21e7-254">| |`move`    |与 `remove` from 源相同，后面是 `add` 使用源中的值。</span><span class="sxs-lookup"><span data-stu-id="f21e7-254">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="f21e7-255">| |`copy`    |与 `add` 通过源中的值与目标相同。</span><span class="sxs-lookup"><span data-stu-id="f21e7-255">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="f21e7-256">| |`test`    |如果值为，则返回成功状态代码 `path` `value` 。 |</span><span class="sxs-lookup"><span data-stu-id="f21e7-256">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="f21e7-257">ASP.NET Core 中的 JSON 修补程序</span><span class="sxs-lookup"><span data-stu-id="f21e7-257">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="f21e7-258">[Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet 包中提供了 JSON 修补程序的 ASP.NET Core 实现。</span><span class="sxs-lookup"><span data-stu-id="f21e7-258">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="f21e7-259">操作方法代码</span><span class="sxs-lookup"><span data-stu-id="f21e7-259">Action method code</span></span>

<span data-ttu-id="f21e7-260">在 API 控制器中，JSON 修补程序的操作方法：</span><span class="sxs-lookup"><span data-stu-id="f21e7-260">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="f21e7-261">使用 `HttpPatch` 属性进行批注。</span><span class="sxs-lookup"><span data-stu-id="f21e7-261">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="f21e7-262">接受 `JsonPatchDocument<T>`，通常带有 `[FromBody]`。</span><span class="sxs-lookup"><span data-stu-id="f21e7-262">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="f21e7-263">在修补程序文档上调用 `ApplyTo` 以应用更改。</span><span class="sxs-lookup"><span data-stu-id="f21e7-263">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="f21e7-264">下面的示例说明：</span><span class="sxs-lookup"><span data-stu-id="f21e7-264">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="f21e7-265">此示例应用程序中的代码可用于以下 `Customer` 模型：</span><span class="sxs-lookup"><span data-stu-id="f21e7-265">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="f21e7-266">示例操作方法：</span><span class="sxs-lookup"><span data-stu-id="f21e7-266">The sample action method:</span></span>

* <span data-ttu-id="f21e7-267">构造一个 `Customer`。</span><span class="sxs-lookup"><span data-stu-id="f21e7-267">Constructs a `Customer`.</span></span>
* <span data-ttu-id="f21e7-268">应用修补程序。</span><span class="sxs-lookup"><span data-stu-id="f21e7-268">Applies the patch.</span></span>
* <span data-ttu-id="f21e7-269">在响应的正文中返回结果。</span><span class="sxs-lookup"><span data-stu-id="f21e7-269">Returns the result in the body of the response.</span></span>

<span data-ttu-id="f21e7-270">在实际应用中，该代码将从存储（如数据库）中检索数据并在应用修补程序后更新数据库。</span><span class="sxs-lookup"><span data-stu-id="f21e7-270">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="f21e7-271">模型状态</span><span class="sxs-lookup"><span data-stu-id="f21e7-271">Model state</span></span>

<span data-ttu-id="f21e7-272">上述操作方法示例调用将模型状态用作其参数之一的 `ApplyTo` 的重载。</span><span class="sxs-lookup"><span data-stu-id="f21e7-272">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="f21e7-273">使用此选项，可以在响应中收到错误消息。</span><span class="sxs-lookup"><span data-stu-id="f21e7-273">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="f21e7-274">以下示例显示了 `test` 操作的“400 错误请求”响应的正文：</span><span class="sxs-lookup"><span data-stu-id="f21e7-274">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="f21e7-275">动态对象</span><span class="sxs-lookup"><span data-stu-id="f21e7-275">Dynamic objects</span></span>

<span data-ttu-id="f21e7-276">以下操作方法示例演示如何将修补程序应用于动态对象：</span><span class="sxs-lookup"><span data-stu-id="f21e7-276">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="f21e7-277">添加操作</span><span class="sxs-lookup"><span data-stu-id="f21e7-277">The add operation</span></span>

* <span data-ttu-id="f21e7-278">如果 `path` 指向数组元素：将新元素插入到 `path` 指定的元素之前。</span><span class="sxs-lookup"><span data-stu-id="f21e7-278">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="f21e7-279">如果 `path` 指向属性：设置属性值。</span><span class="sxs-lookup"><span data-stu-id="f21e7-279">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="f21e7-280">如果 `path` 指向不存在的位置：</span><span class="sxs-lookup"><span data-stu-id="f21e7-280">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="f21e7-281">如果要修补的资源是一个动态对象：添加属性。</span><span class="sxs-lookup"><span data-stu-id="f21e7-281">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="f21e7-282">如果要修补的资源是一个静态对象：请求失败。</span><span class="sxs-lookup"><span data-stu-id="f21e7-282">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="f21e7-283">以下示例修补程序文档设置 `CustomerName` 的值，并将 `Order` 对象添加到 `Orders` 数组末尾。</span><span class="sxs-lookup"><span data-stu-id="f21e7-283">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="f21e7-284">删除操作</span><span class="sxs-lookup"><span data-stu-id="f21e7-284">The remove operation</span></span>

* <span data-ttu-id="f21e7-285">如果 `path` 指向数组元素：删除元素。</span><span class="sxs-lookup"><span data-stu-id="f21e7-285">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="f21e7-286">如果 `path` 指向属性：</span><span class="sxs-lookup"><span data-stu-id="f21e7-286">If `path` points to a property:</span></span>
  * <span data-ttu-id="f21e7-287">如果要修补的资源是一个动态对象：删除属性。</span><span class="sxs-lookup"><span data-stu-id="f21e7-287">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="f21e7-288">如果要修补的资源是一个静态对象：</span><span class="sxs-lookup"><span data-stu-id="f21e7-288">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="f21e7-289">如果属性可以为 Null：将其设置为 null。</span><span class="sxs-lookup"><span data-stu-id="f21e7-289">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="f21e7-290">如果属性不可以为 Null，将其设置为 `default<T>`。</span><span class="sxs-lookup"><span data-stu-id="f21e7-290">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="f21e7-291">下面的示例将修补文档集设置 `CustomerName` 为 null 并删除 `Orders[0]` ：</span><span class="sxs-lookup"><span data-stu-id="f21e7-291">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="f21e7-292">替换操作</span><span class="sxs-lookup"><span data-stu-id="f21e7-292">The replace operation</span></span>

<span data-ttu-id="f21e7-293">此操作在功能上与后跟 `add` 的 `remove` 相同。</span><span class="sxs-lookup"><span data-stu-id="f21e7-293">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="f21e7-294">以下示例修补文档设置的值 `CustomerName` ，并将替换 `Orders[0]` 为新的 `Order` 对象：</span><span class="sxs-lookup"><span data-stu-id="f21e7-294">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="f21e7-295">移动操作</span><span class="sxs-lookup"><span data-stu-id="f21e7-295">The move operation</span></span>

* <span data-ttu-id="f21e7-296">如果 `path` 指向数组元素：将 `from` 元素复制到 `path` 元素的位置，然后对 `from` 元素运行 `remove` 操作。</span><span class="sxs-lookup"><span data-stu-id="f21e7-296">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="f21e7-297">如果 `path` 指向属性：将 `from` 属性的值复制到 `path` 属性，然后对 `from` 属性运行 `remove` 操作。</span><span class="sxs-lookup"><span data-stu-id="f21e7-297">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="f21e7-298">如果 `path` 指向不存在的属性：</span><span class="sxs-lookup"><span data-stu-id="f21e7-298">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="f21e7-299">如果要修补的资源是一个静态对象：请求失败。</span><span class="sxs-lookup"><span data-stu-id="f21e7-299">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="f21e7-300">如果要修补的资源是一个动态对象：将 `from` 属性复制到 `path` 指示的位置，然后对 `from` 属性运行 `remove` 操作。</span><span class="sxs-lookup"><span data-stu-id="f21e7-300">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="f21e7-301">以下示例修补程序文档：</span><span class="sxs-lookup"><span data-stu-id="f21e7-301">The following sample patch document:</span></span>

* <span data-ttu-id="f21e7-302">将 `Orders[0].OrderName` 的值复制到 `CustomerName`。</span><span class="sxs-lookup"><span data-stu-id="f21e7-302">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="f21e7-303">将 `Orders[0].OrderName` 设置为 null。</span><span class="sxs-lookup"><span data-stu-id="f21e7-303">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="f21e7-304">将 `Orders[1]` 移动到 `Orders[0]` 之前。</span><span class="sxs-lookup"><span data-stu-id="f21e7-304">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="f21e7-305">复制操作</span><span class="sxs-lookup"><span data-stu-id="f21e7-305">The copy operation</span></span>

<span data-ttu-id="f21e7-306">此操作在功能上与不包含最后 `remove` 步骤的 `move` 操作相同。</span><span class="sxs-lookup"><span data-stu-id="f21e7-306">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="f21e7-307">以下示例修补程序文档：</span><span class="sxs-lookup"><span data-stu-id="f21e7-307">The following sample patch document:</span></span>

* <span data-ttu-id="f21e7-308">将 `Orders[0].OrderName` 的值复制到 `CustomerName`。</span><span class="sxs-lookup"><span data-stu-id="f21e7-308">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="f21e7-309">将 `Orders[1]` 的副本插入到 `Orders[0]` 之前。</span><span class="sxs-lookup"><span data-stu-id="f21e7-309">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="f21e7-310">测试操作</span><span class="sxs-lookup"><span data-stu-id="f21e7-310">The test operation</span></span>

<span data-ttu-id="f21e7-311">如果 `path` 指示的位置处的值与 `value` 中提供的值不同，则请求会失败。</span><span class="sxs-lookup"><span data-stu-id="f21e7-311">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="f21e7-312">在这种情况下，整个 PATCH 请求会失败，即使修补程序文档中的所有其他操作均成功也是如此。</span><span class="sxs-lookup"><span data-stu-id="f21e7-312">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="f21e7-313">`test` 操作通常用于在发生并发冲突时阻止更新。</span><span class="sxs-lookup"><span data-stu-id="f21e7-313">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="f21e7-314">如果 `CustomerName` 的初始值是“John”，则以下示例修补程序文档不起任何作用，因为测试失败：</span><span class="sxs-lookup"><span data-stu-id="f21e7-314">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="f21e7-315">获取代码</span><span class="sxs-lookup"><span data-stu-id="f21e7-315">Get the code</span></span>

<span data-ttu-id="f21e7-316">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples)。</span><span class="sxs-lookup"><span data-stu-id="f21e7-316">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="f21e7-317">（[下载方法](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="f21e7-317">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="f21e7-318">若要测试此示例，请使用以下设置运行应用并发送 HTTP 请求：</span><span class="sxs-lookup"><span data-stu-id="f21e7-318">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="f21e7-319">URL：`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="f21e7-319">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="f21e7-320">HTTP 方法：`PATCH`</span><span class="sxs-lookup"><span data-stu-id="f21e7-320">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="f21e7-321">标头：`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="f21e7-321">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="f21e7-322">Body：从*json*项目文件夹复制并粘贴一个 json 修补文档示例。</span><span class="sxs-lookup"><span data-stu-id="f21e7-322">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f21e7-323">其他资源</span><span class="sxs-lookup"><span data-stu-id="f21e7-323">Additional resources</span></span>

* [<span data-ttu-id="f21e7-324">IETF RFC 5789 PATCH 方法规范</span><span class="sxs-lookup"><span data-stu-id="f21e7-324">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="f21e7-325">IETF RFC 6902 JSON 修补程序规范</span><span class="sxs-lookup"><span data-stu-id="f21e7-325">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="f21e7-326">IETF RFC 6901 JSON 修补程序路径格式规范</span><span class="sxs-lookup"><span data-stu-id="f21e7-326">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="f21e7-327">[JSON 路径文档](https://jsonpatch.com/)。</span><span class="sxs-lookup"><span data-stu-id="f21e7-327">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="f21e7-328">包括指向用于创建 JSON 修补程序文档的资源的链接。</span><span class="sxs-lookup"><span data-stu-id="f21e7-328">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="f21e7-329">ASP.NET Core JSON 修补程序源代码</span><span class="sxs-lookup"><span data-stu-id="f21e7-329">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f21e7-330">本文介绍如何处理 ASP.NET Core Web API 中的 JSON 修补程序请求。</span><span class="sxs-lookup"><span data-stu-id="f21e7-330">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="f21e7-331">PATCH HTTP 请求方法</span><span class="sxs-lookup"><span data-stu-id="f21e7-331">PATCH HTTP request method</span></span>

<span data-ttu-id="f21e7-332">PUT 和 [PATCH](https://tools.ietf.org/html/rfc5789) 方法用于更新现有资源。</span><span class="sxs-lookup"><span data-stu-id="f21e7-332">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="f21e7-333">它们之间的区别是，PUT 会替换整个资源，而PATCH 仅指定更改。</span><span class="sxs-lookup"><span data-stu-id="f21e7-333">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="f21e7-334">JSON 修补程序</span><span class="sxs-lookup"><span data-stu-id="f21e7-334">JSON Patch</span></span>

<span data-ttu-id="f21e7-335">[JSON 修补程序](https://tools.ietf.org/html/rfc6902)是一种格式，用于指定要应用于资源的更新。</span><span class="sxs-lookup"><span data-stu-id="f21e7-335">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="f21e7-336">JSON 修补程序文档有一个\*\* 操作数组。</span><span class="sxs-lookup"><span data-stu-id="f21e7-336">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="f21e7-337">每个操作标识特定类型的更改，例如添加数组元素或替换属性值。</span><span class="sxs-lookup"><span data-stu-id="f21e7-337">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="f21e7-338">例如，以下 JSON 文档表示资源、资源的 JSON 修补程序文档和应用修补程序操作的结果。</span><span class="sxs-lookup"><span data-stu-id="f21e7-338">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="f21e7-339">资源示例</span><span class="sxs-lookup"><span data-stu-id="f21e7-339">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="f21e7-340">JSON 修补程序示例</span><span class="sxs-lookup"><span data-stu-id="f21e7-340">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="f21e7-341">在上述 JSON 中：</span><span class="sxs-lookup"><span data-stu-id="f21e7-341">In the preceding JSON:</span></span>

* <span data-ttu-id="f21e7-342">`op` 属性指示操作的类型。</span><span class="sxs-lookup"><span data-stu-id="f21e7-342">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="f21e7-343">`path` 属性指示要更新的元素。</span><span class="sxs-lookup"><span data-stu-id="f21e7-343">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="f21e7-344">`value` 属性提供新值。</span><span class="sxs-lookup"><span data-stu-id="f21e7-344">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="f21e7-345">修补程序之后的资源</span><span class="sxs-lookup"><span data-stu-id="f21e7-345">Resource after patch</span></span>

<span data-ttu-id="f21e7-346">下面是应用上述 JSON 修补程序文档后的资源：</span><span class="sxs-lookup"><span data-stu-id="f21e7-346">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="f21e7-347">通过将 JSON 修补程序文档应用于资源所做的更改是原子操作：如果列表中的任何操作失败，则不会应用列表中的任何操作。</span><span class="sxs-lookup"><span data-stu-id="f21e7-347">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="f21e7-348">路径语法</span><span class="sxs-lookup"><span data-stu-id="f21e7-348">Path syntax</span></span>

<span data-ttu-id="f21e7-349">操作对象的[路径](https://tools.ietf.org/html/rfc6901)属性的级别之间有斜杠。</span><span class="sxs-lookup"><span data-stu-id="f21e7-349">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="f21e7-350">例如，`"/address/zipCode"` 。</span><span class="sxs-lookup"><span data-stu-id="f21e7-350">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="f21e7-351">使用从零开始的索引来指定数组元素。</span><span class="sxs-lookup"><span data-stu-id="f21e7-351">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="f21e7-352">`addresses` 数组的第一个元素将位于 `/addresses/0`。</span><span class="sxs-lookup"><span data-stu-id="f21e7-352">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="f21e7-353">若要将 `add` 置于数组末尾，请使用连字符 (-)，而不是索引号：`/addresses/-`。</span><span class="sxs-lookup"><span data-stu-id="f21e7-353">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="f21e7-354">操作</span><span class="sxs-lookup"><span data-stu-id="f21e7-354">Operations</span></span>

<span data-ttu-id="f21e7-355">下表显示了 [JSON 修补程序规范](https://tools.ietf.org/html/rfc6902)中定义的支持操作：</span><span class="sxs-lookup"><span data-stu-id="f21e7-355">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="f21e7-356">操作</span><span class="sxs-lookup"><span data-stu-id="f21e7-356">Operation</span></span>  | <span data-ttu-id="f21e7-357">说明</span><span class="sxs-lookup"><span data-stu-id="f21e7-357">Notes</span></span> |
|---
<span data-ttu-id="f21e7-358">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-358">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-359">'Blazor'</span></span>
- <span data-ttu-id="f21e7-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-360">'Identity'</span></span>
- <span data-ttu-id="f21e7-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-362">'Razor'</span></span>
- <span data-ttu-id="f21e7-363">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-364">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-364">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-365">'Blazor'</span></span>
- <span data-ttu-id="f21e7-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-366">'Identity'</span></span>
- <span data-ttu-id="f21e7-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-368">'Razor'</span></span>
- <span data-ttu-id="f21e7-369">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-370">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-370">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-371">'Blazor'</span></span>
- <span data-ttu-id="f21e7-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-372">'Identity'</span></span>
- <span data-ttu-id="f21e7-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-374">'Razor'</span></span>
- <span data-ttu-id="f21e7-375">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-375">'SignalR' uid:</span></span> 

<span data-ttu-id="f21e7-376">------|---
标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-376">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-377">'Blazor'</span></span>
- <span data-ttu-id="f21e7-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-378">'Identity'</span></span>
- <span data-ttu-id="f21e7-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-380">'Razor'</span></span>
- <span data-ttu-id="f21e7-381">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-382">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-382">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-383">'Blazor'</span></span>
- <span data-ttu-id="f21e7-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-384">'Identity'</span></span>
- <span data-ttu-id="f21e7-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-386">'Razor'</span></span>
- <span data-ttu-id="f21e7-387">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-388">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-388">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-389">'Blazor'</span></span>
- <span data-ttu-id="f21e7-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-390">'Identity'</span></span>
- <span data-ttu-id="f21e7-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-392">'Razor'</span></span>
- <span data-ttu-id="f21e7-393">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-394">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-394">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-395">'Blazor'</span></span>
- <span data-ttu-id="f21e7-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-396">'Identity'</span></span>
- <span data-ttu-id="f21e7-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-398">'Razor'</span></span>
- <span data-ttu-id="f21e7-399">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-400">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-400">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-401">'Blazor'</span></span>
- <span data-ttu-id="f21e7-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-402">'Identity'</span></span>
- <span data-ttu-id="f21e7-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-404">'Razor'</span></span>
- <span data-ttu-id="f21e7-405">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-406">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-406">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-407">'Blazor'</span></span>
- <span data-ttu-id="f21e7-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-408">'Identity'</span></span>
- <span data-ttu-id="f21e7-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-410">'Razor'</span></span>
- <span data-ttu-id="f21e7-411">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-412">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-412">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-413">'Blazor'</span></span>
- <span data-ttu-id="f21e7-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-414">'Identity'</span></span>
- <span data-ttu-id="f21e7-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-416">'Razor'</span></span>
- <span data-ttu-id="f21e7-417">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-418">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-418">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-419">'Blazor'</span></span>
- <span data-ttu-id="f21e7-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-420">'Identity'</span></span>
- <span data-ttu-id="f21e7-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-422">'Razor'</span></span>
- <span data-ttu-id="f21e7-423">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-424">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-424">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-425">'Blazor'</span></span>
- <span data-ttu-id="f21e7-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-426">'Identity'</span></span>
- <span data-ttu-id="f21e7-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-428">'Razor'</span></span>
- <span data-ttu-id="f21e7-429">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-430">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-430">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-431">'Blazor'</span></span>
- <span data-ttu-id="f21e7-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-432">'Identity'</span></span>
- <span data-ttu-id="f21e7-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-434">'Razor'</span></span>
- <span data-ttu-id="f21e7-435">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-435">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-436">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-436">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-437">'Blazor'</span></span>
- <span data-ttu-id="f21e7-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-438">'Identity'</span></span>
- <span data-ttu-id="f21e7-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-440">'Razor'</span></span>
- <span data-ttu-id="f21e7-441">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-442">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-442">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-443">'Blazor'</span></span>
- <span data-ttu-id="f21e7-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-444">'Identity'</span></span>
- <span data-ttu-id="f21e7-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-446">'Razor'</span></span>
- <span data-ttu-id="f21e7-447">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-448">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-448">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-449">'Blazor'</span></span>
- <span data-ttu-id="f21e7-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-450">'Identity'</span></span>
- <span data-ttu-id="f21e7-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-452">'Razor'</span></span>
- <span data-ttu-id="f21e7-453">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-453">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f21e7-454">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f21e7-454">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f21e7-455">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-455">'Blazor'</span></span>
- <span data-ttu-id="f21e7-456">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f21e7-456">'Identity'</span></span>
- <span data-ttu-id="f21e7-457">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f21e7-457">'Let's Encrypt'</span></span>
- <span data-ttu-id="f21e7-458">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f21e7-458">'Razor'</span></span>
- <span data-ttu-id="f21e7-459">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="f21e7-459">'SignalR' uid:</span></span> 

<span data-ttu-id="f21e7-460">----------------| |`add`     |添加属性或数组元素。</span><span class="sxs-lookup"><span data-stu-id="f21e7-460">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="f21e7-461">对于 "现有属性：设置值"。 ||`remove`  |删除属性或数组元素。</span><span class="sxs-lookup"><span data-stu-id="f21e7-461">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="f21e7-462">| |`replace` |与 `remove` 后跟 `add` 同一位置。</span><span class="sxs-lookup"><span data-stu-id="f21e7-462">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="f21e7-463">| |`move`    |与 `remove` from 源相同，后面是 `add` 使用源中的值。</span><span class="sxs-lookup"><span data-stu-id="f21e7-463">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="f21e7-464">| |`copy`    |与 `add` 通过源中的值与目标相同。</span><span class="sxs-lookup"><span data-stu-id="f21e7-464">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="f21e7-465">| |`test`    |如果值为，则返回成功状态代码 `path` `value` 。 |</span><span class="sxs-lookup"><span data-stu-id="f21e7-465">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="f21e7-466">ASP.NET Core 中的 JSON 修补程序</span><span class="sxs-lookup"><span data-stu-id="f21e7-466">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="f21e7-467">[Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet 包中提供了 JSON 修补程序的 ASP.NET Core 实现。</span><span class="sxs-lookup"><span data-stu-id="f21e7-467">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="f21e7-468">此包包含在[AspnetCore](xref:fundamentals/metapackage-app)元包中。</span><span class="sxs-lookup"><span data-stu-id="f21e7-468">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="f21e7-469">操作方法代码</span><span class="sxs-lookup"><span data-stu-id="f21e7-469">Action method code</span></span>

<span data-ttu-id="f21e7-470">在 API 控制器中，JSON 修补程序的操作方法：</span><span class="sxs-lookup"><span data-stu-id="f21e7-470">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="f21e7-471">使用 `HttpPatch` 属性进行批注。</span><span class="sxs-lookup"><span data-stu-id="f21e7-471">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="f21e7-472">接受 `JsonPatchDocument<T>`，通常带有 `[FromBody]`。</span><span class="sxs-lookup"><span data-stu-id="f21e7-472">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="f21e7-473">在修补程序文档上调用 `ApplyTo` 以应用更改。</span><span class="sxs-lookup"><span data-stu-id="f21e7-473">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="f21e7-474">下面的示例说明：</span><span class="sxs-lookup"><span data-stu-id="f21e7-474">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="f21e7-475">示例应用中的此代码适用于以下 `Customer` 模型。</span><span class="sxs-lookup"><span data-stu-id="f21e7-475">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="f21e7-476">示例操作方法：</span><span class="sxs-lookup"><span data-stu-id="f21e7-476">The sample action method:</span></span>

* <span data-ttu-id="f21e7-477">构造一个 `Customer`。</span><span class="sxs-lookup"><span data-stu-id="f21e7-477">Constructs a `Customer`.</span></span>
* <span data-ttu-id="f21e7-478">应用修补程序。</span><span class="sxs-lookup"><span data-stu-id="f21e7-478">Applies the patch.</span></span>
* <span data-ttu-id="f21e7-479">在响应的正文中返回结果。</span><span class="sxs-lookup"><span data-stu-id="f21e7-479">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="f21e7-480">在实际应用中，该代码将从存储（如数据库）中检索数据并在应用修补程序后更新数据库。</span><span class="sxs-lookup"><span data-stu-id="f21e7-480">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="f21e7-481">模型状态</span><span class="sxs-lookup"><span data-stu-id="f21e7-481">Model state</span></span>

<span data-ttu-id="f21e7-482">上述操作方法示例调用将模型状态用作其参数之一的 `ApplyTo` 的重载。</span><span class="sxs-lookup"><span data-stu-id="f21e7-482">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="f21e7-483">使用此选项，可以在响应中收到错误消息。</span><span class="sxs-lookup"><span data-stu-id="f21e7-483">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="f21e7-484">以下示例显示了 `test` 操作的“400 错误请求”响应的正文：</span><span class="sxs-lookup"><span data-stu-id="f21e7-484">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="f21e7-485">动态对象</span><span class="sxs-lookup"><span data-stu-id="f21e7-485">Dynamic objects</span></span>

<span data-ttu-id="f21e7-486">以下操作方法示例演示如何将修补程序应用于动态对象。</span><span class="sxs-lookup"><span data-stu-id="f21e7-486">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="f21e7-487">添加操作</span><span class="sxs-lookup"><span data-stu-id="f21e7-487">The add operation</span></span>

* <span data-ttu-id="f21e7-488">如果 `path` 指向数组元素：将新元素插入到 `path` 指定的元素之前。</span><span class="sxs-lookup"><span data-stu-id="f21e7-488">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="f21e7-489">如果 `path` 指向属性：设置属性值。</span><span class="sxs-lookup"><span data-stu-id="f21e7-489">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="f21e7-490">如果 `path` 指向不存在的位置：</span><span class="sxs-lookup"><span data-stu-id="f21e7-490">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="f21e7-491">如果要修补的资源是一个动态对象：添加属性。</span><span class="sxs-lookup"><span data-stu-id="f21e7-491">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="f21e7-492">如果要修补的资源是一个静态对象：请求失败。</span><span class="sxs-lookup"><span data-stu-id="f21e7-492">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="f21e7-493">以下示例修补程序文档设置 `CustomerName` 的值，并将 `Order` 对象添加到 `Orders` 数组末尾。</span><span class="sxs-lookup"><span data-stu-id="f21e7-493">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="f21e7-494">删除操作</span><span class="sxs-lookup"><span data-stu-id="f21e7-494">The remove operation</span></span>

* <span data-ttu-id="f21e7-495">如果 `path` 指向数组元素：删除元素。</span><span class="sxs-lookup"><span data-stu-id="f21e7-495">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="f21e7-496">如果 `path` 指向属性：</span><span class="sxs-lookup"><span data-stu-id="f21e7-496">If `path` points to a property:</span></span>
  * <span data-ttu-id="f21e7-497">如果要修补的资源是一个动态对象：删除属性。</span><span class="sxs-lookup"><span data-stu-id="f21e7-497">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="f21e7-498">如果要修补的资源是一个静态对象：</span><span class="sxs-lookup"><span data-stu-id="f21e7-498">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="f21e7-499">如果属性可以为 Null：将其设置为 null。</span><span class="sxs-lookup"><span data-stu-id="f21e7-499">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="f21e7-500">如果属性不可以为 Null，将其设置为 `default<T>`。</span><span class="sxs-lookup"><span data-stu-id="f21e7-500">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="f21e7-501">以下示例修补程序文档将 `CustomerName` 设置为 null 并删除 `Orders[0]`。</span><span class="sxs-lookup"><span data-stu-id="f21e7-501">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="f21e7-502">替换操作</span><span class="sxs-lookup"><span data-stu-id="f21e7-502">The replace operation</span></span>

<span data-ttu-id="f21e7-503">此操作在功能上与后跟 `add` 的 `remove` 相同。</span><span class="sxs-lookup"><span data-stu-id="f21e7-503">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="f21e7-504">以下示例修补程序文档设置 `CustomerName` 的值，并将 `Orders[0]` 替换为新的 `Order` 对象。</span><span class="sxs-lookup"><span data-stu-id="f21e7-504">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="f21e7-505">移动操作</span><span class="sxs-lookup"><span data-stu-id="f21e7-505">The move operation</span></span>

* <span data-ttu-id="f21e7-506">如果 `path` 指向数组元素：将 `from` 元素复制到 `path` 元素的位置，然后对 `from` 元素运行 `remove` 操作。</span><span class="sxs-lookup"><span data-stu-id="f21e7-506">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="f21e7-507">如果 `path` 指向属性：将 `from` 属性的值复制到 `path` 属性，然后对 `from` 属性运行 `remove` 操作。</span><span class="sxs-lookup"><span data-stu-id="f21e7-507">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="f21e7-508">如果 `path` 指向不存在的属性：</span><span class="sxs-lookup"><span data-stu-id="f21e7-508">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="f21e7-509">如果要修补的资源是一个静态对象：请求失败。</span><span class="sxs-lookup"><span data-stu-id="f21e7-509">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="f21e7-510">如果要修补的资源是一个动态对象：将 `from` 属性复制到 `path` 指示的位置，然后对 `from` 属性运行 `remove` 操作。</span><span class="sxs-lookup"><span data-stu-id="f21e7-510">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="f21e7-511">以下示例修补程序文档：</span><span class="sxs-lookup"><span data-stu-id="f21e7-511">The following sample patch document:</span></span>

* <span data-ttu-id="f21e7-512">将 `Orders[0].OrderName` 的值复制到 `CustomerName`。</span><span class="sxs-lookup"><span data-stu-id="f21e7-512">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="f21e7-513">将 `Orders[0].OrderName` 设置为 null。</span><span class="sxs-lookup"><span data-stu-id="f21e7-513">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="f21e7-514">将 `Orders[1]` 移动到 `Orders[0]` 之前。</span><span class="sxs-lookup"><span data-stu-id="f21e7-514">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="f21e7-515">复制操作</span><span class="sxs-lookup"><span data-stu-id="f21e7-515">The copy operation</span></span>

<span data-ttu-id="f21e7-516">此操作在功能上与不包含最后 `remove` 步骤的 `move` 操作相同。</span><span class="sxs-lookup"><span data-stu-id="f21e7-516">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="f21e7-517">以下示例修补程序文档：</span><span class="sxs-lookup"><span data-stu-id="f21e7-517">The following sample patch document:</span></span>

* <span data-ttu-id="f21e7-518">将 `Orders[0].OrderName` 的值复制到 `CustomerName`。</span><span class="sxs-lookup"><span data-stu-id="f21e7-518">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="f21e7-519">将 `Orders[1]` 的副本插入到 `Orders[0]` 之前。</span><span class="sxs-lookup"><span data-stu-id="f21e7-519">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="f21e7-520">测试操作</span><span class="sxs-lookup"><span data-stu-id="f21e7-520">The test operation</span></span>

<span data-ttu-id="f21e7-521">如果 `path` 指示的位置处的值与 `value` 中提供的值不同，则请求会失败。</span><span class="sxs-lookup"><span data-stu-id="f21e7-521">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="f21e7-522">在这种情况下，整个 PATCH 请求会失败，即使修补程序文档中的所有其他操作均成功也是如此。</span><span class="sxs-lookup"><span data-stu-id="f21e7-522">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="f21e7-523">`test` 操作通常用于在发生并发冲突时阻止更新。</span><span class="sxs-lookup"><span data-stu-id="f21e7-523">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="f21e7-524">如果 `CustomerName` 的初始值是“John”，则以下示例修补程序文档不起任何作用，因为测试失败：</span><span class="sxs-lookup"><span data-stu-id="f21e7-524">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="f21e7-525">获取代码</span><span class="sxs-lookup"><span data-stu-id="f21e7-525">Get the code</span></span>

<span data-ttu-id="f21e7-526">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)。</span><span class="sxs-lookup"><span data-stu-id="f21e7-526">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="f21e7-527">（[下载方法](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="f21e7-527">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="f21e7-528">若要测试此示例，请使用以下设置运行应用并发送 HTTP 请求：</span><span class="sxs-lookup"><span data-stu-id="f21e7-528">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="f21e7-529">URL：`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="f21e7-529">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="f21e7-530">HTTP 方法：`PATCH`</span><span class="sxs-lookup"><span data-stu-id="f21e7-530">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="f21e7-531">标头：`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="f21e7-531">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="f21e7-532">Body：从*json*项目文件夹复制并粘贴一个 json 修补文档示例。</span><span class="sxs-lookup"><span data-stu-id="f21e7-532">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f21e7-533">其他资源</span><span class="sxs-lookup"><span data-stu-id="f21e7-533">Additional resources</span></span>

* [<span data-ttu-id="f21e7-534">IETF RFC 5789 PATCH 方法规范</span><span class="sxs-lookup"><span data-stu-id="f21e7-534">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="f21e7-535">IETF RFC 6902 JSON 修补程序规范</span><span class="sxs-lookup"><span data-stu-id="f21e7-535">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="f21e7-536">IETF RFC 6901 JSON 修补程序路径格式规范</span><span class="sxs-lookup"><span data-stu-id="f21e7-536">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="f21e7-537">[JSON 路径文档](https://jsonpatch.com/)。</span><span class="sxs-lookup"><span data-stu-id="f21e7-537">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="f21e7-538">包括指向用于创建 JSON 修补程序文档的资源的链接。</span><span class="sxs-lookup"><span data-stu-id="f21e7-538">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="f21e7-539">ASP.NET Core JSON 修补程序源代码</span><span class="sxs-lookup"><span data-stu-id="f21e7-539">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
