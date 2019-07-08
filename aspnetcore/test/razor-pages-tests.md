---
title: 在 ASP.NET Core razor 页单元测试
author: guardrex
description: 了解如何创建 Razor 页面应用的单元测试。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2017
uid: test/razor-pages-tests
ms.openlocfilehash: f89b4fcb0065e725f70deec7859e373f9158b4bd
ms.sourcegitcommit: 91cc1f07ef178ab709ea42f8b3a10399c970496e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622778"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a><span data-ttu-id="e7ee0-103">在 ASP.NET Core razor 页单元测试</span><span class="sxs-lookup"><span data-stu-id="e7ee0-103">Razor Pages unit tests in ASP.NET Core</span></span>

<span data-ttu-id="e7ee0-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e7ee0-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e7ee0-105">ASP.NET Core 支持 Razor 页应用的单元测试。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-105">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="e7ee0-106">测试数据的访问层 (DAL) 和页面模型帮助确保：</span><span class="sxs-lookup"><span data-stu-id="e7ee0-106">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="e7ee0-107">Razor 页面应用的部分独立工作和一起作为一个单元应用构建过程。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-107">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="e7ee0-108">类和方法具有有限的作用域的责任。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-108">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="e7ee0-109">应用程序的行为方式上存在的其他文档。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-109">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="e7ee0-110">自动的生成和部署期间发现错误由更新的代码，这些错误的回归。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-110">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="e7ee0-111">本主题假定你基本了解 Razor 页面应用程序和单元测试。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-111">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="e7ee0-112">如果您是熟悉 Razor 页面应用程序或测试概念，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="e7ee0-112">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [<span data-ttu-id="e7ee0-113">单元测试 C# 中使用 dotnet 测试和 xUnit 的.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7ee0-113">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="e7ee0-114">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="e7ee0-114">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e7ee0-115">示例项目包含两个应用：</span><span class="sxs-lookup"><span data-stu-id="e7ee0-115">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="e7ee0-116">应用</span><span class="sxs-lookup"><span data-stu-id="e7ee0-116">App</span></span>         | <span data-ttu-id="e7ee0-117">项目文件夹</span><span class="sxs-lookup"><span data-stu-id="e7ee0-117">Project folder</span></span>                     | <span data-ttu-id="e7ee0-118">描述</span><span class="sxs-lookup"><span data-stu-id="e7ee0-118">Description</span></span> |
| ----------- | ---------------------------------- | ----------- |
| <span data-ttu-id="e7ee0-119">消息应用程序</span><span class="sxs-lookup"><span data-stu-id="e7ee0-119">Message app</span></span> | <span data-ttu-id="e7ee0-120">*src/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="e7ee0-120">*src/RazorPagesTestSample*</span></span>         | <span data-ttu-id="e7ee0-121">允许用户将添加一条消息，删除一条消息，删除所有消息和分析的消息 （找到的平均每个消息的单词数）。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-121">Allows a user to add a message, delete one message, delete all messages, and analyze messages (find the average number of words per message).</span></span> |
| <span data-ttu-id="e7ee0-122">测试应用</span><span class="sxs-lookup"><span data-stu-id="e7ee0-122">Test app</span></span>    | <span data-ttu-id="e7ee0-123">*tests/RazorPagesTestSample.Tests*</span><span class="sxs-lookup"><span data-stu-id="e7ee0-123">*tests/RazorPagesTestSample.Tests*</span></span> | <span data-ttu-id="e7ee0-124">用于单元测试 DAL 和索引消息应用的页面模型。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-124">Used to unit test the DAL and Index page model of the message app.</span></span> |

<span data-ttu-id="e7ee0-125">可以使用内置测试功能的 IDE，如运行测试[Visual Studio](/visualstudio/test/unit-test-your-code)或[Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-125">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](/visualstudio/test/unit-test-your-code) or [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span></span> <span data-ttu-id="e7ee0-126">如果使用[Visual Studio Code](https://code.visualstudio.com/)或命令行中，执行以下命令在命令提示符处*tests/RazorPagesTestSample.Tests*文件夹：</span><span class="sxs-lookup"><span data-stu-id="e7ee0-126">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```console
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="e7ee0-127">消息应用的组织</span><span class="sxs-lookup"><span data-stu-id="e7ee0-127">Message app organization</span></span>

<span data-ttu-id="e7ee0-128">消息应用程序是 Razor 页面消息系统具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="e7ee0-128">The message app is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="e7ee0-129">应用程序的索引页 (*pages/Index.cshtml*并*Pages/Index.cshtml.cs*) 提供 UI 和页面模型的方法来控制添加、 删除和分析的消息 （找到的平均数每个消息词）。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-129">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (find the average number of words per message).</span></span>
* <span data-ttu-id="e7ee0-130">一条消息由描述`Message`类 (*Data/Message.cs*) 具有两个属性： `Id` （密钥） 和`Text`（消息）。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-130">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="e7ee0-131">`Text`属性是必需的限制为 200 个字符。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-131">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="e7ee0-132">使用存储的消息[Entity Framework 的内存中数据库](/ef/core/providers/in-memory/)&#8224;。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-132">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="e7ee0-133">应用程序包含在其数据库上下文类中，DAL `AppDbContext` (*Data/AppDbContext.cs*)。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-133">The app contains a DAL in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="e7ee0-134">DAL 方法被标记为`virtual`，它允许模拟在测试中使用的方法。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-134">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="e7ee0-135">如果数据库为空应用程序启动时，使用三个消息初始化消息存储区。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-135">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="e7ee0-136">这些*设定种子的消息*还在测试中使用。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-136">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="e7ee0-137">&#8224;EF 主题[测试与 InMemory](/ef/core/miscellaneous/testing/in-memory)，说明如何使用内存中数据库的使用 MSTest 的测试。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-137">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="e7ee0-138">本主题使用[xUnit](https://xunit.github.io/)测试框架。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-138">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="e7ee0-139">测试概念和跨不同测试框架的测试实现有类似，但不是完全相同。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-139">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="e7ee0-140">虽然此示例应用不使用存储库模式并不是有效的示例[工作单元 (UoW) 模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)，Razor 页面支持开发这些模式。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-140">Although the sample app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="e7ee0-141">有关详细信息，请参阅[设计基础结构持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和<xref:mvc/controllers/testing>（此示例实现存储库模式）。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-141">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and <xref:mvc/controllers/testing> (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="e7ee0-142">测试应用程序的组织</span><span class="sxs-lookup"><span data-stu-id="e7ee0-142">Test app organization</span></span>

<span data-ttu-id="e7ee0-143">测试应用程序是一个控制台应用程序内的*tests/RazorPagesTestSample.Tests*文件夹。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-143">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="e7ee0-144">测试应用程序文件夹</span><span class="sxs-lookup"><span data-stu-id="e7ee0-144">Test app folder</span></span> | <span data-ttu-id="e7ee0-145">描述</span><span class="sxs-lookup"><span data-stu-id="e7ee0-145">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="e7ee0-146">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="e7ee0-146">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="e7ee0-147">*DataAccessLayerTest.cs* DAL 包含单元测试。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-147">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="e7ee0-148">*IndexPageTests.cs*包含针对索引页面模型的单元测试。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-148">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="e7ee0-149">*实用程序*</span><span class="sxs-lookup"><span data-stu-id="e7ee0-149">*Utilities*</span></span>     | <span data-ttu-id="e7ee0-150">包含`TestDbContextOptions`用来创建新的数据库上下文选项为每个 DAL 单元测试，以便将数据库重置为其基线条件的每个测试方法。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-150">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="e7ee0-151">测试框架这[xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-151">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="e7ee0-152">模拟框架的对象是[Moq](https://github.com/moq/moq4)。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-152">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="e7ee0-153">单元测试的数据访问层 (DAL)</span><span class="sxs-lookup"><span data-stu-id="e7ee0-153">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="e7ee0-154">消息应用程序中包含的四个方法都具有 DAL`AppDbContext`类 (*src/RazorPagesTestSample/Data/AppDbContext.cs*)。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-154">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="e7ee0-155">每个方法中测试应用程序具有一个或两个单元测试。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-155">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="e7ee0-156">DAL 方法</span><span class="sxs-lookup"><span data-stu-id="e7ee0-156">DAL method</span></span>               | <span data-ttu-id="e7ee0-157">函数</span><span class="sxs-lookup"><span data-stu-id="e7ee0-157">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="e7ee0-158">获取`List<Message>`从数据库按`Text`属性。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-158">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="e7ee0-159">添加`Message`到数据库。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-159">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="e7ee0-160">将删除所有`Message`数据库中的条目。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-160">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="e7ee0-161">将删除单个`Message`从数据库`Id`。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-161">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="e7ee0-162">需要的 DAL 单元测试<xref:Microsoft.EntityFrameworkCore.DbContextOptions>时创建一个新`AppDbContext`为每个测试。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-162">Unit tests of the DAL require <xref:Microsoft.EntityFrameworkCore.DbContextOptions> when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="e7ee0-163">一种方法创建`DbContextOptions`每个测试是使用<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span><span class="sxs-lookup"><span data-stu-id="e7ee0-163">One approach to creating the `DbContextOptions` for each test is to use a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="e7ee0-164">此方法的问题是每个测试中的任何状态前次测试保留它接收的数据库。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-164">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="e7ee0-165">在尝试写入不会相互干扰的原子单元测试时，则可能存在问题。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-165">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="e7ee0-166">若要强制`AppDbContext`若要为每个测试中使用新的数据库上下文，提供`DbContextOptions`基于新的服务提供程序的实例。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-166">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="e7ee0-167">测试应用演示了如何执行此操作使用其`Utilities`类方法`TestDbContextOptions`(*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span><span class="sxs-lookup"><span data-stu-id="e7ee0-167">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="e7ee0-168">使用`DbContextOptions`接 DAL 单元测试，以使用新数据库实例以原子方式运行每个测试：</span><span class="sxs-lookup"><span data-stu-id="e7ee0-168">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="e7ee0-169">在每个测试方法`DataAccessLayerTest`类 (*UnitTests/DataAccessLayerTest.cs*) 遵循类似的排列 Act 断言模式：</span><span class="sxs-lookup"><span data-stu-id="e7ee0-169">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="e7ee0-170">排列：为测试配置数据库和/或定义预期的结果。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-170">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="e7ee0-171">Act:执行测试。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-171">Act: The test is executed.</span></span>
1. <span data-ttu-id="e7ee0-172">断言：做出声明来确定测试结果是否成功完成。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-172">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="e7ee0-173">例如，`DeleteMessageAsync`方法负责删除一条消息由标识其`Id`(*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span><span class="sxs-lookup"><span data-stu-id="e7ee0-173">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="e7ee0-174">有两个测试此方法。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-174">There are two tests for this method.</span></span> <span data-ttu-id="e7ee0-175">一个测试检查数据库中存在消息时，将方法删除一条消息。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-175">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="e7ee0-176">如果不会更改数据库的其他方法测试消息`Id`为删除不存在。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-176">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="e7ee0-177">`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`方法如下所示：</span><span class="sxs-lookup"><span data-stu-id="e7ee0-177">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="e7ee0-178">首先，该方法执行准备步骤中，执行步骤准备发生。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-178">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="e7ee0-179">获取和保存在种子设定消息`seedMessages`。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-179">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="e7ee0-180">种子设定消息保存到数据库中。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-180">The seeding messages are saved into the database.</span></span> <span data-ttu-id="e7ee0-181">与消息`Id`的`1`设置为删除。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-181">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="e7ee0-182">当`DeleteMessageAsync`执行方法时，预期的消息应具有的所有消息使用除`Id`的`1`。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-182">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="e7ee0-183">`expectedMessages`变量表示此预期的结果。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-183">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="e7ee0-184">方法的行为：`DeleteMessageAsync`执行方法并传入`recId`的`1`:</span><span class="sxs-lookup"><span data-stu-id="e7ee0-184">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="e7ee0-185">最后，此方法获取`Messages`上下文中并将其到比较`expectedMessages`这两个相等的断言：</span><span class="sxs-lookup"><span data-stu-id="e7ee0-185">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="e7ee0-186">要比较的两个`List<Message>`相同：</span><span class="sxs-lookup"><span data-stu-id="e7ee0-186">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="e7ee0-187">消息按排序`Id`。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-187">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="e7ee0-188">消息对比较上`Text`属性。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-188">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="e7ee0-189">类似的测试方法，`DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound`检查尝试删除不存在一条消息的结果。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-189">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="e7ee0-190">在这种情况下，在数据库中预期的消息应等于后的实际消息`DeleteMessageAsync`执行方法。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-190">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="e7ee0-191">应未更改数据库的内容：</span><span class="sxs-lookup"><span data-stu-id="e7ee0-191">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="e7ee0-192">页面模型方法的单元测试</span><span class="sxs-lookup"><span data-stu-id="e7ee0-192">Unit tests of the page model methods</span></span>

<span data-ttu-id="e7ee0-193">另一组单元测试的测试的页面模型方法负责。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-193">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="e7ee0-194">在邮件应用中，索引页面模型中找到`IndexModel`类中*src/RazorPagesTestSample/Pages/Index.cshtml.cs*。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-194">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="e7ee0-195">页面模型方法</span><span class="sxs-lookup"><span data-stu-id="e7ee0-195">Page model method</span></span> | <span data-ttu-id="e7ee0-196">函数</span><span class="sxs-lookup"><span data-stu-id="e7ee0-196">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="e7ee0-197">从 UI 使用 DAL 获取消息`GetMessagesAsync`方法。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-197">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="e7ee0-198">如果[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)有效时，调用`AddMessageAsync`向数据库添加一条消息。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-198">If the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="e7ee0-199">调用`DeleteAllMessagesAsync`若要删除所有数据库中的消息。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-199">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="e7ee0-200">执行`DeleteMessageAsync`若要删除的消息`Id`指定。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-200">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="e7ee0-201">如果一个或多个消息是在数据库中，将计算的平均每个消息的单词数。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-201">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="e7ee0-202">使用七个测试中的测试页面模型方法`IndexPageTests`类 (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*)。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-202">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="e7ee0-203">测试使用熟悉的排列断言 Act 模式。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-203">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="e7ee0-204">这些测试的重点：</span><span class="sxs-lookup"><span data-stu-id="e7ee0-204">These tests focus on:</span></span>

* <span data-ttu-id="e7ee0-205">确定是否方法遵循正确的行为时[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)无效。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-205">Determining if the methods follow the correct behavior when the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is invalid.</span></span>
* <span data-ttu-id="e7ee0-206">确认方法产生正确<xref:Microsoft.AspNetCore.Mvc.IActionResult>。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-206">Confirming the methods produce the correct <xref:Microsoft.AspNetCore.Mvc.IActionResult>.</span></span>
* <span data-ttu-id="e7ee0-207">正在检查正确进行属性值分配。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-207">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="e7ee0-208">测试此组通常模拟 DAL 以生成预期的页面模型方法会执行步骤的数据的方法。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-208">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="e7ee0-209">例如，`GetMessagesAsync`方法的`AppDbContext`模拟以生成输出。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-209">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="e7ee0-210">当页面模型方法执行此方法时，模拟将返回的结果。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-210">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="e7ee0-211">数据并不是来自该数据库。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-211">The data doesn't come from the database.</span></span> <span data-ttu-id="e7ee0-212">这将创建页面模型测试中使用 DAL 可预测、 可靠的测试的条件。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-212">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="e7ee0-213">`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`测试显示了如何将`GetMessagesAsync`方法模拟的页面模型：</span><span class="sxs-lookup"><span data-stu-id="e7ee0-213">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="e7ee0-214">当`OnGetAsync`Act 步骤中执行方法时，它调用的页面模型`GetMessagesAsync`方法。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-214">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="e7ee0-215">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span><span class="sxs-lookup"><span data-stu-id="e7ee0-215">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="e7ee0-216">`IndexPage` 页面模型`OnGetAsync`方法 (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="e7ee0-216">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="e7ee0-217">`GetMessagesAsync` DAL 中的方法不会返回此方法调用的结果。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-217">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="e7ee0-218">模拟的版本的方法返回的结果。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-218">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="e7ee0-219">在中`Assert`步骤，实际消息 (`actualMessages`) 从分配的`Messages`的页面模型的属性。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-219">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="e7ee0-220">分配消息时，也执行类型检查。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-220">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="e7ee0-221">通过进行比较的预期和实际消息及其`Text`属性。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-221">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="e7ee0-222">测试断言，这两个`List<Message>`实例包含相同的消息。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-222">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="e7ee0-223">此组中的其他测试创建页包含的模型对象<xref:Microsoft.AspNetCore.Http.DefaultHttpContext>，则<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>、<xref:Microsoft.AspNetCore.Mvc.ActionContext>建立`PageContext`即`ViewDataDictionary`，和一个`PageContext`。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-223">Other tests in this group create page model objects that include the <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, an <xref:Microsoft.AspNetCore.Mvc.ActionContext> to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="e7ee0-224">这些可执行测试。</span><span class="sxs-lookup"><span data-stu-id="e7ee0-224">These are useful in conducting tests.</span></span> <span data-ttu-id="e7ee0-225">例如，消息应用程序建立`ModelState`错误<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>检查是否是有效<xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>时，将返回`OnPostAddMessageAsync`执行：</span><span class="sxs-lookup"><span data-stu-id="e7ee0-225">For example, the message app establishes a `ModelState` error with <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> to check that a valid <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="e7ee0-226">其他资源</span><span class="sxs-lookup"><span data-stu-id="e7ee0-226">Additional resources</span></span>

* [<span data-ttu-id="e7ee0-227">单元测试 C# 中使用 dotnet 测试和 xUnit 的.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7ee0-227">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* <span data-ttu-id="e7ee0-228">[单元测试代码](/visualstudio/test/unit-test-your-code)(Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="e7ee0-228">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* <xref:test/integration-tests>
* [<span data-ttu-id="e7ee0-229">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="e7ee0-229">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="e7ee0-230">使用 Visual Studio for Mac 在 macOS 上构建完整的 .NET Core 解决方案</span><span class="sxs-lookup"><span data-stu-id="e7ee0-230">Building a complete .NET Core solution on macOS using Visual Studio for Mac</span></span>](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [<span data-ttu-id="e7ee0-231">开始使用 xUnit.net:使用.NET SDK 命令行使用.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7ee0-231">Getting started with xUnit.net: Using .NET Core with the .NET SDK command line</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="e7ee0-232">Moq</span><span class="sxs-lookup"><span data-stu-id="e7ee0-232">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="e7ee0-233">Moq 快速入门</span><span class="sxs-lookup"><span data-stu-id="e7ee0-233">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)
