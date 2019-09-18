---
title: ASP.NET Core 入门
author: rick-anderson
description: 介绍如何使用 ASP.NET Core 创建并运行简单的 Hello World 应用的快速教程。
ms.author: riande
ms.custom: mvc
ms.date: 05/15/2019
uid: getting-started
ms.openlocfilehash: d1edf91f1b37ba2b69732471dc6c1f306ac5ad24
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081134"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="6100b-103">教程：ASP.NET Core 入门</span><span class="sxs-lookup"><span data-stu-id="6100b-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="6100b-104">本教程展示了如何使用 .NET Core 命令行接口来创建并运行 ASP.NET Core Web 应用。</span><span class="sxs-lookup"><span data-stu-id="6100b-104">This tutorial shows how to use the .NET Core command-line interface to create and run an ASP.NET Core web app.</span></span>

<span data-ttu-id="6100b-105">你将了解如何：</span><span class="sxs-lookup"><span data-stu-id="6100b-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6100b-106">创建 Web 应用项目。</span><span class="sxs-lookup"><span data-stu-id="6100b-106">Create a web app project.</span></span>
> * <span data-ttu-id="6100b-107">信任开发证书。</span><span class="sxs-lookup"><span data-stu-id="6100b-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="6100b-108">运行应用。</span><span class="sxs-lookup"><span data-stu-id="6100b-108">Run the app.</span></span>
> * <span data-ttu-id="6100b-109">编辑 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="6100b-109">Edit a Razor page.</span></span>

<span data-ttu-id="6100b-110">最后，在本地计算机上运行工作 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="6100b-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Web 应用主页](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="6100b-112">系统必备</span><span class="sxs-lookup"><span data-stu-id="6100b-112">Prerequisites</span></span>

* [<span data-ttu-id="6100b-113">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="6100b-113">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web-app-project"></a><span data-ttu-id="6100b-114">创建 Web 应用项目</span><span class="sxs-lookup"><span data-stu-id="6100b-114">Create a web app project</span></span>

<span data-ttu-id="6100b-115">打开命令行界面，然后输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="6100b-115">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

### <a name="trust-the-development-certificate"></a><span data-ttu-id="6100b-116">信任开发证书</span><span class="sxs-lookup"><span data-stu-id="6100b-116">Trust the development certificate</span></span>

<span data-ttu-id="6100b-117">信任 HTTPS 开发证书：</span><span class="sxs-lookup"><span data-stu-id="6100b-117">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="6100b-118">Windows</span><span class="sxs-lookup"><span data-stu-id="6100b-118">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="6100b-119">以上命令会显示以下对话：</span><span class="sxs-lookup"><span data-stu-id="6100b-119">The preceding command displays the following dialog:</span></span>

![安全警告对话](~/getting-started/_static/cert.png)

<span data-ttu-id="6100b-121">如果你同意信任开发证书，请选择“是”。 </span><span class="sxs-lookup"><span data-stu-id="6100b-121">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="6100b-122">macOS</span><span class="sxs-lookup"><span data-stu-id="6100b-122">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="6100b-123">以上命令会显示以下消息：</span><span class="sxs-lookup"><span data-stu-id="6100b-123">The preceding command displays the following message:</span></span>

<span data-ttu-id="6100b-124">*请求信任 HTTPS 开发证书。如果证书尚不受信任，我们将运行以下命令：* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="6100b-124">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="6100b-125">此命令可能会提示你输入密码以在系统密钥链上安装证书。</span><span class="sxs-lookup"><span data-stu-id="6100b-125">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="6100b-126">如果你同意信任开发证书，请输入密码。</span><span class="sxs-lookup"><span data-stu-id="6100b-126">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="6100b-127">Linux</span><span class="sxs-lookup"><span data-stu-id="6100b-127">Linux</span></span>](#tab/linux)

<span data-ttu-id="6100b-128">对于适用于 Linux 的 Windows 子系统，请参阅[在适用于 Linux 的 Windows 子系统中信任 HTTPS 证书](xref:security/enforcing-ssl#wsl)。</span><span class="sxs-lookup"><span data-stu-id="6100b-128">For Windows Subsystem for Linux, see [Trust HTTPS certificate from Windows Subsystem for Linux](xref:security/enforcing-ssl#wsl).</span></span>

<span data-ttu-id="6100b-129">查看你的 Linux 分发对应的文档，了解如何信任 HTTPS 开发证书。</span><span class="sxs-lookup"><span data-stu-id="6100b-129">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="6100b-130">有关详细信息，请参阅[信任 ASP.NET Core HTTPS 开发证书](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span><span class="sxs-lookup"><span data-stu-id="6100b-130">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="6100b-131">运行应用</span><span class="sxs-lookup"><span data-stu-id="6100b-131">Run the app</span></span>

<span data-ttu-id="6100b-132">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6100b-132">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet run
```

<span data-ttu-id="6100b-133">在命令行界面指明应用已启动后，转到 [https://localhost:5001](https://localhost:5001)。</span><span class="sxs-lookup"><span data-stu-id="6100b-133">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span> <span data-ttu-id="6100b-134">单击“接受”，接受隐私和 cookie 政策  。</span><span class="sxs-lookup"><span data-stu-id="6100b-134">Click **Accept** to accept the privacy and cookie policy.</span></span> <span data-ttu-id="6100b-135">此应用不保留个人信息。</span><span class="sxs-lookup"><span data-stu-id="6100b-135">This app doesn't keep personal information.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="6100b-136">编辑 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="6100b-136">Edit a Razor page</span></span>

<span data-ttu-id="6100b-137">打开 Pages/Index.cshtml  ，并使用以下突出显示标记修改页面：</span><span class="sxs-lookup"><span data-stu-id="6100b-137">Open *Pages/Index.cshtml* and modify the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="6100b-138">转到 [https://localhost:5001](https://localhost:5001)，并验证更改是否显示。</span><span class="sxs-lookup"><span data-stu-id="6100b-138">Browse to [https://localhost:5001](https://localhost:5001), and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6100b-139">后续步骤</span><span class="sxs-lookup"><span data-stu-id="6100b-139">Next steps</span></span>

<span data-ttu-id="6100b-140">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="6100b-140">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6100b-141">创建 Web 应用项目。</span><span class="sxs-lookup"><span data-stu-id="6100b-141">Create a web app project.</span></span>
> * <span data-ttu-id="6100b-142">信任开发证书。</span><span class="sxs-lookup"><span data-stu-id="6100b-142">Trust the development certificate.</span></span>
> * <span data-ttu-id="6100b-143">运行该项目。</span><span class="sxs-lookup"><span data-stu-id="6100b-143">Run the project.</span></span>
> * <span data-ttu-id="6100b-144">进行更改。</span><span class="sxs-lookup"><span data-stu-id="6100b-144">Make a change.</span></span>

<span data-ttu-id="6100b-145">要了解有关 ASP.NET Core 的详细信息，请参阅简介中推荐的学习路径：</span><span class="sxs-lookup"><span data-stu-id="6100b-145">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
