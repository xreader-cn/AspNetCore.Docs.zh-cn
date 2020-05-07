---
title: 通过 ASP.NET Core 使用 React 项目模板
author: SteveSandersonMS
description: 了解如何开始使用适用于 React 和 create-react-app 的 ASP.NET Core 单页应用程序 (SPA) 项目模板。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: spa/react
ms.openlocfilehash: 1dd352f1a00d30df915fe1936d7dcb34af1b6634
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773671"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a><span data-ttu-id="b5d3f-103">通过 ASP.NET Core 使用 React 项目模板</span><span class="sxs-lookup"><span data-stu-id="b5d3f-103">Use the React project template with ASP.NET Core</span></span>

<span data-ttu-id="b5d3f-104">更新的 React 项目模板为使用 React 和 [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) 约定实现丰富的客户端用户界面 (UI) 的 ASP.NET Core 应用程序提供了便捷起点。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-104">The updated React project template provides a convenient starting point for ASP.NET Core apps using React and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="b5d3f-105">该模板等同于创建两个项目，即用作 API 后端的 ASP.NET Core 项目和用作 UI 的标准 CRA React 项目，但均可在可以生成并发布为单个单元的单个应用程序项目中进行托管。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-105">The template is equivalent to creating both an ASP.NET Core project to act as an API backend, and a standard CRA React project to act as a UI, but with the convenience of hosting both in a single app project that can be built and published as a single unit.</span></span>

