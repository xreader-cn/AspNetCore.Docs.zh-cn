---
<span data-ttu-id="10234-101">title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-101">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-103">'Blazor'</span></span>
- <span data-ttu-id="10234-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-104">'Identity'</span></span>
- <span data-ttu-id="10234-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-106">'Razor'</span></span>
- <span data-ttu-id="10234-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="10234-108">ASP.NET Core Blazor 事件处理</span><span class="sxs-lookup"><span data-stu-id="10234-108">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="10234-109">作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="10234-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="10234-110"> 组件提供事件处理功能。</span><span class="sxs-lookup"><span data-stu-id="10234-110"> components provide event handling features.</span></span> <span data-ttu-id="10234-111">对于具有委托类型值的名为 [`@on{EVENT}`](xref:mvc/views/razor#onevent)（例如 `@onclick`）的 HTML 元素属性，Razor 组件将此属性的值视为事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="10234-111">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="10234-112">在 UI 中选择该按钮时，以下代码调用 `UpdateHeading` 方法：</span><span class="sxs-lookup"><span data-stu-id="10234-112">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="10234-113">UI 中的该复选框更改时，以下代码调用 `CheckChanged` 方法：</span><span class="sxs-lookup"><span data-stu-id="10234-113">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="10234-114">事件处理程序也可以是异步处理程序，并返回 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="10234-114">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="10234-115">无需手动调用 [StateHasChanged](xref:blazor/lifecycle#state-changes)。</span><span class="sxs-lookup"><span data-stu-id="10234-115">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="10234-116">异常发生时，它们将被记录下来。</span><span class="sxs-lookup"><span data-stu-id="10234-116">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="10234-117">在下面的示例中，选择该按钮时，异步调用 `UpdateHeading`：</span><span class="sxs-lookup"><span data-stu-id="10234-117">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="10234-118">事件参数类型</span><span class="sxs-lookup"><span data-stu-id="10234-118">Event argument types</span></span>

<span data-ttu-id="10234-119">对于某些事件，允许使用事件参数类型。</span><span class="sxs-lookup"><span data-stu-id="10234-119">For some events, event argument types are permitted.</span></span> <span data-ttu-id="10234-120">仅当方法中使用了事件类型时，才需要在方法调用中指定该事件类型。</span><span class="sxs-lookup"><span data-stu-id="10234-120">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="10234-121">支持的 <xref:System.EventArgs> 显示在下表中。</span><span class="sxs-lookup"><span data-stu-id="10234-121">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="10234-122">事件</span><span class="sxs-lookup"><span data-stu-id="10234-122">Event</span></span>            | <span data-ttu-id="10234-123">类</span><span class="sxs-lookup"><span data-stu-id="10234-123">Class</span></span>                | <span data-ttu-id="10234-124">DOM 事件和说明</span><span class="sxs-lookup"><span data-stu-id="10234-124">DOM events and notes</span></span> |
| ---
<span data-ttu-id="10234-125">title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-125">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-126">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-126">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-127">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-127">'Blazor'</span></span>
- <span data-ttu-id="10234-128">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-128">'Identity'</span></span>
- <span data-ttu-id="10234-129">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-129">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-130">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-130">'Razor'</span></span>
- <span data-ttu-id="10234-131">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-131">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10234-132">title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-132">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-133">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-133">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-134">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-134">'Blazor'</span></span>
- <span data-ttu-id="10234-135">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-135">'Identity'</span></span>
- <span data-ttu-id="10234-136">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-137">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-137">'Razor'</span></span>
- <span data-ttu-id="10234-138">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10234-139">title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-139">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-140">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-140">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-141">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-141">'Blazor'</span></span>
- <span data-ttu-id="10234-142">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-142">'Identity'</span></span>
- <span data-ttu-id="10234-143">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-143">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-144">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-144">'Razor'</span></span>
- <span data-ttu-id="10234-145">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-145">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10234-146">title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-146">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-148">'Blazor'</span></span>
- <span data-ttu-id="10234-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-149">'Identity'</span></span>
- <span data-ttu-id="10234-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-151">'Razor'</span></span>
- <span data-ttu-id="10234-152">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10234-153">title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-153">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-155">'Blazor'</span></span>
- <span data-ttu-id="10234-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-156">'Identity'</span></span>
- <span data-ttu-id="10234-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-158">'Razor'</span></span>
- <span data-ttu-id="10234-159">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10234-160">title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-160">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-162">'Blazor'</span></span>
- <span data-ttu-id="10234-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-163">'Identity'</span></span>
- <span data-ttu-id="10234-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-165">'Razor'</span></span>
- <span data-ttu-id="10234-166">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-166">'SignalR' uid:</span></span> 

<span data-ttu-id="10234-167">-------- | --- title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-167">-------- | --- title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-169">'Blazor'</span></span>
- <span data-ttu-id="10234-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-170">'Identity'</span></span>
- <span data-ttu-id="10234-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-172">'Razor'</span></span>
- <span data-ttu-id="10234-173">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10234-174">title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-174">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-176">'Blazor'</span></span>
- <span data-ttu-id="10234-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-177">'Identity'</span></span>
- <span data-ttu-id="10234-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-179">'Razor'</span></span>
- <span data-ttu-id="10234-180">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10234-181">title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-181">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-183">'Blazor'</span></span>
- <span data-ttu-id="10234-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-184">'Identity'</span></span>
- <span data-ttu-id="10234-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-186">'Razor'</span></span>
- <span data-ttu-id="10234-187">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-187">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10234-188">title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-188">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-189">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-189">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-190">'Blazor'</span></span>
- <span data-ttu-id="10234-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-191">'Identity'</span></span>
- <span data-ttu-id="10234-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-193">'Razor'</span></span>
- <span data-ttu-id="10234-194">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10234-195">title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-195">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-196">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-196">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-197">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-197">'Blazor'</span></span>
- <span data-ttu-id="10234-198">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-198">'Identity'</span></span>
- <span data-ttu-id="10234-199">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-199">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-200">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-200">'Razor'</span></span>
- <span data-ttu-id="10234-201">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-201">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10234-202">title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-202">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-203">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-203">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-204">'Blazor'</span></span>
- <span data-ttu-id="10234-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-205">'Identity'</span></span>
- <span data-ttu-id="10234-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-207">'Razor'</span></span>
- <span data-ttu-id="10234-208">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10234-209">title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-209">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-210">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-210">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-211">'Blazor'</span></span>
- <span data-ttu-id="10234-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-212">'Identity'</span></span>
- <span data-ttu-id="10234-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-214">'Razor'</span></span>
- <span data-ttu-id="10234-215">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-215">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10234-216">title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-216">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-217">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-217">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-218">'Blazor'</span></span>
- <span data-ttu-id="10234-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-219">'Identity'</span></span>
- <span data-ttu-id="10234-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-221">'Razor'</span></span>
- <span data-ttu-id="10234-222">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-222">'SignalR' uid:</span></span> 

<span data-ttu-id="10234-223">---------- | --- title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-223">---------- | --- title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-224">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-224">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-225">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-225">'Blazor'</span></span>
- <span data-ttu-id="10234-226">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-226">'Identity'</span></span>
- <span data-ttu-id="10234-227">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-227">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-228">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-228">'Razor'</span></span>
- <span data-ttu-id="10234-229">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-229">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10234-230">title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-230">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-231">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-231">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-232">'Blazor'</span></span>
- <span data-ttu-id="10234-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-233">'Identity'</span></span>
- <span data-ttu-id="10234-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-235">'Razor'</span></span>
- <span data-ttu-id="10234-236">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10234-237">title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-237">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-238">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-238">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-239">'Blazor'</span></span>
- <span data-ttu-id="10234-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-240">'Identity'</span></span>
- <span data-ttu-id="10234-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-242">'Razor'</span></span>
- <span data-ttu-id="10234-243">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10234-244">title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-244">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-245">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-245">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-246">'Blazor'</span></span>
- <span data-ttu-id="10234-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-247">'Identity'</span></span>
- <span data-ttu-id="10234-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-249">'Razor'</span></span>
- <span data-ttu-id="10234-250">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10234-251">title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-251">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-252">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-252">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-253">'Blazor'</span></span>
- <span data-ttu-id="10234-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-254">'Identity'</span></span>
- <span data-ttu-id="10234-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-256">'Razor'</span></span>
- <span data-ttu-id="10234-257">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10234-258">title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-258">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-259">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-259">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-260">'Blazor'</span></span>
- <span data-ttu-id="10234-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-261">'Identity'</span></span>
- <span data-ttu-id="10234-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-263">'Razor'</span></span>
- <span data-ttu-id="10234-264">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10234-265">title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-265">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-266">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-266">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-267">'Blazor'</span></span>
- <span data-ttu-id="10234-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-268">'Identity'</span></span>
- <span data-ttu-id="10234-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-270">'Razor'</span></span>
- <span data-ttu-id="10234-271">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="10234-272">title:'ASP.NET Core Blazor 事件处理' author: description:'了解 Blazor 的事件处理功能，包括事件参数类型、事件回调和管理默认浏览器事件。'</span><span class="sxs-lookup"><span data-stu-id="10234-272">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="10234-273">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="10234-273">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="10234-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10234-274">'Blazor'</span></span>
- <span data-ttu-id="10234-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10234-275">'Identity'</span></span>
- <span data-ttu-id="10234-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10234-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="10234-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10234-277">'Razor'</span></span>
- <span data-ttu-id="10234-278">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="10234-278">'SignalR' uid:</span></span> 

<span data-ttu-id="10234-279">---------- | | 剪贴板        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`、`oncopy`、`onpaste` | | 拖动             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`、`ondragstart`、`ondragenter`、`ondragleave`、`ondragover`、`ondrop`、`ondragend`</span><span class="sxs-lookup"><span data-stu-id="10234-279">---------- | | Clipboard        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` | | Drag             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="10234-280"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> 和 <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> 保留拖动的项数据。</span><span class="sxs-lookup"><span data-stu-id="10234-280"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> <span data-ttu-id="10234-281">| | 错误            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | 事件            | <xref:System.EventArgs> | *常规*</span><span class="sxs-lookup"><span data-stu-id="10234-281">| | Error            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | Event            | <xref:System.EventArgs> | *General*</span></span><br><span data-ttu-id="10234-282">`onactivate`、`onbeforeactivate`、`onbeforedeactivate`、`ondeactivate`、`onended`、`onfullscreenchange`、`onfullscreenerror`、`onloadeddata`、`onloadedmetadata`、`onpointerlockchange`、`onpointerlockerror`、`onreadystatechange`、`onscroll`</span><span class="sxs-lookup"><span data-stu-id="10234-282">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="10234-283">*剪贴板*</span><span class="sxs-lookup"><span data-stu-id="10234-283">*Clipboard*</span></span><br><span data-ttu-id="10234-284">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="10234-284">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="10234-285">*输入*</span><span class="sxs-lookup"><span data-stu-id="10234-285">*Input*</span></span><br><span data-ttu-id="10234-286">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="10234-286">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="10234-287">*介质*</span><span class="sxs-lookup"><span data-stu-id="10234-287">*Media*</span></span><br><span data-ttu-id="10234-288">`oncanplay`、`oncanplaythrough`、`oncuechange`、`ondurationchange`、`onemptied`、`onpause`、`onplay`、`onplaying`、`onratechange`、`onseeked`、`onseeking`、`onstalled`、`onstop`、`onsuspend`、`ontimeupdate`、`onvolumechange`、`onwaiting`</span><span class="sxs-lookup"><span data-stu-id="10234-288">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="10234-289"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> 保留属性，以配置事件名称和事件参数类型之间的映射。</span><span class="sxs-lookup"><span data-stu-id="10234-289"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> <span data-ttu-id="10234-290">| | 焦点            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`、`onblur`、`onfocusin`、`onfocusout`</span><span class="sxs-lookup"><span data-stu-id="10234-290">| | Focus            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="10234-291">不包含对 `relatedTarget` 的支持。</span><span class="sxs-lookup"><span data-stu-id="10234-291">Doesn't include support for `relatedTarget`.</span></span> <span data-ttu-id="10234-292">| | 输入            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`、`oninput` | | 键盘         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`、`onkeypress`、`onkeyup` | |鼠标            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`、`oncontextmenu`、`ondblclick`、`onmousedown`、`onmouseup`、`onmouseover`、`onmousemove`、`onmouseout` | | 鼠标指针    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`、`onpointerup`、`onpointercancel`、`onpointermove`、`onpointerover`、`onpointerout`、`onpointerenter`、`onpointerleave`、`ongotpointercapture`、`onlostpointercapture` | | 鼠标滚轮      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`、`onmousewheel` | | 进度         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`、`onload`、`onloadend`、`onloadstart`、`onprogress`、`ontimeout` | | 触控            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`、`ontouchend`、`ontouchmove`、`ontouchenter`、`ontouchleave`、`ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="10234-292">| | Input            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` | | Keyboard         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` | | Mouse            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` | | Mouse pointer    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` | | Mouse wheel      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` | | Progress         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` | | Touch            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="10234-293"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> 表示触控敏感型设备上的单个接触点。</span><span class="sxs-lookup"><span data-stu-id="10234-293"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="10234-294">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="10234-294">For more information, see the following resources:</span></span>

* <span data-ttu-id="10234-295">[ASP.NET Core 引用源 (dotnet/aspnetcore release/3.1 branch) 中的 EventArgs 类](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web)。</span><span class="sxs-lookup"><span data-stu-id="10234-295">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="10234-296">[MDN Web 文档：GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers)：包含关于哪些 HTML 元素支持每个 DOM 事件的信息。</span><span class="sxs-lookup"><span data-stu-id="10234-296">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="10234-297">Lambda 表达式</span><span class="sxs-lookup"><span data-stu-id="10234-297">Lambda expressions</span></span>

<span data-ttu-id="10234-298">还可以使用 [Lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)：</span><span class="sxs-lookup"><span data-stu-id="10234-298">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="10234-299">关闭附加值通常很方便，例如在循环访问一组元素时。</span><span class="sxs-lookup"><span data-stu-id="10234-299">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="10234-300">下面的示例创建了三个按钮。在 UI 中选中这些按钮时，每个按钮都调用 `UpdateHeading`传递事件参数 (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) 和其按钮编号 (`buttonNumber`)：</span><span class="sxs-lookup"><span data-stu-id="10234-300">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="10234-301">不要在 Lambda 表达式中直接使用 `for` 循环中的循环变量 (`i`)。</span><span class="sxs-lookup"><span data-stu-id="10234-301">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="10234-302">否则，所有 Lambda 表达式将使用相同的变量，导致所有 Lambda 中的 `i` 值相同。</span><span class="sxs-lookup"><span data-stu-id="10234-302">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="10234-303">始终在本地变量中捕获其值（在前面的示例中为 `buttonNumber`），然后使用它。</span><span class="sxs-lookup"><span data-stu-id="10234-303">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="10234-304">EventCallback</span><span class="sxs-lookup"><span data-stu-id="10234-304">EventCallback</span></span>

<span data-ttu-id="10234-305">嵌套组件的一个常见场景：希望在子组件事件发生时运行父组件的方法。</span><span class="sxs-lookup"><span data-stu-id="10234-305">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="10234-306">子组件中发生的 `onclick` 事件是一个常见用例。</span><span class="sxs-lookup"><span data-stu-id="10234-306">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="10234-307">若要跨组件公开事件，请使用 <xref:Microsoft.AspNetCore.Components.EventCallback>。</span><span class="sxs-lookup"><span data-stu-id="10234-307">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="10234-308">父组件可向子组件的 <xref:Microsoft.AspNetCore.Components.EventCallback> 分配回调方法。</span><span class="sxs-lookup"><span data-stu-id="10234-308">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="10234-309">示例应用 (Components/ChildComponent.razor) 中的 `ChildComponent` 演示如何设置按钮的 `onclick` 处理程序以从示例的 `ParentComponent` 接收 <xref:Microsoft.AspNetCore.Components.EventCallback> 委托。</span><span class="sxs-lookup"><span data-stu-id="10234-309">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="10234-310"><xref:Microsoft.AspNetCore.Components.EventCallback> 是用 `MouseEventArgs` 键入的，这适用于来自外围设备的 `onclick` 事件：</span><span class="sxs-lookup"><span data-stu-id="10234-310">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="10234-311">`ParentComponent` 将子级的 <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) 设置为其 `ShowMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="10234-311">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="10234-312">Pages/ParentComponent：</span><span class="sxs-lookup"><span data-stu-id="10234-312">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="10234-313">在 `ChildComponent` 中选择该按钮时：</span><span class="sxs-lookup"><span data-stu-id="10234-313">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="10234-314">调用 `ParentComponent` 的 `ShowMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="10234-314">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="10234-315">`messageText` 更新并显示在 `ParentComponent` 中。</span><span class="sxs-lookup"><span data-stu-id="10234-315">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="10234-316">回调方法 (`ShowMessage`) 中不需要对 [StateHasChanged](xref:blazor/lifecycle#state-changes) 的调用。</span><span class="sxs-lookup"><span data-stu-id="10234-316">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="10234-317">自动调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 以重新呈现 `ParentComponent`，就像子事件触发组件重新呈现于在子级中执行的事件处理程序中一样。</span><span class="sxs-lookup"><span data-stu-id="10234-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="10234-318"><xref:Microsoft.AspNetCore.Components.EventCallback> 和 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 允许异步委托。</span><span class="sxs-lookup"><span data-stu-id="10234-318"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="10234-319"><xref:Microsoft.AspNetCore.Components.EventCallback%601> 为强类型，需要特定的参数类型。</span><span class="sxs-lookup"><span data-stu-id="10234-319"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="10234-320"><xref:Microsoft.AspNetCore.Components.EventCallback> 为弱类型，允许任何参数类型。</span><span class="sxs-lookup"><span data-stu-id="10234-320"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="10234-321">使用 <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> 调用 <xref:Microsoft.AspNetCore.Components.EventCallback> 或 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 并等待 <xref:System.Threading.Tasks.Task>：</span><span class="sxs-lookup"><span data-stu-id="10234-321">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="10234-322">使用 <xref:Microsoft.AspNetCore.Components.EventCallback> 和 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 处理事件和绑定组件参数。</span><span class="sxs-lookup"><span data-stu-id="10234-322">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="10234-323">优先使用强类型 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 而非 <xref:Microsoft.AspNetCore.Components.EventCallback>。</span><span class="sxs-lookup"><span data-stu-id="10234-323">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="10234-324"><xref:Microsoft.AspNetCore.Components.EventCallback%601> 向用户提供更好的组件错误反馈。</span><span class="sxs-lookup"><span data-stu-id="10234-324"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="10234-325">与其他 UI 事件处理程序类似，指定事件参数是可选操作。</span><span class="sxs-lookup"><span data-stu-id="10234-325">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="10234-326">当没有值传递给回调时，使用 <xref:Microsoft.AspNetCore.Components.EventCallback>。</span><span class="sxs-lookup"><span data-stu-id="10234-326">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="10234-327">阻止默认操作</span><span class="sxs-lookup"><span data-stu-id="10234-327">Prevent default actions</span></span>

<span data-ttu-id="10234-328">使用 [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) 指令属性可阻止事件的默认操作。</span><span class="sxs-lookup"><span data-stu-id="10234-328">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="10234-329">在输入设备上选择某个键并且元素焦点位于某个文本框上时，浏览器通常在该文本框中显示该键的字符。</span><span class="sxs-lookup"><span data-stu-id="10234-329">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="10234-330">在下面的示例中，通过指定 `@onkeypress:preventDefault` 指令属性来阻止默认行为。</span><span class="sxs-lookup"><span data-stu-id="10234-330">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="10234-331">计数器递增，且 + 键不会捕获到 `<input>` 元素的值中：</span><span class="sxs-lookup"><span data-stu-id="10234-331">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

<span data-ttu-id="10234-332">指定没有值的 `@on{EVENT}:preventDefault` 属性等同于 `@on{EVENT}:preventDefault="true"`。</span><span class="sxs-lookup"><span data-stu-id="10234-332">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="10234-333">属性的值也可以是表达式。</span><span class="sxs-lookup"><span data-stu-id="10234-333">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="10234-334">在下面的示例中，`shouldPreventDefault` 是设置为 `true` 或 `false` 的 `bool` 字段：</span><span class="sxs-lookup"><span data-stu-id="10234-334">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="10234-335">不需要事件处理程序来阻止默认操作。</span><span class="sxs-lookup"><span data-stu-id="10234-335">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="10234-336">事件处理程序和阻止默认操作场景可以独立使用。</span><span class="sxs-lookup"><span data-stu-id="10234-336">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="10234-337">停止事件传播</span><span class="sxs-lookup"><span data-stu-id="10234-337">Stop event propagation</span></span>

<span data-ttu-id="10234-338">使用 [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) 指令属性来停止事件传播。</span><span class="sxs-lookup"><span data-stu-id="10234-338">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="10234-339">在下例中，选中复选框可阻止第二个子级 `<div>` 中的单击事件传播到父级 `<div>`：</span><span class="sxs-lookup"><span data-stu-id="10234-339">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
