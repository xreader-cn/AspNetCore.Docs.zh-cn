---
title: 通过 ASP.NET Core 使用 Angular 项目模板
author: SteveSandersonMS
description: 了解如何开始使用适用于 Angular 和 Angular CLI 的 ASP.NET Core 单页应用程序 (SPA) 项目模板。
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 02/06/2020
uid: spa/angular
ms.openlocfilehash: fee872ff237e14cbe491efed9b320809df4c5654
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78646458"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a><span data-ttu-id="de316-103">通过 ASP.NET Core 使用 Angular 项目模板</span><span class="sxs-lookup"><span data-stu-id="de316-103">Use the Angular project template with ASP.NET Core</span></span>

<span data-ttu-id="de316-104">更新的 Angular 项目模板为使用 Angular 和 Angular CLI 实现丰富的客户端用户界面 (UI) 的 ASP.NET Core 应用提供了便捷起点。</span><span class="sxs-lookup"><span data-stu-id="de316-104">The updated Angular project template provides a convenient starting point for ASP.NET Core apps using Angular and the Angular CLI to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="de316-105">该模板等同于创建用作 API 后端的 ASP.NET Core 项目，而 Angular CLI 项目用作 UI。</span><span class="sxs-lookup"><span data-stu-id="de316-105">The template is equivalent to creating an ASP.NET Core project to act as an API backend and an Angular CLI project to act as a UI.</span></span> <span data-ttu-id="de316-106">该模板提供在单个应用项目中托管两种项目类型的便利。</span><span class="sxs-lookup"><span data-stu-id="de316-106">The template offers the convenience of hosting both project types in a single app project.</span></span> <span data-ttu-id="de316-107">因此，应用项目可以作为单个单元来生成和发布。</span><span class="sxs-lookup"><span data-stu-id="de316-107">Consequently, the app project can be built and published as a single unit.</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="de316-108">创建新应用</span><span class="sxs-lookup"><span data-stu-id="de316-108">Create a new app</span></span>

<span data-ttu-id="de316-109">如果已安装 ASP.NET Core 2.1，则无需安装 Angular 项目模板。</span><span class="sxs-lookup"><span data-stu-id="de316-109">If you have ASP.NET Core 2.1 installed, there's no need to install the Angular project template.</span></span>

