---
title: 使用 OpenAPI 开发 ASP.NET Core 应用
author: ryanbrandenburg
description: 演示如何使用“Microsoft.dotnet-openapi”工具添加 OpenAPI 文件引用。
ms.author: rybrande
ms.date: 08/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: a9b38bb7e69744d72867bf69cecf1fa92d7c15b3
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187364"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="b9cda-103">使用 OpenAPI 工具开发 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="b9cda-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="b9cda-104">作者：Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="b9cda-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="b9cda-105">`Microsoft.dotnet-openapi` 是用于管理项目内 [OpenAPI](https://github.com/OAI/OpenAPI-Specification) 引用的 .NET Core 全局工具。</span><span class="sxs-lookup"><span data-stu-id="b9cda-105">`Microsoft.dotnet-openapi` is a .NET Core global tool for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="b9cda-106">安装</span><span class="sxs-lookup"><span data-stu-id="b9cda-106">Installation</span></span>

<span data-ttu-id="b9cda-107">若要安装 `Microsoft.dotnet-openapi`，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b9cda-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```console
dotnet tool install -g Microsoft.dotnet-openapi
```

<span data-ttu-id="b9cda-108">`Microsoft.dotnet-openapi` 是 [.NET Core 全局工具](/dotnet/core/tools/global-tools)。</span><span class="sxs-lookup"><span data-stu-id="b9cda-108">`Microsoft.dotnet-openapi` is a [.NET Core Global Tool](/dotnet/core/tools/global-tools).</span></span>

## <a name="add"></a><span data-ttu-id="b9cda-109">添加</span><span class="sxs-lookup"><span data-stu-id="b9cda-109">Add</span></span>

