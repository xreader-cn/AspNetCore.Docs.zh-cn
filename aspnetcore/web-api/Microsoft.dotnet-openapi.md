---
title: 使用 OpenAPI 开发 ASP.NET Core 应用
author: ryanbrandenburg
description: 演示如何使用“Microsoft.dotnet-openapi”工具添加 OpenAPI 文件引用。
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
no-loc:
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
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 45921deb35452876b0a92a8731da68539a880c1d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626553"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="e2df1-103">使用 OpenAPI 工具开发 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="e2df1-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="e2df1-104">作者：Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="e2df1-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="e2df1-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) 是用于管理项目内 [OpenAPI](https://github.com/OAI/OpenAPI-Specification) 引用的 [.NET Core 全局工具](/dotnet/core/tools/global-tools)。</span><span class="sxs-lookup"><span data-stu-id="e2df1-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="e2df1-106">安装</span><span class="sxs-lookup"><span data-stu-id="e2df1-106">Installation</span></span>

<span data-ttu-id="e2df1-107">若要安装 `Microsoft.dotnet-openapi`，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="e2df1-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="e2df1-108">添加</span><span class="sxs-lookup"><span data-stu-id="e2df1-108">Add</span></span>

<span data-ttu-id="e2df1-109">使用此页上的任意命令添加 OpenAPI 引用会将类似于 `<OpenApiReference />` 下面的元素添加到 *.csproj* 文件：</span><span class="sxs-lookup"><span data-stu-id="e2df1-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="e2df1-110">必须有上述引用，应用才可以调用生成的客户端代码。</span><span class="sxs-lookup"><span data-stu-id="e2df1-110">The preceding reference is required for the app to call the generated client code.</span></span>

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

### <a name="add-file"></a><span data-ttu-id="e2df1-111">添加文件</span><span class="sxs-lookup"><span data-stu-id="e2df1-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="e2df1-112">选项</span><span class="sxs-lookup"><span data-stu-id="e2df1-112">Options</span></span>

| <span data-ttu-id="e2df1-113">短选项</span><span class="sxs-lookup"><span data-stu-id="e2df1-113">Short option</span></span>| <span data-ttu-id="e2df1-114">长选项</span><span class="sxs-lookup"><span data-stu-id="e2df1-114">Long option</span></span>| <span data-ttu-id="e2df1-115">描述</span><span class="sxs-lookup"><span data-stu-id="e2df1-115">Description</span></span> | <span data-ttu-id="e2df1-116">示例</span><span class="sxs-lookup"><span data-stu-id="e2df1-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="e2df1-117">-p</span><span class="sxs-lookup"><span data-stu-id="e2df1-117">-p</span></span>|<span data-ttu-id="e2df1-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="e2df1-118">--updateProject</span></span> | <span data-ttu-id="e2df1-119">要操作的项目。</span><span class="sxs-lookup"><span data-stu-id="e2df1-119">The project to operate on.</span></span> |<span data-ttu-id="e2df1-120">dotnet openapi add file --updateProject .\Ref.csproj\*\* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="e2df1-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="e2df1-121">-c</span><span class="sxs-lookup"><span data-stu-id="e2df1-121">-c</span></span>|<span data-ttu-id="e2df1-122">--code-generator</span><span class="sxs-lookup"><span data-stu-id="e2df1-122">--code-generator</span></span>| <span data-ttu-id="e2df1-123">应用于引用的代码生成器。</span><span class="sxs-lookup"><span data-stu-id="e2df1-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="e2df1-124">选项包括 `NSwagCSharp` 和 `NSwagTypeScript`。</span><span class="sxs-lookup"><span data-stu-id="e2df1-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="e2df1-125">如果未指定 `--code-generator`，则工具将默认为 `NSwagCSharp`。</span><span class="sxs-lookup"><span data-stu-id="e2df1-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="e2df1-126">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="e2df1-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="e2df1-127">-H</span><span class="sxs-lookup"><span data-stu-id="e2df1-127">-h</span></span>|<span data-ttu-id="e2df1-128">--help</span><span class="sxs-lookup"><span data-stu-id="e2df1-128">--help</span></span>|<span data-ttu-id="e2df1-129">显示帮助信息</span><span class="sxs-lookup"><span data-stu-id="e2df1-129">Show help information</span></span>|<span data-ttu-id="e2df1-130">dotnet openapi add file --help</span><span class="sxs-lookup"><span data-stu-id="e2df1-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="e2df1-131">参数</span><span class="sxs-lookup"><span data-stu-id="e2df1-131">Arguments</span></span>

|  <span data-ttu-id="e2df1-132">参数</span><span class="sxs-lookup"><span data-stu-id="e2df1-132">Argument</span></span>  | <span data-ttu-id="e2df1-133">说明</span><span class="sxs-lookup"><span data-stu-id="e2df1-133">Description</span></span> | <span data-ttu-id="e2df1-134">示例</span><span class="sxs-lookup"><span data-stu-id="e2df1-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="e2df1-135">source-file</span><span class="sxs-lookup"><span data-stu-id="e2df1-135">source-file</span></span> | <span data-ttu-id="e2df1-136">要创建的引用的源。</span><span class="sxs-lookup"><span data-stu-id="e2df1-136">The source to create a reference from.</span></span> <span data-ttu-id="e2df1-137">必须为 OpenAPI 文件。</span><span class="sxs-lookup"><span data-stu-id="e2df1-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="e2df1-138">dotnet openapi add file .\OpenAPI.json\*\*</span><span class="sxs-lookup"><span data-stu-id="e2df1-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="e2df1-139">添加 URL</span><span class="sxs-lookup"><span data-stu-id="e2df1-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="e2df1-140">选项</span><span class="sxs-lookup"><span data-stu-id="e2df1-140">Options</span></span>

| <span data-ttu-id="e2df1-141">短选项</span><span class="sxs-lookup"><span data-stu-id="e2df1-141">Short option</span></span>| <span data-ttu-id="e2df1-142">长选项</span><span class="sxs-lookup"><span data-stu-id="e2df1-142">Long option</span></span>| <span data-ttu-id="e2df1-143">描述</span><span class="sxs-lookup"><span data-stu-id="e2df1-143">Description</span></span> | <span data-ttu-id="e2df1-144">示例</span><span class="sxs-lookup"><span data-stu-id="e2df1-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="e2df1-145">-p</span><span class="sxs-lookup"><span data-stu-id="e2df1-145">-p</span></span>|<span data-ttu-id="e2df1-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="e2df1-146">--updateProject</span></span> | <span data-ttu-id="e2df1-147">要操作的项目。</span><span class="sxs-lookup"><span data-stu-id="e2df1-147">The project to operate on.</span></span> |<span data-ttu-id="e2df1-148">dotnet openapi add url --updateProject .\Ref.csproj\*\* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="e2df1-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="e2df1-149">-o</span><span class="sxs-lookup"><span data-stu-id="e2df1-149">-o</span></span>|<span data-ttu-id="e2df1-150">--output-file</span><span class="sxs-lookup"><span data-stu-id="e2df1-150">--output-file</span></span> | <span data-ttu-id="e2df1-151">用于放置 OpenAPI 文件本地副本的位置。</span><span class="sxs-lookup"><span data-stu-id="e2df1-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="e2df1-152">dotnet openapi add url  --output-file myclient.json`https://contoso.com/openapi.json` \*\*</span><span class="sxs-lookup"><span data-stu-id="e2df1-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="e2df1-153">-c</span><span class="sxs-lookup"><span data-stu-id="e2df1-153">-c</span></span>|<span data-ttu-id="e2df1-154">--code-generator</span><span class="sxs-lookup"><span data-stu-id="e2df1-154">--code-generator</span></span>| <span data-ttu-id="e2df1-155">应用于引用的代码生成器。</span><span class="sxs-lookup"><span data-stu-id="e2df1-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="e2df1-156">选项包括 `NSwagCSharp` 和 `NSwagTypeScript`。</span><span class="sxs-lookup"><span data-stu-id="e2df1-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="e2df1-157">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="e2df1-157">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="e2df1-158">-H</span><span class="sxs-lookup"><span data-stu-id="e2df1-158">-h</span></span>|<span data-ttu-id="e2df1-159">--help</span><span class="sxs-lookup"><span data-stu-id="e2df1-159">--help</span></span>|<span data-ttu-id="e2df1-160">显示帮助信息</span><span class="sxs-lookup"><span data-stu-id="e2df1-160">Show help information</span></span>|<span data-ttu-id="e2df1-161">dotnet openapi add url --help</span><span class="sxs-lookup"><span data-stu-id="e2df1-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="e2df1-162">参数</span><span class="sxs-lookup"><span data-stu-id="e2df1-162">Arguments</span></span>

|  <span data-ttu-id="e2df1-163">参数</span><span class="sxs-lookup"><span data-stu-id="e2df1-163">Argument</span></span>  | <span data-ttu-id="e2df1-164">说明</span><span class="sxs-lookup"><span data-stu-id="e2df1-164">Description</span></span> | <span data-ttu-id="e2df1-165">示例</span><span class="sxs-lookup"><span data-stu-id="e2df1-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="e2df1-166">source-URL</span><span class="sxs-lookup"><span data-stu-id="e2df1-166">source-URL</span></span> | <span data-ttu-id="e2df1-167">要创建的引用的源。</span><span class="sxs-lookup"><span data-stu-id="e2df1-167">The source to create a reference from.</span></span> <span data-ttu-id="e2df1-168">必须是 URL。</span><span class="sxs-lookup"><span data-stu-id="e2df1-168">Must be a URL.</span></span> |<span data-ttu-id="e2df1-169">dotnet openapi add url `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="e2df1-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="e2df1-170">删除</span><span class="sxs-lookup"><span data-stu-id="e2df1-170">Remove</span></span>

<span data-ttu-id="e2df1-171">删除与 .csproj 文件中给定文件名匹配的 OpenAPI 引用。\*\*</span><span class="sxs-lookup"><span data-stu-id="e2df1-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="e2df1-172">删除 OpenAPI 引用后，将不会生成客户端。</span><span class="sxs-lookup"><span data-stu-id="e2df1-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="e2df1-173">将删除本地 .json 和 .yaml 文件。\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="e2df1-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="e2df1-174">选项</span><span class="sxs-lookup"><span data-stu-id="e2df1-174">Options</span></span>

| <span data-ttu-id="e2df1-175">短选项</span><span class="sxs-lookup"><span data-stu-id="e2df1-175">Short option</span></span>| <span data-ttu-id="e2df1-176">长选项</span><span class="sxs-lookup"><span data-stu-id="e2df1-176">Long option</span></span>| <span data-ttu-id="e2df1-177">描述</span><span class="sxs-lookup"><span data-stu-id="e2df1-177">Description</span></span>| <span data-ttu-id="e2df1-178">示例</span><span class="sxs-lookup"><span data-stu-id="e2df1-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="e2df1-179">-p</span><span class="sxs-lookup"><span data-stu-id="e2df1-179">-p</span></span>|<span data-ttu-id="e2df1-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="e2df1-180">--updateProject</span></span> | <span data-ttu-id="e2df1-181">要操作的项目。</span><span class="sxs-lookup"><span data-stu-id="e2df1-181">The project to operate on.</span></span> |<span data-ttu-id="e2df1-182">dotnet openapi remove --updateProject .\Ref.csproj\*\* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="e2df1-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="e2df1-183">-H</span><span class="sxs-lookup"><span data-stu-id="e2df1-183">-h</span></span>|<span data-ttu-id="e2df1-184">--help</span><span class="sxs-lookup"><span data-stu-id="e2df1-184">--help</span></span>|<span data-ttu-id="e2df1-185">显示帮助信息</span><span class="sxs-lookup"><span data-stu-id="e2df1-185">Show help information</span></span>|<span data-ttu-id="e2df1-186">dotnet openapi remove --help</span><span class="sxs-lookup"><span data-stu-id="e2df1-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="e2df1-187">参数</span><span class="sxs-lookup"><span data-stu-id="e2df1-187">Arguments</span></span>

|  <span data-ttu-id="e2df1-188">参数</span><span class="sxs-lookup"><span data-stu-id="e2df1-188">Argument</span></span>  | <span data-ttu-id="e2df1-189">说明</span><span class="sxs-lookup"><span data-stu-id="e2df1-189">Description</span></span>| <span data-ttu-id="e2df1-190">示例</span><span class="sxs-lookup"><span data-stu-id="e2df1-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="e2df1-191">source-file</span><span class="sxs-lookup"><span data-stu-id="e2df1-191">source-file</span></span> | <span data-ttu-id="e2df1-192">要删除的引用的源。</span><span class="sxs-lookup"><span data-stu-id="e2df1-192">The source to remove the reference to.</span></span> |<span data-ttu-id="e2df1-193">dotnet openapi remove .\OpenAPI.json\*\*</span><span class="sxs-lookup"><span data-stu-id="e2df1-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="e2df1-194">刷新</span><span class="sxs-lookup"><span data-stu-id="e2df1-194">Refresh</span></span>

<span data-ttu-id="e2df1-195">使用下载 URL 中的最新内容刷新已下载的文件本地版本。</span><span class="sxs-lookup"><span data-stu-id="e2df1-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="e2df1-196">选项</span><span class="sxs-lookup"><span data-stu-id="e2df1-196">Options</span></span>

| <span data-ttu-id="e2df1-197">短选项</span><span class="sxs-lookup"><span data-stu-id="e2df1-197">Short option</span></span>| <span data-ttu-id="e2df1-198">长选项</span><span class="sxs-lookup"><span data-stu-id="e2df1-198">Long option</span></span>| <span data-ttu-id="e2df1-199">描述</span><span class="sxs-lookup"><span data-stu-id="e2df1-199">Description</span></span> | <span data-ttu-id="e2df1-200">示例</span><span class="sxs-lookup"><span data-stu-id="e2df1-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="e2df1-201">-p</span><span class="sxs-lookup"><span data-stu-id="e2df1-201">-p</span></span>|<span data-ttu-id="e2df1-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="e2df1-202">--updateProject</span></span> | <span data-ttu-id="e2df1-203">要操作的项目。</span><span class="sxs-lookup"><span data-stu-id="e2df1-203">The project to operate on.</span></span> | <span data-ttu-id="e2df1-204">dotnet openapi refresh --updateProject .\Ref.csproj\*\* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="e2df1-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="e2df1-205">-H</span><span class="sxs-lookup"><span data-stu-id="e2df1-205">-h</span></span>|<span data-ttu-id="e2df1-206">--help</span><span class="sxs-lookup"><span data-stu-id="e2df1-206">--help</span></span>|<span data-ttu-id="e2df1-207">显示帮助信息</span><span class="sxs-lookup"><span data-stu-id="e2df1-207">Show help information</span></span>|<span data-ttu-id="e2df1-208">dotnet openapi refresh --help</span><span class="sxs-lookup"><span data-stu-id="e2df1-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="e2df1-209">参数</span><span class="sxs-lookup"><span data-stu-id="e2df1-209">Arguments</span></span>

|  <span data-ttu-id="e2df1-210">参数</span><span class="sxs-lookup"><span data-stu-id="e2df1-210">Argument</span></span>  | <span data-ttu-id="e2df1-211">说明</span><span class="sxs-lookup"><span data-stu-id="e2df1-211">Description</span></span> | <span data-ttu-id="e2df1-212">示例</span><span class="sxs-lookup"><span data-stu-id="e2df1-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="e2df1-213">source-URL</span><span class="sxs-lookup"><span data-stu-id="e2df1-213">source-URL</span></span> | <span data-ttu-id="e2df1-214">用于刷新引用的 URL。</span><span class="sxs-lookup"><span data-stu-id="e2df1-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="e2df1-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="e2df1-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
