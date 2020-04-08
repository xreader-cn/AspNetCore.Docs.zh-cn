---
title: 利用 ASP.NET Core Blazor WebAssembly 生成渐进式 Web 应用程序
author: guardrex
description: 了解如何生成基于 Blazor 的渐进式 Web 应用 (PWA)，这类应用使用新式浏览器功能，与桌面应用行为一致。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/progressive-web-app
ms.openlocfilehash: fe69e51aefae9c80e5bb4b78151d384ce25d41a7
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218942"
---
# <a name="build-progressive-web-applications-with-aspnet-core-blazor-webassembly"></a>利用 ASP.NET Core Blazor WebAssembly 生成渐进式 Web 应用

作者：[Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

渐进式 Web 应用 (PWA) 是一种单页应用程序 (SPA)，它使用新式浏览器 API 和功能以表现得如桌面应用。 Blazor WebAssembly 是基于标准的客户端 Web 应用平台，因此它可以使用任何浏览器 API，包括以下功能所需的 PWA API：

* 脱机工作并即时加载（不受网络速度影响）。
* 在自己的应用窗口中运行，而不仅仅是在浏览器窗口中运行。
* 从主机操作系统的开始菜单、扩展坞或主屏幕启动。
* 从后端服务器接收推送通知，即使用户没有在使用该应用。
* 在后台自动更新。

使用“渐进式”一词来描述此类应用的原因如下  ：

* 用户可能先是在其网络浏览器中发现应用并使用它，就像任何其他单页应用程序一样。
* 过了一段时间后，用户进而将其安装到操作系统中并启用推送通知。

## <a name="create-a-project-from-the-pwa-template"></a>通过 PWA 模板创建项目

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在“新建项目”对话框中创建新的“Blazor WebAssembly 应用”时，请选中“进度 Web 应用”复选框    ：

![Visual Studio 的“新建项目”对话框中选中“渐进式 Web 应用”复选框。](progressive-web-app/_static/image1.png)

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

-->

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code/.NET Core CLI](#tab/visual-studio-code+netcore-cli)

使用 `--pwa` 开关在命令 shell 中创建 PWA 项目：

```dotnetcli
dotnet new blazorwasm -o MyNewProject --pwa
```

---

（可选）可以为通过 ASP.NET Core 托管的模板创建的应用配置 PWA。 PWA 方案独立于托管模型。

## <a name="installation-and-app-manifest"></a>安装和应用部件清单 (manifest)

访问使用 PWA 模板创建的应用时，用户可以选择将应用安装到其 OS 的开始菜单、扩展坞或主屏幕。 此选项的显示方式取决于用户的浏览器。 使用基于桌面 Chromium 的浏览器（如 Edge 或 Chrome）时，URL 栏中会出现“添加”按钮  。 用户选择“添加”按钮后，他们将收到一个确认对话框  ：

![Google Chrome 中的确认对话框会向用户显示“MyBlazorPwa”应用的“安装”按钮。](progressive-web-app/_static/image2.png)

在 iOS 上，访问者可以通过 Safari 的“共享”按钮和“添加到主屏幕”选项安装 PWA   。 在适用于 Android 的 Chrome 上，用户应该选择右上角的“菜单”按钮，然后选择“添加到主屏幕”   。

安装后，应用将显示在其自己的窗口中，没有任何地址栏：

![“MyBlazorPwa”应用在 Google Chrome 中运行时不显示地址栏。](progressive-web-app/_static/image3.png)

若要自定义窗口的标题、配色方案、图标或其他详细信息，请参阅项目 wwwroot 目录中的 manifest.json 文件   。 此文件的架构由 Web 标准定义。 有关详细信息，请参阅 [MDN Web 文档：Web 应用部件清单](https://developer.mozilla.org/docs/Web/Manifest)。

## <a name="offline-support"></a>脱机支持

默认情况下，使用 PWA 模板选项创建的应用支持脱机运行。 用户首次访问该应用时需要联机访问。 浏览器会自动下载并缓存脱机操作所需的所有资源。

> [!IMPORTANT]
> 开发支持会干扰常规开发周期（进行更改和测试更改）。 因此，脱机支持只对已发布的应用启用  。 

> [!WARNING]
> 如果打算分发启用脱机的 PWA，则存在[几个重要的警告和注意事项](#caveats-for-offline-pwas)。 这些方案是脱机 PWA 所固有的，而不是特定于 Blazor。 在对启用了脱机的应用的工作方式做出假设之前，请务必阅读并了解这些注意事项。

了解脱机支持的运行原理：

1. 发布应用。 有关详细信息，请参阅 <xref:host-and-deploy/blazor/index#publish-the-app>。
1. 将应用部署到支持 HTTPS 的服务器，并在浏览器中通过安全的 HTTPS 地址访问应用。
1. 打开浏览器的开发工具，并在“应用程序”选项卡上验证是否为主机注册了“服务工作进程”   ：

   ![Google Chrome 开发者工具的“应用程序”选项卡显示一个已激活且正在运行的服务工作进程。](progressive-web-app/_static/image4.png)

1. 重载页面并检查“网络”选项卡  。“服务工作进程”或“内存缓存”作为页面所有资产的源列出   ：

   ![Google Chrome 开发者工具的“网络”选项卡，显示了页面所有资产的源。](progressive-web-app/_static/image5.png)

1. 要验证浏览器是否不依赖网络访问来加载应用，请执行以下任一操作：

   * 关闭 Web 服务器，并查看应用如何继续正常运行，包括页面重载。 同样，当网络连接速度缓慢时，应用仍可正常运行。
   * 指示浏览器模拟“网络”选项卡中的脱机模式  ：

   ![Google Chrome 开发者工具的“网络”选项卡，其中浏览器模式下拉菜单从“联机”更改为“脱机”。](progressive-web-app/_static/image6.png)

使用服务工作进程的脱机支持是一项 Web 标准，并非特定于 Blazor。 有关服务工作进程的详细信息，请参阅 [MDN Web文档：服务工作进程 API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API)。 要了解有关服务工作进程常见使用模式的详细信息，请参阅 [Google Web：服务工作进程生命周期](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle)。

Blazor 的 PWA 模板产生两个服务工作进程文件：

* 在开发过程中使用的 wwwroot/service-worker.js  。
* 在发布应用后使用的 wwwroot/service-worker.published.js  。

要在两个服务工作进程文件之间共享逻辑，请考虑使用以下方法：

* 添加第三个 JavaScript 文件来保存通用逻辑。
* 使用 [self.importScripts](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) 将通用逻辑同时加载到两个服务工作进程文件中。

### <a name="cache-first-fetch-strategy"></a>缓存优先提取策略

内置 service-worker.published.js 服务工作进程使用“缓存优先”策略来解析请求   。 这意味着，无论用户是否有网络访问权限或服务器上是否有更新的内容，服务工作进程始终优先返回缓存的内容。

缓存优先策略非常有用，因为：

* 它可确保可靠性。  &ndash; 网络访问不是布尔状态。 用户不只是“联机”或“脱机”：

  * 用户的设备可能假设它处于联机状态，但可能由于网络速度太慢，根本无法加载。
  * 网络可能会对某些 URL 返回无效结果（例如，当有一个强制 WIFI 门户当前正在阻止或重定向某些请求时）。
  
  这就是浏览器的 `navigator.onLine` API 不可靠并且不应依赖的原因。

* 它可确保正确性。  &ndash; 在生成脱机资源的缓存时，服务工作进程使用内容哈希，确保它在某个时刻提取了完整且自身一致的资源快照。 此缓存随后用作原子单元。 没有必要向网络请求更新的资源，因为需要的版本已经缓存了。 其他任何版本都会产生不一致和不兼容的风险（例如，尝试使用未一起编译的 .NET 程序集的版本）。

### <a name="background-updates"></a>后台更新

作为一种思维模型，你可以将脱机优先 PWA 的行为视为与可安装的移动应用类似。 无论网络连接情况如何，应用都会立即启动，但是已安装的应用逻辑来自可能不是最新版本的时间点快照。

Blazor PWA 模板生成的应用会在用户访问和具有正在工作的网络连接时，自动尝试在后台进行自我更新。 其工作方式如下所示：

* 在编译期间，项目将生成“服务工作进程资产清单”  。 默认情况下，这称为 service-worker-assets.js  。 该清单列出了应用脱机工作所需的所有静态资源，如 .NET 程序集、JavaScript 文件和 CSS，包括其内容哈希。 此资源列表由服务工作进程加载，这样它便知道要缓存哪些资源。
* 用户每次访问应用时，浏览器都会在后台重新请求 service-worker.js 和 service-worker-assets.js   。 将文件与现有已安装的服务工作进程逐字节进行比较。 如果服务器为这些文件中的任一文件返回更改后的内容，则服务工作进程将尝试安装其自身的新版本。
* 安装自身的新版本时，服务工作进程将为脱机资源创建新的独立缓存，并开始使用 service-worker-assets.js 中列出的资源填充该缓存  。 此逻辑在 service-worker.published.js 内的 `onInstall` 函数中实现  。
* 如果所有资源正确加载，并且所有内容哈希匹配，则此进程将顺利完成。 如果成功完成，新服务工作进程将进入“等待激活”状态  。 用户关闭应用后（没有仍未关闭的应用选项卡或窗口），新的服务工作进程将变为活动状态，并用于后续的应用访问  。 旧服务工作进程及其缓存被删除。
* 如果该进程未成功完成，则弃用新的服务工作进程实例。 用户下次访问时将再次尝试此更新进程，届时客户端最好使用更好的网络连接来完成请求。

通过编辑服务工作进程逻辑来自定义此进程。 上述行为均不特定于 Blazor，只是 PWA 模板选项提供的默认体验。 有关详细信息，请参阅 [MDN Web 文档：服务工作进程 API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API)。

### <a name="how-requests-are-resolved"></a>如何解析请求

如上[缓存优先提取策略](#cache-first-fetch-strategy)部分所述，默认服务工作进程使用缓存优先策略，这意味着它会尽量提供缓存的内容（如果有）  。 如果没有对某个 URL 缓存任何内容（例如，从后端 API 请求数据时），则服务工作进程会回退到定期网络请求。 如果服务器可访问，该网络请求将会成功。 此逻辑在 service-worker.published.js 内的 `onFetch` 中实现  。

如果应用的 Razor 组件依赖于从后端 API 请求数据，并且你想要针对因网络不可用而导致请求失败的情况提供友好的用户体验，则需要在应用的组件中实现逻辑。 例如，对 `HttpClient` 请求使用 `try/catch`。

### <a name="support-server-rendered-pages"></a>支持服务器呈现的页面

仔细想想用户第一次导航到应用中的 URL（如 `/counter`）或任何其他深层链接时会发生什么情况。 在这些情况下，你不希望返回缓存为 `/counter` 的内容，而是需要浏览器加载缓存为 `/index.html` 的内容，以启动 Blazor WebAssembly 应用。 这些初始请求称为导航请求，而不是  ：

* 针对图像、样式表或其他文件的子资源请求  。
* 针对 API 数据的“提取/XHR”请求  。

默认服务工作进程包含导航请求的特例逻辑。 服务工作进程通过返回 `/index.html` 的缓存内容来解析这些请求，而不管请求的 URL 是什么。 此逻辑在 service-worker.published.js 内的 `onFetch` 函数中实现  。

如果应用具有必须返回服务器呈现的 HTML 的某些 URL（而不是从缓存中提供 `/index.html`），则需要在服务工作进程中编辑该逻辑。 如果所有包含 `/Identity/` 的 URL 都需要作为对服务器的常规仅联机请求来处理，则需修改 service-worker.published.js `onFetch` 逻辑  。 找到下列代码：

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

将代码更改为以下内容：

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
    && !event.request.url.includes('/Identity/');
```

如果不执行此操作，则无论网络连接情况如何，服务工作进程都将截获此类 URL 的请求，并使用 `/index.html` 解析这些请求。

### <a name="control-asset-caching"></a>控制资产缓存

如果项目定义了 `ServiceWorkerAssetsManifest` MSBuild 属性，则 Blazor 的生成工具将生成具有指定名称的服务工作进程资产清单。 默认 PWA 模板生成包含以下属性的项目文件：

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

该文件位于“wwwroot”输出目录中  ，因此浏览器可以通过请求 `/service-worker-assets.js` 检索此文件。 要查看此文件的内容，请在文本编辑器中打开 /bin/Debug/{TARGET FRAMEWORK}/wwwroot/service-worker-assets.js  。 不过，请不要编辑该文件，因为它会在每次生成时重新生成。

默认情况下，此清单列出：

* 任何 Blazor 管理的资源（如 .NET 程序集）以及脱机工作所需的 .NET WebAssembly 运行时文件。
* 用于发布到应用的 wwwroot 目录的所有资源，例如图像、样式表和 JavaScript 文件，包括由外部项目和 NuGet 包提供的静态 Web 资产  。

通过编辑 service-worker.published.js 中 `onInstall` 中的逻辑，可控制服务工作进程所提取和缓存的资源  。 默认情况下，服务工作进程将提取并缓存与典型 Web 文件扩展名匹配的文件，例如 .html、.css、.js、.wasm 等文件，以及特定于 Blazor WebAssembly 的文件类型（.dll、.pdb）       。

要包括应用的 wwwroot 目录中不存在的其他资源，请定义额外的 MSBuild `ItemGroup` 条目，如以下示例所示  ：

```xml
<ItemGroup>
  <ServiceWorkerAssetsManifestItem Include="MyDirectory\AnotherFile.json"
    RelativePath="MyDirectory\AnotherFile.json" AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

`AssetUrl` 元数据指定了在提取要缓存的资源时浏览器应使用的基相对 URL。 它可以独立于磁盘上的原始源文件名。

> [!IMPORTANT]
> 添加 `ServiceWorkerAssetsManifestItem` 不会导致文件发布到应用的 wwwroot 目录  。 必须单独控制发布输出。 `ServiceWorkerAssetsManifestItem` 仅导致服务工作进程清单中出现一个额外条目。

## <a name="push-notifications"></a>推送通知

与任何其他 PWA 一样，Blazor WebAssembly PWA 可以从后端服务器接收推送通知。 服务器可以随时发送推送通知，即使用户不常使用该应用也是如此。 例如，当其他用户执行相关操作时，可以发送推送通知。

发送推送通知的机制完全独立于 Blazor WebAssembly，因为它是由可以使用任何技术的后端服务器实现的。 如果想要从 ASP.NET Core 服务器发送推送通知，请考虑[使用与 Blazing Pizza 研讨会中采用的方法类似的方法](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications)。

在客户端上接收和显示推送通知的机制也独立于 Blazor WebAssembly，因为它是在服务工作进程 JavaScript 文件中实现的。 例如，请参阅[在 Blazing Pizza 研讨会中使用的方法](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications)。

## <a name="caveats-for-offline-pwas"></a>脱机 PWA 的注意事项

并非所有应用都应尝试支持脱机使用。 脱机支持增加了相当大的复杂性，但并不总是与所需的用例相关。

脱机支持通常仅适用于：

* 主数据存储是浏览器的本地数据存储。 例如，该方法与后述应用相关：该应用具有一个用于 [IoT](https://en.wikipedia.org/wiki/Internet_of_things) 设备的 UI，且该设备将数据存储在 `localStorage` 或 [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API) 中。
* 应用执行大量工作来提取和缓存与每个用户相关的后端 API 数据，以便用户可以脱机浏览数据。 应用必须支持编辑，需要生成一个用于跟踪更改并将数据与后端进行同步的系统。
* 目标是在不考虑网络状况的情况下保证应用立即加载， 则需要对后端 API 请求实现适当的用户体验，以显示请求的进度，并在请求因网络不可用而失败时妥善处理。

此外，支持脱机的 PWA 必须处理一系列额外的复杂操作。 开发者应认真熟悉以下部分中的注意事项。

### <a name="offline-support-only-when-published"></a>仅在发布后支持脱机

在开发过程中，通常希望看到各项更改立即反映在浏览器中，而无需经过后台更新过程。 因此，Blazor 的 PWA 模板仅在发布后启用脱机支持。

在构建支持脱机的应用时，仅在开发环境中测试应用是不够的。 必须在已发布状态下测试应用，了解它对不同网络情况的响应。

### <a name="update-completion-after-user-navigation-away-from-app"></a>用户导航离开应用后完成更新

用户导航离开了所有选项卡中的应用后，更新才会完成。 如[后台更新](#background-updates)部分中所述，在将更新部署到应用后，浏览器将提取更新后的服务工作进程文件以开始更新进程。

令许多开发人员惊讶的是，即使此更新完成，在用户导航离开所有选项卡之前，它也不会生效  。 刷新显示应用的选项卡也不行，即使它是显示应用的唯一选项卡也是如此  。 在应用完全关闭之前，新的服务工作进程将保持“正在等待激活”状态  。 这并不特定于 Blazor，而是标准 Web 平台行为。 

这通常会给试图测试对其服务工作进程或脱机缓存资源的更新的开发人员造成麻烦。 如果签入浏览器的开发者工具，可能会看到如下所示的内容：

![Google Chrome 的“应用程序”选项显示该应用的服务工作进程处于“正在等待激活”状态。](progressive-web-app/_static/image7.png)

只要“客户端”列表（即显示应用的选项卡或窗口）不为空，工作进程就会继续等待。 服务工作进程这样做的原因是为了保证一致性。 一致性意味着所有资源都是从同一原子缓存中提取的。

测试更改时，你可能会发现，单击如上面的屏幕截图中所示的“skipWaiting”链接，然后重载页面，这样操作会比较方便。 可以通过以下方式为所有用户自动实现此操作：对服务工作进程进行编码，[跳过“等待”阶段并在更新时立即激活](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase)。 如果跳过等待阶段，则无法保证资源始终一致地提取自同一缓存实例。

### <a name="users-may-run-any-historical-version-of-the-app"></a>用户可以运行应用的任何历史版本

Web 开发者通常期望用户仅运行其 Web 应用的最新部署版本，因为在传统的 Web 分发模型中这是正常现象。 但是，脱机优先 PWA 更类似于本机移动应用，用户不必运行最新版本。

如[后台更新](#background-updates)部分中所述，在将更新部署到应用后，每个现有用户将继续使用以前的版本，至少再进行一次访问（因为更新在后台进行，且在用户离开后才会激活）  。 此外，使用的上一个版本不一定是之前所部署的版本。 之前的版本可以是任何一个历史版本，具体取决于用户上次完成更新的时间  。

如果应用的前端和后端部分需要就 API 请求的架构达成一致，这可能是一个问题。 要部署后向不兼容的 API 架构变更，必须先确保所有用户都已升级， 或者阻止用户使用不兼容的旧应用版本。 此方案的要求与本机移动应用的要求相同。 如果在服务器 API 中部署中断性变更，则尚未更新的用户的客户端应用将中断。

如果可以，请勿将中断性变更部署到后端 API。 但如果必须这样做，请考虑使用[标准服务工作进程 API（如 ServiceWorkerRegistration）](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration)，确定应用是否为最新版本；如果不是，则阻止使用。

### <a name="interference-with-server-rendered-pages"></a>干扰服务器呈现的页面

如[支持服务器呈现的页面](#support-server-rendered-pages)部分中所述，若要绕过服务工作进程为所有导航请求返回 `/index.html` 内容的行为，请在服务工作进程中编辑该逻辑。

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a>默认缓存所有服务工作进程资产清单内容

如[控制资产缓存](#control-asset-caching)部分中所述，文件 service-worker-assets.js 是在生成过程中产生的，并且列出了服务工作进程应提取和缓存的所有资产  。

由于此列表默认包含发出到 wwwroot 的所有内容（包括外部包和项目提供的内容），因此必须注意不要将太多内容放在此处  。 如果 wwwroot 目录包含数百万个图像，服务工作进程会尝试提取并缓存它们，这会消耗过多的带宽，并且很可能无法成功完成  。

通过编辑 service-worker.published.js 中的 `onInstall` 函数  ，可以实现任意逻辑以控制应提取和缓存清单内容的哪个子集。

### <a name="interaction-with-authentication"></a>与身份验证交互

可以结合使用 PWA 模板选项和身份验证选项。 在用户具有网络连接时，支持脱机的 PWA 还可以支持身份验证。

当用户没有网络连接时，他们无法进行身份验证或获取访问令牌。 默认情况下，在没有网络访问权限的情况下尝试访问登录页面会导致出现“网络错误”消息。

必须设计一个 UI 流，使用户能够在脱机时执行实用的操作，而无需尝试进行身份验证或获取访问令牌。 或者，将应用设计为在网络不可用时正常提示操作失败。 如果这在应用中不可行，你可能不希望启用脱机支持。