<span data-ttu-id="de316-110">在空目录中使用命令 `dotnet new angular` 从命令提示符创建一个新项目。</span><span class="sxs-lookup"><span data-stu-id="de316-110">Create a new project from a command prompt using the command `dotnet new angular` in an empty directory.</span></span> <span data-ttu-id="de316-111">例如，以下命令在 my-new-app  目录中创建应用并切换到该目录：</span><span class="sxs-lookup"><span data-stu-id="de316-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new angular -o my-new-app
cd my-new-app
```

<span data-ttu-id="de316-112">从 Visual Studio 或 .NET Core CLI 运行应用：</span><span class="sxs-lookup"><span data-stu-id="de316-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de316-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de316-113">Visual Studio</span></span>](#tab/visual-studio/)

<span data-ttu-id="de316-114">打开生成的 .csproj  文件，并从此文件正常运行应用。</span><span class="sxs-lookup"><span data-stu-id="de316-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="de316-115">生成过程会在首次运行时还原 npm 依赖关系，这可能需要几分钟的时间。</span><span class="sxs-lookup"><span data-stu-id="de316-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="de316-116">后续版本要快得多。</span><span class="sxs-lookup"><span data-stu-id="de316-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="de316-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="de316-117">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="de316-118">确保你有一个名为 `ASPNETCORE_Environment` 的环境变量，其值为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="de316-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with a value of `Development`.</span></span> <span data-ttu-id="de316-119">在 Windows 上（在非 PowerShell 提示下）运行 `SET ASPNETCORE_Environment=Development`。</span><span class="sxs-lookup"><span data-stu-id="de316-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="de316-120">在 Linux 或 macOS 上运行 `export ASPNETCORE_Environment=Development`。</span><span class="sxs-lookup"><span data-stu-id="de316-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="de316-121">运行 [dotnet build](/dotnet/core/tools/dotnet-build) 以验证应用是否正确生成。</span><span class="sxs-lookup"><span data-stu-id="de316-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify the app builds correctly.</span></span> <span data-ttu-id="de316-122">首次运行时，生成过程会还原 npm 依赖关系，这可能需要几分钟的时间。</span><span class="sxs-lookup"><span data-stu-id="de316-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="de316-123">后续版本要快得多。</span><span class="sxs-lookup"><span data-stu-id="de316-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="de316-124">运行 [dotnet run](/dotnet/core/tools/dotnet-run) 以启动应用。</span><span class="sxs-lookup"><span data-stu-id="de316-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span> <span data-ttu-id="de316-125">记录类似于以下内容的消息：</span><span class="sxs-lookup"><span data-stu-id="de316-125">A message similar to the following is logged:</span></span>

```console
Now listening on: http://localhost:<port>
```

<span data-ttu-id="de316-126">在浏览器中导航到此 URL。</span><span class="sxs-lookup"><span data-stu-id="de316-126">Navigate to this URL in a browser.</span></span>

> [!WARNING]
> <span data-ttu-id="de316-127">该应用在后台启动 Angular CLI 服务器的一个实例。</span><span class="sxs-lookup"><span data-stu-id="de316-127">The app starts up an instance of the Angular CLI server in the background.</span></span> <span data-ttu-id="de316-128">记录类似于以下内容的消息：*NG Live 开发服务器正在 localhost:&lt;otherport&gt; 上进行侦听，在 http://localhost:&lt;otherport&gt;/* 上打开浏览器。</span><span class="sxs-lookup"><span data-stu-id="de316-128">A message similar to the following is logged: *NG Live Development Server is listening on localhost:&lt;otherport&gt;, open a browser to http://localhost:&lt;otherport&gt;/*.</span></span> <span data-ttu-id="de316-129">忽略此消息&mdash;这**不是**组合 ASP.NET Core 和 Angular CLI 应用的 URL。</span><span class="sxs-lookup"><span data-stu-id="de316-129">Ignore this message&mdash;it's **not** the URL for the combined ASP.NET Core and Angular CLI app.</span></span>

---

<span data-ttu-id="de316-130">该项目模板创建一个 ASP.NET Core 应用和一个 Angular 应用。</span><span class="sxs-lookup"><span data-stu-id="de316-130">The project template creates an ASP.NET Core app and an Angular app.</span></span> <span data-ttu-id="de316-131">ASP.NET Core 应用旨在用于数据访问、授权和其他服务器端问题。</span><span class="sxs-lookup"><span data-stu-id="de316-131">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="de316-132">位于 ClientApp  子目录中的 Angular 应用旨在用于所有 UI 问题。</span><span class="sxs-lookup"><span data-stu-id="de316-132">The Angular app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="de316-133">添加页面、映像、样式、模块等。</span><span class="sxs-lookup"><span data-stu-id="de316-133">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="de316-134">ClientApp  目录包含标准的 Angular CLI 应用。</span><span class="sxs-lookup"><span data-stu-id="de316-134">The *ClientApp* directory contains a standard Angular CLI app.</span></span> <span data-ttu-id="de316-135">有关详细信息，请参阅官方 [Angular 文档](https://angular.io)。</span><span class="sxs-lookup"><span data-stu-id="de316-135">See the official [Angular documentation](https://angular.io) for more information.</span></span>

<span data-ttu-id="de316-136">此模板创建的 Angular 应用与 Angular CLI 本身创建的应用（通过 `ng new`）之间存在细微差异；但是，该应用的功能未变。</span><span class="sxs-lookup"><span data-stu-id="de316-136">There are slight differences between the Angular app created by this template and the one created by Angular CLI itself (via `ng new`); however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="de316-137">该模板创建的应用包含基于 [Bootstrap](https://getbootstrap.com/) 的布局和基本路由示例。</span><span class="sxs-lookup"><span data-stu-id="de316-137">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="run-ng-commands"></a><span data-ttu-id="de316-138">运行 ng 命令</span><span class="sxs-lookup"><span data-stu-id="de316-138">Run ng commands</span></span>

<span data-ttu-id="de316-139">在命令提示符中，切换到 ClientApp  子目录：</span><span class="sxs-lookup"><span data-stu-id="de316-139">In a command prompt, switch to the *ClientApp* subdirectory:</span></span>

```console
cd ClientApp
```

<span data-ttu-id="de316-140">如果全局安装了 `ng` 工具，则可以运行其任何命令。</span><span class="sxs-lookup"><span data-stu-id="de316-140">If you have the `ng` tool installed globally, you can run any of its commands.</span></span> <span data-ttu-id="de316-141">例如，你可以运行 `ng lint`、`ng test` 或任何其他 [Angular CLI 命令](https://angular.io/cli)。</span><span class="sxs-lookup"><span data-stu-id="de316-141">For example, you can run `ng lint`, `ng test`, or any of the other [Angular CLI commands](https://angular.io/cli).</span></span> <span data-ttu-id="de316-142">不过无需运行 `ng serve`，因为 ASP.NET Core 应用为应用的服务器端和客户端部分提供服务。</span><span class="sxs-lookup"><span data-stu-id="de316-142">There's no need to run `ng serve` though, because your ASP.NET Core app deals with serving both server-side and client-side parts of your app.</span></span> <span data-ttu-id="de316-143">在内部，它在开发中使用 `ng serve`。</span><span class="sxs-lookup"><span data-stu-id="de316-143">Internally, it uses `ng serve` in development.</span></span>

<span data-ttu-id="de316-144">如果未安装 `ng` 工具，请改为运行 `npm run ng`。</span><span class="sxs-lookup"><span data-stu-id="de316-144">If you don't have the `ng` tool installed, run `npm run ng` instead.</span></span> <span data-ttu-id="de316-145">例如，你可以运行 `npm run ng lint` 或 `npm run ng test`。</span><span class="sxs-lookup"><span data-stu-id="de316-145">For example, you can run `npm run ng lint` or `npm run ng test`.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="de316-146">安装 npm 包</span><span class="sxs-lookup"><span data-stu-id="de316-146">Install npm packages</span></span>

<span data-ttu-id="de316-147">要安装第三方 npm 程序包，请使用 ClientApp  子目录中的命令提示符。</span><span class="sxs-lookup"><span data-stu-id="de316-147">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="de316-148">例如：</span><span class="sxs-lookup"><span data-stu-id="de316-148">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="de316-149">发布和部署</span><span class="sxs-lookup"><span data-stu-id="de316-149">Publish and deploy</span></span>

<span data-ttu-id="de316-150">在开发过程中，应用在为开发人员之便而优化的模式下运行。</span><span class="sxs-lookup"><span data-stu-id="de316-150">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="de316-151">例如，JavaScript 捆绑包包含源映射（这样在调试时可以查看原始 TypeScript 代码）。</span><span class="sxs-lookup"><span data-stu-id="de316-151">For example, JavaScript bundles include source maps (so that when debugging, you can see your original TypeScript code).</span></span> <span data-ttu-id="de316-152">该应用监视磁盘上的 TypeScript、HTML 和 CSS 文件更改，并在看到这些文件更改时自动重新编译和重新加载。</span><span class="sxs-lookup"><span data-stu-id="de316-152">The app watches for TypeScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="de316-153">在生产中，提供针对性能进行了优化的应用版本。</span><span class="sxs-lookup"><span data-stu-id="de316-153">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="de316-154">该操作被配置为自动发生。</span><span class="sxs-lookup"><span data-stu-id="de316-154">This is configured to happen automatically.</span></span> <span data-ttu-id="de316-155">发布时，生成配置会发出预先 (AoT) 编译的压缩客户端代码版本。</span><span class="sxs-lookup"><span data-stu-id="de316-155">When you publish, the build configuration emits a minified, ahead-of-time (AoT) compiled build of your client-side code.</span></span> <span data-ttu-id="de316-156">与开发版本不同，生产版本不需要在服务器上安装 Node.js（除非已启用服务器端呈现 (SSR)）。</span><span class="sxs-lookup"><span data-stu-id="de316-156">Unlike the development build, the production build doesn't require Node.js to be installed on the server (unless you have enabled server-side rendering (SSR)).</span></span>

<span data-ttu-id="de316-157">你可以使用标准 [ASP.NET Core 托管和部署方法](xref:host-and-deploy/index)。</span><span class="sxs-lookup"><span data-stu-id="de316-157">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-ng-serve-independently"></a><span data-ttu-id="de316-158">独立运行“ng serve”</span><span class="sxs-lookup"><span data-stu-id="de316-158">Run "ng serve" independently</span></span>

<span data-ttu-id="de316-159">在 ASP.NET Core 应用以开发模式启动时，该项目配置为在后台启动自己的 Angular CLI 服务器实例。</span><span class="sxs-lookup"><span data-stu-id="de316-159">The project is configured to start its own instance of the Angular CLI server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="de316-160">这很方便，因为你无需手动运行单独的服务器。</span><span class="sxs-lookup"><span data-stu-id="de316-160">This is convenient because you don't have to run a separate server manually.</span></span>

<span data-ttu-id="de316-161">这种默认设置有一个缺点。</span><span class="sxs-lookup"><span data-stu-id="de316-161">There's a drawback to this default setup.</span></span> <span data-ttu-id="de316-162">每次修改 C# 代码并且需要重启 ASP.NET Core 应用时，Angular CLI 服务器都会重启。</span><span class="sxs-lookup"><span data-stu-id="de316-162">Each time you modify your C# code and your ASP.NET Core app needs to restart, the Angular CLI server restarts.</span></span> <span data-ttu-id="de316-163">大约需要 10 秒才能开始备份。</span><span class="sxs-lookup"><span data-stu-id="de316-163">Around 10 seconds is required to start back up.</span></span> <span data-ttu-id="de316-164">如果你经常进行 C# 代码编辑并且不希望等待 Angular CLI 重启，请在外部运行独立于 ASP.NET Core 进程的 Angular CLI 服务器。</span><span class="sxs-lookup"><span data-stu-id="de316-164">If you're making frequent C# code edits and don't want to wait for Angular CLI to restart, run the Angular CLI server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="de316-165">为此，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="de316-165">To do so:</span></span>

1. <span data-ttu-id="de316-166">在命令提示符中，切换到 ClientApp  子目录，并启动 Angular CLI 开发服务器：</span><span class="sxs-lookup"><span data-stu-id="de316-166">In a command prompt, switch to the *ClientApp* subdirectory, and launch the Angular CLI development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > <span data-ttu-id="de316-167">使用 `npm start` 而不是 `ng serve` 启动 Angular CLI 开发服务器，以便遵守 package.json  中的配置。</span><span class="sxs-lookup"><span data-stu-id="de316-167">Use `npm start` to launch the Angular CLI development server, not `ng serve`, so that the configuration in *package.json* is respected.</span></span> <span data-ttu-id="de316-168">要将其他参数传递给 Angular CLI 服务器，请将它们添加到 package.json  文件中的相关 `scripts` 行。</span><span class="sxs-lookup"><span data-stu-id="de316-168">To pass additional parameters to the Angular CLI server, add them to the relevant `scripts` line in your *package.json* file.</span></span>

2. <span data-ttu-id="de316-169">修改 ASP.NET Core 应用以使用外部 Angular CLI 实例，而不是启动它自己的实例。</span><span class="sxs-lookup"><span data-stu-id="de316-169">Modify your ASP.NET Core app to use the external Angular CLI instance instead of launching one of its own.</span></span> <span data-ttu-id="de316-170">在 Startup  类中，将 `spa.UseAngularCliServer` 调用替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="de316-170">In your *Startup* class, replace the `spa.UseAngularCliServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

