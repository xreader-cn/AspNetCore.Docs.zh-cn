---
title: 在 ASP.NET Core 中的集成测试
author: guardrex
description: 了解集成测试如何在基础结构级别（包括数据库、文件系统和网络）确保应用组件功能正常。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/05/2019
uid: test/integration-tests
ms.openlocfilehash: 3af2a1f7c6a65d7ff42597972ee151a50fc95fb6
ms.sourcegitcommit: c716ea9155a6b404c1f3d3d34e2388454cd276d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2019
ms.locfileid: "66716378"
---
# <a name="integration-tests-in-aspnet-core"></a>在 ASP.NET Core 中的集成测试

通过[Luke Latham](https://github.com/guardrex)和[Steve Smith](https://ardalis.com/)

集成测试可确保应用程序的组件正常工作，包括应用程序的支持的基础结构，如数据库、 文件系统和网络级别。 ASP.NET Core 支持集成测试的测试 web 主机和内存中测试服务器中使用单元测试框架。

本主题假定你基本了解单元测试。 如果测试概念不太熟悉，请参阅[.NET Core 和.NET Standard 中的单元测试](/dotnet/core/testing/)主题和其链接的内容。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）

示例应用是 Razor 页面应用，并假定你基本了解 Razor 页面。 如果不熟悉使用 Razor 页面，请参阅以下主题：

* [Razor 页面介绍](xref:razor-pages/index)
* [Razor 页面入门](xref:tutorials/razor-pages/razor-pages-start)
* [Razor 页面单元测试](xref:test/razor-pages-tests)

> [!NOTE]
> 用于测试 Spa，我们建议使用一种工具如[Selenium](https://www.seleniumhq.org/)，这可以自动执行浏览器。

## <a name="introduction-to-integration-tests"></a>集成测试简介

集成测试评估上水平比更为广泛的应用程序的组件[单元测试](/dotnet/core/testing/)。 单元测试用于测试独立的软件组件，例如单独的类的方法。 集成测试确认，两个或多个应用程序组件的协作产生预期的结果，可能包括将完全处理一个请求所需的每个组件。

这些更广泛的测试用于测试应用程序的基础结构和整个 framework 中，通常包括以下组件：

* 数据库
* 文件系统
* 网络设备
* 请求-响应管道

单元测试使用制造的组件，称为*fakes*或*mock 对象*，代替基础结构组件。

与单元测试，集成测试：

* 使用该应用在生产环境中使用的实际组件。
* 需要更多代码和数据处理。
* 需要更长时间运行。

因此，限制为最重要的基础结构方案的集成测试使用。 如果使用单元测试或集成测试，可以测试行为，请选择单元测试。

> [!TIP]
> 不编写与数据库和文件系统的数据和文件访问的每个可能的排列的集成测试。 无论多少位跨应用程序与数据库和文件系统、 已设定焦点的一组读取、 写入、 更新和删除集成测试通常能够充分地测试数据库和文件系统组件进行交互。 使用单元测试的与这些组件进行交互的例程测试的方法逻辑。 在单元测试基础结构使用虚设/模拟测试执行速度更快的结果。

> [!NOTE]
> 在讨论的集成测试时，通常称为测试的项目*待测试系统*，或简称"SUT"。

## <a name="aspnet-core-integration-tests"></a>ASP.NET Core 集成测试

在 ASP.NET Core 中的集成测试需要以下项：

* 测试项目用于包含和执行测试。 测试项目具有对名为经过测试的 ASP.NET Core 项目的引用*待测试系统*(SUT)。 _在本主题中使用"SUT"来指代经过测试的应用。_
* 测试项目创建 SUT 的测试 web 主机，并使用测试服务器客户端处理请求和对 SUT 的响应。
* 测试运行程序用于执行测试和报告测试结果。

集成测试遵循一系列事件，其中包括常用*排列*， *Act*，并*Assert*测试步骤：

1. SUT 的 web 主机配置。
1. 创建测试服务器客户端以将请求提交到应用程序。
1. *排列*执行测试步骤：测试应用程序准备请求。
1. *Act*执行测试步骤：客户端提交该请求并接收响应。
1. *Assert*执行测试步骤：*实际*响应中进行验证*传递*或*失败*基于*预期*响应。
1. 过程持续进行的所有测试执行。
1. 报告测试结果。

通常情况下，测试 web 主机配置以不同的方式不是测试应用程序的普通的 web 主机在运行。 例如，测试可能使用不同的数据库或不同的应用设置。

基础结构组件，如测试 web 主机和内存中测试服务器 ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver))、 提供或由托管[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包。 使用此包的简化了测试创建和执行。

`Microsoft.AspNetCore.Mvc.Testing`程序包处理以下任务：

* 将复制依赖项文件 ( *\*.deps*) 到测试项目的 sut *bin*目录。
* 内容根设置为 SUT 的项目根，使静态文件和网页/视图时执行的测试发现。
* 提供了[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)类，以简化启动与 SUT `TestServer`。

[单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)文档介绍了如何设置测试项目和测试运行程序以及如何运行测试和的建议的名称测试和测试类的详细说明。

> [!NOTE]
> 在创建应用程序的测试项目时，分离到不同的项目集成测试中的单元测试。 这样可以帮助确保测试基础结构组件不会意外地包括在单元测试。 单元测试和集成测试的分离还允许控制对哪些组的测试运行。

几乎的 Razor 页面应用程序测试的配置和 MVC 应用程序之间没有差异。 唯一区别是在测试的命名方式。 在 Razor 页面应用中，测试页终结点的名称通常以在页面模型类后 (例如，`IndexPageTests`测试索引页的组件集成)。 在 MVC 应用中，测试是通常按控制器类和命名这些测试的控制器 (例如，`HomeControllerTests`测试 Home 控制器的组件集成)。

## <a name="test-app-prerequisites"></a>测试应用程序必备组件

测试项目必须：

* 引用以下包：
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* 在项目文件中指定 Web SDK (`<Project Sdk="Microsoft.NET.Sdk.Web">`)。 Web SDK 时是必需的引用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。

这些系统必备组件中所示[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)。 检查*tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj*文件。 示例应用使用[xUnit](https://xunit.github.io/)测试框架和[AngleSharp](https://anglesharp.github.io/)分析器库，因此示例应用还引用：

* [xunit](https://www.nuget.org/packages/xunit/)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>SUT 环境

如果 SUT[环境](xref:fundamentals/environments)未设置开发环境的默认值。

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>默认值 WebApplicationFactory 基本测试

[WebApplicationFactory&lt;TEntryPoint&gt; ](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)用来创建[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)集成测试。 `TEntryPoint` 通常是 SUT 的入口点类`Startup`类。

测试类将实现*类装置*接口 ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) 表明类包含测试以及在测试类中提供共享的对象实例。

### <a name="basic-test-of-app-endpoints"></a>基本应用程序终结点的测试

以下测试类， `BasicTests`，使用`WebApplicationFactory`若要启动 SUT 和提供[HttpClient](/dotnet/api/system.net.http.httpclient)到测试方法、 `Get_EndpointsReturnSuccessAndCorrectContentType`。 该方法检查是否成功的响应状态代码 （状态代码在 200-299 范围） 和`Content-Type`标头是`text/html; charset=utf-8`若干应用程序页。

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)创建的实例`HttpClient`的自动遵循重定向，并处理 cookie。

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

默认情况下，将不会保留非必需 cookie 的请求时[GDPR 许可策略](xref:security/gdpr)已启用。 若要保留非必需 cookie，如使用 TempData 提供程序，将其标记为在你的测试中必不可少。 有关将标记为重要的 cookie 的说明，请参阅[Essential cookie](xref:security/gdpr#essential-cookies)。

### <a name="test-a-secure-endpoint"></a>测试安全终结点

中的另一个测试`BasicTests`类将检查安全终结点到应用程序的登录页将未经身份验证的用户重定向。

在 SUT 中`/SecurePage`页上使用[AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)约定应用[AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)到页。 有关详细信息，请参阅[Razor 页面授权约定](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)。

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

在中`Get_SecurePageRequiresAnAuthenticatedUser`测试，请[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)设置为不重定向允许通过设置[AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)到`false`:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

通过禁止客户端遵循重定向，可以进行以下检查：

* 可以检查返回的 SUT 的状态代码针对预期[HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode)结果中，不是最终状态代码后将重定向到登录页面，将是[HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).
* `Location`响应标头中的标头值进行检查以确认它开头`http://localhost/Identity/Account/Login`，不将最终的登录页响应，其中`Location`标头不会为存在。

有关详细信息`WebApplicationFactoryClientOptions`，请参阅[客户端选项](#client-options)部分。

## <a name="customize-webapplicationfactory"></a>自定义 WebApplicationFactory

Web 主机配置可以通过继承创建独立的测试类于`WebApplicationFactory`来创建一个或多个自定义工厂：

1. 继承自`WebApplicationFactory`并重写[ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)。 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)允许使用的服务集合的配置[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   数据库在种子设定[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)由执行`InitializeDbForTests`方法。 中介绍了方法[集成测试示例：测试应用程序的组织](#test-app-organization)部分。

2. 使用自定义`CustomWebApplicationFactory`测试类中。 下面的示例使用中的工厂`IndexPageTests`类：

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   示例应用程序的客户端配置为阻止`HttpClient`从以下重定向。 中所述[测试安全终结点](#test-a-secure-endpoint)部分中，这允许测试以检查应用程序的第一个响应的结果。 第一个响应是许多与这些测试中的重定向`Location`标头。

3. 典型测试中使用`HttpClient`和帮助器方法来处理请求和响应：

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

任何对 SUT 的 POST 请求必须满足应用的自动所做的防伪复选[的数据保护防伪系统](xref:security/data-protection/introduction)。 若要安排某个测试的 POST 请求，测试应用程序必须：

1. 发出页请求。
1. 分析防伪 cookie 和请求验证令牌响应中。
1. 在位置进行 POST 请求，其中防伪 cookie 和请求验证令牌。

`SendAsync` Helper 扩展方法 (*Helpers/HttpClientExtensions.cs*) 和`GetDocumentAsync`帮助器方法 (*Helpers/HtmlHelpers.cs*) 中[的示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)使用[AngleSharp](https://anglesharp.github.io/)分析器以使用以下方法处理防伪检查：

* `GetDocumentAsync` &ndash; 接收[HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) ，并返回`IHtmlDocument`。 `GetDocumentAsync` 使用工厂，用于准备*虚拟响应*根据原始`HttpResponseMessage`。 有关详细信息，请参阅[AngleSharp 文档](https://github.com/AngleSharp/AngleSharp#documentation)。
* `SendAsync` 扩展方法`HttpClient`compose [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) ，并调用[SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)以将请求提交到 SUT。 有关重载`SendAsync`接受 HTML 窗体 (`IHtmlFormElement`) 和以下：
  * 提交窗体的按钮 (`IHtmlElement`)
  * 窗体值集合 (`IEnumerable<KeyValuePair<string, string>>`)
  * 提交按钮 (`IHtmlElement`) 和窗体值 (`IEnumerable<KeyValuePair<string, string>>`)

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/)第三方分析用于演示目的，在本主题和示例应用程序中的库。 AngleSharp 不受支持或所需的集成测试的 ASP.NET Core 应用。 其他分析器可以使用，如[Html 灵活性包 (HAP)](http://html-agility-pack.net/)。 另一种方法是编写代码来直接处理防伪系统的请求验证令牌和防伪 cookie。

## <a name="customize-the-client-with-withwebhostbuilder"></a>自定义客户端与 WithWebHostBuilder

在测试方法中，需要额外的配置时[WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)创建一个新`WebApplicationFactory`与[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)的配置通过进一步自定义。

`Post_DeleteMessageHandler_ReturnsRedirectToRoot`测试的方法[示例应用程序](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)演示如何使用`WithWebHostBuilder`。 此测试可执行记录删除数据库中通过触发在 SUT 中的窗体提交。

因为另一个测试中`IndexPageTests`类执行的操作将删除所有数据库中的记录，并且可能运行之前`Post_DeleteMessageHandler_ReturnsRedirectToRoot`方法中，数据库将植入以确保记录存在的 SUT，若要删除此测试方法中。 选择`deleteBtn1`按钮的`messages`窗体在 SUT 中的模拟对 SUT 的请求中：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>客户端选项

下表显示的默认[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)时创建可用`HttpClient`实例。

| 选项 | 描述 | 默认 |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | 获取或设置是否`HttpClient`实例应自动跟随重定向响应。 | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | 获取或设置的基址`HttpClient`实例。 | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | 获取或设置是否`HttpClient`实例应处理 cookie。 | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | 获取或设置重定向响应的最大数目`HttpClient`实例应遵循。 | 7 |

创建`WebApplicationFactoryClientOptions`类，并将其传递给[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)方法 （默认值代码示例所示）：

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>插入模拟服务

服务可以通过调用测试中重写[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)主机生成器上。 **若要插入的模拟服务，SUT 必须具有`Startup`类的`Startup.ConfigureServices`方法。**

示例 SUT 包含作用域内返回的服务的引号。 请求索引页时，在索引页的隐藏字段中嵌入引号。

*Services/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

 Pages/Index.cshtml.cs：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index.cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

当 SUT 应用运行时，会生成以下标记：

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

若要在集成测试中测试服务和引号注入，模拟服务注入到 SUT 的测试。 模拟服务替代了应用程序的`QuoteService`测试应用程序提供的服务，名为`TestQuoteService`:

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices` 调用时，并指定了作用域的服务将注册：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

在执行测试的执行期间生成的标记反映了由提供的报价文本`TestQuoteService`，因此在断言阶段：

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>测试基础结构如何推断应用内容根路径

`WebApplicationFactory`构造函数通过搜索来推断应用内容根路径[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)包含其键等于集成测试的程序集上`TEntryPoint`的程序集`System.Reflection.Assembly.FullName`. 如果找不到具有正确的密钥的属性，`WebApplicationFactory`回退到搜索解决方案文件 ( *\*.sln*)，并追加`TEntryPoint`到解决方案目录的程序集名称。 应用程序根目录下 （内容根路径） 用于发现视图和内容文件。

在大多数情况下，无需显式设置应用程序内容根，在搜索逻辑通常在运行时找到正确的内容根。 内容根位置找不到的特殊方案中使用内置的搜索算法，内容可以指定根目录，显式或使用自定义逻辑应用。 若要将应用内容根目录设置在这些情况下，调用`UseSolutionRelativeContentRoot`扩展方法来源[Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost)包。 提供解决方案的相对路径和可选的解决方案文件的名称或 glob 模式 (默认值 = `*.sln`)。

调用[UseSolutionRelativeContentRoot](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.usesolutionrelativecontentroot)扩展方法使用*一个*以下方法之一：

* 配置与测试类时`WebApplicationFactory`，提供的自定义配置[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder):

   ```csharp
   public IndexPageTests(
       WebApplicationFactory<RazorPagesProject.Startup> factory)
   {
       var _factory = factory.WithWebHostBuilder(builder =>
       {
           builder.UseSolutionRelativeContentRoot("<SOLUTION-RELATIVE-PATH>");

           ...
       });
   }
   ```

* 使用自定义配置测试类时`WebApplicationFactory`，继承自`WebApplicationFactory`并重写[ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost):

   ```csharp
   public class CustomWebApplicationFactory<TStartup>
       : WebApplicationFactory<RazorPagesProject.Startup>
   {
       protected override void ConfigureWebHost(IWebHostBuilder builder)
       {
           builder.ConfigureServices(services =>
           {
               builder.UseSolutionRelativeContentRoot("<SOLUTION-RELATIVE-PATH>");

               ...
           });
       }
   }
   ```

## <a name="disable-shadow-copying"></a>禁用卷影复制

卷影复制会导致要在不同的目录比输出目录中执行的测试。 对于测试才能正常工作，卷影复制必须禁用。 [示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)使用 xUnit 和禁用卷影复制对于 xUnit 通过包括*xunit.runner.json*文件，并正确的配置设置。 有关详细信息，请参阅[使用 JSON 配置 xUnit](https://xunit.github.io/docs/configuring-with-json.html)。

添加*xunit.runner.json*到测试项目包含以下内容的根目录的文件：

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>可供使用的对象

之后的测试`IClassFixture`实现会执行， [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)并[HttpClient](/dotnet/api/system.net.http.httpclient) xUnit 释放时，将释放[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). 如果对象实例化由开发人员需要可供使用，释放它们在`IClassFixture`实现。 有关详细信息，请参阅[实现 Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。

## <a name="integration-tests-sample"></a>集成测试示例

[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)由两个应用的组成：

| 应用 | 项目目录 | 描述 |
| --- | ----------------- | ----------- |
| 消息应用程序 (SUT) | *src/RazorPagesProject* | 允许用户添加、 删除其中一个，删除所有，并分析消息。 |
| 测试应用 | *tests/RazorPagesProject.Tests* | 用于集成测试 SUT。 |

可以使用内置测试功能的 IDE，如运行测试[Visual Studio](https://visualstudio.microsoft.com)。 如果使用[Visual Studio Code](https://code.visualstudio.com/)或命令行中，执行以下命令在命令提示符处*tests/RazorPagesProject.Tests*目录：

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>消息应用 (SUT) 组织

SUT 是 Razor 页面消息系统具有以下特征：

* 应用程序的索引页 (*pages/Index.cshtml*并*Pages/Index.cshtml.cs*) 提供 UI 和页面模型方法，用于控制添加、 删除和分析的消息 （每个消息的平均词）.
* 一条消息由描述`Message`类 (*Data/Message.cs*) 具有两个属性： `Id` （密钥） 和`Text`（消息）。 `Text`属性是必需的限制为 200 个字符。
* 使用存储的消息[Entity Framework 的内存中数据库](/ef/core/providers/in-memory/)&#8224;。
* 应用程序包含在其数据库上下文类中，数据访问层 (DAL) `AppDbContext` (*Data/AppDbContext.cs*)。
* 如果数据库为空应用程序启动时，使用三个消息初始化消息存储区。
* 该应用包含`/SecurePage`仅可通过身份验证的用户访问。

&#8224;EF 主题[测试与 InMemory](/ef/core/miscellaneous/testing/in-memory)，说明如何使用内存中数据库的使用 MSTest 的测试。 本主题使用[xUnit](https://xunit.github.io/)测试框架。 测试概念和跨不同测试框架的测试实现有类似，但不是完全相同。

虽然应用不使用存储库模式，并且不是有效的示例[工作单元 (UoW) 模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)，Razor 页面支持开发这些模式。 有关详细信息，请参阅[设计基础结构持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)并[测试控制器逻辑](/aspnet/core/mvc/controllers/testing)（此示例实现存储库模式）。

### <a name="test-app-organization"></a>测试应用程序的组织

测试应用程序是一个控制台应用程序内的*tests/RazorPagesProject.Tests*目录。

| 测试应用程序目录 | 描述 |
| ------------------ | ----------- |
| *BasicTests* | *BasicTests.cs*包含测试方法的路由、 访问的安全页由未经身份验证的用户，并获取 GitHub 用户配置文件和检查配置文件的用户登录名。 |
| *IntegrationTests* | *IndexPageTests.cs*包含使用自定义的索引页的集成测试`WebApplicationFactory`类。 |
| *帮助程序/实用程序* | <ul><li>*Utilities.cs*包含`InitializeDbForTests`方法用于设置测试数据与数据库的种子。</li><li>*HtmlHelpers.cs*提供了一个方法来返回 AngleSharp`IHtmlDocument`以供测试方法。</li><li>*HttpClientExtensions.cs*提供的重载`SendAsync`以将请求提交到 SUT。</li></ul> |

测试框架这[xUnit](https://xunit.github.io/)。 使用进行集成测试[Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost)，其中包括[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)。 因为[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包用于配置测试主机和测试服务器`TestHost`和`TestServer`的包不需要测试应用的项目文件中的直接包引用或在测试应用程序的开发人员配置。

**用于测试数据库进行种子设定**

集成测试通常需要在测试执行之前在数据库中的一个小型数据集。 例如，删除测试的数据库记录删除的调用，因此数据库必须具有至少一个记录删除请求才能成功。

示例应用程序中的三个消息数据库种子*Utilities.cs*测试可以使用它们执行时：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

## <a name="additional-resources"></a>其他资源

* [单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [Razor 页面单元测试](xref:test/razor-pages-tests)
* [中间件](xref:fundamentals/middleware/index)
* [测试控制器](xref:mvc/controllers/testing)
