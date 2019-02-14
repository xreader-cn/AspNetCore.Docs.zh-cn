---
title: 通过 ASP.NET Core 使用 Angular 项目模板
author: SteveSandersonMS
description: 了解如何开始使用适用于 Angular 和 Angular CLI 的 ASP.NET Core 单页应用程序 (SPA) 项目模板。
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 02/13/2019
uid: spa/angular
ms.openlocfilehash: 35a839e31369e8dbf00f5dbfb3751a2985335755
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248116"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a><span data-ttu-id="40a69-103">通过 ASP.NET Core 使用 Angular 项目模板</span><span class="sxs-lookup"><span data-stu-id="40a69-103">Use the Angular project template with ASP.NET Core</span></span>

<span data-ttu-id="40a69-104">更新的 Angular 项目模板为使用 Angular 和 Angular CLI 实现丰富的客户端用户界面 (UI) 的 ASP.NET Core 应用提供了便捷起点。</span><span class="sxs-lookup"><span data-stu-id="40a69-104">The updated Angular project template provides a convenient starting point for ASP.NET Core apps using Angular and the Angular CLI to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="40a69-105">该模板等同于创建用作 API 后端的 ASP.NET Core 项目，而 Angular CLI 项目用作 UI。</span><span class="sxs-lookup"><span data-stu-id="40a69-105">The template is equivalent to creating an ASP.NET Core project to act as an API backend and an Angular CLI project to act as a UI.</span></span> <span data-ttu-id="40a69-106">该模板提供在单个应用项目中托管两种项目类型的便利。</span><span class="sxs-lookup"><span data-stu-id="40a69-106">The template offers the convenience of hosting both project types in a single app project.</span></span> <span data-ttu-id="40a69-107">因此，应用项目可以作为单个单元来生成和发布。</span><span class="sxs-lookup"><span data-stu-id="40a69-107">Consequently, the app project can be built and published as a single unit.</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="40a69-108">创建新应用</span><span class="sxs-lookup"><span data-stu-id="40a69-108">Create a new app</span></span>

<span data-ttu-id="40a69-109">如果您有安装 ASP.NET Core 2.1，则无需安装角度项目模板。</span><span class="sxs-lookup"><span data-stu-id="40a69-109">If you have ASP.NET Core 2.1 installed, there's no need to install the Angular project template.</span></span>

