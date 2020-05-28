---
<span data-ttu-id="98879-101">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-102">'Blazor'</span></span>
- <span data-ttu-id="98879-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-103">'Identity'</span></span>
- <span data-ttu-id="98879-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-105">'Razor'</span></span>
- <span data-ttu-id="98879-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-106">'SignalR' uid:</span></span> 

---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="98879-107">在 ASP.NET Core 中保存外部提供程序的其他声明和令牌</span><span class="sxs-lookup"><span data-stu-id="98879-107">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="98879-108">ASP.NET Core 应用可以从外部身份验证提供程序（如 Facebook、Google、Microsoft 和 Twitter）建立其他声明和令牌。</span><span class="sxs-lookup"><span data-stu-id="98879-108">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="98879-109">每个提供程序都在其平台上显示有关用户的不同信息，但用于接收用户数据并将其转换为其他声明的模式是相同的。</span><span class="sxs-lookup"><span data-stu-id="98879-109">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="98879-110">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="98879-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="98879-111">先决条件</span><span class="sxs-lookup"><span data-stu-id="98879-111">Prerequisites</span></span>

<span data-ttu-id="98879-112">确定要在应用程序中支持的外部身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="98879-112">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="98879-113">对于每个提供程序，注册应用程序，并获取客户端 ID 和客户端密码。</span><span class="sxs-lookup"><span data-stu-id="98879-113">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="98879-114">有关详细信息，请参阅 <xref:security/authentication/social/index>。</span><span class="sxs-lookup"><span data-stu-id="98879-114">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="98879-115">示例应用使用[Google 身份验证提供程序](xref:security/authentication/google-logins)。</span><span class="sxs-lookup"><span data-stu-id="98879-115">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="98879-116">设置客户端 ID 和客户端密码</span><span class="sxs-lookup"><span data-stu-id="98879-116">Set the client ID and client secret</span></span>

<span data-ttu-id="98879-117">OAuth 身份验证提供程序使用客户端 ID 和客户端密码与应用程序建立了信任关系。</span><span class="sxs-lookup"><span data-stu-id="98879-117">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="98879-118">当向提供程序注册应用程序时，外部身份验证提供程序会为应用程序创建客户端 ID 和客户端机密值。</span><span class="sxs-lookup"><span data-stu-id="98879-118">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="98879-119">应用使用的每个外部提供程序必须与提供程序的客户端 ID 和客户端机密一起单独配置。</span><span class="sxs-lookup"><span data-stu-id="98879-119">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="98879-120">有关详细信息，请参阅适用于你的方案的外部身份验证提供程序主题：</span><span class="sxs-lookup"><span data-stu-id="98879-120">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="98879-121">Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="98879-121">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="98879-122">Google 身份验证</span><span class="sxs-lookup"><span data-stu-id="98879-122">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="98879-123">Microsoft 身份验证</span><span class="sxs-lookup"><span data-stu-id="98879-123">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="98879-124">Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="98879-124">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="98879-125">其他身份验证提供程序</span><span class="sxs-lookup"><span data-stu-id="98879-125">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="98879-126">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="98879-126">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="98879-127">示例应用使用 Google 提供的客户端 ID 和客户端机密配置 Google 身份验证提供程序：</span><span class="sxs-lookup"><span data-stu-id="98879-127">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="98879-128">建立身份验证范围</span><span class="sxs-lookup"><span data-stu-id="98879-128">Establish the authentication scope</span></span>

<span data-ttu-id="98879-129">指定要从提供程序检索的权限的列表 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> 。</span><span class="sxs-lookup"><span data-stu-id="98879-129">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="98879-130">常见外部提供程序的身份验证范围如下表中所示。</span><span class="sxs-lookup"><span data-stu-id="98879-130">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="98879-131">提供程序</span><span class="sxs-lookup"><span data-stu-id="98879-131">Provider</span></span>  | <span data-ttu-id="98879-132">范围</span><span class="sxs-lookup"><span data-stu-id="98879-132">Scope</span></span>                                                            |
| ---
<span data-ttu-id="98879-133">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-133">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-134">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-134">'Blazor'</span></span>
- <span data-ttu-id="98879-135">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-135">'Identity'</span></span>
- <span data-ttu-id="98879-136">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-137">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-137">'Razor'</span></span>
- <span data-ttu-id="98879-138">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-139">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-139">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-140">'Blazor'</span></span>
- <span data-ttu-id="98879-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-141">'Identity'</span></span>
- <span data-ttu-id="98879-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-143">'Razor'</span></span>
- <span data-ttu-id="98879-144">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-144">'SignalR' uid:</span></span> 