<span data-ttu-id="de316-171">当启动 ASP.NET Core 应用时，它不会启动 Angular CLI 服务器，</span><span class="sxs-lookup"><span data-stu-id="de316-171">When you start your ASP.NET Core app, it won't launch an Angular CLI server.</span></span> <span data-ttu-id="de316-172">而是使用你手动启动的实例。</span><span class="sxs-lookup"><span data-stu-id="de316-172">The instance you started manually is used instead.</span></span> <span data-ttu-id="de316-173">这使它能够更快地启动和重新启动。</span><span class="sxs-lookup"><span data-stu-id="de316-173">This enables it to start and restart faster.</span></span> <span data-ttu-id="de316-174">不再需要每次等待 Angular CLI 重新生成客户端应用。</span><span class="sxs-lookup"><span data-stu-id="de316-174">It's no longer waiting for Angular CLI to rebuild your client app each time.</span></span>

### <a name="pass-data-from-net-code-into-typescript-code"></a><span data-ttu-id="de316-175">将 .NET 代码中的数据传递到 TypeScript 代码中</span><span class="sxs-lookup"><span data-stu-id="de316-175">Pass data from .NET code into TypeScript code</span></span>

<span data-ttu-id="de316-176">在 SSR 期间，你可能希望将从 ASP.NET Core 应用预请求的数据传递到 Angular 应用。</span><span class="sxs-lookup"><span data-stu-id="de316-176">During SSR, you might want to pass per-request data from your ASP.NET Core app into your Angular app.</span></span> <span data-ttu-id="de316-177">例如，你可以传递 cookie 信息或从数据库读取的某些内容。</span><span class="sxs-lookup"><span data-stu-id="de316-177">For example, you could pass cookie information or something read from a database.</span></span> <span data-ttu-id="de316-178">若要执行此操作，请编辑 Startup  类。</span><span class="sxs-lookup"><span data-stu-id="de316-178">To do this, edit your *Startup* class.</span></span> <span data-ttu-id="de316-179">在 `UseSpaPrerendering` 的回叫中，为 `options.SupplyData` 设置一个值，如下所示：</span><span class="sxs-lookup"><span data-stu-id="de316-179">In the callback for `UseSpaPrerendering`, set a value for `options.SupplyData` such as the following:</span></span>

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

