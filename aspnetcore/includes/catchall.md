> [!WARNING]
> <span data-ttu-id="95832-101">由于路由中的 [bug](https://github.com/dotnet/aspnetcore/issues/18677)catch-all  参数可能无法正确匹配相应路由。</span><span class="sxs-lookup"><span data-stu-id="95832-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="95832-102">受此 Bug 影响的应用具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="95832-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="95832-103">“全部捕获”路由，例如 `{**slug}"`</span><span class="sxs-lookup"><span data-stu-id="95832-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="95832-104">“全部捕获”路由未能匹配应与之匹配的请求。</span><span class="sxs-lookup"><span data-stu-id="95832-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="95832-105">删除其他路由可使“全部捕获”路由开始运行。</span><span class="sxs-lookup"><span data-stu-id="95832-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="95832-106">请参阅 GitHub bug [18677](https://github.com/dotnet/aspnetcore/issues/18677) 和 [16579](https://github.com/dotnet/aspnetcore/issues/16579)，了解遇到此 bug 的示例。</span><span class="sxs-lookup"><span data-stu-id="95832-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="95832-107">此 bug 的选择加入修复包含在[.Net Core 3.1.301 SDK 和更高版本](https://dotnet.microsoft.com/download/dotnet-core/3.1)中。</span><span class="sxs-lookup"><span data-stu-id="95832-107">An opt-in fix for this bug is contained in [.NET Core 3.1.301 SDK and later](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="95832-108">下面的代码设置修复此 bug 的内部开关：</span><span class="sxs-lookup"><span data-stu-id="95832-108">The following code sets an internal switch that fixes this bug:</span></span>
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```