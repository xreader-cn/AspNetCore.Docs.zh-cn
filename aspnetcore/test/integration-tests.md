---
title: ASP.NET Core 中的集成测试
author: guardrex
description: 了解集成测试如何在基础结构级别（包括数据库、文件系统和网络）确保应用组件功能正常。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/06/2019
uid: test/integration-tests
ms.openlocfilehash: ccee8957a72da0eb5d870b1bd184ee1ea146a0e6
ms.sourcegitcommit: 79850db9e79b1705b89f466c6f2c961ff15485de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2020
ms.locfileid: "75693786"
---
# <a name="integration-tests-in-aspnet-core"></a>ASP.NET Core 中的集成测试

作者： [Luke Latham](https://github.com/guardrex)、 [Javier Calvarro 使用](https://github.com/javiercn)、 [Steve Smith](https://ardalis.com/)和[Jos van der 等到](https://jvandertil.nl)

::: moniker range=">= aspnetcore-3.0"

集成测试可确保应用程序的组件在包含应用程序支持的基础结构的级别（例如数据库、文件系统和网络）正常运行。 ASP.NET Core 支持结合使用单元测试框架和测试 web 主机和内存中测试服务器的集成测试。

本主题假定基本了解单元测试。 如果不熟悉测试概念，请参阅[.Net Core 中的单元测试和 .NET Standard](/dotnet/core/testing/)主题及其链接的内容。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）

该示例应用是 Razor Pages 应用程序，并假定基本了解 Razor Pages。 如果不熟悉 Razor Pages，请参阅以下主题：

* [Razor 页面介绍](xref:razor-pages/index)
* [Razor 页面入门](xref:tutorials/razor-pages/razor-pages-start)
* [Razor 页面单元测试](xref:test/razor-pages-tests)

> [!NOTE]
> 对于测试 Spa，我们建议使用[Selenium](https://www.seleniumhq.org/)这样的工具，它可以自动执行浏览器。

## <a name="introduction-to-integration-tests"></a>集成测试简介

与[单元测试](/dotnet/core/testing/)相比，集成测试在更广泛的级别上评估应用的组件。 单元测试用于测试独立的软件组件，如单独的类方法。 集成测试确认两个或更多应用程序组件一起工作以生成预期的结果，其中可能包括完全处理请求所需的每个组件。

这些广泛的测试用于测试应用程序的基础结构和整个框架，通常包括以下组件：

* 数据库
* 文件系统
* 网络设备
* 请求-响应管道

单元测试使用称为*fakes*或*mock 对象*的制造组件来代替基础结构组件。

与单元测试相比，集成测试：

* 使用应用在生产环境中使用的实际组件。
* 需要进行更多的代码和数据处理。
* 需要较长时间才能运行。

因此，将集成测试的使用限制为最重要的基础结构方案。 如果可以使用单元测试或集成测试来测试行为，请选择单元测试。

> [!TIP]
> 请勿为数据库和文件系统的每个可能的数据排列和文件访问编写集成测试。 无论应用程序中有多少位置与数据库和文件系统交互，一组集中式的读取、写入、更新和删除集成测试通常都能充分测试数据库和文件系统组件。 使用单元测试对与这些组件进行交互的方法逻辑进行例程测试。 在单元测试中，使用基础结构 fakes/模拟会导致更快地执行测试。

> [!NOTE]
> 在讨论集成测试时，测试的项目经常称为 "测试中的*系统*" 或简称 "SUT"。
>
> *本主题中使用 "SUT" 来引用已测试的 ASP.NET Core 应用。*

## <a name="aspnet-core-integration-tests"></a>ASP.NET Core 集成测试

ASP.NET Core 中的集成测试需要以下各项：

* 测试项目用于包含和执行测试。 测试项目具有对 SUT 的引用。
* 测试项目将为 SUT 创建测试 web 主机，并使用测试服务器客户端来处理 SUT 的请求和响应。
* 测试运行程序用于执行测试并报告测试结果。

集成测试按一个顺序特定的事件序列发生, 其中包括常见的 *Arrange*、*Act* 和 *Assert* 测试步骤:

1. 已配置 SUT 的 web 主机。
1. 创建测试服务器客户端以向应用程序提交请求。
1. 执行 "*排列*测试" 步骤：测试应用准备请求。
1. 执行*Act*测试步骤：客户端提交请求并接收响应。
1. 将*执行断言*测试步骤：根据*预期*的响应验证*实际*响应是*通过*还是*失败*。
1. 此过程将一直继续，直到执行了所有测试。
1. 将报告测试结果。

通常，测试 web 主机的配置与应用程序用于测试运行的普通 web 主机的配置不同。 例如，可以将不同的数据库或不同的应用设置用于测试。

基础结构组件（例如测试 web 主机和内存中测试服务器（[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)））由[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包提供或管理。 使用此包简化了测试的创建和执行。

`Microsoft.AspNetCore.Mvc.Testing` 包处理以下任务：

* 将依赖项文件（ *. .deps.json*）从 SUT 复制到测试项目的*bin*目录中。
* 将[内容根](xref:fundamentals/index#content-root)设置为 SUT 的项目根，以便在执行测试时找到静态文件和页面/视图。
* 提供[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)类，以简化与 `TestServer`的 SUT 的引导。

[单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)文档介绍了如何设置测试项目和测试运行程序，以及有关如何为测试和测试类命名测试和建议的详细说明。

> [!NOTE]
> 为应用程序创建测试项目时，请将集成测试中的单元测试分成不同的项目。 这有助于确保不会意外地将基础结构测试组件包含在单元测试中。 单元测试和集成测试的隔离还允许控制运行的测试集。

Razor Pages 应用和 MVC 应用的测试的配置几乎没有任何区别。 唯一的区别在于测试的命名方式。 在 Razor Pages 应用程序中，页终结点的测试通常以页面模型类命名（例如，`IndexPageTests` 为索引页测试组件集成）。 在 MVC 应用中，通常按控制器类对测试进行组织，并按它们所测试的控制器（例如 `HomeControllerTests` 来测试主控制器的组件集成）进行命名。

## <a name="test-app-prerequisites"></a>测试应用必备组件

测试项目必须：

* 请参阅[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包。
* 在项目文件中指定 Web SDK （`<Project Sdk="Microsoft.NET.Sdk.Web">`）。

可以在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中查看这些先决条件。 检查 "*测试"/"RazorPagesProject"/"RazorPagesProject* " 文件。 示例应用使用[xUnit](https://xunit.github.io/)测试框架和[AngleSharp](https://anglesharp.github.io/)分析器库，因此示例应用还引用：

* [xunit](https://www.nuget.org/packages/xunit)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp)

还可在测试中使用 Entity Framework Core。 应用引用：

* [AspNetCore. Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [AspNetCore. Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a>SUT 环境

如果未设置 SUT 的[环境](xref:fundamentals/environments)，环境将默认为 "开发"。

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>具有默认 WebApplicationFactory 的基本测试

[WebApplicationFactory\<TEntryPoint >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)用于创建集成测试的[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 。 `TEntryPoint` 是 SUT （通常是 `Startup` 类）的入口点类。

测试类实现*类装置*接口（[IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)）以指示类包含测试，并跨类中的测试提供共享对象实例。

下面的测试类 `BasicTests`使用 `WebApplicationFactory` 来启动 SUT，并为测试方法 `Get_EndpointsReturnSuccessAndCorrectContentType`提供[HttpClient](/dotnet/api/system.net.http.httpclient) 。 方法将检查响应状态代码是否成功（范围200-299 中的状态代码）和多个应用页面的 `Content-Type` 标头是否 `text/html; charset=utf-8`。

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)创建自动跟随重定向并处理 cookie 的 `HttpClient` 的实例。

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

默认情况下，当启用[GDPR 同意策略](xref:security/gdpr)时，不会跨请求保留非关键 cookie。 若要保留不重要的 cookie （如 TempData 提供程序使用的 cookie），请将它们标记为测试中的重要 cookie。 有关将 cookie 标记为必要的说明，请参阅[基本 cookie](xref:security/gdpr#essential-cookies)。

## <a name="customize-webapplicationfactory"></a>自定义 WebApplicationFactory

通过继承 `WebApplicationFactory` 来创建一个或多个自定义工厂，可以独立于测试类创建 Web 主机配置：

1. 从 `WebApplicationFactory` 继承并重写[ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)。 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)允许通过[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)配置服务集合：

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   [示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)中的数据库种子设定由 `InitializeDbForTests` 方法执行。 [集成测试示例：测试应用组织](#test-app-organization)部分介绍了方法。

   在其 `Startup.ConfigureServices` 方法中注册了 SUT 的数据库上下文。 执行应用的 `Startup.ConfigureServices` 代码*后*，将执行测试应用的 `builder.ConfigureServices` 回调。 对于版本为 ASP.NET Core 3.0 的[泛型主机](xref:fundamentals/host/generic-host)，执行顺序是一项重大更改。 若要将不同于应用程序的数据库用于测试，则必须将应用程序的数据库上下文替换 `builder.ConfigureServices`中。

   示例应用将查找数据库上下文的服务描述符，并使用描述符来删除服务注册。 接下来，工厂添加一个新的 `ApplicationDbContext`，使用内存中数据库进行测试。

   若要连接到与内存中数据库不同的数据库，请更改 `UseInMemoryDatabase` 调用以将上下文连接到其他数据库。 使用 SQL Server 测试数据库：

   * 引用项目文件中的[Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet 包。
   * 使用与数据库的连接字符串调用 `UseSqlServer`。

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. 使用测试类中的自定义 `CustomWebApplicationFactory`。 下面的示例使用 `IndexPageTests` 类中的工厂：

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   示例应用的客户端配置为阻止 `HttpClient` 以下重定向。 如稍后在 "[模拟身份验证](#mock-authentication)" 部分中所述，这允许测试检查应用程序的第一个响应的结果。 第一个响应是包含 `Location` 标头的多个测试的重定向。

3. 典型的测试使用 `HttpClient` 和 helper 方法来处理请求和响应：

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

对 SUT 的任何 POST 请求必须满足防伪检查，该检查是由应用的[数据保护防伪系统](xref:security/data-protection/introduction)自动完成的。 为了安排测试的 POST 请求，测试应用必须：

1. 发出对页面的请求。
1. 分析防伪 cookie 并请求响应中的验证令牌。
1. 发出带有防伪 cookie 的 POST 请求，并就地请求验证令牌。

[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中的 `SendAsync` helper 扩展方法（Helper */HttpClientExtensions*）和 `GetDocumentAsync` helper 方法（helper */HtmlHelpers*）使用[AngleSharp](https://anglesharp.github.io/)分析器来处理使用以下方法的防伪检查：

* `GetDocumentAsync` &ndash; 接收 [HttpResponseMessage ](/dotnet/api/system.net.http.httpresponsemessage)并返回`IHtmlDocument`。 `GetDocumentAsync` 使用一个工厂，该工厂根据原始 `HttpResponseMessage`准备*虚拟响应*。 有关详细信息，请参阅[AngleSharp 文档](https://github.com/AngleSharp/AngleSharp#documentation)。
* `SendAsync` 扩展方法 `HttpClient` 构成[HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)并调用[SendAsync （HttpRequestMessage）](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)将请求提交到 SUT。 `SendAsync` 的重载接受 HTML 窗体（`IHtmlFormElement`）以及以下内容：
  * 窗体的 "提交" 按钮（`IHtmlElement`）
  * 窗体值集合（`IEnumerable<KeyValuePair<string, string>>`）
  * 提交按钮（`IHtmlElement`）和窗体值（`IEnumerable<KeyValuePair<string, string>>`）

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/)是用于演示目的的第三方解析库，适用于本主题和示例应用。 ASP.NET Core 应用的集成测试不支持或不需要 AngleSharp。 可以使用其他分析器，如[Html 灵活性包（HAP）](https://html-agility-pack.net/)。 另一种方法是编写代码来直接处理防伪系统的请求验证令牌和防伪 cookie。

## <a name="customize-the-client-with-withwebhostbuilder"></a>用 WithWebHostBuilder 自定义客户端

如果在测试方法中需要其他配置， [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)会创建一个新的 `WebApplicationFactory`，其中包含由配置进一步自定义的[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 。

[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)的 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 测试方法演示如何使用 `WithWebHostBuilder`。 此测试通过在 SUT 中触发窗体提交来在数据库中执行记录删除。

由于 `IndexPageTests` 类中的另一个测试会执行一个操作，该操作将删除数据库中的所有记录，并且该操作可能在 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 方法之前运行，因此，该数据库在此测试方法中种子，以确保要删除的 SUT 存在记录。 选择 SUT 中 `messages` 窗体的第一个 "删除" 按钮时，会将请求中的内容模拟到 SUT：

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>客户端选项

下表显示了在创建 `HttpClient` 实例时可用的默认[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 。

| 选项 | 描述 | 默认值 |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | 获取或设置 `HttpClient` 实例是否应自动跟随重定向响应。 | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | 获取或设置 `HttpClient` 实例的基址。 | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | 获取或设置 `HttpClient` 实例是否应处理 cookie。 | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | 获取或设置 `HttpClient` 实例应遵循的重定向响应的最大数目。 | 7 |

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

通过在主机生成器上调用[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) ，可以在测试中覆盖服务。 **若要注入模拟服务，SUT 必须有一个具有 `Startup.ConfigureServices` 方法的 `Startup` 类。**

示例 SUT 包含一个返回 quote 的作用域服务。 请求索引页时，引号嵌入到索引页上的隐藏字段中。

*服务/IQuoteService*：

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*服务/QuoteService*：

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*：

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

Pages/Index.cshtml.cs：

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index. cs*：

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

运行 SUT 应用时，将生成以下标记：

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

若要在集成测试中测试服务和引号注入，测试会将模拟服务注入到 SUT。 模拟服务会将应用的 `QuoteService` 替换为测试应用提供的服务，称为 `TestQuoteService`：

*IntegrationTests.IndexPageTests.cs*：

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

调用 `ConfigureTestServices`，并注册作用域内服务：

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

在测试执行过程中生成的标记反映 `TestQuoteService`提供的引号文本，因此断言通过：

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>模拟身份验证

`AuthTests` 类中的测试检查安全终结点：

* 将未经身份验证的用户重定向到应用程序的登录页。
* 返回已经过身份验证的用户的内容。

在 SUT 中，`/SecurePage` 页使用[AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)约定将[AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)应用到页面。 有关详细信息，请参阅[Razor Pages 授权约定](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)。

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

在`Get_SecurePageRedirectsAnUnauthenticatedUser`测试中, 通过将[AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)设置为`false`, 将 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 设置为禁止重定向:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

通过禁止客户端按照重定向操作，可以执行以下检查：

* 可以对照预期的[HttpStatusCode](/dotnet/api/system.net.httpstatuscode)结果检查 SUT 返回的状态代码，而不是在重定向到登录页后返回最终状态代码，这会是[HttpStatusCode](/dotnet/api/system.net.httpstatuscode)。
* 将检查响应标头中的 `Location` 标头值，以确认该标头值以 `http://localhost/Identity/Account/Login`（而不是最终的登录页响应）开头，而 `Location` 标头不存在。

测试应用可以模拟[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)中的 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>，以便测试身份验证和授权的各个方面。 最小方案返回[AuthenticateResult](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)：

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

当身份验证方案设置为 `Test` 在其中向 `ConfigureTestServices`注册了 `AddAuthentication` 时，将调用 `TestAuthHandler` 来对用户进行身份验证：

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

有关 `WebApplicationFactoryClientOptions`的详细信息，请参阅[Client options](#client-options)部分。

## <a name="set-the-environment"></a>设置环境

默认情况下，SUT 的主机和应用环境配置为使用开发环境。 重写 SUT 的环境：

* 设置 `ASPNETCORE_ENVIRONMENT` 环境变量（例如，`Staging`、`Production`或其他自定义值，例如 `Testing`）。
* 重写测试应用中 `CreateHostBuilder`，以读取以 `ASPNETCORE`为前缀的环境变量。

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>测试基础结构如何推断应用内容根路径

`WebApplicationFactory` 构造函数通过使用等于 `TEntryPoint` 程序集 `System.Reflection.Assembly.FullName`的键搜索包含集成测试的程序集上的[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)来推断应用[内容根](xref:fundamentals/index#content-root)路径。 如果找不到具有正确键的属性，`WebApplicationFactory` 将回退到搜索解决方案文件（ *.sln*），并在解决方案目录中追加 `TEntryPoint` 程序集名称。 应用根目录（内容根路径）用于发现视图和内容文件。

## <a name="disable-shadow-copying"></a>禁用卷影复制

卷影复制会导致在输出目录以外的目录中执行测试。 要使测试正常工作，必须禁用卷影复制。 该[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)使用 xUnit 并通过包含具有正确配置设置的*xUnit*文件来禁用 xUnit 的卷影复制。 有关详细信息，请参阅[配置 xUnit 与 JSON](https://xunit.github.io/docs/configuring-with-json.html)。

将*xunit*文件添加到测试项目的根，其中包含以下内容：

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>对象的处理

执行 `IClassFixture` 实现的测试后，当 xUnit 释放[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)时， [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)和[HttpClient](/dotnet/api/system.net.http.httpclient)会被释放。 如果开发人员实例化的对象需要处置，请在 `IClassFixture` 实现中释放这些对象。 有关详细信息，请参阅[实现 Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。

## <a name="integration-tests-sample"></a>集成测试示例

该[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)由两个应用组成：

| 应用程序 | 项目目录 | 描述 |
| --- | ----------------- | ----------- |
| 消息应用（SUT） | *src/RazorPagesProject* | 允许用户添加、删除一个、删除和分析消息。 |
| 测试应用 | *tests/RazorPagesProject.Tests* | 用于集成测试 SUT。 |

可以使用 IDE （如[Visual Studio](https://visualstudio.microsoft.com)）的内置测试功能来运行测试。 如果使用[Visual Studio Code](https://code.visualstudio.com/)或命令行，请在*RazorPagesProject 目录中的命令*提示符处执行以下命令：

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>消息应用（SUT）组织

SUT 是 Razor Pages 的消息系统，具有以下特征：

* 应用的 "索引" 页（*Pages/索引. cshtml*和*pages/index*）提供了一个 UI 和页面模型方法，用于控制消息的添加、删除和分析（每条消息的平均单词数）。
* 消息由 `Message` 类（*Data/message*）描述，具有两个属性： `Id` （密钥）和 `Text` （message）。 `Text` 属性是必需的，并且限制为200个字符。
* 使用[实体框架的内存中数据库](/ef/core/providers/in-memory/)&#8224;来存储消息。
* 应用在其数据库上下文类中包含数据访问层（DAL），`AppDbContext` （*data/AppDbContext*）。
* 如果数据库在应用启动时为空，则会用三条消息初始化消息存储。
* 应用包括只能由经过身份验证的用户访问的 `/SecurePage`。

&#8224;EF 主题[使用 InMemory 进行测试](/ef/core/miscellaneous/testing/in-memory)说明了如何将内存中数据库用于使用 MSTest 进行测试。 本主题使用[xUnit](https://xunit.github.io/)测试框架。 不同测试框架中的测试概念和测试实现相似，但并不完全相同。

尽管应用程序不使用存储库模式，并且不是[工作单元（UoW）模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效示例，但 Razor Pages 支持这些模式的开发模式。 有关详细信息，请参阅[设计基础结构持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和[测试控制器逻辑](/aspnet/core/mvc/controllers/testing)（示例实现存储库模式）。

### <a name="test-app-organization"></a>测试应用组织

测试应用是 "*测试"/"RazorPagesProject* " 目录中的控制台应用。

| 测试应用程序目录 | 描述 |
| ------------------ | ----------- |
| *AuthTests* | 包含的测试方法：<ul><li>通过未经身份验证的用户访问安全页。</li><li>使用模拟 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>通过经过身份验证的用户访问安全页。</li><li>获取 GitHub 用户配置文件，并检查配置文件的用户登录名。</li></ul> |
| *BasicTests* | 包含路由和内容类型的测试方法。 |
| *IntegrationTests* | 包含使用自定义 `WebApplicationFactory` 类的索引页的集成测试。 |
| *帮助程序/实用工具* | <ul><li>*Utilities.cs*包含用于使用测试数据对数据库进行种子设定的 `InitializeDbForTests` 方法。</li><li>*HtmlHelpers.cs*提供了一个方法，用于返回 AngleSharp `IHtmlDocument` 供测试方法使用。</li><li>*HttpClientExtensions.cs*为 `SendAsync` 提供重载，以将请求提交到 SUT。</li></ul> |

测试框架为[xUnit](https://xunit.github.io/)。 集成测试是使用 TestHost 的[AspNetCore](/dotnet/api/microsoft.aspnetcore.testhost)进行的，其中包括[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)。 由于[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包用于配置测试主机和测试服务器，因此 `TestHost` 和 `TestServer` 包在测试应用的项目文件或开发人员配置中不需要直接包引用。

**播种要测试的数据库**

集成测试在执行测试前通常需要数据库中的一个小型数据集。 例如，删除测试调用数据库记录，因此数据库必须至少有一条记录，删除请求才能成功。

该示例应用在*Utilities.cs*中使用三个消息对数据库进行种子设定，当测试执行时，可以使用这些消息：

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

在其 `Startup.ConfigureServices` 方法中注册了 SUT 的数据库上下文。 执行应用的 `Startup.ConfigureServices` 代码*后*，将执行测试应用的 `builder.ConfigureServices` 回调。 若要为测试使用其他数据库，必须将应用程序的数据库上下文替换 `builder.ConfigureServices`中。 有关详细信息，请参阅[自定义 WebApplicationFactory](#customize-webapplicationfactory)部分。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

集成测试可确保应用程序的组件在包含应用程序支持的基础结构的级别（例如数据库、文件系统和网络）正常运行。 ASP.NET Core 支持结合使用单元测试框架和测试 web 主机和内存中测试服务器的集成测试。

本主题假定基本了解单元测试。 如果不熟悉测试概念，请参阅[.Net Core 中的单元测试和 .NET Standard](/dotnet/core/testing/)主题及其链接的内容。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）

该示例应用是 Razor Pages 应用程序，并假定基本了解 Razor Pages。 如果不熟悉 Razor Pages，请参阅以下主题：

* [Razor 页面介绍](xref:razor-pages/index)
* [Razor 页面入门](xref:tutorials/razor-pages/razor-pages-start)
* [Razor 页面单元测试](xref:test/razor-pages-tests)

> [!NOTE]
> 对于测试 Spa，我们建议使用[Selenium](https://www.seleniumhq.org/)这样的工具，它可以自动执行浏览器。

## <a name="introduction-to-integration-tests"></a>集成测试简介

与[单元测试](/dotnet/core/testing/)相比，集成测试在更广泛的级别上评估应用的组件。 单元测试用于测试独立的软件组件，如单独的类方法。 集成测试确认两个或更多应用程序组件一起工作以生成预期的结果，其中可能包括完全处理请求所需的每个组件。

这些广泛的测试用于测试应用程序的基础结构和整个框架，通常包括以下组件：

* 数据库
* 文件系统
* 网络设备
* 请求-响应管道

单元测试使用称为*fakes*或*mock 对象*的制造组件来代替基础结构组件。

与单元测试相比，集成测试：

* 使用应用在生产环境中使用的实际组件。
* 需要进行更多的代码和数据处理。
* 需要较长时间才能运行。

因此，将集成测试的使用限制为最重要的基础结构方案。 如果可以使用单元测试或集成测试来测试行为，请选择单元测试。

> [!TIP]
> 请勿为数据库和文件系统的每个可能的数据排列和文件访问编写集成测试。 无论应用程序中有多少位置与数据库和文件系统交互，一组集中式的读取、写入、更新和删除集成测试通常都能充分测试数据库和文件系统组件。 使用单元测试对与这些组件进行交互的方法逻辑进行例程测试。 在单元测试中，使用基础结构 fakes/模拟会导致更快地执行测试。

> [!NOTE]
> 在讨论集成测试时，测试的项目经常称为 "测试中的*系统*" 或简称 "SUT"。
>
> *本主题中使用 "SUT" 来引用已测试的 ASP.NET Core 应用。*

## <a name="aspnet-core-integration-tests"></a>ASP.NET Core 集成测试

ASP.NET Core 中的集成测试需要以下各项：

* 测试项目用于包含和执行测试。 测试项目具有对 SUT 的引用。
* 测试项目将为 SUT 创建测试 web 主机，并使用测试服务器客户端来处理 SUT 的请求和响应。
* 测试运行程序用于执行测试并报告测试结果。

集成测试按一个顺序特定的事件序列发生, 其中包括常见的 *Arrange*、*Act* 和 *Assert* 测试步骤:

1. 已配置 SUT 的 web 主机。
1. 创建测试服务器客户端以向应用程序提交请求。
1. 执行 "*排列*测试" 步骤：测试应用准备请求。
1. 执行*Act*测试步骤：客户端提交请求并接收响应。
1. 将*执行断言*测试步骤：根据*预期*的响应验证*实际*响应是*通过*还是*失败*。
1. 此过程将一直继续，直到执行了所有测试。
1. 将报告测试结果。

通常，测试 web 主机的配置与应用程序用于测试运行的普通 web 主机的配置不同。 例如，可以将不同的数据库或不同的应用设置用于测试。

基础结构组件（例如测试 web 主机和内存中测试服务器（[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)））由[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包提供或管理。 使用此包简化了测试的创建和执行。

`Microsoft.AspNetCore.Mvc.Testing` 包处理以下任务：

* 将依赖项文件（ *. .deps.json*）从 SUT 复制到测试项目的*bin*目录中。
* 将[内容根](xref:fundamentals/index#content-root)设置为 SUT 的项目根，以便在执行测试时找到静态文件和页面/视图。
* 提供[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)类，以简化与 `TestServer`的 SUT 的引导。

[单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)文档介绍了如何设置测试项目和测试运行程序，以及有关如何为测试和测试类命名测试和建议的详细说明。

> [!NOTE]
> 为应用程序创建测试项目时，请将集成测试中的单元测试分成不同的项目。 这有助于确保不会意外地将基础结构测试组件包含在单元测试中。 单元测试和集成测试的隔离还允许控制运行的测试集。

Razor Pages 应用和 MVC 应用的测试的配置几乎没有任何区别。 唯一的区别在于测试的命名方式。 在 Razor Pages 应用程序中，页终结点的测试通常以页面模型类命名（例如，`IndexPageTests` 为索引页测试组件集成）。 在 MVC 应用中，通常按控制器类对测试进行组织，并按它们所测试的控制器（例如 `HomeControllerTests` 来测试主控制器的组件集成）进行命名。

## <a name="test-app-prerequisites"></a>测试应用必备组件

测试项目必须：

* 引用以下包：
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* 在项目文件中指定 Web SDK （`<Project Sdk="Microsoft.NET.Sdk.Web">`）。 引用[AspNetCore 元包](xref:fundamentals/metapackage-app)时，需要 Web SDK。

可以在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中查看这些先决条件。 检查 "*测试"/"RazorPagesProject"/"RazorPagesProject* " 文件。 示例应用使用[xUnit](https://xunit.github.io/)测试框架和[AngleSharp](https://anglesharp.github.io/)分析器库，因此示例应用还引用：

* [xunit](https://www.nuget.org/packages/xunit/)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>SUT 环境

如果未设置 SUT 的[环境](xref:fundamentals/environments)，环境将默认为 "开发"。

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>具有默认 WebApplicationFactory 的基本测试

[WebApplicationFactory\<TEntryPoint >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)用于创建集成测试的[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 。 `TEntryPoint` 是 SUT （通常是 `Startup` 类）的入口点类。

测试类实现*类装置*接口（[IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)）以指示类包含测试，并跨类中的测试提供共享对象实例。

下面的测试类 `BasicTests`使用 `WebApplicationFactory` 来启动 SUT，并为测试方法 `Get_EndpointsReturnSuccessAndCorrectContentType`提供[HttpClient](/dotnet/api/system.net.http.httpclient) 。 方法将检查响应状态代码是否成功（范围200-299 中的状态代码）和多个应用页面的 `Content-Type` 标头是否 `text/html; charset=utf-8`。

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)创建自动跟随重定向并处理 cookie 的 `HttpClient` 的实例。

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

默认情况下，当启用[GDPR 同意策略](xref:security/gdpr)时，不会跨请求保留非关键 cookie。 若要保留不重要的 cookie （如 TempData 提供程序使用的 cookie），请将它们标记为测试中的重要 cookie。 有关将 cookie 标记为必要的说明，请参阅[基本 cookie](xref:security/gdpr#essential-cookies)。

## <a name="customize-webapplicationfactory"></a>自定义 WebApplicationFactory

通过继承 `WebApplicationFactory` 来创建一个或多个自定义工厂，可以独立于测试类创建 Web 主机配置：

1. 从 `WebApplicationFactory` 继承并重写[ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)。 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)允许通过[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)配置服务集合：

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   [示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)中的数据库种子设定由 `InitializeDbForTests` 方法执行。 [集成测试示例：测试应用组织](#test-app-organization)部分介绍了方法。

2. 使用测试类中的自定义 `CustomWebApplicationFactory`。 下面的示例使用 `IndexPageTests` 类中的工厂：

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   示例应用的客户端配置为阻止 `HttpClient` 以下重定向。 如稍后在 "[模拟身份验证](#mock-authentication)" 部分中所述，这允许测试检查应用程序的第一个响应的结果。 第一个响应是包含 `Location` 标头的多个测试的重定向。

3. 典型的测试使用 `HttpClient` 和 helper 方法来处理请求和响应：

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

对 SUT 的任何 POST 请求必须满足防伪检查，该检查是由应用的[数据保护防伪系统](xref:security/data-protection/introduction)自动完成的。 为了安排测试的 POST 请求，测试应用必须：

1. 发出对页面的请求。
1. 分析防伪 cookie 并请求响应中的验证令牌。
1. 发出带有防伪 cookie 的 POST 请求，并就地请求验证令牌。

[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中的 `SendAsync` helper 扩展方法（Helper */HttpClientExtensions*）和 `GetDocumentAsync` helper 方法（helper */HtmlHelpers*）使用[AngleSharp](https://anglesharp.github.io/)分析器来处理使用以下方法的防伪检查：

* `GetDocumentAsync` &ndash; 接收 [HttpResponseMessage ](/dotnet/api/system.net.http.httpresponsemessage)并返回`IHtmlDocument`。 `GetDocumentAsync` 使用一个工厂，该工厂根据原始 `HttpResponseMessage`准备*虚拟响应*。 有关详细信息，请参阅[AngleSharp 文档](https://github.com/AngleSharp/AngleSharp#documentation)。
* `SendAsync` 扩展方法 `HttpClient` 构成[HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)并调用[SendAsync （HttpRequestMessage）](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)将请求提交到 SUT。 `SendAsync` 的重载接受 HTML 窗体（`IHtmlFormElement`）以及以下内容：
  * 窗体的 "提交" 按钮（`IHtmlElement`）
  * 窗体值集合（`IEnumerable<KeyValuePair<string, string>>`）
  * 提交按钮（`IHtmlElement`）和窗体值（`IEnumerable<KeyValuePair<string, string>>`）

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/)是用于演示目的的第三方解析库，适用于本主题和示例应用。 ASP.NET Core 应用的集成测试不支持或不需要 AngleSharp。 可以使用其他分析器，如[Html 灵活性包（HAP）](https://html-agility-pack.net/)。 另一种方法是编写代码来直接处理防伪系统的请求验证令牌和防伪 cookie。

## <a name="customize-the-client-with-withwebhostbuilder"></a>用 WithWebHostBuilder 自定义客户端

如果在测试方法中需要其他配置， [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)会创建一个新的 `WebApplicationFactory`，其中包含由配置进一步自定义的[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 。

[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)的 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 测试方法演示如何使用 `WithWebHostBuilder`。 此测试通过在 SUT 中触发窗体提交来在数据库中执行记录删除。

由于 `IndexPageTests` 类中的另一个测试会执行一个操作，该操作将删除数据库中的所有记录，并且该操作可能在 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 方法之前运行，因此，该数据库在此测试方法中种子，以确保要删除的 SUT 存在记录。 选择 SUT 中 `messages` 窗体的第一个 "删除" 按钮时，会将请求中的内容模拟到 SUT：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>客户端选项

下表显示了在创建 `HttpClient` 实例时可用的默认[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 。

| 选项 | 描述 | 默认值 |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | 获取或设置 `HttpClient` 实例是否应自动跟随重定向响应。 | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | 获取或设置 `HttpClient` 实例的基址。 | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | 获取或设置 `HttpClient` 实例是否应处理 cookie。 | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | 获取或设置 `HttpClient` 实例应遵循的重定向响应的最大数目。 | 7 |

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

通过在主机生成器上调用[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) ，可以在测试中覆盖服务。 **若要注入模拟服务，SUT 必须有一个具有 `Startup.ConfigureServices` 方法的 `Startup` 类。**

示例 SUT 包含一个返回 quote 的作用域服务。 请求索引页时，引号嵌入到索引页上的隐藏字段中。

*服务/IQuoteService*：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*服务/QuoteService*：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

Pages/Index.cshtml.cs：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index. cs*：

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

运行 SUT 应用时，将生成以下标记：

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

若要在集成测试中测试服务和引号注入，测试会将模拟服务注入到 SUT。 模拟服务会将应用的 `QuoteService` 替换为测试应用提供的服务，称为 `TestQuoteService`：

*IntegrationTests.IndexPageTests.cs*：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

调用 `ConfigureTestServices`，并注册作用域内服务：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

在测试执行过程中生成的标记反映 `TestQuoteService`提供的引号文本，因此断言通过：

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>模拟身份验证

`AuthTests` 类中的测试检查安全终结点：

* 将未经身份验证的用户重定向到应用程序的登录页。
* 返回已经过身份验证的用户的内容。

在 SUT 中，`/SecurePage` 页使用[AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)约定将[AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)应用到页面。 有关详细信息，请参阅[Razor Pages 授权约定](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)。

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

在`Get_SecurePageRedirectsAnUnauthenticatedUser`测试中, 通过将[AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)设置为`false`, 将 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 设置为禁止重定向:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

通过禁止客户端按照重定向操作，可以执行以下检查：

* 可以对照预期的[HttpStatusCode](/dotnet/api/system.net.httpstatuscode)结果检查 SUT 返回的状态代码，而不是在重定向到登录页后返回最终状态代码，这会是[HttpStatusCode](/dotnet/api/system.net.httpstatuscode)。
* 将检查响应标头中的 `Location` 标头值，以确认该标头值以 `http://localhost/Identity/Account/Login`（而不是最终的登录页响应）开头，而 `Location` 标头不存在。

测试应用可以模拟[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)中的 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>，以便测试身份验证和授权的各个方面。 最小方案返回[AuthenticateResult](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

当身份验证方案设置为 `Test` 在其中向 `ConfigureTestServices`注册了 `AddAuthentication` 时，将调用 `TestAuthHandler` 来对用户进行身份验证：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

有关 `WebApplicationFactoryClientOptions`的详细信息，请参阅[Client options](#client-options)部分。

## <a name="set-the-environment"></a>设置环境

默认情况下，SUT 的主机和应用环境配置为使用开发环境。 重写 SUT 的环境：

* 设置 `ASPNETCORE_ENVIRONMENT` 环境变量（例如，`Staging`、`Production`或其他自定义值，例如 `Testing`）。
* 重写测试应用中 `CreateHostBuilder`，以读取以 `ASPNETCORE`为前缀的环境变量。

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>测试基础结构如何推断应用内容根路径

`WebApplicationFactory` 构造函数通过使用等于 `TEntryPoint` 程序集 `System.Reflection.Assembly.FullName`的键搜索包含集成测试的程序集上的[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)来推断应用[内容根](xref:fundamentals/index#content-root)路径。 如果找不到具有正确键的属性，`WebApplicationFactory` 将回退到搜索解决方案文件（ *.sln*），并在解决方案目录中追加 `TEntryPoint` 程序集名称。 应用根目录（内容根路径）用于发现视图和内容文件。

## <a name="disable-shadow-copying"></a>禁用卷影复制

卷影复制会导致在输出目录以外的目录中执行测试。 要使测试正常工作，必须禁用卷影复制。 该[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)使用 xUnit 并通过包含具有正确配置设置的*xUnit*文件来禁用 xUnit 的卷影复制。 有关详细信息，请参阅[配置 xUnit 与 JSON](https://xunit.github.io/docs/configuring-with-json.html)。

将*xunit*文件添加到测试项目的根，其中包含以下内容：

```json
{
  "shadowCopy": false
}
```

如果使用的是 Visual Studio，请将文件的 "**复制到输出目录**" 属性设置为 "**始终复制**"。 如果不使用 Visual Studio，请将 `Content` 目标添加到测试应用的项目文件中：

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a>对象的处理

执行 `IClassFixture` 实现的测试后，当 xUnit 释放[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)时， [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)和[HttpClient](/dotnet/api/system.net.http.httpclient)会被释放。 如果开发人员实例化的对象需要处置，请在 `IClassFixture` 实现中释放这些对象。 有关详细信息，请参阅[实现 Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。

## <a name="integration-tests-sample"></a>集成测试示例

该[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)由两个应用组成：

| 应用程序 | 项目目录 | 描述 |
| --- | ----------------- | ----------- |
| 消息应用（SUT） | *src/RazorPagesProject* | 允许用户添加、删除一个、删除和分析消息。 |
| 测试应用 | *tests/RazorPagesProject.Tests* | 用于集成测试 SUT。 |

可以使用 IDE （如[Visual Studio](https://visualstudio.microsoft.com)）的内置测试功能来运行测试。 如果使用[Visual Studio Code](https://code.visualstudio.com/)或命令行，请在*RazorPagesProject 目录中的命令*提示符处执行以下命令：

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a>消息应用（SUT）组织

SUT 是 Razor Pages 的消息系统，具有以下特征：

* 应用的 "索引" 页（*Pages/索引. cshtml*和*pages/index*）提供了一个 UI 和页面模型方法，用于控制消息的添加、删除和分析（每条消息的平均单词数）。
* 消息由 `Message` 类（*Data/message*）描述，具有两个属性： `Id` （密钥）和 `Text` （message）。 `Text` 属性是必需的，并且限制为200个字符。
* 使用[实体框架的内存中数据库](/ef/core/providers/in-memory/)&#8224;来存储消息。
* 应用在其数据库上下文类中包含数据访问层（DAL），`AppDbContext` （*data/AppDbContext*）。
* 如果数据库在应用启动时为空，则会用三条消息初始化消息存储。
* 应用包括只能由经过身份验证的用户访问的 `/SecurePage`。

&#8224;EF 主题[使用 InMemory 进行测试](/ef/core/miscellaneous/testing/in-memory)说明了如何将内存中数据库用于使用 MSTest 进行测试。 本主题使用[xUnit](https://xunit.github.io/)测试框架。 不同测试框架中的测试概念和测试实现相似，但并不完全相同。

尽管应用程序不使用存储库模式，并且不是[工作单元（UoW）模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效示例，但 Razor Pages 支持这些模式的开发模式。 有关详细信息，请参阅[设计基础结构持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和[测试控制器逻辑](/aspnet/core/mvc/controllers/testing)（示例实现存储库模式）。

### <a name="test-app-organization"></a>测试应用组织

测试应用是 "*测试"/"RazorPagesProject* " 目录中的控制台应用。

| 测试应用程序目录 | 描述 |
| ------------------ | ----------- |
| *AuthTests* | 包含的测试方法：<ul><li>通过未经身份验证的用户访问安全页。</li><li>使用模拟 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>通过经过身份验证的用户访问安全页。</li><li>获取 GitHub 用户配置文件，并检查配置文件的用户登录名。</li></ul> |
| *BasicTests* | 包含路由和内容类型的测试方法。 |
| *IntegrationTests* | 包含使用自定义 `WebApplicationFactory` 类的索引页的集成测试。 |
| *帮助程序/实用工具* | <ul><li>*Utilities.cs*包含用于使用测试数据对数据库进行种子设定的 `InitializeDbForTests` 方法。</li><li>*HtmlHelpers.cs*提供了一个方法，用于返回 AngleSharp `IHtmlDocument` 供测试方法使用。</li><li>*HttpClientExtensions.cs*为 `SendAsync` 提供重载，以将请求提交到 SUT。</li></ul> |

测试框架为[xUnit](https://xunit.github.io/)。 集成测试是使用 TestHost 的[AspNetCore](/dotnet/api/microsoft.aspnetcore.testhost)进行的，其中包括[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)。 由于[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包用于配置测试主机和测试服务器，因此 `TestHost` 和 `TestServer` 包在测试应用的项目文件或开发人员配置中不需要直接包引用。

**播种要测试的数据库**

集成测试在执行测试前通常需要数据库中的一个小型数据集。 例如，删除测试调用数据库记录，因此数据库必须至少有一条记录，删除请求才能成功。

该示例应用在*Utilities.cs*中使用三个消息对数据库进行种子设定，当测试执行时，可以使用这些消息：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a>其他资源

* [单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
