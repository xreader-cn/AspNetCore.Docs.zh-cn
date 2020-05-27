---
标题： "ASP.NET Core Blazor WebAssembly 性能最佳做法作者：说明：在 ASP.NET Core WebAssembly 应用中提高性能 Blazor 并避免常见性能问题的提示。"
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a>ASP.NET Core Blazor WebAssembly 性能最佳做法

作者： [Pranav Krishnamoorthy](https://github.com/pranavkm)

本文提供 ASP.NET Core Blazor WebAssembly 性能最佳做法的准则。

## <a name="avoid-unnecessary-component-renders"></a>避免不必要的组件呈现

Blazor如果算法感觉组件未发生更改，则其比较算法可避免 rerendering 组件。 重写 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> 以对组件呈现进行精细控制。

如果创作一个仅限 UI 的组件，而该组件在初始呈现后永不更改，则将配置 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 为返回 `false` ：

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

大多数应用不需要细化控制，但 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 也可用于有选择地呈现响应 UI 事件的组件。

如下示例中：

* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>被重写并设置为该字段的值 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> ，这是最初 `false` 加载组件时的值。
* 选择该按钮时，将 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 设置为 `true` ，这会强制组件诸如此类已更新 `currentCount` 。
* 在 rerendering 后， <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 将返回的值设置 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 为， `false` 以防止在下一次选中按钮之前 rerendering 进一步。

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

有关详细信息，请参阅 <xref:blazor/lifecycle#after-component-render>。

## <a name="virtualize-re-usable-fragments"></a>虚拟化可重复使用的片段

组件提供一种方便的方法来生成可重复使用的代码片段和标记。 通常，我们建议你创作最符合应用要求的单个组件。 需要注意的一点是，每个附加的子组件都有一个用于呈现父组件的总时间。 对于大多数应用程序，额外的开销可忽略不计。 生成大量组件的应用应考虑使用策略来减少处理开销，例如限制呈现的组件数。

例如，呈现数百个包含组件的行的网格或列表是要呈现的处理器密集型。 考虑虚拟化网格或列表布局，以便在任意给定时间只呈现部分组件。 有关组件子集呈现的示例，请参阅[虚拟化示例应用（aspnet/示例 GitHub 存储库）](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization)中的以下组件：

* `Virtualize`组件（[共享/虚拟化 razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)）：用 c # 编写的组件，该组件实现 <xref:Microsoft.AspNetCore.Components.ComponentBase> 以基于用户滚动呈现一组天气数据行。
* `FetchData`组件（[Pages/FetchData](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)）：使用 `Virtualize` 组件一次显示25行天气数据。

## <a name="avoid-javascript-interop-to-marshal-data"></a>避免 JavaScript 互操作对数据进行封送处理

在 Blazor WebAssembly 中，JavaScript （JS）互操作调用必须遍历 WebAssembly 边界。 在两个上下文之间序列化和反序列化内容会为应用创建处理开销。 经常会对性能产生不利影响。 为了减少跨边界数据的封送，确定应用是否可以将许多小型有效负载整合到单个大型负载中，以避免 WebAssembly 与 JS 之间发生的大量上下文切换。

## <a name="use-systemtextjson"></a>使用 system.exception

Blazor的 JS 互操作实现依赖于 <xref:System.Text.Json> ，后者是内存分配较少的高性能 JSON 序列化库。 通过 <xref:System.Text.Json> 添加一个或多个备用 JSON 库，使用不会导致其他应用负载大小。

有关迁移指南，请参阅[如何从 newtonsoft.json 迁移到 system.object](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to)。

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a>在适当的位置使用同步和打乱 JS 互操作 Api

BlazorWebAssembly 提供了 <xref:Microsoft.JSInterop.IJSRuntime> 可用于服务器应用的单一版本的其他两个版本 Blazor ：

* <xref:Microsoft.JSInterop.IJSInProcessRuntime>允许以同步方式调用 JS 互操作调用，其开销低于异步版本：

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>允许打乱 JS 互操作调用：

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > 虽然使用的 <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> 最小开销是 JS 互操作方法，但当前未记录与这些 api 交互所需的 JavaScript api，但在将来的版本中可能会发生重大更改。

## <a name="reduce-app-size"></a>降低应用程序大小

### <a name="intermediate-language-il-linking"></a>中间语言（IL）链接

[链接 BlazorWebAssembly 应用](xref:host-and-deploy/blazor/configure-linker)通过修整应用二进制文件中未使用的代码来减少应用的大小。 默认情况下，仅当在配置中生成时才启用链接器 `Release` 。 为此，请使用[dotnet publish](/dotnet/core/tools/dotnet-publish)命令发布应用，并将[-c |--configuration](/dotnet/core/tools/dotnet-publish#options)选项设置为 `Release` ：

```dotnetcli
dotnet publish -c Release
```

### <a name="disable-unused-features"></a>禁用未使用的功能

BlazorWebAssembly 的运行时包括以下 .NET 功能，如果应用程序不要求这些功能的负载大小较小，则可以禁用这些功能：

* 包含数据文件以使时区信息正确。 如果应用不需要此功能，请考虑通过将 `BlazorEnableTimeZoneSupport` 应用的项目文件中的 MSBuild 属性设置为来禁用它 `false` ：

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* 包括排序规则信息以使 Api <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> 正常工作。 如果确定应用不需要排序规则数据，请考虑通过将 `BlazorWebAssemblyPreserveCollationData` 应用的项目文件中的 MSBuild 属性设置为来禁用它 `false` ：

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