<span data-ttu-id="98879-145">----- |---标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-145">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-146">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-146">'Blazor'</span></span>
- <span data-ttu-id="98879-147">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-147">'Identity'</span></span>
- <span data-ttu-id="98879-148">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-148">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-149">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-149">'Razor'</span></span>
- <span data-ttu-id="98879-150">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-150">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-151">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-151">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-152">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-152">'Blazor'</span></span>
- <span data-ttu-id="98879-153">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-153">'Identity'</span></span>
- <span data-ttu-id="98879-154">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-154">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-155">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-155">'Razor'</span></span>
- <span data-ttu-id="98879-156">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-156">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-157">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-157">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-158">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-158">'Blazor'</span></span>
- <span data-ttu-id="98879-159">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-159">'Identity'</span></span>
- <span data-ttu-id="98879-160">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-160">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-161">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-161">'Razor'</span></span>
- <span data-ttu-id="98879-162">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-162">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-163">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-163">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-164">'Blazor'</span></span>
- <span data-ttu-id="98879-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-165">'Identity'</span></span>
- <span data-ttu-id="98879-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-167">'Razor'</span></span>
- <span data-ttu-id="98879-168">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-169">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-169">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-170">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-170">'Blazor'</span></span>
- <span data-ttu-id="98879-171">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-171">'Identity'</span></span>
- <span data-ttu-id="98879-172">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-172">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-173">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-173">'Razor'</span></span>
- <span data-ttu-id="98879-174">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-174">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-175">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-175">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-176">'Blazor'</span></span>
- <span data-ttu-id="98879-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-177">'Identity'</span></span>
- <span data-ttu-id="98879-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-179">'Razor'</span></span>
- <span data-ttu-id="98879-180">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-181">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-181">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-182">'Blazor'</span></span>
- <span data-ttu-id="98879-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-183">'Identity'</span></span>
- <span data-ttu-id="98879-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-185">'Razor'</span></span>
- <span data-ttu-id="98879-186">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-187">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-188">'Blazor'</span></span>
- <span data-ttu-id="98879-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-189">'Identity'</span></span>
- <span data-ttu-id="98879-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-191">'Razor'</span></span>
- <span data-ttu-id="98879-192">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-193">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-194">'Blazor'</span></span>
- <span data-ttu-id="98879-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-195">'Identity'</span></span>
- <span data-ttu-id="98879-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-197">'Razor'</span></span>
- <span data-ttu-id="98879-198">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-199">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-200">'Blazor'</span></span>
- <span data-ttu-id="98879-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-201">'Identity'</span></span>
- <span data-ttu-id="98879-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-203">'Razor'</span></span>
- <span data-ttu-id="98879-204">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-205">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-206">'Blazor'</span></span>
- <span data-ttu-id="98879-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-207">'Identity'</span></span>
- <span data-ttu-id="98879-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-209">'Razor'</span></span>
- <span data-ttu-id="98879-210">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-211">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-212">'Blazor'</span></span>
- <span data-ttu-id="98879-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-213">'Identity'</span></span>
- <span data-ttu-id="98879-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-215">'Razor'</span></span>
- <span data-ttu-id="98879-216">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-217">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-218">'Blazor'</span></span>
- <span data-ttu-id="98879-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-219">'Identity'</span></span>
- <span data-ttu-id="98879-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-221">'Razor'</span></span>
- <span data-ttu-id="98879-222">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-222">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-223">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-224">'Blazor'</span></span>
- <span data-ttu-id="98879-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-225">'Identity'</span></span>
- <span data-ttu-id="98879-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-227">'Razor'</span></span>
- <span data-ttu-id="98879-228">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-229">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-230">'Blazor'</span></span>
- <span data-ttu-id="98879-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-231">'Identity'</span></span>
- <span data-ttu-id="98879-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-233">'Razor'</span></span>
- <span data-ttu-id="98879-234">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-235">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-236">'Blazor'</span></span>
- <span data-ttu-id="98879-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-237">'Identity'</span></span>
- <span data-ttu-id="98879-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-239">'Razor'</span></span>
- <span data-ttu-id="98879-240">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-241">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-242">'Blazor'</span></span>
- <span data-ttu-id="98879-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-243">'Identity'</span></span>
- <span data-ttu-id="98879-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-245">'Razor'</span></span>
- <span data-ttu-id="98879-246">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-247">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-248">'Blazor'</span></span>
- <span data-ttu-id="98879-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-249">'Identity'</span></span>
- <span data-ttu-id="98879-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-251">'Razor'</span></span>
- <span data-ttu-id="98879-252">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-253">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-254">'Blazor'</span></span>
- <span data-ttu-id="98879-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-255">'Identity'</span></span>
- <span data-ttu-id="98879-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-257">'Razor'</span></span>
- <span data-ttu-id="98879-258">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-259">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-260">'Blazor'</span></span>
- <span data-ttu-id="98879-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-261">'Identity'</span></span>
- <span data-ttu-id="98879-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-263">'Razor'</span></span>
- <span data-ttu-id="98879-264">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-265">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-265">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-266">'Blazor'</span></span>
- <span data-ttu-id="98879-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-267">'Identity'</span></span>
- <span data-ttu-id="98879-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-269">'Razor'</span></span>
- <span data-ttu-id="98879-270">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-270">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-271">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-272">'Blazor'</span></span>
- <span data-ttu-id="98879-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-273">'Identity'</span></span>
- <span data-ttu-id="98879-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-275">'Razor'</span></span>
- <span data-ttu-id="98879-276">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-277">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-278">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-278">'Blazor'</span></span>
- <span data-ttu-id="98879-279">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-279">'Identity'</span></span>
- <span data-ttu-id="98879-280">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-280">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-281">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-281">'Razor'</span></span>
- <span data-ttu-id="98879-282">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-282">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-283">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-283">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-284">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-284">'Blazor'</span></span>
- <span data-ttu-id="98879-285">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-285">'Identity'</span></span>
- <span data-ttu-id="98879-286">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-286">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-287">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-287">'Razor'</span></span>
- <span data-ttu-id="98879-288">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-288">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-289">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-289">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-290">'Blazor'</span></span>
- <span data-ttu-id="98879-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-291">'Identity'</span></span>
- <span data-ttu-id="98879-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-293">'Razor'</span></span>
- <span data-ttu-id="98879-294">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-295">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-295">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-296">'Blazor'</span></span>
- <span data-ttu-id="98879-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-297">'Identity'</span></span>
- <span data-ttu-id="98879-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-299">'Razor'</span></span>
- <span data-ttu-id="98879-300">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-301">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-301">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-302">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-302">'Blazor'</span></span>
- <span data-ttu-id="98879-303">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-303">'Identity'</span></span>
- <span data-ttu-id="98879-304">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-304">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-305">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-305">'Razor'</span></span>
- <span data-ttu-id="98879-306">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-306">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-307">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-307">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-308">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-308">'Blazor'</span></span>
- <span data-ttu-id="98879-309">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-309">'Identity'</span></span>
- <span data-ttu-id="98879-310">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-310">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-311">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-311">'Razor'</span></span>
- <span data-ttu-id="98879-312">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-312">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-313">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-313">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-314">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-314">'Blazor'</span></span>
- <span data-ttu-id="98879-315">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-315">'Identity'</span></span>
- <span data-ttu-id="98879-316">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-316">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-317">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-317">'Razor'</span></span>
- <span data-ttu-id="98879-318">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-318">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-319">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-319">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-320">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-320">'Blazor'</span></span>
- <span data-ttu-id="98879-321">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-321">'Identity'</span></span>
- <span data-ttu-id="98879-322">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-322">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-323">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-323">'Razor'</span></span>
- <span data-ttu-id="98879-324">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-324">'SignalR' uid:</span></span> 