<span data-ttu-id="b9cda-110">使用本页上的任意一个命令添加 OpenAPI 引用，将向 .csproj 文件添加如下所示的 `<OpenApiReference />` 元素：</span><span class="sxs-lookup"><span data-stu-id="b9cda-110">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />`  element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="b9cda-111">必须有上述引用，应用才可以调用生成的客户端代码。</span><span class="sxs-lookup"><span data-stu-id="b9cda-111">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/aspnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -v|--verbose | Show verbose output. |dotnet openapi add project *-v* ../Ref/ProjRef.csproj |
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="b9cda-112">添加文件</span><span class="sxs-lookup"><span data-stu-id="b9cda-112">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="b9cda-113">选项</span><span class="sxs-lookup"><span data-stu-id="b9cda-113">Options</span></span>

| <span data-ttu-id="b9cda-114">短选项</span><span class="sxs-lookup"><span data-stu-id="b9cda-114">Short option</span></span>| <span data-ttu-id="b9cda-115">长选项</span><span class="sxs-lookup"><span data-stu-id="b9cda-115">Long option</span></span>| <span data-ttu-id="b9cda-116">说明</span><span class="sxs-lookup"><span data-stu-id="b9cda-116">Description</span></span> | <span data-ttu-id="b9cda-117">示例</span><span class="sxs-lookup"><span data-stu-id="b9cda-117">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="b9cda-118">-v</span><span class="sxs-lookup"><span data-stu-id="b9cda-118">-v</span></span>|<span data-ttu-id="b9cda-119">--verbose</span><span class="sxs-lookup"><span data-stu-id="b9cda-119">--verbose</span></span> | <span data-ttu-id="b9cda-120">显示详细输出。</span><span class="sxs-lookup"><span data-stu-id="b9cda-120">Show verbose output.</span></span> |<span data-ttu-id="b9cda-121">dotnet openapi add file -v .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="b9cda-121">dotnet openapi add file *-v* .\OpenAPI.json</span></span> |
| <span data-ttu-id="b9cda-122">-p</span><span class="sxs-lookup"><span data-stu-id="b9cda-122">-p</span></span>|<span data-ttu-id="b9cda-123">--updateProject</span><span class="sxs-lookup"><span data-stu-id="b9cda-123">--updateProject</span></span> | <span data-ttu-id="b9cda-124">要操作的项目。</span><span class="sxs-lookup"><span data-stu-id="b9cda-124">The project to operate on.</span></span> |<span data-ttu-id="b9cda-125">dotnet openapi add file --updateProject .\Ref.csproj .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="b9cda-125">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="b9cda-126">-c</span><span class="sxs-lookup"><span data-stu-id="b9cda-126">-c</span></span>|<span data-ttu-id="b9cda-127">--code-generator</span><span class="sxs-lookup"><span data-stu-id="b9cda-127">--code-generator</span></span>| <span data-ttu-id="b9cda-128">应用于引用的代码生成器。</span><span class="sxs-lookup"><span data-stu-id="b9cda-128">The code generator to apply to the reference.</span></span> <span data-ttu-id="b9cda-129">选项包括 `NSwagCSharp` 和 `NSwagTypeScript`。</span><span class="sxs-lookup"><span data-stu-id="b9cda-129">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="b9cda-130">如果未指定 `--code-generator`，则工具将默认为 `NSwagCSharp`。</span><span class="sxs-lookup"><span data-stu-id="b9cda-130">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="b9cda-131">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="b9cda-131">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="b9cda-132">-h</span><span class="sxs-lookup"><span data-stu-id="b9cda-132">-h</span></span>|<span data-ttu-id="b9cda-133">--help</span><span class="sxs-lookup"><span data-stu-id="b9cda-133">--help</span></span>|<span data-ttu-id="b9cda-134">显示帮助信息</span><span class="sxs-lookup"><span data-stu-id="b9cda-134">Show help information</span></span>|<span data-ttu-id="b9cda-135">dotnet openapi add file --help</span><span class="sxs-lookup"><span data-stu-id="b9cda-135">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="b9cda-136">自变量</span><span class="sxs-lookup"><span data-stu-id="b9cda-136">Arguments</span></span>

|  <span data-ttu-id="b9cda-137">参数</span><span class="sxs-lookup"><span data-stu-id="b9cda-137">Argument</span></span>  | <span data-ttu-id="b9cda-138">说明</span><span class="sxs-lookup"><span data-stu-id="b9cda-138">Description</span></span> | <span data-ttu-id="b9cda-139">示例</span><span class="sxs-lookup"><span data-stu-id="b9cda-139">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="b9cda-140">source-file</span><span class="sxs-lookup"><span data-stu-id="b9cda-140">source-file</span></span> | <span data-ttu-id="b9cda-141">要创建的引用的源。</span><span class="sxs-lookup"><span data-stu-id="b9cda-141">The source to create a reference from.</span></span> <span data-ttu-id="b9cda-142">必须为 OpenAPI 文件。</span><span class="sxs-lookup"><span data-stu-id="b9cda-142">Must be an OpenAPI file.</span></span> |<span data-ttu-id="b9cda-143">dotnet openapi add file .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="b9cda-143">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="b9cda-144">添加 URL</span><span class="sxs-lookup"><span data-stu-id="b9cda-144">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="b9cda-145">选项</span><span class="sxs-lookup"><span data-stu-id="b9cda-145">Options</span></span>

| <span data-ttu-id="b9cda-146">短选项</span><span class="sxs-lookup"><span data-stu-id="b9cda-146">Short option</span></span>| <span data-ttu-id="b9cda-147">长选项</span><span class="sxs-lookup"><span data-stu-id="b9cda-147">Long option</span></span>| <span data-ttu-id="b9cda-148">说明</span><span class="sxs-lookup"><span data-stu-id="b9cda-148">Description</span></span> | <span data-ttu-id="b9cda-149">示例</span><span class="sxs-lookup"><span data-stu-id="b9cda-149">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="b9cda-150">-v</span><span class="sxs-lookup"><span data-stu-id="b9cda-150">-v</span></span>|<span data-ttu-id="b9cda-151">--verbose</span><span class="sxs-lookup"><span data-stu-id="b9cda-151">--verbose</span></span> | <span data-ttu-id="b9cda-152">显示详细输出。</span><span class="sxs-lookup"><span data-stu-id="b9cda-152">Show verbose output.</span></span> |<span data-ttu-id="b9cda-153">dotnet openapi add url -v `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="b9cda-153">dotnet openapi add url *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="b9cda-154">-p</span><span class="sxs-lookup"><span data-stu-id="b9cda-154">-p</span></span>|<span data-ttu-id="b9cda-155">--updateProject</span><span class="sxs-lookup"><span data-stu-id="b9cda-155">--updateProject</span></span> | <span data-ttu-id="b9cda-156">要操作的项目。</span><span class="sxs-lookup"><span data-stu-id="b9cda-156">The project to operate on.</span></span> |<span data-ttu-id="b9cda-157">dotnet openapi add url --updateProject .\Ref.csproj `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="b9cda-157">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="b9cda-158">-o</span><span class="sxs-lookup"><span data-stu-id="b9cda-158">-o</span></span>|<span data-ttu-id="b9cda-159">--output-file</span><span class="sxs-lookup"><span data-stu-id="b9cda-159">--output-file</span></span> | <span data-ttu-id="b9cda-160">用于放置 OpenAPI 文件本地副本的位置。</span><span class="sxs-lookup"><span data-stu-id="b9cda-160">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="b9cda-161">dotnet openapi add url `https://contoso.com/openapi.json` --output-file myclient.json</span><span class="sxs-lookup"><span data-stu-id="b9cda-161">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="b9cda-162">-c</span><span class="sxs-lookup"><span data-stu-id="b9cda-162">-c</span></span>|<span data-ttu-id="b9cda-163">--code-generator</span><span class="sxs-lookup"><span data-stu-id="b9cda-163">--code-generator</span></span>| <span data-ttu-id="b9cda-164">应用于引用的代码生成器。</span><span class="sxs-lookup"><span data-stu-id="b9cda-164">The code generator to apply to the reference.</span></span> <span data-ttu-id="b9cda-165">选项包括 `NSwagCSharp` 和 `NSwagTypeScript`。</span><span class="sxs-lookup"><span data-stu-id="b9cda-165">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="b9cda-166">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="b9cda-166">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="b9cda-167">-h</span><span class="sxs-lookup"><span data-stu-id="b9cda-167">-h</span></span>|<span data-ttu-id="b9cda-168">--help</span><span class="sxs-lookup"><span data-stu-id="b9cda-168">--help</span></span>|<span data-ttu-id="b9cda-169">显示帮助信息</span><span class="sxs-lookup"><span data-stu-id="b9cda-169">Show help information</span></span>|<span data-ttu-id="b9cda-170">dotnet openapi add url --help</span><span class="sxs-lookup"><span data-stu-id="b9cda-170">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="b9cda-171">自变量</span><span class="sxs-lookup"><span data-stu-id="b9cda-171">Arguments</span></span>

