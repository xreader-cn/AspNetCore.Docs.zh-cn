---
title: 使用 HTTP REPL 测试 Web API
author: scottaddie
description: 了解如何使用 HTTP REPL .NET Core 全局工具来浏览和测试 ASP.NET Core Web API。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, devx-track-azurecli
ms.date: 11/10/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: web-api/http-repl
ms.openlocfilehash: 81174b551c5b6d81e6ac80975f7f77ee6664059d
ms.sourcegitcommit: fb72e9c1ae5b279817f1fb4b46a52170449b6f30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "94501984"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="897d7-103">使用 HTTP REPL 测试 Web API</span><span class="sxs-lookup"><span data-stu-id="897d7-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="897d7-104">作者：[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="897d7-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="897d7-105">HTTP 读取–求值–打印循环 (REPL)：</span><span class="sxs-lookup"><span data-stu-id="897d7-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="897d7-106">一种轻量级跨平台命令行工具，在所有支持的 .NET Core 的位置都可得到支持。</span><span class="sxs-lookup"><span data-stu-id="897d7-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="897d7-107">用于发出 HTTP 请求以测试 ASP.NET Core Web API（和非 ASP.NET Core web API）并查看其结果。</span><span class="sxs-lookup"><span data-stu-id="897d7-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="897d7-108">可以在任何环境下测试托管的 web API，包括 localhost 和 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="897d7-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="897d7-109">支持以下 [HTTP 谓词](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)：</span><span class="sxs-lookup"><span data-stu-id="897d7-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="897d7-110">DELETE</span><span class="sxs-lookup"><span data-stu-id="897d7-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="897d7-111">GET</span><span class="sxs-lookup"><span data-stu-id="897d7-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="897d7-112">HEAD</span><span class="sxs-lookup"><span data-stu-id="897d7-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="897d7-113">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="897d7-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="897d7-114">PATCH</span><span class="sxs-lookup"><span data-stu-id="897d7-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="897d7-115">POST</span><span class="sxs-lookup"><span data-stu-id="897d7-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="897d7-116">PUT</span><span class="sxs-lookup"><span data-stu-id="897d7-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="897d7-117">若要继续操作，请[查看或下载示例 ASP.NET Core Web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples)（[下载方式](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="897d7-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="897d7-118">先决条件</span><span class="sxs-lookup"><span data-stu-id="897d7-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="897d7-119">安装</span><span class="sxs-lookup"><span data-stu-id="897d7-119">Installation</span></span>

<span data-ttu-id="897d7-120">若要安装 HTTP REPL，运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="897d7-120">To install the HTTP REPL, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

<span data-ttu-id="897d7-121">从 [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet 包安装 [.NET Core 全局工具](/dotnet/core/tools/global-tools#install-a-global-tool)。</span><span class="sxs-lookup"><span data-stu-id="897d7-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="897d7-122">使用情况</span><span class="sxs-lookup"><span data-stu-id="897d7-122">Usage</span></span>

<span data-ttu-id="897d7-123">成功安装该工具后，运行以下命令以启动 HTTP REPL：</span><span class="sxs-lookup"><span data-stu-id="897d7-123">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
httprepl
```

<span data-ttu-id="897d7-124">若要查看可用的 HTTP REPL 命令，请运行下面的一个命令：</span><span class="sxs-lookup"><span data-stu-id="897d7-124">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
httprepl -h
```

```console
httprepl --help
```

<span data-ttu-id="897d7-125">显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="897d7-125">The following output is displayed:</span></span>

```console
Usage:
  httprepl [<BASE_ADDRESS>] [options]

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

<span data-ttu-id="897d7-126">HTTP REPL 提供命令完成。</span><span class="sxs-lookup"><span data-stu-id="897d7-126">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="897d7-127">按 Tab <kbd></kbd>键可循环访问补全所键入字符或 API 终结点的命令的列表。</span><span class="sxs-lookup"><span data-stu-id="897d7-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="897d7-128">以下部分概述了可用的 CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="897d7-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="897d7-129">连接到 Web API</span><span class="sxs-lookup"><span data-stu-id="897d7-129">Connect to the web API</span></span>

<span data-ttu-id="897d7-130">运行以下命令，连接到 Web API：</span><span class="sxs-lookup"><span data-stu-id="897d7-130">Connect to a web API by running the following command:</span></span>

```console
httprepl <ROOT URI>
```

<span data-ttu-id="897d7-131">`<ROOT URI>` 是 Web API 的基 URI。</span><span class="sxs-lookup"><span data-stu-id="897d7-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="897d7-132">例如：</span><span class="sxs-lookup"><span data-stu-id="897d7-132">For example:</span></span>

```console
httprepl https://localhost:5001
```

<span data-ttu-id="897d7-133">或者，在 HTTP REPL 运行期间的任何时刻运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="897d7-133">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="897d7-134">例如：</span><span class="sxs-lookup"><span data-stu-id="897d7-134">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001
```

### <a name="manually-point-to-the-openapi-description-for-the-web-api"></a><span data-ttu-id="897d7-135">手动指向 Web API 的 OpenAPI 说明</span><span class="sxs-lookup"><span data-stu-id="897d7-135">Manually point to the OpenAPI description for the web API</span></span>

<span data-ttu-id="897d7-136">上述 connect 命令将尝试自动查找 OpenAPI 说明。</span><span class="sxs-lookup"><span data-stu-id="897d7-136">The connect command above will attempt to find the OpenAPI description automatically.</span></span> <span data-ttu-id="897d7-137">如果由于某种原因而无法执行此操作，则可以使用 `--openapi` 选项指定 Web API 的 OpenAPI 说明的 URI：</span><span class="sxs-lookup"><span data-stu-id="897d7-137">If for some reason it's unable to do so, you can specify the URI of the OpenAPI description for the web API by using the `--openapi` option:</span></span>

```console
connect <ROOT URI> --openapi <OPENAPI DESCRIPTION ADDRESS>
```

<span data-ttu-id="897d7-138">例如：</span><span class="sxs-lookup"><span data-stu-id="897d7-138">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --openapi /swagger/v1/swagger.json
```

### <a name="enable-verbose-output-for-details-on-openapi-description-searching-parsing-and-validation"></a><span data-ttu-id="897d7-139">启用详细输出，以获取有关 OpenAPI 说明搜索、分析和验证的详细信息</span><span class="sxs-lookup"><span data-stu-id="897d7-139">Enable verbose output for details on OpenAPI description searching, parsing, and validation</span></span>

<span data-ttu-id="897d7-140">当工具搜索 OpenAPI 说明并对其进行分析和验证时，通过 `connect` 命令指定 `--verbose` 选项将生成更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="897d7-140">Specifying the `--verbose` option with the `connect` command will produce more details when the tool searches for the OpenAPI description, parses, and validates it.</span></span>

```console
connect <ROOT URI> --verbose
```

<span data-ttu-id="897d7-141">例如：</span><span class="sxs-lookup"><span data-stu-id="897d7-141">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --verbose
Checking https://localhost:5001/swagger.json... 404 NotFound
Checking https://localhost:5001/swagger/v1/swagger.json... 404 NotFound
Checking https://localhost:5001/openapi.json... Found
Parsing... Successful (with warnings)
The field 'info' in 'document' object is REQUIRED [#/info]
The field 'paths' in 'document' object is REQUIRED [#/paths]
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="897d7-142">浏览 Web API</span><span class="sxs-lookup"><span data-stu-id="897d7-142">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="897d7-143">查看可用的终结点</span><span class="sxs-lookup"><span data-stu-id="897d7-143">View available endpoints</span></span>

<span data-ttu-id="897d7-144">若要在 Web API 地址的当前路径中列出不同的终结点（控制器），请运行 `ls` 或 `dir` 命令：</span><span class="sxs-lookup"><span data-stu-id="897d7-144">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/> ls
```

<span data-ttu-id="897d7-145">以下输出格式随即显示：</span><span class="sxs-lookup"><span data-stu-id="897d7-145">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

<span data-ttu-id="897d7-146">上述输出指示有两个控制器可用：`Fruits` 和 `People`。</span><span class="sxs-lookup"><span data-stu-id="897d7-146">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="897d7-147">这两个控制器都支持无参数 HTTP GET 和 POST 操作。</span><span class="sxs-lookup"><span data-stu-id="897d7-147">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="897d7-148">导航到特定控制器可显示更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="897d7-148">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="897d7-149">例如，以下命令的输出显示 `Fruits` 控制器还支持 HTTP GET、PUT 和 DELETE 操作。</span><span class="sxs-lookup"><span data-stu-id="897d7-149">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="897d7-150">其中每个操作都需要路由中有一个 `id` 参数：</span><span class="sxs-lookup"><span data-stu-id="897d7-150">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits> ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits>
```

<span data-ttu-id="897d7-151">或者，运行 `ui` 命令，在浏览器中打开 Web API 的 Swagger UI 页。</span><span class="sxs-lookup"><span data-stu-id="897d7-151">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="897d7-152">例如：</span><span class="sxs-lookup"><span data-stu-id="897d7-152">For example:</span></span>

```console
https://localhost:5001/> ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="897d7-153">导航到一个终结点</span><span class="sxs-lookup"><span data-stu-id="897d7-153">Navigate to an endpoint</span></span>

<span data-ttu-id="897d7-154">若要在 Web API 上导航到其他终结点，请运行 `cd` 命令：</span><span class="sxs-lookup"><span data-stu-id="897d7-154">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/> cd people
```

<span data-ttu-id="897d7-155">`cd` 命令后的路径不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="897d7-155">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="897d7-156">以下输出格式随即显示：</span><span class="sxs-lookup"><span data-stu-id="897d7-156">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people>
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="897d7-157">自定义 HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="897d7-157">Customize the HTTP REPL</span></span>

<span data-ttu-id="897d7-158">可自定义 HTTP REPL 的默认[颜色](#set-color-preferences)。</span><span class="sxs-lookup"><span data-stu-id="897d7-158">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="897d7-159">此外，还可定义[默认文本编辑器](#set-the-default-text-editor)。</span><span class="sxs-lookup"><span data-stu-id="897d7-159">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="897d7-160">HTTP REPL 首选项在当前会话中保持不变，并且也会在之后的会话中使用。</span><span class="sxs-lookup"><span data-stu-id="897d7-160">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="897d7-161">修改后，首选项存储在以下文件中：</span><span class="sxs-lookup"><span data-stu-id="897d7-161">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linux"></a>[<span data-ttu-id="897d7-162">Linux</span><span class="sxs-lookup"><span data-stu-id="897d7-162">Linux</span></span>](#tab/linux)

<span data-ttu-id="897d7-163">%HOME%/.httpreplprefs</span><span class="sxs-lookup"><span data-stu-id="897d7-163">*%HOME%/.httpreplprefs*</span></span>

# <a name="macos"></a>[<span data-ttu-id="897d7-164">macOS</span><span class="sxs-lookup"><span data-stu-id="897d7-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="897d7-165">%HOME%/.httpreplprefs</span><span class="sxs-lookup"><span data-stu-id="897d7-165">*%HOME%/.httpreplprefs*</span></span>

# <a name="windows"></a>[<span data-ttu-id="897d7-166">Windows</span><span class="sxs-lookup"><span data-stu-id="897d7-166">Windows</span></span>](#tab/windows)

<span data-ttu-id="897d7-167">%USERPROFILE%\\.httpreplprefs</span><span class="sxs-lookup"><span data-stu-id="897d7-167">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="897d7-168">.httpreplprefs 文件将在启动时加载，并且在运行时不监控对其的更改。</span><span class="sxs-lookup"><span data-stu-id="897d7-168">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="897d7-169">对文件的手动修改只会在重启该工具后生效。</span><span class="sxs-lookup"><span data-stu-id="897d7-169">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="897d7-170">查看设置</span><span class="sxs-lookup"><span data-stu-id="897d7-170">View the settings</span></span>

<span data-ttu-id="897d7-171">若要查看可用的设置，请运行 `pref get` 命令。</span><span class="sxs-lookup"><span data-stu-id="897d7-171">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="897d7-172">例如：</span><span class="sxs-lookup"><span data-stu-id="897d7-172">For example:</span></span>

```console
https://localhost:5001/> pref get
```

<span data-ttu-id="897d7-173">上述命令显示可用的键值对：</span><span class="sxs-lookup"><span data-stu-id="897d7-173">The preceding command displays the available key-value pairs:</span></span>

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

### <a name="set-color-preferences"></a><span data-ttu-id="897d7-174">设置颜色首选项</span><span class="sxs-lookup"><span data-stu-id="897d7-174">Set color preferences</span></span>

<span data-ttu-id="897d7-175">当前仅 JSON 支持响应着色。</span><span class="sxs-lookup"><span data-stu-id="897d7-175">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="897d7-176">若要自定义默认的 HTTP REPL 工具着色，请找到与要更改的颜色相对应的键。</span><span class="sxs-lookup"><span data-stu-id="897d7-176">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="897d7-177">有关如何查找键的说明，请参阅[查看设置](#view-the-settings)部分。</span><span class="sxs-lookup"><span data-stu-id="897d7-177">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="897d7-178">例如，将 `colors.json` 键值从 `Green` 更改为 `White`如下所示：</span><span class="sxs-lookup"><span data-stu-id="897d7-178">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people> pref set colors.json White
```

<span data-ttu-id="897d7-179">只能使用[允许的颜色](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs)。</span><span class="sxs-lookup"><span data-stu-id="897d7-179">Only the [allowed colors](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="897d7-180">后续 HTTP 请求使用新着色显示输出。</span><span class="sxs-lookup"><span data-stu-id="897d7-180">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="897d7-181">如果未设置特定颜色键，则会考虑使用更通用的键。</span><span class="sxs-lookup"><span data-stu-id="897d7-181">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="897d7-182">若要演示此回退行为，请考虑使用以下示例：</span><span class="sxs-lookup"><span data-stu-id="897d7-182">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="897d7-183">如果 `colors.json.name` 没有值，则使用 `colors.json.string`。</span><span class="sxs-lookup"><span data-stu-id="897d7-183">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="897d7-184">如果 `colors.json.string` 没有值，则使用 `colors.json.literal`。</span><span class="sxs-lookup"><span data-stu-id="897d7-184">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="897d7-185">如果 `colors.json.literal` 没有值，则使用 `colors.json`。</span><span class="sxs-lookup"><span data-stu-id="897d7-185">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="897d7-186">如果 `colors.json` 没有值，则使用命令行界面的默认文本颜色 (`AllowedColors.None`)。</span><span class="sxs-lookup"><span data-stu-id="897d7-186">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="897d7-187">设置缩进尺寸</span><span class="sxs-lookup"><span data-stu-id="897d7-187">Set indentation size</span></span>

<span data-ttu-id="897d7-188">当前，仅 JSON 支持响应缩进尺寸自定义。</span><span class="sxs-lookup"><span data-stu-id="897d7-188">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="897d7-189">默认尺寸为两个空格。</span><span class="sxs-lookup"><span data-stu-id="897d7-189">The default size is two spaces.</span></span> <span data-ttu-id="897d7-190">例如：</span><span class="sxs-lookup"><span data-stu-id="897d7-190">For example:</span></span>

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

<span data-ttu-id="897d7-191">若要更改默认尺寸，请设置 `formatting.json.indentSize` 键。</span><span class="sxs-lookup"><span data-stu-id="897d7-191">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="897d7-192">例如，始终使用四个空格：</span><span class="sxs-lookup"><span data-stu-id="897d7-192">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="897d7-193">后续响应遵循四个空格的设置：</span><span class="sxs-lookup"><span data-stu-id="897d7-193">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-the-default-text-editor"></a><span data-ttu-id="897d7-194">设置默认文本编辑器</span><span class="sxs-lookup"><span data-stu-id="897d7-194">Set the default text editor</span></span>

<span data-ttu-id="897d7-195">默认情况下，HTTP REPL 未配置任何文本编辑器供使用。</span><span class="sxs-lookup"><span data-stu-id="897d7-195">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="897d7-196">若要测试需要 HTTP 请求正文的 Web API 方法，必须设置默认文本编辑器。</span><span class="sxs-lookup"><span data-stu-id="897d7-196">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="897d7-197">HTTP REPL 工具将启动已配置的文本编辑器，专门用于编写请求正文。</span><span class="sxs-lookup"><span data-stu-id="897d7-197">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="897d7-198">运行以下命令，将你青睐的文本编辑器设置为默认编辑器：</span><span class="sxs-lookup"><span data-stu-id="897d7-198">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="897d7-199">在上述命令中，`<EXECUTABLE>` 是文本编辑器可执行文件的完整路径。</span><span class="sxs-lookup"><span data-stu-id="897d7-199">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="897d7-200">例如，运行以下命令，将 Visual Studio Code 设置为默认文本编辑器：</span><span class="sxs-lookup"><span data-stu-id="897d7-200">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linux"></a>[<span data-ttu-id="897d7-201">Linux</span><span class="sxs-lookup"><span data-stu-id="897d7-201">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[<span data-ttu-id="897d7-202">macOS</span><span class="sxs-lookup"><span data-stu-id="897d7-202">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[<span data-ttu-id="897d7-203">Windows</span><span class="sxs-lookup"><span data-stu-id="897d7-203">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="897d7-204">若要使用特定 CLI 参数启动默认文本编辑器，请设置 `editor.command.default.arguments` 键。</span><span class="sxs-lookup"><span data-stu-id="897d7-204">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="897d7-205">例如，假设 Visual Studio Code 为默认文本编辑器，并且你总是希望 HTTP REPL 在禁用了扩展的新会话中打开 Visual Studio Code。</span><span class="sxs-lookup"><span data-stu-id="897d7-205">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="897d7-206">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="897d7-206">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

> [!TIP]
> <span data-ttu-id="897d7-207">如果默认编辑器是 Visual Studio Code，则通常需要传递 `-w` 或 `--wait` 参数以强制 Visual Studio Code 等待你关闭文件，然后再返回。</span><span class="sxs-lookup"><span data-stu-id="897d7-207">If your default editor is Visual Studio Code, you'll usually want to pass the `-w` or `--wait` argument to force Visual Studio Code to wait for you to close the file before returning.</span></span>

### <a name="set-the-openapi-description-search-paths"></a><span data-ttu-id="897d7-208">设置 OpenAPI 说明搜索路径</span><span class="sxs-lookup"><span data-stu-id="897d7-208">Set the OpenAPI Description search paths</span></span>

<span data-ttu-id="897d7-209">默认情况下，HTTP REPL 具有一组相对路径，可用于在不使用 `--openapi` 选项执行 `connect` 命令时查找 OpenAPI 说明。</span><span class="sxs-lookup"><span data-stu-id="897d7-209">By default, the HTTP REPL has a set of relative paths that it uses to find the OpenAPI description when executing the `connect` command without the `--openapi` option.</span></span> <span data-ttu-id="897d7-210">将这些相对路径与 `connect` 命令中指定的根路径和基路径组合在一起。</span><span class="sxs-lookup"><span data-stu-id="897d7-210">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="897d7-211">默认相对路径为：</span><span class="sxs-lookup"><span data-stu-id="897d7-211">The default relative paths are:</span></span>

- <span data-ttu-id="897d7-212">*swagger.json*</span><span class="sxs-lookup"><span data-stu-id="897d7-212">*swagger.json*</span></span>
- <span data-ttu-id="897d7-213">*swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="897d7-213">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="897d7-214">*/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="897d7-214">*/swagger.json*</span></span>
- <span data-ttu-id="897d7-215">*/swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="897d7-215">*/swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="897d7-216">openapi.json</span><span class="sxs-lookup"><span data-stu-id="897d7-216">*openapi.json*</span></span>
- <span data-ttu-id="897d7-217">/openapi.json</span><span class="sxs-lookup"><span data-stu-id="897d7-217">*/openapi.json*</span></span>

<span data-ttu-id="897d7-218">若要在环境中使用一组不同的搜索路径，请设置 `swagger.searchPaths` 首选项。</span><span class="sxs-lookup"><span data-stu-id="897d7-218">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="897d7-219">该值必须是以竖线分隔的相对路径列表。</span><span class="sxs-lookup"><span data-stu-id="897d7-219">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="897d7-220">例如：</span><span class="sxs-lookup"><span data-stu-id="897d7-220">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

<span data-ttu-id="897d7-221">除了替换默认列表外，还可以通过添加或删除路径来修改列表。</span><span class="sxs-lookup"><span data-stu-id="897d7-221">Instead of replacing the default list altogether, the list can also be modified by adding or removing paths.</span></span>

<span data-ttu-id="897d7-222">若要将一个或多个搜索路径添加到默认列表，请设置 `swagger.addToSearchPaths` 首选项。</span><span class="sxs-lookup"><span data-stu-id="897d7-222">To add one or more search paths to the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="897d7-223">该值必须是以竖线分隔的相对路径列表。</span><span class="sxs-lookup"><span data-stu-id="897d7-223">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="897d7-224">例如：</span><span class="sxs-lookup"><span data-stu-id="897d7-224">For example:</span></span>

```console
pref set swagger.addToSearchPaths "openapi/v2/openapi.json|openapi/v3/openapi.json"
```

<span data-ttu-id="897d7-225">若要从默认列表中删除一个或多个搜索路径，请设置 `swagger.addToSearchPaths` 首选项。</span><span class="sxs-lookup"><span data-stu-id="897d7-225">To remove one or more search paths from the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="897d7-226">该值必须是以竖线分隔的相对路径列表。</span><span class="sxs-lookup"><span data-stu-id="897d7-226">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="897d7-227">例如：</span><span class="sxs-lookup"><span data-stu-id="897d7-227">For example:</span></span>

```console
pref set swagger.removeFromSearchPaths "swagger.json|/swagger.json"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="897d7-228">测试 HTTP GET 请求</span><span class="sxs-lookup"><span data-stu-id="897d7-228">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="897d7-229">摘要</span><span class="sxs-lookup"><span data-stu-id="897d7-229">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="897d7-230">自变量</span><span class="sxs-lookup"><span data-stu-id="897d7-230">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="897d7-231">关联控制器操作方法所需的路由参数（如果有）。</span><span class="sxs-lookup"><span data-stu-id="897d7-231">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="897d7-232">选项</span><span class="sxs-lookup"><span data-stu-id="897d7-232">Options</span></span>

<span data-ttu-id="897d7-233">`get` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="897d7-233">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="897d7-234">示例</span><span class="sxs-lookup"><span data-stu-id="897d7-234">Example</span></span>

<span data-ttu-id="897d7-235">发出 HTTP GET 请求：</span><span class="sxs-lookup"><span data-stu-id="897d7-235">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="897d7-236">在支持 `get` 命令的终结点上运行该命令：</span><span class="sxs-lookup"><span data-stu-id="897d7-236">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> get
    ```

    <span data-ttu-id="897d7-237">上述命令显示以下输出格式：</span><span class="sxs-lookup"><span data-stu-id="897d7-237">The preceding command displays the following output format:</span></span>

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


    https://localhost:5001/people>
    ```

1. <span data-ttu-id="897d7-238">通过将参数传递给 `get` 命令来检索特定记录：</span><span class="sxs-lookup"><span data-stu-id="897d7-238">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people> get 2
    ```

    <span data-ttu-id="897d7-239">上述命令显示以下输出格式：</span><span class="sxs-lookup"><span data-stu-id="897d7-239">The preceding command displays the following output format:</span></span>

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


    https://localhost:5001/people>
    ```

## <a name="test-http-post-requests"></a><span data-ttu-id="897d7-240">测试 HTTP POST 请求</span><span class="sxs-lookup"><span data-stu-id="897d7-240">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="897d7-241">摘要</span><span class="sxs-lookup"><span data-stu-id="897d7-241">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="897d7-242">自变量</span><span class="sxs-lookup"><span data-stu-id="897d7-242">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="897d7-243">关联控制器操作方法所需的路由参数（如果有）。</span><span class="sxs-lookup"><span data-stu-id="897d7-243">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="897d7-244">选项</span><span class="sxs-lookup"><span data-stu-id="897d7-244">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="897d7-245">示例</span><span class="sxs-lookup"><span data-stu-id="897d7-245">Example</span></span>

<span data-ttu-id="897d7-246">发出 HTTP POST 请求：</span><span class="sxs-lookup"><span data-stu-id="897d7-246">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="897d7-247">在支持 `post` 命令的终结点上运行该命令：</span><span class="sxs-lookup"><span data-stu-id="897d7-247">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```

    <span data-ttu-id="897d7-248">在上述命令中，`Content-Type` HTTP 请求标头被设置为指示 JSON 的请求正文媒体类型。</span><span class="sxs-lookup"><span data-stu-id="897d7-248">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="897d7-249">默认文本编辑器打开一个 .tmp 文件，其中包含一个表示 HTTP 请求正文的 JSON 模板。</span><span class="sxs-lookup"><span data-stu-id="897d7-249">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="897d7-250">例如：</span><span class="sxs-lookup"><span data-stu-id="897d7-250">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="897d7-251">若要设置默认文本编辑器，请参阅[设置默认文本编辑器](#set-the-default-text-editor)部分。</span><span class="sxs-lookup"><span data-stu-id="897d7-251">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="897d7-252">修改 JSON 模板以满足模型验证要求：</span><span class="sxs-lookup"><span data-stu-id="897d7-252">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="897d7-253">保存 .tmp 文件，并关闭文本编辑器。</span><span class="sxs-lookup"><span data-stu-id="897d7-253">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="897d7-254">以下输出显示在命令行界面中：</span><span class="sxs-lookup"><span data-stu-id="897d7-254">The following output appears in the command shell:</span></span>

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


    https://localhost:5001/people>
    ```

## <a name="test-http-put-requests"></a><span data-ttu-id="897d7-255">测试 HTTP PUT 请求</span><span class="sxs-lookup"><span data-stu-id="897d7-255">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="897d7-256">摘要</span><span class="sxs-lookup"><span data-stu-id="897d7-256">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="897d7-257">自变量</span><span class="sxs-lookup"><span data-stu-id="897d7-257">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="897d7-258">关联控制器操作方法所需的路由参数（如果有）。</span><span class="sxs-lookup"><span data-stu-id="897d7-258">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="897d7-259">选项</span><span class="sxs-lookup"><span data-stu-id="897d7-259">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="897d7-260">示例</span><span class="sxs-lookup"><span data-stu-id="897d7-260">Example</span></span>

<span data-ttu-id="897d7-261">发出 HTTP PUT 请求：</span><span class="sxs-lookup"><span data-stu-id="897d7-261">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="897d7-262">*可选* ：在修改之前，运行 `get` 命令以查看数据：</span><span class="sxs-lookup"><span data-stu-id="897d7-262">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits> get
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
    ```

1. <span data-ttu-id="897d7-263">在支持 `put` 命令的终结点上运行该命令：</span><span class="sxs-lookup"><span data-stu-id="897d7-263">Run the `put` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="897d7-264">在上述命令中，`Content-Type` HTTP 请求标头被设置为指示 JSON 的请求正文媒体类型。</span><span class="sxs-lookup"><span data-stu-id="897d7-264">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="897d7-265">默认文本编辑器打开一个 .tmp 文件，其中包含一个表示 HTTP 请求正文的 JSON 模板。</span><span class="sxs-lookup"><span data-stu-id="897d7-265">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="897d7-266">例如：</span><span class="sxs-lookup"><span data-stu-id="897d7-266">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="897d7-267">若要设置默认文本编辑器，请参阅[设置默认文本编辑器](#set-the-default-text-editor)部分。</span><span class="sxs-lookup"><span data-stu-id="897d7-267">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="897d7-268">修改 JSON 模板以满足模型验证要求：</span><span class="sxs-lookup"><span data-stu-id="897d7-268">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="897d7-269">保存 .tmp 文件，并关闭文本编辑器。</span><span class="sxs-lookup"><span data-stu-id="897d7-269">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="897d7-270">以下输出显示在命令行界面中：</span><span class="sxs-lookup"><span data-stu-id="897d7-270">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="897d7-271">*可选* ：发出 `get` 命令以查看修改。</span><span class="sxs-lookup"><span data-stu-id="897d7-271">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="897d7-272">例如，如果在文本编辑器中键入了“Cherry”，则 `get` 会返回以下输出：</span><span class="sxs-lookup"><span data-stu-id="897d7-272">For example, if you typed "Cherry" in the text editor, a `get` returns the following output:</span></span>

    ```console
    https://localhost:5001/fruits> get
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


    https://localhost:5001/fruits>
    ```

## <a name="test-http-delete-requests"></a><span data-ttu-id="897d7-273">测试 HTTP DELETE 请求</span><span class="sxs-lookup"><span data-stu-id="897d7-273">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="897d7-274">摘要</span><span class="sxs-lookup"><span data-stu-id="897d7-274">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="897d7-275">自变量</span><span class="sxs-lookup"><span data-stu-id="897d7-275">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="897d7-276">关联控制器操作方法所需的路由参数（如果有）。</span><span class="sxs-lookup"><span data-stu-id="897d7-276">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="897d7-277">选项</span><span class="sxs-lookup"><span data-stu-id="897d7-277">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="897d7-278">示例</span><span class="sxs-lookup"><span data-stu-id="897d7-278">Example</span></span>

<span data-ttu-id="897d7-279">发出 HTTP DELETE 请求：</span><span class="sxs-lookup"><span data-stu-id="897d7-279">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="897d7-280">*可选* ：在修改之前，运行 `get` 命令以查看数据：</span><span class="sxs-lookup"><span data-stu-id="897d7-280">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits> get
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
    ```

1. <span data-ttu-id="897d7-281">在支持 `delete` 命令的终结点上运行该命令：</span><span class="sxs-lookup"><span data-stu-id="897d7-281">Run the `delete` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> delete 2
    ```

    <span data-ttu-id="897d7-282">上述命令显示以下输出格式：</span><span class="sxs-lookup"><span data-stu-id="897d7-282">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="897d7-283">*可选* ：发出 `get` 命令以查看修改。</span><span class="sxs-lookup"><span data-stu-id="897d7-283">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="897d7-284">在此示例中，`get` 返回以下输出：</span><span class="sxs-lookup"><span data-stu-id="897d7-284">In this example, a `get` returns the following output:</span></span>

    ```console
    https://localhost:5001/fruits> get
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


    https://localhost:5001/fruits>
    ```

## <a name="test-http-patch-requests"></a><span data-ttu-id="897d7-285">测试 HTTP PATCH 请求</span><span class="sxs-lookup"><span data-stu-id="897d7-285">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="897d7-286">摘要</span><span class="sxs-lookup"><span data-stu-id="897d7-286">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="897d7-287">自变量</span><span class="sxs-lookup"><span data-stu-id="897d7-287">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="897d7-288">关联控制器操作方法所需的路由参数（如果有）。</span><span class="sxs-lookup"><span data-stu-id="897d7-288">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="897d7-289">选项</span><span class="sxs-lookup"><span data-stu-id="897d7-289">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="897d7-290">测试 HTTP HEAD 请求</span><span class="sxs-lookup"><span data-stu-id="897d7-290">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="897d7-291">摘要</span><span class="sxs-lookup"><span data-stu-id="897d7-291">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="897d7-292">自变量</span><span class="sxs-lookup"><span data-stu-id="897d7-292">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="897d7-293">关联控制器操作方法所需的路由参数（如果有）。</span><span class="sxs-lookup"><span data-stu-id="897d7-293">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="897d7-294">选项</span><span class="sxs-lookup"><span data-stu-id="897d7-294">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="897d7-295">测试 HTTP OPTIONS 请求</span><span class="sxs-lookup"><span data-stu-id="897d7-295">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="897d7-296">摘要</span><span class="sxs-lookup"><span data-stu-id="897d7-296">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="897d7-297">自变量</span><span class="sxs-lookup"><span data-stu-id="897d7-297">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="897d7-298">关联控制器操作方法所需的路由参数（如果有）。</span><span class="sxs-lookup"><span data-stu-id="897d7-298">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="897d7-299">选项</span><span class="sxs-lookup"><span data-stu-id="897d7-299">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="897d7-300">设置 HTTP 请求标头</span><span class="sxs-lookup"><span data-stu-id="897d7-300">Set HTTP request headers</span></span>

<span data-ttu-id="897d7-301">若要设置 HTTP 请求标头，请使用下面一种方法：</span><span class="sxs-lookup"><span data-stu-id="897d7-301">To set an HTTP request header, use one of the following approaches:</span></span>

* <span data-ttu-id="897d7-302">使用该 HTTP 请求进行内联设置。</span><span class="sxs-lookup"><span data-stu-id="897d7-302">Set inline with the HTTP request.</span></span> <span data-ttu-id="897d7-303">例如：</span><span class="sxs-lookup"><span data-stu-id="897d7-303">For example:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```
    
    <span data-ttu-id="897d7-304">若使用上述方法，每个不同的 HTTP 请求标头都需要其自己的 `-h` 选项。</span><span class="sxs-lookup"><span data-stu-id="897d7-304">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

* <span data-ttu-id="897d7-305">在发送 HTTP 请求之前进行设置。</span><span class="sxs-lookup"><span data-stu-id="897d7-305">Set before sending the HTTP request.</span></span> <span data-ttu-id="897d7-306">例如：</span><span class="sxs-lookup"><span data-stu-id="897d7-306">For example:</span></span>

    ```console
    https://localhost:5001/people> set header Content-Type application/json
    ```
    
    <span data-ttu-id="897d7-307">在发送请求之前设置标头时，标头在命令行界面会话期间保持设置。</span><span class="sxs-lookup"><span data-stu-id="897d7-307">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="897d7-308">若要清除标头，请提供一个空值。</span><span class="sxs-lookup"><span data-stu-id="897d7-308">To clear the header, provide an empty value.</span></span> <span data-ttu-id="897d7-309">例如：</span><span class="sxs-lookup"><span data-stu-id="897d7-309">For example:</span></span>
    
    ```console
    https://localhost:5001/people> set header Content-Type
    ```

## <a name="test-secured-endpoints"></a><span data-ttu-id="897d7-310">测试受保护的终结点</span><span class="sxs-lookup"><span data-stu-id="897d7-310">Test secured endpoints</span></span>

<span data-ttu-id="897d7-311">HTTP REPL 支持通过以下方式测试受保护的终结点：</span><span class="sxs-lookup"><span data-stu-id="897d7-311">The HTTP REPL supports the testing of secured endpoints in the following ways:</span></span>

* <span data-ttu-id="897d7-312">通过已登录用户的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="897d7-312">Via the default credentials of the logged in user.</span></span>
* <span data-ttu-id="897d7-313">通过使用 HTTP 请求头。</span><span class="sxs-lookup"><span data-stu-id="897d7-313">Through the use of HTTP request headers.</span></span>

### <a name="default-credentials"></a><span data-ttu-id="897d7-314">默认凭据</span><span class="sxs-lookup"><span data-stu-id="897d7-314">Default credentials</span></span>

<span data-ttu-id="897d7-315">假设你正在测试的 Web API 托管在 IIS 中，并使用 Windows 身份验证进行保护。</span><span class="sxs-lookup"><span data-stu-id="897d7-315">Consider a web API you're testing that's hosted in IIS and secured with Windows authentication.</span></span> <span data-ttu-id="897d7-316">你希望正在运行该工具的用户的凭据流向正在进行测试的 HTTP 终结点。</span><span class="sxs-lookup"><span data-stu-id="897d7-316">You want the credentials of the user running the tool to flow across to the HTTP endpoints being tested.</span></span> <span data-ttu-id="897d7-317">若要传递已登录用户的默认凭据，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="897d7-317">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="897d7-318">将 `httpClient.useDefaultCredentials` 首选项设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="897d7-318">Set the `httpClient.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. <span data-ttu-id="897d7-319">退出并重启该工具，然后将另一个请求发送到 Web API。</span><span class="sxs-lookup"><span data-stu-id="897d7-319">Exit and restart the tool before sending another request to the web API.</span></span>
 
### <a name="default-proxy-credentials"></a><span data-ttu-id="897d7-320">默认代理凭据</span><span class="sxs-lookup"><span data-stu-id="897d7-320">Default proxy credentials</span></span>

<span data-ttu-id="897d7-321">假设你正在测试的 Web API 位于使用 Windows 身份验证保护的代理后面。</span><span class="sxs-lookup"><span data-stu-id="897d7-321">Consider a scenario in which the web API you're testing is behind a proxy secured with Windows authentication.</span></span> <span data-ttu-id="897d7-322">你希望正在运行该工具的用户的凭据流向该代理。</span><span class="sxs-lookup"><span data-stu-id="897d7-322">You want the credentials of the user running the tool to flow to the proxy.</span></span> <span data-ttu-id="897d7-323">若要传递已登录用户的默认凭据，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="897d7-323">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="897d7-324">将 `httpClient.proxy.useDefaultCredentials` 首选项设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="897d7-324">Set the `httpClient.proxy.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.proxy.useDefaultCredentials true
    ```

1. <span data-ttu-id="897d7-325">退出并重启该工具，然后将另一个请求发送到 Web API。</span><span class="sxs-lookup"><span data-stu-id="897d7-325">Exit and restart the tool before sending another request to the web API.</span></span>

### <a name="http-request-headers"></a><span data-ttu-id="897d7-326">HTTP 请求标头</span><span class="sxs-lookup"><span data-stu-id="897d7-326">HTTP request headers</span></span>

<span data-ttu-id="897d7-327">支持的身份验证和授权方案的示例包括：</span><span class="sxs-lookup"><span data-stu-id="897d7-327">Examples of supported authentication and authorization schemes include:</span></span>

* <span data-ttu-id="897d7-328">basic authentication</span><span class="sxs-lookup"><span data-stu-id="897d7-328">basic authentication</span></span>
* <span data-ttu-id="897d7-329">JWT 持有者令牌</span><span class="sxs-lookup"><span data-stu-id="897d7-329">JWT bearer tokens</span></span>
* <span data-ttu-id="897d7-330">摘要式身份验证</span><span class="sxs-lookup"><span data-stu-id="897d7-330">digest authentication</span></span>

<span data-ttu-id="897d7-331">例如，可以使用以下命令将持有者令牌发送到终结点：</span><span class="sxs-lookup"><span data-stu-id="897d7-331">For example, you can send a bearer token to an endpoint with the following command:</span></span>

```console
set header Authorization "bearer <TOKEN VALUE>"
```

<span data-ttu-id="897d7-332">若要访问 Azure 托管的终结点或使用 [Azure REST API](/rest/api/azure/)，你需要持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="897d7-332">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span></span> <span data-ttu-id="897d7-333">使用以下步骤，通过 [Azure CLI](/cli/azure/) 来获取 Azure 订阅的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="897d7-333">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="897d7-334">HTTP REPL 在 HTTP 请求头中设置持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="897d7-334">The HTTP REPL sets the bearer token in an HTTP request header.</span></span> <span data-ttu-id="897d7-335">检索 Azure 应用服务 Web 应用的列表。</span><span class="sxs-lookup"><span data-stu-id="897d7-335">A list of Azure App Service Web Apps is retrieved.</span></span>

1. <span data-ttu-id="897d7-336">登录 Azure：</span><span class="sxs-lookup"><span data-stu-id="897d7-336">Sign in to Azure:</span></span>

    ```azurecli
    az login
    ```

1. <span data-ttu-id="897d7-337">使用以下命令获取订阅 ID：</span><span class="sxs-lookup"><span data-stu-id="897d7-337">Get your subscription ID with the following command:</span></span>

    ```azurecli
    az account show --query id
    ```

1. <span data-ttu-id="897d7-338">复制订阅 ID 并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="897d7-338">Copy your subscription ID and run the following command:</span></span>

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. <span data-ttu-id="897d7-339">使用以下命令获取持有者令牌：</span><span class="sxs-lookup"><span data-stu-id="897d7-339">Get your bearer token with the following command:</span></span>

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. <span data-ttu-id="897d7-340">通过 HTTP REPL 连接到 Azure REST API：</span><span class="sxs-lookup"><span data-stu-id="897d7-340">Connect to the Azure REST API via the HTTP REPL:</span></span>

    ```console
    httprepl https://management.azure.com
    ```

1. <span data-ttu-id="897d7-341">设置 `Authorization` HTTP 请求标头：</span><span class="sxs-lookup"><span data-stu-id="897d7-341">Set the `Authorization` HTTP request header:</span></span>

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. <span data-ttu-id="897d7-342">导航到订阅：</span><span class="sxs-lookup"><span data-stu-id="897d7-342">Navigate to the subscription:</span></span>

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. <span data-ttu-id="897d7-343">获取订阅的 Azure 应用服务 Web 应用的列表：</span><span class="sxs-lookup"><span data-stu-id="897d7-343">Get a list of your subscription's Azure App Service Web Apps:</span></span>

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    <span data-ttu-id="897d7-344">将显示以下响应：</span><span class="sxs-lookup"><span data-stu-id="897d7-344">The following response is displayed:</span></span>

    ```console
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 35948
    Content-Type: application/json; charset=utf-8
    Date: Thu, 19 Sep 2019 23:04:03 GMT
    Expires: -1
    Pragma: no-cache
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    X-Content-Type-Options: nosniff
    x-ms-correlation-request-id: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-original-request-ids: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx;xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-ratelimit-remaining-subscription-reads: 11999
    x-ms-request-id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    x-ms-routing-request-id: WESTUS:xxxxxxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx
    {
      "value": [
        <AZURE RESOURCES LIST>
      ]
    }
    ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="897d7-345">切换 HTTP 请求显示</span><span class="sxs-lookup"><span data-stu-id="897d7-345">Toggle HTTP request display</span></span>

<span data-ttu-id="897d7-346">默认情况下，禁止显示正在发送的 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="897d7-346">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="897d7-347">可在命令行界面会话期间更改相应的设置。</span><span class="sxs-lookup"><span data-stu-id="897d7-347">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="897d7-348">启用请求显示</span><span class="sxs-lookup"><span data-stu-id="897d7-348">Enable request display</span></span>

<span data-ttu-id="897d7-349">运行 `echo on` 命令可查看正在发送的 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="897d7-349">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="897d7-350">例如：</span><span class="sxs-lookup"><span data-stu-id="897d7-350">For example:</span></span>

```console
https://localhost:5001/people> echo on
Request echoing is on
```

<span data-ttu-id="897d7-351">当前会话中的后续 HTTP 请求显示请求标头。</span><span class="sxs-lookup"><span data-stu-id="897d7-351">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="897d7-352">例如：</span><span class="sxs-lookup"><span data-stu-id="897d7-352">For example:</span></span>

```console
https://localhost:5001/people> post

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


https://localhost:5001/people>
```

### <a name="disable-request-display"></a><span data-ttu-id="897d7-353">禁用请求显示</span><span class="sxs-lookup"><span data-stu-id="897d7-353">Disable request display</span></span>

<span data-ttu-id="897d7-354">运行 `echo off` 命令可禁止显示正在发送的 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="897d7-354">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="897d7-355">例如：</span><span class="sxs-lookup"><span data-stu-id="897d7-355">For example:</span></span>

```console
https://localhost:5001/people> echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="897d7-356">运行脚本</span><span class="sxs-lookup"><span data-stu-id="897d7-356">Run a script</span></span>

<span data-ttu-id="897d7-357">如果经常执行一组相同的 HTTP REPL 命令，请考虑将它们存储在一个文本文件中。</span><span class="sxs-lookup"><span data-stu-id="897d7-357">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="897d7-358">文件中的命令采用与在命令行上手动执行的命令相同的形式。</span><span class="sxs-lookup"><span data-stu-id="897d7-358">Commands in the file take the same form as commands executed manually on the command line.</span></span> <span data-ttu-id="897d7-359">可使用 `run` 命令批量执行这些命令。</span><span class="sxs-lookup"><span data-stu-id="897d7-359">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="897d7-360">例如：</span><span class="sxs-lookup"><span data-stu-id="897d7-360">For example:</span></span>

1. <span data-ttu-id="897d7-361">创建一个文本文件，其中包含一组换行符分隔的命令。</span><span class="sxs-lookup"><span data-stu-id="897d7-361">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="897d7-362">例如，一个包含以下命令的 people-script.txt 文件：</span><span class="sxs-lookup"><span data-stu-id="897d7-362">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="897d7-363">执行 `run` 命令，传入文本文件的路径。</span><span class="sxs-lookup"><span data-stu-id="897d7-363">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="897d7-364">例如：</span><span class="sxs-lookup"><span data-stu-id="897d7-364">For example:</span></span>

    ```console
    https://localhost:5001/> run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="897d7-365">随即显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="897d7-365">The following output appears:</span></span>

    ```console
    https://localhost:5001/> set base https://localhost:5001
    Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/> ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/> cd People
    /People    [get|post]
    
    https://localhost:5001/People> ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People> get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People>
    ```

## <a name="clear-the-output"></a><span data-ttu-id="897d7-366">清除输出</span><span class="sxs-lookup"><span data-stu-id="897d7-366">Clear the output</span></span>

<span data-ttu-id="897d7-367">若要删除 HTTP REPL 工具写入命令行界面的所有输出，请运行 `clear` 或 `cls` 命令。</span><span class="sxs-lookup"><span data-stu-id="897d7-367">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="897d7-368">例如，假设命令行界面包含以下输出：</span><span class="sxs-lookup"><span data-stu-id="897d7-368">To illustrate, imagine the command shell contains the following output:</span></span>

```console
httprepl https://localhost:5001
(Disconnected)> set base "https://localhost:5001"
Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/> ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

<span data-ttu-id="897d7-369">运行以下命令以清除输出：</span><span class="sxs-lookup"><span data-stu-id="897d7-369">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/> clear
```

<span data-ttu-id="897d7-370">运行上述命令后，命令行界面仅包含以下输出：</span><span class="sxs-lookup"><span data-stu-id="897d7-370">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/>
```

## <a name="additional-resources"></a><span data-ttu-id="897d7-371">其他资源</span><span class="sxs-lookup"><span data-stu-id="897d7-371">Additional resources</span></span>

* [<span data-ttu-id="897d7-372">REST API 请求</span><span class="sxs-lookup"><span data-stu-id="897d7-372">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="897d7-373">HTTP REPL GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="897d7-373">HTTP REPL GitHub repository</span></span>](https://github.com/dotnet/HttpRepl)
