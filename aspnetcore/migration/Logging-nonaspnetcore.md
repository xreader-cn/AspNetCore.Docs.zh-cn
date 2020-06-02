---
<span data-ttu-id="1f4ee-101">标题：从 Microsoft 迁移2.2 到2.1 或3.0 作者： pakrym 说明：了解如何将使用 non-ASP.NET 的核心应用程序从2.1 迁移到2.2 或3.0。</span><span class="sxs-lookup"><span data-stu-id="1f4ee-101">title: Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0 author: pakrym description: Learn how to migrate a non-ASP.NET Core application that uses Microsoft.Extensions.Logging from 2.1 to 2.2 or 3.0.</span></span>
<span data-ttu-id="1f4ee-102">ms-chap： pakrym：自定义： mvc ms. 日期：01/04/2019 无位置：</span><span class="sxs-lookup"><span data-stu-id="1f4ee-102">ms.author: pakrym ms.custom: mvc ms.date: 01/04/2019 no-loc:</span></span>
- <span data-ttu-id="1f4ee-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1f4ee-103">'Blazor'</span></span>
- <span data-ttu-id="1f4ee-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1f4ee-104">'Identity'</span></span>
- <span data-ttu-id="1f4ee-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1f4ee-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="1f4ee-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1f4ee-106">'Razor'</span></span>
- <span data-ttu-id="1f4ee-107">" SignalR " uid：迁移/日志记录-nonaspnetcore</span><span class="sxs-lookup"><span data-stu-id="1f4ee-107">'SignalR' uid: migration/logging-nonaspnetcore</span></span>

---

# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="1f4ee-108">从 Microsoft 进行迁移。日志记录2.1 到2.2 或3。0</span><span class="sxs-lookup"><span data-stu-id="1f4ee-108">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="1f4ee-109">本文概述了将使用2.1 的 non-ASP.NET 核心应用程序迁移 `Microsoft.Extensions.Logging` 到2.2 或3.0 的常见步骤。</span><span class="sxs-lookup"><span data-stu-id="1f4ee-109">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="1f4ee-110">2.1 到 2.2</span><span class="sxs-lookup"><span data-stu-id="1f4ee-110">2.1 to 2.2</span></span>

<span data-ttu-id="1f4ee-111">手动创建 `ServiceCollection` 并调用 `AddLogging` 。</span><span class="sxs-lookup"><span data-stu-id="1f4ee-111">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="1f4ee-112">2.1 示例：</span><span class="sxs-lookup"><span data-stu-id="1f4ee-112">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="1f4ee-113">2.2 示例：</span><span class="sxs-lookup"><span data-stu-id="1f4ee-113">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="1f4ee-114">2.1 至3。0</span><span class="sxs-lookup"><span data-stu-id="1f4ee-114">2.1 to 3.0</span></span>

<span data-ttu-id="1f4ee-115">在3.0 中，使用 `LoggingFactory.Create` 。</span><span class="sxs-lookup"><span data-stu-id="1f4ee-115">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="1f4ee-116">2.1 示例：</span><span class="sxs-lookup"><span data-stu-id="1f4ee-116">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="1f4ee-117">3.0 示例：</span><span class="sxs-lookup"><span data-stu-id="1f4ee-117">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="1f4ee-118">其他资源</span><span class="sxs-lookup"><span data-stu-id="1f4ee-118">Additional resources</span></span>

* <span data-ttu-id="1f4ee-119">- [Console NuGet 包](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)。</span><span class="sxs-lookup"><span data-stu-id="1f4ee-119">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
