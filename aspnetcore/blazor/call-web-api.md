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
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>从 ASP.NET Core Blazor 调用 web API

作者： [Luke Latham](https://github.com/guardrex)、 [Daniel Roth](https://github.com/danroth27)和[Juan De la Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor WebAssembly apps 使用预先配置的 @no__t 0 服务来调用 web Api。 撰写请求，其中可以包含 JavaScript [FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)选项，使用 Blazor JSON 帮助器或 <xref:System.Net.Http.HttpRequestMessage>。

Blazor 服务器应用使用 <xref:System.Net.Http.IHttpClientFactory> 创建的 @no__t 的实例来调用 web Api。 有关详细信息，请参阅 <xref:fundamentals/http-requests> 。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

有关 Blazor WebAssembly 示例，请参阅示例应用中的以下组件：

* 调用 Web API （*Pages/CallWebAPI*）
* HTTP 请求测试器（*组件/HTTPRequestTester*）

## <a name="httpclient-and-json-helpers"></a>HttpClient 和 JSON 帮助器

在 Blazor WebAssembly apps 中， [HttpClient](xref:fundamentals/http-requests)作为预配置服务提供，用于向源服务器发送请求。 若要使用 `HttpClient` JSON 帮助程序，请将包引用添加到 @no__t。 @no__t 0 和 JSON 帮助器也用于调用第三方 web API 终结点。 `HttpClient` 是使用浏览器[提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API)实现的，并且受到其限制，包括强制实施相同的源策略。

客户端的基址设置为源服务器的地址。 使用 `@inject` 指令插入 `HttpClient` 实例：

```cshtml
@using System.Net.Http
@inject HttpClient Http
```

在下面的示例中，Todo web API 处理创建、读取、更新和删除（CRUD）操作。 这些示例基于一个 @no__t 0 类，该类用于存储：

* ID （`Id`，`long`） @no__t 项的唯一 ID。
* 名称（`Name`，`string`） @no__t 项的2名称。
* Status （`IsComplete`，`bool`） &ndash; 指示是否已完成 Todo 项。

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

JSON helper 方法将请求发送到 URI （以下示例中的 web API）并处理响应：

* `GetJsonAsync` &ndash; 发送 HTTP GET 请求并分析 JSON 响应正文以创建对象。

  在下面的代码中，组件显示 `_todoItems`。 当组件完成呈现（[OnInitializedAsync](xref:blazor/components#lifecycle-methods)）时，将触发 `GetTodoItems` 方法。 有关完整示例，请参阅示例应用。

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostJsonAsync` &ndash; 发送 HTTP POST 请求（包括 JSON 编码的内容），并分析 JSON 响应正文以创建对象。

  在下面的代码中，`_newItemName` 由组件的绑定元素提供。 @No__t-0 方法通过选择 @no__t 1 元素触发。 有关完整示例，请参阅示例应用。

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

* `PutJsonAsync` &ndash; 发送 HTTP PUT 请求，其中包括 JSON 编码的内容。

  在下面的代码中，@no__t 的 @no__t 值为-1，`IsCompleted` 由组件的绑定元素提供。 当在 UI 的另一部分中选择项并且调用 `EditItem` 时，将设置项的 `Id`。 通过选择 "保存 `<button>`" 元素触发 `SaveItem` 方法。 有关完整示例，请参阅示例应用。

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

<xref:System.Net.Http> 包含用于发送 HTTP 请求和接收 HTTP 响应的附加扩展方法。 [HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*)用于将 HTTP DELETE 请求发送到 web API。

在下面的代码中，Delete `<button>` 元素调用 `DeleteItem` 方法。 绑定 `<input>` 元素提供要删除的项的 @no__t。 有关完整示例，请参阅示例应用。

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

## <a name="cross-origin-resource-sharing-cors"></a>跨域资源共享（CORS）

浏览器安全性可防止网页向其他域发出请求，而不是提供给网页的域。 此限制称为*相同源策略*。 同一源策略可防止恶意站点读取另一个站点中的敏感数据。 若要将浏览器请求发送到具有不同源的终结点，*终结点*必须启用[跨域资源共享（CORS）](https://www.w3.org/TR/cors/)。

示例应用演示了如何在调用 Web API 组件（*Pages/CallWebAPI*）中使用 CORS。

若要允许其他站点对应用进行跨域资源共享（CORS）请求，请参阅 <xref:security/cors>。

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>带有 Fetch API 请求选项的 HttpClient 和 HttpRequestMessage

在 Blazor WebAssembly 应用中的 WebAssembly 上运行时，使用[HttpClient](xref:fundamentals/http-requests)并 <xref:System.Net.Http.HttpRequestMessage> 自定义请求。 例如，可以指定请求 URI、HTTP 方法以及任何所需的请求标头。

使用请求的 `WebAssemblyHttpMessageHandler.FetchArgs` 属性向基础 JavaScript[提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API)提供请求选项。 如以下示例中所示，将 `credentials` 属性设置为以下任意值：

* `FetchCredentialsOption.Include` （"include"） &ndash; 建议浏览器发送凭据（如 cookie 或 HTTP 身份验证标头），即使对于跨源请求也是如此。 仅当 CORS 策略配置为允许凭据时才允许。
* `FetchCredentialsOption.Omit` （"省略"） &ndash; 建议浏览器从不发送凭据（如 cookie 或 HTTP 身份验证标头）。
* `FetchCredentialsOption.SameOrigin` （"相同源"） &ndash; 建议仅当目标 URL 与调用应用程序位于同一源时，才向浏览器发送凭据（如 cookie 或 HTTP 身份验证标头）。

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

有关 Fetch API 选项的详细信息，@no__t 请参阅 0MDN web 文档：WindowOrWorkerGlobalScope （）:P arameters @ no__t-0。

在 CORS 请求上发送凭据（授权 cookie/标头）时，CORS 策略必须允许 @no__t 0 标头。

以下策略包括的配置：

* 请求来源（`http://localhost:5000`，`https://localhost:5001`）。
* 任何方法（谓词）。
* @no__t 0 和 @no__t 标头。 若要允许自定义标头（例如 `x-custom-header`），请在调用 @no__t 时列出标头。
* 由客户端 JavaScript 代码（@no__t 设置为 `include`）设置的凭据。

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

有关详细信息，请参阅 <xref:security/cors> 和示例应用的 HTTP 请求测试器组件（*组件/HTTPRequestTester*）。

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Kestrel HTTPS 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [W3C 上的跨域资源共享（CORS）](https://www.w3.org/TR/cors/)
