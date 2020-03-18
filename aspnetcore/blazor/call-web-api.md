---
title: 从 ASP.NET Core Blazor 调用 Web API
author: guardrex
description: 了解如何使用 JSON 帮助程序从 Blazor 应用程序调用 Web API，包括进行跨域资源共享 (CORS) 请求。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: e6996f0e6731b05038d0a9329152b8afd5f6796d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647730"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a><span data-ttu-id="66ebd-103">从 ASP.NET Core Blazor 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="66ebd-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="66ebd-104">作者：[Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27) 和 [Juan De la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="66ebd-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="66ebd-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 应用使用预配置的 `HttpClient` 服务调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="66ebd-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="66ebd-106">使用 Blazor JSON 帮助程序或通过 <xref:System.Net.Http.HttpRequestMessage> 撰写请求，其中可以包含 JavaScript [Fetch API ](https://developer.mozilla.org/docs/Web/API/Fetch_API) 选项。</span><span class="sxs-lookup"><span data-stu-id="66ebd-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="66ebd-107">Blazor WebAssembly 应用中的 `HttpClient` 服务侧重于使请求返回源服务器。</span><span class="sxs-lookup"><span data-stu-id="66ebd-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="66ebd-108">本主题中的指导仅适用于 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="66ebd-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="66ebd-109">[Blazor 服务器](xref:blazor/hosting-models#blazor-server)应用使用 <xref:System.Net.Http.HttpClient> 实例（通常是使用 <xref:System.Net.Http.IHttpClientFactory> 创建）调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="66ebd-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="66ebd-110">本主题中的指导不适用于 Blazor 服务器应用。</span><span class="sxs-lookup"><span data-stu-id="66ebd-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="66ebd-111">开发 Blazor 服务器应用时，请按照 <xref:fundamentals/http-requests> 中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="66ebd-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="66ebd-112">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）&ndash; 选择 BlazorWebAssemblySample  应用。</span><span class="sxs-lookup"><span data-stu-id="66ebd-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="66ebd-113">请查看 BlazorWebAssemblySample  示例应用中的以下组件：</span><span class="sxs-lookup"><span data-stu-id="66ebd-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="66ebd-114">调用 Web API (Pages/CallWebAPI.razor  )</span><span class="sxs-lookup"><span data-stu-id="66ebd-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="66ebd-115">HTTP 请求测试器 (Components/HTTPRequestTester.razor  )</span><span class="sxs-lookup"><span data-stu-id="66ebd-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="66ebd-116">package</span><span class="sxs-lookup"><span data-stu-id="66ebd-116">Packages</span></span>

<span data-ttu-id="66ebd-117">在项目文件中引用试验  [Microsoft.AspNetCore.Blazor.HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="66ebd-117">Reference the *experimental* [Microsoft.AspNetCore.Blazor.HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) NuGet package in the project file.</span></span> <span data-ttu-id="66ebd-118">`Microsoft.AspNetCore.Blazor.HttpClient` 基于 `HttpClient` 和 [System.Text.Json](https://www.nuget.org/packages/System.Text.Json/)。</span><span class="sxs-lookup"><span data-stu-id="66ebd-118">`Microsoft.AspNetCore.Blazor.HttpClient` is based on `HttpClient` and [System.Text.Json](https://www.nuget.org/packages/System.Text.Json/).</span></span>

<span data-ttu-id="66ebd-119">若要使用稳定 API，请使用 [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) 包，它使用 [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)/[Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="66ebd-119">To use a stable API, use the [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) package, which uses [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)/[Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span> <span data-ttu-id="66ebd-120">在 `Microsoft.AspNet.WebApi.Client` 中使用稳定 API 并不提供本主题中所述的 JSON 帮助程序（它们对于试验 `Microsoft.AspNetCore.Blazor.HttpClient` 包是唯一的）。</span><span class="sxs-lookup"><span data-stu-id="66ebd-120">Using the stable API in `Microsoft.AspNet.WebApi.Client` doesn't provide the JSON helpers described in this topic, which are unique to the experimental `Microsoft.AspNetCore.Blazor.HttpClient` package.</span></span>

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="66ebd-121">HttpClient 和 JSON 帮助程序</span><span class="sxs-lookup"><span data-stu-id="66ebd-121">HttpClient and JSON helpers</span></span>

<span data-ttu-id="66ebd-122">在 Blazor WebAssembly 应用中，[HttpClient](xref:fundamentals/http-requests) 作为预配置服务提供，用于使请求返回源服务器。</span><span class="sxs-lookup"><span data-stu-id="66ebd-122">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="66ebd-123">默认情况下，Blazor 服务器应用不包含 `HttpClient` 服务。</span><span class="sxs-lookup"><span data-stu-id="66ebd-123">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="66ebd-124">使用 [HttpClient 工厂基础结构](xref:fundamentals/http-requests)向应用提供 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="66ebd-124">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="66ebd-125">`HttpClient` 和 JSON 帮助程序还用于调用第三方 Web API 终结点。</span><span class="sxs-lookup"><span data-stu-id="66ebd-125">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="66ebd-126">`HttpClient` 使用浏览器 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 来实现，受其限制制约，包括强制实施相同初始策略。</span><span class="sxs-lookup"><span data-stu-id="66ebd-126">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="66ebd-127">客户端的基址设置为原始服务器的地址。</span><span class="sxs-lookup"><span data-stu-id="66ebd-127">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="66ebd-128">使用 `@inject` 指令插入 `HttpClient` 实例：</span><span class="sxs-lookup"><span data-stu-id="66ebd-128">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="66ebd-129">在下面的示例中，Todo Web API 处理创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="66ebd-129">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="66ebd-130">这些示例基于存储以下内容的 `TodoItem` 类：</span><span class="sxs-lookup"><span data-stu-id="66ebd-130">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="66ebd-131">ID（`Id`，`long`）&ndash; 项目的唯一 ID。</span><span class="sxs-lookup"><span data-stu-id="66ebd-131">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="66ebd-132">名称（`Name`，`string`）&ndash; 项目的名称。</span><span class="sxs-lookup"><span data-stu-id="66ebd-132">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="66ebd-133">状态（`IsComplete``bool`）&ndash; 指示 Todo 项是否已完成。</span><span class="sxs-lookup"><span data-stu-id="66ebd-133">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="66ebd-134">JSON 帮助程序方法将请求发送到 URI（以下示例中的 Web API）并处理响应：</span><span class="sxs-lookup"><span data-stu-id="66ebd-134">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="66ebd-135">`GetJsonAsync` &ndash; 发送 HTTP GET 请求并分析 JSON 响应正文以创建对象。</span><span class="sxs-lookup"><span data-stu-id="66ebd-135">`GetJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="66ebd-136">在下面的代码中，`_todoItems` 由组件显示。</span><span class="sxs-lookup"><span data-stu-id="66ebd-136">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="66ebd-137">当组件完成呈现 ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)) 时，会触发 `GetTodoItems` 方法。</span><span class="sxs-lookup"><span data-stu-id="66ebd-137">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="66ebd-138">有关完整的示例，请参阅示例应用。</span><span class="sxs-lookup"><span data-stu-id="66ebd-138">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="66ebd-139">`PostJsonAsync` &ndash; 发送 HTTP POST 请求（包括 JSON 编码的内容），并分析 JSON 响应正文以创建对象。</span><span class="sxs-lookup"><span data-stu-id="66ebd-139">`PostJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="66ebd-140">在下面的代码中，`_newItemName` 由组件的绑定元素提供。</span><span class="sxs-lookup"><span data-stu-id="66ebd-140">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="66ebd-141">通过选择 `<button>` 元素来触发 `AddItem` 方法。</span><span class="sxs-lookup"><span data-stu-id="66ebd-141">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="66ebd-142">有关完整的示例，请参阅示例应用。</span><span class="sxs-lookup"><span data-stu-id="66ebd-142">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="_newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string _newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = _newItemName, IsComplete = false };
          await Http.PostJsonAsync("api/TodoItems", addItem);
      }
  }
  ```

* <span data-ttu-id="66ebd-143">`PutJsonAsync` &ndash; 发送 HTTP PUT 请求（包括 JSON 编码的内容）。</span><span class="sxs-lookup"><span data-stu-id="66ebd-143">`PutJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="66ebd-144">在下面的代码中，`Name` 和 `IsCompleted` 的 `_editItem` 值由组件的绑定元素提供。</span><span class="sxs-lookup"><span data-stu-id="66ebd-144">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="66ebd-145">当在 UI 的另一个部分中选择项并调用 `EditItem` 时，会设置项的 `Id`。</span><span class="sxs-lookup"><span data-stu-id="66ebd-145">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="66ebd-146">通过选择 Save `<button>` 元素来触发 `SaveItem` 方法。</span><span class="sxs-lookup"><span data-stu-id="66ebd-146">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="66ebd-147">有关完整的示例，请参阅示例应用。</span><span class="sxs-lookup"><span data-stu-id="66ebd-147">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="_editItem.IsComplete" />
  <input @bind="_editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem _editItem = new TodoItem();

      private void EditItem(long id)
      {
          var editItem = _todoItems.Single(i => i.Id == id);
          _editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```

<span data-ttu-id="66ebd-148"><xref:System.Net.Http> 包括用于发送 HTTP 请求和接收 HTTP 响应的附加扩展方法。</span><span class="sxs-lookup"><span data-stu-id="66ebd-148"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="66ebd-149">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) 用于将 HTTP DELETE 请求发送到 Web API。</span><span class="sxs-lookup"><span data-stu-id="66ebd-149">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="66ebd-150">在下面的代码中，Delete `<button>` 元素调用 `DeleteItem` 方法。</span><span class="sxs-lookup"><span data-stu-id="66ebd-150">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="66ebd-151">绑定 `<input>` 元素提供要删除的项的 `id`。</span><span class="sxs-lookup"><span data-stu-id="66ebd-151">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="66ebd-152">有关完整的示例，请参阅示例应用。</span><span class="sxs-lookup"><span data-stu-id="66ebd-152">See the sample app for a complete example.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="66ebd-153">跨域资源共享 (CORS)</span><span class="sxs-lookup"><span data-stu-id="66ebd-153">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="66ebd-154">浏览器安全可防止网页向不同域（而不是向网页提供服务的域）进行请求。</span><span class="sxs-lookup"><span data-stu-id="66ebd-154">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="66ebd-155">此限制称为同域策略  。</span><span class="sxs-lookup"><span data-stu-id="66ebd-155">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="66ebd-156">同域策略可防止恶意站点从另一站点读取敏感数据。</span><span class="sxs-lookup"><span data-stu-id="66ebd-156">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="66ebd-157">若要从浏览器向具有不同源的终结点进行请求，终结点  必须启用[跨域资源共享 (CORS)](https://www.w3.org/TR/cors/)。</span><span class="sxs-lookup"><span data-stu-id="66ebd-157">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="66ebd-158">[Blazor WebAssembly 示例应用 (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) 演示如何在调用 Web API 组件 (Pages/CallWebAPI.razor  ) 中使用 CORS。</span><span class="sxs-lookup"><span data-stu-id="66ebd-158">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="66ebd-159">若要允许其他站点对应用进行跨域资源共享 (CORS) 请求，请参阅 <xref:security/cors>。</span><span class="sxs-lookup"><span data-stu-id="66ebd-159">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="66ebd-160">具有 Fetch API 请求选项的 HttpClient 和 HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="66ebd-160">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="66ebd-161">在 Blazor WebAssembly 应用中的 WebAssembly 上运行时，可使用 [HttpClient](xref:fundamentals/http-requests) 和 <xref:System.Net.Http.HttpRequestMessage> 自定义请求。</span><span class="sxs-lookup"><span data-stu-id="66ebd-161">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="66ebd-162">例如，可以指定请求 URI、HTTP 方法以及任何所需的请求标头。</span><span class="sxs-lookup"><span data-stu-id="66ebd-162">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

```razor
@using System.Net.Http
@using System.Net.Http.Headers
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        Http.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                new StringContent(
                    @"{""name"":""A New Todo Item"",""isComplete"":false}")
        };

        requestMessage.Content.Headers.ContentType = 
            new System.Net.Http.Headers.MediaTypeHeaderValue(
                "application/json");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

<span data-ttu-id="66ebd-163">有关 Fetch API 选项的详细信息，请参阅 [MDN Web 文档：WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。</span><span class="sxs-lookup"><span data-stu-id="66ebd-163">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="66ebd-164">在 CORS 请求中发送凭据（授权 cookie/标头）时，CORS 策略必须允许使用 `Authorization` 标头。</span><span class="sxs-lookup"><span data-stu-id="66ebd-164">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="66ebd-165">以下策略包括的配置用于：</span><span class="sxs-lookup"><span data-stu-id="66ebd-165">The following policy includes configuration for:</span></span>

* <span data-ttu-id="66ebd-166">请求来源（`http://localhost:5000`、`https://localhost:5001`）。</span><span class="sxs-lookup"><span data-stu-id="66ebd-166">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="66ebd-167">Any 方法（谓词）。</span><span class="sxs-lookup"><span data-stu-id="66ebd-167">Any method (verb).</span></span>
* <span data-ttu-id="66ebd-168">`Content-Type` 和 `Authorization` 标头。</span><span class="sxs-lookup"><span data-stu-id="66ebd-168">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="66ebd-169">若要允许使用自定义标头（例如 `x-custom-header`），请在调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 时列出标头。</span><span class="sxs-lookup"><span data-stu-id="66ebd-169">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="66ebd-170">由客户端 JavaScript 代码设置的凭据（`credentials` 属性设置为 `include`）。</span><span class="sxs-lookup"><span data-stu-id="66ebd-170">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="66ebd-171">有关详细信息，请参阅 <xref:security/cors> 和示例应用的 HTTP 请求测试器组件 (Components/HTTPRequestTester.razor  )。</span><span class="sxs-lookup"><span data-stu-id="66ebd-171">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="66ebd-172">其他资源</span><span class="sxs-lookup"><span data-stu-id="66ebd-172">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="66ebd-173">Kestrel HTTPS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="66ebd-173">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="66ebd-174">W3C 上的跨域资源共享 (CORS)</span><span class="sxs-lookup"><span data-stu-id="66ebd-174">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
