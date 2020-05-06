> [!WARNING]
> <span data-ttu-id="99d73-101">由于路由中的 [bug](https://github.com/dotnet/aspnetcore/issues/18677)catch-all  参数可能无法正确匹配相应路由。</span><span class="sxs-lookup"><span data-stu-id="99d73-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="99d73-102">受此 Bug 影响的应用具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="99d73-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="99d73-103">“全部捕获”路由，例如 `{**slug}"`</span><span class="sxs-lookup"><span data-stu-id="99d73-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="99d73-104">“全部捕获”路由未能匹配应与之匹配的请求。</span><span class="sxs-lookup"><span data-stu-id="99d73-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="99d73-105">删除其他路由可使“全部捕获”路由开始运行。</span><span class="sxs-lookup"><span data-stu-id="99d73-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="99d73-106">请参阅 GitHub bug [18677](https://github.com/dotnet/aspnetcore/issues/18677) 和 [16579](https://github.com/dotnet/aspnetcore/issues/16579)，了解遇到此 bug 的示例。</span><span class="sxs-lookup"><span data-stu-id="99d73-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="99d73-107">已经在计划此 Bug 的选择加入修补程序。</span><span class="sxs-lookup"><span data-stu-id="99d73-107">An opt-in fix for this bug is planned.</span></span> <span data-ttu-id="99d73-108">发布这个修补程序时将更新这篇文档。</span><span class="sxs-lookup"><span data-stu-id="99d73-108">This doc will be updated when the patch is released.</span></span> <span data-ttu-id="99d73-109">发布修补程序后，可使用以下代码设置用于修复此 bug 的内部开关：</span><span class="sxs-lookup"><span data-stu-id="99d73-109">When the patch is released, the following code will set an internal switch that fixes this bug:</span></span>
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```