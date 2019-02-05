---
uid: mvc/overview/getting-started/introduction/adding-search
title: 搜索 |Microsoft Docs
author: Rick-Anderson
description: ''
ms.author: riande
ms.date: 05/22/2015
ms.assetid: df001954-18bf-4550-b03d-43911a0ea186
msc.legacyurl: /mvc/overview/getting-started/introduction/adding-search
msc.type: authoredcontent
ms.openlocfilehash: 31fd35ac63f3eb31d824e1710833ad83a0852ac9
ms.sourcegitcommit: a91e8dd2f4b788114c8bc834507277f4b5e8d6c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2019
ms.locfileid: "55712258"
---
<a name="search"></a><span data-ttu-id="9b611-102">搜索</span><span class="sxs-lookup"><span data-stu-id="9b611-102">Search</span></span>
====================
<span data-ttu-id="9b611-103">通过[Rick Anderson]((https://twitter.com/RickAndMSFT))</span><span class="sxs-lookup"><span data-stu-id="9b611-103">by [Rick Anderson]((https://twitter.com/RickAndMSFT))</span></span>

[!INCLUDE [Tutorial Note](sample/code-location.md)]

## <a name="adding-a-search-method-and-search-view"></a><span data-ttu-id="9b611-104">添加搜索方法和搜索视图</span><span class="sxs-lookup"><span data-stu-id="9b611-104">Adding a Search Method and Search View</span></span>

<span data-ttu-id="9b611-105">在本部分中，你将添加到搜索功能`Index`操作方法，您可以搜索电影的流派或名称。</span><span class="sxs-lookup"><span data-stu-id="9b611-105">In this section you'll add search capability to the `Index` action method that lets you search movies by genre or name.</span></span>

## <a name="updating-the-index-form"></a><span data-ttu-id="9b611-106">更新索引窗体</span><span class="sxs-lookup"><span data-stu-id="9b611-106">Updating the Index Form</span></span>

<span data-ttu-id="9b611-107">通过更新开始`Index`到现有的操作方法`MoviesController`类。</span><span class="sxs-lookup"><span data-stu-id="9b611-107">Start by updating the `Index` action method to the existing `MoviesController` class.</span></span> <span data-ttu-id="9b611-108">下面是代码：</span><span class="sxs-lookup"><span data-stu-id="9b611-108">Here's the code:</span></span>

[!code-csharp[Main](adding-search/samples/sample1.cs?highlight=1,6-9)]

<span data-ttu-id="9b611-109">第一行`Index`方法将创建以下[LINQ](https://msdn.microsoft.com/library/bb397926.aspx)查询用于选择电影：</span><span class="sxs-lookup"><span data-stu-id="9b611-109">The first line of the `Index` method creates the following [LINQ](https://msdn.microsoft.com/library/bb397926.aspx) query to select the movies:</span></span>

[!code-csharp[Main](adding-search/samples/sample2.cs)]

<span data-ttu-id="9b611-110">该查询在此情况下，定义，但尚未尚未针对数据库运行。</span><span class="sxs-lookup"><span data-stu-id="9b611-110">The query is defined at this point, but hasn't yet been run against the database.</span></span>

<span data-ttu-id="9b611-111">如果`searchString`参数包含一个字符串，电影查询则修改要筛选的搜索字符串，使用下面的代码的值：</span><span class="sxs-lookup"><span data-stu-id="9b611-111">If the `searchString` parameter contains a string, the movies query is modified to filter on the value of the search string, using the following code:</span></span>

[!code-csharp[Main](adding-search/samples/sample3.cs)]

<span data-ttu-id="9b611-112">上面的 `s => s.Title` 代码是 [Lambda 表达式](https://msdn.microsoft.com/library/bb397687.aspx)。</span><span class="sxs-lookup"><span data-stu-id="9b611-112">The `s => s.Title` code above is a [Lambda Expression](https://msdn.microsoft.com/library/bb397687.aspx).</span></span> <span data-ttu-id="9b611-113">Lambda 在基于方法的用于[LINQ](https://msdn.microsoft.com/library/bb397926.aspx)查询用作标准查询运算符方法的参数，如[其中](https://msdn.microsoft.com/library/system.linq.enumerable.where.aspx)上述代码中使用的方法。</span><span class="sxs-lookup"><span data-stu-id="9b611-113">Lambdas are used in method-based [LINQ](https://msdn.microsoft.com/library/bb397926.aspx) queries as arguments to standard query operator methods such as the [Where](https://msdn.microsoft.com/library/system.linq.enumerable.where.aspx) method used in the above code.</span></span> <span data-ttu-id="9b611-114">进行定义或通过调用一个方法，如修改后不会执行 LINQ 查询`Where`或`OrderBy`。</span><span class="sxs-lookup"><span data-stu-id="9b611-114">LINQ queries are not executed when they are defined or when they are modified by calling a method such as `Where` or `OrderBy`.</span></span> <span data-ttu-id="9b611-115">相反，延迟查询执行，这意味着表达式的计算延迟，直到真正循环访问其实现的值或[ `ToList` ](https://msdn.microsoft.com/library/bb342261.aspx)调用方法。</span><span class="sxs-lookup"><span data-stu-id="9b611-115">Instead, query execution is deferred, which means that the evaluation of an expression is delayed until its realized value is actually iterated over or the [`ToList`](https://msdn.microsoft.com/library/bb342261.aspx) method is called.</span></span> <span data-ttu-id="9b611-116">在中`Search`示例中，该查询中执行*Index.cshtml*视图。</span><span class="sxs-lookup"><span data-stu-id="9b611-116">In the `Search` sample, the query is executed in the *Index.cshtml* view.</span></span> <span data-ttu-id="9b611-117">有关延迟执行查询的详细信息，请参阅[Query Execution](https://msdn.microsoft.com/library/bb738633.aspx)（查询执行）。</span><span class="sxs-lookup"><span data-stu-id="9b611-117">For more information about deferred query execution, see [Query Execution](https://msdn.microsoft.com/library/bb738633.aspx).</span></span>

> [!NOTE]
> <span data-ttu-id="9b611-118">[Contains](https://msdn.microsoft.com/library/bb155125.aspx)方法运行数据库，而不是 c# 代码更高版本。</span><span class="sxs-lookup"><span data-stu-id="9b611-118">The [Contains](https://msdn.microsoft.com/library/bb155125.aspx) method is run on the database, not the c# code above.</span></span> <span data-ttu-id="9b611-119">在数据库中， [Contains](https://msdn.microsoft.com/library/bb155125.aspx)映射到[SQL LIKE](https://msdn.microsoft.com/library/ms179859.aspx)，这是不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="9b611-119">On the database, [Contains](https://msdn.microsoft.com/library/bb155125.aspx) maps to [SQL LIKE](https://msdn.microsoft.com/library/ms179859.aspx), which is case insensitive.</span></span>

<span data-ttu-id="9b611-120">现在可以更新`Index`将向用户显示窗体的视图。</span><span class="sxs-lookup"><span data-stu-id="9b611-120">Now you can update the `Index` view that will display the form to the user.</span></span>

<span data-ttu-id="9b611-121">运行应用程序并导航到 */Movies/Index*。</span><span class="sxs-lookup"><span data-stu-id="9b611-121">Run the application and navigate to */Movies/Index*.</span></span> <span data-ttu-id="9b611-122">将查询字符串（如 `?searchString=ghost`）追加到 URL。</span><span class="sxs-lookup"><span data-stu-id="9b611-122">Append a query string such as `?searchString=ghost` to the URL.</span></span> <span data-ttu-id="9b611-123">筛选的电影将显示出来。</span><span class="sxs-lookup"><span data-stu-id="9b611-123">The filtered movies are displayed.</span></span>

![SearchQryStr](adding-search/_static/image1.png)

<span data-ttu-id="9b611-125">如果您更改的签名`Index`方法，以使名为的参数`id`，则`id`参数将匹配`{id}`占位符默认路由中的设置*应用\_用户RouteConfig.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="9b611-125">If you change the signature of the `Index` method to have a parameter named `id`, the `id` parameter will match the `{id}` placeholder for the default routes set in the *App\_Start\RouteConfig.cs* file.</span></span>

[!code-json[Main](adding-search/samples/sample4.json)]

<span data-ttu-id="9b611-126">原始`Index`方法如下所示::</span><span class="sxs-lookup"><span data-stu-id="9b611-126">The original `Index` method looks like this::</span></span>

[!code-csharp[Main](adding-search/samples/sample5.cs)]

<span data-ttu-id="9b611-127">已修改`Index`方法看起来，如下所示：</span><span class="sxs-lookup"><span data-stu-id="9b611-127">The modified `Index` method would look as follows:</span></span>

[!code-csharp[Main](adding-search/samples/sample6.cs?highlight=1,3)]

<span data-ttu-id="9b611-128">现可将搜索标题作为路由数据（ URL 段）而非查询字符串值进行传递。</span><span class="sxs-lookup"><span data-stu-id="9b611-128">You can now pass the search title as route data (a URL segment) instead of as a query string value.</span></span>

![](adding-search/_static/image2.png)

<span data-ttu-id="9b611-129">但是，不能指望用户在每次要搜索电影时都修改 URL。</span><span class="sxs-lookup"><span data-stu-id="9b611-129">However, you can't expect users to modify the URL every time they want to search for a movie.</span></span> <span data-ttu-id="9b611-130">因此，现在您需要添加 UI 来帮助他们筛选电影。</span><span class="sxs-lookup"><span data-stu-id="9b611-130">So now you you'll add UI to help them filter movies.</span></span> <span data-ttu-id="9b611-131">如果已更改的签名`Index`方法来测试如何传递绑定路由的 ID 参数，将其更改回来，以使您`Index`方法采用字符串参数名为`searchString`:</span><span class="sxs-lookup"><span data-stu-id="9b611-131">If you changed the signature of the `Index` method to test how to pass the route-bound ID parameter, change it back so that your `Index` method takes a string parameter named `searchString`:</span></span>

[!code-csharp[Main](adding-search/samples/sample7.cs)]

<span data-ttu-id="9b611-132">打开*Views\Movies\Index.cshtml*文件，并紧后面`@Html.ActionLink("Create New", "Create")`，添加以下突出显示的窗体标记：</span><span class="sxs-lookup"><span data-stu-id="9b611-132">Open the *Views\Movies\Index.cshtml* file, and just after `@Html.ActionLink("Create New", "Create")`, add the form markup highlighted below:</span></span>

[!code-cshtml[Main](adding-search/samples/sample8.cshtml?highlight=12-15)]

<span data-ttu-id="9b611-133">`Html.BeginForm`帮助程序将创建一个打开`<form>`标记。</span><span class="sxs-lookup"><span data-stu-id="9b611-133">The `Html.BeginForm` helper creates an opening `<form>` tag.</span></span> <span data-ttu-id="9b611-134">`Html.BeginForm`帮助器后，窗体会发布到其自身，当用户通过单击提交窗体**筛选器**按钮。</span><span class="sxs-lookup"><span data-stu-id="9b611-134">The `Html.BeginForm` helper causes the form to post to itself when the user submits the form by clicking the **Filter** button.</span></span>

<span data-ttu-id="9b611-135">Visual Studio 2013 具有较好的改进时显示和编辑视图文件。</span><span class="sxs-lookup"><span data-stu-id="9b611-135">Visual Studio 2013 has a nice improvement when displaying and editing View files.</span></span> <span data-ttu-id="9b611-136">当你运行应用程序时的视图文件打开时，Visual Studio 2013 调用正确的控制器操作方法，以显示该视图。</span><span class="sxs-lookup"><span data-stu-id="9b611-136">When you run the application with a view file open, Visual Studio 2013 invokes the correct controller action method to display the view.</span></span>

![](adding-search/_static/image3.png)

<span data-ttu-id="9b611-137">索引视图 （如在上图中所示） 在 Visual Studio 中打开，点击 Ctr F5 或按 F5 运行应用程序，然后再尝试搜索电影。</span><span class="sxs-lookup"><span data-stu-id="9b611-137">With the Index view open in Visual Studio (as shown in the image above), tap Ctr F5 or F5 to run the application and then try searching for a movie.</span></span>

![](adding-search/_static/image4.png)

<span data-ttu-id="9b611-138">没有任何`HttpPost`重载`Index`方法。</span><span class="sxs-lookup"><span data-stu-id="9b611-138">There's no `HttpPost` overload of the `Index` method.</span></span> <span data-ttu-id="9b611-139">您不需要它，因为该方法不更改状态的应用程序，只需筛选数据。</span><span class="sxs-lookup"><span data-stu-id="9b611-139">You don't need it, because the method isn't changing the state of the application, just filtering data.</span></span>

<span data-ttu-id="9b611-140">可添加以下 `HttpPost Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="9b611-140">You could add the following `HttpPost Index` method.</span></span> <span data-ttu-id="9b611-141">在这种情况下，操作调用程序将匹配`HttpPost Index`方法，和`HttpPost Index`方法将运行下图中所示。</span><span class="sxs-lookup"><span data-stu-id="9b611-141">In that case, the action invoker would match the `HttpPost Index` method, and the `HttpPost Index` method would run as shown in the image below.</span></span>

[!code-csharp[Main](adding-search/samples/sample9.cs)]

![SearchPostGhost](adding-search/_static/image5.png)

<span data-ttu-id="9b611-143">但是，即使添加 `Index` 方法的 `HttpPost` 版本，其实现方式也受到限制。</span><span class="sxs-lookup"><span data-stu-id="9b611-143">However, even if you add this `HttpPost` version of the `Index` method, there's a limitation in how this has all been implemented.</span></span> <span data-ttu-id="9b611-144">假设你想要将特定搜索加入书签，或向朋友发送一个链接，让他们单击链接即可查看筛选出的相同电影列表。</span><span class="sxs-lookup"><span data-stu-id="9b611-144">Imagine that you want to bookmark a particular search or you want to send a link to friends that they can click in order to see the same filtered list of movies.</span></span> <span data-ttu-id="9b611-145">请注意，HTTP POST 请求的 URL 是 GET 请求 （localhost:xxxxx/Movies/Index） 的 URL 相同--没有 URL 本身中的搜索信息。</span><span class="sxs-lookup"><span data-stu-id="9b611-145">Notice that the URL for the HTTP POST request is the same as the URL for the GET request (localhost:xxxxx/Movies/Index) -- there's no search information in the URL itself.</span></span> <span data-ttu-id="9b611-146">右现在，搜索字符串信息发送到服务器作为窗体字段值。</span><span class="sxs-lookup"><span data-stu-id="9b611-146">Right now, the search string information is sent to the server as a form field value.</span></span> <span data-ttu-id="9b611-147">这意味着无法捕获该搜索信息加入书签或发送给朋友，在 URL 中。</span><span class="sxs-lookup"><span data-stu-id="9b611-147">This means you can't capture that search information to bookmark or send to friends in a URL.</span></span>

<span data-ttu-id="9b611-148">解决方法是使用的重载`BeginForm`，它指定 POST 请求，应将搜索信息添加到 URL，并且应路由到`HttpGet`版本的`Index`方法。</span><span class="sxs-lookup"><span data-stu-id="9b611-148">The solution is to use an overload of `BeginForm` that specifies that the POST request should add the search information to the URL and that it should be routed to the `HttpGet` version of the `Index` method.</span></span> <span data-ttu-id="9b611-149">替换现有无参数`BeginForm`方法替换为以下标记：</span><span class="sxs-lookup"><span data-stu-id="9b611-149">Replace the existing parameterless `BeginForm` method with the following markup:</span></span>

[!code-cshtml[Main](adding-search/samples/sample10.cshtml)]

![BeginFormPost_SM](adding-search/_static/image6.png)

<span data-ttu-id="9b611-151">现在提交搜索时，URL 包含搜索查询字符串。</span><span class="sxs-lookup"><span data-stu-id="9b611-151">Now when you submit a search, the URL contains a search query string.</span></span> <span data-ttu-id="9b611-152">即使具备 `HttpPost Index` 方法，搜索也将转到 `HttpGet Index` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="9b611-152">Searching will also go to the `HttpGet Index` action method, even if you have a `HttpPost Index` method.</span></span>

![IndexWithGetURL](adding-search/_static/image7.png)

## <a name="adding-search-by-genre"></a><span data-ttu-id="9b611-154">添加按流派搜索</span><span class="sxs-lookup"><span data-stu-id="9b611-154">Adding Search by Genre</span></span>

<span data-ttu-id="9b611-155">如果添加了`HttpPost`版本的`Index`方法，现在将其删除。</span><span class="sxs-lookup"><span data-stu-id="9b611-155">If you added the `HttpPost` version of the `Index` method, delete it now.</span></span>

<span data-ttu-id="9b611-156">接下来，你将添加特定功能，让用户按流派搜索电影。</span><span class="sxs-lookup"><span data-stu-id="9b611-156">Next, you'll add a feature to let users search for movies by genre.</span></span> <span data-ttu-id="9b611-157">将 `Index` 方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="9b611-157">Replace the `Index` method with the following code:</span></span>

[!code-csharp[Main](adding-search/samples/sample11.cs)]

<span data-ttu-id="9b611-158">此版本的`Index`方法采用附加参数，即`movieGenre`。</span><span class="sxs-lookup"><span data-stu-id="9b611-158">This version of the `Index` method takes an additional parameter, namely `movieGenre`.</span></span> <span data-ttu-id="9b611-159">前的几行代码创建`List`对象以保存从数据库的电影流派。</span><span class="sxs-lookup"><span data-stu-id="9b611-159">The first few lines of code create a `List` object to hold movie genres from the database.</span></span>

<span data-ttu-id="9b611-160">下面的代码是一种 LINQ 查询，可从数据库中检索所有流派。</span><span class="sxs-lookup"><span data-stu-id="9b611-160">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[Main](adding-search/samples/sample12.cs)]

<span data-ttu-id="9b611-161">该代码使用`AddRange`方法的泛型`List`集合以添加到列表中的所有不同的流派。</span><span class="sxs-lookup"><span data-stu-id="9b611-161">The code uses the `AddRange` method of the generic `List` collection to add all the distinct genres to the list.</span></span> <span data-ttu-id="9b611-162">(而无需`Distinct`修饰符，则添加重复的流派 — 例如，将在我们的示例中两次添加喜剧)。</span><span class="sxs-lookup"><span data-stu-id="9b611-162">(Without the `Distinct` modifier, duplicate genres would be added — for example, comedy would be added twice in our sample).</span></span> <span data-ttu-id="9b611-163">然后代码将存储在流派列表的`ViewBag.MovieGenre`对象。</span><span class="sxs-lookup"><span data-stu-id="9b611-163">The code then stores the list of genres in the `ViewBag.MovieGenre` object.</span></span> <span data-ttu-id="9b611-164">存储类别数据 （此类电影流派的） 作为[SelectList](https://msdn.microsoft.cus/library/system.web.mvc.selectlist(v=vs.108).aspx)对象中`ViewBag`，则访问下拉列表框中的类别数据是典型的 MVC 应用程序的方法。</span><span class="sxs-lookup"><span data-stu-id="9b611-164">Storing category data (such a movie genre's) as a [SelectList](https://msdn.microsoft.cus/library/system.web.mvc.selectlist(v=vs.108).aspx) object in a `ViewBag`, then accessing the category data in a dropdown list box is a typical approach for MVC applications.</span></span>

<span data-ttu-id="9b611-165">下面的代码演示了如何检查`movieGenre`参数。</span><span class="sxs-lookup"><span data-stu-id="9b611-165">The following code shows how to check the `movieGenre` parameter.</span></span> <span data-ttu-id="9b611-166">如果不为空，则代码进一步约束电影查询，以限制到指定类型的所选的电影。</span><span class="sxs-lookup"><span data-stu-id="9b611-166">If it's not empty, the code further constrains the movies query to limit the selected movies to the specified genre.</span></span>

[!code-csharp[Main](adding-search/samples/sample13.cs)]

<span data-ttu-id="9b611-167">如前面所述，运行查询时不在数据库上直到电影列表循环访问 (这在视图中，会发生后`Index`操作方法返回)。</span><span class="sxs-lookup"><span data-stu-id="9b611-167">As stated previously, the query is not run on the database until the movie list is iterated over (which happens in the View, after the `Index` action method returns).</span></span>

## <a name="adding-markup-to-the-index-view-to-support-search-by-genre"></a><span data-ttu-id="9b611-168">将标记添加到索引视图，以支持按流派搜索</span><span class="sxs-lookup"><span data-stu-id="9b611-168">Adding Markup to the Index View to Support Search by Genre</span></span>

<span data-ttu-id="9b611-169">添加`Html.DropDownList`到帮助程序*Views\Movies\Index.cshtml*文件，再`TextBox`帮助器。</span><span class="sxs-lookup"><span data-stu-id="9b611-169">Add an `Html.DropDownList` helper to the *Views\Movies\Index.cshtml* file, just before the `TextBox` helper.</span></span> <span data-ttu-id="9b611-170">完成的标记如下所示：</span><span class="sxs-lookup"><span data-stu-id="9b611-170">The completed markup is shown below:</span></span>

[!code-cshtml[Main](adding-search/samples/sample14.cshtml?highlight=11)]

<span data-ttu-id="9b611-171">在下面的代码：</span><span class="sxs-lookup"><span data-stu-id="9b611-171">In the following code:</span></span>

[!code-cshtml[Main](adding-search/samples/sample15.cshtml)]

<span data-ttu-id="9b611-172">参数"MovieGenre"提供的键`DropDownList`帮助器以查找`IEnumerable<SelectListItem>`中`ViewBag`。</span><span class="sxs-lookup"><span data-stu-id="9b611-172">The parameter "MovieGenre" provides the key for the `DropDownList` helper to find a `IEnumerable<SelectListItem>` in the `ViewBag`.</span></span> <span data-ttu-id="9b611-173">`ViewBag`填充操作方法中：</span><span class="sxs-lookup"><span data-stu-id="9b611-173">The `ViewBag` was populated in the action method:</span></span>

[!code-csharp[Main](adding-search/samples/sample16.cs?highlight=10)]

<span data-ttu-id="9b611-174">"All"提供选项标签参数。</span><span class="sxs-lookup"><span data-stu-id="9b611-174">The parameter "All" provides an option label.</span></span> <span data-ttu-id="9b611-175">如果在浏览器中查看该选择，您将看到其"值"属性为空。</span><span class="sxs-lookup"><span data-stu-id="9b611-175">If you inspect that choice in your browser, you'll see that its "value" attribute is empty.</span></span> <span data-ttu-id="9b611-176">由于我们的控制器仅筛选`if`字符串不是`null`或为空，将提交为空值`movieGenre`显示所有流派。</span><span class="sxs-lookup"><span data-stu-id="9b611-176">Since our controller only filters `if` the string is not `null` or empty, submitting an empty value for `movieGenre` shows all genres.</span></span>

<span data-ttu-id="9b611-177">此外可以设置默认情况下选择的选项。</span><span class="sxs-lookup"><span data-stu-id="9b611-177">You can also set an option to be selected by default.</span></span> <span data-ttu-id="9b611-178">如果您希望"喜剧"作为默认选项，则会更改控制器中的代码如下所示：</span><span class="sxs-lookup"><span data-stu-id="9b611-178">If you wanted "Comedy" as your default option, you would change the code in the Controller like so:</span></span>

[!code-cshtml[Main](adding-search/samples/sample17.cshtml)]

<span data-ttu-id="9b611-179">运行应用程序，并浏览到 */Movies/Index*。</span><span class="sxs-lookup"><span data-stu-id="9b611-179">Run the application and browse to */Movies/Index*.</span></span> <span data-ttu-id="9b611-180">按流派、 电影名称和这两个条件，请尝试搜索。</span><span class="sxs-lookup"><span data-stu-id="9b611-180">Try a search by genre, by movie name, and by both criteria.</span></span>

![](adding-search/_static/image8.png)

<span data-ttu-id="9b611-181">在本部分中创建了搜索操作方法和视图，以让用户搜索电影标题和流派。</span><span class="sxs-lookup"><span data-stu-id="9b611-181">In this section you created a search action method and view that let users search by movie title and genre.</span></span> <span data-ttu-id="9b611-182">在下一部分中，将探讨如何将属性添加到`Movie`模型以及如何添加初始值设定项将自动创建测试数据库。</span><span class="sxs-lookup"><span data-stu-id="9b611-182">In the next section, you'll look at how to add a property to the `Movie` model and how to add an initializer that will automatically create a test database.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9b611-183">[上一页](examining-the-edit-methods-and-edit-view.md)
> [下一页](adding-a-new-field.md)</span><span class="sxs-lookup"><span data-stu-id="9b611-183">[Previous](examining-the-edit-methods-and-edit-view.md)
[Next](adding-a-new-field.md)</span></span>
