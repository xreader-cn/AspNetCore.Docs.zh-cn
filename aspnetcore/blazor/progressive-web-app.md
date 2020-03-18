---
title: 利用 ASP.NET Core Blazor WebAssembly 生成渐进式 Web 应用程序
author: guardrex
description: 了解如何生成基于 Blazor 的渐进式 Web 应用程序 (PWA)，即使用新式浏览器功能以表现得如桌面应用的 Web 应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: blazor/progressive-web-app
ms.openlocfilehash: f1c1ce50f20bf579e67e1d4eb02cc7d9d681e354
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083559"
---
# <a name="build-progressive-web-applications-with-aspnet-core-opno-locblazor-webassembly"></a>利用 ASP.NET Core Blazor WebAssembly 生成渐进式 Web 应用程序

作者：[Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

渐进式 Web 应用程序 (PWA) 是一种基于 Web 的应用程序，它使用新式浏览器 API 和功能以表现得如桌面应用程序。 这些功能包括：

* 脱机工作并始终即时加载（不受网络速度影响）
* 能够在其自己的应用程序窗口中（而不只是在浏览器窗口中）运行
* 从主机操作系统 (OS) 的开始菜单、扩展坞或主屏幕启动
* 从后端服务器接收推送通知，即使用户没有在使用应用程序
* 在后台自动更新

用户可能会首先在 Web 浏览器中发现并使用该应用程序，就像其他任何单页应用程序 (SPA) 一样，然后再逐步将其安装到 OS 中并启用推送通知。 这就是我们使用术语“渐进式”的原因。

Blazor WebAssembly 是真正的基于标准的客户端 Web 应用程序平台，因此它可以使用任何浏览器 API，包括上面列出的功能所需的 PWA API。 与任何其他客户端 Web 技术一样，它可以脱机工作。

## <a name="pwa-template"></a>PWA 模板

创建新的 Blazor WebAssembly 应用程序时，你可选择添加 PWA 功能。 在 Visual Studio 中，该选项在项目创建对话框中以复选框形式提供：

![图像](https://user-images.githubusercontent.com/1101362/76207411-a6b54200-61f5-11ea-9dfc-6acd87a91428.png)

如果要在命令行上创建项目，可以使用 `--pwa` 标志。 例如，应用于对象的

```dotnetcli
dotnet new blazorwasm --pwa -o MyNewProject
```

在这两种情况下，你可根据需要将其与“托管的 ASP.NET Core”选项结合使用，但不一定非要这样做。 PWA 功能独立于托管模型。

## <a name="installation-and-app-manifest"></a>安装和应用部件清单 (manifest)

访问使用 PWA 模板选项创建的应用程序时，用户可以选择将应用程序安装到其 OS 的开始菜单、扩展坞或主屏幕。

此选项的显示方式取决于用户的浏览器。 例如，使用基于桌面 Chromium 的浏览器（如 Edge 或 Chrome）时，URL 栏中会出现“添加”按钮：

![图像](https://user-images.githubusercontent.com/1101362/76208127-f7796a80-61f6-11ea-8aea-7fba704be787.png)

在 iOS 上，访问者可以通过 Safari 的“共享”按钮和“添加到主屏幕”选项安装 PWA。 在适用于 Android 的 Chrome 上，用户应该点击右上角的“菜单”按钮，然后选择“添加到主屏幕”。

安装后，应用程序将显示在其自己的窗口中，没有任何地址栏。

![图像](https://user-images.githubusercontent.com/1101362/76208588-e2e9a200-61f7-11ea-85e1-8c3fc849b252.png)

若要自定义窗口的标题、配色方案、图标或其他详细信息，请参阅项目 wwwroot 目录中的 `manifest.json` 文件。 此文件的架构由 Web 标准定义。 有关详细文档，请参阅 https://developer.mozilla.org/docs/Web/Manifest。

## <a name="offline-support"></a>脱机支持

默认情况下，使用 PWA 模板选项创建的应用程序支持脱机运行。 用户必须先在联机时访问该应用程序，然后浏览器将自动下载并缓存脱机操作所需的所有资源。

> [!IMPORTANT]
> 脱机支持只对已发布的应用程序启用。 它在开发过程中未启用。 这是因为它会干扰常规开发周期（进行更改和测试更改）。

> [!WARNING]
> 如果你打算交付启用脱机的 PWA，则需要了解[几个重要的警告和注意事项](#caveats-for-offline-pwas)。 这些是脱机 PWA 所固有的，而不是特定于 Blazor。 在对启用了脱机的应用程序的工作方式做出假设之前，请务必阅读并了解这些注意事项。

若要查看脱机支持的工作方式，请首先[发布应用程序](https://docs.microsoft.com/aspnet/core/host-and-deploy/blazor/?view=aspnetcore-3.1&tabs=visual-studio#publish-the-app)，并将其托管在支持 HTTPS 的服务器上。 访问应用程序时，你应该能够打开浏览器的开发工具，并验证是否为主机注册了“服务工作进程”：

![图像](https://user-images.githubusercontent.com/1101362/76210294-bd5e9780-61fb-11ea-9869-65c55c62803d.png)

此外，如果你重载页面，则在“网络”选项卡上，你应可看到加载页面所需的所有资源都是从“服务工作进程”或“内存缓存”检索的：

![图像](https://user-images.githubusercontent.com/1101362/76210472-1d553e00-61fc-11ea-84c6-291644df709e.png)

这表明，浏览器不依赖于网络访问来加载应用程序。 若要验证这一点，你可关闭 Web 服务器，或指示浏览器模拟脱机模式：

![图像](https://user-images.githubusercontent.com/1101362/76210556-47a6fb80-61fc-11ea-9d12-20a8f6528744.png)

现在，即使无法访问 Web 服务器，你也应能够重载页面，并查看应用程序是否仍在加载和运行。 同样，即使你模拟非常缓慢的网络连接，页面仍会立即加载，因为它是独立于网络而加载的。

### <a name="service-worker"></a>服务工作进程

脱机支持可通过服务工作进程实现。 这是一种 Web 标准，不特定于 Blazor。 有关服务工作进程的文档，请参阅 https://developer.mozilla.org/docs/Web/API/Service_Worker_API。 若要了解有关服务工作进程的常见使用模式的详细信息，请参阅这篇精彩文章 - [服务工作进程生命周期](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle)。

Blazor 的 PWA 模板产生两个服务工作进程文件：

* 在开发过程中使用的 wwwroot/service-worker.js
* 在发布应用程序后使用的 wwwroot/service-worker.published.js

如果要在这两个文件之间共享逻辑，请考虑添加第三个 JavaScript 文件来保存公用逻辑，并使用 [`self.importScripts`](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) 将该逻辑加载到这两个文件中。

#### <a name="cache-first-fetch-strategy"></a>缓存优先提取策略

内置 service-worker.published.js 服务工作进程使用“缓存优先”策略来解析请求。 这意味着，无论用户是否有网络访问权限或服务器上是否有更新的内容，它始终优先返回缓存的内容（如果有）。

它的价值体现在以下两点：

* 它可确保可靠性。 网络访问不是布尔状态。 用户不只是“联机”或“脱机”。 事实上，用户的设备可能认为它处于联机状态，但可能由于网络速度太慢，根本无法加载。 或者，网络可能会对某些 URL 返回无效结果（例如，当有一个强制 WIFI 门户当前正在阻止或重定向某些请求时）。 这就是浏览器的 `navigator.onLine` API 不可靠并且不应依赖的原因。
* 它可确保正确性。 在生成脱机资源的缓存时，服务工作进程使用内容哈希，确保它在某个时刻提取了完整且自身一致的资源快照。 此缓存随后用作原子单元。 鉴于此，没有必要向网络请求更新的资源，因为你唯一需要的版本是已经缓存的版本。 其他任何版本都会产生不一致和不兼容的风险（例如，尝试使用未一起编译的 .NET 程序集的版本）。

#### <a name="background-updates"></a>后台更新

作为一种思维模型，你可以将脱机优先 PWA 的行为视为与可安装的移动应用类似。 无论网络连接情况如何，它都会立即启动，但是已安装的应用程序逻辑来自可能不是最新版本的时间点快照。

Blazor PWA 模板生成的应用程序会在用户访问和具有正在工作的网络连接时，自动尝试在后台进行自我更新。 其工作方式如下所示：

* 在编译期间，你的项目将生成“服务工作进程资产清单”。 默认情况下，这称为 service-worker-assets.js。 这列出了应用程序脱机工作所需的所有静态资源，如 .NET 程序集、JavaScript 文件、CSS 等，包括其内容哈希。 此列表由服务工作进程加载，这样它便知道要缓存哪些资源。
* 用户每次访问应用程序时，浏览器会在后台重新请求 service-worker.js 和 service-worker-assets.js。 如果服务器为这些文件中的任一文件返回更改的内容（与现有已安装的服务工作进程进行逐字节比较），则服务工作进程将尝试安装其自身的新版本。
* 安装自身的新版本时，服务工作进程将为脱机资源创建新的独立缓存，并开始使用 service-worker-assets.js 中列出的资源填充该缓存。 此逻辑在 service-worker.published.js 内的 `onInstall` 函数中实现。
* 如果该过程成功完成（即，所有资源都加载无误，所有内容哈希都匹配），则新的服务工作进程将进入“正在等待激活”状态。 一旦用户关闭应用程序（即，没有剩余的选项卡或窗口显示应用程序），则新的服务工作进程将变为“活动”，并将用于对应用程序的后续访问。 旧服务工作进程及其缓存被删除。
* 如果该过程未成功完成，则弃用新的服务工作进程实例。 用户下次访问时将再次尝试此更新过程，届时用户最好使用更好的网络连接来完成请求。

你可以通过编辑服务工作进程逻辑来自定义此过程的任何方面。 以上均不特定于 Blazor，只是 PWA 模板选项提供的建议。 有关详细信息，请参阅[服务工作进程文档](https://developer.mozilla.org/docs/Web/API/Service_Worker_API.)。

#### <a name="how-requests-are-resolved"></a>如何解析请求

如上所述，默认服务工作进程使用缓存优先策略，这意味着它会尽量提供缓存的内容（如果有）。 如果没有对某个 URL 缓存任何内容（例如，从后端 API 请求数据时），则服务工作进程回退到定期网络请求，该请求仅在服务器可访问时才会成功。 此逻辑在 service-worker.published.js 内的 `onFetch` 中实现。

如果你的 Blazor 组件依赖于从后端 API 请求数据，并且你想要在此类请求由于网络不可用而失败的情况下提供友好的用户体验，则需要在组件中实现逻辑。 例如，对 `HttpClient` 请求使用 `try/catch`。

#### <a name="support-server-rendered-pages"></a>支持服务器呈现的页面

仔细想想用户第一次导航到应用程序中的 URL（如 `/counter`）或任何其他深层链接时会发生什么情况。 在这些情况下，你不希望返回缓存为 `/counter` 的内容，而是需要浏览器加载缓存为 `/index.html` 的内容，以启动 Blazor WebAssembly 应用程序。 这些初始请求称为“导航”请求（与图像/CSS 等的子资源请求或 API 数据的提取/XHR 请求相对）。

默认服务工作进程包含导航请求的特例逻辑。 它通过返回 `/index.html` 的缓存内容来解析这些请求，而不管请求的 URL 是什么。 此逻辑在 service-worker.published.js 内的 `onFetch` 函数中实现。

如果应用程序具有必须返回服务器呈现的 HTML 的某些 URL（而不是从缓存中提供 `/index.html`），则你需要在服务工作进程中编辑该逻辑。 例如，如果所有包含 `/Identity/` 的 URL 都需要作为对服务器的常规仅联机请求来处理，则需修改 service-worker.published.js `onFetch` 逻辑。 找到下列代码：

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

将代码更改为以下内容：

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
    && !event.request.url.includes('/Identity/');
```

如果你不执行此操作，则无论网络连接情况如何，服务工作进程都将截获此类 URL 的请求，并使用 `/index.html` 解析这些请求。

#### <a name="control-asset-caching"></a>控制资产缓存

如果你的项目定义了一个名为 `ServiceWorkerAssetsManifest` 的MSBuild 属性，则 Blazor 的生成工具将生成具有指定名称的服务工作进程资产清单。 默认 PWA 模板生成包含以下内容的项目文件：

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

该文件位于“wwwroot”输出目录中，因此浏览器可以通过请求 `/service-worker-assets.js` 检索此文件。 要查看内容，请在文本编辑器中打开 YourProject\bin\Debug\netstandard2.1\wwwroot\service-worker-assets.js。 不过，请不要编辑该文件，因为它会在每次生成时重新生成。

默认情况下，此清单列出：

* 任何 Blazor 管理的资源（如 .NET 程序集）以及脱机工作所需的 .NET WebAssembly 运行时文件
* 要在 wwwroot 目录中发布的所有资源，如图像、CSS 文件和 JavaScript 文件。 这包括外部项目和 NuGet 包提供的静态 Web 资产。

可通过编辑 service-worker.published.js 中 `onInstall` 中的逻辑，控制哪些资源将由服务工作进程提取和缓存。 默认情况下，它将提取并缓存与典型 Web 文件扩展名匹配的文件，例如 .html、.css、.js、.wasm 等文件，以及特定于 Blazor WebAssembly 的文件类型（.dll、.pdb）。

如果你希望包括 wwwroot 目录中不存在的其他资源，可通过定义额外的 MSBuild 项目组条目来实现。 例如，在项目文件中，添加：

```xml
<ItemGroup>
    <ServiceWorkerAssetsManifestItem
        Include="MyDirectory\AnotherFile.json"
        RelativePath="MyDirectory\AnotherFile.json"
        AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

`AssetUrl` 元数据指定了在提取要缓存的资源时浏览器应使用的基相对 URL。 它可以独立于磁盘上的原始源文件名。

> [!IMPORTANT]
> 添加 `ServiceWorkerAssetsManifestItem` 不会导致文件发布到 wwwroot 目录。 你可以单独控制发布输出。 `ServiceWorkerAssetsManifestItem` 仅导致服务工作进程清单中出现一个额外条目。

## <a name="push-notifications"></a>推送通知

与任何其他 PWA 一样，Blazor WebAssembly PWA 可以从后端服务器接收推送通知。 你的服务器可以随时发送这些通知，即使用户未积极使用应用程序（例如，当其他用户执行可能相关的操作时）。

发送推送通知的机制完全独立于 Blazor WebAssembly，因为它是由可以使用任何技术的后端服务器实现的。 如果你想要从 ASP.NET Core 服务器发送推送通知，请考虑[使用类似于 Blazing Pizza 研讨会中的技术](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications)。

在客户端上接收和显示推送通知的机制也独立于 Blazor WebAssembly，因为它是在服务工作进程（即 JavaScript 文件）中实现的。 例如，你可以再次查看[在 Blazing Pizza 研讨会中使用的方法](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications)。

## <a name="caveats-for-offline-pwas"></a>脱机 PWA 的注意事项

并非所有应用程序都应尝试支持脱机使用。 它增加了很大的复杂性，但有时并没有必要。

脱机支持通常仅适用于：

* 如果主数据存储是浏览器的本地数据存储。 例如，为将数据存储在 `localStorage` 或 [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API) 中的 [IoT](https://en.wikipedia.org/wiki/Internet_of_things) 设备生成 UI 时。

* 如果你执行大量工作来提取和缓存与每个用户相关的后端 API 数据，则他们可以脱机浏览。 如果你支持编辑，你还需要生成一个系统来跟踪更改，并将其与后端同步。

* 如果你的目标是在不考虑网络状况的情况下保证应用程序立即加载， 则你需要对后端 API 请求实现适当的用户体验，以显示请求的进度，并在请求因网络不可用而失败时妥善处理。

此外，支持脱机的 PWA 需要处理一系列额外的复杂操作。 开发人员应认真熟悉以下注意事项。

### <a name="offline-support-only-when-published"></a>仅在发布后支持脱机

Blazor 的 PWA 模板仅在发布后启用脱机支持。 这是因为，在开发过程中，你通常希望看到各项更改立即反映在浏览器中，而无需经过后台更新过程。

因此，在生成支持脱机的应用程序时，在开发模式下测试应用程序是不够的。 必须在已发布状态下测试应用程序，了解它如何响应不同的网络情况。

### <a name="update-completion-after-user-navigation-away-from-app"></a>用户导航离开应用后完成更新

用户导航离开了应用程序的所有选项卡后，更新才会完成。 如[后台更新](#background-updates)中所述，在你将更新部署到应用程序后，浏览器将提取更新后的服务工作进程文件并开始更新过程。

令许多开发人员惊讶的是，即使此更新完成，在用户导航离开所有选项卡之前，它也不会生效。 刷新显示应用程序的选项卡也不行，即使它是显示应用程序的唯一选项卡。 在应用程序完全关闭之前，新的服务工作进程将保持“正在等待激活”状态。 这并不特定于 Blazor，而是标准 Web 平台行为。

这通常会给试图测试对其服务工作进程或脱机缓存资源的更新的开发人员造成麻烦。 如果你签入浏览器的开发工具，可能会看到如下所示的内容：

![图像](https://user-images.githubusercontent.com/1101362/76226394-b93f7380-6215-11ea-8572-7d52afee2dd8.png)

只要“客户端”列表（即显示应用程序的标签或窗口）不为空，工作进程就会继续等待。 服务工作进程这样做的原因是为了保证一致性，即所有资源均提取自同一原子缓存。

测试更改时，你可能会发现，单击如上面的屏幕截图中所示的“skipWaiting”链接，然后重载页面，这样操作会比较方便。 如果需要，你可以通过以下方式为所有用户自动实现此操作：对服务工作进程进行编码，[跳过“等待”阶段并在更新时立即激活](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase)。 但是，如果这样做，你无法保证资源始终一致地提取自同一缓存实例。

### <a name="users-may-run-any-historical-version-of-the-app"></a>用户可以运行应用的任何历史版本

Web 开发人员习惯性地期望用户仅运行其 Web 应用程序的最新部署版本，因为在传统的 Web 分发模型中这是正常现象。 但是，脱机优先 PWA 更类似于本机移动应用，用户不必运行最新版本。

如[后台更新](#background-updates)中所述，在你将更新部署到应用程序后，每个现有用户将继续使用以前的版本，至少再进行一次访问（因为更新在后台进行，且在用户离开后才会激活）。 此外，使用的先前版本不一定是你部署的上一个版本 - 它可以是任何历史版本，具体取决于用户上次完成更新的时间。

如果应用程序的前端和后端部分需要就 API 请求的架构达成一致，这可能是一个问题。 你须先确保所有用户都已升级，或者至少可阻止用户使用不兼容的旧版应用，才能部署后向不兼容的 API 架构变更。 这与本机移动应用类似。 如果在服务器 API 中部署中断性变更，则尚未更新的用户的客户端应用将中断。

如果可以，请勿将中断性变更部署到后端 API。 但如果必须这样做，请考虑使用[标准服务工作进程 API（如 `ServiceWorkerRegistration`）](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration)，确定应用程序是否为最新版本；如果不是，则阻止使用。

### <a name="interference-with-server-rendered-pages"></a>干扰服务器呈现的页面

[如上所述](#support-server-rendered-pages)，若你要绕过服务工作进程为所有导航请求返回 `/index.html` 内容的行为，需要在服务工作进程中编辑该逻辑。

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a>默认缓存所有服务工作进程资产清单内容

[如上所述](#control-asset-caching)，文件 service-worker-assets.js 是在生成过程中产生的，并且列出了服务工作进程应提取和缓存的所有资产。

由于此列表默认包含发出到 wwwroot 的所有内容（包括外部包和项目提供的内容），因此必须注意不要将太多内容放在此处。 例如，如果你的 wwwroot 目录包含数百万个图像，服务工作进程会尝试提取并缓存它们，这会消耗过多的带宽，并且很可能无法成功完成。

可以通过编辑 service-worker.published.js 中的 `onInstall` 函数，实现任意逻辑以控制应提取和缓存清单内容的哪个子集。

### <a name="interaction-with-authentication"></a>与身份验证交互

可以结合使用 PWA 模板选项和身份验证选项。 在用户具有网络连接时，支持脱机的 PWA 还可以支持身份验证。

但是，当用户没有网络连接时，他们将无法进行身份验证或获取访问令牌。 尝试访问“登录”页会默认显示一条指示“网络错误”的消息。

因此，你的工作就是设计一个 UI 流，使用户能够在脱机时执行实用的操作，而无需尝试进行身份验证或获取访问令牌，或者至少在这些情况下妥善地提示操作失败。 如果这在你的应用程序中不可行，你可能不希望启用脱机支持。
