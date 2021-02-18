---
title: 从 ASP.NET Core Blazor WebAssembly 调用 Web API
author: guardrex
description: 了解如何使用 JSON 帮助程序从 Blazor WebAssembly 应用程序调用 Web API，包括进行跨域资源共享 (CORS) 请求。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
no-loc:
- appsettings.json
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
uid: blazor/call-web-api
ms.openlocfilehash: b3c783623252512621a0cee7a3607c69cb6d09bb
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280282"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="a8bfb-103">从 ASP.NET Core Blazor 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="a8bfb-103">Call a web API from ASP.NET Core Blazor</span></span>

> [!NOTE]
> <span data-ttu-id="a8bfb-104">本主题适用于 Blazor WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="a8bfb-105">[Blazor Server](xref:blazor/hosting-models#blazor-server) 应用使用 <xref:System.Net.Http.HttpClient> 实例（通常是使用 <xref:System.Net.Http.IHttpClientFactory> 创建）调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-105">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="a8bfb-106">有关适用于 Blazor Server 的指南，请参阅 <xref:fundamentals/http-requests>。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-106">For guidance that applies to Blazor Server, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="a8bfb-107">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 应用使用预配置的 <xref:System.Net.Http.HttpClient> 服务调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-107">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured <xref:System.Net.Http.HttpClient> service.</span></span> <span data-ttu-id="a8bfb-108">使用 Blazor JSON 帮助程序或通过 <xref:System.Net.Http.HttpRequestMessage> 撰写请求，其中可以包含 JavaScript [Fetch API ](https://developer.mozilla.org/docs/Web/API/Fetch_API) 选项。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-108">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="a8bfb-109">Blazor WebAssembly 应用中的 <xref:System.Net.Http.HttpClient> 服务侧重于使请求返回源服务器。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-109">The <xref:System.Net.Http.HttpClient> service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="a8bfb-110">本主题中的指导仅适用于 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-110">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="a8bfb-111">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）：选择 `BlazorWebAssemblySample` 应用。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)): Select the `BlazorWebAssemblySample` app.</span></span>

<span data-ttu-id="a8bfb-112">请查看 `BlazorWebAssemblySample` 示例应用中的以下组件：</span><span class="sxs-lookup"><span data-stu-id="a8bfb-112">See the following components in the `BlazorWebAssemblySample` sample app:</span></span>

* <span data-ttu-id="a8bfb-113">调用 Web API (`Pages/CallWebAPI.razor`)</span><span class="sxs-lookup"><span data-stu-id="a8bfb-113">Call Web API (`Pages/CallWebAPI.razor`)</span></span>
* <span data-ttu-id="a8bfb-114">HTTP 请求测试程序 (`Components/HTTPRequestTester.razor`)</span><span class="sxs-lookup"><span data-stu-id="a8bfb-114">HTTP Request Tester (`Components/HTTPRequestTester.razor`)</span></span>

## <a name="packages"></a><span data-ttu-id="a8bfb-115">package</span><span class="sxs-lookup"><span data-stu-id="a8bfb-115">Packages</span></span>

<span data-ttu-id="a8bfb-116">在项目文件中引用 [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-116">Reference the [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="a8bfb-117">添加 HttpClient 服务</span><span class="sxs-lookup"><span data-stu-id="a8bfb-117">Add the HttpClient service</span></span>

<span data-ttu-id="a8bfb-118">在 `Program.Main` 中个，如果 <xref:System.Net.Http.HttpClient> 服务尚不存在，则添加它：</span><span class="sxs-lookup"><span data-stu-id="a8bfb-118">In `Program.Main`, add an <xref:System.Net.Http.HttpClient> service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddScoped(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="a8bfb-119">HttpClient 和 JSON 帮助程序</span><span class="sxs-lookup"><span data-stu-id="a8bfb-119">HttpClient and JSON helpers</span></span>

<span data-ttu-id="a8bfb-120">在 Blazor WebAssembly 应用中，[`HttpClient`](xref:fundamentals/http-requests) 作为预配置服务提供，用于使请求返回源服务器。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-120">In a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="a8bfb-121">默认情况下，Blazor Server 应用不包含 <xref:System.Net.Http.HttpClient> 服务。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-121">A Blazor Server app doesn't include an <xref:System.Net.Http.HttpClient> service by default.</span></span> <span data-ttu-id="a8bfb-122">使用 [`HttpClient` 工厂基础结构](xref:fundamentals/http-requests)向应用提供 <xref:System.Net.Http.HttpClient>。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-122">Provide an <xref:System.Net.Http.HttpClient> to the app using the [`HttpClient` factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="a8bfb-123"><xref:System.Net.Http.HttpClient> 和 JSON 帮助程序还用于调用第三方 Web API 终结点。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-123"><xref:System.Net.Http.HttpClient> and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="a8bfb-124"><xref:System.Net.Http.HttpClient> 使用浏览器 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 来实现，受其限制制约，包括强制实施相同初始策略。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-124"><xref:System.Net.Http.HttpClient> is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="a8bfb-125">客户端的基址设置为原始服务器的地址。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-125">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="a8bfb-126">使用 [`@inject`](xref:mvc/views/razor#inject) 指令插入 <xref:System.Net.Http.HttpClient> 实例：</span><span class="sxs-lookup"><span data-stu-id="a8bfb-126">Inject an <xref:System.Net.Http.HttpClient> instance using the [`@inject`](xref:mvc/views/razor#inject) directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="a8bfb-127">在下面的示例中，Todo Web API 处理创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-127">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="a8bfb-128">这些示例基于存储以下内容的 `TodoItem` 类：</span><span class="sxs-lookup"><span data-stu-id="a8bfb-128">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="a8bfb-129">ID（`Id`、`long`）：项的唯一 ID。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-129">ID (`Id`, `long`): Unique ID of the item.</span></span>
* <span data-ttu-id="a8bfb-130">名称（`Name`、`string`）：项的名称。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-130">Name (`Name`, `string`): Name of the item.</span></span>
* <span data-ttu-id="a8bfb-131">状态（`IsComplete`、`bool`）：指明 Todo 项是否已完成。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-131">Status (`IsComplete`, `bool`): Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="a8bfb-132">JSON 帮助程序方法将请求发送到 URI（以下示例中的 Web API）并处理响应：</span><span class="sxs-lookup"><span data-stu-id="a8bfb-132">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="a8bfb-133"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>：发送 HTTP GET 请求，并分析 JSON 响应正文来创建对象。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-133"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="a8bfb-134">在下面的代码中，`todoItems` 由组件显示。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-134">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="a8bfb-135">当组件完成呈现 ([`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods)) 时，会触发 `GetTodoItems` 方法。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-135">The `GetTodoItems` method is triggered when the component is finished rendering ([`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="a8bfb-136">有关完整的示例，请参阅示例应用。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-136">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="a8bfb-137"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>：发送 HTTP POST 请求（包括 JSON 编码的内容），并分析 JSON 响应正文来创建对象。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-137"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="a8bfb-138">在下面的代码中，`newItemName` 由组件的绑定元素提供。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-138">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="a8bfb-139">通过选择 `<button>` 元素来触发 `AddItem` 方法。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-139">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="a8bfb-140">有关完整的示例，请参阅示例应用。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-140">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  <span data-ttu-id="a8bfb-141">调用 <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> 会返回 <xref:System.Net.Http.HttpResponseMessage>。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-141">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="a8bfb-142">若要对响应消息中的 JSON 内容进行反序列化处理，请使用 `ReadFromJsonAsync<T>` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="a8bfb-142">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="a8bfb-143"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>：发送 HTTP PUT 请求（包括 JSON 编码的内容）。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-143"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="a8bfb-144">在下面的代码中，`Name` 和 `IsCompleted` 的 `editItem` 值由组件的绑定元素提供。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-144">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="a8bfb-145">当在 UI 的另一个部分中选择项并调用 `EditItem` 时，会设置项的 `Id`。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-145">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="a8bfb-146">通过选择 Save `<button>` 元素来触发 `SaveItem` 方法。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-146">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="a8bfb-147">有关完整的示例，请参阅示例应用。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-147">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="editItem.IsComplete" />
  <input @bind="editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem editItem = new TodoItem();

      private void EditItem(long id)
      {
          editItem = todoItems.Single(i => i.Id == id);
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  <span data-ttu-id="a8bfb-148">调用 <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> 会返回 <xref:System.Net.Http.HttpResponseMessage>。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-148">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="a8bfb-149">若要对响应消息中的 JSON 内容进行反序列化处理，请使用 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="a8bfb-149">To deserialize the JSON content from the response message, use the <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="a8bfb-150"><xref:System.Net.Http> 包括用于发送 HTTP 请求和接收 HTTP 响应的附加扩展方法。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-150"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="a8bfb-151"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> 用于将 HTTP DELETE 请求发送到 Web API。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-151"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="a8bfb-152">在下面的代码中，Delete `<button>` 元素调用 `DeleteItem` 方法。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-152">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="a8bfb-153">绑定 `<input>` 元素提供要删除的项的 `id`。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-153">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="a8bfb-154">有关完整的示例，请参阅示例应用。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-154">See the sample app for a complete example.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{id}");
}
```

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="a8bfb-155">已命名的 HttpClient 和 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="a8bfb-155">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="a8bfb-156">支持已命名的 <xref:System.Net.Http.HttpClient> 的 <xref:System.Net.Http.IHttpClientFactory> 服务和配置。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-156"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="a8bfb-157">在项目文件中引用 [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-157">Reference the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package in the project file.</span></span>

<span data-ttu-id="a8bfb-158">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="a8bfb-158">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="a8bfb-159">`FetchData` 组件 (`Pages/FetchData.razor`)：</span><span class="sxs-lookup"><span data-stu-id="a8bfb-159">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecast");
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="a8bfb-160">类型化 HttpClient</span><span class="sxs-lookup"><span data-stu-id="a8bfb-160">Typed HttpClient</span></span>

<span data-ttu-id="a8bfb-161">类型化 <xref:System.Net.Http.HttpClient> 使用应用的一个或多个 <xref:System.Net.Http.HttpClient> 实例（默认或命名）从一个或多个 web API 终结点返回数据。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-161">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="a8bfb-162">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="a8bfb-162">`WeatherForecastClient.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastHttpClient
{
    private readonly HttpClient http;

    public WeatherForecastHttpClient(HttpClient http)
    {
        this.http = http;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await http.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch
        {
            ...
        }

        return forecasts;
    }
}
```

<span data-ttu-id="a8bfb-163">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="a8bfb-163">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastHttpClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="a8bfb-164">组件插入类型化的 <xref:System.Net.Http.HttpClient> 来调用 web API。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-164">Components inject the typed <xref:System.Net.Http.HttpClient> to call the web API.</span></span>

<span data-ttu-id="a8bfb-165">`FetchData` 组件 (`Pages/FetchData.razor`)：</span><span class="sxs-lookup"><span data-stu-id="a8bfb-165">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastHttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Http.GetForecastAsync();
    }
}
```

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="a8bfb-166">具有提取 API 请求选项的 `HttpClient` 和 `HttpRequestMessage`</span><span class="sxs-lookup"><span data-stu-id="a8bfb-166">`HttpClient` and `HttpRequestMessage` with Fetch API request options</span></span>

<span data-ttu-id="a8bfb-167">在 Blazor WebAssembly 应用中的 WebAssembly 上运行时，可以使用 [`HttpClient`](xref:fundamentals/http-requests)（[API 文档](xref:System.Net.Http.HttpClient)）和 <xref:System.Net.Http.HttpRequestMessage> 自定义请求。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-167">When running on WebAssembly in a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) ([API documentation](xref:System.Net.Http.HttpClient)) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="a8bfb-168">例如，可以指定 HTTP 方法和请求标头。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-168">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="a8bfb-169">以下组件向服务器上的待办事项列表 API 终结点发出 `POST` 请求，并显示响应正文：</span><span class="sxs-lookup"><span data-stu-id="a8bfb-169">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@responseBody</p>

@code {
    private string responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="a8bfb-170"><xref:System.Net.Http.HttpClient> 的 .NET WebAssembly 实现使用 [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-170">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="a8bfb-171">提取允许配置多个[特定于请求的选项](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-171">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="a8bfb-172">使用下表中所示的 <xref:System.Net.Http.HttpRequestMessage> 扩展方法可配置 HTTP fetch 请求选项。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-172">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="a8bfb-173">扩展方法</span><span class="sxs-lookup"><span data-stu-id="a8bfb-173">Extension method</span></span> | <span data-ttu-id="a8bfb-174">提取请求属性</span><span class="sxs-lookup"><span data-stu-id="a8bfb-174">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="a8bfb-175">可使用更通用的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> 扩展方法设置其他选项。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-175">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="a8bfb-176">HTTP 响应通常在 Blazor WebAssembly 应用中缓冲，以支持同步读取响应内容。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-176">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="a8bfb-177">要支持响应流式处理，请对请求使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-177">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="a8bfb-178">要在跨源请求中加入凭据，请使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="a8bfb-178">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="a8bfb-179">有关 Fetch API 选项的详细信息，请参阅 [MDN Web 文档：WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-179">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

## <a name="handle-errors"></a><span data-ttu-id="a8bfb-180">处理错误</span><span class="sxs-lookup"><span data-stu-id="a8bfb-180">Handle errors</span></span>

<span data-ttu-id="a8bfb-181">如果在与 web API 交互时出现错误，开发人员代码可以处理这些错误。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-181">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="a8bfb-182">例如，<xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> 需要服务器 API 的 JSON 响应，其中 `Content-Type` 为 `application/json`。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-182">For example, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="a8bfb-183">如果响应不是 JSON 格式，则内容验证会引发 <xref:System.NotSupportedException>。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-183">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="a8bfb-184">在下面的示例中，天气预测数据请求的 URI 终结点拼写错误。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-184">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="a8bfb-185">URI 应该为 `WeatherForecast`，但在单元格中显示为 `WeatherForcast`（缺少“e”）。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-185">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="a8bfb-186"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> 调用需要返回 JSON，但服务器为服务器上的未处理异常返回 HTML，其中 `Content-Type` 为 `text/html`。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-186">The <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="a8bfb-187">由于找不到路径并且中间件无法为请求提供页面或视图，因此服务器上发生未处理的异常。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-187">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="a8bfb-188">在客户端上的 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 中，当响应内容验证为非 JSON 时，将引发 <xref:System.NotSupportedException>。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-188">In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="a8bfb-189">在 `catch` 块中捕获到异常，其中自定义逻辑可以记录错误或向用户显示友好错误消息：</span><span class="sxs-lookup"><span data-stu-id="a8bfb-189">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForcast");
    }
    catch (NotSupportedException exception)
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="a8bfb-190">前面示例代码为了方便本文演示。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-190">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="a8bfb-191">即使终结点不存在或服务器上发生未处理的异常，也可将 Web API 服务器应用配置为返回 JSON。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-191">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled exception on the server occurs.</span></span>

<span data-ttu-id="a8bfb-192">有关详细信息，请参阅 <xref:blazor/fundamentals/handle-errors>。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-192">For more information, see <xref:blazor/fundamentals/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="a8bfb-193">跨域资源共享 (CORS)</span><span class="sxs-lookup"><span data-stu-id="a8bfb-193">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="a8bfb-194">浏览器安全可防止网页向不同域（而不是向网页提供服务的域）进行请求。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-194">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="a8bfb-195">此限制称为同域策略。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-195">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="a8bfb-196">同域策略可防止恶意站点从另一站点读取敏感数据。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-196">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="a8bfb-197">若要从浏览器向具有不同源的终结点进行请求，终结点必须启用[跨域资源共享 (CORS)](https://www.w3.org/TR/cors/)。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-197">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="a8bfb-198">[Blazor WebAssembly 示例应用 (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) 演示如何在调用 Web API 组件 (`Pages/CallWebAPI.razor`) 中使用 CORS。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-198">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (`Pages/CallWebAPI.razor`).</span></span>

<span data-ttu-id="a8bfb-199">有关 CORS 与 Blazor 应用中的安全请求的详细信息，请参阅 <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors>。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-199">For more information on CORS with secure requests in Blazor apps, see <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors>.</span></span>

<span data-ttu-id="a8bfb-200">有关 CORS 与 ASP.NET Core 应用的一般信息，请参阅 <xref:security/cors>。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-200">For general information on CORS with ASP.NET Core apps, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a8bfb-201">其他资源</span><span class="sxs-lookup"><span data-stu-id="a8bfb-201">Additional resources</span></span>

* <span data-ttu-id="a8bfb-202"><xref:blazor/security/webassembly/additional-scenarios>：包括对使用 <xref:System.Net.Http.HttpClient> 发出安全 Web API 请求的介绍。</span><span class="sxs-lookup"><span data-stu-id="a8bfb-202"><xref:blazor/security/webassembly/additional-scenarios>: Includes coverage on using <xref:System.Net.Http.HttpClient> to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
::: moniker range=">= aspnetcore-5.0"
* [<span data-ttu-id="a8bfb-203">Kestrel HTTPS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="a8bfb-203">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel/endpoints)
::: moniker-end
::: moniker range="< aspnetcore-5.0"
* [<span data-ttu-id="a8bfb-204">Kestrel HTTPS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="a8bfb-204">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
::: moniker-end
* [<span data-ttu-id="a8bfb-205">W3C 上的跨域资源共享 (CORS)</span><span class="sxs-lookup"><span data-stu-id="a8bfb-205">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