<span data-ttu-id="40a69-110">在空目录中使用命令 `dotnet new angular` 从命令提示符创建一个新项目。</span><span class="sxs-lookup"><span data-stu-id="40a69-110">Create a new project from a command prompt using the command `dotnet new angular` in an empty directory.</span></span> <span data-ttu-id="40a69-111">例如，以下命令在 my-new-app 目录中创建应用并切换到该目录：</span><span class="sxs-lookup"><span data-stu-id="40a69-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```console
dotnet new angular -o my-new-app
cd my-new-app
```

<span data-ttu-id="40a69-112">从 Visual Studio 或 .NET Core CLI 运行应用：</span><span class="sxs-lookup"><span data-stu-id="40a69-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="40a69-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40a69-113">Visual Studio</span></span>](#tab/visual-studio/)

<span data-ttu-id="40a69-114">打开生成的 .csproj 文件，并从此文件正常运行应用。</span><span class="sxs-lookup"><span data-stu-id="40a69-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="40a69-115">生成过程会在首次运行时还原 npm 依赖关系，这可能需要几分钟的时间。</span><span class="sxs-lookup"><span data-stu-id="40a69-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="40a69-116">后续版本要快得多。</span><span class="sxs-lookup"><span data-stu-id="40a69-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="40a69-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="40a69-117">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="40a69-118">确保你有一个名为 `ASPNETCORE_Environment` 的环境变量，其值为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="40a69-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with a value of `Development`.</span></span> <span data-ttu-id="40a69-119">在 Windows 上（在非 PowerShell 提示下）运行 `SET ASPNETCORE_Environment=Development`。</span><span class="sxs-lookup"><span data-stu-id="40a69-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="40a69-120">在 Linux 或 macOS 上运行 `export ASPNETCORE_Environment=Development`。</span><span class="sxs-lookup"><span data-stu-id="40a69-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="40a69-121">运行 [dotnet build](/dotnet/core/tools/dotnet-build) 以验证应用是否正确生成。</span><span class="sxs-lookup"><span data-stu-id="40a69-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify the app builds correctly.</span></span> <span data-ttu-id="40a69-122">首次运行时，生成过程会还原 npm 依赖关系，这可能需要几分钟的时间。</span><span class="sxs-lookup"><span data-stu-id="40a69-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="40a69-123">后续版本要快得多。</span><span class="sxs-lookup"><span data-stu-id="40a69-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="40a69-124">运行 [dotnet run](/dotnet/core/tools/dotnet-run) 以启动应用。</span><span class="sxs-lookup"><span data-stu-id="40a69-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span> <span data-ttu-id="40a69-125">记录类似于以下内容的消息：</span><span class="sxs-lookup"><span data-stu-id="40a69-125">A message similar to the following is logged:</span></span>

```console
Now listening on: http://localhost:<port>
```

<span data-ttu-id="40a69-126">在浏览器中导航到此 URL。</span><span class="sxs-lookup"><span data-stu-id="40a69-126">Navigate to this URL in a browser.</span></span>

<span data-ttu-id="40a69-127">该应用在后台启动 Angular CLI 服务器的一个实例。</span><span class="sxs-lookup"><span data-stu-id="40a69-127">The app starts up an instance of the Angular CLI server in the background.</span></span> <span data-ttu-id="40a69-128">记录类似于以下内容的消息：*NG Live 开发服务器正在侦听 localhost:&lt;otherport&gt;，打开浏览器上 http://localhost:&lt; otherport&gt;/*。</span><span class="sxs-lookup"><span data-stu-id="40a69-128">A message similar to the following is logged: *NG Live Development Server is listening on localhost:&lt;otherport&gt;, open your browser on http://localhost:&lt;otherport&gt;/*.</span></span> <span data-ttu-id="40a69-129">忽略此消息&mdash;这**不是**组合 ASP.NET Core 和 Angular CLI 应用的 URL。</span><span class="sxs-lookup"><span data-stu-id="40a69-129">Ignore this message&mdash;it's **not** the URL for the combined ASP.NET Core and Angular CLI app.</span></span>

---

<span data-ttu-id="40a69-130">该项目模板创建一个 ASP.NET Core 应用和一个 Angular 应用。</span><span class="sxs-lookup"><span data-stu-id="40a69-130">The project template creates an ASP.NET Core app and an Angular app.</span></span> <span data-ttu-id="40a69-131">ASP.NET Core 应用旨在用于数据访问、授权和其他服务器端问题。</span><span class="sxs-lookup"><span data-stu-id="40a69-131">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="40a69-132">位于 ClientApp 子目录中的 Angular 应用旨在用于所有 UI 问题。</span><span class="sxs-lookup"><span data-stu-id="40a69-132">The Angular app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="40a69-133">添加页面、映像、样式、模块等。</span><span class="sxs-lookup"><span data-stu-id="40a69-133">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="40a69-134">ClientApp 目录包含标准的 Angular CLI 应用。</span><span class="sxs-lookup"><span data-stu-id="40a69-134">The *ClientApp* directory contains a standard Angular CLI app.</span></span> <span data-ttu-id="40a69-135">有关详细信息，请参阅官方 [Angular 文档](https://github.com/angular/angular-cli/wiki)。</span><span class="sxs-lookup"><span data-stu-id="40a69-135">See the official [Angular documentation](https://github.com/angular/angular-cli/wiki) for more information.</span></span>

<span data-ttu-id="40a69-136">此模板创建的 Angular 应用与 Angular CLI 本身创建的应用（通过 `ng new`）之间存在细微差异；但是，该应用的功能未变。</span><span class="sxs-lookup"><span data-stu-id="40a69-136">There are slight differences between the Angular app created by this template and the one created by Angular CLI itself (via `ng new`); however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="40a69-137">该模板创建的应用包含基于 [Bootstrap](https://getbootstrap.com/) 的布局和基本路由示例。</span><span class="sxs-lookup"><span data-stu-id="40a69-137">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="run-ng-commands"></a><span data-ttu-id="40a69-138">运行 ng 命令</span><span class="sxs-lookup"><span data-stu-id="40a69-138">Run ng commands</span></span>

<span data-ttu-id="40a69-139">在命令提示符中，切换到 ClientApp 子目录：</span><span class="sxs-lookup"><span data-stu-id="40a69-139">In a command prompt, switch to the *ClientApp* subdirectory:</span></span>

```console
cd ClientApp
```

<span data-ttu-id="40a69-140">如果全局安装了 `ng` 工具，则可以运行其任何命令。</span><span class="sxs-lookup"><span data-stu-id="40a69-140">If you have the `ng` tool installed globally, you can run any of its commands.</span></span> <span data-ttu-id="40a69-141">例如，你可以运行 `ng lint`、`ng test` 或任何其他 [Angular CLI 命令](https://github.com/angular/angular-cli/wiki#additional-commands)。</span><span class="sxs-lookup"><span data-stu-id="40a69-141">For example, you can run `ng lint`, `ng test`, or any of the other [Angular CLI commands](https://github.com/angular/angular-cli/wiki#additional-commands).</span></span> <span data-ttu-id="40a69-142">不过无需运行 `ng serve`，因为 ASP.NET Core 应用为应用的服务器端和客户端部分提供服务。</span><span class="sxs-lookup"><span data-stu-id="40a69-142">There's no need to run `ng serve` though, because your ASP.NET Core app deals with serving both server-side and client-side parts of your app.</span></span> <span data-ttu-id="40a69-143">在内部，它在开发中使用 `ng serve`。</span><span class="sxs-lookup"><span data-stu-id="40a69-143">Internally, it uses `ng serve` in development.</span></span>

<span data-ttu-id="40a69-144">如果未安装 `ng` 工具，请改为运行 `npm run ng`。</span><span class="sxs-lookup"><span data-stu-id="40a69-144">If you don't have the `ng` tool installed, run `npm run ng` instead.</span></span> <span data-ttu-id="40a69-145">例如，你可以运行 `npm run ng lint` 或 `npm run ng test`。</span><span class="sxs-lookup"><span data-stu-id="40a69-145">For example, you can run `npm run ng lint` or `npm run ng test`.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="40a69-146">安装 npm 包</span><span class="sxs-lookup"><span data-stu-id="40a69-146">Install npm packages</span></span>

<span data-ttu-id="40a69-147">要安装第三方 npm 程序包，请使用 ClientApp 子目录中的命令提示符。</span><span class="sxs-lookup"><span data-stu-id="40a69-147">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="40a69-148">例如：</span><span class="sxs-lookup"><span data-stu-id="40a69-148">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="40a69-149">发布和部署</span><span class="sxs-lookup"><span data-stu-id="40a69-149">Publish and deploy</span></span>

<span data-ttu-id="40a69-150">在开发过程中，应用在为开发人员之便而优化的模式下运行。</span><span class="sxs-lookup"><span data-stu-id="40a69-150">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="40a69-151">例如，JavaScript 捆绑包包含源映射（这样在调试时可以查看原始 TypeScript 代码）。</span><span class="sxs-lookup"><span data-stu-id="40a69-151">For example, JavaScript bundles include source maps (so that when debugging, you can see your original TypeScript code).</span></span> <span data-ttu-id="40a69-152">该应用监视磁盘上的 TypeScript、HTML 和 CSS 文件更改，并在看到这些文件更改时自动重新编译和重新加载。</span><span class="sxs-lookup"><span data-stu-id="40a69-152">The app watches for TypeScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="40a69-153">在生产中，提供针对性能进行了优化的应用版本。</span><span class="sxs-lookup"><span data-stu-id="40a69-153">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="40a69-154">该操作被配置为自动发生。</span><span class="sxs-lookup"><span data-stu-id="40a69-154">This is configured to happen automatically.</span></span> <span data-ttu-id="40a69-155">发布时，生成配置会发出预先 (AoT) 编译的压缩客户端代码版本。</span><span class="sxs-lookup"><span data-stu-id="40a69-155">When you publish, the build configuration emits a minified, ahead-of-time (AoT) compiled build of your client-side code.</span></span> <span data-ttu-id="40a69-156">与开发版本不同，生产版本不需要在服务器上安装 Node.js（除非已启用[服务器端预呈现](#server-side-rendering)）。</span><span class="sxs-lookup"><span data-stu-id="40a69-156">Unlike the development build, the production build doesn't require Node.js to be installed on the server (unless you have enabled [server-side prerendering](#server-side-rendering)).</span></span>

<span data-ttu-id="40a69-157">你可以使用标准 [ASP.NET Core 托管和部署方法](xref:host-and-deploy/index)。</span><span class="sxs-lookup"><span data-stu-id="40a69-157">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-ng-serve-independently"></a><span data-ttu-id="40a69-158">独立运行“ng serve”</span><span class="sxs-lookup"><span data-stu-id="40a69-158">Run "ng serve" independently</span></span>

<span data-ttu-id="40a69-159">在 ASP.NET Core 应用以开发模式启动时，该项目配置为在后台启动自己的 Angular CLI 服务器实例。</span><span class="sxs-lookup"><span data-stu-id="40a69-159">The project is configured to start its own instance of the Angular CLI server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="40a69-160">这很方便，因为你无需手动运行单独的服务器。</span><span class="sxs-lookup"><span data-stu-id="40a69-160">This is convenient because you don't have to run a separate server manually.</span></span>

<span data-ttu-id="40a69-161">这种默认设置有一个缺点。</span><span class="sxs-lookup"><span data-stu-id="40a69-161">There's a drawback to this default setup.</span></span> <span data-ttu-id="40a69-162">每次修改 C# 代码并且需要重启 ASP.NET Core 应用时，Angular CLI 服务器都会重启。</span><span class="sxs-lookup"><span data-stu-id="40a69-162">Each time you modify your C# code and your ASP.NET Core app needs to restart, the Angular CLI server restarts.</span></span> <span data-ttu-id="40a69-163">大约需要 10 秒才能开始备份。</span><span class="sxs-lookup"><span data-stu-id="40a69-163">Around 10 seconds is required to start back up.</span></span> <span data-ttu-id="40a69-164">如果你经常进行 C# 代码编辑并且不希望等待 Angular CLI 重启，请在外部运行独立于 ASP.NET Core 进程的 Angular CLI 服务器。</span><span class="sxs-lookup"><span data-stu-id="40a69-164">If you're making frequent C# code edits and don't want to wait for Angular CLI to restart, run the Angular CLI server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="40a69-165">为此，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="40a69-165">To do so:</span></span>

1. <span data-ttu-id="40a69-166">在命令提示符中，切换到 ClientApp 子目录，并启动 Angular CLI 开发服务器：</span><span class="sxs-lookup"><span data-stu-id="40a69-166">In a command prompt, switch to the *ClientApp* subdirectory, and launch the Angular CLI development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > <span data-ttu-id="40a69-167">使用 `npm start` 而不是 `ng serve` 启动 Angular CLI 开发服务器，以便遵守 package.json 中的配置。</span><span class="sxs-lookup"><span data-stu-id="40a69-167">Use `npm start` to launch the Angular CLI development server, not `ng serve`, so that the configuration in *package.json* is respected.</span></span> <span data-ttu-id="40a69-168">要将其他参数传递给 Angular CLI 服务器，请将它们添加到 package.json 文件中的相关 `scripts` 行。</span><span class="sxs-lookup"><span data-stu-id="40a69-168">To pass additional parameters to the Angular CLI server, add them to the relevant `scripts` line in your *package.json* file.</span></span>

2. <span data-ttu-id="40a69-169">修改 ASP.NET Core 应用以使用外部 Angular CLI 实例，而不是启动它自己的实例。</span><span class="sxs-lookup"><span data-stu-id="40a69-169">Modify your ASP.NET Core app to use the external Angular CLI instance instead of launching one of its own.</span></span> <span data-ttu-id="40a69-170">在 Startup 类中，将 `spa.UseAngularCliServer` 调用替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="40a69-170">In your *Startup* class, replace the `spa.UseAngularCliServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