|  <span data-ttu-id="b9cda-172">参数</span><span class="sxs-lookup"><span data-stu-id="b9cda-172">Argument</span></span>  | <span data-ttu-id="b9cda-173">说明</span><span class="sxs-lookup"><span data-stu-id="b9cda-173">Description</span></span> | <span data-ttu-id="b9cda-174">示例</span><span class="sxs-lookup"><span data-stu-id="b9cda-174">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="b9cda-175">source-URL</span><span class="sxs-lookup"><span data-stu-id="b9cda-175">source-URL</span></span> | <span data-ttu-id="b9cda-176">要创建的引用的源。</span><span class="sxs-lookup"><span data-stu-id="b9cda-176">The source to create a reference from.</span></span> <span data-ttu-id="b9cda-177">必须是 URL。</span><span class="sxs-lookup"><span data-stu-id="b9cda-177">Must be a URL.</span></span> |<span data-ttu-id="b9cda-178">dotnet openapi add url `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="b9cda-178">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="b9cda-179">删除</span><span class="sxs-lookup"><span data-stu-id="b9cda-179">Remove</span></span>

<span data-ttu-id="b9cda-180">删除与 .csproj 文件中给定文件名匹配的 OpenAPI 引用。</span><span class="sxs-lookup"><span data-stu-id="b9cda-180">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="b9cda-181">删除 OpenAPI 引用后，将不会生成客户端。</span><span class="sxs-lookup"><span data-stu-id="b9cda-181">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="b9cda-182">将删除本地 .json 和 .yaml 文件。</span><span class="sxs-lookup"><span data-stu-id="b9cda-182">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="b9cda-183">选项</span><span class="sxs-lookup"><span data-stu-id="b9cda-183">Options</span></span>

| <span data-ttu-id="b9cda-184">短选项</span><span class="sxs-lookup"><span data-stu-id="b9cda-184">Short option</span></span>| <span data-ttu-id="b9cda-185">长选项</span><span class="sxs-lookup"><span data-stu-id="b9cda-185">Long option</span></span>| <span data-ttu-id="b9cda-186">说明</span><span class="sxs-lookup"><span data-stu-id="b9cda-186">Description</span></span>| <span data-ttu-id="b9cda-187">示例</span><span class="sxs-lookup"><span data-stu-id="b9cda-187">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="b9cda-188">-v</span><span class="sxs-lookup"><span data-stu-id="b9cda-188">-v</span></span>|<span data-ttu-id="b9cda-189">--verbose</span><span class="sxs-lookup"><span data-stu-id="b9cda-189">--verbose</span></span> | <span data-ttu-id="b9cda-190">显示详细输出。</span><span class="sxs-lookup"><span data-stu-id="b9cda-190">Show verbose output.</span></span> |<span data-ttu-id="b9cda-191">dotnet openapi remove -v</span><span class="sxs-lookup"><span data-stu-id="b9cda-191">dotnet openapi remove *-v*</span></span>|
| <span data-ttu-id="b9cda-192">-p</span><span class="sxs-lookup"><span data-stu-id="b9cda-192">-p</span></span>|<span data-ttu-id="b9cda-193">--updateProject</span><span class="sxs-lookup"><span data-stu-id="b9cda-193">--updateProject</span></span> | <span data-ttu-id="b9cda-194">要操作的项目。</span><span class="sxs-lookup"><span data-stu-id="b9cda-194">The project to operate on.</span></span> |<span data-ttu-id="b9cda-195">dotnet openapi remove --updateProject .\Ref.csproj .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="b9cda-195">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="b9cda-196">-h</span><span class="sxs-lookup"><span data-stu-id="b9cda-196">-h</span></span>|<span data-ttu-id="b9cda-197">--help</span><span class="sxs-lookup"><span data-stu-id="b9cda-197">--help</span></span>|<span data-ttu-id="b9cda-198">显示帮助信息</span><span class="sxs-lookup"><span data-stu-id="b9cda-198">Show help information</span></span>|<span data-ttu-id="b9cda-199">dotnet openapi remove --help</span><span class="sxs-lookup"><span data-stu-id="b9cda-199">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="b9cda-200">自变量</span><span class="sxs-lookup"><span data-stu-id="b9cda-200">Arguments</span></span>

|  <span data-ttu-id="b9cda-201">参数</span><span class="sxs-lookup"><span data-stu-id="b9cda-201">Argument</span></span>  | <span data-ttu-id="b9cda-202">说明</span><span class="sxs-lookup"><span data-stu-id="b9cda-202">Description</span></span>| <span data-ttu-id="b9cda-203">示例</span><span class="sxs-lookup"><span data-stu-id="b9cda-203">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="b9cda-204">source-file</span><span class="sxs-lookup"><span data-stu-id="b9cda-204">source-file</span></span> | <span data-ttu-id="b9cda-205">要删除的引用的源。</span><span class="sxs-lookup"><span data-stu-id="b9cda-205">The source to remove the reference to.</span></span> |<span data-ttu-id="b9cda-206">dotnet openapi remove .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="b9cda-206">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="b9cda-207">刷新</span><span class="sxs-lookup"><span data-stu-id="b9cda-207">Refresh</span></span>

<span data-ttu-id="b9cda-208">使用下载 URL 中的最新内容刷新已下载的文件本地版本。</span><span class="sxs-lookup"><span data-stu-id="b9cda-208">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="b9cda-209">选项</span><span class="sxs-lookup"><span data-stu-id="b9cda-209">Options</span></span>

| <span data-ttu-id="b9cda-210">短选项</span><span class="sxs-lookup"><span data-stu-id="b9cda-210">Short option</span></span>| <span data-ttu-id="b9cda-211">长选项</span><span class="sxs-lookup"><span data-stu-id="b9cda-211">Long option</span></span>| <span data-ttu-id="b9cda-212">说明</span><span class="sxs-lookup"><span data-stu-id="b9cda-212">Description</span></span> | <span data-ttu-id="b9cda-213">示例</span><span class="sxs-lookup"><span data-stu-id="b9cda-213">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="b9cda-214">-v</span><span class="sxs-lookup"><span data-stu-id="b9cda-214">-v</span></span>|<span data-ttu-id="b9cda-215">--verbose</span><span class="sxs-lookup"><span data-stu-id="b9cda-215">--verbose</span></span> | <span data-ttu-id="b9cda-216">显示详细输出。</span><span class="sxs-lookup"><span data-stu-id="b9cda-216">Show verbose output.</span></span> | <span data-ttu-id="b9cda-217">dotnet openapi refresh -v `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="b9cda-217">dotnet openapi refresh *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="b9cda-218">-p</span><span class="sxs-lookup"><span data-stu-id="b9cda-218">-p</span></span>|<span data-ttu-id="b9cda-219">--updateProject</span><span class="sxs-lookup"><span data-stu-id="b9cda-219">--updateProject</span></span> | <span data-ttu-id="b9cda-220">要操作的项目。</span><span class="sxs-lookup"><span data-stu-id="b9cda-220">The project to operate on.</span></span> | <span data-ttu-id="b9cda-221">dotnet openapi refresh --updateProject .\Ref.csproj `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="b9cda-221">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="b9cda-222">-h</span><span class="sxs-lookup"><span data-stu-id="b9cda-222">-h</span></span>|<span data-ttu-id="b9cda-223">--help</span><span class="sxs-lookup"><span data-stu-id="b9cda-223">--help</span></span>|<span data-ttu-id="b9cda-224">显示帮助信息</span><span class="sxs-lookup"><span data-stu-id="b9cda-224">Show help information</span></span>|<span data-ttu-id="b9cda-225">dotnet openapi refresh --help</span><span class="sxs-lookup"><span data-stu-id="b9cda-225">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="b9cda-226">自变量</span><span class="sxs-lookup"><span data-stu-id="b9cda-226">Arguments</span></span>

|  <span data-ttu-id="b9cda-227">参数</span><span class="sxs-lookup"><span data-stu-id="b9cda-227">Argument</span></span>  | <span data-ttu-id="b9cda-228">说明</span><span class="sxs-lookup"><span data-stu-id="b9cda-228">Description</span></span> | <span data-ttu-id="b9cda-229">示例</span><span class="sxs-lookup"><span data-stu-id="b9cda-229">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="b9cda-230">source-URL</span><span class="sxs-lookup"><span data-stu-id="b9cda-230">source-URL</span></span> | <span data-ttu-id="b9cda-231">用于刷新引用的 URL。</span><span class="sxs-lookup"><span data-stu-id="b9cda-231">The URL to refresh the reference from.</span></span> | <span data-ttu-id="b9cda-232">dotnet openapi refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="b9cda-232">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
