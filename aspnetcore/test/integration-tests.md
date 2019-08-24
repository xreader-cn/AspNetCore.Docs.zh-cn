---
title: ASP.NET Core 中的集成测试
author: guardrex
description: 了解集成测试如何在基础结构级别（包括数据库、文件系统和网络）确保应用组件功能正常。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/23/2019
uid: test/integration-tests
ms.openlocfilehash: 195acd3e03f3de63ebd61767f2c86d1c0f38fca5
ms.sourcegitcommit: 983b31449fe398e6e922eb13e9eb6f4287ec91e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2019
ms.locfileid: "70017438"
---
# <a name="integration-tests-in-aspnet-core"></a>ASP.NET Core 中的集成测试

作者: [Luke Latham](https://github.com/guardrex)和[Steve Smith](https://ardalis.com/)

集成测试可确保应用程序的组件在包含应用程序支持的基础结构的级别 (例如数据库、文件系统和网络) 正常运行。 ASP.NET Core 支持结合使用单元测试框架和测试 web 主机和内存中测试服务器的集成测试。

本主题假定基本了解单元测试。 如果不熟悉测试概念, 请参阅[.Net Core 中的单元测试和 .NET Standard](/dotnet/core/testing/)主题及其链接的内容。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）

该示例应用是 Razor Pages 应用程序, 并假定基本了解 Razor Pages。 如果不熟悉 Razor Pages, 请参阅以下主题:

* [Razor 页面介绍](xref:razor-pages/index)
* [Razor 页面入门](xref:tutorials/razor-pages/razor-pages-start)
* [Razor 页面单元测试](xref:test/razor-pages-tests)

> [!NOTE]
> 对于测试 Spa, 我们建议使用[Selenium](https://www.seleniumhq.org/)这样的工具, 它可以自动执行浏览器。

## <a name="introduction-to-integration-tests"></a>集成测试简介

与[单元测试](/dotnet/core/testing/)相比, 集成测试在更广泛的级别上评估应用的组件。 单元测试用于测试独立的软件组件, 如单独的类方法。 集成测试确认两个或更多应用程序组件一起工作以生成预期的结果, 其中可能包括完全处理请求所需的每个组件。

这些广泛的测试用于测试应用程序的基础结构和整个框架, 通常包括以下组件:

* 数据库
* 文件系统
* 网络设备
* 请求-响应管道

单元测试使用称为*fakes*或*mock 对象*的制造组件来代替基础结构组件。

与单元测试相比, 集成测试:

* 使用应用在生产环境中使用的实际组件。
* 需要进行更多的代码和数据处理。
* 需要较长时间才能运行。

因此, 将集成测试的使用限制为最重要的基础结构方案。 如果可以使用单元测试或集成测试来测试行为, 请选择单元测试。

> [!TIP]
> 请勿为数据库和文件系统的每个可能的数据排列和文件访问编写集成测试。 无论应用程序中有多少位置与数据库和文件系统交互, 一组集中式的读取、写入、更新和删除集成测试通常都能充分测试数据库和文件系统组件。 使用单元测试对与这些组件进行交互的方法逻辑进行例程测试。 在单元测试中, 使用基础结构 fakes/模拟会导致更快地执行测试。

> [!NOTE]
> 在讨论集成测试时, 测试的项目经常称为 "测试中的*系统*" 或简称 "SUT"。

## <a name="aspnet-core-integration-tests"></a>ASP.NET Core 集成测试

ASP.NET Core 中的集成测试需要以下各项:

* 测试项目用于包含和执行测试。 测试项目具有对测试的 ASP.NET Core 项目的引用, 称为 "测试中的*系统*" (SUT)。 _本主题中使用了 "SUT" 来引用经过测试的应用程序。_
* 测试项目为 SUT 创建测试 web 主机, 并使用测试服务器客户端来处理对 SUT 的请求和响应。
* 测试运行程序用于执行测试并报告测试结果。

集成测试遵循一系列事件, 其中包括常见的*排列*、*操作*和*断言*测试步骤:

1. 已配置 SUT 的 web 主机。
1. 创建测试服务器客户端以向应用程序提交请求。
1. 执行 "*排列*测试" 步骤:测试应用将准备一个请求。
1. 执行*Act*测试步骤:客户端提交请求并接收响应。
1. 执行*Assert*测试步骤:*实际*响应会根据*预期*响应验证为*通过*或*失败*。
1. 此过程将一直继续, 直到执行了所有测试。
1. 将报告测试结果。

通常, 测试 web 主机的配置与应用程序用于测试运行的普通 web 主机的配置不同。 例如, 可以将不同的数据库或不同的应用设置用于测试。

基础结构组件 (例如测试 web 主机和内存中测试服务器 ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver))) 由[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包提供或管理。 使用此包简化了测试的创建和执行。

`Microsoft.AspNetCore.Mvc.Testing`包处理以下任务:

* 将依赖项文件 ( *\*. .deps.json*) 从 SUT 复制到测试项目的*bin*目录中。
* 将内容根设置为 SUT 的项目根, 以便在执行测试时找到静态文件和页面/视图。
* 提供[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)类, 以简化将自行启动到`TestServer`的。

[单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)文档介绍了如何设置测试项目和测试运行程序, 以及有关如何为测试和测试类命名测试和建议的详细说明。

> [!NOTE]
> 为应用程序创建测试项目时, 请将集成测试中的单元测试分成不同的项目。 这有助于确保不会意外地将基础结构测试组件包含在单元测试中。 单元测试和集成测试的隔离还允许控制运行的测试集。

Razor Pages 应用和 MVC 应用的测试的配置几乎没有任何区别。 唯一的区别在于测试的命名方式。 在 Razor Pages 应用中, 页终结点的测试通常以页面模型类命名 (例如, `IndexPageTests`为索引页测试组件集成)。 在 MVC 应用中, 通常按控制器类对测试进行组织, 并按它们所测试的控制器 (例如`HomeControllerTests` , 测试主控制器的组件集成) 进行命名。

## <a name="test-app-prerequisites"></a>测试应用必备组件

测试项目必须:

* 引用以下包:
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* 在项目文件中指定 Web SDK (`<Project Sdk="Microsoft.NET.Sdk.Web">`)。 引用[AspNetCore 元包](xref:fundamentals/metapackage-app)时, 需要 Web SDK。

可以在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中查看这些先决条件。 检查 "*测试"/"RazorPagesProject"/"RazorPagesProject* " 文件。 示例应用使用[xUnit](https://xunit.github.io/)测试框架和[AngleSharp](https://anglesharp.github.io/)分析器库, 因此示例应用还引用:

* [xunit](https://www.nuget.org/packages/xunit/)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>SUT 环境

如果未设置 SUT 的[环境](xref:fundamentals/environments), 环境将默认为 "开发"。

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>具有默认 WebApplicationFactory 的基本测试

[WebApplicationFactory&lt;TEntryPoint&gt; ](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)用于创建集成测试的[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 。 `TEntryPoint`是 SUT 的入口点类, 通常`Startup`为类。

测试类实现*类装置*接口 ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) 以指示类包含测试, 并跨类中的测试提供共享对象实例。

### <a name="basic-test-of-app-endpoints"></a>应用终结点的基本测试

下面的`BasicTests`测试类`WebApplicationFactory`使用来启动 SUT, 并为测试方法提供`Get_EndpointsReturnSuccessAndCorrectContentType` [HttpClient](/dotnet/api/system.net.http.httpclient) 。 方法检查响应状态代码是否成功 (范围200-299 中的状态代码) 和`Content-Type`标头是否`text/html; charset=utf-8`适用于多个应用程序页。

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)创建自动跟随重`HttpClient`定向并处理 cookie 的实例。

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

默认情况下, 当启用[GDPR 同意策略](xref:security/gdpr)时, 不会跨请求保留非关键 cookie。 若要保留不重要的 cookie (如 TempData 提供程序使用的 cookie), 请将它们标记为测试中的重要 cookie。 有关将 cookie 标记为必要的说明, 请参阅[基本 cookie](xref:security/gdpr#essential-cookies)。

### <a name="test-a-secure-endpoint"></a>测试安全终结点

`BasicTests`类中的另一个测试检查安全终结点是否将未经身份验证的用户重定向到应用程序的登录页。

在 SUT 中, 此`/SecurePage`页使用[AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)约定将[AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)应用到页面。 有关详细信息, 请参阅[Razor Pages 授权约定](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)。

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

在`Get_SecurePageRequiresAnAuthenticatedUser`测试中, 通过将[AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)设置为`false`, 将 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 设置为禁止重定向:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

通过禁止客户端按照重定向操作, 可以执行以下检查:

* 可以对照预期的[HttpStatusCode](/dotnet/api/system.net.httpstatuscode)结果检查 SUT 返回的状态代码, 而不是在重定向到登录页后返回最终状态代码, 这会是[HttpStatusCode](/dotnet/api/system.net.httpstatuscode)。
* 系统`Location`将检查响应标头中的标头值`http://localhost/Identity/Account/Login`, 以确认该标头的开头为, 而不是最终`Location`的登录页响应 (标头不存在)。

有关的详细信息`WebApplicationFactoryClientOptions`, 请参阅[Client options](#client-options)部分。

## <a name="customize-webapplicationfactory"></a>自定义 WebApplicationFactory

通过从`WebApplicationFactory`继承来创建一个或多个自定义工厂, 可以独立于测试类创建 Web 主机配置:

1. 从`WebApplicationFactory`继承并覆盖[ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)。 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)允许通过[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)配置服务集合:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   [示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)中的数据库种子设定由`InitializeDbForTests`方法执行。 [集成测试示例中介绍了方法:测试应用组织](#test-app-organization)部分。

2. 在测试类`CustomWebApplicationFactory`中使用自定义。 下面的示例使用`IndexPageTests`类中的工厂:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   示例应用的客户端配置为阻止`HttpClient`以下重定向。 如 "[测试安全终结点](#test-a-secure-endpoint)" 一节中所述, 这允许测试检查应用程序的第一个响应的结果。 第一个响应是包含`Location`标头的许多测试中的重定向。

3. 典型的测试使用`HttpClient`和 helper 方法来处理请求和响应:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

对 SUT 的任何 POST 请求必须满足防伪检查, 该检查是由应用的[数据保护防伪系统](xref:security/data-protection/introduction)自动完成的。 为了安排测试的 POST 请求, 测试应用必须:

1. 发出对页面的请求。
1. 分析防伪 cookie 并请求响应中的验证令牌。
1. 发出带有防伪 cookie 的 POST 请求, 并就地请求验证令牌。

`SendAsync` [示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中的帮助器扩展方法 (helper */HttpClientExtensions*) `GetDocumentAsync`和 helper 方法 (helper */HtmlHelpers*) 使用[AngleSharp](https://anglesharp.github.io/)分析器来处理防伪请检查以下方法:

* `GetDocumentAsync` &ndash; 接收 [HttpResponseMessage ](/dotnet/api/system.net.http.httpresponsemessage)并返回`IHtmlDocument`。 `GetDocumentAsync`使用一个工厂, 该工厂基于原始`HttpResponseMessage`*响应准备虚拟响应*。 有关详细信息, 请参阅[AngleSharp 文档](https://github.com/AngleSharp/AngleSharp#documentation)。
* `SendAsync`用于`HttpClient`撰写[HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)并调用[SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)的扩展方法将请求提交到 SUT。 的`SendAsync`重载接受 HTML 窗体 (`IHtmlFormElement`) 和以下内容:
  * 表单 (`IHtmlElement`) 的 "提交" 按钮
  * 窗体值集合`IEnumerable<KeyValuePair<string, string>>`()
  * 提交按钮 (`IHtmlElement`) 和窗体值`IEnumerable<KeyValuePair<string, string>>`()

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/)是用于演示目的的第三方解析库, 适用于本主题和示例应用。 ASP.NET Core 应用的集成测试不支持或不需要 AngleSharp。 可以使用其他分析器, 如[Html 灵活性包 (HAP)](https://html-agility-pack.net/)。 另一种方法是编写代码来直接处理防伪系统的请求验证令牌和防伪 cookie。

## <a name="customize-the-client-with-withwebhostbuilder"></a>用 WithWebHostBuilder 自定义客户端

如果在测试方法中需要其他配置, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)会创建一个新`WebApplicationFactory`的, 其中包含由配置进一步自定义的[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 。

[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)的`WithWebHostBuilder`测试方法演示如何使用。 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 此测试通过在 SUT 中触发窗体提交来在数据库中执行记录删除。

由于类中的`IndexPageTests`另一个测试执行的操作会删除数据库中的所有记录, 并且可能会在该`Post_DeleteMessageHandler_ReturnsRedirectToRoot`方法之前运行, 因此, 该数据库在此测试方法中具有种子, 以确保要删除的 SUT 存在记录。 选择 sut 中`messages`窗体的按钮时,会将请求中的内容模拟到sut:`deleteBtn1`

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>客户端选项

下表显示了创建`HttpClient`实例时可用的默认[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 。

| 选项 | 描述 | 默认 |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | 获取或设置`HttpClient`实例是否应自动跟随重定向响应。 | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | 获取或设置`HttpClient`实例的基址。 | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | 获取或设置实例`HttpClient`是否应处理 cookie。 | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | 获取或设置`HttpClient`实例应遵循的重定向响应的最大数目。 | 7 |

创建`WebApplicationFactoryClientOptions`类并将其传递给 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 方法 (在代码示例中显示默认值):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>注入模拟服务

通过在主机生成器上调用[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) , 可以在测试中覆盖服务。 **若要注入 mock 服务, SUT 必须有一个`Startup` `Startup.ConfigureServices`具有方法的类。**

示例 SUT 包含一个返回 quote 的作用域服务。 请求索引页时, 引号嵌入到索引页上的隐藏字段中。

*服务/IQuoteService*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*服务/QuoteService*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

Pages/Index.cshtml.cs：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index. cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

运行 SUT 应用时, 将生成以下标记:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

若要在集成测试中测试服务和引号注入, 测试会将模拟服务注入到 SUT。 模拟服务会将应用`QuoteService`替换为测试应用提供的服务, 名`TestQuoteService`为:

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices`调用并注册作用域服务:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

在测试执行过程中生成的标记反映了由`TestQuoteService`提供的引号文本, 因此断言通过:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>测试基础结构如何推断应用内容根路径

构造函数通过使用等于`TEntryPoint`程序集`System.Reflection.Assembly.FullName`的键搜索包含集成测试的程序集上的[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) , 以推断应用内容根路径。 `WebApplicationFactory` 如果找不到具有正确键的属性, `WebApplicationFactory`则回退到搜索解决方案文件 ( *\*.sln*) 并将`TEntryPoint`程序集名称追加到解决方案目录。 应用根目录 (内容根路径) 用于发现视图和内容文件。

## <a name="disable-shadow-copying"></a>禁用卷影复制

卷影复制会导致在输出目录以外的目录中执行测试。 要使测试正常工作, 必须禁用卷影复制。 该[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)使用 xUnit 并通过包含具有正确配置设置的*xUnit*文件来禁用 xUnit 的卷影复制。 有关详细信息, 请参阅[配置 xUnit 与 JSON](https://xunit.github.io/docs/configuring-with-json.html)。

将*xunit*文件添加到测试项目的根, 其中包含以下内容:

```json
{
  "shadowCopy": false
}
```

如果使用的是 Visual Studio, 请将文件的 "**复制到输出目录**" 属性设置为 "**始终复制**"。 如果不使用 Visual Studio, 请将`Content`目标添加到测试应用的项目文件中:

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a>对象的处理

执行`IClassFixture`完实现的测试后, 当 xUnit 释放[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)时, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)和[HttpClient](/dotnet/api/system.net.http.httpclient)会被释放。 如果开发人员实例化的对象需要处置, 请在`IClassFixture`实现中释放这些对象。 有关详细信息, 请参阅[实现 Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。

## <a name="integration-tests-sample"></a>集成测试示例

该[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)由两个应用组成:

| 应用 | 项目目录 | 描述 |
| --- | ----------------- | ----------- |
| 消息应用 (SUT) | *src/RazorPagesProject* | 允许用户添加、删除一个、删除和分析消息。 |
| 测试应用 | *tests/RazorPagesProject.Tests* | 用于集成测试 SUT。 |

可以使用 IDE (如[Visual Studio](https://visualstudio.microsoft.com)) 的内置测试功能来运行测试。 如果使用[Visual Studio Code](https://code.visualstudio.com/)或命令行, 请在 RazorPagesProject 目录中的命令提示符处执行以下命令:

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>消息应用 (SUT) 组织

SUT 是 Razor Pages 的消息系统, 具有以下特征:

* 应用的 "索引" 页 (*Pages/索引. cshtml*和*pages/index*) 提供了一个 UI 和页面模型方法, 用于控制消息的添加、删除和分析 (每条消息的平均单词数)。
* 消息由`Message`类 (*Data/message .cs*) 描述, 具有两个属性: `Id` (键) 和`Text` (message)。 此`Text`属性是必需的, 并且限制为200个字符。
* 使用[实体框架的内存中数据库](/ef/core/providers/in-memory/)&#8224;来存储消息。
* 应用程序在其数据库上下文类`AppDbContext` (*data/AppDbContext*) 中包含数据访问层 (DAL)。
* 如果数据库在应用启动时为空, 则会用三条消息初始化消息存储。
* 应用包括`/SecurePage`只能由经过身份验证的用户访问的。

&#8224;EF 主题[使用 InMemory 进行测试](/ef/core/miscellaneous/testing/in-memory)说明了如何将内存中数据库用于使用 MSTest 进行测试。 本主题使用[xUnit](https://xunit.github.io/)测试框架。 不同测试框架中的测试概念和测试实现相似, 但并不完全相同。

尽管应用程序不使用存储库模式, 并且不是[工作单元 (UoW) 模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效示例, 但 Razor Pages 支持这些模式的开发模式。 有关详细信息, 请参阅[设计基础结构持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和[测试控制器逻辑](/aspnet/core/mvc/controllers/testing)(示例实现存储库模式)。

### <a name="test-app-organization"></a>测试应用组织

测试应用是 "*测试"/"RazorPagesProject* " 目录中的控制台应用。

| 测试应用程序目录 | 描述 |
| ------------------ | ----------- |
| *BasicTests* | *BasicTests.cs*包含用于路由的测试方法、通过未经身份验证的用户访问安全页以及获取 GitHub 用户配置文件和检查配置文件的用户登录名。 |
| *IntegrationTests* | *IndexPageTests.cs*包含使用自定义`WebApplicationFactory`类的索引页的集成测试。 |
| *帮助程序/实用工具* | <ul><li>*Utilities.cs*包含用于`InitializeDbForTests`使数据库具有测试数据的种子的方法。</li><li>*HtmlHelpers.cs*提供了一种方法, 用于`IHtmlDocument`返回 AngleSharp 供测试方法使用。</li><li>*HttpClientExtensions.cs*提供用于`SendAsync`将请求提交到 SUT 的重载。</li></ul> |

测试框架为[xUnit](https://xunit.github.io/)。 集成测试是使用 TestHost 的[AspNetCore](/dotnet/api/microsoft.aspnetcore.testhost)进行的, 其中包括[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)。 由于[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包用于配置测试主机和测试服务器, `TestHost`并且和`TestServer`包不需要测试应用的项目文件或开发人员中的直接包引用测试应用程序中的配置。

**播种要测试的数据库**

集成测试在执行测试前通常需要数据库中的一个小型数据集。 例如, 删除测试调用数据库记录, 因此数据库必须至少有一条记录, 删除请求才能成功。

该示例应用在*Utilities.cs*中使用三个消息对数据库进行种子设定, 当测试执行时, 可以使用这些消息:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

## <a name="additional-resources"></a>其他资源

* [单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [Razor 页面单元测试](xref:test/razor-pages-tests)
* [中间件](xref:fundamentals/middleware/index)
* [测试控制器](xref:mvc/controllers/testing)
