---
title: ASP.NET Core Blazor 静态文件
author: guardrex
description: 了解如何配置和管理 Blazor 应用的静态文件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/27/2021
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
uid: blazor/fundamentals/static-files
ms.openlocfilehash: 709250251113014027fe86c6a373e58a9c2a5009
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "100109930"
---
# <a name="aspnet-core-blazor-static-files"></a><span data-ttu-id="7bc0a-103">ASP.NET Core Blazor 静态文件</span><span class="sxs-lookup"><span data-stu-id="7bc0a-103">ASP.NET Core Blazor static files</span></span>

<span data-ttu-id="7bc0a-104">本文适用于 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="7bc0a-104">*This article applies to Blazor Server.*</span></span>

<span data-ttu-id="7bc0a-105">若要使用 <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> 创建其他文件映射，或者要配置其他 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>，请使用以下方法之一。</span><span class="sxs-lookup"><span data-stu-id="7bc0a-105">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="7bc0a-106">在以下示例中，`{EXTENSION}` 占位符为文件扩展名，`{CONTENT TYPE}` 占位符为内容类型。</span><span class="sxs-lookup"><span data-stu-id="7bc0a-106">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="7bc0a-107">使用 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> 通过 `Startup.ConfigureServices` (`Startup.cs`) 中的[依赖项注入 (DI)](xref:blazor/fundamentals/dependency-injection) 来配置选项：</span><span class="sxs-lookup"><span data-stu-id="7bc0a-107">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  <span data-ttu-id="7bc0a-108">此方法会配置用于为 `blazor.server.js`提供服务的相同文件提供程序，因此请确保你的自定义配置不会干扰为 `blazor.server.js` 提供服务。</span><span class="sxs-lookup"><span data-stu-id="7bc0a-108">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="7bc0a-109">例如，不要通过使用 `provider.Mappings.Remove(".js")` 配置提供程序来删除 JavaScript 文件的映射。</span><span class="sxs-lookup"><span data-stu-id="7bc0a-109">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="7bc0a-110">在 `Startup.Configure` (`Startup.cs`) 中使用两次对 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> 的调用：</span><span class="sxs-lookup"><span data-stu-id="7bc0a-110">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="7bc0a-111">使用 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> 在第一次调用中配置自定义文件提供程序。</span><span class="sxs-lookup"><span data-stu-id="7bc0a-111">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="7bc0a-112">第二个中间件提供 `blazor.server.js`，其使用 Blazor 框架提供的默认静态文件配置。</span><span class="sxs-lookup"><span data-stu-id="7bc0a-112">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the Blazor framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* <span data-ttu-id="7bc0a-113">可以使用 <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> 执行自定义静态文件中间件来避免在提供 `_framework/blazor.server.js` 时受到干扰：</span><span class="sxs-lookup"><span data-stu-id="7bc0a-113">You can avoid interfering with serving `_framework/blazor.server.js` by using <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> to execute a custom Static File Middleware:</span></span>

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```
