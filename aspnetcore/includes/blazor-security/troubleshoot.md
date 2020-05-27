## <a name="troubleshoot"></a>疑难解答

### <a name="cookies-and-site-data"></a>Cookie 和站点数据

Cookie 和站点数据可跨应用程序更新保持不变，并干扰测试和故障排除。 更改应用程序代码、更改提供程序的用户帐户更改或提供程序应用配置更改时，请清除以下内容：

* 用户登录 cookie
* 应用 cookie
* 缓存和存储的站点数据

防止延迟 cookie 和站点数据干扰测试和故障排除的一种方法是：

* 配置浏览器
  * 使用浏览器进行测试，你可以将其配置为在每次关闭浏览器时删除所有 cookie 和站点数据。
  * 请确保在应用程序、测试用户或提供程序配置的任何更改之间手动或通过 IDE 关闭浏览器。
* 在 Visual Studio 中使用自定义命令以 incognito 或 private 模式打开浏览器：
  * 从 Visual Studio 的 "**运行**" 按钮打开 "**浏览方式**" 对话框。
  * 选择“添加”按钮。
  * 在 "**程序**" 字段中提供浏览器的路径。
  * 在 "**参数**" 字段中，提供浏览器用来在 incognito 或专用模式下打开的命令行选项以及应用的 URL。 例如：
    * Google Chrome &ndash;`--incognito --new-window https://localhost:5001`
    * Mozilla Firefox &ndash;`-private -url https://localhost:5001`
  * 在 "**友好名称**" 字段中提供名称。 例如，`Firefox Auth Testing` 。
  * 选择“确定”  按钮。
  * 若要避免必须为应用的每个测试迭代选择浏览器配置文件，请使用 "**设置为默认值**" 按钮将配置文件设置为默认配置文件。
  * 请确保在对应用程序、测试用户或提供程序配置进行任何更改之间的 IDE 关闭浏览器。

### <a name="run-the-server-app"></a>运行服务器应用程序

在对托管的 Blazor 应用进行测试和故障排除时，请确保从**服务器**项目运行应用。 例如，在 Visual Studio 中，确认在用以下任一方法启动应用之前**解决方案资源管理器**中突出显示了服务器项目：

* 选择“运行”按钮。
* 使用菜单中的 "**调试**" "  >  **开始调试**"。
* 按 <kbd>F5</kbd>。

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a>检查 JSON Web 令牌的内容（JWT）

若要对 JSON Web 令牌（JWT）进行解码，请使用 Microsoft 的[jwt.ms](https://jwt.ms/)工具。 UI 中的值永远不会离开浏览器。
