---
title: 在 ASP.NET Core 中使用集线器筛选器SignalR
author: brecon
description: 了解如何在 ASP.NET Core 中使用集线器筛选器 SignalR 。
monikerRange: '>= aspnetcore-5.0'
ms.author: brecon
ms.custom: mvc
ms.date: 05/22/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/hub-filters
ms.openlocfilehash: afdb52039c0eff53a421038518c687c78e1d509b
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756062"
---
# <a name="use-hub-filters-in-aspnet-core-signalr"></a><span data-ttu-id="5f883-103">在 ASP.NET Core 中使用集线器筛选器SignalR</span><span class="sxs-lookup"><span data-stu-id="5f883-103">Use hub filters in ASP.NET Core SignalR</span></span>

<span data-ttu-id="5f883-104">中心筛选器：</span><span class="sxs-lookup"><span data-stu-id="5f883-104">Hub filters:</span></span>

* <span data-ttu-id="5f883-105">在 ASP.NET Core 5.0 或更高版本中可用。</span><span class="sxs-lookup"><span data-stu-id="5f883-105">Are available in ASP.NET Core 5.0 or later.</span></span>
* <span data-ttu-id="5f883-106">允许在客户端调用中心方法之前和之后运行逻辑。</span><span class="sxs-lookup"><span data-stu-id="5f883-106">Allow logic to run before and after hub methods are invoked by clients.</span></span>

<span data-ttu-id="5f883-107">本文提供了有关编写和使用集线器筛选器的指南。</span><span class="sxs-lookup"><span data-stu-id="5f883-107">This article provides guidance for writing and using hub filters.</span></span>

## <a name="configure-hub-filters"></a><span data-ttu-id="5f883-108">配置中心筛选器</span><span class="sxs-lookup"><span data-stu-id="5f883-108">Configure hub filters</span></span>

<span data-ttu-id="5f883-109">可以全局或按中心类型应用中心筛选器。</span><span class="sxs-lookup"><span data-stu-id="5f883-109">Hub filters can be applied globally or per hub type.</span></span> <span data-ttu-id="5f883-110">添加筛选器的顺序是筛选器的运行顺序。</span><span class="sxs-lookup"><span data-stu-id="5f883-110">The order in which filters are added is the order in which the filters run.</span></span> <span data-ttu-id="5f883-111">全局集线器筛选器在本地中心筛选器之前运行。</span><span class="sxs-lookup"><span data-stu-id="5f883-111">Global hub filters run before local hub filters.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(options =>
    {
        // Global filters will run first
        options.AddFilter<CustomFilter>();
    }).AddHubOptions<ChatHub>(options =>
    {
        // Local filters will run second
        options.AddFilter<CustomFilter2>();
    });
}
```

<span data-ttu-id="5f883-112">可以通过以下方式之一添加集线器筛选器：</span><span class="sxs-lookup"><span data-stu-id="5f883-112">A hub filter can be added in one of the following ways:</span></span>

* <span data-ttu-id="5f883-113">按具体类型添加筛选器：</span><span class="sxs-lookup"><span data-stu-id="5f883-113">Add a filter by concrete type:</span></span>

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    <span data-ttu-id="5f883-114">这将从依赖关系注入（DI）或已激活的类型中解决。</span><span class="sxs-lookup"><span data-stu-id="5f883-114">This will be resolved from dependency injection (DI) or type activated.</span></span>

* <span data-ttu-id="5f883-115">按运行时类型添加筛选器：</span><span class="sxs-lookup"><span data-stu-id="5f883-115">Add a filter by runtime type:</span></span>

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    <span data-ttu-id="5f883-116">这将从 DI 或激活的类型进行解析。</span><span class="sxs-lookup"><span data-stu-id="5f883-116">This will be resolved from DI or type activated.</span></span>

* <span data-ttu-id="5f883-117">按实例添加筛选器：</span><span class="sxs-lookup"><span data-stu-id="5f883-117">Add a filter by instance:</span></span>

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    <span data-ttu-id="5f883-118">此实例将像单一实例一样使用。</span><span class="sxs-lookup"><span data-stu-id="5f883-118">This instance will be used like a singleton.</span></span> <span data-ttu-id="5f883-119">所有集线器方法调用都将使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="5f883-119">All hub method invocations will use the same instance.</span></span>

<span data-ttu-id="5f883-120">中心筛选器按中心调用创建和释放。</span><span class="sxs-lookup"><span data-stu-id="5f883-120">Hub filters are created and disposed per hub invocation.</span></span> <span data-ttu-id="5f883-121">如果要将全局状态存储在筛选器中或不是状态，请将中心筛选器类型作为单一实例添加到 DI，以获得更好的性能。</span><span class="sxs-lookup"><span data-stu-id="5f883-121">If you want to store global state in the filter, or no state, add the hub filter type to DI as a singleton for better performance.</span></span> <span data-ttu-id="5f883-122">或者，将筛选器添加为实例（如果可以）。</span><span class="sxs-lookup"><span data-stu-id="5f883-122">Alternatively, add the filter as an instance if you can.</span></span>

## <a name="create-hub-filters"></a><span data-ttu-id="5f883-123">创建中心筛选器</span><span class="sxs-lookup"><span data-stu-id="5f883-123">Create hub filters</span></span>

<span data-ttu-id="5f883-124">通过声明从继承的类来创建筛选器 `IHubFilter` ，并添加 `InvokeMethodAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="5f883-124">Create a filter by declaring a class that inherits from `IHubFilter`, and add the `InvokeMethodAsync` method.</span></span> <span data-ttu-id="5f883-125">此外，还 `OnConnectedAsync` `OnDisconnectedAsync` 可以选择实现来 `OnConnectedAsync` 分别包装和 `OnDisconnectedAsync` 中心方法。</span><span class="sxs-lookup"><span data-stu-id="5f883-125">There is also `OnConnectedAsync` and `OnDisconnectedAsync` that can optionally be implemented to wrap the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods respectively.</span></span>