<span data-ttu-id="40a69-171">当启动 ASP.NET Core 应用时，它不会启动 Angular CLI 服务器，</span><span class="sxs-lookup"><span data-stu-id="40a69-171">When you start your ASP.NET Core app, it won't launch an Angular CLI server.</span></span> <span data-ttu-id="40a69-172">而是使用你手动启动的实例。</span><span class="sxs-lookup"><span data-stu-id="40a69-172">The instance you started manually is used instead.</span></span> <span data-ttu-id="40a69-173">这使它能够更快地启动和重新启动。</span><span class="sxs-lookup"><span data-stu-id="40a69-173">This enables it to start and restart faster.</span></span> <span data-ttu-id="40a69-174">不再需要每次等待 Angular CLI 重新生成客户端应用。</span><span class="sxs-lookup"><span data-stu-id="40a69-174">It's no longer waiting for Angular CLI to rebuild your client app each time.</span></span>

## <a name="server-side-rendering"></a><span data-ttu-id="40a69-175">服务器端呈现</span><span class="sxs-lookup"><span data-stu-id="40a69-175">Server-side rendering</span></span>

<span data-ttu-id="40a69-176">作为一项性能特性，你可以选择在服务器上预呈现 Angular 应用并在客户端运行它。</span><span class="sxs-lookup"><span data-stu-id="40a69-176">As a performance feature, you can choose to pre-render your Angular app on the server as well as running it on the client.</span></span> <span data-ttu-id="40a69-177">这意味着浏览器会收到表示应用初始 UI 的 HTML 标记，所以即使在下载并执行 JavaScript 捆绑包之前，浏览器也会显示它。</span><span class="sxs-lookup"><span data-stu-id="40a69-177">This means that browsers receive HTML markup representing your app's initial UI, so they display it even before downloading and executing your JavaScript bundles.</span></span> <span data-ttu-id="40a69-178">该操作的大部分实现过程由名为 [Angular Universal](https://universal.angular.io/) 的 Angular 功能完成。</span><span class="sxs-lookup"><span data-stu-id="40a69-178">Most of the implementation of this comes from an Angular feature called [Angular Universal](https://universal.angular.io/).</span></span>