<span data-ttu-id="98879-325">-------------------------------- | |Facebook |`https://www.facebook.com/dialog/oauth`                          |
|Google |`https://www.googleapis.com/auth/userinfo.profile`               |
|Microsoft |`https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
|Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="98879-325">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="98879-326">在示例应用中， `userinfo.profile` 当在上调用时，由框架自动添加 Google 的作用域 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> 。</span><span class="sxs-lookup"><span data-stu-id="98879-326">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="98879-327">如果应用需要其他作用域，请将它们添加到选项。</span><span class="sxs-lookup"><span data-stu-id="98879-327">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="98879-328">在以下示例中，添加了 Google `https://www.googleapis.com/auth/user.birthday.read` 范围以便检索用户的生日：</span><span class="sxs-lookup"><span data-stu-id="98879-328">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="98879-329">映射用户数据密钥并创建声明</span><span class="sxs-lookup"><span data-stu-id="98879-329">Map user data keys and create claims</span></span>

<span data-ttu-id="98879-330">在提供程序的选项中，为 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> 外部提供程序的 JSON 用户数据中的每个键/子项指定或，以便在登录时读取应用程序标识。</span><span class="sxs-lookup"><span data-stu-id="98879-330">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="98879-331">有关声明类型的详细信息，请参阅 <xref:System.Security.Claims.ClaimTypes> 。</span><span class="sxs-lookup"><span data-stu-id="98879-331">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="98879-332">该示例应用 `urn:google:locale` `urn:google:picture` `locale` `picture` 在 Google 用户数据中的和密钥中创建区域设置（）和图片（）声明：</span><span class="sxs-lookup"><span data-stu-id="98879-332">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="98879-333">在中 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` ， <xref:Microsoft.AspNetCore.Identity.IdentityUser> （ `ApplicationUser` ）使用登录到应用 <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> 。</span><span class="sxs-lookup"><span data-stu-id="98879-333">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="98879-334">在登录过程中， <xref:Microsoft.AspNetCore.Identity.UserManager%601> 可以存储 `ApplicationUser` 提供的用户数据的声明 <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> 。</span><span class="sxs-lookup"><span data-stu-id="98879-334">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="98879-335">在示例应用程序中， `OnPostConfirmationAsync` （*Account/ExternalLogin*） `urn:google:locale` 为已登录的提供区域设置（）和图片（ `urn:google:picture` ）声明 `ApplicationUser` ，包括的声明 <xref:System.Security.Claims.ClaimTypes.GivenName> ：</span><span class="sxs-lookup"><span data-stu-id="98879-335">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="98879-336">默认情况下，用户的声明存储在身份验证 cookie 中。</span><span class="sxs-lookup"><span data-stu-id="98879-336">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="98879-337">如果身份验证 cookie 太大，则可能会导致应用程序失败，因为：</span><span class="sxs-lookup"><span data-stu-id="98879-337">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="98879-338">浏览器检测到 cookie 标头太长。</span><span class="sxs-lookup"><span data-stu-id="98879-338">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="98879-339">请求的整体大小太大。</span><span class="sxs-lookup"><span data-stu-id="98879-339">The overall size of the request is too large.</span></span>

<span data-ttu-id="98879-340">如果需要大量用户数据来处理用户请求：</span><span class="sxs-lookup"><span data-stu-id="98879-340">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="98879-341">将请求处理的用户声明的数量和大小限制为仅应用需要的内容。</span><span class="sxs-lookup"><span data-stu-id="98879-341">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="98879-342">使用 <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> Cookie 身份验证中间件的自定义 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> 来跨请求存储标识。</span><span class="sxs-lookup"><span data-stu-id="98879-342">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="98879-343">在服务器上保留大量标识信息，同时仅向客户端发送一个小型会话标识符密钥。</span><span class="sxs-lookup"><span data-stu-id="98879-343">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="98879-344">保存访问令牌</span><span class="sxs-lookup"><span data-stu-id="98879-344">Save the access token</span></span>

<span data-ttu-id="98879-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>定义在授权成功后是否应将访问和刷新令牌存储在中 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> 。</span><span class="sxs-lookup"><span data-stu-id="98879-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="98879-346">`SaveTokens`默认情况下，设置为以 `false` 减小最终身份验证 cookie 的大小。</span><span class="sxs-lookup"><span data-stu-id="98879-346">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="98879-347">示例应用将的值设置 `SaveTokens` 为 `true` 中的 <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> ：</span><span class="sxs-lookup"><span data-stu-id="98879-347">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="98879-348">`OnPostConfirmationAsync`执行时，从的外部提供程序存储访问令牌（[ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)） `ApplicationUser` `AuthenticationProperties` 。</span><span class="sxs-lookup"><span data-stu-id="98879-348">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="98879-349">该示例应用程序将访问令牌保存在 `OnPostConfirmationAsync` 帐户/ExternalLogin 中（新用户注册）和 `OnGetCallbackAsync` （ *Account/ExternalLogin.cshtml.cs*以前注册的用户）中：</span><span class="sxs-lookup"><span data-stu-id="98879-349">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="98879-350">如何添加其他自定义令牌</span><span class="sxs-lookup"><span data-stu-id="98879-350">How to add additional custom tokens</span></span>

<span data-ttu-id="98879-351">为了演示如何添加作为的一部分存储的自定义令牌， `SaveTokens` 示例应用程序会 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> <xref:System.DateTime> 为的[AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*)添加一个，其中包含 `TicketCreated` ：</span><span class="sxs-lookup"><span data-stu-id="98879-351">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="98879-352">创建和添加声明</span><span class="sxs-lookup"><span data-stu-id="98879-352">Creating and adding claims</span></span>

<span data-ttu-id="98879-353">框架提供用于创建声明并将其添加到集合的常见操作和扩展方法。</span><span class="sxs-lookup"><span data-stu-id="98879-353">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="98879-354">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 和 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>。</span><span class="sxs-lookup"><span data-stu-id="98879-354">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="98879-355">用户可以通过从派生 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> 并实现抽象方法来定义自定义操作 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> 。</span><span class="sxs-lookup"><span data-stu-id="98879-355">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="98879-356">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>。</span><span class="sxs-lookup"><span data-stu-id="98879-356">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="98879-357">删除声明操作和声明</span><span class="sxs-lookup"><span data-stu-id="98879-357">Removal of claim actions and claims</span></span>

<span data-ttu-id="98879-358">[ClaimActionCollection （String）](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*)从集合中移除给定的所有声明操作 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> 。</span><span class="sxs-lookup"><span data-stu-id="98879-358">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="98879-359">[ClaimActionCollectionMapExtensions. DeleteClaim （ClaimActionCollection，String）](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*)从标识中删除给定的声明 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> 。</span><span class="sxs-lookup"><span data-stu-id="98879-359">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="98879-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>主要与[OpenID connect （OIDC）](/azure/active-directory/develop/v2-protocols-oidc)一起使用，以删除协议生成的声明。</span><span class="sxs-lookup"><span data-stu-id="98879-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="98879-361">示例应用程序输出</span><span class="sxs-lookup"><span data-stu-id="98879-361">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="98879-362">ASP.NET Core 应用可以从外部身份验证提供程序（如 Facebook、Google、Microsoft 和 Twitter）建立其他声明和令牌。</span><span class="sxs-lookup"><span data-stu-id="98879-362">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="98879-363">每个提供程序都在其平台上显示有关用户的不同信息，但用于接收用户数据并将其转换为其他声明的模式是相同的。</span><span class="sxs-lookup"><span data-stu-id="98879-363">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="98879-364">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="98879-364">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="98879-365">先决条件</span><span class="sxs-lookup"><span data-stu-id="98879-365">Prerequisites</span></span>

<span data-ttu-id="98879-366">确定要在应用程序中支持的外部身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="98879-366">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="98879-367">对于每个提供程序，注册应用程序，并获取客户端 ID 和客户端密码。</span><span class="sxs-lookup"><span data-stu-id="98879-367">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="98879-368">有关详细信息，请参阅 <xref:security/authentication/social/index>。</span><span class="sxs-lookup"><span data-stu-id="98879-368">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="98879-369">示例应用使用[Google 身份验证提供程序](xref:security/authentication/google-logins)。</span><span class="sxs-lookup"><span data-stu-id="98879-369">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="98879-370">设置客户端 ID 和客户端密码</span><span class="sxs-lookup"><span data-stu-id="98879-370">Set the client ID and client secret</span></span>

<span data-ttu-id="98879-371">OAuth 身份验证提供程序使用客户端 ID 和客户端密码与应用程序建立了信任关系。</span><span class="sxs-lookup"><span data-stu-id="98879-371">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="98879-372">当向提供程序注册应用程序时，外部身份验证提供程序会为应用程序创建客户端 ID 和客户端机密值。</span><span class="sxs-lookup"><span data-stu-id="98879-372">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="98879-373">应用使用的每个外部提供程序必须与提供程序的客户端 ID 和客户端机密一起单独配置。</span><span class="sxs-lookup"><span data-stu-id="98879-373">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="98879-374">有关详细信息，请参阅适用于你的方案的外部身份验证提供程序主题：</span><span class="sxs-lookup"><span data-stu-id="98879-374">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="98879-375">Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="98879-375">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="98879-376">Google 身份验证</span><span class="sxs-lookup"><span data-stu-id="98879-376">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="98879-377">Microsoft 身份验证</span><span class="sxs-lookup"><span data-stu-id="98879-377">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="98879-378">Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="98879-378">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="98879-379">其他身份验证提供程序</span><span class="sxs-lookup"><span data-stu-id="98879-379">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="98879-380">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="98879-380">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="98879-381">示例应用使用 Google 提供的客户端 ID 和客户端机密配置 Google 身份验证提供程序：</span><span class="sxs-lookup"><span data-stu-id="98879-381">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="98879-382">建立身份验证范围</span><span class="sxs-lookup"><span data-stu-id="98879-382">Establish the authentication scope</span></span>

<span data-ttu-id="98879-383">指定要从提供程序检索的权限的列表 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> 。</span><span class="sxs-lookup"><span data-stu-id="98879-383">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="98879-384">常见外部提供程序的身份验证范围如下表中所示。</span><span class="sxs-lookup"><span data-stu-id="98879-384">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="98879-385">提供程序</span><span class="sxs-lookup"><span data-stu-id="98879-385">Provider</span></span>  | <span data-ttu-id="98879-386">范围</span><span class="sxs-lookup"><span data-stu-id="98879-386">Scope</span></span>                                                            |
| ---
<span data-ttu-id="98879-387">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-388">'Blazor'</span></span>
- <span data-ttu-id="98879-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-389">'Identity'</span></span>
- <span data-ttu-id="98879-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-391">'Razor'</span></span>
- <span data-ttu-id="98879-392">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-393">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-394">'Blazor'</span></span>
- <span data-ttu-id="98879-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-395">'Identity'</span></span>
- <span data-ttu-id="98879-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-397">'Razor'</span></span>
- <span data-ttu-id="98879-398">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-398">'SignalR' uid:</span></span> 

<span data-ttu-id="98879-399">----- |---标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-399">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-400">'Blazor'</span></span>
- <span data-ttu-id="98879-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-401">'Identity'</span></span>
- <span data-ttu-id="98879-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-403">'Razor'</span></span>
- <span data-ttu-id="98879-404">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-405">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-406">'Blazor'</span></span>
- <span data-ttu-id="98879-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-407">'Identity'</span></span>
- <span data-ttu-id="98879-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-409">'Razor'</span></span>
- <span data-ttu-id="98879-410">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-411">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-412">'Blazor'</span></span>
- <span data-ttu-id="98879-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-413">'Identity'</span></span>
- <span data-ttu-id="98879-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-415">'Razor'</span></span>
- <span data-ttu-id="98879-416">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-416">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-417">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-418">'Blazor'</span></span>
- <span data-ttu-id="98879-419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-419">'Identity'</span></span>
- <span data-ttu-id="98879-420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-420">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-421">'Razor'</span></span>
- <span data-ttu-id="98879-422">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-422">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-423">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-424">'Blazor'</span></span>
- <span data-ttu-id="98879-425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-425">'Identity'</span></span>
- <span data-ttu-id="98879-426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-426">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-427">'Razor'</span></span>
- <span data-ttu-id="98879-428">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-428">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-429">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-430">'Blazor'</span></span>
- <span data-ttu-id="98879-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-431">'Identity'</span></span>
- <span data-ttu-id="98879-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-433">'Razor'</span></span>
- <span data-ttu-id="98879-434">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-435">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-436">'Blazor'</span></span>
- <span data-ttu-id="98879-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-437">'Identity'</span></span>
- <span data-ttu-id="98879-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-439">'Razor'</span></span>
- <span data-ttu-id="98879-440">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-441">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-442">'Blazor'</span></span>
- <span data-ttu-id="98879-443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-443">'Identity'</span></span>
- <span data-ttu-id="98879-444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-444">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-445">'Razor'</span></span>
- <span data-ttu-id="98879-446">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-447">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-448">'Blazor'</span></span>
- <span data-ttu-id="98879-449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-449">'Identity'</span></span>
- <span data-ttu-id="98879-450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-450">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-451">'Razor'</span></span>
- <span data-ttu-id="98879-452">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-453">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-454">'Blazor'</span></span>
- <span data-ttu-id="98879-455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-455">'Identity'</span></span>
- <span data-ttu-id="98879-456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-456">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-457">'Razor'</span></span>
- <span data-ttu-id="98879-458">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-459">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-460">'Blazor'</span></span>
- <span data-ttu-id="98879-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-461">'Identity'</span></span>
- <span data-ttu-id="98879-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-463">'Razor'</span></span>
- <span data-ttu-id="98879-464">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-465">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-466">'Blazor'</span></span>
- <span data-ttu-id="98879-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-467">'Identity'</span></span>
- <span data-ttu-id="98879-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-469">'Razor'</span></span>
- <span data-ttu-id="98879-470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-471">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-472">'Blazor'</span></span>
- <span data-ttu-id="98879-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-473">'Identity'</span></span>
- <span data-ttu-id="98879-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-475">'Razor'</span></span>
- <span data-ttu-id="98879-476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-477">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-478">'Blazor'</span></span>
- <span data-ttu-id="98879-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-479">'Identity'</span></span>
- <span data-ttu-id="98879-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-481">'Razor'</span></span>
- <span data-ttu-id="98879-482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-483">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-484">'Blazor'</span></span>
- <span data-ttu-id="98879-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-485">'Identity'</span></span>
- <span data-ttu-id="98879-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-487">'Razor'</span></span>
- <span data-ttu-id="98879-488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-489">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-490">'Blazor'</span></span>
- <span data-ttu-id="98879-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-491">'Identity'</span></span>
- <span data-ttu-id="98879-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-493">'Razor'</span></span>
- <span data-ttu-id="98879-494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-495">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-496">'Blazor'</span></span>
- <span data-ttu-id="98879-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-497">'Identity'</span></span>
- <span data-ttu-id="98879-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-499">'Razor'</span></span>
- <span data-ttu-id="98879-500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-501">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-502">'Blazor'</span></span>
- <span data-ttu-id="98879-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-503">'Identity'</span></span>
- <span data-ttu-id="98879-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-505">'Razor'</span></span>
- <span data-ttu-id="98879-506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-507">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-508">'Blazor'</span></span>
- <span data-ttu-id="98879-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-509">'Identity'</span></span>
- <span data-ttu-id="98879-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-511">'Razor'</span></span>
- <span data-ttu-id="98879-512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-513">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-514">'Blazor'</span></span>
- <span data-ttu-id="98879-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-515">'Identity'</span></span>
- <span data-ttu-id="98879-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-517">'Razor'</span></span>
- <span data-ttu-id="98879-518">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-518">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-519">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-520">'Blazor'</span></span>
- <span data-ttu-id="98879-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-521">'Identity'</span></span>
- <span data-ttu-id="98879-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-523">'Razor'</span></span>
- <span data-ttu-id="98879-524">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-525">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-526">'Blazor'</span></span>
- <span data-ttu-id="98879-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-527">'Identity'</span></span>
- <span data-ttu-id="98879-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-529">'Razor'</span></span>
- <span data-ttu-id="98879-530">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-531">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-532">'Blazor'</span></span>
- <span data-ttu-id="98879-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-533">'Identity'</span></span>
- <span data-ttu-id="98879-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-535">'Razor'</span></span>
- <span data-ttu-id="98879-536">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-537">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-538">'Blazor'</span></span>
- <span data-ttu-id="98879-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-539">'Identity'</span></span>
- <span data-ttu-id="98879-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-541">'Razor'</span></span>
- <span data-ttu-id="98879-542">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-543">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-544">'Blazor'</span></span>
- <span data-ttu-id="98879-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-545">'Identity'</span></span>
- <span data-ttu-id="98879-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-547">'Razor'</span></span>
- <span data-ttu-id="98879-548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-549">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-550">'Blazor'</span></span>
- <span data-ttu-id="98879-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-551">'Identity'</span></span>
- <span data-ttu-id="98879-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-553">'Razor'</span></span>
- <span data-ttu-id="98879-554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-555">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-556">'Blazor'</span></span>
- <span data-ttu-id="98879-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-557">'Identity'</span></span>
- <span data-ttu-id="98879-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-559">'Razor'</span></span>
- <span data-ttu-id="98879-560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-561">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-562">'Blazor'</span></span>
- <span data-ttu-id="98879-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-563">'Identity'</span></span>
- <span data-ttu-id="98879-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-565">'Razor'</span></span>
- <span data-ttu-id="98879-566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-567">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-568">'Blazor'</span></span>
- <span data-ttu-id="98879-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-569">'Identity'</span></span>
- <span data-ttu-id="98879-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-571">'Razor'</span></span>
- <span data-ttu-id="98879-572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98879-573">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98879-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98879-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98879-574">'Blazor'</span></span>
- <span data-ttu-id="98879-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98879-575">'Identity'</span></span>
- <span data-ttu-id="98879-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98879-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="98879-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98879-577">'Razor'</span></span>
- <span data-ttu-id="98879-578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="98879-578">'SignalR' uid:</span></span> 

<span data-ttu-id="98879-579">-------------------------------- | |Facebook |`https://www.facebook.com/dialog/oauth`                          |
|Google |`https://www.googleapis.com/auth/userinfo.profile`               |
|Microsoft |`https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
|Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="98879-579">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="98879-580">在示例应用中， `userinfo.profile` 当在上调用时，由框架自动添加 Google 的作用域 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> 。</span><span class="sxs-lookup"><span data-stu-id="98879-580">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="98879-581">如果应用需要其他作用域，请将它们添加到选项。</span><span class="sxs-lookup"><span data-stu-id="98879-581">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="98879-582">在以下示例中，添加了 Google `https://www.googleapis.com/auth/user.birthday.read` 范围以便检索用户的生日：</span><span class="sxs-lookup"><span data-stu-id="98879-582">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="98879-583">映射用户数据密钥并创建声明</span><span class="sxs-lookup"><span data-stu-id="98879-583">Map user data keys and create claims</span></span>

