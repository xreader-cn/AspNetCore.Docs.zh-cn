---
title: 使用 HTTP REPL 测试 Web API
author: scottaddie
description: 了解如何使用 HTTP REPL .NET Core 全局工具来浏览和测试 ASP.NET Core Web API。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/25/2019
uid: web-api/http-repl
ms.openlocfilehash: d2c5f774595e7a2223e84cc76eecdb9baa04adfe
ms.sourcegitcommit: 776598f71da0d1e4c9e923b3b395d3c3b5825796
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2019
ms.locfileid: "70024807"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="4a3d6-103">使用 HTTP REPL 测试 Web API</span><span class="sxs-lookup"><span data-stu-id="4a3d6-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="4a3d6-104">作者：[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="4a3d6-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="4a3d6-105">HTTP 读取–求值–打印循环 (REPL)：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="4a3d6-106">一种轻量级跨平台命令行工具，在所有支持的 .NET Core 的位置都可得到支持。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="4a3d6-107">用于发出 HTTP 请求以测试 ASP.NET Core Web API（和非 ASP.NET Core web API）并查看其结果。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="4a3d6-108">可以在任何环境下测试托管的 web API，包括 localhost 和 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="4a3d6-109">支持以下 [HTTP 谓词](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="4a3d6-110">DELETE</span><span class="sxs-lookup"><span data-stu-id="4a3d6-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="4a3d6-111">GET</span><span class="sxs-lookup"><span data-stu-id="4a3d6-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="4a3d6-112">HEAD</span><span class="sxs-lookup"><span data-stu-id="4a3d6-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="4a3d6-113">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="4a3d6-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="4a3d6-114">PATCH</span><span class="sxs-lookup"><span data-stu-id="4a3d6-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="4a3d6-115">POST</span><span class="sxs-lookup"><span data-stu-id="4a3d6-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="4a3d6-116">PUT</span><span class="sxs-lookup"><span data-stu-id="4a3d6-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="4a3d6-117">若要继续操作，请[查看或下载示例 ASP.NET Core Web API](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples)（[下载方式](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4a3d6-118">系统必备</span><span class="sxs-lookup"><span data-stu-id="4a3d6-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="4a3d6-119">安装</span><span class="sxs-lookup"><span data-stu-id="4a3d6-119">Installation</span></span>

<span data-ttu-id="4a3d6-120">若要安装 HTTP REPL，运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-120">To install the HTTP REPL, run the following command:</span></span>

```console
dotnet tool install -g Microsoft.dotnet-httprepl --version "3.0.0-*"
```

<span data-ttu-id="4a3d6-121">从 [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet 包安装 [.NET Core 全局工具](/dotnet/core/tools/global-tools#install-a-global-tool)。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="4a3d6-122">用法</span><span class="sxs-lookup"><span data-stu-id="4a3d6-122">Usage</span></span>

<span data-ttu-id="4a3d6-123">成功安装该工具后，运行以下命令以启动 HTTP REPL：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-123">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
dotnet httprepl
```

<span data-ttu-id="4a3d6-124">若要查看可用的 HTTP REPL 命令，请运行下面的一个命令：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-124">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
dotnet httprepl -h
```

```console
dotnet httprepl --help
```

<span data-ttu-id="4a3d6-125">显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-125">The following output is displayed:</span></span>

```console
Usage:
  dotnet httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

Setup Commands:
Use these commands to configure the tool for your API server

connect        Configures the directory structure and base address of the api server
set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

set base       Set the base URI. e.g. `set base http://locahost:5000`
ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

<span data-ttu-id="4a3d6-126">HTTP REPL 提供命令完成。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-126">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="4a3d6-127">按 Tab <kbd></kbd>键可循环访问补全所键入字符或 API 终结点的命令的列表。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="4a3d6-128">以下部分概述了可用的 CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="4a3d6-129">连接到 Web API</span><span class="sxs-lookup"><span data-stu-id="4a3d6-129">Connect to the web API</span></span>

<span data-ttu-id="4a3d6-130">运行以下命令，连接到 Web API：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-130">Connect to a web API by running the following command:</span></span>

```console
dotnet httprepl <ROOT URI>
```

<span data-ttu-id="4a3d6-131">`<ROOT URI>` 是 Web API 的基 URI。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="4a3d6-132">例如:</span><span class="sxs-lookup"><span data-stu-id="4a3d6-132">For example:</span></span>

```console
dotnet httprepl https://localhost:5001
```

<span data-ttu-id="4a3d6-133">或者，在 HTTP REPL 运行期间的任何时刻运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-133">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="4a3d6-134">例如:</span><span class="sxs-lookup"><span data-stu-id="4a3d6-134">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001
```

## <a name="manually-point-to-the-swagger-document-for-the-web-api"></a><span data-ttu-id="4a3d6-135">手动指向 Web API 的 Swagger 文档</span><span class="sxs-lookup"><span data-stu-id="4a3d6-135">Manually point to the Swagger document for the web API</span></span>

<span data-ttu-id="4a3d6-136">上述 connect 命令将尝试自动查找 Swagger 文档。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-136">The connect command above will attempt to find the Swagger document automatically.</span></span> <span data-ttu-id="4a3d6-137">如果由于某种原因而无法执行此操作，则可以使用 `--swagger` 选项指定 Web API 的 Swagger 文档的 URI：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-137">If for some reason it is unable to do so, you can specify the URI of the Swagger document for the web API by using the `--swagger` option:</span></span>

```console
connect <ROOT URI> --swagger <SWAGGER URI>
```

<span data-ttu-id="4a3d6-138">例如:</span><span class="sxs-lookup"><span data-stu-id="4a3d6-138">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001 --swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="4a3d6-139">浏览 Web API</span><span class="sxs-lookup"><span data-stu-id="4a3d6-139">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="4a3d6-140">查看可用的终结点</span><span class="sxs-lookup"><span data-stu-id="4a3d6-140">View available endpoints</span></span>

<span data-ttu-id="4a3d6-141">若要在 Web API 地址的当前路径中列出不同的终结点（控制器），请运行 `ls` 或 `dir` 命令：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-141">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/~ ls
```

<span data-ttu-id="4a3d6-142">以下输出格式随即显示：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-142">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="4a3d6-143">上述输出指示有两个控制器可用：`Fruits` 和 `People`。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-143">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="4a3d6-144">这两个控制器都支持无参数 HTTP GET 和 POST 操作。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-144">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="4a3d6-145">导航到特定控制器可显示更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-145">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="4a3d6-146">例如，以下命令的输出显示 `Fruits` 控制器还支持 HTTP GET、PUT 和 DELETE 操作。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-146">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="4a3d6-147">其中每个操作都需要路由中有一个 `id` 参数：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-147">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

<span data-ttu-id="4a3d6-148">或者，运行 `ui` 命令，在浏览器中打开 Web API 的 Swagger UI 页。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-148">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="4a3d6-149">例如:</span><span class="sxs-lookup"><span data-stu-id="4a3d6-149">For example:</span></span>

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="4a3d6-150">导航到一个终结点</span><span class="sxs-lookup"><span data-stu-id="4a3d6-150">Navigate to an endpoint</span></span>

<span data-ttu-id="4a3d6-151">若要在 Web API 上导航到其他终结点，请运行 `cd` 命令：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-151">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/~ cd people
```

<span data-ttu-id="4a3d6-152">`cd` 命令后的路径不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-152">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="4a3d6-153">以下输出格式随即显示：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-153">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="4a3d6-154">自定义 HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="4a3d6-154">Customize the HTTP REPL</span></span>

<span data-ttu-id="4a3d6-155">可自定义 HTTP REPL 的默认[颜色](#set-color-preferences)。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-155">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="4a3d6-156">此外，还可定义[默认文本编辑器](#set-the-default-text-editor)。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-156">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="4a3d6-157">HTTP REPL 首选项在当前会话中保持不变，并且也会在之后的会话中使用。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-157">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="4a3d6-158">修改后，首选项存储在以下文件中：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-158">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="4a3d6-159">Linux</span><span class="sxs-lookup"><span data-stu-id="4a3d6-159">Linux</span></span>](#tab/linux)

<span data-ttu-id="4a3d6-160">%HOME%/.httpreplprefs </span><span class="sxs-lookup"><span data-stu-id="4a3d6-160">*%HOME%/.httpreplprefs*</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="4a3d6-161">macOS</span><span class="sxs-lookup"><span data-stu-id="4a3d6-161">macOS</span></span>](#tab/macos)

<span data-ttu-id="4a3d6-162">%HOME%/.httpreplprefs </span><span class="sxs-lookup"><span data-stu-id="4a3d6-162">*%HOME%/.httpreplprefs*</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="4a3d6-163">Windows</span><span class="sxs-lookup"><span data-stu-id="4a3d6-163">Windows</span></span>](#tab/windows)

<span data-ttu-id="4a3d6-164">%USERPROFILE%\\.httpreplprefs </span><span class="sxs-lookup"><span data-stu-id="4a3d6-164">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="4a3d6-165">.httpreplprefs  文件将在启动时加载，并且在运行时不监控对其的更改。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-165">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="4a3d6-166">对文件的手动修改只会在重启该工具后生效。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-166">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="4a3d6-167">查看设置</span><span class="sxs-lookup"><span data-stu-id="4a3d6-167">View the settings</span></span>

<span data-ttu-id="4a3d6-168">若要查看可用的设置，请运行 `pref get` 命令。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-168">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="4a3d6-169">例如:</span><span class="sxs-lookup"><span data-stu-id="4a3d6-169">For example:</span></span>

```console
https://localhost:5001/~ pref get
```

<span data-ttu-id="4a3d6-170">上述命令显示可用的键值对：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-170">The preceding command displays the available key-value pairs:</span></span>

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a><span data-ttu-id="4a3d6-171">设置颜色首选项</span><span class="sxs-lookup"><span data-stu-id="4a3d6-171">Set color preferences</span></span>

<span data-ttu-id="4a3d6-172">当前仅 JSON 支持响应着色。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-172">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="4a3d6-173">若要自定义默认的 HTTP REPL 工具着色，请找到与要更改的颜色相对应的键。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-173">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="4a3d6-174">有关如何查找键的说明，请参阅[查看设置](#view-the-settings)部分。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-174">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="4a3d6-175">例如，将 `colors.json` 键值从 `Green` 更改为 `White`如下所示：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-175">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people~ pref set colors.json White
```

<span data-ttu-id="4a3d6-176">只能使用[允许的颜色](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs)。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-176">Only the [allowed colors](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="4a3d6-177">后续 HTTP 请求使用新着色显示输出。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-177">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="4a3d6-178">如果未设置特定颜色键，则会考虑使用更通用的键。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-178">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="4a3d6-179">若要演示此回退行为，请考虑使用以下示例：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-179">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="4a3d6-180">如果 `colors.json.name` 没有值，则使用 `colors.json.string`。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-180">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="4a3d6-181">如果 `colors.json.string` 没有值，则使用 `colors.json.literal`。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-181">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="4a3d6-182">如果 `colors.json.literal` 没有值，则使用 `colors.json`。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-182">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="4a3d6-183">如果 `colors.json` 没有值，则使用命令行界面的默认文本颜色 (`AllowedColors.None`)。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-183">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="4a3d6-184">设置缩进尺寸</span><span class="sxs-lookup"><span data-stu-id="4a3d6-184">Set indentation size</span></span>

<span data-ttu-id="4a3d6-185">当前，仅 JSON 支持响应缩进尺寸自定义。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-185">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="4a3d6-186">默认尺寸为两个空格。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-186">The default size is two spaces.</span></span> <span data-ttu-id="4a3d6-187">例如:</span><span class="sxs-lookup"><span data-stu-id="4a3d6-187">For example:</span></span>

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

<span data-ttu-id="4a3d6-188">若要更改默认尺寸，请设置 `formatting.json.indentSize` 键。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-188">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="4a3d6-189">例如，始终使用四个空格：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-189">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="4a3d6-190">后续响应遵循四个空格的设置：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-190">Subsequent responses honor the setting of four spaces:</span></span>

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-indentation-size"></a><span data-ttu-id="4a3d6-191">设置缩进尺寸</span><span class="sxs-lookup"><span data-stu-id="4a3d6-191">Set indentation size</span></span>

<span data-ttu-id="4a3d6-192">当前，仅 JSON 支持响应缩进尺寸自定义。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-192">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="4a3d6-193">默认尺寸为两个空格。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-193">The default size is two spaces.</span></span> <span data-ttu-id="4a3d6-194">例如:</span><span class="sxs-lookup"><span data-stu-id="4a3d6-194">For example:</span></span>

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

<span data-ttu-id="4a3d6-195">若要更改默认尺寸，请设置 `formatting.json.indentSize` 键。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-195">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="4a3d6-196">例如，始终使用四个空格：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-196">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="4a3d6-197">后续响应遵循四个空格的设置：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-197">Subsequent responses honor the setting of four spaces:</span></span>

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a><span data-ttu-id="4a3d6-198">设置默认文本编辑器</span><span class="sxs-lookup"><span data-stu-id="4a3d6-198">Set the default text editor</span></span>

<span data-ttu-id="4a3d6-199">默认情况下，HTTP REPL 未配置任何文本编辑器供使用。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-199">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="4a3d6-200">若要测试需要 HTTP 请求正文的 Web API 方法，必须设置默认文本编辑器。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-200">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="4a3d6-201">HTTP REPL 工具将启动已配置的文本编辑器，专门用于编写请求正文。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-201">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="4a3d6-202">运行以下命令，将你青睐的文本编辑器设置为默认编辑器：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-202">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="4a3d6-203">在上述命令中，`<EXECUTABLE>` 是文本编辑器可执行文件的完整路径。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-203">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="4a3d6-204">例如，运行以下命令，将 Visual Studio Code 设置为默认文本编辑器：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-204">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="4a3d6-205">Linux</span><span class="sxs-lookup"><span data-stu-id="4a3d6-205">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macostabmacos"></a>[<span data-ttu-id="4a3d6-206">macOS</span><span class="sxs-lookup"><span data-stu-id="4a3d6-206">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windowstabwindows"></a>[<span data-ttu-id="4a3d6-207">Windows</span><span class="sxs-lookup"><span data-stu-id="4a3d6-207">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="4a3d6-208">若要使用特定 CLI 参数启动默认文本编辑器，请设置 `editor.command.default.arguments` 键。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-208">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="4a3d6-209">例如，假设 Visual Studio Code 为默认文本编辑器，并且你总是希望 HTTP REPL 在禁用了扩展的新会话中打开 Visual Studio Code。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-209">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="4a3d6-210">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-210">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

### <a name="set-the-swagger-search-paths"></a><span data-ttu-id="4a3d6-211">设置 Swagger 搜索路径</span><span class="sxs-lookup"><span data-stu-id="4a3d6-211">Set the Swagger search paths</span></span>

<span data-ttu-id="4a3d6-212">默认情况下，HTTP REPL 具有一组相对路径，可用于在不使用 `--swagger` 选项执行 `connect` 命令时查找 Swagger 文档。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-212">By default, the HTTP REPL has a set of relative paths that it uses to find the Swagger document when executing the `connect` command without the `--swagger` option.</span></span> <span data-ttu-id="4a3d6-213">将这些相对路径与 `connect` 命令中指定的根路径和基路径组合在一起。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-213">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="4a3d6-214">默认相对路径为：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-214">The default relative paths are:</span></span>

- <span data-ttu-id="4a3d6-215">*swagger.json*</span><span class="sxs-lookup"><span data-stu-id="4a3d6-215">*swagger.json*</span></span>
- <span data-ttu-id="4a3d6-216">*swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="4a3d6-216">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="4a3d6-217">*/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="4a3d6-217">*/swagger.json*</span></span>
- <span data-ttu-id="4a3d6-218">*/swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="4a3d6-218">*/swagger/v1/swagger.json*</span></span>

<span data-ttu-id="4a3d6-219">若要在环境中使用一组不同的搜索路径，请设置 `swagger.searchPaths` 首选项。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-219">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="4a3d6-220">该值必须是以竖线分隔的相对路径列表。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-220">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="4a3d6-221">例如:</span><span class="sxs-lookup"><span data-stu-id="4a3d6-221">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json
```

## <a name="test-http-get-requests"></a><span data-ttu-id="4a3d6-222">测试 HTTP GET 请求</span><span class="sxs-lookup"><span data-stu-id="4a3d6-222">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="4a3d6-223">摘要</span><span class="sxs-lookup"><span data-stu-id="4a3d6-223">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="4a3d6-224">自变量</span><span class="sxs-lookup"><span data-stu-id="4a3d6-224">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="4a3d6-225">关联控制器操作方法所需的路由参数（如果有）。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-225">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="4a3d6-226">选项</span><span class="sxs-lookup"><span data-stu-id="4a3d6-226">Options</span></span>

<span data-ttu-id="4a3d6-227">`get` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-227">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="4a3d6-228">示例</span><span class="sxs-lookup"><span data-stu-id="4a3d6-228">Example</span></span>

<span data-ttu-id="4a3d6-229">发出 HTTP GET 请求：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-229">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="4a3d6-230">在支持 `get` 命令的终结点上运行该命令：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-230">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ get
    ```

    <span data-ttu-id="4a3d6-231">上述命令显示以下输出格式：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-231">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people~
    ```

1. <span data-ttu-id="4a3d6-232">通过将参数传递给 `get` 命令来检索特定记录：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-232">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people~ get 2
    ```

    <span data-ttu-id="4a3d6-233">上述命令显示以下输出格式：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-233">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people~
    ```

## <a name="test-http-post-requests"></a><span data-ttu-id="4a3d6-234">测试 HTTP POST 请求</span><span class="sxs-lookup"><span data-stu-id="4a3d6-234">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="4a3d6-235">摘要</span><span class="sxs-lookup"><span data-stu-id="4a3d6-235">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="4a3d6-236">自变量</span><span class="sxs-lookup"><span data-stu-id="4a3d6-236">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="4a3d6-237">关联控制器操作方法所需的路由参数（如果有）。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-237">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="4a3d6-238">选项</span><span class="sxs-lookup"><span data-stu-id="4a3d6-238">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="4a3d6-239">示例</span><span class="sxs-lookup"><span data-stu-id="4a3d6-239">Example</span></span>

<span data-ttu-id="4a3d6-240">发出 HTTP POST 请求：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-240">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="4a3d6-241">在支持 `post` 命令的终结点上运行该命令：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-241">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    <span data-ttu-id="4a3d6-242">在上述命令中，`Content-Type` HTTP 请求标头被设置为指示 JSON 的请求正文媒体类型。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-242">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="4a3d6-243">默认文本编辑器打开一个 .tmp  文件，其中包含一个表示 HTTP 请求正文的 JSON 模板。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-243">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="4a3d6-244">例如:</span><span class="sxs-lookup"><span data-stu-id="4a3d6-244">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="4a3d6-245">若要设置默认文本编辑器，请参阅[设置默认文本编辑器](#set-the-default-text-editor)部分。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-245">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="4a3d6-246">修改 JSON 模板以满足模型验证要求：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-246">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="4a3d6-247">保存 .tmp  文件，并关闭文本编辑器。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-247">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="4a3d6-248">以下输出显示在命令行界面中：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-248">The following output appears in the command shell:</span></span>

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people~
    ```

## <a name="test-http-put-requests"></a><span data-ttu-id="4a3d6-249">测试 HTTP PUT 请求</span><span class="sxs-lookup"><span data-stu-id="4a3d6-249">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="4a3d6-250">摘要</span><span class="sxs-lookup"><span data-stu-id="4a3d6-250">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="4a3d6-251">自变量</span><span class="sxs-lookup"><span data-stu-id="4a3d6-251">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="4a3d6-252">关联控制器操作方法所需的路由参数（如果有）。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-252">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="4a3d6-253">选项</span><span class="sxs-lookup"><span data-stu-id="4a3d6-253">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="4a3d6-254">示例</span><span class="sxs-lookup"><span data-stu-id="4a3d6-254">Example</span></span>

<span data-ttu-id="4a3d6-255">发出 HTTP PUT 请求：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-255">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="4a3d6-256">*可选*：在修改之前，运行 `get` 命令以查看数据：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-256">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]

1. Run the `put` command on an endpoint that supports it:

    ```console
    https://localhost:5001/fruits~ put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="4a3d6-257">在上述命令中，`Content-Type` HTTP 请求标头被设置为指示 JSON 的请求正文媒体类型。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-257">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="4a3d6-258">默认文本编辑器打开一个 .tmp  文件，其中包含一个表示 HTTP 请求正文的 JSON 模板。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-258">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="4a3d6-259">例如:</span><span class="sxs-lookup"><span data-stu-id="4a3d6-259">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="4a3d6-260">若要设置默认文本编辑器，请参阅[设置默认文本编辑器](#set-the-default-text-editor)部分。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-260">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="4a3d6-261">修改 JSON 模板以满足模型验证要求：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-261">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="4a3d6-262">保存 .tmp  文件，并关闭文本编辑器。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-262">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="4a3d6-263">以下输出显示在命令行界面中：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-263">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="4a3d6-264">*可选*：发出 `get` 命令以查看修改。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-264">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="4a3d6-265">例如，如果在文本编辑器中键入“Cherry”，`get` 会返回以下内容：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-265">For example, if you typed "Cherry" in the text editor, a `get` returns the following:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-delete-requests"></a><span data-ttu-id="4a3d6-266">测试 HTTP DELETE 请求</span><span class="sxs-lookup"><span data-stu-id="4a3d6-266">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="4a3d6-267">摘要</span><span class="sxs-lookup"><span data-stu-id="4a3d6-267">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="4a3d6-268">自变量</span><span class="sxs-lookup"><span data-stu-id="4a3d6-268">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="4a3d6-269">关联控制器操作方法所需的路由参数（如果有）。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-269">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="4a3d6-270">选项</span><span class="sxs-lookup"><span data-stu-id="4a3d6-270">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="4a3d6-271">示例</span><span class="sxs-lookup"><span data-stu-id="4a3d6-271">Example</span></span>

<span data-ttu-id="4a3d6-272">发出 HTTP DELETE 请求：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-272">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="4a3d6-273">*可选*：在修改之前，运行 `get` 命令以查看数据：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-273">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]

1. Run the `delete` command on an endpoint that supports it:

    ```console
    https://localhost:5001/fruits~ delete 2
    ```

    <span data-ttu-id="4a3d6-274">上述命令显示以下输出格式：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-274">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="4a3d6-275">*可选*：发出 `get` 命令以查看修改。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-275">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="4a3d6-276">在此示例中，`get` 返回以下内容：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-276">In this example, a `get` returns the following:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-patch-requests"></a><span data-ttu-id="4a3d6-277">测试 HTTP PATCH 请求</span><span class="sxs-lookup"><span data-stu-id="4a3d6-277">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="4a3d6-278">摘要</span><span class="sxs-lookup"><span data-stu-id="4a3d6-278">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="4a3d6-279">自变量</span><span class="sxs-lookup"><span data-stu-id="4a3d6-279">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="4a3d6-280">关联控制器操作方法所需的路由参数（如果有）。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-280">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="4a3d6-281">选项</span><span class="sxs-lookup"><span data-stu-id="4a3d6-281">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="4a3d6-282">测试 HTTP HEAD 请求</span><span class="sxs-lookup"><span data-stu-id="4a3d6-282">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="4a3d6-283">摘要</span><span class="sxs-lookup"><span data-stu-id="4a3d6-283">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="4a3d6-284">自变量</span><span class="sxs-lookup"><span data-stu-id="4a3d6-284">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="4a3d6-285">关联控制器操作方法所需的路由参数（如果有）。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-285">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="4a3d6-286">选项</span><span class="sxs-lookup"><span data-stu-id="4a3d6-286">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="4a3d6-287">测试 HTTP OPTIONS 请求</span><span class="sxs-lookup"><span data-stu-id="4a3d6-287">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="4a3d6-288">摘要</span><span class="sxs-lookup"><span data-stu-id="4a3d6-288">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="4a3d6-289">自变量</span><span class="sxs-lookup"><span data-stu-id="4a3d6-289">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="4a3d6-290">关联控制器操作方法所需的路由参数（如果有）。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-290">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="4a3d6-291">选项</span><span class="sxs-lookup"><span data-stu-id="4a3d6-291">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="4a3d6-292">设置 HTTP 请求标头</span><span class="sxs-lookup"><span data-stu-id="4a3d6-292">Set HTTP request headers</span></span>

<span data-ttu-id="4a3d6-293">若要设置 HTTP 请求标头，请使用下面一种方法：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-293">To set an HTTP request header, use one of the following approaches:</span></span>

1. <span data-ttu-id="4a3d6-294">使用该 HTTP 请求进行内联设置。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-294">Set inline with the HTTP request.</span></span> <span data-ttu-id="4a3d6-295">例如:</span><span class="sxs-lookup"><span data-stu-id="4a3d6-295">For example:</span></span>

  ```console
  https://localhost:5001/people~ post -h Content-Type=application/json
  ```

  <span data-ttu-id="4a3d6-296">若使用上述方法，每个不同的 HTTP 请求标头都需要其自己的 `-h` 选项。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-296">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

1. <span data-ttu-id="4a3d6-297">在发送 HTTP 请求之前进行设置。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-297">Set before sending the HTTP request.</span></span> <span data-ttu-id="4a3d6-298">例如:</span><span class="sxs-lookup"><span data-stu-id="4a3d6-298">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type application/json
  ```

  <span data-ttu-id="4a3d6-299">在发送请求之前设置标头时，标头在命令行界面会话期间保持设置。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-299">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="4a3d6-300">若要清除标头，请提供一个空值。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-300">To clear the header, provide an empty value.</span></span> <span data-ttu-id="4a3d6-301">例如:</span><span class="sxs-lookup"><span data-stu-id="4a3d6-301">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type
  ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="4a3d6-302">切换 HTTP 请求显示</span><span class="sxs-lookup"><span data-stu-id="4a3d6-302">Toggle HTTP request display</span></span>

<span data-ttu-id="4a3d6-303">默认情况下，禁止显示正在发送的 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-303">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="4a3d6-304">可在命令行界面会话期间更改相应的设置。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-304">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="4a3d6-305">启用请求显示</span><span class="sxs-lookup"><span data-stu-id="4a3d6-305">Enable request display</span></span>

<span data-ttu-id="4a3d6-306">运行 `echo on` 命令可查看正在发送的 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-306">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="4a3d6-307">例如:</span><span class="sxs-lookup"><span data-stu-id="4a3d6-307">For example:</span></span>

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

<span data-ttu-id="4a3d6-308">当前会话中的后续 HTTP 请求显示请求标头。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-308">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="4a3d6-309">例如:</span><span class="sxs-lookup"><span data-stu-id="4a3d6-309">For example:</span></span>

```console
https://localhost:5001/people~ post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people~
```

### <a name="disable-request-display"></a><span data-ttu-id="4a3d6-310">禁用请求显示</span><span class="sxs-lookup"><span data-stu-id="4a3d6-310">Disable request display</span></span>

<span data-ttu-id="4a3d6-311">运行 `echo off` 命令可禁止显示正在发送的 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-311">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="4a3d6-312">例如:</span><span class="sxs-lookup"><span data-stu-id="4a3d6-312">For example:</span></span>

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="4a3d6-313">运行脚本</span><span class="sxs-lookup"><span data-stu-id="4a3d6-313">Run a script</span></span>

<span data-ttu-id="4a3d6-314">如果经常执行一组相同的 HTTP REPL 命令，请考虑将它们存储在一个文本文件中。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-314">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="4a3d6-315">文件中的命令采用与在命令行上手动执行的命令相同的形式。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-315">Commands in the file take the same form as those executed manually on the command line.</span></span> <span data-ttu-id="4a3d6-316">可使用 `run` 命令批量执行这些命令。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-316">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="4a3d6-317">例如:</span><span class="sxs-lookup"><span data-stu-id="4a3d6-317">For example:</span></span>

1. <span data-ttu-id="4a3d6-318">创建一个文本文件，其中包含一组换行符分隔的命令。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-318">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="4a3d6-319">例如，一个包含以下命令的 people-script.txt  文件：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-319">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="4a3d6-320">执行 `run` 命令，传入文本文件的路径。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-320">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="4a3d6-321">例如:</span><span class="sxs-lookup"><span data-stu-id="4a3d6-321">For example:</span></span>

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="4a3d6-322">将显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-322">The following output appears:</span></span>

    ```console
    https://localhost:5001/~ set base https://localhost:5001
    Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/~ ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/~ cd People
    /People    [get|post]
    
    https://localhost:5001/People~ ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People~ get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People~
    ```

## <a name="clear-the-output"></a><span data-ttu-id="4a3d6-323">清除输出</span><span class="sxs-lookup"><span data-stu-id="4a3d6-323">Clear the output</span></span>

<span data-ttu-id="4a3d6-324">若要删除 HTTP REPL 工具写入命令行界面的所有输出，请运行 `clear` 或 `cls` 命令。</span><span class="sxs-lookup"><span data-stu-id="4a3d6-324">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="4a3d6-325">例如，假设命令行界面包含以下输出：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-325">To illustrate, imagine the command shell contains the following output:</span></span>

```console
dotnet httprepl https://localhost:5001
(Disconnected)~ set base "https://localhost:5001"
Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/~ ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="4a3d6-326">运行以下命令以清除输出：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-326">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/~ clear
```

<span data-ttu-id="4a3d6-327">运行上述命令后，命令行界面仅包含以下输出：</span><span class="sxs-lookup"><span data-stu-id="4a3d6-327">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a><span data-ttu-id="4a3d6-328">其他资源</span><span class="sxs-lookup"><span data-stu-id="4a3d6-328">Additional resources</span></span>

* [<span data-ttu-id="4a3d6-329">REST API 请求</span><span class="sxs-lookup"><span data-stu-id="4a3d6-329">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="4a3d6-330">HTTP REPL GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="4a3d6-330">HTTP REPL GitHub repository</span></span>](https://github.com/aspnet/HttpRepl)
