---
title: ASP.NET Core Blazor WebAssembly 性能最佳做法
author: pranavkm
description: 用于提高 ASP.NET Core Blazor WebAssembly 应用性能并避免常见性能问题的提示。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: c5169231eec67a43830f761bff7585deff774613
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103212"
---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a>ASP.NET Core Blazor WebAssembly 性能最佳做法

作者：[Pranav Krishnamoorthy](https://github.com/pranavkm)

本文提供了 ASP.NET Core Blazor WebAssembly 性能最佳做法的准则。

## <a name="avoid-unnecessary-component-renders"></a>避免不必要的组件呈现

借助 Blazor 的差分算法，当算法感知到组件未更改时，不用重新呈现组件。 可重写 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> 来实现对组件呈现的精细控制。

如果创作了一个仅限 UI 的组件，且该组件在最初呈现后从未更改，则请将 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 配置为返回 `false`：

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

大多数应用不需要精细控制，但是 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 也可用于选择性地呈现响应 UI 事件的组件。

如下示例中：

* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 被重写并设置为 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 字段的值，该字段最初在组件加载时为 `false`。
* 选中该按钮后，<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 设置为 `true`，这将强制组件重新呈现并显示更新后的 `currentCount`。
* 重新呈现后，<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 立即将 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 的值设置回 `false`，以防止在下次选中该按钮之前进一步重新呈现。

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

有关详细信息，请参阅 <xref:blazor/components/lifecycle#after-component-render>。

## <a name="virtualize-re-usable-fragments"></a>虚拟化可重用的片段

组件提供了一种方便的方法来生成代码和标记的可重用片段。 通常，我们建议创作最符合应用要求的单个组件。 需要注意的是，每个附加的子组件都会增加呈现父组件所需的总时间。 对于大多数应用，额外的开销可以忽略不计。 生成大量组件的应用应考虑使用策略来减少处理开销，例如限制所呈现的组件的数量。

例如，如果某网格或列表要呈现数百个包含组件的行，则该网格或列表呈现时会大量使用处理器。 请考虑将网格或列表布局虚拟化，以便在任何给定时间都只呈现其中的一部分组件。 有关组件子集呈现的示例，请参阅[虚拟化示例应用（aspnet/示例 GitHub 存储库）](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization)中的以下组件：

* `Virtualize` 组件（[共享/Virtualize.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)）：用 C# 语言编写的一种组件，实现了 <xref:Microsoft.AspNetCore.Components.ComponentBase> 来根据用户滚动呈现一组天气数据行。
* `FetchData` 组件（[页面/FetchData.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)）：使用 `Virtualize` 组件一次显示 25 行天气数据。

## <a name="avoid-javascript-interop-to-marshal-data"></a>不要用 JavaScript 互操作来封送数据

在 Blazor WebAssembly 中，JavaScript (JS) 互操作调用必须遍历 WebAssembly-JS 边界。 如果跨两个上下文序列化和反序列化内容，会产生应用处理开销。 频繁的 JS 互操作调用通常会对性能产生负面影响。 为了减少数据的跨边界封送，请确定应用能否将许多小的有效负载合并到一个大的有效负载中，以避免在 WebAssembly 与 JS 之间频繁切换上下文。

## <a name="use-systemtextjson"></a>使用 System.Text.Json

Blazor 的 JS 互操作实现依赖于 <xref:System.Text.Json> - 这是一个性能高但内存分配较低的 JSON 序列化库。 与添加一个或多个备用 JSON 库相比，使用 <xref:System.Text.Json> 不会增加应用有效负载的大小。

有关迁移指南，请参阅[如何从 Newtonsoft.Json 迁移到 System.Text.Json](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to)。

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a>根据需要使用同步的和未封装的 JS 互操作 API

Blazor WebAssembly 额外提供了两个 <xref:Microsoft.JSInterop.IJSRuntime> 版本，而 Blazor 服务器应用只有一个版本：

* <xref:Microsoft.JSInterop.IJSInProcessRuntime> 允许同步调用 JS 互操作调用，其开销低于异步版本：

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

* <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> 允许使用未封装的 JS 互操作调用：

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
  > 虽然使用 <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> 这种 JS 互操作方法的开销最小，但与这些 API 交互所需的 JavaScript API 目前没有文档记录，而且可能会在将来的版本中出现中断性变更。

## <a name="reduce-app-size"></a>减小应用大小

### <a name="intermediate-language-il-linking"></a>中间语言 (IL) 链接

通过[链接 Blazor WebAssembly 应用](xref:blazor/host-and-deploy/configure-linker)，可剪裁应用二进制文件中未使用的代码来减小应用的大小。 默认情况下，仅在 `Release` 配置中生成时才启用链接器。 要从此中受益，请使用 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令发布应用用于部署，并将 [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) 选项设置为 `Release`：

```dotnetcli
dotnet publish -c Release
```

### <a name="compression"></a>压缩

发布 Blazor WebAssembly 应用时，将在发布过程中对输出内容进行静态压缩，从而减小应用的大小，并免去运行时压缩的开销。 Blazor 依赖服务器来执行内容协商和提供静态压缩的文件。

部署应用后，请验证该应用是否提供压缩的文件。 检查浏览器开发人员工具中的“网络”选项卡，并验证文件是否具有 `Content-Encoding: br` 或 `Content-Encoding: gz`。 如果主机未提供压缩的文件，请按照 <xref:blazor/host-and-deploy/webassembly#compression> 中的说明操作。

### <a name="disable-unused-features"></a>禁用未使用的功能

Blazor WebAssembly 的运行时包含以下 .NET 功能；如果应用不需要这些功能就能减少有效负载的大小，可将它们禁用：

* 包含数据文件来确保时区信息正确。 如果应用不需要此功能，请考虑通过将应用项目文件中的 `BlazorEnableTimeZoneSupport` MSBuild 属性设置为 `false` 来禁用它：

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* 包括排序规则信息来确保 <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> 之类的 API 正常工作。 如果确定应用不需要排序规则数据，请考虑通过将应用项目文件中的 `BlazorWebAssemblyPreserveCollationData` MSBuild 属性设置为 `false` 来禁用它：

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