```csharp
public class CustomFilter : IHubFilter
{
    public async ValueTask<object> InvokeMethodAsync(
        HubInvocationContext invocationContext, Func<HubInvocationContext, ValueTask<object>> next)
    {
        Console.WriteLine($"Calling hub method '{invocationContext.HubMethodName}'");
        try
        {
            return await next(invocationContext);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Exception calling '{invocationContext.HubMethodName}'");
            throw ex;
        }
    }

    // Optional method
    public Task OnConnectedAsync(HubLifetimeContext context, Func<HubLifetimeContext, Task> next)
    {
        return next(context);
    }

    // Optional method
    public Task OnDisconnectedAsync(
        HubLifetimeContext context, Exception exception, Func<HubLifetimeContext, Exception, Task> next)
    {
        return next(context, exception);
    }
}
```

<span data-ttu-id="5f883-126">筛选器与中间件非常类似。</span><span class="sxs-lookup"><span data-stu-id="5f883-126">Filters are very similar to middleware.</span></span> <span data-ttu-id="5f883-127">`next`方法调用下一个筛选器。</span><span class="sxs-lookup"><span data-stu-id="5f883-127">The `next` method invokes the next filter.</span></span> <span data-ttu-id="5f883-128">最终筛选器将调用中心方法。</span><span class="sxs-lookup"><span data-stu-id="5f883-128">The final filter will invoke the hub method.</span></span> <span data-ttu-id="5f883-129">筛选器还可以存储在 `next` 调用中心方法之前调用的结果，然后再从返回结果 `next` 。</span><span class="sxs-lookup"><span data-stu-id="5f883-129">Filters can also store the result from awaiting `next` and run logic after the hub method has been called before returning the result from `next`.</span></span>

<span data-ttu-id="5f883-130">若要跳过筛选器中的集线器方法调用，请引发类型为的异常， `HubException` 而不是调用 `next` 。</span><span class="sxs-lookup"><span data-stu-id="5f883-130">To skip a hub method invocation in a filter, throw an exception of type `HubException` instead of calling `next`.</span></span> <span data-ttu-id="5f883-131">如果要求，客户端将收到错误消息。</span><span class="sxs-lookup"><span data-stu-id="5f883-131">The client will receive an error if it was expecting a result.</span></span>

