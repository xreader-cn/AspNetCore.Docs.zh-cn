---
title: 解决 ASP.NET Core 项目
author: Rick-Anderson
description: 理解 ASP.NET Core 项目的警告和错误，并对其进行故障排除。
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2019
uid: test/troubleshoot
ms.openlocfilehash: 1f53375a6aa504c989920a0bff83b400b7bd83ec
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208838"
---
# <a name="troubleshoot-aspnet-core-projects"></a><span data-ttu-id="21d8f-103">解决 ASP.NET Core 项目</span><span class="sxs-lookup"><span data-stu-id="21d8f-103">Troubleshoot ASP.NET Core projects</span></span>

<span data-ttu-id="21d8f-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="21d8f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="21d8f-105">以下链接提供的故障排除指南：</span><span class="sxs-lookup"><span data-stu-id="21d8f-105">The following links provide troubleshooting guidance:</span></span>

* <xref:host-and-deploy/azure-apps/troubleshoot>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="21d8f-106">NDC 会议 （伦敦，2018年）：诊断 ASP.NET Core 应用程序中的问题</span><span class="sxs-lookup"><span data-stu-id="21d8f-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="21d8f-107">ASP.NET 博客：ASP.NET Core 性能问题故障排除</span><span class="sxs-lookup"><span data-stu-id="21d8f-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="21d8f-108">.NET Core SDK 警告</span><span class="sxs-lookup"><span data-stu-id="21d8f-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="21d8f-109">安装的 32 位和 64 位版本的.NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="21d8f-109">Both the 32 bit and 64 bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="21d8f-110">在**新项目**对话框为 ASP.NET Core，你可能会看到以下警告：</span><span class="sxs-lookup"><span data-stu-id="21d8f-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="21d8f-111">安装了.NET Core SDK 32 和 64 位版本。</span><span class="sxs-lookup"><span data-stu-id="21d8f-111">Both 32 and 64 bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="21d8f-112">仅从安装在 64 位版本的模板 c:\\Program Files\\dotnet\\sdk\\将显示。</span><span class="sxs-lookup"><span data-stu-id="21d8f-112">Only templates from the 64 bit version(s) installed at 'C:\\Program Files\\dotnet\\sdk\\' will be displayed.</span></span>

![显示的警告消息的 OneASP.NET 对话框屏幕截图](troubleshoot/_static/both32and64bit.png)