> [!TIP]
> <span data-ttu-id="40a69-179">启用服务器端呈现 (SSR) 会在开发和部署期间引入一些额外的复杂问题。</span><span class="sxs-lookup"><span data-stu-id="40a69-179">Enabling server-side rendering (SSR) introduces a number of extra complications both during development and deployment.</span></span> <span data-ttu-id="40a69-180">阅读 [SSR 缺点](#drawbacks-of-ssr)，以确定 SSR 是否符合你的要求。</span><span class="sxs-lookup"><span data-stu-id="40a69-180">Read [drawbacks of SSR](#drawbacks-of-ssr) to determine if SSR is a good fit for your requirements.</span></span>

<span data-ttu-id="40a69-181">要启用 SSR，你需要将大量内容添加到项目。</span><span class="sxs-lookup"><span data-stu-id="40a69-181">To enable SSR, you need to make a number of additions to your project.</span></span>

<span data-ttu-id="40a69-182">在 Startup 类中，在配置 `spa.Options.SourcePath` 的行之后并且在调用 `UseAngularCliServer` 或 `UseProxyToSpaDevelopmentServer` 之前，添加以下内容：</span><span class="sxs-lookup"><span data-stu-id="40a69-182">In the *Startup* class, *after* the line that configures `spa.Options.SourcePath`, and *before* the call to `UseAngularCliServer` or `UseProxyToSpaDevelopmentServer`, add the following:</span></span>

[!code-csharp[](sample/AngularServerSideRendering/Startup.cs?name=snippet_Call_UseSpa&highlight=5-12)]

<span data-ttu-id="40a69-183">在开发模式下，此代码尝试通过运行在 ClientApp\package.json 中定义的脚本 `build:ssr` 来构建 SSR 捆绑包。</span><span class="sxs-lookup"><span data-stu-id="40a69-183">In development mode, this code attempts to build the SSR bundle by running the script `build:ssr`, which is defined in *ClientApp\package.json*.</span></span> <span data-ttu-id="40a69-184">这将生成一个名为 `ssr` 的 Angular 应用，该应用尚未定义。</span><span class="sxs-lookup"><span data-stu-id="40a69-184">This builds an Angular app named `ssr`, which isn't yet defined.</span></span>

<span data-ttu-id="40a69-185">在 ClientApp/.angular-cli.json 中 `apps` 数组的末尾，定义一个名为 `ssr` 的额外应用。</span><span class="sxs-lookup"><span data-stu-id="40a69-185">At the end of the `apps` array in *ClientApp/.angular-cli.json*, define an extra app with name `ssr`.</span></span> <span data-ttu-id="40a69-186">使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="40a69-186">Use the following options:</span></span>

[!code-json[](sample/AngularServerSideRendering/ClientApp/.angular-cli.json?range=24-41)]

<span data-ttu-id="40a69-187">启用了 SSR 的新应用配置需要另外两个文件：tsconfig.server.json 和 main.server.ts。</span><span class="sxs-lookup"><span data-stu-id="40a69-187">This new SSR-enabled app configuration requires two further files: *tsconfig.server.json* and *main.server.ts*.</span></span> <span data-ttu-id="40a69-188">tsconfig.server.json 文件指定 TypeScript 编译选项。</span><span class="sxs-lookup"><span data-stu-id="40a69-188">The *tsconfig.server.json* file specifies TypeScript compilation options.</span></span> <span data-ttu-id="40a69-189">main.server.ts 文件在 SSR 期间用作代码入口点。</span><span class="sxs-lookup"><span data-stu-id="40a69-189">The *main.server.ts* file serves as the code entry point during SSR.</span></span>

<span data-ttu-id="40a69-190">在 ClientApp/src （与现有 tsconfig.app.json 同时存在）中添加名为 tsconfig.server.json 的新文件，其中包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="40a69-190">Add a new file called *tsconfig.server.json* inside *ClientApp/src* (alongside the existing *tsconfig.app.json*), containing the following:</span></span>

[!code-json[](sample/AngularServerSideRendering/ClientApp/src/tsconfig.server.json)]

<span data-ttu-id="40a69-191">该文件将 Angular 的 AoT 编译器配置为查找名为 `app.server.module` 的模块。</span><span class="sxs-lookup"><span data-stu-id="40a69-191">This file configures Angular's AoT compiler to look for a module called `app.server.module`.</span></span> <span data-ttu-id="40a69-192">通过在 ClientApp/src/app/app.server.module.ts （与现有 app.module.ts 同时存在）处创建新文件来添加该模块，其中包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="40a69-192">Add this by creating a new file at *ClientApp/src/app/app.server.module.ts* (alongside the existing *app.module.ts*) containing the following:</span></span>

[!code-typescript[](sample/AngularServerSideRendering/ClientApp/src/app/app.server.module.ts)]

<span data-ttu-id="40a69-193">该模块继承自客户端 `app.module` 并定义在 SSR 期间哪些额外的 Angular 模块可用。</span><span class="sxs-lookup"><span data-stu-id="40a69-193">This module inherits from your client-side `app.module` and defines which extra Angular modules are available during SSR.</span></span>

<span data-ttu-id="40a69-194">回想一下，.angular-cli.json 中的新 `ssr` 条目引用了名为 main.server.ts 的入口点文件。</span><span class="sxs-lookup"><span data-stu-id="40a69-194">Recall that the new `ssr` entry in *.angular-cli.json* referenced an entry point file called *main.server.ts*.</span></span> <span data-ttu-id="40a69-195">如果你尚未添加该文件，那么现在是时候添加了。</span><span class="sxs-lookup"><span data-stu-id="40a69-195">You haven't yet added that file, and now is time to do so.</span></span> <span data-ttu-id="40a69-196">在 ClientApp/src/main.server.ts 处创建一个新文件（与现有 main.ts 同时存在），其中包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="40a69-196">Create a new file at *ClientApp/src/main.server.ts* (alongside the existing *main.ts*), containing the following:</span></span>

[!code-typescript[](sample/AngularServerSideRendering/ClientApp/src/main.server.ts)]

<span data-ttu-id="40a69-197">此文件的代码是 ASP.NET Core 在运行添加到 Startup 类的 `UseSpaPrerendering` 中间件时为每个请求执行的内容。</span><span class="sxs-lookup"><span data-stu-id="40a69-197">This file's code is what ASP.NET Core executes for each request when it runs the `UseSpaPrerendering` middleware that you added to the *Startup* class.</span></span> <span data-ttu-id="40a69-198">它处理从 .NET 代码（例如所请求的 URL）收到的 `params`，并调用 Angular SSR API 以获取生成的 HTML。</span><span class="sxs-lookup"><span data-stu-id="40a69-198">It deals with receiving `params` from the .NET code (such as the URL being requested), and making calls to Angular SSR APIs to get the resulting HTML.</span></span>

<span data-ttu-id="40a69-199">严格来说，这足以在开发模式下启用 SSR。</span><span class="sxs-lookup"><span data-stu-id="40a69-199">Strictly-speaking, this is sufficient to enable SSR in development mode.</span></span> <span data-ttu-id="40a69-200">重要的是做出最后的更改，以便应用在发布时能够正常工作。</span><span class="sxs-lookup"><span data-stu-id="40a69-200">It's essential to make one final change so that your app works correctly when published.</span></span> <span data-ttu-id="40a69-201">在应用的主 .csproj 文件中，将 `BuildServerSideRenderer` 属性值设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="40a69-201">In your app's main *.csproj* file, set the `BuildServerSideRenderer` property value to `true`:</span></span>

[!code-xml[](sample/AngularServerSideRendering/AngularServerSideRendering.csproj?name=snippet_EnableBuildServerSideRenderer)]

<span data-ttu-id="40a69-202">这会将生成过程配置为在发布期间运行 `build:ssr`，并将 SSR 文件部署到服务器。</span><span class="sxs-lookup"><span data-stu-id="40a69-202">This configures the build process to run `build:ssr` during publishing and deploy the SSR files to the server.</span></span> <span data-ttu-id="40a69-203">如果未启用此功能，则 SSR 将在生产中失败。</span><span class="sxs-lookup"><span data-stu-id="40a69-203">If you don't enable this, SSR fails in production.</span></span>

<span data-ttu-id="40a69-204">当应用在开发模式或生产模式下运行时，Angular 代码会在服务器上预呈现为 HTML。</span><span class="sxs-lookup"><span data-stu-id="40a69-204">When your app runs in either development or production mode, the Angular code pre-renders as HTML on the server.</span></span> <span data-ttu-id="40a69-205">客户端代码执行正常。</span><span class="sxs-lookup"><span data-stu-id="40a69-205">The client-side code executes as normal.</span></span>

### <a name="pass-data-from-net-code-into-typescript-code"></a><span data-ttu-id="40a69-206">将 .NET 代码中的数据传递到 TypeScript 代码中</span><span class="sxs-lookup"><span data-stu-id="40a69-206">Pass data from .NET code into TypeScript code</span></span>

<span data-ttu-id="40a69-207">在 SSR 期间，你可能希望将从 ASP.NET Core 应用预请求的数据传递到 Angular 应用。</span><span class="sxs-lookup"><span data-stu-id="40a69-207">During SSR, you might want to pass per-request data from your ASP.NET Core app into your Angular app.</span></span> <span data-ttu-id="40a69-208">例如，你可以传递 cookie 信息或从数据库读取的某些内容。</span><span class="sxs-lookup"><span data-stu-id="40a69-208">For example, you could pass cookie information or something read from a database.</span></span> <span data-ttu-id="40a69-209">若要执行此操作，请编辑 Startup 类。</span><span class="sxs-lookup"><span data-stu-id="40a69-209">To do this, edit your *Startup* class.</span></span> <span data-ttu-id="40a69-210">在 `UseSpaPrerendering` 的回叫中，为 `options.SupplyData` 设置一个值，如下所示：</span><span class="sxs-lookup"><span data-stu-id="40a69-210">In the callback for `UseSpaPrerendering`, set a value for `options.SupplyData` such as the following:</span></span>

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

<span data-ttu-id="40a69-211">`SupplyData` 回叫允许传递按请求的任意 JSON 序列化数据（例如字符串、布尔值或数字）。</span><span class="sxs-lookup"><span data-stu-id="40a69-211">The `SupplyData` callback lets you pass arbitrary, per-request, JSON-serializable data (for example, strings, booleans, or numbers).</span></span> <span data-ttu-id="40a69-212">main.server.ts 代码将此数据接收为 `params.data`。</span><span class="sxs-lookup"><span data-stu-id="40a69-212">Your *main.server.ts* code receives this as `params.data`.</span></span> <span data-ttu-id="40a69-213">例如，前面的代码示例将一个布尔值作为 `params.data.isHttpsRequest` 传递给 `createServerRenderer` 回叫。</span><span class="sxs-lookup"><span data-stu-id="40a69-213">For example, the preceding code sample passes a boolean value as `params.data.isHttpsRequest` into the `createServerRenderer` callback.</span></span> <span data-ttu-id="40a69-214">你可以通过 Angular 支持的任何方式将此传递给应用的其他部分。</span><span class="sxs-lookup"><span data-stu-id="40a69-214">You can pass this to other parts of your app in any way supported by Angular.</span></span> <span data-ttu-id="40a69-215">例如，请参阅 main.server.ts 如何将 `BASE_URL` 值传递给构造函数被声明为接收它的任何组件。</span><span class="sxs-lookup"><span data-stu-id="40a69-215">For example, see how *main.server.ts* passes the `BASE_URL` value to any component whose constructor is declared to receive it.</span></span>

### <a name="drawbacks-of-ssr"></a><span data-ttu-id="40a69-216">SSR 的缺点</span><span class="sxs-lookup"><span data-stu-id="40a69-216">Drawbacks of SSR</span></span>

<span data-ttu-id="40a69-217">并非所有应用都受益于 SSR。</span><span class="sxs-lookup"><span data-stu-id="40a69-217">Not all apps benefit from SSR.</span></span> <span data-ttu-id="40a69-218">主要优点是可感知的性能。</span><span class="sxs-lookup"><span data-stu-id="40a69-218">The primary benefit is perceived performance.</span></span> <span data-ttu-id="40a69-219">通过缓慢的网络连接或缓慢的移动设备连接你的应用的访问者可以快速查看初始 UI，即使需要一段时间才能提取或分析 JavaScript 捆绑包也是如此。</span><span class="sxs-lookup"><span data-stu-id="40a69-219">Visitors reaching your app over a slow network connection or on slow mobile devices see the initial UI quickly, even if it takes a while to fetch or parse the JavaScript bundles.</span></span> <span data-ttu-id="40a69-220">但是，许多 SPA 主要在具有快速内部公司网络的快速计算机（其中应用几乎立即显示）上使用。</span><span class="sxs-lookup"><span data-stu-id="40a69-220">However, many SPAs are mainly used over fast, internal company networks on fast computers where the app appears almost instantly.</span></span>

<span data-ttu-id="40a69-221">同时，启用 SSR 也存在重大缺点。</span><span class="sxs-lookup"><span data-stu-id="40a69-221">At the same time, there are significant drawbacks to enabling SSR.</span></span> <span data-ttu-id="40a69-222">它增加了开发过程的复杂性。</span><span class="sxs-lookup"><span data-stu-id="40a69-222">It adds complexity to your development process.</span></span> <span data-ttu-id="40a69-223">代码必须在两个不同的环境中运行：客户端和服务器端（在从 ASP.NET Core 中调用的 Node.js 环境中）。</span><span class="sxs-lookup"><span data-stu-id="40a69-223">Your code must run in two different environments: client-side and server-side (in a Node.js environment invoked from ASP.NET Core).</span></span> <span data-ttu-id="40a69-224">以下是一些需要谨记的事项：</span><span class="sxs-lookup"><span data-stu-id="40a69-224">Here are some things to bear in mind:</span></span>

* <span data-ttu-id="40a69-225">SSR 需要在生产服务器上安装 Node.js。</span><span class="sxs-lookup"><span data-stu-id="40a69-225">SSR requires a Node.js installation on your production servers.</span></span> <span data-ttu-id="40a69-226">对于某些部署方案（例如 Azure 应用服务）而言，情况自然如此，但对于其他方案（例如 Azure Service Fabric）则不适用。</span><span class="sxs-lookup"><span data-stu-id="40a69-226">This is automatically the case for some deployment scenarios, such as Azure App Services, but not for others, such as Azure Service Fabric.</span></span>
* <span data-ttu-id="40a69-227">启用 `BuildServerSideRenderer` 生成标志会导致发布 node_modules 目录。</span><span class="sxs-lookup"><span data-stu-id="40a69-227">Enabling the `BuildServerSideRenderer` build flag causes your *node_modules* directory to publish.</span></span> <span data-ttu-id="40a69-228">该文件夹包含 20,000 多个文件，这会增加部署时间。</span><span class="sxs-lookup"><span data-stu-id="40a69-228">This folder contains 20,000+ files, which increases deployment time.</span></span>
* <span data-ttu-id="40a69-229">要在 Node.js 环境中运行代码，则不能依赖于特定浏览器的 JavaScript API（如 `window` 或 `localStorage`）。</span><span class="sxs-lookup"><span data-stu-id="40a69-229">To run your code in a Node.js environment, it can't rely on the existence of browser-specific JavaScript APIs such as `window` or `localStorage`.</span></span> <span data-ttu-id="40a69-230">如果代码（或引用的某个第三方库）尝试使用这些 API，则在 SSR 期间会出现错误。</span><span class="sxs-lookup"><span data-stu-id="40a69-230">If your code (or some third-party library you reference) tries to use these APIs, you'll get an error during SSR.</span></span> <span data-ttu-id="40a69-231">例如，不要使用 jQuery，因为它在许多位置引用特定浏览器的 API。</span><span class="sxs-lookup"><span data-stu-id="40a69-231">For example, don't use jQuery because it references browser-specific APIs in many places.</span></span> <span data-ttu-id="40a69-232">要避免错误，你必须避免使用 SSR 或避免使用特定于浏览器的 API 或库。</span><span class="sxs-lookup"><span data-stu-id="40a69-232">To prevent errors, you must either avoid SSR or avoid browser-specific APIs or libraries.</span></span> <span data-ttu-id="40a69-233">你可以将对这些 API 的任何调用包装在检查中，以确保它们在 SSR 期间不被调用。</span><span class="sxs-lookup"><span data-stu-id="40a69-233">You can wrap any calls to such APIs in checks to ensure they aren't invoked during SSR.</span></span> <span data-ttu-id="40a69-234">例如，在 JavaScript 或 TypeScript 代码中使用如下所示的检查：</span><span class="sxs-lookup"><span data-stu-id="40a69-234">For example, use a check such as the following in JavaScript or TypeScript code:</span></span>

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```
