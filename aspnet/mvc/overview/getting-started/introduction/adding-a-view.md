---
title: 将视图添加到 MVC 应用
author: Rick-Anderson
description: 将视图添加到 MVC 应用
ms.author: riande
ms.date: 01/23/2019
uid: mvc/overview/getting-started/introduction/adding-a-view
ms.openlocfilehash: afa7584529566ebe82a0eb3849de89bd0df064bd
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837749"
---
<a name="adding-a-view"></a><span data-ttu-id="c1419-103">添加视图</span><span class="sxs-lookup"><span data-stu-id="c1419-103">Adding a View</span></span>
====================
<span data-ttu-id="c1419-104">通过[Rick Anderson]((https://twitter.com/RickAndMSFT))</span><span class="sxs-lookup"><span data-stu-id="c1419-104">by [Rick Anderson]((https://twitter.com/RickAndMSFT))</span></span>

[!INCLUDE [Tutorial Note](sample/code-location.md)]

<span data-ttu-id="c1419-105">在本部分中想要修改`HelloWorldController`类，以使用视图模板文件来顺利封装生成 HTML 响应向客户端的过程。</span><span class="sxs-lookup"><span data-stu-id="c1419-105">In this section you're going to modify the `HelloWorldController` class to use view template files to cleanly encapsulate the process of generating HTML responses to a client.</span></span> 

<span data-ttu-id="c1419-106">使用视图模板文件，您将创建[Razor 视图引擎](../../../../web-pages/overview/getting-started/introducing-razor-syntax-c.md)。</span><span class="sxs-lookup"><span data-stu-id="c1419-106">You'll create a view template file using the [Razor view engine](../../../../web-pages/overview/getting-started/introducing-razor-syntax-c.md).</span></span> <span data-ttu-id="c1419-107">基于 razor 的视图模板具有 *.cshtml*文件扩展名，并提供创建 HTML 输出使用 C# 的简洁方法。</span><span class="sxs-lookup"><span data-stu-id="c1419-107">Razor-based view templates have a *.cshtml* file extension, and provide an elegant way to create HTML output using C#.</span></span> <span data-ttu-id="c1419-108">Razor 字符和击键时编写视图模板，所需的数量降至最低，并启用编码工作流即快速、 流畅。</span><span class="sxs-lookup"><span data-stu-id="c1419-108">Razor minimizes the number of characters and keystrokes required when writing a view template, and enables a fast, fluid coding workflow.</span></span>

<span data-ttu-id="c1419-109">当前，`Index` 方法返回带有在控制器类中硬编码的消息的字符串。</span><span class="sxs-lookup"><span data-stu-id="c1419-109">Currently the `Index` method returns a string with a message that is hard-coded in the controller class.</span></span> <span data-ttu-id="c1419-110">更改`Index`方法来调用控制器[视图](/dotnet/api/microsoft.aspnetcore.mvc.controller.view#Microsoft_AspNetCore_Mvc_Controller_View)方法，如以下代码所示：</span><span class="sxs-lookup"><span data-stu-id="c1419-110">Change the `Index` method to call the controllers [View](/dotnet/api/microsoft.aspnetcore.mvc.controller.view#Microsoft_AspNetCore_Mvc_Controller_View) method, as shown in the following code:</span></span>

[!code-csharp[Main](adding-a-view/samples/sample1.cs?highlight=1,3)]

<span data-ttu-id="c1419-111">`Index`上述方法使用视图模板生成到浏览器的 HTML 响应。</span><span class="sxs-lookup"><span data-stu-id="c1419-111">The `Index` method above uses a view template to generate an HTML response to the browser.</span></span> <span data-ttu-id="c1419-112">控制器方法 (也称为[操作方法](http://rachelappel.com/asp.net-mvc-actionresults-explained))，如`Index`上面，方法通常返回[ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult.aspx) (或从派生的类[ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult.aspx))，字符串等不基元类型。</span><span class="sxs-lookup"><span data-stu-id="c1419-112">Controller methods (also known as [action methods](http://rachelappel.com/asp.net-mvc-actionresults-explained)), such as the `Index` method above, generally return an [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult.aspx) (or a class derived from [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult.aspx)), not primitive types like string.</span></span>

<span data-ttu-id="c1419-113">右键单击*Views\HelloWorld*文件夹，然后单击**添加**，然后单击**MVC 5 视图页布局 (Razor)**。</span><span class="sxs-lookup"><span data-stu-id="c1419-113">Right click the *Views\HelloWorld* folder and click **Add**, then click **MVC 5 View Page with Layout (Razor)**.</span></span>
  
![](adding-a-view/_static/image1.png)   
  
<span data-ttu-id="c1419-114">在中**指定项名称**对话框框中，输入*索引*，然后单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="c1419-114">In the **Specify Name for Item** dialog box, enter *Index*, and then click **OK**.</span></span>  
  
![](adding-a-view/_static/image2.png)  
  
<span data-ttu-id="c1419-115">在中**选择布局页**对话框中，接受默认 **\_Layout.cshtml**然后单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="c1419-115">In the **Select a Layout Page** dialog, accept the default **\_Layout.cshtml** and click **OK**.</span></span>  
  
![](adding-a-view/_static/image3.png)  
  
<span data-ttu-id="c1419-116">在更高版本，对话框*views/shared*的左窗格中选择文件夹。</span><span class="sxs-lookup"><span data-stu-id="c1419-116">In the dialog above, the *Views\Shared* folder is selected in the left pane.</span></span> <span data-ttu-id="c1419-117">如果在另一个文件夹中有一个自定义布局文件，则无法选择它。</span><span class="sxs-lookup"><span data-stu-id="c1419-117">If you had a custom layout file in another folder, you could select it.</span></span> <span data-ttu-id="c1419-118">我们稍后再讨论的布局文件本教程的后面</span><span class="sxs-lookup"><span data-stu-id="c1419-118">We'll talk about the layout file later in the tutorial</span></span>

<span data-ttu-id="c1419-119">*MvcMovie\Views\HelloWorld\Index.cshtml*创建文件。</span><span class="sxs-lookup"><span data-stu-id="c1419-119">The *MvcMovie\Views\HelloWorld\Index.cshtml* file is created.</span></span>

![](adding-a-view/_static/image4.png)

<span data-ttu-id="c1419-120">添加以下突出显示的标记。</span><span class="sxs-lookup"><span data-stu-id="c1419-120">Add the following highlighted markup.</span></span>

[!code-cshtml[Main](adding-a-view/samples/sample2.cshtml?highlight=4-11)]

<span data-ttu-id="c1419-121">右键单击*Index.cshtml*文件，然后选择**用浏览器查看**。</span><span class="sxs-lookup"><span data-stu-id="c1419-121">Right click the *Index.cshtml* file and select **View in Browser**.</span></span>

![PI](adding-a-view/_static/image5.png)

<span data-ttu-id="c1419-123">您还可以右键单击*Index.cshtml*文件，然后选择**在 Page Inspector 中的视图。**</span><span class="sxs-lookup"><span data-stu-id="c1419-123">You can also right click the *Index.cshtml* file and select **View in Page Inspector.**</span></span> <span data-ttu-id="c1419-124">请参阅[Page Inspector 教程](../../views/using-page-inspector-in-aspnet-mvc.md)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="c1419-124">See the [Page Inspector tutorial](../../views/using-page-inspector-in-aspnet-mvc.md) for more information.</span></span>

<span data-ttu-id="c1419-125">或者，运行该应用程序并浏览到`HelloWorld`控制器 (`http://localhost:xxxx/HelloWorld`)。</span><span class="sxs-lookup"><span data-stu-id="c1419-125">Alternatively, run the application and browse to the `HelloWorld` controller (`http://localhost:xxxx/HelloWorld`).</span></span> <span data-ttu-id="c1419-126">`Index`控制器中的方法未做大量的工作; 它只需运行该语句`return View()`，其指定该方法应使用视图模板文件来呈现到浏览器的响应。</span><span class="sxs-lookup"><span data-stu-id="c1419-126">The `Index` method in your controller didn't do much work; it simply ran the statement `return View()`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="c1419-127">由于未显式指定要使用的视图模板文件的名称，ASP.NET MVC 的默认设置为使用*Index.cshtml*视图中的文件*\Views\HelloWorld*文件夹。</span><span class="sxs-lookup"><span data-stu-id="c1419-127">Because you didn't explicitly specify the name of the view template file to use, ASP.NET MVC defaulted to using the *Index.cshtml* view file in the *\Views\HelloWorld* folder.</span></span> <span data-ttu-id="c1419-128">下图显示了字符串&quot;Hello from our View Template ！&quot;硬编码的视图中。</span><span class="sxs-lookup"><span data-stu-id="c1419-128">The image below shows the string &quot;Hello from our View Template!&quot; hard-coded in the view.</span></span>

![](adding-a-view/_static/image6.png)

<span data-ttu-id="c1419-129">看起来相当棒。</span><span class="sxs-lookup"><span data-stu-id="c1419-129">Looks pretty good.</span></span> <span data-ttu-id="c1419-130">但是，请注意，浏览器的标题栏显示"索引-My ASP.NET Application，"，并在页面顶部的大链接显示"Application name"。</span><span class="sxs-lookup"><span data-stu-id="c1419-130">However, notice that the browser's title bar shows "Index - My ASP.NET Application," and the big link at the top of the page says "Application name."</span></span> <span data-ttu-id="c1419-131">具体取决于如何小进行浏览器窗口中，您可能需要单击三个条，若要查看的右上角向**主页**，**有关**，**联系人**， **注册**并**登录**链接。</span><span class="sxs-lookup"><span data-stu-id="c1419-131">Depending on how small you make your browser window, you might need to click the three bars in the upper right to see the to the **Home**, **About**, **Contact**, **Register** and **Log in** links.</span></span>

## <a name="changing-views-and-layout-pages"></a><span data-ttu-id="c1419-132">更改视图和布局页面</span><span class="sxs-lookup"><span data-stu-id="c1419-132">Changing Views and Layout Pages</span></span>

<span data-ttu-id="c1419-133">首先，你想要更改&quot;应用程序名称&quot;在页面顶部的链接。</span><span class="sxs-lookup"><span data-stu-id="c1419-133">First, you want to change the &quot;Application name&quot; link at the top of the page.</span></span> <span data-ttu-id="c1419-134">该文本是通用的每一页。</span><span class="sxs-lookup"><span data-stu-id="c1419-134">That text is common to every page.</span></span> <span data-ttu-id="c1419-135">它是实际实现只能在一个位置，在项目中，即使它在应用程序中每一页显示。</span><span class="sxs-lookup"><span data-stu-id="c1419-135">It's actually implemented in only one place in the project, even though it appears on every page in the application.</span></span> <span data-ttu-id="c1419-136">转到 */视图/共享*中的文件夹**解决方案资源管理器**，然后打开 *\_Layout.cshtml*文件。</span><span class="sxs-lookup"><span data-stu-id="c1419-136">Go to the */Views/Shared* folder in **Solution Explorer** and open the *\_Layout.cshtml* file.</span></span> <span data-ttu-id="c1419-137">此文件称为*布局页*以及在所有其他页使用的共享文件夹中。</span><span class="sxs-lookup"><span data-stu-id="c1419-137">This file is called a *layout page* and it's in the shared folder that all other pages use.</span></span>

![_LayoutCshtml](adding-a-view/_static/image7.png)

<span data-ttu-id="c1419-139">布局模板可用于在一个位置指定您的网站的 HTML 容器布局，然后将其应用于你的站点中多个页面。</span><span class="sxs-lookup"><span data-stu-id="c1419-139">Layout templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="c1419-140">查找 `@RenderBody()` 行。</span><span class="sxs-lookup"><span data-stu-id="c1419-140">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="c1419-141">`RenderBody` 是显示创建的所有特定于视图的页面的占位符，已包装在布局页面中&quot;&quot;。</span><span class="sxs-lookup"><span data-stu-id="c1419-141">`RenderBody` is a placeholder where all the view-specific pages you create show up, &quot;wrapped&quot; in the layout page.</span></span> <span data-ttu-id="c1419-142">例如，如果您选择**有关**链接，链接*Views\Home\About.cshtml*内呈现视图`RenderBody`方法。</span><span class="sxs-lookup"><span data-stu-id="c1419-142">For example, if you select the **About** link, the *Views\Home\About.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<span data-ttu-id="c1419-143">更改标题元素的内容。</span><span class="sxs-lookup"><span data-stu-id="c1419-143">Change the contents of the title element.</span></span> <span data-ttu-id="c1419-144">更改[ActionLink](https://msdn.microsoft.com/library/dd504972(v=vs.108).aspx)中的布局模板中&quot;应用程序名称&quot;到&quot;MVC 电影&quot;并将控制器从`Home`到`Movies`。</span><span class="sxs-lookup"><span data-stu-id="c1419-144">Change the [ActionLink](https://msdn.microsoft.com/library/dd504972(v=vs.108).aspx) in the layout template from &quot;Application name&quot; to &quot;MVC Movie&quot; and the controller from `Home` to `Movies`.</span></span> <span data-ttu-id="c1419-145">完整布局文件如下所示：</span><span class="sxs-lookup"><span data-stu-id="c1419-145">The complete layout file is shown below:</span></span>

[!code-cshtml[Main](adding-a-view/samples/sample3.cshtml?highlight=6,20)]

<span data-ttu-id="c1419-146">运行应用程序，请注意，它现在显示&quot;MVC 电影&quot;。</span><span class="sxs-lookup"><span data-stu-id="c1419-146">Run the application and notice that it now says &quot;MVC Movie &quot;.</span></span> <span data-ttu-id="c1419-147">单击**有关**链接，你会看到该页面的显示方式&quot;MVC 电影&quot;、 过。</span><span class="sxs-lookup"><span data-stu-id="c1419-147">Click the **About** link, and you see how that page shows &quot;MVC Movie&quot;, too.</span></span> <span data-ttu-id="c1419-148">我们能够在布局模板中一次进行更改，并具有在站点上的所有页面都反映新标题。</span><span class="sxs-lookup"><span data-stu-id="c1419-148">We were able to make the change once in the layout template and have all pages on the site reflect the new title.</span></span>

![](adding-a-view/_static/image8.png)

<span data-ttu-id="c1419-149">当我们首次创建*Views\HelloWorld\Index.cshtml*文件，它包含以下代码：</span><span class="sxs-lookup"><span data-stu-id="c1419-149">When we first created the *Views\HelloWorld\Index.cshtml* file, it contained the following code:</span></span>

[!code-cshtml[Main](adding-a-view/samples/sample4.cshtml)]

<span data-ttu-id="c1419-150">上面的 Razor 代码显式设置布局页。</span><span class="sxs-lookup"><span data-stu-id="c1419-150">The Razor code above is explicitly setting the layout page.</span></span> <span data-ttu-id="c1419-151">检查*视图\\_ViewStart.cshtml*文件，它包含完全相同的 Razor 标记。</span><span class="sxs-lookup"><span data-stu-id="c1419-151">Examine the *Views\\_ViewStart.cshtml* file, it contains the exact same Razor markup.</span></span> <span data-ttu-id="c1419-152">*[视图\\_ViewStart.cshtml](https://weblogs.asp.net/scottgu/archive/2010/10/22/asp-net-mvc-3-layouts.aspx)* 文件定义的所有视图将都使用的常见布局，因此你可以注释或删除该代码从*Views\HelloWorld\Index.cshtml*文件。</span><span class="sxs-lookup"><span data-stu-id="c1419-152">The *[Views\\_ViewStart.cshtml](https://weblogs.asp.net/scottgu/archive/2010/10/22/asp-net-mvc-3-layouts.aspx)* file defines the common layout that all views will use, therefore you can comment out or remove that code from the *Views\HelloWorld\Index.cshtml* file.</span></span>

[!code-cshtml[Main](adding-a-view/samples/sample5.cshtml?highlight=1-3)]

<span data-ttu-id="c1419-153">可以使用 `Layout` 属性设置不同的布局视图，或将它设置为 `null`，这样将不会使用任何布局文件。</span><span class="sxs-lookup"><span data-stu-id="c1419-153">You can use the `Layout` property to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="c1419-154">现在，让我们来更改索引视图的标题。</span><span class="sxs-lookup"><span data-stu-id="c1419-154">Now, let's change the title of the Index view.</span></span>

<span data-ttu-id="c1419-155">打开*MvcMovie\Views\HelloWorld\Index.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="c1419-155">Open *MvcMovie\Views\HelloWorld\Index.cshtml*.</span></span> <span data-ttu-id="c1419-156">有两个位置进行更改： 首先，文本显示的标题中的浏览器中，然后在辅助标头 (`<h2>`元素)。</span><span class="sxs-lookup"><span data-stu-id="c1419-156">There are two places to make a change: first, the text that appears in the title of the browser, and then in the secondary header (the `<h2>` element).</span></span> <span data-ttu-id="c1419-157">稍稍对它们进行一些更改，这样可以看出哪一段代码更改了应用的哪部分。</span><span class="sxs-lookup"><span data-stu-id="c1419-157">You'll make them slightly different so you can see which bit of code changes which part of the app.</span></span>

[!code-cshtml[Main](adding-a-view/samples/sample6.cshtml?highlight=2,5)]

<span data-ttu-id="c1419-158">若要指示显示集上面的代码的 HTML 标题`Title`的属性`ViewBag`对象 (后者位于*Index.cshtml*视图模板)。</span><span class="sxs-lookup"><span data-stu-id="c1419-158">To indicate the HTML title to display, the code above sets a `Title` property of the `ViewBag` object (which is in the *Index.cshtml* view template).</span></span> <span data-ttu-id="c1419-159">请注意，布局模板 ( *views/shared\\_Layout.cshtml* ) 使用中的此值`<title>`元素的一部分`<head>`我们以前修改 HTML 的一部分。</span><span class="sxs-lookup"><span data-stu-id="c1419-159">Notice that the layout template ( *Views\Shared\\_Layout.cshtml* ) uses this value in the `<title>` element as part of the `<head>` section of the HTML that we modified previously.</span></span>

[!code-cshtml[Main](adding-a-view/samples/sample7.cshtml?highlight=6)]

<span data-ttu-id="c1419-160">使用此`ViewBag`方法时，你可以轻松地传递其他参数视图模板和布局文件之间。</span><span class="sxs-lookup"><span data-stu-id="c1419-160">Using this `ViewBag` approach, you can easily pass other parameters between your view template and your layout file.</span></span>

<span data-ttu-id="c1419-161">运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="c1419-161">Run the application.</span></span> <span data-ttu-id="c1419-162">请注意，浏览器标题、主标题和辅助标题已更改。</span><span class="sxs-lookup"><span data-stu-id="c1419-162">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="c1419-163">（如果没有在浏览器中看到更改，则可能正在查看缓存的内容。</span><span class="sxs-lookup"><span data-stu-id="c1419-163">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="c1419-164">在浏览器中按 Ctrl + F5 强制加载来自服务器的响应。）使用创建浏览器标题`ViewBag.Title`中设置*Index.cshtml*查看模板和其他&quot;-电影应用&quot;添加布局文件中。</span><span class="sxs-lookup"><span data-stu-id="c1419-164">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with the `ViewBag.Title` we set in the *Index.cshtml* view template and the additional &quot;- Movie App&quot; added in the layout file.</span></span>

<span data-ttu-id="c1419-165">此外会注意到了中的内容*Index.cshtml*视图的模板已与合并 *\_Layout.cshtml*视图模板和单个 HTML 响应已发送到浏览器。</span><span class="sxs-lookup"><span data-stu-id="c1419-165">Also notice how the content in the *Index.cshtml* view template was merged with the *\_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="c1419-166">凭借布局模板可以很容易地对应用程序中所有页面进行更改。</span><span class="sxs-lookup"><span data-stu-id="c1419-166">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span>

![](adding-a-view/_static/image9.png)

<span data-ttu-id="c1419-167">我们一小段&quot;数据&quot;(在这种情况下&quot;Hello from our View Template ！&quot;消息) 不过是硬编码。</span><span class="sxs-lookup"><span data-stu-id="c1419-167">Our little bit of &quot;data&quot; (in this case the &quot;Hello from our View Template!&quot; message) is hard-coded, though.</span></span> <span data-ttu-id="c1419-168">MVC 应用程序具有&quot;V&quot; （视图） 并且您已获得&quot;C&quot; （控制器），但没有&quot;M&quot; （模型） 尚未。</span><span class="sxs-lookup"><span data-stu-id="c1419-168">The MVC application has a &quot;V&quot; (view) and you've got a &quot;C&quot; (controller), but no &quot;M&quot; (model) yet.</span></span> <span data-ttu-id="c1419-169">稍后，我们将演练如何创建数据库并从它检索模型数据。</span><span class="sxs-lookup"><span data-stu-id="c1419-169">Shortly, we'll walk through how to create a database and retrieve model data from it.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="c1419-170">将数据从控制器传递给视图</span><span class="sxs-lookup"><span data-stu-id="c1419-170">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="c1419-171">我们转到数据库，并讨论模型之前，不过，我们首先讨论一下将信息从控制器传递给视图。</span><span class="sxs-lookup"><span data-stu-id="c1419-171">Before we go to a database and talk about models, though, let's first talk about passing information from the controller to a view.</span></span> <span data-ttu-id="c1419-172">控制器类调用以响应传入的 URL 请求。</span><span class="sxs-lookup"><span data-stu-id="c1419-172">Controller classes are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="c1419-173">控制器类是响应的编写处理传入浏览器的代码的请求、 从数据库检索数据，并最终决定将哪些类型发送回浏览器的位置。</span><span class="sxs-lookup"><span data-stu-id="c1419-173">A controller class is where you write the code that handles the incoming browser requests, retrieves data from a database, and ultimately decides what type of response to send back to the browser.</span></span> <span data-ttu-id="c1419-174">查看模板然后可从控制器来生成并格式化对浏览器的 HTML 响应。</span><span class="sxs-lookup"><span data-stu-id="c1419-174">View templates can then be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="c1419-175">控制器负责提供使视图模板能够呈现到浏览器的响应所需的任何数据或对象。</span><span class="sxs-lookup"><span data-stu-id="c1419-175">Controllers are responsible for providing whatever data or objects are required in order for a view template to render a response to the browser.</span></span> <span data-ttu-id="c1419-176">最佳做法：**视图模板应永远不会执行业务逻辑或直接与数据库交互**。</span><span class="sxs-lookup"><span data-stu-id="c1419-176">A best practice: **A view template should never perform business logic or interact with a database directly**.</span></span> <span data-ttu-id="c1419-177">相反，视图模板应使用仅由控制器提供给它的数据。</span><span class="sxs-lookup"><span data-stu-id="c1419-177">Instead, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="c1419-178">维护这&quot;关注点分离&quot;有助于保持代码干净、 可测试且更易于维护。</span><span class="sxs-lookup"><span data-stu-id="c1419-178">Maintaining this &quot;separation of concerns&quot; helps keep your code clean, testable and more maintainable.</span></span>

<span data-ttu-id="c1419-179">目前，`Welcome`操作方法中的`HelloWorldController`类采用`name`和一个`numTimes`参数，然后输出直接向浏览器的值。</span><span class="sxs-lookup"><span data-stu-id="c1419-179">Currently, the `Welcome` action method in the `HelloWorldController` class takes a `name` and a `numTimes` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="c1419-180">而不是使控制器将作为一个字符串，此响应呈现，让我们更改控制器以改为使用视图模板。</span><span class="sxs-lookup"><span data-stu-id="c1419-180">Rather than have the controller render this response as a string, let's change the controller to use a view template instead.</span></span> <span data-ttu-id="c1419-181">视图模板将生成动态响应，这意味着你需要将适当的数据位从控制器传递给视图以生成响应。</span><span class="sxs-lookup"><span data-stu-id="c1419-181">The view template will generate a dynamic response, which means that you need to pass appropriate bits of data from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="c1419-182">可以为此，需要查看模板的动态数据 （参数） 放置在控制器`ViewBag`视图模板稍后可以访问的对象。</span><span class="sxs-lookup"><span data-stu-id="c1419-182">You can do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewBag` object that the view template can then access.</span></span>

<span data-ttu-id="c1419-183">返回到*HelloWorldController.cs*文件，并更改`Welcome`方法以添加`Message`并`NumTimes`值设为`ViewBag`对象。</span><span class="sxs-lookup"><span data-stu-id="c1419-183">Return to the *HelloWorldController.cs* file and change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewBag` object.</span></span> <span data-ttu-id="c1419-184">`ViewBag` 是一个动态对象，这意味着您可以随意设置其中;`ViewBag`对象具有定义的属性，直到你将内容放在它。</span><span class="sxs-lookup"><span data-stu-id="c1419-184">`ViewBag` is a dynamic object, which means you can put whatever you want in to it; the `ViewBag` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="c1419-185">[ASP.NET MVC 模型绑定系统](http://odetocode.com/Blogs/scott/archive/2009/04/27/6-tips-for-asp-net-mvc-model-binding.aspx)会自动将映射命名的参数 (`name`和`numTimes`) 从方法中的参数到地址栏中的查询字符串。</span><span class="sxs-lookup"><span data-stu-id="c1419-185">The [ASP.NET MVC model binding system](http://odetocode.com/Blogs/scott/archive/2009/04/27/6-tips-for-asp-net-mvc-model-binding.aspx) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="c1419-186">完整的 HelloWorldController.cs 文件如下所示：</span><span class="sxs-lookup"><span data-stu-id="c1419-186">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[Main](adding-a-view/samples/sample8.cs)]

<span data-ttu-id="c1419-187">现在`ViewBag`对象包含将自动传递给视图的数据。</span><span class="sxs-lookup"><span data-stu-id="c1419-187">Now the `ViewBag` object contains data that will be passed to the view automatically.</span></span> <span data-ttu-id="c1419-188">接下来，需要一个欢迎使用视图模板 ！</span><span class="sxs-lookup"><span data-stu-id="c1419-188">Next, you need a Welcome view template!</span></span> <span data-ttu-id="c1419-189">在中**构建**菜单中，选择**生成解决方案**（或 Ctrl + Shift + B） 若要确保编译项目。</span><span class="sxs-lookup"><span data-stu-id="c1419-189">In the **Build** menu, select **Build Solution** (or Ctrl+Shift+B) to make sure the project is compiled.</span></span> <span data-ttu-id="c1419-190">右键单击*Views\HelloWorld*文件夹，然后单击**添加**，然后单击**MVC 5 视图页布局 (Razor)**。</span><span class="sxs-lookup"><span data-stu-id="c1419-190">Right click the *Views\HelloWorld* folder and click **Add**, then click **MVC 5 View Page with Layout (Razor)**.</span></span>
  
![](adding-a-view/_static/image10.png)   
  
<span data-ttu-id="c1419-191">在中**指定项名称**对话框框中，输入*欢迎*，然后单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="c1419-191">In the **Specify Name for Item** dialog box, enter *Welcome*, and then click **OK**.</span></span>   
  
<span data-ttu-id="c1419-192">在中**选择布局页**对话框中，接受默认 **\_Layout.cshtml**然后单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="c1419-192">In the **Select a Layout Page** dialog, accept the default **\_Layout.cshtml** and click **OK**.</span></span>  
  
![](adding-a-view/_static/image11.png)   

<span data-ttu-id="c1419-193">*MvcMovie\Views\HelloWorld\Welcome.cshtml*创建文件。</span><span class="sxs-lookup"><span data-stu-id="c1419-193">The *MvcMovie\Views\HelloWorld\Welcome.cshtml* file is created.</span></span>

<span data-ttu-id="c1419-194">替换中的标记*Welcome.cshtml*文件。</span><span class="sxs-lookup"><span data-stu-id="c1419-194">Replace the markup in the *Welcome.cshtml* file.</span></span> <span data-ttu-id="c1419-195">你将创建一个循环，说&quot;Hello&quot;根据用户所说它应多次。</span><span class="sxs-lookup"><span data-stu-id="c1419-195">You'll create a loop that says &quot;Hello&quot; as many times as the user says it should.</span></span> <span data-ttu-id="c1419-196">完整*Welcome.cshtml*文件如下所示。</span><span class="sxs-lookup"><span data-stu-id="c1419-196">The complete *Welcome.cshtml* file is shown below.</span></span>

[!code-cshtml[Main](adding-a-view/samples/sample9.cshtml)]

<span data-ttu-id="c1419-197">运行应用程序并浏览到以下 URL:</span><span class="sxs-lookup"><span data-stu-id="c1419-197">Run the application and browse to the following URL:</span></span>

`http://localhost:xx/HelloWorld/Welcome?name=Scott&numtimes=4`

<span data-ttu-id="c1419-198">数据取自 URL 并传递给控制器使用现在[模型联编程序](http://odetocode.com/Blogs/scott/archive/2009/04/27/6-tips-for-asp-net-mvc-model-binding.aspx)。</span><span class="sxs-lookup"><span data-stu-id="c1419-198">Now data is taken from the URL and passed to the controller using the [model binder](http://odetocode.com/Blogs/scott/archive/2009/04/27/6-tips-for-asp-net-mvc-model-binding.aspx).</span></span> <span data-ttu-id="c1419-199">控制器将数据打包到`ViewBag`对象并将该对象传递给视图。</span><span class="sxs-lookup"><span data-stu-id="c1419-199">The controller packages the data into a `ViewBag` object and passes that object to the view.</span></span> <span data-ttu-id="c1419-200">该视图然后会以 html 格式的数据向用户显示。</span><span class="sxs-lookup"><span data-stu-id="c1419-200">The view then displays the data as HTML to the user.</span></span>

![](adding-a-view/_static/image12.png)

<span data-ttu-id="c1419-201">在上面的示例中，我们使用`ViewBag`对象将数据从控制器传递到视图。</span><span class="sxs-lookup"><span data-stu-id="c1419-201">In the sample above, we used a `ViewBag` object to pass data from the controller to a view.</span></span> <span data-ttu-id="c1419-202">稍后在本教程中，我们将使用视图模型将数据从控制器传递给视图。</span><span class="sxs-lookup"><span data-stu-id="c1419-202">Later in the tutorial, we will use a view model to pass data from a controller to a view.</span></span> <span data-ttu-id="c1419-203">将数据传递到视图模型方法通常更方法相比是首选视图包。</span><span class="sxs-lookup"><span data-stu-id="c1419-203">The view model approach to passing data is generally much preferred over the view bag approach.</span></span> <span data-ttu-id="c1419-204">请参阅博客文章[动态 V 强类型化视图](https://blogs.msdn.com/b/rickandy/archive/2011/01/28/dynamic-v-strongly-typed-views.aspx)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="c1419-204">See the blog entry [Dynamic V Strongly Typed Views](https://blogs.msdn.com/b/rickandy/archive/2011/01/28/dynamic-v-strongly-typed-views.aspx) for more information.</span></span> 

<span data-ttu-id="c1419-205">当然，这是一种类型的&quot;M&quot;模型，而不是数据库类。</span><span class="sxs-lookup"><span data-stu-id="c1419-205">Well, that was a kind of an &quot;M&quot; for model, but not the database kind.</span></span> <span data-ttu-id="c1419-206">让我们用学到的内容来创建一个电影数据库。</span><span class="sxs-lookup"><span data-stu-id="c1419-206">Let's take what we've learned and create a database of movies.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="c1419-207">[上一页](adding-a-controller.md)
> [下一页](adding-a-model.md)</span><span class="sxs-lookup"><span data-stu-id="c1419-207">[Previous](adding-a-controller.md)
[Next](adding-a-model.md)</span></span>
