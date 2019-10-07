---
title: 使用 OpenAPI 开发 ASP.NET Core 应用
author: ryanbrandenburg
description: 演示如何使用“Microsoft.dotnet-openapi”工具添加 OpenAPI 文件引用。
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: f5eae9e871bc8efc30d500769adb845ff244a90c
ms.sourcegitcommit: e644258c95dd50a82284f107b9bf3becbc43b2b2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317774"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="6d4e5-103">使用 OpenAPI 工具开发 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="6d4e5-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="6d4e5-104">作者：Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="6d4e5-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="6d4e5-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) 是用于管理项目内 [OpenAPI](https://github.com/OAI/OpenAPI-Specification) 引用的 [.NET Core 全局工具](/dotnet/core/tools/global-tools)。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="6d4e5-106">安装</span><span class="sxs-lookup"><span data-stu-id="6d4e5-106">Installation</span></span>

<span data-ttu-id="6d4e5-107">若要安装 `Microsoft.dotnet-openapi`，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6d4e5-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="6d4e5-108">添加</span><span class="sxs-lookup"><span data-stu-id="6d4e5-108">Add</span></span>

<span data-ttu-id="6d4e5-109">使用本页上的任意一个命令添加 OpenAPI 引用，将向 .csproj 文件添加如下所示的 `<OpenApiReference />` 元素  ：</span><span class="sxs-lookup"><span data-stu-id="6d4e5-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="6d4e5-110">必须有上述引用，应用才可以调用生成的客户端代码。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-110">The preceding reference is required for the app to call the generated client code.</span></span>

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

### <a name="add-file"></a><span data-ttu-id="6d4e5-111">添加文件</span><span class="sxs-lookup"><span data-stu-id="6d4e5-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="6d4e5-112">选项</span><span class="sxs-lookup"><span data-stu-id="6d4e5-112">Options</span></span>

| <span data-ttu-id="6d4e5-113">短选项</span><span class="sxs-lookup"><span data-stu-id="6d4e5-113">Short option</span></span>| <span data-ttu-id="6d4e5-114">长选项</span><span class="sxs-lookup"><span data-stu-id="6d4e5-114">Long option</span></span>| <span data-ttu-id="6d4e5-115">说明</span><span class="sxs-lookup"><span data-stu-id="6d4e5-115">Description</span></span> | <span data-ttu-id="6d4e5-116">示例</span><span class="sxs-lookup"><span data-stu-id="6d4e5-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="6d4e5-117">-v</span><span class="sxs-lookup"><span data-stu-id="6d4e5-117">-v</span></span>|<span data-ttu-id="6d4e5-118">--verbose</span><span class="sxs-lookup"><span data-stu-id="6d4e5-118">--verbose</span></span> | <span data-ttu-id="6d4e5-119">显示详细输出。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-119">Show verbose output.</span></span> |<span data-ttu-id="6d4e5-120">dotnet openapi add file -v  .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="6d4e5-120">dotnet openapi add file *-v* .\OpenAPI.json</span></span> |
| <span data-ttu-id="6d4e5-121">-p</span><span class="sxs-lookup"><span data-stu-id="6d4e5-121">-p</span></span>|<span data-ttu-id="6d4e5-122">--updateProject</span><span class="sxs-lookup"><span data-stu-id="6d4e5-122">--updateProject</span></span> | <span data-ttu-id="6d4e5-123">要操作的项目。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-123">The project to operate on.</span></span> |<span data-ttu-id="6d4e5-124">dotnet openapi add file --updateProject .\Ref.csproj  .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="6d4e5-124">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="6d4e5-125">-c</span><span class="sxs-lookup"><span data-stu-id="6d4e5-125">-c</span></span>|<span data-ttu-id="6d4e5-126">--code-generator</span><span class="sxs-lookup"><span data-stu-id="6d4e5-126">--code-generator</span></span>| <span data-ttu-id="6d4e5-127">应用于引用的代码生成器。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-127">The code generator to apply to the reference.</span></span> <span data-ttu-id="6d4e5-128">选项包括 `NSwagCSharp` 和 `NSwagTypeScript`。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-128">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="6d4e5-129">如果未指定 `--code-generator`，则工具将默认为 `NSwagCSharp`。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-129">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="6d4e5-130">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="6d4e5-130">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="6d4e5-131">-h</span><span class="sxs-lookup"><span data-stu-id="6d4e5-131">-h</span></span>|<span data-ttu-id="6d4e5-132">--help</span><span class="sxs-lookup"><span data-stu-id="6d4e5-132">--help</span></span>|<span data-ttu-id="6d4e5-133">显示帮助信息</span><span class="sxs-lookup"><span data-stu-id="6d4e5-133">Show help information</span></span>|<span data-ttu-id="6d4e5-134">dotnet openapi add file --help</span><span class="sxs-lookup"><span data-stu-id="6d4e5-134">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="6d4e5-135">自变量</span><span class="sxs-lookup"><span data-stu-id="6d4e5-135">Arguments</span></span>

|  <span data-ttu-id="6d4e5-136">参数</span><span class="sxs-lookup"><span data-stu-id="6d4e5-136">Argument</span></span>  | <span data-ttu-id="6d4e5-137">说明</span><span class="sxs-lookup"><span data-stu-id="6d4e5-137">Description</span></span> | <span data-ttu-id="6d4e5-138">示例</span><span class="sxs-lookup"><span data-stu-id="6d4e5-138">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="6d4e5-139">source-file</span><span class="sxs-lookup"><span data-stu-id="6d4e5-139">source-file</span></span> | <span data-ttu-id="6d4e5-140">要创建的引用的源。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-140">The source to create a reference from.</span></span> <span data-ttu-id="6d4e5-141">必须为 OpenAPI 文件。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-141">Must be an OpenAPI file.</span></span> |<span data-ttu-id="6d4e5-142">dotnet openapi add file .\OpenAPI.json </span><span class="sxs-lookup"><span data-stu-id="6d4e5-142">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="6d4e5-143">添加 URL</span><span class="sxs-lookup"><span data-stu-id="6d4e5-143">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="6d4e5-144">选项</span><span class="sxs-lookup"><span data-stu-id="6d4e5-144">Options</span></span>

| <span data-ttu-id="6d4e5-145">短选项</span><span class="sxs-lookup"><span data-stu-id="6d4e5-145">Short option</span></span>| <span data-ttu-id="6d4e5-146">长选项</span><span class="sxs-lookup"><span data-stu-id="6d4e5-146">Long option</span></span>| <span data-ttu-id="6d4e5-147">说明</span><span class="sxs-lookup"><span data-stu-id="6d4e5-147">Description</span></span> | <span data-ttu-id="6d4e5-148">示例</span><span class="sxs-lookup"><span data-stu-id="6d4e5-148">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="6d4e5-149">-v</span><span class="sxs-lookup"><span data-stu-id="6d4e5-149">-v</span></span>|<span data-ttu-id="6d4e5-150">--verbose</span><span class="sxs-lookup"><span data-stu-id="6d4e5-150">--verbose</span></span> | <span data-ttu-id="6d4e5-151">显示详细输出。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-151">Show verbose output.</span></span> |<span data-ttu-id="6d4e5-152">dotnet openapi add url -v  `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="6d4e5-152">dotnet openapi add url *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="6d4e5-153">-p</span><span class="sxs-lookup"><span data-stu-id="6d4e5-153">-p</span></span>|<span data-ttu-id="6d4e5-154">--updateProject</span><span class="sxs-lookup"><span data-stu-id="6d4e5-154">--updateProject</span></span> | <span data-ttu-id="6d4e5-155">要操作的项目。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-155">The project to operate on.</span></span> |<span data-ttu-id="6d4e5-156">dotnet openapi add url --updateProject .\Ref.csproj  `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="6d4e5-156">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="6d4e5-157">-o</span><span class="sxs-lookup"><span data-stu-id="6d4e5-157">-o</span></span>|<span data-ttu-id="6d4e5-158">--output-file</span><span class="sxs-lookup"><span data-stu-id="6d4e5-158">--output-file</span></span> | <span data-ttu-id="6d4e5-159">用于放置 OpenAPI 文件本地副本的位置。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-159">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="6d4e5-160">dotnet openapi add url `https://contoso.com/openapi.json` --output-file myclient.json </span><span class="sxs-lookup"><span data-stu-id="6d4e5-160">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="6d4e5-161">-c</span><span class="sxs-lookup"><span data-stu-id="6d4e5-161">-c</span></span>|<span data-ttu-id="6d4e5-162">--code-generator</span><span class="sxs-lookup"><span data-stu-id="6d4e5-162">--code-generator</span></span>| <span data-ttu-id="6d4e5-163">应用于引用的代码生成器。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-163">The code generator to apply to the reference.</span></span> <span data-ttu-id="6d4e5-164">选项包括 `NSwagCSharp` 和 `NSwagTypeScript`。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-164">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="6d4e5-165">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="6d4e5-165">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="6d4e5-166">-h</span><span class="sxs-lookup"><span data-stu-id="6d4e5-166">-h</span></span>|<span data-ttu-id="6d4e5-167">--help</span><span class="sxs-lookup"><span data-stu-id="6d4e5-167">--help</span></span>|<span data-ttu-id="6d4e5-168">显示帮助信息</span><span class="sxs-lookup"><span data-stu-id="6d4e5-168">Show help information</span></span>|<span data-ttu-id="6d4e5-169">dotnet openapi add url --help</span><span class="sxs-lookup"><span data-stu-id="6d4e5-169">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="6d4e5-170">自变量</span><span class="sxs-lookup"><span data-stu-id="6d4e5-170">Arguments</span></span>

|  <span data-ttu-id="6d4e5-171">参数</span><span class="sxs-lookup"><span data-stu-id="6d4e5-171">Argument</span></span>  | <span data-ttu-id="6d4e5-172">说明</span><span class="sxs-lookup"><span data-stu-id="6d4e5-172">Description</span></span> | <span data-ttu-id="6d4e5-173">示例</span><span class="sxs-lookup"><span data-stu-id="6d4e5-173">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="6d4e5-174">source-URL</span><span class="sxs-lookup"><span data-stu-id="6d4e5-174">source-URL</span></span> | <span data-ttu-id="6d4e5-175">要创建的引用的源。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-175">The source to create a reference from.</span></span> <span data-ttu-id="6d4e5-176">必须是 URL。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-176">Must be a URL.</span></span> |<span data-ttu-id="6d4e5-177">dotnet openapi add url `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="6d4e5-177">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="6d4e5-178">删除</span><span class="sxs-lookup"><span data-stu-id="6d4e5-178">Remove</span></span>

<span data-ttu-id="6d4e5-179">删除与 .csproj 文件中给定文件名匹配的 OpenAPI 引用。 </span><span class="sxs-lookup"><span data-stu-id="6d4e5-179">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="6d4e5-180">删除 OpenAPI 引用后，将不会生成客户端。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-180">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="6d4e5-181">将删除本地 .json 和 .yaml 文件。  </span><span class="sxs-lookup"><span data-stu-id="6d4e5-181">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="6d4e5-182">选项</span><span class="sxs-lookup"><span data-stu-id="6d4e5-182">Options</span></span>

| <span data-ttu-id="6d4e5-183">短选项</span><span class="sxs-lookup"><span data-stu-id="6d4e5-183">Short option</span></span>| <span data-ttu-id="6d4e5-184">长选项</span><span class="sxs-lookup"><span data-stu-id="6d4e5-184">Long option</span></span>| <span data-ttu-id="6d4e5-185">说明</span><span class="sxs-lookup"><span data-stu-id="6d4e5-185">Description</span></span>| <span data-ttu-id="6d4e5-186">示例</span><span class="sxs-lookup"><span data-stu-id="6d4e5-186">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="6d4e5-187">-v</span><span class="sxs-lookup"><span data-stu-id="6d4e5-187">-v</span></span>|<span data-ttu-id="6d4e5-188">--verbose</span><span class="sxs-lookup"><span data-stu-id="6d4e5-188">--verbose</span></span> | <span data-ttu-id="6d4e5-189">显示详细输出。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-189">Show verbose output.</span></span> |<span data-ttu-id="6d4e5-190">dotnet openapi remove -v </span><span class="sxs-lookup"><span data-stu-id="6d4e5-190">dotnet openapi remove *-v*</span></span>|
| <span data-ttu-id="6d4e5-191">-p</span><span class="sxs-lookup"><span data-stu-id="6d4e5-191">-p</span></span>|<span data-ttu-id="6d4e5-192">--updateProject</span><span class="sxs-lookup"><span data-stu-id="6d4e5-192">--updateProject</span></span> | <span data-ttu-id="6d4e5-193">要操作的项目。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-193">The project to operate on.</span></span> |<span data-ttu-id="6d4e5-194">dotnet openapi remove --updateProject .\Ref.csproj  .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="6d4e5-194">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="6d4e5-195">-h</span><span class="sxs-lookup"><span data-stu-id="6d4e5-195">-h</span></span>|<span data-ttu-id="6d4e5-196">--help</span><span class="sxs-lookup"><span data-stu-id="6d4e5-196">--help</span></span>|<span data-ttu-id="6d4e5-197">显示帮助信息</span><span class="sxs-lookup"><span data-stu-id="6d4e5-197">Show help information</span></span>|<span data-ttu-id="6d4e5-198">dotnet openapi remove --help</span><span class="sxs-lookup"><span data-stu-id="6d4e5-198">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="6d4e5-199">自变量</span><span class="sxs-lookup"><span data-stu-id="6d4e5-199">Arguments</span></span>

|  <span data-ttu-id="6d4e5-200">参数</span><span class="sxs-lookup"><span data-stu-id="6d4e5-200">Argument</span></span>  | <span data-ttu-id="6d4e5-201">说明</span><span class="sxs-lookup"><span data-stu-id="6d4e5-201">Description</span></span>| <span data-ttu-id="6d4e5-202">示例</span><span class="sxs-lookup"><span data-stu-id="6d4e5-202">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="6d4e5-203">source-file</span><span class="sxs-lookup"><span data-stu-id="6d4e5-203">source-file</span></span> | <span data-ttu-id="6d4e5-204">要删除的引用的源。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-204">The source to remove the reference to.</span></span> |<span data-ttu-id="6d4e5-205">dotnet openapi remove .\OpenAPI.json </span><span class="sxs-lookup"><span data-stu-id="6d4e5-205">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="6d4e5-206">刷新</span><span class="sxs-lookup"><span data-stu-id="6d4e5-206">Refresh</span></span>

<span data-ttu-id="6d4e5-207">使用下载 URL 中的最新内容刷新已下载的文件本地版本。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-207">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="6d4e5-208">选项</span><span class="sxs-lookup"><span data-stu-id="6d4e5-208">Options</span></span>

| <span data-ttu-id="6d4e5-209">短选项</span><span class="sxs-lookup"><span data-stu-id="6d4e5-209">Short option</span></span>| <span data-ttu-id="6d4e5-210">长选项</span><span class="sxs-lookup"><span data-stu-id="6d4e5-210">Long option</span></span>| <span data-ttu-id="6d4e5-211">说明</span><span class="sxs-lookup"><span data-stu-id="6d4e5-211">Description</span></span> | <span data-ttu-id="6d4e5-212">示例</span><span class="sxs-lookup"><span data-stu-id="6d4e5-212">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="6d4e5-213">-v</span><span class="sxs-lookup"><span data-stu-id="6d4e5-213">-v</span></span>|<span data-ttu-id="6d4e5-214">--verbose</span><span class="sxs-lookup"><span data-stu-id="6d4e5-214">--verbose</span></span> | <span data-ttu-id="6d4e5-215">显示详细输出。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-215">Show verbose output.</span></span> | <span data-ttu-id="6d4e5-216">dotnet openapi refresh -v  `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="6d4e5-216">dotnet openapi refresh *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="6d4e5-217">-p</span><span class="sxs-lookup"><span data-stu-id="6d4e5-217">-p</span></span>|<span data-ttu-id="6d4e5-218">--updateProject</span><span class="sxs-lookup"><span data-stu-id="6d4e5-218">--updateProject</span></span> | <span data-ttu-id="6d4e5-219">要操作的项目。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-219">The project to operate on.</span></span> | <span data-ttu-id="6d4e5-220">dotnet openapi refresh --updateProject .\Ref.csproj  `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="6d4e5-220">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="6d4e5-221">-h</span><span class="sxs-lookup"><span data-stu-id="6d4e5-221">-h</span></span>|<span data-ttu-id="6d4e5-222">--help</span><span class="sxs-lookup"><span data-stu-id="6d4e5-222">--help</span></span>|<span data-ttu-id="6d4e5-223">显示帮助信息</span><span class="sxs-lookup"><span data-stu-id="6d4e5-223">Show help information</span></span>|<span data-ttu-id="6d4e5-224">dotnet openapi refresh --help</span><span class="sxs-lookup"><span data-stu-id="6d4e5-224">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="6d4e5-225">自变量</span><span class="sxs-lookup"><span data-stu-id="6d4e5-225">Arguments</span></span>

|  <span data-ttu-id="6d4e5-226">参数</span><span class="sxs-lookup"><span data-stu-id="6d4e5-226">Argument</span></span>  | <span data-ttu-id="6d4e5-227">说明</span><span class="sxs-lookup"><span data-stu-id="6d4e5-227">Description</span></span> | <span data-ttu-id="6d4e5-228">示例</span><span class="sxs-lookup"><span data-stu-id="6d4e5-228">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="6d4e5-229">source-URL</span><span class="sxs-lookup"><span data-stu-id="6d4e5-229">source-URL</span></span> | <span data-ttu-id="6d4e5-230">用于刷新引用的 URL。</span><span class="sxs-lookup"><span data-stu-id="6d4e5-230">The URL to refresh the reference from.</span></span> | <span data-ttu-id="6d4e5-231">dotnet openapi refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="6d4e5-231">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