## <a name="use-hub-filters"></a><span data-ttu-id="5f883-132">使用中心筛选器</span><span class="sxs-lookup"><span data-stu-id="5f883-132">Use hub filters</span></span>

<span data-ttu-id="5f883-133">编写筛选器逻辑时，请尝试使用集线器方法上的属性而不是检查中心方法名称，使其成为泛型。</span><span class="sxs-lookup"><span data-stu-id="5f883-133">When writing the filter logic, try to make it generic by using attributes on hub methods instead of checking for hub method names.</span></span>

<span data-ttu-id="5f883-134">假设有一个筛选器，它将检查禁止的短语的集线器方法参数，并替换它所找到的任何短语 `***` 。</span><span class="sxs-lookup"><span data-stu-id="5f883-134">Consider a filter that will check a hub method argument for banned phrases and replace any phrases it finds with `***`.</span></span>
<span data-ttu-id="5f883-135">对于本示例，假定定义了一个 `LanguageFilterAttribute` 类。</span><span class="sxs-lookup"><span data-stu-id="5f883-135">For this example, assume a `LanguageFilterAttribute` class is defined.</span></span> <span data-ttu-id="5f883-136">类具有一个名为 `FilterArgument` 的属性，该属性可在使用特性时进行设置。</span><span class="sxs-lookup"><span data-stu-id="5f883-136">The class has a property named `FilterArgument` that can be set when using the attribute.</span></span>

1. <span data-ttu-id="5f883-137">将属性放置在具有要清理的字符串参数的 hub 方法上：</span><span class="sxs-lookup"><span data-stu-id="5f883-137">Place the attribute on the hub method that has a string argument to be cleaned:</span></span>

    ```csharp
    public class ChatHub
    {
        [LanguageFilter(filterArgument: 0)]
        public async Task SendMessage(string message, string username)
        {
            await Clients.All.SendAsync("SendMessage", $"{username} says: {message}");
        }
    }
    ```

1. <span data-ttu-id="5f883-138">定义一个中心筛选器来检查属性，并将集线器方法参数中的禁止的短语替换为 `***` ：</span><span class="sxs-lookup"><span data-stu-id="5f883-138">Define a hub filter to check for the attribute and replace banned phrases in a hub method argument with `***`:</span></span>

    ```csharp
    public class LanguageFilter : IHubFilter
    {
        // populated from a file or inline
        private List<string> bannedPhrases = new List<string> { "async void", ".Result" };

        public async ValueTask<object> InvokeMethodAsync(HubInvocationContext invocationContext, 
            Func<HubInvocationContext, ValueTask<object>> next)
        {
            var languageFilter = (LanguageFilterAttribute)Attribute.GetCustomAttribute(
                invocationContext.HubMethod, typeof(LanguageFilterAttribute));
            if (languageFilter != null &&
                invocationContext.HubMethodArguments.Count > languageFilter.FilterArgument &&
                invocationContext.HubMethodArguments[languageFilter.FilterArgument] is string str)
            {
                foreach (var bannedPhrase in bannedPhrases)
                {
                    str = str.Replace(bannedPhrase, "***");
                }

                arguments = invocationContext.HubMethodArguments.ToArray();
                arguments[languageFilter.FilterArgument] = str;
                invocationContext = new HubInvocationContext(invocationContext.Context,
                    invocationContext.ServiceProvider,
                    invocationContext.Hub,
                    invocationContext.HubMethod,
                    arguments);
            }

            return await next(invocationContext);
        }
    }
    ```