<span data-ttu-id="b5d3f-106">React 项目模板不适用于服务器端呈现 (SSR)。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-106">The React project template isn't meant for server-side rendering (SSR).</span></span> <span data-ttu-id="b5d3f-107">对于带有 React 和 Node.js 的 SSR，请考虑使用 [Next.js](https://github.com/zeit/next.js/) 或 [Razzle](https://github.com/jaredpalmer/razzle)。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-107">For SSR with React and Node.js, consider [Next.js](https://github.com/zeit/next.js/) or [Razzle](https://github.com/jaredpalmer/razzle).</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="b5d3f-108">创建新应用</span><span class="sxs-lookup"><span data-stu-id="b5d3f-108">Create a new app</span></span>

<span data-ttu-id="b5d3f-109">如果已安装 ASP.NET Core 2.1，则无需安装 React 项目模板。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-109">If you have ASP.NET Core 2.1 installed, there's no need to install the React project template.</span></span>

<span data-ttu-id="b5d3f-110">在空目录中使用命令 `dotnet new react` 从命令提示符创建一个新项目。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-110">Create a new project from a command prompt using the command `dotnet new react` in an empty directory.</span></span> <span data-ttu-id="b5d3f-111">例如，以下命令在 my-new-app  目录中创建应用并切换到该目录：</span><span class="sxs-lookup"><span data-stu-id="b5d3f-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new react -o my-new-app
cd my-new-app
```

<span data-ttu-id="b5d3f-112">从 Visual Studio 或 .NET Core CLI 运行应用：</span><span class="sxs-lookup"><span data-stu-id="b5d3f-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b5d3f-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b5d3f-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b5d3f-114">打开生成的 .csproj  文件，并从此文件正常运行应用。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="b5d3f-115">生成过程会在首次运行时还原 npm 依赖关系，这可能需要几分钟的时间。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="b5d3f-116">后续版本要快得多。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b5d3f-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b5d3f-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b5d3f-118">确保你有一个名为 `ASPNETCORE_Environment` 的环境变量，其值为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with value of `Development`.</span></span> <span data-ttu-id="b5d3f-119">在 Windows 上（在非 PowerShell 提示下）运行 `SET ASPNETCORE_Environment=Development`。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="b5d3f-120">在 Linux 或 macOS 上运行 `export ASPNETCORE_Environment=Development`。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="b5d3f-121">运行 [dotnet build](/dotnet/core/tools/dotnet-build) 以验证应用是否正确生成。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify your app builds correctly.</span></span> <span data-ttu-id="b5d3f-122">首次运行时，生成过程会还原 npm 依赖关系，这可能需要几分钟的时间。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="b5d3f-123">后续版本要快得多。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="b5d3f-124">运行 [dotnet run](/dotnet/core/tools/dotnet-run) 以启动应用。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span>

---

<span data-ttu-id="b5d3f-125">该项目模板创建一个 ASP.NET Core 应用和一个 React 应用。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-125">The project template creates an ASP.NET Core app and a React app.</span></span> <span data-ttu-id="b5d3f-126">ASP.NET Core 应用旨在用于数据访问、授权和其他服务器端问题。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-126">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="b5d3f-127">位于 ClientApp  子目录中的 React 应用旨在用于所有 UI 问题。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-127">The React app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="b5d3f-128">添加页面、映像、样式、模块等。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-128">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="b5d3f-129"> ClientApp 目录是标准的 CRA React 应用。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-129">The *ClientApp* directory is a standard CRA React app.</span></span> <span data-ttu-id="b5d3f-130">有关详细信息，请参阅官方 [CRA 文档](https://create-react-app.dev/docs/getting-started/)。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-130">See the official [CRA documentation](https://create-react-app.dev/docs/getting-started/) for more information.</span></span>

<span data-ttu-id="b5d3f-131">此模板创建的 React 应用与 CRA 自身创建的应用之间存在细微差异；但是，该应用的功能未变。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-131">There are slight differences between the React app created by this template and the one created by CRA itself; however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="b5d3f-132">该模板创建的应用包含基于 [Bootstrap](https://getbootstrap.com/) 的布局和基本路由示例。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-132">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="b5d3f-133">安装 npm 包</span><span class="sxs-lookup"><span data-stu-id="b5d3f-133">Install npm packages</span></span>

<span data-ttu-id="b5d3f-134">要安装第三方 npm 程序包，请使用 ClientApp  子目录中的命令提示符。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-134">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="b5d3f-135">例如:</span><span class="sxs-lookup"><span data-stu-id="b5d3f-135">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="b5d3f-136">发布和部署</span><span class="sxs-lookup"><span data-stu-id="b5d3f-136">Publish and deploy</span></span>

<span data-ttu-id="b5d3f-137">在开发过程中，应用在为开发人员之便而优化的模式下运行。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-137">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="b5d3f-138">例如，JavaScript 捆绑包包含源映射（这样在调试时可以查看原始源代码）。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-138">For example, JavaScript bundles include source maps (so that when debugging, you can see your original source code).</span></span> <span data-ttu-id="b5d3f-139">该应用程序监视磁盘上的 JavaScript、HTML 和 CSS 文件更改，并在看到这些文件更改时自动重新编译和重新加载。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-139">The app watches JavaScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="b5d3f-140">在生产中，提供针对性能进行了优化的应用版本。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-140">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="b5d3f-141">该操作被配置为自动发生。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-141">This is configured to happen automatically.</span></span> <span data-ttu-id="b5d3f-142">发布时，生成配置会发出转换的压缩客户端代码版本。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-142">When you publish, the build configuration emits a minified, transpiled build of your client-side code.</span></span> <span data-ttu-id="b5d3f-143">与开发版本不同，生产版本不需要在服务器上安装 Node.js。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-143">Unlike the development build, the production build doesn't require Node.js to be installed on the server.</span></span>

<span data-ttu-id="b5d3f-144">你可以使用标准 [ASP.NET Core 托管和部署方法](xref:host-and-deploy/index)。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-144">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-the-cra-server-independently"></a><span data-ttu-id="b5d3f-145">独立运行 CRA 服务器</span><span class="sxs-lookup"><span data-stu-id="b5d3f-145">Run the CRA server independently</span></span>

<span data-ttu-id="b5d3f-146">在 ASP.NET Core 应用以开发模式启动时，该项目配置为在后台启动自己的 CRA 开发服务器实例。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-146">The project is configured to start its own instance of the CRA development server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="b5d3f-147">这很方便，因为这表示你无需手动运行单独的服务器。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-147">This is convenient because it means you don't have to run a separate server manually.</span></span>

<span data-ttu-id="b5d3f-148">这种默认设置有一个缺点。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-148">There's a drawback to this default setup.</span></span> <span data-ttu-id="b5d3f-149">每次修改 C# 代码并且需要重启 ASP.NET Core 应用时，CRA 服务器都会重启。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-149">Each time you modify your C# code and your ASP.NET Core app needs to restart, the CRA server restarts.</span></span> <span data-ttu-id="b5d3f-150">大约需要几秒才能开始备份。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-150">A few seconds are required to start back up.</span></span> <span data-ttu-id="b5d3f-151">如果你经常进行 C# 代码编辑并且不希望等待 CRA 服务器重启，请在外部运行独立于 ASP.NET Core 进程的 CRA 服务器。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-151">If you're making frequent C# code edits and don't want to wait for the CRA server to restart, run the CRA server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="b5d3f-152">为此，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b5d3f-152">To do so:</span></span>

1. <span data-ttu-id="b5d3f-153">使用以下设置将 .env 文件添加到 ClientApp 子目录   ：</span><span class="sxs-lookup"><span data-stu-id="b5d3f-153">Add a *.env* file to the *ClientApp* subdirectory with the following setting:</span></span>

    ```
    BROWSER=none
    ```

    <span data-ttu-id="b5d3f-154">这将阻止在外部启动 CRA 服务器时打开 Web 浏览器。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-154">This will prevent your web browser from opening when starting the CRA server externally.</span></span>

2. <span data-ttu-id="b5d3f-155">在命令提示符中，切换到 ClientApp  子目录，并启动 CRA 开发服务器：</span><span class="sxs-lookup"><span data-stu-id="b5d3f-155">In a command prompt, switch to the *ClientApp* subdirectory, and launch the CRA development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

3. <span data-ttu-id="b5d3f-156">修改 ASP.NET Core 应用以使用外部 CRA 服务器实例，而不是启动它自己的实例。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-156">Modify your ASP.NET Core app to use the external CRA server instance instead of launching one of its own.</span></span> <span data-ttu-id="b5d3f-157">在 Startup  类中，将 `spa.UseReactDevelopmentServer` 调用替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="b5d3f-157">In your *Startup* class, replace the `spa.UseReactDevelopmentServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

<span data-ttu-id="b5d3f-158">当启动 ASP.NET Core 应用时，它不会启动 CRA 服务器，</span><span class="sxs-lookup"><span data-stu-id="b5d3f-158">When you start your ASP.NET Core app, it won't launch a CRA server.</span></span> <span data-ttu-id="b5d3f-159">而是使用你手动启动的实例。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-159">The instance you started manually is used instead.</span></span> <span data-ttu-id="b5d3f-160">这使它能够更快地启动和重新启动。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-160">This enables it to start and restart faster.</span></span> <span data-ttu-id="b5d3f-161">不再需要每次等待 React 应用重新生成。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-161">It's no longer waiting for your React app to rebuild each time.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b5d3f-162">此模板不支持“服务器端呈现”功能。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-162">"Server-side rendering" is not a supported feature of this template.</span></span> <span data-ttu-id="b5d3f-163">使用此模板是为了通过“create-react-app”满足奇偶一致性。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-163">Our goal with this template is to meet parity with "create-react-app".</span></span> <span data-ttu-id="b5d3f-164">因此，不支持“create-react-app”项目中没有的方案和功能（例如 SSR），这些内容作为练习提供给用户。</span><span class="sxs-lookup"><span data-stu-id="b5d3f-164">As such, scenarios and features not included in a "create-react-app" project (such as SSR) are not supported and are left as an exercise for the user.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b5d3f-165">其他资源</span><span class="sxs-lookup"><span data-stu-id="b5d3f-165">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
