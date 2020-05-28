---
<span data-ttu-id="5e1ef-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-102">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-103">'Identity'</span></span>
- <span data-ttu-id="5e1ef-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-105">'Razor'</span></span>
- <span data-ttu-id="5e1ef-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-106">'SignalR' uid:</span></span> 

---
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="5e1ef-107">ASP.NET Core 中的响应缓存</span><span class="sxs-lookup"><span data-stu-id="5e1ef-107">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="5e1ef-108">作者： [John Luo](https://github.com/JunTaoLuo)、 [Rick Anderson](https://twitter.com/RickAndMSFT)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5e1ef-108">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5e1ef-109">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="5e1ef-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5e1ef-110">响应缓存可减少客户端或代理对 web 服务器发出的请求数。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-110">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="5e1ef-111">响应缓存还减少了 web 服务器生成响应所需的工作量。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-111">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="5e1ef-112">响应缓存由指定你希望客户端、代理和中间件缓存响应的方式的标头控制。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-112">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="5e1ef-113">[ResponseCache 属性](#responsecache-attribute)参与设置响应缓存标头。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-113">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers.</span></span> <span data-ttu-id="5e1ef-114">在[HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234)下，客户端和中间代理应遵循用于缓存响应的标头。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-114">Clients and intermediate proxies should honor the headers for caching responses under the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span>

<span data-ttu-id="5e1ef-115">对于遵循 HTTP 1.1 缓存规范的服务器端缓存，请使用[响应缓存中间件](xref:performance/caching/middleware)。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-115">For server-side caching that follows the HTTP 1.1 Caching specification, use [Response Caching Middleware](xref:performance/caching/middleware).</span></span> <span data-ttu-id="5e1ef-116">中间件可以使用 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 属性来影响服务器端的缓存行为。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-116">The middleware can use the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="5e1ef-117">基于 HTTP 的响应缓存</span><span class="sxs-lookup"><span data-stu-id="5e1ef-117">HTTP-based response caching</span></span>

<span data-ttu-id="5e1ef-118">[HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234)描述了 Internet 缓存的行为方式。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-118">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="5e1ef-119">用于缓存的主 HTTP 标头是[缓存控制](https://tools.ietf.org/html/rfc7234#section-5.2)，它用于指定缓存*指令*。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-119">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="5e1ef-120">指令控制缓存行为作为请求从客户端发送到服务器，而作为响应，使其从服务器到客户端的方式。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-120">The directives control caching behavior as requests make their way from clients to servers and as responses make their way from servers back to clients.</span></span> <span data-ttu-id="5e1ef-121">请求和响应在代理服务器之间移动，并且代理服务器还必须符合 HTTP 1.1 缓存规范。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-121">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="5e1ef-122">`Cache-Control`下表显示了常见的指令。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-122">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="5e1ef-123">指令</span><span class="sxs-lookup"><span data-stu-id="5e1ef-123">Directive</span></span>                                                       | <span data-ttu-id="5e1ef-124">操作</span><span class="sxs-lookup"><span data-stu-id="5e1ef-124">Action</span></span> |
| ---
<span data-ttu-id="5e1ef-125">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-125">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-126">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-126">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-127">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-127">'Identity'</span></span>
- <span data-ttu-id="5e1ef-128">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-128">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-129">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-129">'Razor'</span></span>
- <span data-ttu-id="5e1ef-130">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-130">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-131">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-131">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-132">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-132">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-133">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-133">'Identity'</span></span>
- <span data-ttu-id="5e1ef-134">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-134">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-135">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-135">'Razor'</span></span>
- <span data-ttu-id="5e1ef-136">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-136">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-137">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-137">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-138">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-138">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-139">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-139">'Identity'</span></span>
- <span data-ttu-id="5e1ef-140">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-140">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-141">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-141">'Razor'</span></span>
- <span data-ttu-id="5e1ef-142">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-142">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-143">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-143">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-144">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-144">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-145">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-145">'Identity'</span></span>
- <span data-ttu-id="5e1ef-146">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-146">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-147">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-147">'Razor'</span></span>
- <span data-ttu-id="5e1ef-148">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-148">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-149">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-149">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-150">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-151">'Identity'</span></span>
- <span data-ttu-id="5e1ef-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-153">'Razor'</span></span>
- <span data-ttu-id="5e1ef-154">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-155">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-155">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-156">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-157">'Identity'</span></span>
- <span data-ttu-id="5e1ef-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-159">'Razor'</span></span>
- <span data-ttu-id="5e1ef-160">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-161">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-161">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-162">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-163">'Identity'</span></span>
- <span data-ttu-id="5e1ef-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-165">'Razor'</span></span>
- <span data-ttu-id="5e1ef-166">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-166">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-167">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-167">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-168">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-169">'Identity'</span></span>
- <span data-ttu-id="5e1ef-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-171">'Razor'</span></span>
- <span data-ttu-id="5e1ef-172">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-173">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-173">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-174">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-175">'Identity'</span></span>
- <span data-ttu-id="5e1ef-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-177">'Razor'</span></span>
- <span data-ttu-id="5e1ef-178">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-179">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-179">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-180">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-181">'Identity'</span></span>
- <span data-ttu-id="5e1ef-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-183">'Razor'</span></span>
- <span data-ttu-id="5e1ef-184">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-185">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-185">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-186">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-187">'Identity'</span></span>
- <span data-ttu-id="5e1ef-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-189">'Razor'</span></span>
- <span data-ttu-id="5e1ef-190">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-191">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-191">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-192">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-193">'Identity'</span></span>
- <span data-ttu-id="5e1ef-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-195">'Razor'</span></span>
- <span data-ttu-id="5e1ef-196">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-197">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-197">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-198">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-199">'Identity'</span></span>
- <span data-ttu-id="5e1ef-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-201">'Razor'</span></span>
- <span data-ttu-id="5e1ef-202">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-203">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-203">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-204">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-205">'Identity'</span></span>
- <span data-ttu-id="5e1ef-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-207">'Razor'</span></span>
- <span data-ttu-id="5e1ef-208">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-209">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-209">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-210">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-211">'Identity'</span></span>
- <span data-ttu-id="5e1ef-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-213">'Razor'</span></span>
- <span data-ttu-id="5e1ef-214">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-215">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-215">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-216">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-217">'Identity'</span></span>
- <span data-ttu-id="5e1ef-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-219">'Razor'</span></span>
- <span data-ttu-id="5e1ef-220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-221">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-221">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-222">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-223">'Identity'</span></span>
- <span data-ttu-id="5e1ef-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-225">'Razor'</span></span>
- <span data-ttu-id="5e1ef-226">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-227">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-227">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-228">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-229">'Identity'</span></span>
- <span data-ttu-id="5e1ef-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-231">'Razor'</span></span>
- <span data-ttu-id="5e1ef-232">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-233">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-233">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-234">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-235">'Identity'</span></span>
- <span data-ttu-id="5e1ef-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-237">'Razor'</span></span>
- <span data-ttu-id="5e1ef-238">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-239">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-239">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-240">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-241">'Identity'</span></span>
- <span data-ttu-id="5e1ef-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-243">'Razor'</span></span>
- <span data-ttu-id="5e1ef-244">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-245">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-245">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-246">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-247">'Identity'</span></span>
- <span data-ttu-id="5e1ef-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-249">'Razor'</span></span>
- <span data-ttu-id="5e1ef-250">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-251">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-251">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-252">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-253">'Identity'</span></span>
- <span data-ttu-id="5e1ef-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-255">'Razor'</span></span>
- <span data-ttu-id="5e1ef-256">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-257">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-257">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-258">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-259">'Identity'</span></span>
- <span data-ttu-id="5e1ef-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-261">'Razor'</span></span>
- <span data-ttu-id="5e1ef-262">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-263">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-263">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-264">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-264">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-265">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-265">'Identity'</span></span>
- <span data-ttu-id="5e1ef-266">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-266">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-267">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-267">'Razor'</span></span>
- <span data-ttu-id="5e1ef-268">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-268">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-269">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-269">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-270">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-270">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-271">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-271">'Identity'</span></span>
- <span data-ttu-id="5e1ef-272">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-272">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-273">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-273">'Razor'</span></span>
- <span data-ttu-id="5e1ef-274">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-274">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-275">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-275">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-276">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-277">'Identity'</span></span>
- <span data-ttu-id="5e1ef-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-279">'Razor'</span></span>
- <span data-ttu-id="5e1ef-280">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-281">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-281">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-282">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-283">'Identity'</span></span>
- <span data-ttu-id="5e1ef-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-285">'Razor'</span></span>
- <span data-ttu-id="5e1ef-286">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-287">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-287">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-288">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-288">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-289">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-289">'Identity'</span></span>
- <span data-ttu-id="5e1ef-290">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-290">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-291">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-291">'Razor'</span></span>
- <span data-ttu-id="5e1ef-292">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-292">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-293">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-293">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-294">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-294">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-295">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-295">'Identity'</span></span>
- <span data-ttu-id="5e1ef-296">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-296">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-297">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-297">'Razor'</span></span>
- <span data-ttu-id="5e1ef-298">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-298">'SignalR' uid:</span></span> 

<span data-ttu-id="5e1ef-299">-------------------------------- |---标题：作者：说明： monikerRange： ms. 作者： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="5e1ef-299">-------------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-300">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-301">'Identity'</span></span>
- <span data-ttu-id="5e1ef-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-303">'Razor'</span></span>
- <span data-ttu-id="5e1ef-304">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-304">'SignalR' uid:</span></span> 

<span data-ttu-id="5e1ef-305">--- | |[公共](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)|缓存可以存储响应。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-305">--- | | [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | A cache may store the response.</span></span> <span data-ttu-id="5e1ef-306">| |[私有](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)|共享缓存不能存储响应。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-306">| | [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | The response must not be stored by a shared cache.</span></span> <span data-ttu-id="5e1ef-307">专用缓存可以存储和重用响应。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-307">A private cache may store and reuse the response.</span></span> <span data-ttu-id="5e1ef-308">| |[最大期限](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)|客户端不接受其期限大于指定秒数的响应。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-308">| | [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | The client doesn't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="5e1ef-309">示例： `max-age=60` （60秒）、 `max-age=2592000` （1个月） | |[非缓存](https://tools.ietf.org/html/rfc7234#section-5.2.1.4)  | **请求时**：缓存不能使用存储的响应来满足请求。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-309">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month) | | [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="5e1ef-310">源服务器重新生成客户端的响应，中间件更新其缓存中存储的响应。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-310">The origin server regenerates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="5e1ef-311">**响应：在**源服务器上没有验证的后续请求不得使用响应。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-311">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> <span data-ttu-id="5e1ef-312">| |[无-商店](https://tools.ietf.org/html/rfc7234#section-5.2.1.5)  | **请求时**：缓存不能存储请求。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-312">| | [no-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="5e1ef-313">**响应**：缓存不能存储响应的任何部分。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-313">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="5e1ef-314">下表显示了在缓存中扮演角色的其他缓存标头。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-314">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="5e1ef-315">Header</span><span class="sxs-lookup"><span data-stu-id="5e1ef-315">Header</span></span>                                                     | <span data-ttu-id="5e1ef-316">函数</span><span class="sxs-lookup"><span data-stu-id="5e1ef-316">Function</span></span> |
| ---
<span data-ttu-id="5e1ef-317">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-317">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-318">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-319">'Identity'</span></span>
- <span data-ttu-id="5e1ef-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-321">'Razor'</span></span>
- <span data-ttu-id="5e1ef-322">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-323">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-323">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-324">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-324">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-325">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-325">'Identity'</span></span>
- <span data-ttu-id="5e1ef-326">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-326">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-327">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-327">'Razor'</span></span>
- <span data-ttu-id="5e1ef-328">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-328">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-329">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-329">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-330">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-330">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-331">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-331">'Identity'</span></span>
- <span data-ttu-id="5e1ef-332">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-332">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-333">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-333">'Razor'</span></span>
- <span data-ttu-id="5e1ef-334">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-334">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-335">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-335">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-336">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-336">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-337">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-337">'Identity'</span></span>
- <span data-ttu-id="5e1ef-338">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-338">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-339">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-339">'Razor'</span></span>
- <span data-ttu-id="5e1ef-340">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-340">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-341">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-341">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-342">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-342">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-343">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-343">'Identity'</span></span>
- <span data-ttu-id="5e1ef-344">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-344">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-345">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-345">'Razor'</span></span>
- <span data-ttu-id="5e1ef-346">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-346">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-347">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-347">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-348">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-348">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-349">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-349">'Identity'</span></span>
- <span data-ttu-id="5e1ef-350">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-350">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-351">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-351">'Razor'</span></span>
- <span data-ttu-id="5e1ef-352">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-352">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-353">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-353">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-354">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-354">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-355">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-355">'Identity'</span></span>
- <span data-ttu-id="5e1ef-356">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-356">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-357">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-357">'Razor'</span></span>
- <span data-ttu-id="5e1ef-358">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-358">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-359">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-359">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-360">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-360">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-361">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-361">'Identity'</span></span>
- <span data-ttu-id="5e1ef-362">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-362">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-363">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-363">'Razor'</span></span>
- <span data-ttu-id="5e1ef-364">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-364">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-365">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-365">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-366">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-367">'Identity'</span></span>
- <span data-ttu-id="5e1ef-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-369">'Razor'</span></span>
- <span data-ttu-id="5e1ef-370">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-371">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-371">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-372">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-372">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-373">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-373">'Identity'</span></span>
- <span data-ttu-id="5e1ef-374">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-374">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-375">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-375">'Razor'</span></span>
- <span data-ttu-id="5e1ef-376">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-376">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-377">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-377">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-378">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-378">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-379">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-379">'Identity'</span></span>
- <span data-ttu-id="5e1ef-380">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-380">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-381">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-381">'Razor'</span></span>
- <span data-ttu-id="5e1ef-382">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-382">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-383">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-383">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-384">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-384">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-385">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-385">'Identity'</span></span>
- <span data-ttu-id="5e1ef-386">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-386">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-387">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-387">'Razor'</span></span>
- <span data-ttu-id="5e1ef-388">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-388">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-389">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-389">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-390">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-390">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-391">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-391">'Identity'</span></span>
- <span data-ttu-id="5e1ef-392">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-392">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-393">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-393">'Razor'</span></span>
- <span data-ttu-id="5e1ef-394">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-394">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-395">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-395">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-396">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-397">'Identity'</span></span>
- <span data-ttu-id="5e1ef-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-399">'Razor'</span></span>
- <span data-ttu-id="5e1ef-400">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-400">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-401">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-401">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-402">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-403">'Identity'</span></span>
- <span data-ttu-id="5e1ef-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-405">'Razor'</span></span>
- <span data-ttu-id="5e1ef-406">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-407">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-407">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-408">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-409">'Identity'</span></span>
- <span data-ttu-id="5e1ef-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-411">'Razor'</span></span>
- <span data-ttu-id="5e1ef-412">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-413">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-413">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-414">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-414">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-415">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-415">'Identity'</span></span>
- <span data-ttu-id="5e1ef-416">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-416">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-417">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-417">'Razor'</span></span>
- <span data-ttu-id="5e1ef-418">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-418">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-419">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-419">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-420">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-420">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-421">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-421">'Identity'</span></span>
- <span data-ttu-id="5e1ef-422">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-422">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-423">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-423">'Razor'</span></span>
- <span data-ttu-id="5e1ef-424">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-424">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-425">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-425">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-426">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-426">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-427">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-427">'Identity'</span></span>
- <span data-ttu-id="5e1ef-428">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-428">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-429">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-429">'Razor'</span></span>
- <span data-ttu-id="5e1ef-430">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-430">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-431">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-431">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-432">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-432">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-433">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-433">'Identity'</span></span>
- <span data-ttu-id="5e1ef-434">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-434">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-435">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-435">'Razor'</span></span>
- <span data-ttu-id="5e1ef-436">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-436">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-437">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-437">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-438">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-438">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-439">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-439">'Identity'</span></span>
- <span data-ttu-id="5e1ef-440">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-440">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-441">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-441">'Razor'</span></span>
- <span data-ttu-id="5e1ef-442">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-442">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-443">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-443">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-444">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-445">'Identity'</span></span>
- <span data-ttu-id="5e1ef-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-447">'Razor'</span></span>
- <span data-ttu-id="5e1ef-448">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-449">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-449">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-450">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-451">'Identity'</span></span>
- <span data-ttu-id="5e1ef-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-453">'Razor'</span></span>
- <span data-ttu-id="5e1ef-454">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-455">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-455">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-456">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-456">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-457">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-457">'Identity'</span></span>
- <span data-ttu-id="5e1ef-458">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-458">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-459">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-459">'Razor'</span></span>
- <span data-ttu-id="5e1ef-460">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-460">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-461">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-461">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-462">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-462">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-463">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-463">'Identity'</span></span>
- <span data-ttu-id="5e1ef-464">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-464">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-465">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-465">'Razor'</span></span>
- <span data-ttu-id="5e1ef-466">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-466">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-467">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-467">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-468">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-468">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-469">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-469">'Identity'</span></span>
- <span data-ttu-id="5e1ef-470">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-470">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-471">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-471">'Razor'</span></span>
- <span data-ttu-id="5e1ef-472">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-472">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-473">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-473">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-474">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-474">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-475">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-475">'Identity'</span></span>
- <span data-ttu-id="5e1ef-476">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-476">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-477">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-477">'Razor'</span></span>
- <span data-ttu-id="5e1ef-478">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-478">'SignalR' uid:</span></span> 

<span data-ttu-id="5e1ef-479">----------------------------- |---标题：作者：说明： monikerRange： ms. 作者： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="5e1ef-479">----------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-480">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-480">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-481">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-481">'Identity'</span></span>
- <span data-ttu-id="5e1ef-482">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-482">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-483">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-483">'Razor'</span></span>
- <span data-ttu-id="5e1ef-484">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-484">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-485">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-485">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-486">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-486">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-487">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-487">'Identity'</span></span>
- <span data-ttu-id="5e1ef-488">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-488">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-489">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-489">'Razor'</span></span>
- <span data-ttu-id="5e1ef-490">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-490">'SignalR' uid:</span></span> 

<span data-ttu-id="5e1ef-491">---- | |[Age](https://tools.ietf.org/html/rfc7234#section-5.1) |在源服务器上生成或成功验证响应以来的时间量（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-491">---- | | [Age](https://tools.ietf.org/html/rfc7234#section-5.1)     | An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> <span data-ttu-id="5e1ef-492">| |[过期](https://tools.ietf.org/html/rfc7234#section-5.3)|响应被视为过时的时间。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-492">| | [Expires](https://tools.ietf.org/html/rfc7234#section-5.3) | The time after which the response is considered stale.</span></span> <span data-ttu-id="5e1ef-493">| |[Pragma](https://tools.ietf.org/html/rfc7234#section-5.4) |存在，以便向后兼容 HTTP/1.0 缓存以设置 `no-cache` 行为。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="5e1ef-494">如果该 `Cache-Control` 标头存在，则将 `Pragma` 忽略该标头。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-494">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> <span data-ttu-id="5e1ef-495">| |[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) |指定不能发送缓存的响应，除非 `Vary` 缓存响应的原始请求和新请求中的所有标头字段都匹配。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-495">| | [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="5e1ef-496">基于 HTTP 的缓存遵循请求缓存控制指令</span><span class="sxs-lookup"><span data-stu-id="5e1ef-496">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="5e1ef-497">[缓存控制标头的 HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234#section-5.2)要求使用缓存来服从 `Cache-Control` 客户端发送的有效标头。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-497">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="5e1ef-498">客户端可以使用 `no-cache` 标头值发出请求，并强制服务器为每个请求生成新的响应。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-498">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="5e1ef-499">`Cache-Control`如果考虑 HTTP 缓存的目标，则始终考虑客户端请求标头是有意义的。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-499">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="5e1ef-500">在官方规范下，缓存旨在减少在客户端、代理和服务器网络中满足请求的延迟和网络开销。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-500">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="5e1ef-501">它不一定是控制源服务器上的负载的一种方法。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-501">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="5e1ef-502">使用[响应缓存中间件](xref:performance/caching/middleware)时，无开发人员对此缓存行为的控制，因为中间件遵循官方缓存规范。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-502">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="5e1ef-503">[中间件的计划增强功能](https://github.com/dotnet/AspNetCore/issues/2612)是在 `Cache-Control` 决定为缓存的响应提供服务时，将中间件配置为忽略请求标头的机会。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-503">[Planned enhancements to the middleware](https://github.com/dotnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="5e1ef-504">计划的增强功能提供了一种更好地控制服务器负载的机会。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-504">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="5e1ef-505">ASP.NET Core 中的其他缓存技术</span><span class="sxs-lookup"><span data-stu-id="5e1ef-505">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="5e1ef-506">内存中缓存</span><span class="sxs-lookup"><span data-stu-id="5e1ef-506">In-memory caching</span></span>

<span data-ttu-id="5e1ef-507">内存中缓存使用服务器内存来存储缓存的数据。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-507">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="5e1ef-508">这种类型的缓存适用于单个服务器或使用*粘滞会话*的多台服务器。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-508">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="5e1ef-509">粘滞会话表示来自客户端的请求始终路由到同一服务器进行处理。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-509">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="5e1ef-510">有关详细信息，请参阅 <xref:performance/caching/memory>。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-510">For more information, see <xref:performance/caching/memory>.</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="5e1ef-511">分布式缓存</span><span class="sxs-lookup"><span data-stu-id="5e1ef-511">Distributed Cache</span></span>

<span data-ttu-id="5e1ef-512">当应用程序托管在云或服务器场中时，使用分布式缓存将数据存储在内存中。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-512">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="5e1ef-513">缓存在处理请求的服务器之间共享。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-513">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="5e1ef-514">如果客户端的缓存数据可用，则客户端可以提交由组中的任何服务器处理的请求。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-514">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="5e1ef-515">ASP.NET Core 适用于 SQL Server、 [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)和[NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-515">ASP.NET Core works with SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), and [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) distributed caches.</span></span>

<span data-ttu-id="5e1ef-516">有关详细信息，请参阅 <xref:performance/caching/distributed>。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-516">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="5e1ef-517">缓存标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="5e1ef-517">Cache Tag Helper</span></span>

<span data-ttu-id="5e1ef-518">使用缓存标记帮助程序从 MVC 视图或页面缓存内容 Razor 。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-518">Cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="5e1ef-519">缓存标记帮助程序使用内存中缓存来存储数据。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-519">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="5e1ef-520">有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-520">For more information, see <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="5e1ef-521">分布式缓存标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="5e1ef-521">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="5e1ef-522">Razor使用分布式缓存标记帮助程序在分布式云和 web 场方案中，通过 MVC 视图或页面缓存内容。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-522">Cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="5e1ef-523">分布式缓存标记帮助程序使用 SQL Server、 [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)或[NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)来存储数据。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-523">The Distributed Cache Tag Helper uses SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), or [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) to store data.</span></span>

<span data-ttu-id="5e1ef-524">有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-524">For more information, see <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="5e1ef-525">ResponseCache 特性</span><span class="sxs-lookup"><span data-stu-id="5e1ef-525">ResponseCache attribute</span></span>

<span data-ttu-id="5e1ef-526"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>指定在响应缓存中设置适当的标头所需的参数。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-526">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="5e1ef-527">禁用包含经过身份验证的客户端信息的内容的缓存。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-527">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="5e1ef-528">只应为不会根据用户身份更改或用户是否已登录的内容启用缓存。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-528">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="5e1ef-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>根据给定的查询键列表的值，改变存储的响应。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="5e1ef-530">当提供了的单个值时 `*` ，中间件将根据所有请求查询字符串参数来改变响应。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-530">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span>

<span data-ttu-id="5e1ef-531">必须启用[响应缓存中间件](xref:performance/caching/middleware)才能设置 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 属性。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-531">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="5e1ef-532">否则，会引发运行时异常。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-532">Otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="5e1ef-533">此属性没有相应的 HTTP 标头 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-533">There isn't a corresponding HTTP header for the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="5e1ef-534">属性是由响应缓存中间件处理的 HTTP 功能。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-534">The property is an HTTP feature handled by Response Caching Middleware.</span></span> <span data-ttu-id="5e1ef-535">对于用于缓存响应的中间件，查询字符串和查询字符串值必须与上一个请求匹配。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-535">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="5e1ef-536">例如，请考虑下表中显示的请求和结果的顺序。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-536">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="5e1ef-537">请求</span><span class="sxs-lookup"><span data-stu-id="5e1ef-537">Request</span></span>                          | <span data-ttu-id="5e1ef-538">结果</span><span class="sxs-lookup"><span data-stu-id="5e1ef-538">Result</span></span>                    |
| ---
<span data-ttu-id="5e1ef-539">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-539">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-540">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-540">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-541">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-541">'Identity'</span></span>
- <span data-ttu-id="5e1ef-542">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-542">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-543">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-543">'Razor'</span></span>
- <span data-ttu-id="5e1ef-544">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-544">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-545">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-545">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-546">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-546">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-547">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-547">'Identity'</span></span>
- <span data-ttu-id="5e1ef-548">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-548">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-549">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-549">'Razor'</span></span>
- <span data-ttu-id="5e1ef-550">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-550">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-551">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-551">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-552">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-553">'Identity'</span></span>
- <span data-ttu-id="5e1ef-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-555">'Razor'</span></span>
- <span data-ttu-id="5e1ef-556">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-557">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-557">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-558">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-558">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-559">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-559">'Identity'</span></span>
- <span data-ttu-id="5e1ef-560">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-560">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-561">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-561">'Razor'</span></span>
- <span data-ttu-id="5e1ef-562">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-562">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-563">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-563">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-564">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-564">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-565">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-565">'Identity'</span></span>
- <span data-ttu-id="5e1ef-566">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-566">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-567">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-567">'Razor'</span></span>
- <span data-ttu-id="5e1ef-568">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-568">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-569">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-569">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-570">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-570">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-571">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-571">'Identity'</span></span>
- <span data-ttu-id="5e1ef-572">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-572">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-573">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-573">'Razor'</span></span>
- <span data-ttu-id="5e1ef-574">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-574">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-575">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-575">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-576">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-576">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-577">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-577">'Identity'</span></span>
- <span data-ttu-id="5e1ef-578">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-578">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-579">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-579">'Razor'</span></span>
- <span data-ttu-id="5e1ef-580">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-580">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-581">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-581">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-582">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-582">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-583">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-583">'Identity'</span></span>
- <span data-ttu-id="5e1ef-584">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-584">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-585">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-585">'Razor'</span></span>
- <span data-ttu-id="5e1ef-586">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-586">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-587">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-587">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-588">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-588">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-589">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-589">'Identity'</span></span>
- <span data-ttu-id="5e1ef-590">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-590">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-591">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-591">'Razor'</span></span>
- <span data-ttu-id="5e1ef-592">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-592">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-593">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-593">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-594">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-595">'Identity'</span></span>
- <span data-ttu-id="5e1ef-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-597">'Razor'</span></span>
- <span data-ttu-id="5e1ef-598">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-599">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-599">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-600">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-600">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-601">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-601">'Identity'</span></span>
- <span data-ttu-id="5e1ef-602">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-602">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-603">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-603">'Razor'</span></span>
- <span data-ttu-id="5e1ef-604">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-604">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-605">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-605">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-606">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-606">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-607">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-607">'Identity'</span></span>
- <span data-ttu-id="5e1ef-608">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-608">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-609">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-609">'Razor'</span></span>
- <span data-ttu-id="5e1ef-610">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-610">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-611">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-611">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-612">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-612">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-613">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-613">'Identity'</span></span>
- <span data-ttu-id="5e1ef-614">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-614">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-615">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-615">'Razor'</span></span>
- <span data-ttu-id="5e1ef-616">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-616">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-617">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-617">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-618">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-618">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-619">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-619">'Identity'</span></span>
- <span data-ttu-id="5e1ef-620">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-620">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-621">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-621">'Razor'</span></span>
- <span data-ttu-id="5e1ef-622">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-622">'SignalR' uid:</span></span> 

<span data-ttu-id="5e1ef-623">---------------- |---标题：作者：说明： monikerRange： ms. 作者： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="5e1ef-623">---------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-624">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-624">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-625">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-625">'Identity'</span></span>
- <span data-ttu-id="5e1ef-626">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-626">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-627">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-627">'Razor'</span></span>
- <span data-ttu-id="5e1ef-628">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-628">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-629">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-629">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-630">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-630">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-631">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-631">'Identity'</span></span>
- <span data-ttu-id="5e1ef-632">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-632">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-633">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-633">'Razor'</span></span>
- <span data-ttu-id="5e1ef-634">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-634">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-635">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-635">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-636">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-637">'Identity'</span></span>
- <span data-ttu-id="5e1ef-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-639">'Razor'</span></span>
- <span data-ttu-id="5e1ef-640">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-641">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-641">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-642">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-642">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-643">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-643">'Identity'</span></span>
- <span data-ttu-id="5e1ef-644">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-644">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-645">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-645">'Razor'</span></span>
- <span data-ttu-id="5e1ef-646">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-646">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-647">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-647">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-648">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-648">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-649">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-649">'Identity'</span></span>
- <span data-ttu-id="5e1ef-650">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-650">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-651">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-651">'Razor'</span></span>
- <span data-ttu-id="5e1ef-652">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-652">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-653">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-653">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-654">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-654">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-655">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-655">'Identity'</span></span>
- <span data-ttu-id="5e1ef-656">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-656">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-657">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-657">'Razor'</span></span>
- <span data-ttu-id="5e1ef-658">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-658">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-659">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-659">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-660">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-660">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-661">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-661">'Identity'</span></span>
- <span data-ttu-id="5e1ef-662">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-662">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-663">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-663">'Razor'</span></span>
- <span data-ttu-id="5e1ef-664">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-664">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-665">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-665">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-666">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-666">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-667">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-667">'Identity'</span></span>
- <span data-ttu-id="5e1ef-668">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-668">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-669">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-669">'Razor'</span></span>
- <span data-ttu-id="5e1ef-670">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-670">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-671">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-671">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-672">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-672">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-673">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-673">'Identity'</span></span>
- <span data-ttu-id="5e1ef-674">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-674">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-675">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-675">'Razor'</span></span>
- <span data-ttu-id="5e1ef-676">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-676">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5e1ef-677">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-677">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e1ef-678">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-678">'Blazor'</span></span>
- <span data-ttu-id="5e1ef-679">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-679">'Identity'</span></span>
- <span data-ttu-id="5e1ef-680">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-680">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e1ef-681">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e1ef-681">'Razor'</span></span>
- <span data-ttu-id="5e1ef-682">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5e1ef-682">'SignalR' uid:</span></span> 

<span data-ttu-id="5e1ef-683">------------- | |`http://example.com?key1=value1` |从服务器返回的。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-683">------------- | | `http://example.com?key1=value1` | Returned from the server.</span></span> <span data-ttu-id="5e1ef-684">| |`http://example.com?key1=value1` |从中间件返回。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-684">| | `http://example.com?key1=value1` | Returned from middleware.</span></span> <span data-ttu-id="5e1ef-685">| |`http://example.com?key1=value2` |从服务器返回的。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-685">| | `http://example.com?key1=value2` | Returned from the server.</span></span> |

<span data-ttu-id="5e1ef-686">第一个请求由服务器返回，并缓存在中间件中。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-686">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="5e1ef-687">第二个请求是由中间件返回的，因为查询字符串与上一个请求匹配。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-687">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="5e1ef-688">第三个请求不在中间件缓存中，因为查询字符串值与以前的请求不匹配。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-688">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span>

<span data-ttu-id="5e1ef-689"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>用于配置和创建（通过 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> ） `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` 。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-689">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> is used to configure and create (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`.</span></span> <span data-ttu-id="5e1ef-690">`ResponseCacheFilter`执行更新相应 HTTP 标头和响应功能的工作。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-690">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="5e1ef-691">筛选器：</span><span class="sxs-lookup"><span data-stu-id="5e1ef-691">The filter:</span></span>

* <span data-ttu-id="5e1ef-692">删除、和的任何现有标头 `Vary` `Cache-Control` `Pragma` 。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-692">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span>
* <span data-ttu-id="5e1ef-693">根据中设置的属性写出适当的标头 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-693">Writes out the appropriate headers based on the properties set in the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.</span></span>
* <span data-ttu-id="5e1ef-694">如果设置了，则更新响应缓存 HTTP 功能 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-694">Updates the response caching HTTP feature if <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> is set.</span></span>

### <a name="vary"></a><span data-ttu-id="5e1ef-695">大</span><span class="sxs-lookup"><span data-stu-id="5e1ef-695">Vary</span></span>

<span data-ttu-id="5e1ef-696">仅当设置了属性时，才写入此标头 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> 。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-696">This header is only written when the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property is set.</span></span> <span data-ttu-id="5e1ef-697">属性设置为 `Vary` 属性的值。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-697">The property set to the `Vary` property's value.</span></span> <span data-ttu-id="5e1ef-698">下面的示例使用 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> 属性：</span><span class="sxs-lookup"><span data-stu-id="5e1ef-698">The following sample uses the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

<span data-ttu-id="5e1ef-699">使用示例应用程序，使用浏览器的网络工具查看响应标头。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-699">Using the sample app, view the response headers with the browser's network tools.</span></span> <span data-ttu-id="5e1ef-700">以下响应标头随 Cache1 页响应一起发送：</span><span class="sxs-lookup"><span data-stu-id="5e1ef-700">The following response headers are sent with the Cache1 page response:</span></span>

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a><span data-ttu-id="5e1ef-701">NoStore 和 Location。 None</span><span class="sxs-lookup"><span data-stu-id="5e1ef-701">NoStore and Location.None</span></span>

<span data-ttu-id="5e1ef-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>覆盖其他大多数属性。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> overrides most of the other properties.</span></span> <span data-ttu-id="5e1ef-703">当此属性设置为时 `true` ， `Cache-Control` 标头将设置为 `no-store` 。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-703">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="5e1ef-704">如果 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 设置为 `None` ：</span><span class="sxs-lookup"><span data-stu-id="5e1ef-704">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is set to `None`:</span></span>

* <span data-ttu-id="5e1ef-705">`Cache-Control` 设置为 `no-store,no-cache`。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-705">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="5e1ef-706">`Pragma` 设置为 `no-cache`。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-706">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="5e1ef-707">如果 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 为 `false` 且 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 为 `None` ，则 `Cache-Control` 和 `Pragma` 均设置为 `no-cache` 。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-707">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is `false` and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is `None`, `Cache-Control`, and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="5e1ef-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>对于错误页，通常设置为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is typically set to `true` for error pages.</span></span> <span data-ttu-id="5e1ef-709">示例应用中的 Cache2 页面生成响应标头，以指示客户端不存储响应。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-709">The Cache2 page in the sample app produces response headers that instruct the client not to store the response.</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

<span data-ttu-id="5e1ef-710">示例应用返回具有以下标头的 Cache2 页：</span><span class="sxs-lookup"><span data-stu-id="5e1ef-710">The sample app returns the Cache2 page with the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="5e1ef-711">位置和持续时间</span><span class="sxs-lookup"><span data-stu-id="5e1ef-711">Location and Duration</span></span>

<span data-ttu-id="5e1ef-712">若要启用缓存， <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> 必须将设置为正值，并且 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 必须为 `Any` （默认值）或 `Client` 。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-712">To enable caching, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> must be set to a positive value and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="5e1ef-713">框架将 `Cache-Control` 标头设置为位置值，后跟 `max-age` 响应的。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-713">The framework sets the `Cache-Control` header to the location value followed by the `max-age` of the response.</span></span>

<span data-ttu-id="5e1ef-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>的选项， `Any` 并 `Client` 分别转换为 `Cache-Control` 和的标头值 `public` `private` 。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="5e1ef-715">如 "NoStore"[和 "Location](#nostore-and-locationnone) " 部分所述，将设置 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 为，并将 `None` `Cache-Control` `Pragma` 标头设置为 `no-cache` 。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-715">As noted in the [NoStore and Location.None](#nostore-and-locationnone) section, setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="5e1ef-716">`Location.Any`（ `Cache-Control` 设置为 `public` ）表示*客户端或任何中间代理*可以缓存值，包括[响应缓存中间件](xref:performance/caching/middleware)。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-716">`Location.Any` (`Cache-Control` set to `public`) indicates that the *client or any intermediate proxy* may cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="5e1ef-717">`Location.Client`（ `Cache-Control` 设置为 `private` ）表示*只有客户端*可以缓存该值。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-717">`Location.Client` (`Cache-Control` set to `private`) indicates that *only the client* may cache the value.</span></span> <span data-ttu-id="5e1ef-718">中间缓存不应缓存值，包括[响应缓存中间件](xref:performance/caching/middleware)。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-718">No intermediate cache should cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="5e1ef-719">缓存控制标头仅向客户端和中间代理提供指导，以及如何缓存响应。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-719">Cache control headers merely provide guidance to clients and intermediary proxies when and how to cache responses.</span></span> <span data-ttu-id="5e1ef-720">不保证客户端和代理将遵循[HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234)。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-720">There's no guarantee that clients and proxies will honor the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span> <span data-ttu-id="5e1ef-721">[响应缓存中间件](xref:performance/caching/middleware)始终遵循由规范布局的缓存规则。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-721">[Response Caching Middleware](xref:performance/caching/middleware) always follows the caching rules laid out by the specification.</span></span>

<span data-ttu-id="5e1ef-722">下面的示例演示示例应用中的 Cache3 页模型以及通过设置 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> 并保留默认值而生成的标头 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> ：</span><span class="sxs-lookup"><span data-stu-id="5e1ef-722">The following example shows the Cache3 page model from the sample app and the headers produced by setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> and leaving the default <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> value:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

<span data-ttu-id="5e1ef-723">示例应用返回具有以下标头的 Cache3 页：</span><span class="sxs-lookup"><span data-stu-id="5e1ef-723">The sample app returns the Cache3 page with the following header:</span></span>

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a><span data-ttu-id="5e1ef-724">缓存配置文件</span><span class="sxs-lookup"><span data-stu-id="5e1ef-724">Cache profiles</span></span>

<span data-ttu-id="5e1ef-725">在中设置 MVC/Pages 时，可以将缓存配置文件配置为选项，而不是在许多控制器操作属性上复制响应缓存设置 Razor `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-725">Instead of duplicating response cache settings on many controller action attributes, cache profiles can be configured as options when setting up MVC/Razor Pages in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5e1ef-726">在引用的缓存配置文件中找到的值将用作默认值 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> ，并由特性上指定的任何属性重写。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-726">Values found in a referenced cache profile are used as the defaults by the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="5e1ef-727">设置缓存配置文件。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-727">Set up a cache profile.</span></span> <span data-ttu-id="5e1ef-728">下面的示例显示了示例应用中的30秒缓存配置文件 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="5e1ef-728">The following example shows a 30 second cache profile in the sample app's `Startup.ConfigureServices`:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

<span data-ttu-id="5e1ef-729">示例应用的 Cache4 页面模型引用 `Default30` 缓存配置文件：</span><span class="sxs-lookup"><span data-stu-id="5e1ef-729">The sample app's Cache4 page model references the `Default30` cache profile:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<span data-ttu-id="5e1ef-730"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>可应用于：</span><span class="sxs-lookup"><span data-stu-id="5e1ef-730">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> can be applied to:</span></span>

* Razor<span data-ttu-id="5e1ef-731">页处理程序（类）：特性不能应用于处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-731"> Page handlers (classes): Attributes can't be applied to handler methods.</span></span>
* <span data-ttu-id="5e1ef-732">MVC 控制器（类）。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-732">MVC controllers (classes).</span></span>
* <span data-ttu-id="5e1ef-733">MVC 操作（方法）：方法级特性会替代类级特性中指定的设置。</span><span class="sxs-lookup"><span data-stu-id="5e1ef-733">MVC actions (methods): Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="5e1ef-734">缓存配置文件将生成的标头应用到 Cache4 页响应 `Default30` ：</span><span class="sxs-lookup"><span data-stu-id="5e1ef-734">The resulting header applied to the Cache4 page response by the `Default30` cache profile:</span></span>

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a><span data-ttu-id="5e1ef-735">其他资源</span><span class="sxs-lookup"><span data-stu-id="5e1ef-735">Additional resources</span></span>

* [<span data-ttu-id="5e1ef-736">在缓存中存储响应</span><span class="sxs-lookup"><span data-stu-id="5e1ef-736">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="5e1ef-737">缓存-控制</span><span class="sxs-lookup"><span data-stu-id="5e1ef-737">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
