---
title: 解决 ASP.NET Core 项目
author: Rick-Anderson
description: 理解 ASP.NET Core 项目的警告和错误，并对其进行故障排除。
ms.author: riande
ms.custom: mvc
ms.date: 03/13/2019
uid: test/troubleshoot
ms.openlocfilehash: 3d755b2f0c509d65dea86bbe719e42935d87d546
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895324"
---
# <a name="troubleshoot-aspnet-core-projects"></a><span data-ttu-id="65c0c-103">解决 ASP.NET Core 项目</span><span class="sxs-lookup"><span data-stu-id="65c0c-103">Troubleshoot ASP.NET Core projects</span></span>

<span data-ttu-id="65c0c-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="65c0c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="65c0c-105">以下链接提供的故障排除指南：</span><span class="sxs-lookup"><span data-stu-id="65c0c-105">The following links provide troubleshooting guidance:</span></span>

* <xref:host-and-deploy/azure-apps/troubleshoot>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="65c0c-106">NDC 会议 （伦敦，2018年）：诊断 ASP.NET Core 应用程序中的问题</span><span class="sxs-lookup"><span data-stu-id="65c0c-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="65c0c-107">ASP.NET 博客：ASP.NET Core 性能问题故障排除</span><span class="sxs-lookup"><span data-stu-id="65c0c-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="65c0c-108">.NET Core SDK 警告</span><span class="sxs-lookup"><span data-stu-id="65c0c-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="65c0c-109">安装的 32 位和 64 位版本的.NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="65c0c-109">Both the 32 bit and 64 bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="65c0c-110">在**新项目**对话框为 ASP.NET Core，你可能会看到以下警告：</span><span class="sxs-lookup"><span data-stu-id="65c0c-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="65c0c-111">安装了.NET Core SDK 32 和 64 位版本。</span><span class="sxs-lookup"><span data-stu-id="65c0c-111">Both 32 and 64 bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="65c0c-112">仅从安装在 64 位版本的模板 c:\\Program Files\\dotnet\\sdk\\将显示。</span><span class="sxs-lookup"><span data-stu-id="65c0c-112">Only templates from the 64 bit version(s) installed at 'C:\\Program Files\\dotnet\\sdk\\' will be displayed.</span></span>

