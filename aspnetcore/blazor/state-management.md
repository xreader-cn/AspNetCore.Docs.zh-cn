---
<span data-ttu-id="57405-101">title:“ASP.NET Core Blazor 状态管理”author: description:“了解如何在 Blazor 服务器应用中保留状态。”</span><span class="sxs-lookup"><span data-stu-id="57405-101">title: 'ASP.NET Core Blazor state management' author: description: 'Learn how to persist state in Blazor Server apps.'</span></span>
<span data-ttu-id="57405-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57405-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57405-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57405-103">'Blazor'</span></span>
- <span data-ttu-id="57405-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57405-104">'Identity'</span></span>
- <span data-ttu-id="57405-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57405-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="57405-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57405-106">'Razor'</span></span>
- <span data-ttu-id="57405-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="57405-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-state-management"></a><span data-ttu-id="57405-108">ASP.NET Core Blazor 状态管理</span><span class="sxs-lookup"><span data-stu-id="57405-108">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="57405-109">作者：[Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="57405-109">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

Blazor<span data-ttu-id="57405-110"> 服务器是有状态的应用框架。</span><span class="sxs-lookup"><span data-stu-id="57405-110"> Server is a stateful app framework.</span></span> <span data-ttu-id="57405-111">大多数情况下，应用保持与服务器的持续连接。</span><span class="sxs-lookup"><span data-stu-id="57405-111">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="57405-112">用户的状态保留在线路中的服务器内存中。</span><span class="sxs-lookup"><span data-stu-id="57405-112">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="57405-113">为用户线路保留的状态示例包括：</span><span class="sxs-lookup"><span data-stu-id="57405-113">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="57405-114">呈现的 UI：组件实例的层次结构及其最新的呈现输出。</span><span class="sxs-lookup"><span data-stu-id="57405-114">The rendered UI: The hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="57405-115">组件实例中的任何字段和属性的值。</span><span class="sxs-lookup"><span data-stu-id="57405-115">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="57405-116">在线路范围内的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 服务实例中保留的数据。</span><span class="sxs-lookup"><span data-stu-id="57405-116">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="57405-117">本文介绍 Blazor 服务器应用中的状态暂留。</span><span class="sxs-lookup"><span data-stu-id="57405-117">This article addresses state persistence in Blazor Server apps.</span></span> Blazor<span data-ttu-id="57405-118"> WebAssembly 应用可以利用[浏览器中的客户端状态暂留](#client-side-in-the-browser)，但需要自定义解决方案或第三方包（这些并不在本文的讨论范围之内）。</span><span class="sxs-lookup"><span data-stu-id="57405-118"> WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="blazor-circuits"></a>Blazor<span data-ttu-id="57405-119"> 线路</span><span class="sxs-lookup"><span data-stu-id="57405-119"> circuits</span></span>

<span data-ttu-id="57405-120">如果用户遇到暂时的网络连接丢失问题，Blazor 会尝试将用户重新连接到其原始线路，以便用户继续使用该应用。</span><span class="sxs-lookup"><span data-stu-id="57405-120">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="57405-121">但是，将用户重新连接到服务器内存中的原始电路并非总是能够实现的：</span><span class="sxs-lookup"><span data-stu-id="57405-121">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="57405-122">服务器不能永久保留断开连接的线路。</span><span class="sxs-lookup"><span data-stu-id="57405-122">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="57405-123">超时后或在服务器面临内存压力时，服务器必须释放断开连接的线路。</span><span class="sxs-lookup"><span data-stu-id="57405-123">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="57405-124">在多服务器、负载均衡的部署环境中，任何服务器处理请求在任何给定时间都可能变得不可用。</span><span class="sxs-lookup"><span data-stu-id="57405-124">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="57405-125">不再需要单个服务器处理整个请求量时，它可能会失败或被自动删除。</span><span class="sxs-lookup"><span data-stu-id="57405-125">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="57405-126">当用户尝试重新连接时，原始服务器可能不可用。</span><span class="sxs-lookup"><span data-stu-id="57405-126">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="57405-127">用户可能会关闭并重新打开其浏览器或重载页面，这会删除浏览器内存中保留的所有状态。</span><span class="sxs-lookup"><span data-stu-id="57405-127">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="57405-128">例如，通过 JavaScript 互操作调用设置的值将丢失。</span><span class="sxs-lookup"><span data-stu-id="57405-128">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="57405-129">当无法将用户重新连接到其原始线路时，用户将收到一个具有空状态的新线路。</span><span class="sxs-lookup"><span data-stu-id="57405-129">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="57405-130">这等效于关闭并重新打开桌面应用。</span><span class="sxs-lookup"><span data-stu-id="57405-130">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="57405-131">跨线路保留状态</span><span class="sxs-lookup"><span data-stu-id="57405-131">Preserve state across circuits</span></span>

<span data-ttu-id="57405-132">在某些应用场景下，需要跨线路保留状态。</span><span class="sxs-lookup"><span data-stu-id="57405-132">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="57405-133">如果出现以下情况，应用可以为用户保留重要数据：</span><span class="sxs-lookup"><span data-stu-id="57405-133">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="57405-134">Web 服务器不可用。</span><span class="sxs-lookup"><span data-stu-id="57405-134">The web server becomes unavailable.</span></span>
* <span data-ttu-id="57405-135">用户的浏览器被强制使用新的 Web 服务器启动新线路。</span><span class="sxs-lookup"><span data-stu-id="57405-135">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="57405-136">通常情况下，跨线路保持状态适用于用户主动创建数据，而不是简单地读取已存在的数据的应用场景。</span><span class="sxs-lookup"><span data-stu-id="57405-136">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="57405-137">若要在单个电路之外保留状态，请勿只是将数据存储在服务器的内存中。</span><span class="sxs-lookup"><span data-stu-id="57405-137">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="57405-138">应用必须将数据保留到其他存储位置。</span><span class="sxs-lookup"><span data-stu-id="57405-138">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="57405-139">状态暂留并非是自动进行的。</span><span class="sxs-lookup"><span data-stu-id="57405-139">State persistence isn't automatic.</span></span> <span data-ttu-id="57405-140">必须在开发应用时采取措施来实现有状态的数据暂留。</span><span class="sxs-lookup"><span data-stu-id="57405-140">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="57405-141">通常，只有用户投入了大量精力所创建的高价值状态才需要数据暂留。</span><span class="sxs-lookup"><span data-stu-id="57405-141">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="57405-142">在下面的示例中，保留状态可以节省时间或有助于商业活动：</span><span class="sxs-lookup"><span data-stu-id="57405-142">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="57405-143">多步骤 Web 窗体：如果多步骤流程的多个已完成步骤的状态丢失，用户重新输入这些步骤的数据会非常耗时。</span><span class="sxs-lookup"><span data-stu-id="57405-143">Multistep webform: It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="57405-144">如果用户离开多步骤窗体并在稍后返回，在这种应用场景下，用户将失去状态。</span><span class="sxs-lookup"><span data-stu-id="57405-144">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="57405-145">购物车：应用中任何代表潜在收入且具有重要商业价值的组件都可以保留。</span><span class="sxs-lookup"><span data-stu-id="57405-145">Shopping cart: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="57405-146">如果用户丢失了其状态，进而丢失了其购物车，则在他们稍后返回站点时可购买较少的产品或服务。</span><span class="sxs-lookup"><span data-stu-id="57405-146">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="57405-147">通常不需要保留易于重新创建的状态，例如在登录对话框中输入的尚未提交的用户名。</span><span class="sxs-lookup"><span data-stu-id="57405-147">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="57405-148">应用只能保留应用状态。</span><span class="sxs-lookup"><span data-stu-id="57405-148">An app can only persist *app state*.</span></span> <span data-ttu-id="57405-149">不能保留 UI，如组件实例及其呈现树。</span><span class="sxs-lookup"><span data-stu-id="57405-149">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="57405-150">组件和呈现树通常不能序列化。</span><span class="sxs-lookup"><span data-stu-id="57405-150">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="57405-151">若要保留类似于 UI 状态的内容（如 TreeView 的展开节点），应用必须具有自定义代码，以便将行为建模为可序列化应用状态。</span><span class="sxs-lookup"><span data-stu-id="57405-151">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="57405-152">保留状态的位置</span><span class="sxs-lookup"><span data-stu-id="57405-152">Where to persist state</span></span>

<span data-ttu-id="57405-153">有三个常见位置用于保留 Blazor 服务器应用中的状态。</span><span class="sxs-lookup"><span data-stu-id="57405-153">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="57405-154">每种方法分别适用于不同的应用场景，且有不同的注意事项：</span><span class="sxs-lookup"><span data-stu-id="57405-154">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="57405-155">数据库中的服务器端</span><span class="sxs-lookup"><span data-stu-id="57405-155">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="57405-156">URL</span><span class="sxs-lookup"><span data-stu-id="57405-156">URL</span></span>](#url)
* [<span data-ttu-id="57405-157">浏览器中的客户端</span><span class="sxs-lookup"><span data-stu-id="57405-157">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="57405-158">数据库中的服务器端</span><span class="sxs-lookup"><span data-stu-id="57405-158">Server-side in a database</span></span>

<span data-ttu-id="57405-159">对于永久数据暂留或必须跨多个用户或设备的任何数据，独立的服务器端数据库几乎肯定是最佳选择。</span><span class="sxs-lookup"><span data-stu-id="57405-159">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="57405-160">选项包括：</span><span class="sxs-lookup"><span data-stu-id="57405-160">Options include:</span></span>

* <span data-ttu-id="57405-161">关系 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="57405-161">Relational SQL database</span></span>
* <span data-ttu-id="57405-162">键值存储</span><span class="sxs-lookup"><span data-stu-id="57405-162">Key-value store</span></span>
* <span data-ttu-id="57405-163">Blob 存储</span><span class="sxs-lookup"><span data-stu-id="57405-163">Blob store</span></span>
* <span data-ttu-id="57405-164">表存储</span><span class="sxs-lookup"><span data-stu-id="57405-164">Table store</span></span>

<span data-ttu-id="57405-165">将数据保存到数据库后，用户可以随时启动新线路。</span><span class="sxs-lookup"><span data-stu-id="57405-165">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="57405-166">用户数据会保留且在任何新线路中可用。</span><span class="sxs-lookup"><span data-stu-id="57405-166">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="57405-167">有关 Azure 数据存储选项的详细信息，请参阅 [Azure 存储文档](/azure/storage/)和 [Azure 数据库](https://azure.microsoft.com/product-categories/databases/)。</span><span class="sxs-lookup"><span data-stu-id="57405-167">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="57405-168">URL</span><span class="sxs-lookup"><span data-stu-id="57405-168">URL</span></span>

<span data-ttu-id="57405-169">对于表示导航状态的暂时性数据，请将数据作为 URL 的一部分进行建模。</span><span class="sxs-lookup"><span data-stu-id="57405-169">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="57405-170">URL 中建模的状态示例包括：</span><span class="sxs-lookup"><span data-stu-id="57405-170">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="57405-171">已查看实体的 ID。</span><span class="sxs-lookup"><span data-stu-id="57405-171">The ID of a viewed entity.</span></span>
* <span data-ttu-id="57405-172">分页网格中的当前页码。</span><span class="sxs-lookup"><span data-stu-id="57405-172">The current page number in a paged grid.</span></span>

<span data-ttu-id="57405-173">保留浏览器地址栏的内容：</span><span class="sxs-lookup"><span data-stu-id="57405-173">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="57405-174">如果用户手动重载页面。</span><span class="sxs-lookup"><span data-stu-id="57405-174">If the user manually reloads the page.</span></span>
* <span data-ttu-id="57405-175">如果 Web 服务器不可用，且用户被强制重载页面，以便连接到其他服务器。</span><span class="sxs-lookup"><span data-stu-id="57405-175">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="57405-176">有关使用 `@page` 指令定义 URL 模式的信息，请参阅 <xref:blazor/routing>。</span><span class="sxs-lookup"><span data-stu-id="57405-176">For information on defining URL patterns with the `@page` directive, see <xref:blazor/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="57405-177">浏览器中的客户端</span><span class="sxs-lookup"><span data-stu-id="57405-177">Client-side in the browser</span></span>

<span data-ttu-id="57405-178">对于用户正在主动创建的暂时性数据，通用后备存储是浏览器的 `localStorage` 和 `sessionStorage` 集合。</span><span class="sxs-lookup"><span data-stu-id="57405-178">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="57405-179">如果放弃该线路，则应用无需管理或清除存储的状态。与服务器端存储相比，这是一项优势。</span><span class="sxs-lookup"><span data-stu-id="57405-179">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="57405-180">本节中的“客户端”是指浏览器中的客户端方案，而不是 [BlazorWebAssembly 托管模型](xref:blazor/hosting-models#blazor-webassembly)。</span><span class="sxs-lookup"><span data-stu-id="57405-180">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="57405-181">`localStorage` 和 `sessionStorage` 可用于 Blazor WebAssembly 应用，但只能通过编写自定义代码或使用第三方包进行使用。</span><span class="sxs-lookup"><span data-stu-id="57405-181">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="57405-182">`localStorage` 和 `sessionStorage` 的区别如下：</span><span class="sxs-lookup"><span data-stu-id="57405-182">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="57405-183">`localStorage` 的应用范围限定为用户的浏览器。</span><span class="sxs-lookup"><span data-stu-id="57405-183">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="57405-184">如果用户重载页面或关闭并重新打开浏览器，则状态保持不变。</span><span class="sxs-lookup"><span data-stu-id="57405-184">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="57405-185">如果用户打开多个浏览器选项卡，则状态跨选项卡共享。</span><span class="sxs-lookup"><span data-stu-id="57405-185">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="57405-186">数据保留在 `localStorage` 中，直到被显式清除为止。</span><span class="sxs-lookup"><span data-stu-id="57405-186">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="57405-187">`sessionStorage` 的应用范围限定为用户的浏览器选项卡。如果用户重载该选项卡，则状态保持不变。</span><span class="sxs-lookup"><span data-stu-id="57405-187">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="57405-188">如果用户关闭该选项卡或该浏览器，则状态丢失。</span><span class="sxs-lookup"><span data-stu-id="57405-188">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="57405-189">如果用户打开多个浏览器选项卡，则每个选项卡都有自己独立的数据版本。</span><span class="sxs-lookup"><span data-stu-id="57405-189">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="57405-190">通常，`sessionStorage` 使用起来更安全。</span><span class="sxs-lookup"><span data-stu-id="57405-190">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="57405-191">`sessionStorage` 避免了用户打开多个选项卡并遇到以下问题的风险：</span><span class="sxs-lookup"><span data-stu-id="57405-191">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="57405-192">跨选项卡的状态存储中出现 bug。</span><span class="sxs-lookup"><span data-stu-id="57405-192">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="57405-193">一个选项卡覆盖其他选项卡的状态时出现混乱行为。</span><span class="sxs-lookup"><span data-stu-id="57405-193">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="57405-194">如果应用必须在关闭和重新打开浏览器期间保持状态，则 `localStorage` 是更好的选择。</span><span class="sxs-lookup"><span data-stu-id="57405-194">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="57405-195">使用浏览器存储时的注意事项：</span><span class="sxs-lookup"><span data-stu-id="57405-195">Caveats for using browser storage:</span></span>

* <span data-ttu-id="57405-196">与使用服务器端数据库类似，加载和保存数据都是异步的。</span><span class="sxs-lookup"><span data-stu-id="57405-196">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="57405-197">与服务器端数据库不同，在预呈现期间，存储不可用，因为在预呈现阶段，请求的页面在浏览器中不存在。</span><span class="sxs-lookup"><span data-stu-id="57405-197">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="57405-198">保留状态的位置对于 Blazor 服务器应用，持久存储几千字节的数据是合理的。</span><span class="sxs-lookup"><span data-stu-id="57405-198">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="57405-199">超出几千字节后，你就须考虑性能影响，因为数据是跨网络加载和保存的。</span><span class="sxs-lookup"><span data-stu-id="57405-199">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="57405-200">用户可以查看或篡改数据。</span><span class="sxs-lookup"><span data-stu-id="57405-200">Users may view or tamper with the data.</span></span> <span data-ttu-id="57405-201">ASP.NET Core [数据保护](xref:security/data-protection/introduction)可以降低风险。</span><span class="sxs-lookup"><span data-stu-id="57405-201">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="57405-202">第三方浏览器存储解决方案</span><span class="sxs-lookup"><span data-stu-id="57405-202">Third-party browser storage solutions</span></span>

<span data-ttu-id="57405-203">第三方 NuGet 包提供使用 `localStorage` 和 `sessionStorage` 时采用的 API。</span><span class="sxs-lookup"><span data-stu-id="57405-203">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="57405-204">值得考虑的是，选择一个透明地使用 ASP.NET Core [数据保护](xref:security/data-protection/introduction)的包。</span><span class="sxs-lookup"><span data-stu-id="57405-204">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="57405-205">ASP.NET Core 数据保护可对存储的数据进行加密，并降低篡改存储数据的潜在风险。</span><span class="sxs-lookup"><span data-stu-id="57405-205">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="57405-206">如果 JSON 序列化的数据以纯文本形式存储，则用户可以使用浏览器开发人员工具查看数据，还可以修改存储的数据。</span><span class="sxs-lookup"><span data-stu-id="57405-206">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="57405-207">保护数据并非总是一个问题，因为有些数据本质上可能是无足轻重的。</span><span class="sxs-lookup"><span data-stu-id="57405-207">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="57405-208">例如，读取或修改 UI 元素的存储颜色不会对用户或组织造成严重的安全风险。</span><span class="sxs-lookup"><span data-stu-id="57405-208">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="57405-209">避免允许用户检查或篡改敏感数据。</span><span class="sxs-lookup"><span data-stu-id="57405-209">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="57405-210">受保护的浏览器存储实验性包</span><span class="sxs-lookup"><span data-stu-id="57405-210">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="57405-211">[Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) 就是一个为 `localStorage` 和 `sessionStorage` 提供[数据保护](xref:security/data-protection/introduction)的 NuGet 包示例。</span><span class="sxs-lookup"><span data-stu-id="57405-211">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="57405-212">`Microsoft.AspNetCore.ProtectedBrowserStorage` 是一个不受支持的实验性包，目前不适合用于生产。</span><span class="sxs-lookup"><span data-stu-id="57405-212">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="57405-213">安装</span><span class="sxs-lookup"><span data-stu-id="57405-213">Installation</span></span>

<span data-ttu-id="57405-214">安装 `Microsoft.AspNetCore.ProtectedBrowserStorage` 包：</span><span class="sxs-lookup"><span data-stu-id="57405-214">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="57405-215">在 Blazor 服务器应用项目中，将包引用添加到 [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)。</span><span class="sxs-lookup"><span data-stu-id="57405-215">In the Blazor Server app project, add a package reference to [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="57405-216">在顶级 HTML（例如，在默认项目模板中的“Pages/_Host.cshtml”文件中）中，添加以下 `<script>` 标记：</span><span class="sxs-lookup"><span data-stu-id="57405-216">In the top-level HTML (for example, in the *Pages/_Host.cshtml* file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="57405-217">在 `Startup.ConfigureServices` 方法中，调用 `AddProtectedBrowserStorage` 以将 `localStorage` 和 `sessionStorage` 服务添加到服务集合：</span><span class="sxs-lookup"><span data-stu-id="57405-217">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="57405-218">保存和加载组件中的数据</span><span class="sxs-lookup"><span data-stu-id="57405-218">Save and load data within a component</span></span>

<span data-ttu-id="57405-219">在需要将数据加载或保存到浏览器存储的任何组件中，使用 [`@inject`](xref:mvc/views/razor#inject) 注入以下任意一项的实例：</span><span class="sxs-lookup"><span data-stu-id="57405-219">In any component that requires loading or saving data to browser storage, use [`@inject`](xref:mvc/views/razor#inject) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="57405-220">此选择取决于你要使用的后备存储。</span><span class="sxs-lookup"><span data-stu-id="57405-220">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="57405-221">在以下示例中，使用 `sessionStorage`：</span><span class="sxs-lookup"><span data-stu-id="57405-221">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="57405-222">可将 `@using` 语句放置在“_Imports.razor”文件而不是组件中。</span><span class="sxs-lookup"><span data-stu-id="57405-222">The `@using` statement can be placed into an *_Imports.razor* file instead of in the component.</span></span> <span data-ttu-id="57405-223">使用“_Imports.razor”文件可使命名空间可用于应用的较大部分或整个应用。</span><span class="sxs-lookup"><span data-stu-id="57405-223">Use of the *_Imports.razor* file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="57405-224">若要在项目模板的 `Counter` 组件中保留 `currentCount` 值，请修改 `IncrementCount` 方法以使用 `ProtectedSessionStore.SetAsync`：</span><span class="sxs-lookup"><span data-stu-id="57405-224">To persist the `currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="57405-225">在更大、更真实的应用中，存储单个字段是不太可能出现的情况。</span><span class="sxs-lookup"><span data-stu-id="57405-225">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="57405-226">应用更有可能存储包含复杂状态的整个模型对象。</span><span class="sxs-lookup"><span data-stu-id="57405-226">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="57405-227">`ProtectedSessionStore` 自动串行化和反序列化 JSON 数据。</span><span class="sxs-lookup"><span data-stu-id="57405-227">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="57405-228">在前面的代码示例中，`currentCount` 数据存储为用户浏览器中的 `sessionStorage['count']`。</span><span class="sxs-lookup"><span data-stu-id="57405-228">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="57405-229">数据不会以纯文本形式存储，而是使用 ASP.NET Core 的[数据保护](xref:security/data-protection/introduction)进行保护。</span><span class="sxs-lookup"><span data-stu-id="57405-229">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="57405-230">如果在浏览器的开发人员控制台中评估了 `sessionStorage['count']`，则可以查看加密的数据。</span><span class="sxs-lookup"><span data-stu-id="57405-230">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="57405-231">若要在用户稍后返回到 `Counter` 组件时（包括他们位于全新线路上时）恢复 `currentCount` 数据，请使用 `ProtectedSessionStore.GetAsync`：</span><span class="sxs-lookup"><span data-stu-id="57405-231">To recover the `currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="57405-232">如果组件的参数包括导航状态，请调用 `ProtectedSessionStore.GetAsync` 并将结果分配给 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>，而不是 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>。</span><span class="sxs-lookup"><span data-stu-id="57405-232">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="57405-233"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 仅在首次实例化组件时调用一次。</span><span class="sxs-lookup"><span data-stu-id="57405-233"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="57405-234">如果用户导航到不同的 URL，而仍然停留在相同的页面上，则 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 之后不会再次调用。</span><span class="sxs-lookup"><span data-stu-id="57405-234"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="57405-235">有关详细信息，请参阅 <xref:blazor/lifecycle>。</span><span class="sxs-lookup"><span data-stu-id="57405-235">For more information, see <xref:blazor/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="57405-236">本节中的示例仅在服务器未启用预呈现的情况下有效。</span><span class="sxs-lookup"><span data-stu-id="57405-236">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="57405-237">启用预呈现后，将生成如下错误：</span><span class="sxs-lookup"><span data-stu-id="57405-237">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="57405-238">此时无法发出 JavaScript 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="57405-238">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="57405-239">这是因为该组件已预呈现。</span><span class="sxs-lookup"><span data-stu-id="57405-239">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="57405-240">禁用预呈现或添加其他代码以处理预呈现。</span><span class="sxs-lookup"><span data-stu-id="57405-240">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="57405-241">若要了解有关编写可处理预呈现的代码的详细信息，请参阅[处理预呈现](#handle-prerendering)一节。</span><span class="sxs-lookup"><span data-stu-id="57405-241">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="57405-242">处理加载状态</span><span class="sxs-lookup"><span data-stu-id="57405-242">Handle the loading state</span></span>

<span data-ttu-id="57405-243">由于浏览器存储是异步存储（通过网络连接进行访问），因此在数据已加载并可供组件使用之前始终需要一段时间。</span><span class="sxs-lookup"><span data-stu-id="57405-243">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="57405-244">为获得最佳结果，请在加载进行过程中呈现加载状态消息，而不要显示空数据或默认数据。</span><span class="sxs-lookup"><span data-stu-id="57405-244">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="57405-245">一种方法是跟踪数据是否为 `null`（仍在加载）。</span><span class="sxs-lookup"><span data-stu-id="57405-245">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="57405-246">在默认 `Counter` 组件中，计数保留在 `int` 中。</span><span class="sxs-lookup"><span data-stu-id="57405-246">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="57405-247">通过将问号 (`?`) 添加到类型 (`int`)，使 `currentCount` 可以为 null：</span><span class="sxs-lookup"><span data-stu-id="57405-247">Make `currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="57405-248">请勿无条件地显示计数和“增量”按钮，而选择仅在数据已加载后才显示这些元素：</span><span class="sxs-lookup"><span data-stu-id="57405-248">Instead of unconditionally displaying the count and **Increment** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="57405-249">处理预呈现</span><span class="sxs-lookup"><span data-stu-id="57405-249">Handle prerendering</span></span>

<span data-ttu-id="57405-250">预呈现期间：</span><span class="sxs-lookup"><span data-stu-id="57405-250">During prerendering:</span></span>

* <span data-ttu-id="57405-251">与用户浏览器的交互式连接不存在。</span><span class="sxs-lookup"><span data-stu-id="57405-251">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="57405-252">浏览器尚无可在其中运行 JavaScript 代码的页面。</span><span class="sxs-lookup"><span data-stu-id="57405-252">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="57405-253">在预呈现期间，`localStorage` 或 `sessionStorage` 不可用。</span><span class="sxs-lookup"><span data-stu-id="57405-253">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="57405-254">如果组件尝试与存储进行交互，将生成如下错误：</span><span class="sxs-lookup"><span data-stu-id="57405-254">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="57405-255">此时无法发出 JavaScript 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="57405-255">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="57405-256">这是因为该组件已预呈现。</span><span class="sxs-lookup"><span data-stu-id="57405-256">This is because the component is being prerendered.</span></span>

<span data-ttu-id="57405-257">解决此错误的一种方法是禁用预呈现。</span><span class="sxs-lookup"><span data-stu-id="57405-257">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="57405-258">如果应用大量使用基于浏览器的存储，则这通常是最佳选择。</span><span class="sxs-lookup"><span data-stu-id="57405-258">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="57405-259">预呈现会增加复杂性，且不会给应用带来好处，因为在 `localStorage` 或 `sessionStorage` 可用之前，应用无法预呈现任何有用的内容。</span><span class="sxs-lookup"><span data-stu-id="57405-259">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="57405-260">若要禁用预呈现，请打开“Pages/_Host.cshtml”文件，并将[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)的 `render-mode` 更改为 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>。</span><span class="sxs-lookup"><span data-stu-id="57405-260">To disable prerendering, open the *Pages/_Host.cshtml* file and change the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span></span>

<span data-ttu-id="57405-261">对于不使用 `localStorage` 或 `sessionStorage` 的其他页面，预呈现可能很有用。</span><span class="sxs-lookup"><span data-stu-id="57405-261">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="57405-262">若要使预呈现保持启用状态，可延迟加载操作，直到浏览器连接到线路。</span><span class="sxs-lookup"><span data-stu-id="57405-262">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="57405-263">以下是存储计数器值的示例：</span><span class="sxs-lookup"><span data-stu-id="57405-263">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="57405-264">将状态保留提取到常见位置</span><span class="sxs-lookup"><span data-stu-id="57405-264">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="57405-265">如果多个组件依赖于基于浏览器的存储，则多次重新实现状态提供程序代码会造成代码重复。</span><span class="sxs-lookup"><span data-stu-id="57405-265">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="57405-266">若要避免代码重复，一种方法是创建一个封装了状态提供程序逻辑的状态提供程序父组件。</span><span class="sxs-lookup"><span data-stu-id="57405-266">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="57405-267">子组件可处理保留的数据，而无需考虑状态暂留机制。</span><span class="sxs-lookup"><span data-stu-id="57405-267">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="57405-268">在 `CounterStateProvider` 组件的以下示例中，保留计数器数据：</span><span class="sxs-lookup"><span data-stu-id="57405-268">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="57405-269">`CounterStateProvider` 组件处理加载阶段的方式是在加载完成后才呈现其子内容。</span><span class="sxs-lookup"><span data-stu-id="57405-269">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="57405-270">若要使用 `CounterStateProvider` 组件，请围绕需要访问计数器状态的任何其他组件包装该组件的实例。</span><span class="sxs-lookup"><span data-stu-id="57405-270">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="57405-271">若要使某个应用中的所有组件都可以访问该状态，请围绕 `App` 组件 (App.razor) 中的 <xref:Microsoft.AspNetCore.Components.Routing.Router> 来包装 `CounterStateProvider` 组件：</span><span class="sxs-lookup"><span data-stu-id="57405-271">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (*App.razor*):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="57405-272">已包装的组件接收并可修改保留的计数器状态。</span><span class="sxs-lookup"><span data-stu-id="57405-272">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="57405-273">以下 `Counter` 组件实现了该模式：</span><span class="sxs-lookup"><span data-stu-id="57405-273">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="57405-274">与 `ProtectedBrowserStorage` 进行交互无需前面的组件，该组件也不会处理“正在加载”阶段。</span><span class="sxs-lookup"><span data-stu-id="57405-274">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="57405-275">如前所述，若要处理预呈现，可对 `CounterStateProvider` 进行修改，以便所有使用计数器数据的组件均可自动处理预呈现。</span><span class="sxs-lookup"><span data-stu-id="57405-275">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="57405-276">有关详细信息，请参阅[处理预呈现](#handle-prerendering)一节。</span><span class="sxs-lookup"><span data-stu-id="57405-276">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="57405-277">通常，建议在以下情况下使用状态提供程序父组件模式：</span><span class="sxs-lookup"><span data-stu-id="57405-277">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="57405-278">在多个其他组件中使用状态时。</span><span class="sxs-lookup"><span data-stu-id="57405-278">To consume state in many other components.</span></span>
* <span data-ttu-id="57405-279">只有一个顶级状态对象要保留时。</span><span class="sxs-lookup"><span data-stu-id="57405-279">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="57405-280">若要保留多个不同的状态对象并在不同位置使用不同的对象子集，最好避免全局地处理状态的加载和保存。</span><span class="sxs-lookup"><span data-stu-id="57405-280">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