<span data-ttu-id="de316-180">`SupplyData` 回叫允许传递按请求的任意 JSON 序列化数据（例如字符串、布尔值或数字）。</span><span class="sxs-lookup"><span data-stu-id="de316-180">The `SupplyData` callback lets you pass arbitrary, per-request, JSON-serializable data (for example, strings, booleans, or numbers).</span></span> <span data-ttu-id="de316-181"> main.server.ts 代码将此数据接收为 `params.data`。</span><span class="sxs-lookup"><span data-stu-id="de316-181">Your *main.server.ts* code receives this as `params.data`.</span></span> <span data-ttu-id="de316-182">例如，前面的代码示例将一个布尔值作为 `params.data.isHttpsRequest` 传递给 `createServerRenderer` 回叫。</span><span class="sxs-lookup"><span data-stu-id="de316-182">For example, the preceding code sample passes a boolean value as `params.data.isHttpsRequest` into the `createServerRenderer` callback.</span></span> <span data-ttu-id="de316-183">你可以通过 Angular 支持的任何方式将此传递给应用的其他部分。</span><span class="sxs-lookup"><span data-stu-id="de316-183">You can pass this to other parts of your app in any way supported by Angular.</span></span> <span data-ttu-id="de316-184">例如，请参阅 main.server.ts  如何将 `BASE_URL` 值传递给构造函数被声明为接收它的任何组件。</span><span class="sxs-lookup"><span data-stu-id="de316-184">For example, see how *main.server.ts* passes the `BASE_URL` value to any component whose constructor is declared to receive it.</span></span>

