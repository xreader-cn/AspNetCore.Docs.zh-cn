---
title: ASP.NET Core 入门
author: rick-anderson
description: 介绍如何使用 ASP.NET Core 创建并运行简单的 Hello World 应用的快速教程。
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2019
uid: getting-started
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="d89aa-103">教程：ASP.NET Core 入门</span><span class="sxs-lookup"><span data-stu-id="d89aa-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="d89aa-104">本教程演示如何使用 .NET Core 命令行接口创建 ASP.NET Core Web 应用。</span><span class="sxs-lookup"><span data-stu-id="d89aa-104">This tutorial shows how to use the .NET Core command-line interface to create an ASP.NET Core web app.</span></span>

<span data-ttu-id="d89aa-105">你将了解如何：</span><span class="sxs-lookup"><span data-stu-id="d89aa-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d89aa-106">创建 Web 应用项目。</span><span class="sxs-lookup"><span data-stu-id="d89aa-106">Create a web app project.</span></span>
> * <span data-ttu-id="d89aa-107">启用本地 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="d89aa-107">Enable local HTTPS.</span></span>
> * <span data-ttu-id="d89aa-108">运行应用。</span><span class="sxs-lookup"><span data-stu-id="d89aa-108">Run the app.</span></span>
> * <span data-ttu-id="d89aa-109">编辑 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="d89aa-109">Edit a Razor page.</span></span>

<span data-ttu-id="d89aa-110">最后，在本地计算机上运行工作 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="d89aa-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Web 应用主页](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="d89aa-112">系统必备</span><span class="sxs-lookup"><span data-stu-id="d89aa-112">Prerequisites</span></span>

* [<span data-ttu-id="d89aa-113">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="d89aa-113">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web-app-project"></a><span data-ttu-id="d89aa-114">创建 Web 应用项目</span><span class="sxs-lookup"><span data-stu-id="d89aa-114">Create a web app project</span></span>

<span data-ttu-id="d89aa-115">打开命令行界面，然后输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="d89aa-115">Open a command shell, and enter the following command:</span></span>

```console
dotnet new webapp -o aspnetcoreapp
```

## <a name="enable-local-https"></a><span data-ttu-id="d89aa-116">启用本地 HTTPS</span><span class="sxs-lookup"><span data-stu-id="d89aa-116">Enable local HTTPS</span></span>

<span data-ttu-id="d89aa-117">信任 HTTPS 开发证书：</span><span class="sxs-lookup"><span data-stu-id="d89aa-117">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="d89aa-118">Windows</span><span class="sxs-lookup"><span data-stu-id="d89aa-118">Windows</span></span>](#tab/windows)

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="d89aa-119">以上命令会显示以下对话：</span><span class="sxs-lookup"><span data-stu-id="d89aa-119">The preceding command displays the following dialog:</span></span>

![安全警告对话](~/getting-started/_static/cert.png)

<span data-ttu-id="d89aa-121">如果你同意信任开发证书，请选择“是”。</span><span class="sxs-lookup"><span data-stu-id="d89aa-121">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="d89aa-122">macOS</span><span class="sxs-lookup"><span data-stu-id="d89aa-122">macOS</span></span>](#tab/macos)

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="d89aa-123">以上命令会显示以下消息：</span><span class="sxs-lookup"><span data-stu-id="d89aa-123">The preceding command displays the following message:</span></span>

<span data-ttu-id="d89aa-124">*请求信任 HTTPS 开发证书。如果证书尚不受信任，我们将运行以下命令：* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`。</span><span class="sxs-lookup"><span data-stu-id="d89aa-124">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`.</span></span>
 
<span data-ttu-id="d89aa-125">此命令可能会提示你输入密码，以在系统密钥链上安装证书。</span><span class="sxs-lookup"><span data-stu-id="d89aa-125">This command command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="d89aa-126">如果你同意信任开发证书，请输入密码。</span><span class="sxs-lookup"><span data-stu-id="d89aa-126">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="d89aa-127">Linux</span><span class="sxs-lookup"><span data-stu-id="d89aa-127">Linux</span></span>](#tab/linux)

<span data-ttu-id="d89aa-128">查看你的 Linux 分发对应的文档，了解如何信任 HTTPS 开发证书。</span><span class="sxs-lookup"><span data-stu-id="d89aa-128">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="d89aa-129">有关详细信息，请参阅[信任 ASP.NET Core HTTPS 开发证书](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span><span class="sxs-lookup"><span data-stu-id="d89aa-129">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="d89aa-130">运行应用</span><span class="sxs-lookup"><span data-stu-id="d89aa-130">Run the app</span></span>

<span data-ttu-id="d89aa-131">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="d89aa-131">Run the following commands:</span></span>

```console
cd aspnetcoreapp
dotnet run
```

<span data-ttu-id="d89aa-132">在命令行界面指明应用已启动后，转到 [https://localhost:5001](https://localhost:5001)。</span><span class="sxs-lookup"><span data-stu-id="d89aa-132">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span> <span data-ttu-id="d89aa-133">单击“接受”，接受隐私和 cookie 政策。</span><span class="sxs-lookup"><span data-stu-id="d89aa-133">Click **Accept** to accept the privacy and cookie policy.</span></span> <span data-ttu-id="d89aa-134">此应用不保留个人信息。</span><span class="sxs-lookup"><span data-stu-id="d89aa-134">This app doesn't keep personal information.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="d89aa-135">编辑 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="d89aa-135">Edit a Razor page</span></span>

<span data-ttu-id="d89aa-136">打开 Pages/About.cshtml，并使用以下突出显示标记修改页面：</span><span class="sxs-lookup"><span data-stu-id="d89aa-136">Open *Pages/Index.cshtml* and modify the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="d89aa-137">转到 [https://localhost:5001](https://localhost:5001)，并验证更改是否显示。</span><span class="sxs-lookup"><span data-stu-id="d89aa-137">Browse to [https://localhost:5001](https://localhost:5001), and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d89aa-138">后续步骤</span><span class="sxs-lookup"><span data-stu-id="d89aa-138">Next steps</span></span>

<span data-ttu-id="d89aa-139">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="d89aa-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d89aa-140">创建 Web 应用项目。</span><span class="sxs-lookup"><span data-stu-id="d89aa-140">Create a web app project.</span></span>
> * <span data-ttu-id="d89aa-141">启用本地 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="d89aa-141">Enable local HTTPS.</span></span>
> * <span data-ttu-id="d89aa-142">运行该项目。</span><span class="sxs-lookup"><span data-stu-id="d89aa-142">Run the project.</span></span>
> * <span data-ttu-id="d89aa-143">进行更改。</span><span class="sxs-lookup"><span data-stu-id="d89aa-143">Make a change.</span></span>

<span data-ttu-id="d89aa-144">要了解有关 ASP.NET Core 的详细信息，请参阅简介中推荐的学习路径：</span><span class="sxs-lookup"><span data-stu-id="d89aa-144">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
