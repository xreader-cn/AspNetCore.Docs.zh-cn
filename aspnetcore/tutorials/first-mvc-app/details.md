---
title: 检查 ASP.NET Core 应用的 Details 和 Delete 方法
author: rick-anderson
description: 了解基本 ASP.NET Core MVC 应用中的详细信息控制器方法和视图。
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: d19e8cdb63da2bb9c66db1943dfcec183d432401
ms.sourcegitcommit: 2719c70cd15a430479ab4007ff3e197fbf5dfee0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68862972"
---
# <a name="examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a><span data-ttu-id="9462d-103">检查 ASP.NET Core 应用的 Details 和 Delete 方法</span><span class="sxs-lookup"><span data-stu-id="9462d-103">Examine the Details and Delete methods of an ASP.NET Core app</span></span>

<span data-ttu-id="9462d-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9462d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9462d-105">打开电影控制器，并检查 `Details` 方法：</span><span class="sxs-lookup"><span data-stu-id="9462d-105">Open the Movie controller and examine the `Details` method:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

<span data-ttu-id="9462d-106">创建此操作方法的 MVC 基架引擎添加显示调用方法的 HTTP 请求的注释。</span><span class="sxs-lookup"><span data-stu-id="9462d-106">The MVC scaffolding engine that created this action method adds a comment showing an HTTP request that invokes the method.</span></span> <span data-ttu-id="9462d-107">在此情况下，它是包含三个 URL 段的 GET 请求，这三个段为 `Movies` 控制器、`Details` 方法和 `id` 值。</span><span class="sxs-lookup"><span data-stu-id="9462d-107">In this case it's a GET request with three URL segments, the `Movies` controller, the `Details` method, and an `id` value.</span></span> <span data-ttu-id="9462d-108">回顾这些在 Startup.cs 中定义的段。 </span><span class="sxs-lookup"><span data-stu-id="9462d-108">Recall these segments are defined in *Startup.cs*.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="9462d-109">EF 可以使用 `FirstOrDefaultAsync` 方法轻松搜索数据。</span><span class="sxs-lookup"><span data-stu-id="9462d-109">EF makes it easy to search for data using the `FirstOrDefaultAsync` method.</span></span> <span data-ttu-id="9462d-110">该方法中内置的一个重要安全功能是，代码会先验证搜索方法已经找到电影，然后再执行操作。</span><span class="sxs-lookup"><span data-stu-id="9462d-110">An important security feature built into the method is that the code verifies that the search method has found a movie before it tries to do anything with it.</span></span> <span data-ttu-id="9462d-111">例如，一个黑客可能通过将链接创建的 URL 从 `http://localhost:{PORT}/Movies/Details/1` 更改为类似 `http://localhost:{PORT}/Movies/Details/12345` 的值（或者不代表任何实际电影的其他值）将错误引入站点。</span><span class="sxs-lookup"><span data-stu-id="9462d-111">For example, a hacker could introduce errors into the site by changing the URL created by the links from `http://localhost:{PORT}/Movies/Details/1` to something like  `http://localhost:{PORT}/Movies/Details/12345` (or some other value that doesn't represent an actual movie).</span></span> <span data-ttu-id="9462d-112">如果未检查是否有空电影，则应用可能引发异常。</span><span class="sxs-lookup"><span data-stu-id="9462d-112">If you didn't check for a null movie, the app would throw an exception.</span></span>

<span data-ttu-id="9462d-113">检查 `Delete` 和 `DeleteConfirmed` 方法。</span><span class="sxs-lookup"><span data-stu-id="9462d-113">Examine the `Delete` and `DeleteConfirmed` methods.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

<span data-ttu-id="9462d-114">请注意，`HTTP GET Delete` 方法不删除指定的电影，而是返回可在其中提交 (HttpPost) 删除的电影视图。</span><span class="sxs-lookup"><span data-stu-id="9462d-114">Note that the `HTTP GET Delete` method doesn't delete the specified movie, it returns a view of the movie where you can submit (HttpPost) the deletion.</span></span> <span data-ttu-id="9462d-115">执行删除操作以响应 GET 请求（或者说，执行编辑操作、创建操作或更改数据的任何其他操作）会打开安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="9462d-115">Performing a delete operation in response to a GET request (or for that matter, performing an edit operation, create operation, or any other operation that changes data) opens up a security hole.</span></span>