### <a name="drawbacks-of-ssr"></a><span data-ttu-id="de316-185">SSR 的缺点</span><span class="sxs-lookup"><span data-stu-id="de316-185">Drawbacks of SSR</span></span>

<span data-ttu-id="de316-186">并非所有应用都受益于 SSR。</span><span class="sxs-lookup"><span data-stu-id="de316-186">Not all apps benefit from SSR.</span></span> <span data-ttu-id="de316-187">主要优点是可感知的性能。</span><span class="sxs-lookup"><span data-stu-id="de316-187">The primary benefit is perceived performance.</span></span> <span data-ttu-id="de316-188">通过缓慢的网络连接或缓慢的移动设备连接你的应用的访问者可以快速查看初始 UI，即使需要一段时间才能提取或分析 JavaScript 捆绑包也是如此。</span><span class="sxs-lookup"><span data-stu-id="de316-188">Visitors reaching your app over a slow network connection or on slow mobile devices see the initial UI quickly, even if it takes a while to fetch or parse the JavaScript bundles.</span></span> <span data-ttu-id="de316-189">但是，许多 SPA 主要在具有快速内部公司网络的快速计算机（其中应用几乎立即显示）上使用。</span><span class="sxs-lookup"><span data-stu-id="de316-189">However, many SPAs are mainly used over fast, internal company networks on fast computers where the app appears almost instantly.</span></span>