<span data-ttu-id="65c0c-113">时，此警告会出现 (x86) 32 位和 64 位 (x64) 版本的[.NET Core SDK](https://www.microsoft.com/net/download/all)安装。</span><span class="sxs-lookup"><span data-stu-id="65c0c-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://www.microsoft.com/net/download/all) are installed.</span></span> <span data-ttu-id="65c0c-114">可能安装这两个版本的常见原因包括：</span><span class="sxs-lookup"><span data-stu-id="65c0c-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="65c0c-115">你最初下载.NET Core SDK 安装程序使用 32 位计算机，但然后复制它跨并安装在 64 位计算机上。</span><span class="sxs-lookup"><span data-stu-id="65c0c-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="65c0c-116">由另一个应用程序安装了 32 位.NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="65c0c-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="65c0c-117">下载并安装了错误的版本。</span><span class="sxs-lookup"><span data-stu-id="65c0c-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="65c0c-118">卸载 32 位.NET Core SDK，以防止出现此警告。</span><span class="sxs-lookup"><span data-stu-id="65c0c-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="65c0c-119">从卸载**Control Panel** > **程序和功能** > **卸载或更改程序**。</span><span class="sxs-lookup"><span data-stu-id="65c0c-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="65c0c-120">如果您了解为何会出现的警告和其影响，则可以忽略该警告。</span><span class="sxs-lookup"><span data-stu-id="65c0c-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="65c0c-121">.NET Core SDK 的安装在多个位置</span><span class="sxs-lookup"><span data-stu-id="65c0c-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="65c0c-122">在**新项目**对话框为 ASP.NET Core，你可能会看到以下警告：</span><span class="sxs-lookup"><span data-stu-id="65c0c-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="65c0c-123">.NET Core SDK 安装在多个位置中。</span><span class="sxs-lookup"><span data-stu-id="65c0c-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="65c0c-124">仅安装在上 SDK 的模板 c:\\Program Files\\dotnet\\sdk\\将显示。</span><span class="sxs-lookup"><span data-stu-id="65c0c-124">Only templates from the SDK(s) installed at 'C:\\Program Files\\dotnet\\sdk\\' will be displayed.</span></span>

<span data-ttu-id="65c0c-125">外部的一个目录中有至少一个安装的.NET Core SDK 时，将显示此消息*c:\\Program Files\\dotnet\\sdk\\*。</span><span class="sxs-lookup"><span data-stu-id="65c0c-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="65c0c-126">这通常发生在使用复制/粘贴，而不 MSI 安装程序的计算机上部署了.NET Core SDK 时。</span><span class="sxs-lookup"><span data-stu-id="65c0c-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="65c0c-127">卸载所有 32 位.NET Core Sdk 和运行时以防止出现此警告。</span><span class="sxs-lookup"><span data-stu-id="65c0c-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="65c0c-128">从卸载**Control Panel** > **程序和功能** > **卸载或更改程序**。</span><span class="sxs-lookup"><span data-stu-id="65c0c-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="65c0c-129">如果您了解为何会出现的警告和其影响，则可以忽略该警告。</span><span class="sxs-lookup"><span data-stu-id="65c0c-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="65c0c-130">检测到没有.NET Core Sdk</span><span class="sxs-lookup"><span data-stu-id="65c0c-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="65c0c-131">在 Visual Studio**新的项目**对话框适用于 ASP.NET Core，可能会看到以下警告：</span><span class="sxs-lookup"><span data-stu-id="65c0c-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="65c0c-132">检测到任何.NET Core Sdk，请确保将它们包括在环境变量`PATH`。</span><span class="sxs-lookup"><span data-stu-id="65c0c-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="65c0c-133">执行时`dotnet`命令时，警告显示为：</span><span class="sxs-lookup"><span data-stu-id="65c0c-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="65c0c-134">无法找到任何已安装的 dotnet Sdk。</span><span class="sxs-lookup"><span data-stu-id="65c0c-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="65c0c-135">这些警告显示时的环境变量`PATH`没有指向计算机上任何.NET Core Sdk。</span><span class="sxs-lookup"><span data-stu-id="65c0c-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="65c0c-136">若要解决此问题：</span><span class="sxs-lookup"><span data-stu-id="65c0c-136">To resolve this problem:</span></span>

* <span data-ttu-id="65c0c-137">安装 .NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="65c0c-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="65c0c-138">获取从最新的安装程序[.NET 下载](https://dotnet.microsoft.com/download)。</span><span class="sxs-lookup"><span data-stu-id="65c0c-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="65c0c-139">确认`PATH`环境变量指向 SDK 安装的位置 (`C:\Program Files\dotnet\`为 64 位 x64 或`C:\Program Files (x86)\dotnet\`为 32 位 x86)。</span><span class="sxs-lookup"><span data-stu-id="65c0c-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="65c0c-140">在 SDK 安装程序通常设置`PATH`。</span><span class="sxs-lookup"><span data-stu-id="65c0c-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="65c0c-141">始终在同一台计算机上安装相同的位数 Sdk 和运行时。</span><span class="sxs-lookup"><span data-stu-id="65c0c-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="65c0c-142">安装.NET Core 托管捆绑包后缺少 SDK</span><span class="sxs-lookup"><span data-stu-id="65c0c-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="65c0c-143">安装[.NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)修改`PATH`时，它会安装.NET Core 运行时指向 32 位 (x86) 版本的.NET Core (`C:\Program Files (x86)\dotnet\`)。</span><span class="sxs-lookup"><span data-stu-id="65c0c-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="65c0c-144">这可能导致缺少 Sdk 时 32 位 (x86) 的.NET Core`dotnet`使用命令 ([检测到任何.NET Core Sdk](#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="65c0c-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="65c0c-145">若要解决此问题，移动`C:\Program Files\dotnet\`到之前的位置`C:\Program Files (x86)\dotnet\`上`PATH`。</span><span class="sxs-lookup"><span data-stu-id="65c0c-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="65c0c-146">从应用中获取数据</span><span class="sxs-lookup"><span data-stu-id="65c0c-146">Obtain data from an app</span></span>

<span data-ttu-id="65c0c-147">如果应用程序能够对请求作出响应，你可以从应用使用中间件获取以下数据：</span><span class="sxs-lookup"><span data-stu-id="65c0c-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="65c0c-148">请求&ndash;方法、 方案、 主机、 pathbase、 路径、 查询字符串，标头</span><span class="sxs-lookup"><span data-stu-id="65c0c-148">Request &ndash; Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="65c0c-149">连接&ndash;远程 IP 地址、 远程端口、 本地 IP 地址、 本地端口、 客户端证书</span><span class="sxs-lookup"><span data-stu-id="65c0c-149">Connection &ndash; Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="65c0c-150">标识&ndash;名称、 显示名称</span><span class="sxs-lookup"><span data-stu-id="65c0c-150">Identity &ndash; Name, display name</span></span>
* <span data-ttu-id="65c0c-151">配置设置</span><span class="sxs-lookup"><span data-stu-id="65c0c-151">Configuration settings</span></span>
* <span data-ttu-id="65c0c-152">环境变量</span><span class="sxs-lookup"><span data-stu-id="65c0c-152">Environment variables</span></span>

<span data-ttu-id="65c0c-153">将以下项放[中间件](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)代码的开头`Startup.Configure`方法的请求处理管道。</span><span class="sxs-lookup"><span data-stu-id="65c0c-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="65c0c-154">中间件运行以确保仅在开发环境中执行的代码之前，将检查该环境。</span><span class="sxs-lookup"><span data-stu-id="65c0c-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="65c0c-155">若要获取该环境，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="65c0c-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="65c0c-156">注入`IHostingEnvironment`到`Startup.Configure`方法，并检查本地变量的环境。</span><span class="sxs-lookup"><span data-stu-id="65c0c-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="65c0c-157">下面的示例代码演示了这种方法。</span><span class="sxs-lookup"><span data-stu-id="65c0c-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="65c0c-158">将在环境中的属性分配`Startup`类。</span><span class="sxs-lookup"><span data-stu-id="65c0c-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="65c0c-159">检查在环境中使用属性 (例如， `if (Environment.IsDevelopment())`)。</span><span class="sxs-lookup"><span data-stu-id="65c0c-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

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
