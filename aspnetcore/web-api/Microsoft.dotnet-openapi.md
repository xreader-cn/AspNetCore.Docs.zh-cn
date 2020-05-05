---
title: 使用 OpenAPI 开发 ASP.NET Core 应用
author: ryanbrandenburg
description: 演示如何使用“Microsoft.dotnet-openapi”工具添加 OpenAPI 文件引用。
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 1924fb8ee5ac1ba8dc31d2175a336c8333c81fb2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775708"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="9824e-103">使用 OpenAPI 工具开发 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="9824e-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="9824e-104">作者：Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="9824e-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="9824e-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) 是用于管理项目内 [OpenAPI](https://github.com/OAI/OpenAPI-Specification) 引用的 [.NET Core 全局工具](/dotnet/core/tools/global-tools)。</span><span class="sxs-lookup"><span data-stu-id="9824e-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="9824e-106">安装</span><span class="sxs-lookup"><span data-stu-id="9824e-106">Installation</span></span>

<span data-ttu-id="9824e-107">若要安装 `Microsoft.dotnet-openapi`，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="9824e-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="9824e-108">添加</span><span class="sxs-lookup"><span data-stu-id="9824e-108">Add</span></span>

<span data-ttu-id="9824e-109">使用此页上的任意命令添加 OpenAPI 引用会将类似于`<OpenApiReference />`下面的元素添加到 *.csproj*文件：</span><span class="sxs-lookup"><span data-stu-id="9824e-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="9824e-110">必须有上述引用，应用才可以调用生成的客户端代码。</span><span class="sxs-lookup"><span data-stu-id="9824e-110">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/dotnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="9824e-111">添加文件</span><span class="sxs-lookup"><span data-stu-id="9824e-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="9824e-112">选项</span><span class="sxs-lookup"><span data-stu-id="9824e-112">Options</span></span>

| <span data-ttu-id="9824e-113">短选项</span><span class="sxs-lookup"><span data-stu-id="9824e-113">Short option</span></span>| <span data-ttu-id="9824e-114">长选项</span><span class="sxs-lookup"><span data-stu-id="9824e-114">Long option</span></span>| <span data-ttu-id="9824e-115">说明</span><span class="sxs-lookup"><span data-stu-id="9824e-115">Description</span></span> | <span data-ttu-id="9824e-116">示例</span><span class="sxs-lookup"><span data-stu-id="9824e-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="9824e-117">-p</span><span class="sxs-lookup"><span data-stu-id="9824e-117">-p</span></span>|<span data-ttu-id="9824e-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="9824e-118">--updateProject</span></span> | <span data-ttu-id="9824e-119">要操作的项目。</span><span class="sxs-lookup"><span data-stu-id="9824e-119">The project to operate on.</span></span> |<span data-ttu-id="9824e-120">dotnet openapi add file --updateProject .\Ref.csproj\*\* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="9824e-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="9824e-121">-c</span><span class="sxs-lookup"><span data-stu-id="9824e-121">-c</span></span>|<span data-ttu-id="9824e-122">--code-generator</span><span class="sxs-lookup"><span data-stu-id="9824e-122">--code-generator</span></span>| <span data-ttu-id="9824e-123">应用于引用的代码生成器。</span><span class="sxs-lookup"><span data-stu-id="9824e-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="9824e-124">选项包括 `NSwagCSharp` 和 `NSwagTypeScript`。</span><span class="sxs-lookup"><span data-stu-id="9824e-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="9824e-125">如果未指定 `--code-generator`，则工具将默认为 `NSwagCSharp`。</span><span class="sxs-lookup"><span data-stu-id="9824e-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="9824e-126">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="9824e-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="9824e-127">-H</span><span class="sxs-lookup"><span data-stu-id="9824e-127">-h</span></span>|<span data-ttu-id="9824e-128">--help</span><span class="sxs-lookup"><span data-stu-id="9824e-128">--help</span></span>|<span data-ttu-id="9824e-129">显示帮助信息</span><span class="sxs-lookup"><span data-stu-id="9824e-129">Show help information</span></span>|<span data-ttu-id="9824e-130">dotnet openapi add file --help</span><span class="sxs-lookup"><span data-stu-id="9824e-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="9824e-131">自变量</span><span class="sxs-lookup"><span data-stu-id="9824e-131">Arguments</span></span>

|  <span data-ttu-id="9824e-132">参数</span><span class="sxs-lookup"><span data-stu-id="9824e-132">Argument</span></span>  | <span data-ttu-id="9824e-133">说明</span><span class="sxs-lookup"><span data-stu-id="9824e-133">Description</span></span> | <span data-ttu-id="9824e-134">示例</span><span class="sxs-lookup"><span data-stu-id="9824e-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="9824e-135">source-file</span><span class="sxs-lookup"><span data-stu-id="9824e-135">source-file</span></span> | <span data-ttu-id="9824e-136">要创建的引用的源。</span><span class="sxs-lookup"><span data-stu-id="9824e-136">The source to create a reference from.</span></span> <span data-ttu-id="9824e-137">必须为 OpenAPI 文件。</span><span class="sxs-lookup"><span data-stu-id="9824e-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="9824e-138">dotnet openapi add file .\OpenAPI.json\*\*</span><span class="sxs-lookup"><span data-stu-id="9824e-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="9824e-139">添加 URL</span><span class="sxs-lookup"><span data-stu-id="9824e-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="9824e-140">选项</span><span class="sxs-lookup"><span data-stu-id="9824e-140">Options</span></span>

| <span data-ttu-id="9824e-141">短选项</span><span class="sxs-lookup"><span data-stu-id="9824e-141">Short option</span></span>| <span data-ttu-id="9824e-142">长选项</span><span class="sxs-lookup"><span data-stu-id="9824e-142">Long option</span></span>| <span data-ttu-id="9824e-143">说明</span><span class="sxs-lookup"><span data-stu-id="9824e-143">Description</span></span> | <span data-ttu-id="9824e-144">示例</span><span class="sxs-lookup"><span data-stu-id="9824e-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="9824e-145">-p</span><span class="sxs-lookup"><span data-stu-id="9824e-145">-p</span></span>|<span data-ttu-id="9824e-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="9824e-146">--updateProject</span></span> | <span data-ttu-id="9824e-147">要操作的项目。</span><span class="sxs-lookup"><span data-stu-id="9824e-147">The project to operate on.</span></span> |<span data-ttu-id="9824e-148">dotnet openapi add url --updateProject .\Ref.csproj\*\* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="9824e-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="9824e-149">-o</span><span class="sxs-lookup"><span data-stu-id="9824e-149">-o</span></span>|<span data-ttu-id="9824e-150">--output-file</span><span class="sxs-lookup"><span data-stu-id="9824e-150">--output-file</span></span> | <span data-ttu-id="9824e-151">用于放置 OpenAPI 文件本地副本的位置。</span><span class="sxs-lookup"><span data-stu-id="9824e-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="9824e-152">dotnet openapi add url  --output-file myclient.json`https://contoso.com/openapi.json` \*\*</span><span class="sxs-lookup"><span data-stu-id="9824e-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="9824e-153">-c</span><span class="sxs-lookup"><span data-stu-id="9824e-153">-c</span></span>|<span data-ttu-id="9824e-154">--code-generator</span><span class="sxs-lookup"><span data-stu-id="9824e-154">--code-generator</span></span>| <span data-ttu-id="9824e-155">应用于引用的代码生成器。</span><span class="sxs-lookup"><span data-stu-id="9824e-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="9824e-156">选项包括 `NSwagCSharp` 和 `NSwagTypeScript`。</span><span class="sxs-lookup"><span data-stu-id="9824e-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="9824e-157">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="9824e-157">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="9824e-158">-H</span><span class="sxs-lookup"><span data-stu-id="9824e-158">-h</span></span>|<span data-ttu-id="9824e-159">--help</span><span class="sxs-lookup"><span data-stu-id="9824e-159">--help</span></span>|<span data-ttu-id="9824e-160">显示帮助信息</span><span class="sxs-lookup"><span data-stu-id="9824e-160">Show help information</span></span>|<span data-ttu-id="9824e-161">dotnet openapi add url --help</span><span class="sxs-lookup"><span data-stu-id="9824e-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="9824e-162">自变量</span><span class="sxs-lookup"><span data-stu-id="9824e-162">Arguments</span></span>

|  <span data-ttu-id="9824e-163">参数</span><span class="sxs-lookup"><span data-stu-id="9824e-163">Argument</span></span>  | <span data-ttu-id="9824e-164">说明</span><span class="sxs-lookup"><span data-stu-id="9824e-164">Description</span></span> | <span data-ttu-id="9824e-165">示例</span><span class="sxs-lookup"><span data-stu-id="9824e-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="9824e-166">source-URL</span><span class="sxs-lookup"><span data-stu-id="9824e-166">source-URL</span></span> | <span data-ttu-id="9824e-167">要创建的引用的源。</span><span class="sxs-lookup"><span data-stu-id="9824e-167">The source to create a reference from.</span></span> <span data-ttu-id="9824e-168">必须是 URL。</span><span class="sxs-lookup"><span data-stu-id="9824e-168">Must be a URL.</span></span> |<span data-ttu-id="9824e-169">dotnet openapi add url `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="9824e-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="9824e-170">删除</span><span class="sxs-lookup"><span data-stu-id="9824e-170">Remove</span></span>

<span data-ttu-id="9824e-171">删除与 .csproj 文件中给定文件名匹配的 OpenAPI 引用。\*\*</span><span class="sxs-lookup"><span data-stu-id="9824e-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="9824e-172">删除 OpenAPI 引用后，将不会生成客户端。</span><span class="sxs-lookup"><span data-stu-id="9824e-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="9824e-173">将删除本地 .json 和 .yaml 文件。\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="9824e-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="9824e-174">选项</span><span class="sxs-lookup"><span data-stu-id="9824e-174">Options</span></span>

| <span data-ttu-id="9824e-175">短选项</span><span class="sxs-lookup"><span data-stu-id="9824e-175">Short option</span></span>| <span data-ttu-id="9824e-176">长选项</span><span class="sxs-lookup"><span data-stu-id="9824e-176">Long option</span></span>| <span data-ttu-id="9824e-177">说明</span><span class="sxs-lookup"><span data-stu-id="9824e-177">Description</span></span>| <span data-ttu-id="9824e-178">示例</span><span class="sxs-lookup"><span data-stu-id="9824e-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="9824e-179">-p</span><span class="sxs-lookup"><span data-stu-id="9824e-179">-p</span></span>|<span data-ttu-id="9824e-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="9824e-180">--updateProject</span></span> | <span data-ttu-id="9824e-181">要操作的项目。</span><span class="sxs-lookup"><span data-stu-id="9824e-181">The project to operate on.</span></span> |<span data-ttu-id="9824e-182">dotnet openapi remove --updateProject .\Ref.csproj\*\* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="9824e-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="9824e-183">-H</span><span class="sxs-lookup"><span data-stu-id="9824e-183">-h</span></span>|<span data-ttu-id="9824e-184">--help</span><span class="sxs-lookup"><span data-stu-id="9824e-184">--help</span></span>|<span data-ttu-id="9824e-185">显示帮助信息</span><span class="sxs-lookup"><span data-stu-id="9824e-185">Show help information</span></span>|<span data-ttu-id="9824e-186">dotnet openapi remove --help</span><span class="sxs-lookup"><span data-stu-id="9824e-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="9824e-187">自变量</span><span class="sxs-lookup"><span data-stu-id="9824e-187">Arguments</span></span>

|  <span data-ttu-id="9824e-188">参数</span><span class="sxs-lookup"><span data-stu-id="9824e-188">Argument</span></span>  | <span data-ttu-id="9824e-189">说明</span><span class="sxs-lookup"><span data-stu-id="9824e-189">Description</span></span>| <span data-ttu-id="9824e-190">示例</span><span class="sxs-lookup"><span data-stu-id="9824e-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="9824e-191">source-file</span><span class="sxs-lookup"><span data-stu-id="9824e-191">source-file</span></span> | <span data-ttu-id="9824e-192">要删除的引用的源。</span><span class="sxs-lookup"><span data-stu-id="9824e-192">The source to remove the reference to.</span></span> |<span data-ttu-id="9824e-193">dotnet openapi remove .\OpenAPI.json\*\*</span><span class="sxs-lookup"><span data-stu-id="9824e-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="9824e-194">刷新</span><span class="sxs-lookup"><span data-stu-id="9824e-194">Refresh</span></span>

<span data-ttu-id="9824e-195">使用下载 URL 中的最新内容刷新已下载的文件本地版本。</span><span class="sxs-lookup"><span data-stu-id="9824e-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="9824e-196">选项</span><span class="sxs-lookup"><span data-stu-id="9824e-196">Options</span></span>

| <span data-ttu-id="9824e-197">短选项</span><span class="sxs-lookup"><span data-stu-id="9824e-197">Short option</span></span>| <span data-ttu-id="9824e-198">长选项</span><span class="sxs-lookup"><span data-stu-id="9824e-198">Long option</span></span>| <span data-ttu-id="9824e-199">说明</span><span class="sxs-lookup"><span data-stu-id="9824e-199">Description</span></span> | <span data-ttu-id="9824e-200">示例</span><span class="sxs-lookup"><span data-stu-id="9824e-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="9824e-201">-p</span><span class="sxs-lookup"><span data-stu-id="9824e-201">-p</span></span>|<span data-ttu-id="9824e-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="9824e-202">--updateProject</span></span> | <span data-ttu-id="9824e-203">要操作的项目。</span><span class="sxs-lookup"><span data-stu-id="9824e-203">The project to operate on.</span></span> | <span data-ttu-id="9824e-204">dotnet openapi refresh --updateProject .\Ref.csproj\*\* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="9824e-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="9824e-205">-H</span><span class="sxs-lookup"><span data-stu-id="9824e-205">-h</span></span>|<span data-ttu-id="9824e-206">--help</span><span class="sxs-lookup"><span data-stu-id="9824e-206">--help</span></span>|<span data-ttu-id="9824e-207">显示帮助信息</span><span class="sxs-lookup"><span data-stu-id="9824e-207">Show help information</span></span>|<span data-ttu-id="9824e-208">dotnet openapi refresh --help</span><span class="sxs-lookup"><span data-stu-id="9824e-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="9824e-209">自变量</span><span class="sxs-lookup"><span data-stu-id="9824e-209">Arguments</span></span>

|  <span data-ttu-id="9824e-210">参数</span><span class="sxs-lookup"><span data-stu-id="9824e-210">Argument</span></span>  | <span data-ttu-id="9824e-211">说明</span><span class="sxs-lookup"><span data-stu-id="9824e-211">Description</span></span> | <span data-ttu-id="9824e-212">示例</span><span class="sxs-lookup"><span data-stu-id="9824e-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="9824e-213">source-URL</span><span class="sxs-lookup"><span data-stu-id="9824e-213">source-URL</span></span> | <span data-ttu-id="9824e-214">用于刷新引用的 URL。</span><span class="sxs-lookup"><span data-stu-id="9824e-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="9824e-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="9824e-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