<span data-ttu-id="21d8f-114">时，此警告会出现 (x86) 32 位和 64 位 (x64) 版本的[.NET Core SDK](https://www.microsoft.com/net/download/all)安装。</span><span class="sxs-lookup"><span data-stu-id="21d8f-114">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://www.microsoft.com/net/download/all) are installed.</span></span> <span data-ttu-id="21d8f-115">可能安装这两个版本的常见原因包括：</span><span class="sxs-lookup"><span data-stu-id="21d8f-115">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="21d8f-116">你最初下载.NET Core SDK 安装程序使用 32 位计算机，但然后复制它跨并安装在 64 位计算机上。</span><span class="sxs-lookup"><span data-stu-id="21d8f-116">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="21d8f-117">由另一个应用程序安装了 32 位.NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="21d8f-117">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="21d8f-118">下载并安装了错误的版本。</span><span class="sxs-lookup"><span data-stu-id="21d8f-118">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="21d8f-119">卸载 32 位.NET Core SDK，以防止出现此警告。</span><span class="sxs-lookup"><span data-stu-id="21d8f-119">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="21d8f-120">从卸载**Control Panel** > **程序和功能** > **卸载或更改程序**。</span><span class="sxs-lookup"><span data-stu-id="21d8f-120">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="21d8f-121">如果您了解为何会出现的警告和其影响，则可以忽略该警告。</span><span class="sxs-lookup"><span data-stu-id="21d8f-121">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="21d8f-122">.NET Core SDK 的安装在多个位置</span><span class="sxs-lookup"><span data-stu-id="21d8f-122">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="21d8f-123">在**新项目**对话框为 ASP.NET Core，你可能会看到以下警告：</span><span class="sxs-lookup"><span data-stu-id="21d8f-123">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="21d8f-124">.NET Core SDK 安装在多个位置中。</span><span class="sxs-lookup"><span data-stu-id="21d8f-124">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="21d8f-125">仅安装在上 SDK 的模板 c:\\Program Files\\dotnet\\sdk\\将显示。</span><span class="sxs-lookup"><span data-stu-id="21d8f-125">Only templates from the SDK(s) installed at 'C:\\Program Files\\dotnet\\sdk\\' will be displayed.</span></span>

![显示的警告消息的 OneASP.NET 对话框屏幕截图](troubleshoot/_static/multiplelocations.png)

<span data-ttu-id="21d8f-127">外部的一个目录中有至少一个安装的.NET Core SDK 时，将显示此消息*c:\\Program Files\\dotnet\\sdk\\*。</span><span class="sxs-lookup"><span data-stu-id="21d8f-127">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="21d8f-128">这通常发生在使用复制/粘贴，而不 MSI 安装程序的计算机上部署了.NET Core SDK 时。</span><span class="sxs-lookup"><span data-stu-id="21d8f-128">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="21d8f-129">卸载所有 32 位.NET Core Sdk 和运行时以防止出现此警告。</span><span class="sxs-lookup"><span data-stu-id="21d8f-129">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="21d8f-130">从卸载**Control Panel** > **程序和功能** > **卸载或更改程序**。</span><span class="sxs-lookup"><span data-stu-id="21d8f-130">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="21d8f-131">如果您了解为何会出现的警告和其影响，则可以忽略该警告。</span><span class="sxs-lookup"><span data-stu-id="21d8f-131">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="21d8f-132">检测到没有.NET Core Sdk</span><span class="sxs-lookup"><span data-stu-id="21d8f-132">No .NET Core SDKs were detected</span></span>

<span data-ttu-id="21d8f-133">在**新项目**对话框为 ASP.NET Core，你可能会看到以下警告：</span><span class="sxs-lookup"><span data-stu-id="21d8f-133">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="21d8f-134">检测到没有.NET Core Sdk，请确保将它们包括在环境变量 PATH。</span><span class="sxs-lookup"><span data-stu-id="21d8f-134">No .NET Core SDKs were detected, ensure they are included in the environment variable 'PATH'.</span></span>

![显示的警告消息的 OneASP.NET 对话框屏幕截图](troubleshoot/_static/NoNetCore.png)

<span data-ttu-id="21d8f-136">何时会出现此警告的环境变量`PATH`没有指向计算机上任何.NET Core Sdk (例如，`C:\Program Files\dotnet\`和`C:\Program Files (x86)\dotnet\`)。</span><span class="sxs-lookup"><span data-stu-id="21d8f-136">This warning appears when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine (for example, `C:\Program Files\dotnet\` and `C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="21d8f-137">若要解决此问题：</span><span class="sxs-lookup"><span data-stu-id="21d8f-137">To resolve this problem:</span></span>

* <span data-ttu-id="21d8f-138">安装或验证.NET Core SDK 的安装。</span><span class="sxs-lookup"><span data-stu-id="21d8f-138">Install or verify the .NET Core SDK is installed.</span></span> <span data-ttu-id="21d8f-139">获取从最新的安装程序[.NET 下载](https://dotnet.microsoft.com/download)。</span><span class="sxs-lookup"><span data-stu-id="21d8f-139">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span> 
* <span data-ttu-id="21d8f-140">验证`PATH`环境变量指向 SDK 安装的位置。</span><span class="sxs-lookup"><span data-stu-id="21d8f-140">Verify that the `PATH` environment variable points to the location where the SDK is installed.</span></span> <span data-ttu-id="21d8f-141">安装程序通常设置`PATH`。</span><span class="sxs-lookup"><span data-stu-id="21d8f-141">The installer normally sets the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="21d8f-142">从应用中获取数据</span><span class="sxs-lookup"><span data-stu-id="21d8f-142">Obtain data from an app</span></span>

<span data-ttu-id="21d8f-143">如果应用程序能够对请求作出响应，你可以从应用使用中间件获取以下数据：</span><span class="sxs-lookup"><span data-stu-id="21d8f-143">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="21d8f-144">请求&ndash;方法、 方案、 主机、 pathbase、 路径、 查询字符串，标头</span><span class="sxs-lookup"><span data-stu-id="21d8f-144">Request &ndash; Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="21d8f-145">连接&ndash;远程 IP 地址、 远程端口、 本地 IP 地址、 本地端口、 客户端证书</span><span class="sxs-lookup"><span data-stu-id="21d8f-145">Connection &ndash; Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="21d8f-146">标识&ndash;名称、 显示名称</span><span class="sxs-lookup"><span data-stu-id="21d8f-146">Identity &ndash; Name, display name</span></span>
* <span data-ttu-id="21d8f-147">配置设置</span><span class="sxs-lookup"><span data-stu-id="21d8f-147">Configuration settings</span></span>
* <span data-ttu-id="21d8f-148">环境变量</span><span class="sxs-lookup"><span data-stu-id="21d8f-148">Environment variables</span></span>

<span data-ttu-id="21d8f-149">将以下项放[中间件](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)代码的开头`Startup.Configure`方法的请求处理管道。</span><span class="sxs-lookup"><span data-stu-id="21d8f-149">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="21d8f-150">中间件运行以确保仅在开发环境中执行的代码之前，将检查该环境。</span><span class="sxs-lookup"><span data-stu-id="21d8f-150">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="21d8f-151">若要获取该环境，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="21d8f-151">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="21d8f-152">注入`IHostingEnvironment`到`Startup.Configure`方法，并检查本地变量的环境。</span><span class="sxs-lookup"><span data-stu-id="21d8f-152">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="21d8f-153">下面的示例代码演示了这种方法。</span><span class="sxs-lookup"><span data-stu-id="21d8f-153">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="21d8f-154">将在环境中的属性分配`Startup`类。</span><span class="sxs-lookup"><span data-stu-id="21d8f-154">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="21d8f-155">检查在环境中使用属性 (例如， `if (Environment.IsDevelopment())`)。</span><span class="sxs-lookup"><span data-stu-id="21d8f-155">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

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
