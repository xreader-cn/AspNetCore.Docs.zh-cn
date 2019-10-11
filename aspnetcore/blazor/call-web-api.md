---
title: 从 ASP.NET Core Blazor 调用 web API
author: guardrex
description: 了解如何使用 JSON 帮助程序（包括生成跨域资源共享（CORS）请求）从 Blazor 应用程序调用 web API。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: blazor/call-web-api
ms.openlocfilehash: 3d70af2226eb29870458a5fd3c2bbbc3ee5c14ce
ms.sourcegitcommit: 73a451e9a58ac7102f90b608d661d8c23dd9bbaf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72037438"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="8b2d6-103">从 ASP.NET Core Blazor 调用 web API</span><span class="sxs-lookup"><span data-stu-id="8b2d6-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="8b2d6-104">作者： [Luke Latham](https://github.com/guardrex)、 [Daniel Roth](https://github.com/danroth27)和[Juan De la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="8b2d6-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="8b2d6-105">Blazor WebAssembly apps 使用预先配置的 @no__t 0 服务来调用 web Api。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-105">Blazor WebAssembly apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="8b2d6-106">撰写请求，其中可以包含 JavaScript [FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)选项，使用 Blazor JSON 帮助器或 <xref:System.Net.Http.HttpRequestMessage>。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span>

<span data-ttu-id="8b2d6-107">Blazor 服务器应用使用 <xref:System.Net.Http.IHttpClientFactory> 创建的 @no__t 的实例来调用 web Api。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-107">Blazor Server apps call web APIs using <xref:System.Net.Http.HttpClient> instances typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="8b2d6-108">有关详细信息，请参阅 <xref:fundamentals/http-requests> 。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-108">For more information, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="8b2d6-109">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="8b2d6-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8b2d6-110">有关 Blazor WebAssembly 示例，请参阅示例应用中的以下组件：</span><span class="sxs-lookup"><span data-stu-id="8b2d6-110">For Blazor WebAssembly examples, see the following components in the sample app:</span></span>

* <span data-ttu-id="8b2d6-111">调用 Web API （*Pages/CallWebAPI*）</span><span class="sxs-lookup"><span data-stu-id="8b2d6-111">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="8b2d6-112">HTTP 请求测试器（*组件/HTTPRequestTester*）</span><span class="sxs-lookup"><span data-stu-id="8b2d6-112">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="8b2d6-113">HttpClient 和 JSON 帮助器</span><span class="sxs-lookup"><span data-stu-id="8b2d6-113">HttpClient and JSON helpers</span></span>

<span data-ttu-id="8b2d6-114">在 Blazor WebAssembly apps 中， [HttpClient](xref:fundamentals/http-requests)作为预配置服务提供，用于向源服务器发送请求。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-114">In Blazor WebAssembly apps, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span> <span data-ttu-id="8b2d6-115">若要使用 `HttpClient` JSON 帮助程序，请将包引用添加到 @no__t。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-115">To use `HttpClient` JSON helpers, add a package reference to `Microsoft.AspNetCore.Blazor.HttpClient`.</span></span> <span data-ttu-id="8b2d6-116">@no__t 0 和 JSON 帮助器也用于调用第三方 web API 终结点。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-116">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="8b2d6-117">`HttpClient` 是使用浏览器[提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API)实现的，并且受到其限制，包括强制实施相同的源策略。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-117">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="8b2d6-118">客户端的基址设置为源服务器的地址。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-118">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="8b2d6-119">使用 `@inject` 指令插入 `HttpClient` 实例：</span><span class="sxs-lookup"><span data-stu-id="8b2d6-119">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```cshtml
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="8b2d6-120">在下面的示例中，Todo web API 处理创建、读取、更新和删除（CRUD）操作。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-120">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="8b2d6-121">这些示例基于一个 @no__t 0 类，该类用于存储：</span><span class="sxs-lookup"><span data-stu-id="8b2d6-121">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="8b2d6-122">ID （`Id`，`long`） @no__t 项的唯一 ID。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-122">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="8b2d6-123">名称（`Name`，`string`） @no__t 项的2名称。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-123">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="8b2d6-124">Status （`IsComplete`，`bool`） &ndash; 指示是否已完成 Todo 项。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-124">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="8b2d6-125">JSON helper 方法将请求发送到 URI （以下示例中的 web API）并处理响应：</span><span class="sxs-lookup"><span data-stu-id="8b2d6-125">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="8b2d6-126">`GetJsonAsync` &ndash; 发送 HTTP GET 请求并分析 JSON 响应正文以创建对象。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-126">`GetJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="8b2d6-127">在下面的代码中，组件显示 `_todoItems`。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-127">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="8b2d6-128">当组件完成呈现（[OnInitializedAsync](xref:blazor/components#lifecycle-methods)）时，将触发 `GetTodoItems` 方法。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-128">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/components#lifecycle-methods)).</span></span> <span data-ttu-id="8b2d6-129">有关完整示例，请参阅示例应用。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-129">See the sample app for a complete example.</span></span>

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="8b2d6-130">`PostJsonAsync` &ndash; 发送 HTTP POST 请求（包括 JSON 编码的内容），并分析 JSON 响应正文以创建对象。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-130">`PostJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="8b2d6-131">在下面的代码中，`_newItemName` 由组件的绑定元素提供。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-131">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="8b2d6-132">@No__t-0 方法通过选择 @no__t 1 元素触发。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-132">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="8b2d6-133">有关完整示例，请参阅示例应用。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-133">See the sample app for a complete example.</span></span>

  ```cshtml
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

* <span data-ttu-id="8b2d6-134">`PutJsonAsync` &ndash; 发送 HTTP PUT 请求，其中包括 JSON 编码的内容。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-134">`PutJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="8b2d6-135">在下面的代码中，@no__t 的 @no__t 值为-1，`IsCompleted` 由组件的绑定元素提供。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-135">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="8b2d6-136">当在 UI 的另一部分中选择项并且调用 `EditItem` 时，将设置项的 `Id`。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-136">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="8b2d6-137">通过选择 "保存 `<button>`" 元素触发 `SaveItem` 方法。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-137">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="8b2d6-138">有关完整示例，请参阅示例应用。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-138">See the sample app for a complete example.</span></span>

  ```cshtml
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

<span data-ttu-id="8b2d6-139"><xref:System.Net.Http> 包含用于发送 HTTP 请求和接收 HTTP 响应的附加扩展方法。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-139"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="8b2d6-140">[HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*)用于将 HTTP DELETE 请求发送到 web API。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-140">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="8b2d6-141">在下面的代码中，Delete `<button>` 元素调用 `DeleteItem` 方法。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-141">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="8b2d6-142">绑定 `<input>` 元素提供要删除的项的 @no__t。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-142">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="8b2d6-143">有关完整示例，请参阅示例应用。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-143">See the sample app for a complete example.</span></span>

```cshtml
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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="8b2d6-144">跨域资源共享（CORS）</span><span class="sxs-lookup"><span data-stu-id="8b2d6-144">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="8b2d6-145">浏览器安全性可防止网页向其他域发出请求，而不是提供给网页的域。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-145">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="8b2d6-146">此限制称为*相同源策略*。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-146">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="8b2d6-147">同一源策略可防止恶意站点读取另一个站点中的敏感数据。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-147">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="8b2d6-148">若要将浏览器请求发送到具有不同源的终结点，*终结点*必须启用[跨域资源共享（CORS）](https://www.w3.org/TR/cors/)。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-148">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="8b2d6-149">示例应用演示了如何在调用 Web API 组件（*Pages/CallWebAPI*）中使用 CORS。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-149">The sample app demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="8b2d6-150">若要允许其他站点对应用进行跨域资源共享（CORS）请求，请参阅 <xref:security/cors>。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-150">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="8b2d6-151">带有 Fetch API 请求选项的 HttpClient 和 HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="8b2d6-151">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="8b2d6-152">在 Blazor WebAssembly 应用中的 WebAssembly 上运行时，使用[HttpClient](xref:fundamentals/http-requests)并 <xref:System.Net.Http.HttpRequestMessage> 自定义请求。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-152">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="8b2d6-153">例如，可以指定请求 URI、HTTP 方法以及任何所需的请求标头。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-153">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

<span data-ttu-id="8b2d6-154">使用请求的 `WebAssemblyHttpMessageHandler.FetchArgs` 属性向基础 JavaScript[提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API)提供请求选项。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-154">Supply request options to the underlying JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) using the `WebAssemblyHttpMessageHandler.FetchArgs` property on the request.</span></span> <span data-ttu-id="8b2d6-155">如以下示例中所示，将 `credentials` 属性设置为以下任意值：</span><span class="sxs-lookup"><span data-stu-id="8b2d6-155">As shown in the following example, the `credentials` property is set to any of the following values:</span></span>

* <span data-ttu-id="8b2d6-156">`FetchCredentialsOption.Include` （"include"） &ndash; 建议浏览器发送凭据（如 cookie 或 HTTP 身份验证标头），即使对于跨源请求也是如此。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-156">`FetchCredentialsOption.Include` ("include") &ndash; Advises the browser to send credentials (such as cookies or HTTP authentication headers) even for cross-origin requests.</span></span> <span data-ttu-id="8b2d6-157">仅当 CORS 策略配置为允许凭据时才允许。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-157">Only allowed when the CORS policy is configured to allow credentials.</span></span>
* <span data-ttu-id="8b2d6-158">`FetchCredentialsOption.Omit` （"省略"） &ndash; 建议浏览器从不发送凭据（如 cookie 或 HTTP 身份验证标头）。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-158">`FetchCredentialsOption.Omit` ("omit") &ndash; Advises the browser never to send credentials (such as cookies or HTTP auth headers).</span></span>
* <span data-ttu-id="8b2d6-159">`FetchCredentialsOption.SameOrigin` （"相同源"） &ndash; 建议仅当目标 URL 与调用应用程序位于同一源时，才向浏览器发送凭据（如 cookie 或 HTTP 身份验证标头）。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-159">`FetchCredentialsOption.SameOrigin` ("same-origin") &ndash; Advises the browser to send credentials (such as cookies or HTTP auth headers) only if the target URL is on the same origin as the calling application.</span></span>

```cshtml
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
        
        requestMessage.Properties[WebAssemblyHttpMessageHandler.FetchArgs] = new
        { 
            credentials = FetchCredentialsOption.Include
        };

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

<span data-ttu-id="8b2d6-160">有关 Fetch API 选项的详细信息，@no__t 请参阅 0MDN web 文档：WindowOrWorkerGlobalScope （）:P arameters @ no__t-0。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-160">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="8b2d6-161">在 CORS 请求上发送凭据（授权 cookie/标头）时，CORS 策略必须允许 @no__t 0 标头。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-161">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="8b2d6-162">以下策略包括的配置：</span><span class="sxs-lookup"><span data-stu-id="8b2d6-162">The following policy includes configuration for:</span></span>

* <span data-ttu-id="8b2d6-163">请求来源（`http://localhost:5000`，`https://localhost:5001`）。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-163">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="8b2d6-164">任何方法（谓词）。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-164">Any method (verb).</span></span>
* <span data-ttu-id="8b2d6-165">@no__t 0 和 @no__t 标头。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-165">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="8b2d6-166">若要允许自定义标头（例如 `x-custom-header`），请在调用 @no__t 时列出标头。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-166">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="8b2d6-167">由客户端 JavaScript 代码（@no__t 设置为 `include`）设置的凭据。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-167">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="8b2d6-168">有关详细信息，请参阅 <xref:security/cors> 和示例应用的 HTTP 请求测试器组件（*组件/HTTPRequestTester*）。</span><span class="sxs-lookup"><span data-stu-id="8b2d6-168">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b2d6-169">其他资源</span><span class="sxs-lookup"><span data-stu-id="8b2d6-169">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="8b2d6-170">Kestrel HTTPS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="8b2d6-170">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="8b2d6-171">W3C 上的跨域资源共享（CORS）</span><span class="sxs-lookup"><span data-stu-id="8b2d6-171">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