<span data-ttu-id="9462d-116">删除数据的 `[HttpPost]` 方法命名为 `DeleteConfirmed`，以便为 HTTP POST 方法提供一个唯一的签名或名称。</span><span class="sxs-lookup"><span data-stu-id="9462d-116">The `[HttpPost]` method that deletes the data is named `DeleteConfirmed` to give the HTTP POST method a unique signature or name.</span></span> <span data-ttu-id="9462d-117">下面显示了两个方法签名：</span><span class="sxs-lookup"><span data-stu-id="9462d-117">The two method signatures are shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

<span data-ttu-id="9462d-118">公共语言运行时 (CLR) 需要重载方法拥有唯一的参数签名（相同的方法名称但不同的参数列表）。</span><span class="sxs-lookup"><span data-stu-id="9462d-118">The common language runtime (CLR) requires overloaded methods to have a unique parameter signature (same method name but different list of parameters).</span></span> <span data-ttu-id="9462d-119">但是，这里需要两个 `Delete` 方法 -- 一个用于 GET，另一个用于 POST -- 这两个方法拥有相同的参数签名。</span><span class="sxs-lookup"><span data-stu-id="9462d-119">However, here you need two `Delete` methods -- one for GET and one for POST -- that both have the same parameter signature.</span></span> <span data-ttu-id="9462d-120">（它们都需要接受单个整数作为参数。）</span><span class="sxs-lookup"><span data-stu-id="9462d-120">(They both need to accept a single integer as a parameter.)</span></span>

<span data-ttu-id="9462d-121">可通过两种方法解决此问题，一种是为方法提供不同的名称。</span><span class="sxs-lookup"><span data-stu-id="9462d-121">There are two approaches to this problem, one is to give the methods different names.</span></span> <span data-ttu-id="9462d-122">这正是前面的示例中的基架机制进行的操作。</span><span class="sxs-lookup"><span data-stu-id="9462d-122">That's what the scaffolding mechanism did in the preceding example.</span></span> <span data-ttu-id="9462d-123">但是，这会造成一个小问题：ASP.NET 按名称将 URL 段映射到操作方法，如果重命名方法，则路由通常无法找到该方法。</span><span class="sxs-lookup"><span data-stu-id="9462d-123">However, this introduces a small problem: ASP.NET maps segments of a URL to action methods by name, and if you rename a method, routing normally wouldn't be able to find that method.</span></span> <span data-ttu-id="9462d-124">该示例中也提供了解决方案，即向 `DeleteConfirmed` 方法添加 `ActionName("Delete")` 属性。</span><span class="sxs-lookup"><span data-stu-id="9462d-124">The solution is what you see in the example, which is to add the `ActionName("Delete")` attribute to the `DeleteConfirmed` method.</span></span> <span data-ttu-id="9462d-125">该属性对路由系统执行映射，以便包括 POST 请求的 /Delete/ 的 URL可找到 `DeleteConfirmed` 方法。</span><span class="sxs-lookup"><span data-stu-id="9462d-125">That attribute performs mapping for the routing system so that a URL that includes /Delete/ for a POST request will find the `DeleteConfirmed` method.</span></span>

<span data-ttu-id="9462d-126">对于名称和签名相同的方法，另一个常用解决方法是手动更改 POST 方法的签名以包括额外（未使用）的参数。</span><span class="sxs-lookup"><span data-stu-id="9462d-126">Another common work around for methods that have identical names and signatures is to artificially change the signature of the POST method to include an extra (unused) parameter.</span></span> <span data-ttu-id="9462d-127">这正是前面的文章中添加 `notUsed` 参数时进行的操作。</span><span class="sxs-lookup"><span data-stu-id="9462d-127">That's what we did in a previous post when we added the `notUsed` parameter.</span></span> <span data-ttu-id="9462d-128">这里为了 `[HttpPost] Delete` 方法可以执行同样的操作：</span><span class="sxs-lookup"><span data-stu-id="9462d-128">You could do the same thing here for the `[HttpPost] Delete` method:</span></span>

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a><span data-ttu-id="9462d-129">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="9462d-129">Publish to Azure</span></span>

<span data-ttu-id="9462d-130">若要了解如何部署到 Azure，请参阅[教程：在 Azure 应用服务中生成 .NET Core 和 SQL 数据库 Web 应用](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)。</span><span class="sxs-lookup"><span data-stu-id="9462d-130">For information on deploying to Azure, see [Tutorial: Build a .NET Core and SQL Database web app in Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="9462d-131">上一篇</span><span class="sxs-lookup"><span data-stu-id="9462d-131">Previous</span></span>](validation.md)
