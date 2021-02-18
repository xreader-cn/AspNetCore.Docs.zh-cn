---
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
ms.openlocfilehash: 08d212b48c3f97531bea34b11d5b8c9a9069ae34
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536272"
---
<a name="csc"></a>

<span data-ttu-id="97f35-101">请考虑下面的 `ConfigureServices` 方法，该方法可注册服务并配置选项：</span><span class="sxs-lookup"><span data-stu-id="97f35-101">Consider the following `ConfigureServices` method, which registers services and configures options:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

<span data-ttu-id="97f35-102">可以将相关的注册组移动到扩展方法以注册服务。</span><span class="sxs-lookup"><span data-stu-id="97f35-102">Related groups of registrations can be moved to an extension method to register services.</span></span> <span data-ttu-id="97f35-103">例如，配置服务会被添加到以下类中：</span><span class="sxs-lookup"><span data-stu-id="97f35-103">For example, the configuration services are added to the following class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

<span data-ttu-id="97f35-104">剩余的服务会在类似的类中注册。</span><span class="sxs-lookup"><span data-stu-id="97f35-104">The remaining services are registered in a similar class.</span></span> <span data-ttu-id="97f35-105">下面的 `ConfigureServices` 方法使用新扩展方法来注册服务：</span><span class="sxs-lookup"><span data-stu-id="97f35-105">The following `ConfigureServices` method uses the new extension methods to register the services:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

<span data-ttu-id="97f35-106">**注意：** 每个 `services.Add{GROUP_NAME}` 扩展方法添加并可能配置服务。</span><span class="sxs-lookup"><span data-stu-id="97f35-106">**_Note:_** Each `services.Add{GROUP_NAME}` extension method adds and potentially configures services.</span></span> <span data-ttu-id="97f35-107">例如，<xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> 会添加带视图的 MVC 控制器所需的服务，<xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> 会添加 Razor Pages 所需的服务。</span><span class="sxs-lookup"><span data-stu-id="97f35-107">For example, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> adds the services MVC controllers with views require, and <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> adds the services Razor Pages requires.</span></span> <span data-ttu-id="97f35-108">我们建议应用遵循此命名约定。</span><span class="sxs-lookup"><span data-stu-id="97f35-108">We recommended that apps follow this naming convention.</span></span> <span data-ttu-id="97f35-109">将扩展方法置于 <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> 命名空间中以封装服务注册的组。</span><span class="sxs-lookup"><span data-stu-id="97f35-109">Place extension methods in the <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> namespace to encapsulate groups of service registrations.</span></span>
