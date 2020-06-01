---
<span data-ttu-id="7f0d2-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-102">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-103">'Identity'</span></span>
- <span data-ttu-id="7f0d2-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-105">'Razor'</span></span>
- <span data-ttu-id="7f0d2-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-106">'SignalR' uid:</span></span> 

---
# <a name="compare-grpc-services-with-http-apis"></a><span data-ttu-id="7f0d2-107">比较 gRPC 服务和 HTTP API</span><span class="sxs-lookup"><span data-stu-id="7f0d2-107">Compare gRPC services with HTTP APIs</span></span>

<span data-ttu-id="7f0d2-108">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="7f0d2-108">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="7f0d2-109">本文介绍如何将 [gRPC 服务](https://grpc.io/docs/guides/)与具有 JSON 的 HTTP API（包括 ASP.NET Core [Web API](xref:web-api/index)）进行比较。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-109">This article explains how [gRPC services](https://grpc.io/docs/guides/) compare to HTTP APIs with JSON (including ASP.NET Core [web APIs](xref:web-api/index)).</span></span> <span data-ttu-id="7f0d2-110">用于为应用提供 API 的技术是一个重要选择，与 HTTP API 相比，gRPC 提供独特优势。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-110">The technology used to provide an API for your app is an important choice, and gRPC offers unique benefits compared to HTTP APIs.</span></span> <span data-ttu-id="7f0d2-111">本文讨论 gRPC 的优点和缺点，并提供优先于其他技术选择使用 gRPC 的建议方案。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-111">This article discusses the strengths and weaknesses of gRPC and recommends scenarios for using gRPC over other technologies.</span></span>

## <a name="high-level-comparison"></a><span data-ttu-id="7f0d2-112">概括比较</span><span class="sxs-lookup"><span data-stu-id="7f0d2-112">High-level comparison</span></span>

<span data-ttu-id="7f0d2-113">下表对 gRPC 和具有 JSON 的 HTTP API 之间的功能进行了简单比较。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-113">The following table offers a high-level comparison of features between gRPC and HTTP APIs with JSON.</span></span>

| <span data-ttu-id="7f0d2-114">功能</span><span class="sxs-lookup"><span data-stu-id="7f0d2-114">Feature</span></span>          | <span data-ttu-id="7f0d2-115">gRPC</span><span class="sxs-lookup"><span data-stu-id="7f0d2-115">gRPC</span></span>                                               | <span data-ttu-id="7f0d2-116">具有 JSON 的 HTTP API</span><span class="sxs-lookup"><span data-stu-id="7f0d2-116">HTTP APIs with JSON</span></span>           |
| ---
<span data-ttu-id="7f0d2-117">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-117">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-118">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-118">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-119">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-119">'Identity'</span></span>
- <span data-ttu-id="7f0d2-120">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-120">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-121">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-121">'Razor'</span></span>
- <span data-ttu-id="7f0d2-122">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-122">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-123">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-123">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-124">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-124">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-125">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-125">'Identity'</span></span>
- <span data-ttu-id="7f0d2-126">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-126">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-127">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-127">'Razor'</span></span>
- <span data-ttu-id="7f0d2-128">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-128">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-129">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-129">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-130">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-130">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-131">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-131">'Identity'</span></span>
- <span data-ttu-id="7f0d2-132">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-132">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-133">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-133">'Razor'</span></span>
- <span data-ttu-id="7f0d2-134">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-134">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-135">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-135">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-136">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-136">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-137">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-137">'Identity'</span></span>
- <span data-ttu-id="7f0d2-138">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-138">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-139">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-139">'Razor'</span></span>
- <span data-ttu-id="7f0d2-140">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-140">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-141">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-141">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-142">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-142">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-143">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-143">'Identity'</span></span>
- <span data-ttu-id="7f0d2-144">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-144">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-145">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-145">'Razor'</span></span>
- <span data-ttu-id="7f0d2-146">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-146">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-147">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-147">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-148">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-149">'Identity'</span></span>
- <span data-ttu-id="7f0d2-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-151">'Razor'</span></span>
- <span data-ttu-id="7f0d2-152">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-152">'SignalR' uid:</span></span> 

<span data-ttu-id="7f0d2-153">-------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-153">-------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-154">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-155">'Identity'</span></span>
- <span data-ttu-id="7f0d2-156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-157">'Razor'</span></span>
- <span data-ttu-id="7f0d2-158">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-159">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-159">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-160">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-161">'Identity'</span></span>
- <span data-ttu-id="7f0d2-162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-162">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-163">'Razor'</span></span>
- <span data-ttu-id="7f0d2-164">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-165">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-165">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-166">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-167">'Identity'</span></span>
- <span data-ttu-id="7f0d2-168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-168">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-169">'Razor'</span></span>
- <span data-ttu-id="7f0d2-170">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-171">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-171">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-172">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-173">'Identity'</span></span>
- <span data-ttu-id="7f0d2-174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-174">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-175">'Razor'</span></span>
- <span data-ttu-id="7f0d2-176">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-177">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-177">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-178">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-179">'Identity'</span></span>
- <span data-ttu-id="7f0d2-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-181">'Razor'</span></span>
- <span data-ttu-id="7f0d2-182">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-183">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-183">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-184">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-185">'Identity'</span></span>
- <span data-ttu-id="7f0d2-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-187">'Razor'</span></span>
- <span data-ttu-id="7f0d2-188">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-189">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-189">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-190">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-191">'Identity'</span></span>
- <span data-ttu-id="7f0d2-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-193">'Razor'</span></span>
- <span data-ttu-id="7f0d2-194">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-195">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-195">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-196">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-197">'Identity'</span></span>
- <span data-ttu-id="7f0d2-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-199">'Razor'</span></span>
- <span data-ttu-id="7f0d2-200">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-201">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-201">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-202">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-203">'Identity'</span></span>
- <span data-ttu-id="7f0d2-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-205">'Razor'</span></span>
- <span data-ttu-id="7f0d2-206">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-207">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-207">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-208">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-209">'Identity'</span></span>
- <span data-ttu-id="7f0d2-210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-210">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-211">'Razor'</span></span>
- <span data-ttu-id="7f0d2-212">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-213">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-213">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-214">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-215">'Identity'</span></span>
- <span data-ttu-id="7f0d2-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-217">'Razor'</span></span>
- <span data-ttu-id="7f0d2-218">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-219">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-219">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-220">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-221">'Identity'</span></span>
- <span data-ttu-id="7f0d2-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-223">'Razor'</span></span>
- <span data-ttu-id="7f0d2-224">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-225">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-225">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-226">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-227">'Identity'</span></span>
- <span data-ttu-id="7f0d2-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-229">'Razor'</span></span>
- <span data-ttu-id="7f0d2-230">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-231">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-231">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-232">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-233">'Identity'</span></span>
- <span data-ttu-id="7f0d2-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-235">'Razor'</span></span>
- <span data-ttu-id="7f0d2-236">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-237">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-237">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-238">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-239">'Identity'</span></span>
- <span data-ttu-id="7f0d2-240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-241">'Razor'</span></span>
- <span data-ttu-id="7f0d2-242">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-243">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-243">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-244">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-245">'Identity'</span></span>
- <span data-ttu-id="7f0d2-246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-247">'Razor'</span></span>
- <span data-ttu-id="7f0d2-248">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-249">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-249">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-250">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-251">'Identity'</span></span>
- <span data-ttu-id="7f0d2-252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-252">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-253">'Razor'</span></span>
- <span data-ttu-id="7f0d2-254">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-255">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-255">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-256">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-257">'Identity'</span></span>
- <span data-ttu-id="7f0d2-258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-258">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-259">'Razor'</span></span>
- <span data-ttu-id="7f0d2-260">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-261">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-261">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-262">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-263">'Identity'</span></span>
- <span data-ttu-id="7f0d2-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-265">'Razor'</span></span>
- <span data-ttu-id="7f0d2-266">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-267">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-267">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-268">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-269">'Identity'</span></span>
- <span data-ttu-id="7f0d2-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-271">'Razor'</span></span>
- <span data-ttu-id="7f0d2-272">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-273">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-273">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-274">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-275">'Identity'</span></span>
- <span data-ttu-id="7f0d2-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-277">'Razor'</span></span>
- <span data-ttu-id="7f0d2-278">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-279">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-279">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-280">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-281">'Identity'</span></span>
- <span data-ttu-id="7f0d2-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-283">'Razor'</span></span>
- <span data-ttu-id="7f0d2-284">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-285">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-285">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-286">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-287">'Identity'</span></span>
- <span data-ttu-id="7f0d2-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-289">'Razor'</span></span>
- <span data-ttu-id="7f0d2-290">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-290">'SignalR' uid:</span></span> 

<span data-ttu-id="7f0d2-291">------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-291">------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-292">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-293">'Identity'</span></span>
- <span data-ttu-id="7f0d2-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-295">'Razor'</span></span>
- <span data-ttu-id="7f0d2-296">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-297">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-297">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-298">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-299">'Identity'</span></span>
- <span data-ttu-id="7f0d2-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-301">'Razor'</span></span>
- <span data-ttu-id="7f0d2-302">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-303">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-303">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-304">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-305">'Identity'</span></span>
- <span data-ttu-id="7f0d2-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-307">'Razor'</span></span>
- <span data-ttu-id="7f0d2-308">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-309">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-309">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-310">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-311">'Identity'</span></span>
- <span data-ttu-id="7f0d2-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-313">'Razor'</span></span>
- <span data-ttu-id="7f0d2-314">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-315">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-315">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-316">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-317">'Identity'</span></span>
- <span data-ttu-id="7f0d2-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-319">'Razor'</span></span>
- <span data-ttu-id="7f0d2-320">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-321">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-321">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-322">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-323">'Identity'</span></span>
- <span data-ttu-id="7f0d2-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-325">'Razor'</span></span>
- <span data-ttu-id="7f0d2-326">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-327">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-327">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-328">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-329">'Identity'</span></span>
- <span data-ttu-id="7f0d2-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-331">'Razor'</span></span>
- <span data-ttu-id="7f0d2-332">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-333">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-333">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-334">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-335">'Identity'</span></span>
- <span data-ttu-id="7f0d2-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-337">'Razor'</span></span>
- <span data-ttu-id="7f0d2-338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-339">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-339">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-340">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-341">'Identity'</span></span>
- <span data-ttu-id="7f0d2-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-343">'Razor'</span></span>
- <span data-ttu-id="7f0d2-344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-345">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-345">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-346">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-347">'Identity'</span></span>
- <span data-ttu-id="7f0d2-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-349">'Razor'</span></span>
- <span data-ttu-id="7f0d2-350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-351">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-351">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-352">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-353">'Identity'</span></span>
- <span data-ttu-id="7f0d2-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-355">'Razor'</span></span>
- <span data-ttu-id="7f0d2-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7f0d2-357">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-357">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f0d2-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-358">'Blazor'</span></span>
- <span data-ttu-id="7f0d2-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-359">'Identity'</span></span>
- <span data-ttu-id="7f0d2-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f0d2-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f0d2-361">'Razor'</span></span>
- <span data-ttu-id="7f0d2-362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7f0d2-362">'SignalR' uid:</span></span> 

<span data-ttu-id="7f0d2-363">--------------- | | 协定         | 必需 ( *.proto*)                                | 可选 (OpenAPI)            | | 协议         | HTTP/2                                             | HTTP                          | | 有效负载          | [Protobuf（小型、二进制）](#performance)           | JSON（大型、人工可读取）  | | 规定性 | [严格规范](#strict-specification)      | 宽松。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-363">--------------- | | Contract         | Required (*.proto*)                                | Optional (OpenAPI)            | | Protocol         | HTTP/2                                             | HTTP                          | | Payload          | [Protobuf (small, binary)](#performance)           | JSON (large, human readable)  | | Prescriptiveness | [Strict specification](#strict-specification)      | Loose.</span></span> <span data-ttu-id="7f0d2-364">任何 HTTP 均有效。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-364">Any HTTP is valid.</span></span>     <span data-ttu-id="7f0d2-365">| | 流式处理        | [客户端、服务器、双向](#streaming)       | 客户端、服务器                | | 浏览器支持  | [无（需要 grpc-web）](#limited-browser-support) | 是                           | | 安全         | 传输 (TLS)                                    | 传输 (TLS)               | | 客户端代码生成 | [是](#code-generation)                      | OpenAPI + 第三方工具 |</span><span class="sxs-lookup"><span data-stu-id="7f0d2-365">| | Streaming        | [Client, server, bi-directional](#streaming)       | Client, server                | | Browser support  | [No (requires grpc-web)](#limited-browser-support) | Yes                           | | Security         | Transport (TLS)                                    | Transport (TLS)               | | Client code-generation | [Yes](#code-generation)                      | OpenAPI + third-party tooling |</span></span>

## <a name="grpc-strengths"></a><span data-ttu-id="7f0d2-366">gRPC 优点</span><span class="sxs-lookup"><span data-stu-id="7f0d2-366">gRPC strengths</span></span>

### <a name="performance"></a><span data-ttu-id="7f0d2-367">性能</span><span class="sxs-lookup"><span data-stu-id="7f0d2-367">Performance</span></span>

<span data-ttu-id="7f0d2-368">gRPC 消息使用 [Protobuf](https://developers.google.com/protocol-buffers/docs/overview)（一种高效的二进制消息格式）进行序列化。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-368">gRPC messages are serialized using [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), an efficient binary message format.</span></span> <span data-ttu-id="7f0d2-369">Protobuf 在服务器和客户端上可以非常快速地序列化。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-369">Protobuf serializes very quickly on the server and client.</span></span> <span data-ttu-id="7f0d2-370">Protobuf 序列化产生的有效负载较小，这在移动应用等带宽有限的方案中很重要。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-370">Protobuf serialization results in small message payloads, important in limited bandwidth scenarios like mobile apps.</span></span>

<span data-ttu-id="7f0d2-371">gRPC 专为 HTTP/2（HTTP 的主要版本）而设计，与 HTTP 1.x 相比，HTTP/2 具有巨大性能优势：</span><span class="sxs-lookup"><span data-stu-id="7f0d2-371">gRPC is designed for HTTP/2, a major revision of HTTP that provides significant performance benefits over HTTP 1.x:</span></span>

* <span data-ttu-id="7f0d2-372">二进制组帧和压缩。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-372">Binary framing and compression.</span></span> <span data-ttu-id="7f0d2-373">HTTP/2 协议在发送和接收方面均紧凑且高效。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-373">HTTP/2 protocol is compact and efficient both in sending and receiving.</span></span>
* <span data-ttu-id="7f0d2-374">在单个 TCP 连接上多路复用多个 HTTP/2 调用。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-374">Multiplexing of multiple HTTP/2 calls over a single TCP connection.</span></span> <span data-ttu-id="7f0d2-375">多路复用可消除[队头阻塞](https://en.wikipedia.org/wiki/Head-of-line_blocking)。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-375">Multiplexing eliminates [head-of-line blocking](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span></span>

<span data-ttu-id="7f0d2-376">HTTP/2 不是 gRPC 独占的。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-376">HTTP/2 is not exclusive to gRPC.</span></span> <span data-ttu-id="7f0d2-377">许多请求类型（包括使用 JSON 的 HTTP API）都可以使用 HTTP/2，并受益于其性能改进。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-377">Many request types, including HTTP APIs with JSON, can use HTTP/2 and benefit from its performance improvements.</span></span>

### <a name="code-generation"></a><span data-ttu-id="7f0d2-378">代码生成</span><span class="sxs-lookup"><span data-stu-id="7f0d2-378">Code generation</span></span>

<span data-ttu-id="7f0d2-379">所有 gRPC 框架都为代码生成提供一流支持。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-379">All gRPC frameworks provide first-class support for code generation.</span></span> <span data-ttu-id="7f0d2-380">[.proto 文件](https://developers.google.com/protocol-buffers/docs/proto3)是 gRPC 开发的核心文件，它定义 gRPC 服务和消息的协定。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-380">A core file to gRPC development is the [.proto file](https://developers.google.com/protocol-buffers/docs/proto3), which defines the contract of gRPC services and messages.</span></span> <span data-ttu-id="7f0d2-381">通过此文件，gRPC 框架编码生成服务基类、消息和完整的客户端。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-381">From this file gRPC frameworks will code generate a service base class, messages, and a complete client.</span></span>

<span data-ttu-id="7f0d2-382">通过在服务器和客户端之间共享 *.proto* 文件，可以端到端生成消息和客户端代码。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-382">By sharing the *.proto* file between the server and client, messages and client code can be generated from end to end.</span></span> <span data-ttu-id="7f0d2-383">客户端的代码生成消除了客户端和服务器上的消息重复，并为你创建强类型客户端。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-383">Code generation of the client eliminates duplication of messages on the client and server, and creates a strongly-typed client for you.</span></span> <span data-ttu-id="7f0d2-384">无需编写客户端可在具有许多服务的应用程序中节省大量开发时间。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-384">Not having to write a client saves significant development time in applications with many services.</span></span>

### <a name="strict-specification"></a><span data-ttu-id="7f0d2-385">严格规范</span><span class="sxs-lookup"><span data-stu-id="7f0d2-385">Strict specification</span></span>

<span data-ttu-id="7f0d2-386">具有 JSON 的 HTTP API 没有正式规范。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-386">A formal specification for HTTP API with JSON doesn't exist.</span></span> <span data-ttu-id="7f0d2-387">开发人员为 URL、HTTP 谓词和响应代码的最佳格式争论不休。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-387">Developers debate the best format of URLs, HTTP verbs, and response codes.</span></span>

<span data-ttu-id="7f0d2-388">[gRPC 规范](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md)对 gRPC 服务必须遵循的格式进行了规定。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-388">The [gRPC specification](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) is prescriptive about the format a gRPC service must follow.</span></span> <span data-ttu-id="7f0d2-389">gRPC 消除了争论并为开发人员节省了时间，因为 gRPC 在各个平台和实现中都是一致的。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-389">gRPC eliminates debate and saves developer time because gRPC is consistent across platforms and implementations.</span></span>

### <a name="streaming"></a><span data-ttu-id="7f0d2-390">流式处理</span><span class="sxs-lookup"><span data-stu-id="7f0d2-390">Streaming</span></span>

<span data-ttu-id="7f0d2-391">HTTP/2 为长期实时通信流提供基础。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-391">HTTP/2 provides a foundation for long-lived, real-time communication streams.</span></span> <span data-ttu-id="7f0d2-392">gRPC 为通过 HTTP/2 进行流式传输提供一流支持。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-392">gRPC provides first-class support for streaming through HTTP/2.</span></span>

<span data-ttu-id="7f0d2-393">gRPC 服务支持所有流式传输组合：</span><span class="sxs-lookup"><span data-stu-id="7f0d2-393">A gRPC service supports all streaming combinations:</span></span>

* <span data-ttu-id="7f0d2-394">一元（无流式传输）</span><span class="sxs-lookup"><span data-stu-id="7f0d2-394">Unary (no streaming)</span></span>
* <span data-ttu-id="7f0d2-395">服务器到客户端流式传输</span><span class="sxs-lookup"><span data-stu-id="7f0d2-395">Server to client streaming</span></span>
* <span data-ttu-id="7f0d2-396">客户端到服务器流式传输</span><span class="sxs-lookup"><span data-stu-id="7f0d2-396">Client to server streaming</span></span>
* <span data-ttu-id="7f0d2-397">双向流式传输</span><span class="sxs-lookup"><span data-stu-id="7f0d2-397">Bi-directional streaming</span></span>

### <a name="deadlinetimeouts-and-cancellation"></a><span data-ttu-id="7f0d2-398">截止时间/超时和取消</span><span class="sxs-lookup"><span data-stu-id="7f0d2-398">Deadline/timeouts and cancellation</span></span>

<span data-ttu-id="7f0d2-399">gRPC 允许客户端指定其愿意等待 RPC 完成的时间期限。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-399">gRPC allows clients to specify how long they are willing to wait for an RPC to complete.</span></span> <span data-ttu-id="7f0d2-400">[截止时间](https://grpc.io/blog/deadlines)会发送到服务器，如果超过截止时间，服务器可以决定要执行的操作。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-400">The [deadline](https://grpc.io/blog/deadlines) is sent to the server, and the server can decide what action to take if it exceeds the deadline.</span></span> <span data-ttu-id="7f0d2-401">例如，服务器可能会在超时后取消正在进行的 gRPC/HTTP/数据库请求。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-401">For example, the server might cancel in-progress gRPC/HTTP/database requests on timeout.</span></span>

<span data-ttu-id="7f0d2-402">通过 gRPC 子调用传播截止时间和取消有助于强制执行资源使用限制。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-402">Propagating the deadline and cancellation through child gRPC calls helps enforce resource usage limits.</span></span>

## <a name="grpc-recommended-scenarios"></a><span data-ttu-id="7f0d2-403">gRPC 建议方案</span><span class="sxs-lookup"><span data-stu-id="7f0d2-403">gRPC recommended scenarios</span></span>

<span data-ttu-id="7f0d2-404">gRPC 非常适合以下方案：</span><span class="sxs-lookup"><span data-stu-id="7f0d2-404">gRPC is well suited to the following scenarios:</span></span>

* <span data-ttu-id="7f0d2-405">微服务：gRPC 设计用于低延迟和高吞吐量通信。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-405">**Microservices**: gRPC is designed for low latency and high throughput communication.</span></span> <span data-ttu-id="7f0d2-406">gRPC 对于效率至关重要的轻量级微服务非常有用。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-406">gRPC is great for lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="7f0d2-407">点对点实时通信：gRPC 对双向流式传输提供出色的支持。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-407">**Point-to-point real-time communication**: gRPC has excellent support for bi-directional streaming.</span></span> <span data-ttu-id="7f0d2-408">gRPC 服务可以实时推送消息而无需轮询。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-408">gRPC services can push messages in real-time without polling.</span></span>
* <span data-ttu-id="7f0d2-409">多语言环境：gRPC 工具支持所有常用的开发语言，因此，gRPC 是多语言环境的理想选择。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-409">**Polyglot environments**: gRPC tooling supports all popular development languages, making gRPC a good choice for multi-language environments.</span></span>
* <span data-ttu-id="7f0d2-410">网络受限环境：gRPC 消息使用 Protobuf（一种轻量级消息格式）进行序列化。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-410">**Network constrained environments**: gRPC messages are serialized with Protobuf, a lightweight message format.</span></span> <span data-ttu-id="7f0d2-411">gRPC 消息始终小于等效的 JSON 消息。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-411">A gRPC message is always smaller than an equivalent JSON message.</span></span>

## <a name="grpc-weaknesses"></a><span data-ttu-id="7f0d2-412">gRPC 弱点</span><span class="sxs-lookup"><span data-stu-id="7f0d2-412">gRPC weaknesses</span></span>

### <a name="limited-browser-support"></a><span data-ttu-id="7f0d2-413">浏览器支持受限</span><span class="sxs-lookup"><span data-stu-id="7f0d2-413">Limited browser support</span></span>

<span data-ttu-id="7f0d2-414">当前无法通过浏览器直接调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-414">It's impossible to directly call a gRPC service from a browser today.</span></span> <span data-ttu-id="7f0d2-415">gRPC 大量使用 HTTP/2 功能，且没有浏览器在 Web 请求中提供支持 gRPC 客户端所需的控制级别。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-415">gRPC heavily uses HTTP/2 features and no browser provides the level of control required over web requests to support a gRPC client.</span></span> <span data-ttu-id="7f0d2-416">例如，浏览器不允许调用方要求使用 HTTP/2，也不提供对 HTTP/2 基础框架的访问。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-416">For example, browsers do not allow a caller to require that HTTP/2 be used, or provide access to underlying HTTP/2 frames.</span></span>

<span data-ttu-id="7f0d2-417">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) 是 gRPC 团队的另一项技术，可在浏览器中提供有限的 gRPC 支持。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-417">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) is an additional technology from the gRPC team that provides limited gRPC support in the browser.</span></span> <span data-ttu-id="7f0d2-418">gRPC-Web 由两部分组成：支持所有现代浏览器的 JavaScript 客户端，以及服务器上的 gRPC-Web 代理。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-418">gRPC-Web consists of two parts: a JavaScript client that supports all modern browsers, and a gRPC-Web proxy on the server.</span></span> <span data-ttu-id="7f0d2-419">gRPC-Web 客户端调用代理，代理将根据 gRPC 请求转发到 gRPC 服务器。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-419">The gRPC-Web client calls the proxy and the proxy will forward on the gRPC requests to the gRPC server.</span></span>

<span data-ttu-id="7f0d2-420">gRPC-Web 并不支持所有 gRPC 功能。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-420">Not all of gRPC's features are supported by gRPC-Web.</span></span> <span data-ttu-id="7f0d2-421">不支持客户端和双向流式传输，并且对服务器流式传输的支持有限。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-421">Client and bi-directional streaming isn't supported, and there is limited support for server streaming.</span></span>

> [!TIP]
> <span data-ttu-id="7f0d2-422">.NET Core 对 gRPC-Web 提供实验性支持。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-422">.NET Core has experimental support for gRPC-Web.</span></span> <span data-ttu-id="7f0d2-423">有关详细信息，请访问 <xref:grpc/browser>。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-423">Visit <xref:grpc/browser> for more information.</span></span>

### <a name="not-human-readable"></a><span data-ttu-id="7f0d2-424">非人工可读取</span><span class="sxs-lookup"><span data-stu-id="7f0d2-424">Not human readable</span></span>

<span data-ttu-id="7f0d2-425">HTTP API 请求以文本形式发送，并且可进行人工读取和创建。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-425">HTTP API requests are sent as text and can be read and created by humans.</span></span>

<span data-ttu-id="7f0d2-426">默认情况下，gRPC 消息使用 Protobuf 进行编码。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-426">gRPC messages are encoded with Protobuf by default.</span></span> <span data-ttu-id="7f0d2-427">尽管 Protobuf 可以高效地发送和接收，但其二进制格式非人工可读取。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-427">While Protobuf is efficient to send and receive, its binary format isn't human readable.</span></span> <span data-ttu-id="7f0d2-428">Protobuf 要求在 *.proto* 文件中指定消息接口描述来正确地反序列化。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-428">Protobuf requires the message's interface description specified in the *.proto* file to properly deserialize.</span></span> <span data-ttu-id="7f0d2-429">需要使用其他工具来分析网络上的 Protobuf 有效负载以及手动撰写请求。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-429">Additional tooling is required to analyze Protobuf payloads on the wire and to compose requests by hand.</span></span>

<span data-ttu-id="7f0d2-430">[服务器反射](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)和 [gRPC 命令行工具](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md)等功能可帮助使用二进制 Protobuf 消息。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-430">Features such as [server reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) and the [gRPC command line tool](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) exist to assist with binary Protobuf messages.</span></span> <span data-ttu-id="7f0d2-431">此外，Protobuf 消息支持[与 JSON 之间的转换](https://developers.google.com/protocol-buffers/docs/proto3#json)。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-431">Also, Protobuf messages support [conversion to and from JSON](https://developers.google.com/protocol-buffers/docs/proto3#json).</span></span> <span data-ttu-id="7f0d2-432">内置的 JSON 转换提供在调试时将 Protobuf 消息与人工可读取格式互相转换的高效方法。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-432">The built-in JSON conversion provides an efficient way to convert Protobuf messages to and from human readable form when debugging.</span></span>

## <a name="alternative-framework-scenarios"></a><span data-ttu-id="7f0d2-433">备用框架方案</span><span class="sxs-lookup"><span data-stu-id="7f0d2-433">Alternative framework scenarios</span></span>

<span data-ttu-id="7f0d2-434">在以下方案中，建议使用其他框架取代 gRPC：</span><span class="sxs-lookup"><span data-stu-id="7f0d2-434">Other frameworks are recommended over gRPC in the following scenarios:</span></span>

* <span data-ttu-id="7f0d2-435">浏览器可访问的 API：gRPC 在浏览器中未受到完全支持。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-435">**Browser accessible APIs**: gRPC isn't fully supported in the browser.</span></span> <span data-ttu-id="7f0d2-436">gRPC-Web 可以提供浏览器支持，但它具有局限性并引入了服务器代理。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-436">gRPC-Web can offer browser support, but it has limitations and introduces a server proxy.</span></span>
* <span data-ttu-id="7f0d2-437">广播实时通信：gRPC 支持通过流式传输进行实时通信，但不存在将消息广播到注册连接的概念。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-437">**Broadcast real-time communication**: gRPC supports real-time communication via streaming, but the concept of broadcasting a message out to registered connections doesn't exist.</span></span> <span data-ttu-id="7f0d2-438">例如，在聊天室方案中，应将新的聊天消息发送到聊天室中的所有客户端，这要求每个 gRPC 调用将新的聊天消息单独流式传输到客户端。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-438">For example in a chat room scenario where new chat messages should be sent to all clients in the chat room, each gRPC call is required to individually stream new chat messages to the client.</span></span> <span data-ttu-id="7f0d2-439">[SignalR](xref:signalr/introduction) 是适用于此方案的框架。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-439">[SignalR](xref:signalr/introduction) is a useful framework for this scenario.</span></span> SignalR<span data-ttu-id="7f0d2-440"> 具有持久性连接的概念，并内置对广播消息的支持。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-440"> has the concept of persistent connections and built-in support for broadcasting messages.</span></span>
* <span data-ttu-id="7f0d2-441">进程间通信：进程必须托管 HTTP/2 服务器才能接受传入的 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-441">**Inter-process communication**: A process must host an HTTP/2 server to accept incoming gRPC calls.</span></span> <span data-ttu-id="7f0d2-442">对于 Windows，进程间通信[管道](/dotnet/standard/io/pipe-operations)是一种快速、轻便的通信方法。</span><span class="sxs-lookup"><span data-stu-id="7f0d2-442">For Windows, inter-process communication [pipes](/dotnet/standard/io/pipe-operations) is a fast, lightweight method of communication.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7f0d2-443">其他资源</span><span class="sxs-lookup"><span data-stu-id="7f0d2-443">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
