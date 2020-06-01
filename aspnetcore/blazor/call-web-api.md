---
<span data-ttu-id="c4f8b-101">title:“从 ASP.NET Core Blazor WebAssembly 调用 Web API”author: description:“了解如何使用 JSON 帮助程序从 Blazor WebAssembly 应用调用 Web API，包括发出跨域资源共享 (CORS) 请求。”</span><span class="sxs-lookup"><span data-stu-id="c4f8b-101">title: 'Call a web API from ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to call a web API from a Blazor WebAssembly app using JSON helpers, including making cross-origin resource sharing (CORS) requests.'</span></span>
<span data-ttu-id="c4f8b-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c4f8b-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c4f8b-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c4f8b-103">'Blazor'</span></span>
- <span data-ttu-id="c4f8b-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c4f8b-104">'Identity'</span></span>
- <span data-ttu-id="c4f8b-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c4f8b-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="c4f8b-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c4f8b-106">'Razor'</span></span>
- <span data-ttu-id="c4f8b-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c4f8b-107">'SignalR' uid:</span></span> 

---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="c4f8b-108">从 ASP.NET Core Blazor 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="c4f8b-108">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="c4f8b-109">作者：[Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27) 和 [Juan De la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="c4f8b-109">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

<span data-ttu-id="c4f8b-110">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 应用使用预配置的 <xref:System.Net.Http.HttpClient> 服务调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-110">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured <xref:System.Net.Http.HttpClient> service.</span></span> <span data-ttu-id="c4f8b-111">使用 Blazor JSON 帮助程序或通过 <xref:System.Net.Http.HttpRequestMessage> 撰写请求，其中可以包含 JavaScript [Fetch API ](https://developer.mozilla.org/docs/Web/API/Fetch_API) 选项。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-111">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="c4f8b-112">Blazor WebAssembly 应用中的 <xref:System.Net.Http.HttpClient> 服务侧重于使请求返回源服务器。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-112">The <xref:System.Net.Http.HttpClient> service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="c4f8b-113">本主题中的指导仅适用于 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-113">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="c4f8b-114">[Blazor 服务器](xref:blazor/hosting-models#blazor-server)应用使用 <xref:System.Net.Http.HttpClient> 实例（通常是使用 <xref:System.Net.Http.IHttpClientFactory> 创建）调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-114">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="c4f8b-115">本主题中的指导不适用于 Blazor 服务器应用。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-115">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="c4f8b-116">开发 Blazor 服务器应用时，请按照 <xref:fundamentals/http-requests> 中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-116">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="c4f8b-117">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）：选择“BlazorWebAssemblySample”应用。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)): Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="c4f8b-118">请查看 BlazorWebAssemblySample 示例应用中的以下组件：</span><span class="sxs-lookup"><span data-stu-id="c4f8b-118">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="c4f8b-119">调用 Web API (Pages/CallWebAPI.razor)</span><span class="sxs-lookup"><span data-stu-id="c4f8b-119">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="c4f8b-120">HTTP 请求测试器 (Components/HTTPRequestTester.razor)</span><span class="sxs-lookup"><span data-stu-id="c4f8b-120">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="c4f8b-121">package</span><span class="sxs-lookup"><span data-stu-id="c4f8b-121">Packages</span></span>

<span data-ttu-id="c4f8b-122">在项目文件中引用 [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-122">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="c4f8b-123">添加 HttpClient 服务</span><span class="sxs-lookup"><span data-stu-id="c4f8b-123">Add the HttpClient service</span></span>

<span data-ttu-id="c4f8b-124">在 `Program.Main` 中个，如果 <xref:System.Net.Http.HttpClient> 服务尚不存在，则添加它：</span><span class="sxs-lookup"><span data-stu-id="c4f8b-124">In `Program.Main`, add an <xref:System.Net.Http.HttpClient> service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="c4f8b-125">HttpClient 和 JSON 帮助程序</span><span class="sxs-lookup"><span data-stu-id="c4f8b-125">HttpClient and JSON helpers</span></span>

<span data-ttu-id="c4f8b-126">在 Blazor WebAssembly 应用中，[HttpClient](xref:fundamentals/http-requests) 作为预配置服务提供，用于使请求返回源服务器。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-126">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="c4f8b-127">默认情况下，Blazor 服务器应用不包含 <xref:System.Net.Http.HttpClient> 服务。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-127">A Blazor Server app doesn't include an <xref:System.Net.Http.HttpClient> service by default.</span></span> <span data-ttu-id="c4f8b-128">使用 [HttpClient 工厂基础结构](xref:fundamentals/http-requests)向应用提供 <xref:System.Net.Http.HttpClient>。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-128">Provide an <xref:System.Net.Http.HttpClient> to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="c4f8b-129"><xref:System.Net.Http.HttpClient> 和 JSON 帮助程序还用于调用第三方 Web API 终结点。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-129"><xref:System.Net.Http.HttpClient> and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="c4f8b-130"><xref:System.Net.Http.HttpClient> 使用浏览器 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 来实现，受其限制制约，包括强制实施相同初始策略。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-130"><xref:System.Net.Http.HttpClient> is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="c4f8b-131">客户端的基址设置为原始服务器的地址。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-131">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="c4f8b-132">使用 [`@inject`](xref:mvc/views/razor#inject) 指令插入 <xref:System.Net.Http.HttpClient> 实例：</span><span class="sxs-lookup"><span data-stu-id="c4f8b-132">Inject an <xref:System.Net.Http.HttpClient> instance using the [`@inject`](xref:mvc/views/razor#inject) directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="c4f8b-133">在下面的示例中，Todo Web API 处理创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-133">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="c4f8b-134">这些示例基于存储以下内容的 `TodoItem` 类：</span><span class="sxs-lookup"><span data-stu-id="c4f8b-134">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="c4f8b-135">ID（`Id`、`long`）：项的唯一 ID。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-135">ID (`Id`, `long`): Unique ID of the item.</span></span>
* <span data-ttu-id="c4f8b-136">名称（`Name`、`string`）：项的名称。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-136">Name (`Name`, `string`): Name of the item.</span></span>
* <span data-ttu-id="c4f8b-137">状态（`IsComplete`、`bool`）：指明 Todo 项是否已完成。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-137">Status (`IsComplete`, `bool`): Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="c4f8b-138">JSON 帮助程序方法将请求发送到 URI（以下示例中的 Web API）并处理响应：</span><span class="sxs-lookup"><span data-stu-id="c4f8b-138">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="c4f8b-139"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>：发送 HTTP GET 请求，并分析 JSON 响应正文来创建对象。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-139"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="c4f8b-140">在下面的代码中，`todoItems` 由组件显示。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-140">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="c4f8b-141">当组件完成呈现 ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)) 时，会触发 `GetTodoItems` 方法。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-141">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="c4f8b-142">有关完整的示例，请参阅示例应用。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-142">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="c4f8b-143"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>：发送 HTTP POST 请求（包括 JSON 编码的内容），并分析 JSON 响应正文来创建对象。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-143"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="c4f8b-144">在下面的代码中，`newItemName` 由组件的绑定元素提供。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-144">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="c4f8b-145">通过选择 `<button>` 元素来触发 `AddItem` 方法。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-145">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="c4f8b-146">有关完整的示例，请参阅示例应用。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-146">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="c4f8b-147">调用 <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> 会返回 <xref:System.Net.Http.HttpResponseMessage>。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-147">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="c4f8b-148">若要对响应消息中的 JSON 内容进行反序列化处理，请使用 `ReadFromJsonAsync<T>` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c4f8b-148">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="c4f8b-149"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>：发送 HTTP PUT 请求（包括 JSON 编码的内容）。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-149"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="c4f8b-150">在下面的代码中，`Name` 和 `IsCompleted` 的 `editItem` 值由组件的绑定元素提供。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-150">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="c4f8b-151">当在 UI 的另一个部分中选择项并调用 `EditItem` 时，会设置项的 `Id`。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-151">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="c4f8b-152">通过选择 Save `<button>` 元素来触发 `SaveItem` 方法。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-152">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="c4f8b-153">有关完整的示例，请参阅示例应用。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-153">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="c4f8b-154">调用 <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> 会返回 <xref:System.Net.Http.HttpResponseMessage>。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-154">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="c4f8b-155">若要对响应消息中的 JSON 内容进行反序列化处理，请使用 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c4f8b-155">To deserialize the JSON content from the response message, use the <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="c4f8b-156"><xref:System.Net.Http> 包括用于发送 HTTP 请求和接收 HTTP 响应的附加扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-156"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="c4f8b-157"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> 用于将 HTTP DELETE 请求发送到 Web API。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-157"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="c4f8b-158">在下面的代码中，Delete `<button>` 元素调用 `DeleteItem` 方法。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-158">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="c4f8b-159">绑定 `<input>` 元素提供要删除的项的 `id`。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-159">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="c4f8b-160">有关完整的示例，请参阅示例应用。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-160">See the sample app for a complete example.</span></span>

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

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="c4f8b-161">已命名的 HttpClient 和 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="c4f8b-161">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="c4f8b-162">支持已命名的 <xref:System.Net.Http.HttpClient> 的 <xref:System.Net.Http.IHttpClientFactory> 服务和配置。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-162"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="c4f8b-163">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="c4f8b-163">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="c4f8b-164">`FetchData` component (*Pages/FetchData.razor*)：</span><span class="sxs-lookup"><span data-stu-id="c4f8b-164">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="c4f8b-165">类型化 HttpClient</span><span class="sxs-lookup"><span data-stu-id="c4f8b-165">Typed HttpClient</span></span>

<span data-ttu-id="c4f8b-166">类型化 <xref:System.Net.Http.HttpClient> 使用应用的一个或多个 <xref:System.Net.Http.HttpClient> 实例（默认或命名）从一个或多个 web API 终结点返回数据。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-166">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="c4f8b-167">WeatherForecastClient.cs：</span><span class="sxs-lookup"><span data-stu-id="c4f8b-167">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="c4f8b-168">`Program.Main` (*Program.cs*)：</span><span class="sxs-lookup"><span data-stu-id="c4f8b-168">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="c4f8b-169">组件插入类型化的 <xref:System.Net.Http.HttpClient> 来调用 web API。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-169">Components inject the typed <xref:System.Net.Http.HttpClient> to call the web API.</span></span>

<span data-ttu-id="c4f8b-170">`FetchData` component (*Pages/FetchData.razor*)：</span><span class="sxs-lookup"><span data-stu-id="c4f8b-170">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="handle-errors"></a><span data-ttu-id="c4f8b-171">处理错误</span><span class="sxs-lookup"><span data-stu-id="c4f8b-171">Handle errors</span></span>

<span data-ttu-id="c4f8b-172">如果在与 web API 交互时出现错误，开发人员代码可以处理这些错误。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-172">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="c4f8b-173">例如，<xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> 需要服务器 API 的 JSON 响应，其中 `Content-Type` 为 `application/json`。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-173">For example, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="c4f8b-174">如果响应不是 JSON 格式，则内容验证会引发 <xref:System.NotSupportedException>。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-174">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="c4f8b-175">在下面的示例中，天气预测数据请求的 URI 终结点拼写错误。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-175">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="c4f8b-176">URI 应该为 `WeatherForecast`，但在单元格中显示为 `WeatherForcast`（缺少“e”）。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-176">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="c4f8b-177"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> 调用需要返回 JSON，但服务器为服务器上的未处理异常返回 HTML，其中 `Content-Type` 为 `text/html`。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-177">The <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="c4f8b-178">由于找不到路径并且中间件无法为请求提供页面或视图，因此服务器上发生未处理的异常。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-178">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="c4f8b-179">在客户端上的 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 中，当响应内容验证为非 JSON 时，将引发 <xref:System.NotSupportedException>。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-179">In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="c4f8b-180">在 `catch` 块中捕获到异常，其中自定义逻辑可以记录错误或向用户显示友好错误消息：</span><span class="sxs-lookup"><span data-stu-id="c4f8b-180">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

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
> <span data-ttu-id="c4f8b-181">前面示例代码为了方便本文演示。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-181">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="c4f8b-182">即使终结点不存在或服务器上发生未处理的异常，也可将 web API 服务器应用配置为返回 JSON。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-182">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled excpetion on the server occurs.</span></span>

<span data-ttu-id="c4f8b-183">有关详细信息，请参阅 <xref:blazor/handle-errors>。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-183">For more information, see <xref:blazor/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="c4f8b-184">跨域资源共享 (CORS)</span><span class="sxs-lookup"><span data-stu-id="c4f8b-184">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="c4f8b-185">浏览器安全可防止网页向不同域（而不是向网页提供服务的域）进行请求。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-185">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="c4f8b-186">此限制称为同域策略。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-186">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="c4f8b-187">同域策略可防止恶意站点从另一站点读取敏感数据。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-187">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="c4f8b-188">若要从浏览器向具有不同源的终结点进行请求，终结点必须启用[跨域资源共享 (CORS)](https://www.w3.org/TR/cors/)。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-188">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="c4f8b-189">[Blazor WebAssembly 示例应用 (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) 演示如何在调用 Web API 组件 (Pages/CallWebAPI.razor) 中使用 CORS。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-189">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="c4f8b-190">若要允许其他站点对应用进行跨域资源共享 (CORS) 请求，请参阅 <xref:security/cors>。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-190">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c4f8b-191">其他资源</span><span class="sxs-lookup"><span data-stu-id="c4f8b-191">Additional resources</span></span>

* <span data-ttu-id="c4f8b-192"><xref:security/blazor/webassembly/additional-scenarios>：包括对使用 <xref:System.Net.Http.HttpClient> 发出安全 Web API 请求的介绍。</span><span class="sxs-lookup"><span data-stu-id="c4f8b-192"><xref:security/blazor/webassembly/additional-scenarios>: Includes coverage on using <xref:System.Net.Http.HttpClient> to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="c4f8b-193">Kestrel HTTPS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="c4f8b-193">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="c4f8b-194">W3C 上的跨域资源共享 (CORS)</span><span class="sxs-lookup"><span data-stu-id="c4f8b-194">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