<span data-ttu-id="98879-584">在提供程序的选项中，为 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> 外部提供程序的 JSON 用户数据中的每个键/子项指定或，以便在登录时读取应用程序标识。</span><span class="sxs-lookup"><span data-stu-id="98879-584">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="98879-585">有关声明类型的详细信息，请参阅 <xref:System.Security.Claims.ClaimTypes> 。</span><span class="sxs-lookup"><span data-stu-id="98879-585">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="98879-586">该示例应用 `urn:google:locale` `urn:google:picture` `locale` `picture` 在 Google 用户数据中的和密钥中创建区域设置（）和图片（）声明：</span><span class="sxs-lookup"><span data-stu-id="98879-586">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="98879-587">在中 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` ， <xref:Microsoft.AspNetCore.Identity.IdentityUser> （ `ApplicationUser` ）使用登录到应用 <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> 。</span><span class="sxs-lookup"><span data-stu-id="98879-587">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="98879-588">在登录过程中， <xref:Microsoft.AspNetCore.Identity.UserManager%601> 可以存储 `ApplicationUser` 提供的用户数据的声明 <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> 。</span><span class="sxs-lookup"><span data-stu-id="98879-588">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="98879-589">在示例应用程序中， `OnPostConfirmationAsync` （*Account/ExternalLogin*） `urn:google:locale` 为已登录的提供区域设置（）和图片（ `urn:google:picture` ）声明 `ApplicationUser` ，包括的声明 <xref:System.Security.Claims.ClaimTypes.GivenName> ：</span><span class="sxs-lookup"><span data-stu-id="98879-589">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="98879-590">默认情况下，用户的声明存储在身份验证 cookie 中。</span><span class="sxs-lookup"><span data-stu-id="98879-590">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="98879-591">如果身份验证 cookie 太大，则可能会导致应用程序失败，因为：</span><span class="sxs-lookup"><span data-stu-id="98879-591">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="98879-592">浏览器检测到 cookie 标头太长。</span><span class="sxs-lookup"><span data-stu-id="98879-592">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="98879-593">请求的整体大小太大。</span><span class="sxs-lookup"><span data-stu-id="98879-593">The overall size of the request is too large.</span></span>

<span data-ttu-id="98879-594">如果需要大量用户数据来处理用户请求：</span><span class="sxs-lookup"><span data-stu-id="98879-594">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="98879-595">将请求处理的用户声明的数量和大小限制为仅应用需要的内容。</span><span class="sxs-lookup"><span data-stu-id="98879-595">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="98879-596">使用 <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> Cookie 身份验证中间件的自定义 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> 来跨请求存储标识。</span><span class="sxs-lookup"><span data-stu-id="98879-596">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="98879-597">在服务器上保留大量标识信息，同时仅向客户端发送一个小型会话标识符密钥。</span><span class="sxs-lookup"><span data-stu-id="98879-597">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="98879-598">保存访问令牌</span><span class="sxs-lookup"><span data-stu-id="98879-598">Save the access token</span></span>

<span data-ttu-id="98879-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>定义在授权成功后是否应将访问和刷新令牌存储在中 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> 。</span><span class="sxs-lookup"><span data-stu-id="98879-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="98879-600">`SaveTokens`默认情况下，设置为以 `false` 减小最终身份验证 cookie 的大小。</span><span class="sxs-lookup"><span data-stu-id="98879-600">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="98879-601">示例应用将的值设置 `SaveTokens` 为 `true` 中的 <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> ：</span><span class="sxs-lookup"><span data-stu-id="98879-601">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="98879-602">`OnPostConfirmationAsync`执行时，从的外部提供程序存储访问令牌（[ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)） `ApplicationUser` `AuthenticationProperties` 。</span><span class="sxs-lookup"><span data-stu-id="98879-602">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="98879-603">该示例应用程序将访问令牌保存在 `OnPostConfirmationAsync` 帐户/ExternalLogin 中（新用户注册）和 `OnGetCallbackAsync` （ *Account/ExternalLogin.cshtml.cs*以前注册的用户）中：</span><span class="sxs-lookup"><span data-stu-id="98879-603">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="98879-604">如何添加其他自定义令牌</span><span class="sxs-lookup"><span data-stu-id="98879-604">How to add additional custom tokens</span></span>

<span data-ttu-id="98879-605">为了演示如何添加作为的一部分存储的自定义令牌， `SaveTokens` 示例应用程序会 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> <xref:System.DateTime> 为的[AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*)添加一个，其中包含 `TicketCreated` ：</span><span class="sxs-lookup"><span data-stu-id="98879-605">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="98879-606">创建和添加声明</span><span class="sxs-lookup"><span data-stu-id="98879-606">Creating and adding claims</span></span>

<span data-ttu-id="98879-607">框架提供用于创建声明并将其添加到集合的常见操作和扩展方法。</span><span class="sxs-lookup"><span data-stu-id="98879-607">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="98879-608">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 和 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>。</span><span class="sxs-lookup"><span data-stu-id="98879-608">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="98879-609">用户可以通过从派生 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> 并实现抽象方法来定义自定义操作 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> 。</span><span class="sxs-lookup"><span data-stu-id="98879-609">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="98879-610">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>。</span><span class="sxs-lookup"><span data-stu-id="98879-610">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="98879-611">删除声明操作和声明</span><span class="sxs-lookup"><span data-stu-id="98879-611">Removal of claim actions and claims</span></span>

<span data-ttu-id="98879-612">[ClaimActionCollection （String）](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*)从集合中移除给定的所有声明操作 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> 。</span><span class="sxs-lookup"><span data-stu-id="98879-612">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="98879-613">[ClaimActionCollectionMapExtensions. DeleteClaim （ClaimActionCollection，String）](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*)从标识中删除给定的声明 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> 。</span><span class="sxs-lookup"><span data-stu-id="98879-613">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="98879-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>主要与[OpenID connect （OIDC）](/azure/active-directory/develop/v2-protocols-oidc)一起使用，以删除协议生成的声明。</span><span class="sxs-lookup"><span data-stu-id="98879-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="98879-615">示例应用程序输出</span><span class="sxs-lookup"><span data-stu-id="98879-615">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="98879-616">其他资源</span><span class="sxs-lookup"><span data-stu-id="98879-616">Additional resources</span></span>

* <span data-ttu-id="98879-617">[dotnet/AspNetCore 工程 SocialSample 应用](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample)：链接的示例应用位于[Dotnet/AspNetCore GitHub 存储库的](https://github.com/dotnet/AspNetCore) `master` 工程分支中。</span><span class="sxs-lookup"><span data-stu-id="98879-617">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="98879-618">`master`分支包含处于活动开发下的下一版本 ASP.NET Core 的代码。</span><span class="sxs-lookup"><span data-stu-id="98879-618">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="98879-619">若要查看 ASP.NET Core 的已发布版本的示例应用的版本，请使用**分支**下拉列表选择一个发布分支（例如 `release/{X.Y}` ）。</span><span class="sxs-lookup"><span data-stu-id="98879-619">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