<span data-ttu-id="de316-190">同时，启用 SSR 也存在重大缺点。</span><span class="sxs-lookup"><span data-stu-id="de316-190">At the same time, there are significant drawbacks to enabling SSR.</span></span> <span data-ttu-id="de316-191">它增加了开发过程的复杂性。</span><span class="sxs-lookup"><span data-stu-id="de316-191">It adds complexity to your development process.</span></span> <span data-ttu-id="de316-192">代码必须在两个不同的环境中运行：客户端和服务器端（在从 ASP.NET Core 中调用的 Node.js 环境中）。</span><span class="sxs-lookup"><span data-stu-id="de316-192">Your code must run in two different environments: client-side and server-side (in a Node.js environment invoked from ASP.NET Core).</span></span> <span data-ttu-id="de316-193">以下是一些需要谨记的事项：</span><span class="sxs-lookup"><span data-stu-id="de316-193">Here are some things to bear in mind:</span></span>

* <span data-ttu-id="de316-194">SSR 需要在生产服务器上安装 Node.js。</span><span class="sxs-lookup"><span data-stu-id="de316-194">SSR requires a Node.js installation on your production servers.</span></span> <span data-ttu-id="de316-195">对于某些部署方案（例如 Azure 应用服务）而言，情况自然如此，但对于其他方案（例如 Azure Service Fabric）则不适用。</span><span class="sxs-lookup"><span data-stu-id="de316-195">This is automatically the case for some deployment scenarios, such as Azure App Services, but not for others, such as Azure Service Fabric.</span></span>
* <span data-ttu-id="de316-196">启用 `BuildServerSideRenderer` 生成标志会导致发布 node_modules  目录。</span><span class="sxs-lookup"><span data-stu-id="de316-196">Enabling the `BuildServerSideRenderer` build flag causes your *node_modules* directory to publish.</span></span> <span data-ttu-id="de316-197">该文件夹包含 20,000 多个文件，这会增加部署时间。</span><span class="sxs-lookup"><span data-stu-id="de316-197">This folder contains 20,000+ files, which increases deployment time.</span></span>
* <span data-ttu-id="de316-198">要在 Node.js 环境中运行代码，则不能依赖于特定浏览器的 JavaScript API（如 `window` 或 `localStorage`）。</span><span class="sxs-lookup"><span data-stu-id="de316-198">To run your code in a Node.js environment, it can't rely on the existence of browser-specific JavaScript APIs such as `window` or `localStorage`.</span></span> <span data-ttu-id="de316-199">如果代码（或引用的某个第三方库）尝试使用这些 API，则在 SSR 期间会出现错误。</span><span class="sxs-lookup"><span data-stu-id="de316-199">If your code (or some third-party library you reference) tries to use these APIs, you'll get an error during SSR.</span></span> <span data-ttu-id="de316-200">例如，不要使用 jQuery，因为它在许多位置引用特定浏览器的 API。</span><span class="sxs-lookup"><span data-stu-id="de316-200">For example, don't use jQuery because it references browser-specific APIs in many places.</span></span> <span data-ttu-id="de316-201">要避免错误，你必须避免使用 SSR 或避免使用特定于浏览器的 API 或库。</span><span class="sxs-lookup"><span data-stu-id="de316-201">To prevent errors, you must either avoid SSR or avoid browser-specific APIs or libraries.</span></span> <span data-ttu-id="de316-202">你可以将对这些 API 的任何调用包装在检查中，以确保它们在 SSR 期间不被调用。</span><span class="sxs-lookup"><span data-stu-id="de316-202">You can wrap any calls to such APIs in checks to ensure they aren't invoked during SSR.</span></span> <span data-ttu-id="de316-203">例如，在 JavaScript 或 TypeScript 代码中使用如下所示的检查：</span><span class="sxs-lookup"><span data-stu-id="de316-203">For example, use a check such as the following in JavaScript or TypeScript code:</span></span>

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="de316-204">其他资源</span><span class="sxs-lookup"><span data-stu-id="de316-204">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
