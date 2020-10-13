---
title: 应用脱机文件 (app_offline.htm)
author: rick-anderson
description: 了解如何将应用脱机文件 (`app_offline.htm`) 与 ASP.NET Core 模块配合使用。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 95dfadd084af5909fee754308ad5d65f54d4875d
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755148"
---
# <a name="app-offline-file-app_offlinehtm"></a><span data-ttu-id="89769-103">应用脱机文件 (`app_offline.htm`)</span><span class="sxs-lookup"><span data-stu-id="89769-103">App Offline file (`app_offline.htm`)</span></span>

<span data-ttu-id="89769-104">ASP.NET Core 模块使用应用脱机文件 (`app_offline.htm`) 来关闭应用。</span><span class="sxs-lookup"><span data-stu-id="89769-104">The App Offline file (`app_offline.htm`) is used by the ASP.NET Core Module to shut down an app.</span></span>

<span data-ttu-id="89769-105">如果在应用的根目录中检测到名为“`app_offline.htm`”的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="89769-105">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shut down the app and stop processing incoming requests.</span></span> <span data-ttu-id="89769-106">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将停止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="89769-106">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module stops the running process.</span></span>

<span data-ttu-id="89769-107">存在 `app_offline.htm` 文件时，ASP.NET Core 模块会通过发送回 `app_offline.htm` 文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="89769-107">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="89769-108">删除 `app_offline.htm` 文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="89769-108">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="89769-109">使用进程外托管模型时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="89769-109">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="89769-110">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="89769-110">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="locked-deployment-files"></a><span data-ttu-id="89769-111">锁定的部署文件</span><span class="sxs-lookup"><span data-stu-id="89769-111">Locked deployment files</span></span>

<span data-ttu-id="89769-112">如果应用正在运行，部署文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="89769-112">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="89769-113">在部署期间，无法覆盖已锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="89769-113">Locked files can't be overwritten during deployment.</span></span>

<span data-ttu-id="89769-114">`app_offline.htm` 是释放锁定文件的主要机制。</span><span class="sxs-lookup"><span data-stu-id="89769-114">`app_offline.htm` is the primary mechanism to release locked files.</span></span> <span data-ttu-id="89769-115">`app_offline.htm` 供 Web 部署用于正确地停止和启动应用。</span><span class="sxs-lookup"><span data-stu-id="89769-115">`app_offline.htm` is used by Web Deploy to properly stop and start the app.</span></span>

<span data-ttu-id="89769-116">可以手动将 `app_offline.htm` 用于启动和停止应用（需要 PowerShell 5 或更高版本）：</span><span class="sxs-lookup"><span data-stu-id="89769-116">`app_offline.htm` can be manually used to start and stop the app (requires PowerShell 5 or later):</span></span>

```powershell
$pathToApp = '{PATH TO APP}'

New-Item -Path $pathToApp app_offline.htm

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp app_offline.htm
```

<span data-ttu-id="89769-117">在上述 PowerShell 脚本中：</span><span class="sxs-lookup"><span data-stu-id="89769-117">In the preceding PowerShell script:</span></span>

* <span data-ttu-id="89769-118">占位符 `{PATH TO APP}` 是指向应用的路径。</span><span class="sxs-lookup"><span data-stu-id="89769-118">The placeholder `{PATH TO APP}` is the path to the app.</span></span>
* <span data-ttu-id="89769-119">`New-Item` 命令停止应用池。</span><span class="sxs-lookup"><span data-stu-id="89769-119">The `New-Item` command stops the app pool.</span></span>
* <span data-ttu-id="89769-120">`Remove-Item` 命令启动应用池。</span><span class="sxs-lookup"><span data-stu-id="89769-120">The `Remove-Item` command starts the app pool.</span></span>
* <span data-ttu-id="89769-121">开发人员提供 `New-Item` 命令和 `Remove-Item` 命令之间的命令来部署应用。</span><span class="sxs-lookup"><span data-stu-id="89769-121">Commands between the `New-Item` command and the `Remove-Item` command are provided by the developer to deploy the app.</span></span>

<span data-ttu-id="89769-122">还可以通过在服务器上的 IIS 管理器中手动停止应用池来解锁文件。</span><span class="sxs-lookup"><span data-stu-id="89769-122">Files can also be unlocked by manually stopping the app pool in the IIS Manager on the server.</span></span> <span data-ttu-id="89769-123">使用 IIS 管理器停止和重新启动应用池时，请勿使用 `app_offine.htm` 文件。</span><span class="sxs-lookup"><span data-stu-id="89769-123">Don't use the `app_offine.htm` file when using the IIS Manager to stop and restart the app pool.</span></span>