1. <span data-ttu-id="5f883-139">在方法中注册集线器筛选器 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="5f883-139">Register the hub filter in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5f883-140">为了避免为每个调用重新初始化禁止使用的词组列表，将中心筛选器注册为单一实例：</span><span class="sxs-lookup"><span data-stu-id="5f883-140">To avoid reinitializing the banned phrases list for every invocation, the hub filter is registered as a singleton:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR(hubOptions =>
        {
            hubOptions.AddFilter<LanguageFilter>();
        });
    
        services.AddSingleton<LanguageFilter>();
    }
    ```

## <a name="the-hubinvocationcontext-object"></a><span data-ttu-id="5f883-141">HubInvocationContext 对象</span><span class="sxs-lookup"><span data-stu-id="5f883-141">The HubInvocationContext object</span></span>

<span data-ttu-id="5f883-142">`HubInvocationContext`包含当前集线器方法调用的信息。</span><span class="sxs-lookup"><span data-stu-id="5f883-142">The `HubInvocationContext` contains information for the current hub method invocation.</span></span>

| <span data-ttu-id="5f883-143">属性</span><span class="sxs-lookup"><span data-stu-id="5f883-143">Property</span></span> | <span data-ttu-id="5f883-144">说明</span><span class="sxs-lookup"><span data-stu-id="5f883-144">Description</span></span> | <span data-ttu-id="5f883-145">类型</span><span class="sxs-lookup"><span data-stu-id="5f883-145">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="5f883-146">`HubCallerContext`包含有关连接的信息。</span><span class="sxs-lookup"><span data-stu-id="5f883-146">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="5f883-147">正在用于此集线器方法调用的集线器的实例。</span><span class="sxs-lookup"><span data-stu-id="5f883-147">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `HubMethodName` | <span data-ttu-id="5f883-148">正在调用的集线器方法的名称。</span><span class="sxs-lookup"><span data-stu-id="5f883-148">The name of the hub method being invoked.</span></span> | `string` |
| `HubMethodArguments` | <span data-ttu-id="5f883-149">要传递到中心方法的参数的列表。</span><span class="sxs-lookup"><span data-stu-id="5f883-149">The list of arguments being passed to the hub method.</span></span> | `IReadOnlyList<string>` |
| `ServiceProvider` | <span data-ttu-id="5f883-150">此中心方法调用的作用域内服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="5f883-150">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |
| `HubMethod` | <span data-ttu-id="5f883-151">集线器方法信息。</span><span class="sxs-lookup"><span data-stu-id="5f883-151">The hub method information.</span></span> | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a><span data-ttu-id="5f883-152">HubLifetimeContext 对象</span><span class="sxs-lookup"><span data-stu-id="5f883-152">The HubLifetimeContext object</span></span>

<span data-ttu-id="5f883-153">`HubLifetimeContext`包含 `OnConnectedAsync` 和集线器方法的信息 `OnDisconnectedAsync` 。</span><span class="sxs-lookup"><span data-stu-id="5f883-153">The `HubLifetimeContext` contains information for the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods.</span></span>

| <span data-ttu-id="5f883-154">属性</span><span class="sxs-lookup"><span data-stu-id="5f883-154">Property</span></span> | <span data-ttu-id="5f883-155">说明</span><span class="sxs-lookup"><span data-stu-id="5f883-155">Description</span></span> | <span data-ttu-id="5f883-156">类型</span><span class="sxs-lookup"><span data-stu-id="5f883-156">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="5f883-157">`HubCallerContext`包含有关连接的信息。</span><span class="sxs-lookup"><span data-stu-id="5f883-157">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="5f883-158">正在用于此集线器方法调用的集线器的实例。</span><span class="sxs-lookup"><span data-stu-id="5f883-158">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `ServiceProvider` | <span data-ttu-id="5f883-159">此中心方法调用的作用域内服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="5f883-159">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |

## <a name="authorization-and-filters"></a><span data-ttu-id="5f883-160">授权和筛选器</span><span class="sxs-lookup"><span data-stu-id="5f883-160">Authorization and filters</span></span>

<span data-ttu-id="5f883-161">在中心筛选器之前，[对集线器方法上的属性授权](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization)。</span><span class="sxs-lookup"><span data-stu-id="5f883-161">[Authorize attributes on hub methods](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) run before hub filters.</span></span>
