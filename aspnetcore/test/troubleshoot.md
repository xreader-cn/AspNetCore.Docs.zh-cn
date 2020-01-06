---
title: ASP.NET Core 项目疑难解答和调试
author: Rick-Anderson
description: 理解 ASP.NET Core 项目的警告和错误，并对其进行故障排除。
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: 73a73fb51571e5f7b706ff4b958217854750c1fb
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354710"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="f7a80-103">ASP.NET Core 项目疑难解答和调试</span><span class="sxs-lookup"><span data-stu-id="f7a80-103">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="f7a80-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f7a80-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f7a80-105">以下链接提供了故障排除指南：</span><span class="sxs-lookup"><span data-stu-id="f7a80-105">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="f7a80-106">ASP.NET Core 应用程序中 （ndc） 会议 （伦敦，2018年）： 诊断问题</span><span class="sxs-lookup"><span data-stu-id="f7a80-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="f7a80-107">ASP.NET 博客： 解决 ASP.NET Core 性能问题</span><span class="sxs-lookup"><span data-stu-id="f7a80-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="f7a80-108">.NET Core SDK 警告</span><span class="sxs-lookup"><span data-stu-id="f7a80-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="f7a80-109">同时安装了 .NET Core SDK 的32位和64位版本</span><span class="sxs-lookup"><span data-stu-id="f7a80-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="f7a80-110">在**新项目**对话框为 ASP.NET Core，你可能会看到以下警告：</span><span class="sxs-lookup"><span data-stu-id="f7a80-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="f7a80-111">同时安装了 .NET Core SDK 的32位和64位版本。</span><span class="sxs-lookup"><span data-stu-id="f7a80-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="f7a80-112">仅显示64位版本中安装了 "C：\\Program Files\\dotnet\\sdk\\" 的模板。</span><span class="sxs-lookup"><span data-stu-id="f7a80-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="f7a80-113">时，此警告会出现 (x86) 32 位和 64 位 (x64) 版本的[.NET Core SDK](https://www.microsoft.com/net/download/all)安装。</span><span class="sxs-lookup"><span data-stu-id="f7a80-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://www.microsoft.com/net/download/all) are installed.</span></span> <span data-ttu-id="f7a80-114">这两个版本可能安装的常见原因包括：</span><span class="sxs-lookup"><span data-stu-id="f7a80-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="f7a80-115">你最初下载.NET Core SDK 安装程序使用 32 位计算机，但然后复制它跨并安装在 64 位计算机上。</span><span class="sxs-lookup"><span data-stu-id="f7a80-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="f7a80-116">由另一个应用程序安装了 32 位.NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="f7a80-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="f7a80-117">下载并安装了错误的版本。</span><span class="sxs-lookup"><span data-stu-id="f7a80-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="f7a80-118">卸载 32 位.NET Core SDK，以防止出现此警告。</span><span class="sxs-lookup"><span data-stu-id="f7a80-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="f7a80-119">从 **"控制面板" > "** **程序和功能**" 中卸载 > **卸载或更改程序**。</span><span class="sxs-lookup"><span data-stu-id="f7a80-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="f7a80-120">如果了解警告出现的原因及其含义，可以忽略此警告。</span><span class="sxs-lookup"><span data-stu-id="f7a80-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="f7a80-121">.NET Core SDK 的安装在多个位置</span><span class="sxs-lookup"><span data-stu-id="f7a80-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="f7a80-122">在**新项目**对话框为 ASP.NET Core，你可能会看到以下警告：</span><span class="sxs-lookup"><span data-stu-id="f7a80-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="f7a80-123">.NET Core SDK 安装在多个位置中。</span><span class="sxs-lookup"><span data-stu-id="f7a80-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="f7a80-124">仅显示在 "C：\\Program Files\\dotnet\\sdk\\" 上安装的 Sdk 中的模板。</span><span class="sxs-lookup"><span data-stu-id="f7a80-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="f7a80-125">外部的一个目录中有至少一个安装的.NET Core SDK 时，将显示此消息*c:\\Program Files\\dotnet\\sdk\\* 。</span><span class="sxs-lookup"><span data-stu-id="f7a80-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="f7a80-126">这通常发生在使用复制/粘贴，而不 MSI 安装程序的计算机上部署了.NET Core SDK 时。</span><span class="sxs-lookup"><span data-stu-id="f7a80-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="f7a80-127">卸载所有32位 .NET Core Sdk 和运行时，以防出现此警告。</span><span class="sxs-lookup"><span data-stu-id="f7a80-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="f7a80-128">从 **"控制面板" > "** **程序和功能**" 中卸载 > **卸载或更改程序**。</span><span class="sxs-lookup"><span data-stu-id="f7a80-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="f7a80-129">如果了解警告出现的原因及其含义，可以忽略此警告。</span><span class="sxs-lookup"><span data-stu-id="f7a80-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="f7a80-130">检测到没有.NET Core Sdk</span><span class="sxs-lookup"><span data-stu-id="f7a80-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="f7a80-131">在 ASP.NET Core 的 Visual Studio "**新建项目**" 对话框中，你可能会看到以下警告：</span><span class="sxs-lookup"><span data-stu-id="f7a80-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="f7a80-132">未检测到任何 .NET Core Sdk，请确保它们包含在环境变量 `PATH`中。</span><span class="sxs-lookup"><span data-stu-id="f7a80-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="f7a80-133">执行 `dotnet` 命令时，警告显示为：</span><span class="sxs-lookup"><span data-stu-id="f7a80-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="f7a80-134">找不到任何已安装的 dotnet Sdk。</span><span class="sxs-lookup"><span data-stu-id="f7a80-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="f7a80-135">如果环境变量 `PATH` 不指向计算机上的任何 .NET Core Sdk，则会出现这些警告。</span><span class="sxs-lookup"><span data-stu-id="f7a80-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="f7a80-136">ИфЅвѕцґЛОКМв，ЗлЦґРРТФПВІЩЧч：</span><span class="sxs-lookup"><span data-stu-id="f7a80-136">To resolve this problem:</span></span>

* <span data-ttu-id="f7a80-137">安装 .NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="f7a80-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="f7a80-138">从[.Net 下载](https://dotnet.microsoft.com/download)获取最新的安装程序。</span><span class="sxs-lookup"><span data-stu-id="f7a80-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="f7a80-139">验证 `PATH` 环境变量是否指向安装 SDK 的位置（对于64位/x64，则为`C:\Program Files\dotnet\`，对于32位/x86 为 `C:\Program Files (x86)\dotnet\`）。</span><span class="sxs-lookup"><span data-stu-id="f7a80-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="f7a80-140">SDK 安装程序通常会设置 `PATH`。</span><span class="sxs-lookup"><span data-stu-id="f7a80-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="f7a80-141">始终在同一台计算机上安装相同的位数 Sdk 和运行时。</span><span class="sxs-lookup"><span data-stu-id="f7a80-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="f7a80-142">安装 .NET Core 托管捆绑包后缺少 SDK</span><span class="sxs-lookup"><span data-stu-id="f7a80-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="f7a80-143">安装 .net [Core 托管捆绑](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)会在安装 .net core 运行时以指向 .net core 的32位（x86）版本（`C:\Program Files (x86)\dotnet\`）时修改 `PATH`。</span><span class="sxs-lookup"><span data-stu-id="f7a80-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="f7a80-144">当使用32位（x86） .NET Core `dotnet` 命令时，这可能会导致缺少 Sdk （[未检测到 .Net Core sdk](#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="f7a80-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="f7a80-145">若要解决此问题，请将 `C:\Program Files\dotnet\` 移到 `PATH`之前 `C:\Program Files (x86)\dotnet\` 的位置。</span><span class="sxs-lookup"><span data-stu-id="f7a80-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="f7a80-146">从应用中获取数据</span><span class="sxs-lookup"><span data-stu-id="f7a80-146">Obtain data from an app</span></span>

<span data-ttu-id="f7a80-147">如果某个应用能够响应请求，则可以使用中间件从应用获取以下数据：</span><span class="sxs-lookup"><span data-stu-id="f7a80-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="f7a80-148">Request &ndash; 方法，方案，主机，pathbase，路径，查询字符串，标头</span><span class="sxs-lookup"><span data-stu-id="f7a80-148">Request &ndash; Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="f7a80-149">连接 &ndash; 远程 IP 地址，远程端口，本地 IP 地址，本地端口，客户端证书</span><span class="sxs-lookup"><span data-stu-id="f7a80-149">Connection &ndash; Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="f7a80-150">标识 &ndash; 名称，显示名称</span><span class="sxs-lookup"><span data-stu-id="f7a80-150">Identity &ndash; Name, display name</span></span>
* <span data-ttu-id="f7a80-151">配置设置</span><span class="sxs-lookup"><span data-stu-id="f7a80-151">Configuration settings</span></span>
* <span data-ttu-id="f7a80-152">环境变量</span><span class="sxs-lookup"><span data-stu-id="f7a80-152">Environment variables</span></span>

<span data-ttu-id="f7a80-153">将以下[中间件](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)代码置于 `Startup.Configure` 方法的请求处理管道的开头。</span><span class="sxs-lookup"><span data-stu-id="f7a80-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="f7a80-154">在运行中间件之前会检查环境，以确保仅在开发环境中执行代码。</span><span class="sxs-lookup"><span data-stu-id="f7a80-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="f7a80-155">若要获取环境，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="f7a80-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="f7a80-156">将 `IHostingEnvironment` 插入到 `Startup.Configure` 方法中，并检查带有本地变量的环境。</span><span class="sxs-lookup"><span data-stu-id="f7a80-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="f7a80-157">下面的示例代码演示了这种方法。</span><span class="sxs-lookup"><span data-stu-id="f7a80-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="f7a80-158">将环境分配到 `Startup` 类中的属性。</span><span class="sxs-lookup"><span data-stu-id="f7a80-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="f7a80-159">使用属性（例如 `if (Environment.IsDevelopment())`）检查环境。</span><span class="sxs-lookup"><span data-stu-id="f7a80-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="f7a80-160">调试 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="f7a80-160">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="f7a80-161">以下链接提供有关 ASP.NET Core 应用程序进行调试的信息。</span><span class="sxs-lookup"><span data-stu-id="f7a80-161">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="f7a80-162">在 Linux 上调试 ASP Core</span><span class="sxs-lookup"><span data-stu-id="f7a80-162">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="f7a80-163">通过 SSH 调试 Unix 上的 .NET Core</span><span class="sxs-lookup"><span data-stu-id="f7a80-163">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="f7a80-164">快速入门：通过 Visual Studio 调试器调试 ASP.NET</span><span class="sxs-lookup"><span data-stu-id="f7a80-164">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="f7a80-165">有关更多调试信息，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/2960)。</span><span class="sxs-lookup"><span data-stu-id="f7a80-165">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
