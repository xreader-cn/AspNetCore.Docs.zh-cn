---
title: ASP.NET Core Blazor 托管模型配置
author: guardrex
description: 了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: bd44643877e45c5b48b0972bcc2f637fbc5d98f2
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78646788"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="d34b9-103">ASP.NET Core Blazor 托管模型配置</span><span class="sxs-lookup"><span data-stu-id="d34b9-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="d34b9-104">作者：[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="d34b9-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="d34b9-105">本文介绍了托管模型配置。</span><span class="sxs-lookup"><span data-stu-id="d34b9-105">This article covers hosting model configuration.</span></span>

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a><span data-ttu-id="d34b9-106">Blazor 服务器</span><span class="sxs-lookup"><span data-stu-id="d34b9-106">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="d34b9-107">反映 UI 中的连接状态</span><span class="sxs-lookup"><span data-stu-id="d34b9-107">Reflect the connection state in the UI</span></span>

<span data-ttu-id="d34b9-108">如果客户端检测到连接已丢失，在客户端尝试重新连接时会向用户显示默认 UI。</span><span class="sxs-lookup"><span data-stu-id="d34b9-108">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="d34b9-109">如果重新连接失败，则会向用户提供重试选项。</span><span class="sxs-lookup"><span data-stu-id="d34b9-109">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="d34b9-110">若要自定义 UI，请在 _Host.cshtml Razor 页面的 `<body>` 中定义一个 `id` 为 `components-reconnect-modal` 的元素  ：</span><span class="sxs-lookup"><span data-stu-id="d34b9-110">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="d34b9-111">下表介绍应用于 `components-reconnect-modal` 元素的 CSS 类。</span><span class="sxs-lookup"><span data-stu-id="d34b9-111">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="d34b9-112">CSS 类</span><span class="sxs-lookup"><span data-stu-id="d34b9-112">CSS class</span></span>                       | <span data-ttu-id="d34b9-113">指示&hellip;</span><span class="sxs-lookup"><span data-stu-id="d34b9-113">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="d34b9-114">连接已丢失。</span><span class="sxs-lookup"><span data-stu-id="d34b9-114">A lost connection.</span></span> <span data-ttu-id="d34b9-115">客户端正在尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="d34b9-115">The client is attempting to reconnect.</span></span> <span data-ttu-id="d34b9-116">显示模式。</span><span class="sxs-lookup"><span data-stu-id="d34b9-116">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="d34b9-117">将为服务器重新建立活动连接。</span><span class="sxs-lookup"><span data-stu-id="d34b9-117">An active connection is re-established to the server.</span></span> <span data-ttu-id="d34b9-118">隐藏模式。</span><span class="sxs-lookup"><span data-stu-id="d34b9-118">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="d34b9-119">重新连接失败，可能是由于网络故障引起的。</span><span class="sxs-lookup"><span data-stu-id="d34b9-119">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="d34b9-120">若要尝试重新连接，请调用 `window.Blazor.reconnect()`。</span><span class="sxs-lookup"><span data-stu-id="d34b9-120">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="d34b9-121">已拒绝重新连接。</span><span class="sxs-lookup"><span data-stu-id="d34b9-121">Reconnection rejected.</span></span> <span data-ttu-id="d34b9-122">已达到服务器，但拒绝连接，服务器上的用户状态丢失。</span><span class="sxs-lookup"><span data-stu-id="d34b9-122">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="d34b9-123">若要重载应用，请调用 `location.reload()`。</span><span class="sxs-lookup"><span data-stu-id="d34b9-123">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="d34b9-124">当出现以下情况时，可能会导致此连接状态：</span><span class="sxs-lookup"><span data-stu-id="d34b9-124">This connection state may result when:</span></span><ul><li><span data-ttu-id="d34b9-125">服务器端线路发生故障。</span><span class="sxs-lookup"><span data-stu-id="d34b9-125">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="d34b9-126">客户端断开连接的时间足以使服务器删除用户的状态。</span><span class="sxs-lookup"><span data-stu-id="d34b9-126">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="d34b9-127">已释放用户正在与之交互的组件的实例。</span><span class="sxs-lookup"><span data-stu-id="d34b9-127">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="d34b9-128">服务器已重启，或者应用的工作进程被回收。</span><span class="sxs-lookup"><span data-stu-id="d34b9-128">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="d34b9-129">呈现模式</span><span class="sxs-lookup"><span data-stu-id="d34b9-129">Render mode</span></span>

<span data-ttu-id="d34b9-130">默认情况下，Blazor 服务器应用设置为在客户端与服务器建立连接之前在服务器上预呈现 UI。</span><span class="sxs-lookup"><span data-stu-id="d34b9-130">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="d34b9-131">这是在 _Host.cshtml Razor 页面中设置的  ：</span><span class="sxs-lookup"><span data-stu-id="d34b9-131">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="d34b9-132">`RenderMode` 配置组件是否：</span><span class="sxs-lookup"><span data-stu-id="d34b9-132">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="d34b9-133">在页面中预呈现。</span><span class="sxs-lookup"><span data-stu-id="d34b9-133">Is prerendered into the page.</span></span>
* <span data-ttu-id="d34b9-134">在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="d34b9-134">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="d34b9-135">描述</span><span class="sxs-lookup"><span data-stu-id="d34b9-135">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="d34b9-136">将组件呈现为静态 HTML，并包含 Blazor 服务器应用的标记。</span><span class="sxs-lookup"><span data-stu-id="d34b9-136">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="d34b9-137">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="d34b9-137">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="d34b9-138">呈现 Blazor 服务器应用的标记。</span><span class="sxs-lookup"><span data-stu-id="d34b9-138">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="d34b9-139">不包括组件的输出。</span><span class="sxs-lookup"><span data-stu-id="d34b9-139">Output from the component isn't included.</span></span> <span data-ttu-id="d34b9-140">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="d34b9-140">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="d34b9-141">将组件呈现为静态 HTML。</span><span class="sxs-lookup"><span data-stu-id="d34b9-141">Renders the component into static HTML.</span></span> |

<span data-ttu-id="d34b9-142">不支持从静态 HTML 页面呈现服务器组件。</span><span class="sxs-lookup"><span data-stu-id="d34b9-142">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="d34b9-143">从 Razor 页面和视图呈现有状态的交互式组件</span><span class="sxs-lookup"><span data-stu-id="d34b9-143">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="d34b9-144">可以将有状态的交互式组件添加到 Razor 页面或视图。</span><span class="sxs-lookup"><span data-stu-id="d34b9-144">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="d34b9-145">呈现页面或视图时：</span><span class="sxs-lookup"><span data-stu-id="d34b9-145">When the page or view renders:</span></span>

* <span data-ttu-id="d34b9-146">该组件通过页面或视图预呈现。</span><span class="sxs-lookup"><span data-stu-id="d34b9-146">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="d34b9-147">用于预呈现的初始组件状态丢失。</span><span class="sxs-lookup"><span data-stu-id="d34b9-147">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="d34b9-148">建立 SignalR 连接时，将创建新的组件状态。</span><span class="sxs-lookup"><span data-stu-id="d34b9-148">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="d34b9-149">以下 Razor 页面将呈现 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="d34b9-149">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="d34b9-150">从 Razor 页面和视图呈现非交互式组件</span><span class="sxs-lookup"><span data-stu-id="d34b9-150">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="d34b9-151">在以下 Razor 页面中，使用以下格式通过指定的初始值静态呈现 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="d34b9-151">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="d34b9-152">由于 `MyComponent` 是以静态方式呈现的，因此该组件不能是交互式的。</span><span class="sxs-lookup"><span data-stu-id="d34b9-152">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="d34b9-153">为 Blazor 服务器应用配置 SignalR 客户端</span><span class="sxs-lookup"><span data-stu-id="d34b9-153">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="d34b9-154">有时，需要配置 Blazor 服务器应用使用的 SignalR 客户端。</span><span class="sxs-lookup"><span data-stu-id="d34b9-154">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="d34b9-155">例如，可能需要在 SignalR 客户端上配置日志记录以诊断连接问题。</span><span class="sxs-lookup"><span data-stu-id="d34b9-155">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="d34b9-156">若要在 Pages/_Host.cshtml 文件中配置 SignalR 客户端，请执行以下操作  ：</span><span class="sxs-lookup"><span data-stu-id="d34b9-156">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="d34b9-157">将 `autostart="false"` 属性添加到 `blazor.server.js` 脚本的 `<script>` 标记中。</span><span class="sxs-lookup"><span data-stu-id="d34b9-157">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="d34b9-158">调用 `Blazor.start` 并传入指定 SignalR 生成器的配置对象。</span><span class="sxs-lookup"><span data-stu-id="d34b9-158">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```
