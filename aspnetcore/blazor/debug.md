---
title:“调试 ASP.NET Core Blazor WebAssembly”author: description:“了解如何调试 Blazor 应用。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---
# <a name="debug-aspnet-core-blazor-webassembly"></a>调试 ASP.NET Core Blazor WebAssembly

[Daniel Roth](https://github.com/danroth27)

可以使用基于 Chromium 的浏览器 (Microsoft Edge/Chrome) 中的浏览器开发工具调试 Blazor WebAssembly 应用。  也可以使用 Visual Studio 或 Visual Studio Code 调试应用。

可用方案包括：

* 设置和删除断点。
* 使用 Visual Studio 和 Visual Studio Code 中的调试支持来运行应用（<kbd>F5</kbd> 支持）。
* 单步执行代码 (<kbd>F10</kbd>)。
* 在浏览器中使用 <kbd>F8</kbd> 恢复代码执行，在 Visual Studio 或 Visual Studio Code 中使用 <kbd>F5</kbd> 恢复代码执行。
* 在“局部变量”视图中，观察局部变量的值。
* 查看调用堆栈，包括从 JavaScript 到 .NET 以及从 .NET 到 JavaScript 的调用链。

目前，无法执行以下操作：

* 出现未经处理的异常时中断。
* 在应用启动期间命中断点。

在即将发布的版本中，我们将继续改进调试体验。

## <a name="prerequisites"></a>先决条件

调试需要使用以下浏览器之一：

* Microsoft Edge（版本 80 或更高版本）
* Google Chrome（版本 70 或更高版本）

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>在 Visual Studio 和 Visual Studio Code 中启用调试

若要为现有 Blazor WebAssembly 应用启用调试，请更新启动项目中的 launchSettings.json 文件，使每个启动配置文件包含以下 `inspectUri` 属性：

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

更新后，launchSettings.json 文件应类似于以下示例：

[!code-json[](debug/launchSettings.json?highlight=14,22)]

`inspectUri` 属性具有以下作用：

* 使 IDE 能够检测到该应用为 Blazor WebAssembly 应用。
* 指示脚本调试基础结构通过 Blazor 的调试代理连接到浏览器。

## <a name="visual-studio"></a>Visual Studio

要在 Visual Studio 中调试 Blazor WebAssembly 应用，请按以下步骤执行：

1. 创建一个由 ASP.NET Core 托管的新 Blazor WebAssembly 应用。
1. 按 <kbd>F5</kbd> 在调试器中运行应用。
1. 在 `IncrementCount` 方法的 Counter.razor 中设置一个断点。
1. 浏览到“计数器”选项卡，选择该按钮以命中断点：

   ![调试计数器](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. 查看局部变量窗口中 `currentCount` 字段的值：

   ![查看局部变量](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. 按 <kbd>F5</kbd> 继续执行。

调试 Blazor WebAssembly 应用时，还可以调试服务器代码：

1. 在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 的 FetchData.razor 页面中设置一个断点。
1. 在 `Get` 操作方法的 `WeatherForecastController` 中设置一个断点。
1. 浏览到“提取数据”选项卡，在 `FetchData` 组件中的首个断点向服务器发出 HTTP 请求前命中断点：

   ![调试提取数据](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. 按 <kbd>F5</kbd> 继续执行，然后在服务器上命中 `WeatherForecastController` 中的断点：

   ![调试服务器](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. 再次按 <kbd>F5</kbd> 继续执行，查看是否呈现天气预报表。

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

要在 Visual Studio Code 中调试 Blazor WebAssembly 应用，请按以下步骤执行：
 
1. 安装 [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)和 [JavaScript 调试程序（Nightly 版）](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly)，并将 `debug.javascript.usePreview` 设置为 `true`。

   ![扩展](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![JS 预览版调试程序](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. 打开已启用调试的现有 Blazor WebAssembly 应用。

   * 如果收到以下通知告知你需要其他设置才能启用调试，请确认是否已安装正确的扩展并已启用 JavaScript 预览版调试，然后重新加载此窗口：

     ![其他设置要求](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * 系统显示一个通知，询问是否将所需资产添加到应用以进行构建和调试。 选择“是”：

     ![添加所需资产](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. 按照以下两个步骤在调试程序中启动应用：

   1\. 首先，使用“.NET Core 启动 (Blazor 独立版)”启动配置启动应用 。

   2\. 应用启动后，使用“Chrome 中的 .NET Core 调试 Blazor Web 程序集”启动配置来启动浏览器（需要使用 Chrome） 。 若要使用 Microsoft Edge 而不是 Chrome，请将 .vscode/launch.json 中的启动配置的 `type` 从 `pwa-chrome` 更改为 `pwa-msedge`。

1. 在 `Counter` 组件的 `IncrementCount` 方法中设置一个断点，然后选择该按钮命中该断点。

   ![VS Code 中的调试计数器](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a>在浏览器中调试

1. 在开发环境中运行该应用的调试版本。

1. 按 <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>。

1. 浏览器必须在启用远程调试的情况下运行。 如果远程调试未启用，则系统会生成“无法找到可调试的浏览器标签页”错误页面。 该错误页面包含有关一些说明，指示应在调试端口处于打开状态的情况下运行浏览器以便 Blazor 调试代理可以连接到应用。 请关闭所有浏览器实例，并按照说明重启浏览器。

在启用远程调试的情况下运行浏览器后，按调试键盘快捷方式会打开新的调试程序标签页。片刻后，“源”选项卡显示应用中的 .NET 程序集的列表。 展开每个程序集并找到可用于调试的 *.cs*/ *.razor* 源文件。 设置断点，然后切换回应用的选项卡，断点在代码执行时被命中。 命中断点后，正常单步执行代码 (<kbd>F10</kbd>) 或恢复代码执行 (<kbd>F8</kbd>)。

Blazor 提供调试代理，该代理实现 [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/)，并使用特定于 .NET 的信息扩展该协议。 按下调试键盘快捷方式后，Blazor 会将 Chrome 开发者工具指向代理。 代理连接到要调试的浏览器窗口（因此需要启用远程调试）。

## <a name="browser-source-maps"></a>浏览器源映射

浏览器源映射允许浏览器将编译后的文件映射回其原始源文件，并且通常用于客户端调试。 但是，Blazor 当前不直接将 C# 映射到 JavaScript/WASM。 相反，Blazor 在浏览器中进行 IL 解释，因此源映射不相关。

## <a name="troubleshoot"></a>疑难解答

如果遇到错误，以下提示可能有所帮助：

在“调试程序”选项卡中，在浏览器中打开开发人员工具。 在控制台中，执行 `localStorage.clear()` 以删除所有断点。
