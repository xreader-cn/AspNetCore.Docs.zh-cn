---
title: 从 ASP.NET Core Blazor WebAssembly 调用 Web API
author: guardrex
description: 了解如何使用 JSON 帮助程序从 Blazor WebAssembly 应用调用 Web API，包括发出跨域资源共享 (CORS) 请求。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: db1f6a357f63b405bf2f3b98e51c9aeffda97d66
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242519"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="ca0a2-103">从 ASP.NET Core Blazor 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="ca0a2-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="ca0a2-104">作者：[Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27) 和 [Juan De la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="ca0a2-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

> [!NOTE]
> <span data-ttu-id="ca0a2-105">本主题适用于 Blazor WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-105">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="ca0a2-106">[Blazor 服务器](xref:blazor/hosting-models#blazor-server)应用使用 <xref:System.Net.Http.HttpClient> 实例（通常是使用 <xref:System.Net.Http.IHttpClientFactory> 创建）调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-106">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="ca0a2-107">有关适用于 Blazor Server 的指南，请参阅 <xref:fundamentals/http-requests>。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-107">For guidance that applies to Blazor Server, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="ca0a2-108">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 应用使用预配置的 <xref:System.Net.Http.HttpClient> 服务调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-108">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured <xref:System.Net.Http.HttpClient> service.</span></span> <span data-ttu-id="ca0a2-109">使用 Blazor JSON 帮助程序或通过 <xref:System.Net.Http.HttpRequestMessage> 撰写请求，其中可以包含 JavaScript [Fetch API ](https://developer.mozilla.org/docs/Web/API/Fetch_API) 选项。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-109">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="ca0a2-110">Blazor WebAssembly 应用中的 <xref:System.Net.Http.HttpClient> 服务侧重于使请求返回源服务器。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-110">The <xref:System.Net.Http.HttpClient> service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="ca0a2-111">本主题中的指导仅适用于 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-111">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="ca0a2-112">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）：选择 `BlazorWebAssemblySample` 应用。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)): Select the `BlazorWebAssemblySample` app.</span></span>

<span data-ttu-id="ca0a2-113">请查看 `BlazorWebAssemblySample` 示例应用中的以下组件：</span><span class="sxs-lookup"><span data-stu-id="ca0a2-113">See the following components in the `BlazorWebAssemblySample` sample app:</span></span>

* <span data-ttu-id="ca0a2-114">调用 Web API (`Pages/CallWebAPI.razor`)</span><span class="sxs-lookup"><span data-stu-id="ca0a2-114">Call Web API (`Pages/CallWebAPI.razor`)</span></span>
* <span data-ttu-id="ca0a2-115">HTTP 请求测试程序 (`Components/HTTPRequestTester.razor`)</span><span class="sxs-lookup"><span data-stu-id="ca0a2-115">HTTP Request Tester (`Components/HTTPRequestTester.razor`)</span></span>

## <a name="packages"></a><span data-ttu-id="ca0a2-116">package</span><span class="sxs-lookup"><span data-stu-id="ca0a2-116">Packages</span></span>

<span data-ttu-id="ca0a2-117">在项目文件中引用 [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-117">Reference the [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="ca0a2-118">添加 HttpClient 服务</span><span class="sxs-lookup"><span data-stu-id="ca0a2-118">Add the HttpClient service</span></span>

<span data-ttu-id="ca0a2-119">在 `Program.Main` 中个，如果 <xref:System.Net.Http.HttpClient> 服务尚不存在，则添加它：</span><span class="sxs-lookup"><span data-stu-id="ca0a2-119">In `Program.Main`, add an <xref:System.Net.Http.HttpClient> service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="ca0a2-120">HttpClient 和 JSON 帮助程序</span><span class="sxs-lookup"><span data-stu-id="ca0a2-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="ca0a2-121">在 Blazor WebAssembly 应用中，[`HttpClient`](xref:fundamentals/http-requests) 作为预配置服务提供，用于使请求返回源服务器。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-121">In a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="ca0a2-122">默认情况下，Blazor 服务器应用不包含 <xref:System.Net.Http.HttpClient> 服务。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-122">A Blazor Server app doesn't include an <xref:System.Net.Http.HttpClient> service by default.</span></span> <span data-ttu-id="ca0a2-123">使用 [`HttpClient` 工厂基础结构](xref:fundamentals/http-requests)向应用提供 <xref:System.Net.Http.HttpClient>。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-123">Provide an <xref:System.Net.Http.HttpClient> to the app using the [`HttpClient` factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="ca0a2-124"><xref:System.Net.Http.HttpClient> 和 JSON 帮助程序还用于调用第三方 Web API 终结点。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-124"><xref:System.Net.Http.HttpClient> and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="ca0a2-125"><xref:System.Net.Http.HttpClient> 使用浏览器 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 来实现，受其限制制约，包括强制实施相同初始策略。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-125"><xref:System.Net.Http.HttpClient> is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="ca0a2-126">客户端的基址设置为原始服务器的地址。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="ca0a2-127">使用 [`@inject`](xref:mvc/views/razor#inject) 指令插入 <xref:System.Net.Http.HttpClient> 实例：</span><span class="sxs-lookup"><span data-stu-id="ca0a2-127">Inject an <xref:System.Net.Http.HttpClient> instance using the [`@inject`](xref:mvc/views/razor#inject) directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="ca0a2-128">在下面的示例中，Todo Web API 处理创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="ca0a2-129">这些示例基于存储以下内容的 `TodoItem` 类：</span><span class="sxs-lookup"><span data-stu-id="ca0a2-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="ca0a2-130">ID（`Id`、`long`）：项的唯一 ID。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-130">ID (`Id`, `long`): Unique ID of the item.</span></span>
* <span data-ttu-id="ca0a2-131">名称（`Name`、`string`）：项的名称。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-131">Name (`Name`, `string`): Name of the item.</span></span>
* <span data-ttu-id="ca0a2-132">状态（`IsComplete`、`bool`）：指明 Todo 项是否已完成。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-132">Status (`IsComplete`, `bool`): Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="ca0a2-133">JSON 帮助程序方法将请求发送到 URI（以下示例中的 Web API）并处理响应：</span><span class="sxs-lookup"><span data-stu-id="ca0a2-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="ca0a2-134"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>：发送 HTTP GET 请求，并分析 JSON 响应正文来创建对象。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-134"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="ca0a2-135">在下面的代码中，`todoItems` 由组件显示。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-135">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="ca0a2-136">当组件完成呈现 ([`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods)) 时，会触发 `GetTodoItems` 方法。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-136">The `GetTodoItems` method is triggered when the component is finished rendering ([`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="ca0a2-137">有关完整的示例，请参阅示例应用。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="ca0a2-138"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>：发送 HTTP POST 请求（包括 JSON 编码的内容），并分析 JSON 响应正文来创建对象。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-138"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="ca0a2-139">在下面的代码中，`newItemName` 由组件的绑定元素提供。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-139">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="ca0a2-140">通过选择 `<button>` 元素来触发 `AddItem` 方法。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="ca0a2-141">有关完整的示例，请参阅示例应用。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-141">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="ca0a2-142">调用 <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> 会返回 <xref:System.Net.Http.HttpResponseMessage>。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-142">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="ca0a2-143">若要对响应消息中的 JSON 内容进行反序列化处理，请使用 `ReadFromJsonAsync<T>` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="ca0a2-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="ca0a2-144"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>：发送 HTTP PUT 请求（包括 JSON 编码的内容）。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-144"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="ca0a2-145">在下面的代码中，`Name` 和 `IsCompleted` 的 `editItem` 值由组件的绑定元素提供。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-145">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="ca0a2-146">当在 UI 的另一个部分中选择项并调用 `EditItem` 时，会设置项的 `Id`。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="ca0a2-147">通过选择 Save `<button>` 元素来触发 `SaveItem` 方法。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="ca0a2-148">有关完整的示例，请参阅示例应用。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-148">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="ca0a2-149">调用 <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> 会返回 <xref:System.Net.Http.HttpResponseMessage>。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-149">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="ca0a2-150">若要对响应消息中的 JSON 内容进行反序列化处理，请使用 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="ca0a2-150">To deserialize the JSON content from the response message, use the <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="ca0a2-151"><xref:System.Net.Http> 包括用于发送 HTTP 请求和接收 HTTP 响应的附加扩展方法。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="ca0a2-152"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> 用于将 HTTP DELETE 请求发送到 Web API。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-152"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="ca0a2-153">在下面的代码中，Delete `<button>` 元素调用 `DeleteItem` 方法。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="ca0a2-154">绑定 `<input>` 元素提供要删除的项的 `id`。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="ca0a2-155">有关完整的示例，请参阅示例应用。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-155">See the sample app for a complete example.</span></span>

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

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="ca0a2-156">已命名的 HttpClient 和 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="ca0a2-156">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="ca0a2-157">支持已命名的 <xref:System.Net.Http.HttpClient> 的 <xref:System.Net.Http.IHttpClientFactory> 服务和配置。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-157"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="ca0a2-158">在项目文件中引用 [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-158">Reference the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package in the project file.</span></span>

<span data-ttu-id="ca0a2-159">`Program.Main` (`Program.cs`)：</span><span class="sxs-lookup"><span data-stu-id="ca0a2-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="ca0a2-160">`FetchData` 组件 (`Pages/FetchData.razor`)：</span><span class="sxs-lookup"><span data-stu-id="ca0a2-160">`FetchData` component (`Pages/FetchData.razor`):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="ca0a2-161">类型化 HttpClient</span><span class="sxs-lookup"><span data-stu-id="ca0a2-161">Typed HttpClient</span></span>

<span data-ttu-id="ca0a2-162">类型化 <xref:System.Net.Http.HttpClient> 使用应用的一个或多个 <xref:System.Net.Http.HttpClient> 实例（默认或命名）从一个或多个 web API 终结点返回数据。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-162">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="ca0a2-163">`WeatherForecastClient.cs`：</span><span class="sxs-lookup"><span data-stu-id="ca0a2-163">`WeatherForecastClient.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastClient
{
    private readonly HttpClient client;

    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];
    
        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
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

<span data-ttu-id="ca0a2-164">`Program.Main` (`Program.cs`)：</span><span class="sxs-lookup"><span data-stu-id="ca0a2-164">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="ca0a2-165">组件插入类型化的 <xref:System.Net.Http.HttpClient> 来调用 web API。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-165">Components inject the typed <xref:System.Net.Http.HttpClient> to call the web API.</span></span>

<span data-ttu-id="ca0a2-166">`FetchData` 组件 (`Pages/FetchData.razor`)：</span><span class="sxs-lookup"><span data-stu-id="ca0a2-166">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Client.GetForecastAsync();
    }
}
```

## <a name="handle-errors"></a><span data-ttu-id="ca0a2-167">处理错误</span><span class="sxs-lookup"><span data-stu-id="ca0a2-167">Handle errors</span></span>

<span data-ttu-id="ca0a2-168">如果在与 web API 交互时出现错误，开发人员代码可以处理这些错误。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-168">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="ca0a2-169">例如，<xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> 需要服务器 API 的 JSON 响应，其中 `Content-Type` 为 `application/json`。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-169">For example, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="ca0a2-170">如果响应不是 JSON 格式，则内容验证会引发 <xref:System.NotSupportedException>。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-170">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="ca0a2-171">在下面的示例中，天气预测数据请求的 URI 终结点拼写错误。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-171">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="ca0a2-172">URI 应该为 `WeatherForecast`，但在单元格中显示为 `WeatherForcast`（缺少“e”）。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-172">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="ca0a2-173"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> 调用需要返回 JSON，但服务器为服务器上的未处理异常返回 HTML，其中 `Content-Type` 为 `text/html`。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-173">The <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="ca0a2-174">由于找不到路径并且中间件无法为请求提供页面或视图，因此服务器上发生未处理的异常。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-174">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="ca0a2-175">在客户端上的 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 中，当响应内容验证为非 JSON 时，将引发 <xref:System.NotSupportedException>。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-175">In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="ca0a2-176">在 `catch` 块中捕获到异常，其中自定义逻辑可以记录错误或向用户显示友好错误消息：</span><span class="sxs-lookup"><span data-stu-id="ca0a2-176">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

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
> <span data-ttu-id="ca0a2-177">前面示例代码为了方便本文演示。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-177">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="ca0a2-178">即使终结点不存在或服务器上发生未处理的异常，也可将 web API 服务器应用配置为返回 JSON。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-178">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled excpetion on the server occurs.</span></span>

<span data-ttu-id="ca0a2-179">有关详细信息，请参阅 <xref:blazor/fundamentals/handle-errors>。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-179">For more information, see <xref:blazor/fundamentals/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="ca0a2-180">跨域资源共享 (CORS)</span><span class="sxs-lookup"><span data-stu-id="ca0a2-180">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="ca0a2-181">浏览器安全可防止网页向不同域（而不是向网页提供服务的域）进行请求。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-181">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="ca0a2-182">此限制称为同域策略。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-182">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="ca0a2-183">同域策略可防止恶意站点从另一站点读取敏感数据。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-183">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="ca0a2-184">若要从浏览器向具有不同源的终结点进行请求，终结点必须启用[跨域资源共享 (CORS)](https://www.w3.org/TR/cors/)。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-184">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="ca0a2-185">[Blazor WebAssembly 示例应用 (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) 演示如何在调用 Web API 组件 (`Pages/CallWebAPI.razor`) 中使用 CORS。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-185">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (`Pages/CallWebAPI.razor`).</span></span>

<span data-ttu-id="ca0a2-186">若要允许其他站点对应用进行跨域资源共享 (CORS) 请求，请参阅 <xref:security/cors>。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-186">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ca0a2-187">其他资源</span><span class="sxs-lookup"><span data-stu-id="ca0a2-187">Additional resources</span></span>

* <span data-ttu-id="ca0a2-188"><xref:blazor/security/webassembly/additional-scenarios>：包括对使用 <xref:System.Net.Http.HttpClient> 发出安全 Web API 请求的介绍。</span><span class="sxs-lookup"><span data-stu-id="ca0a2-188"><xref:blazor/security/webassembly/additional-scenarios>: Includes coverage on using <xref:System.Net.Http.HttpClient> to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="ca0a2-189">Kestrel HTTPS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="ca0a2-189">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="ca0a2-190">W3C 上的跨域资源共享 (CORS)</span><span class="sxs-lookup"><span data-stu-id="ca0a2-190">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
