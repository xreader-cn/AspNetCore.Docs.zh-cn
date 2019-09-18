---
title: 在 ASP.NET Core razor 页单元测试
author: guardrex
description: 了解如何为 Razor Pages 应用创建单元测试。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: test/razor-pages-tests
ms.openlocfilehash: afac97d686ef190ebb92d20a55a15dd774b0d1de
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081430"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a><span data-ttu-id="140fe-103">在 ASP.NET Core razor 页单元测试</span><span class="sxs-lookup"><span data-stu-id="140fe-103">Razor Pages unit tests in ASP.NET Core</span></span>

<span data-ttu-id="140fe-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="140fe-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="140fe-105">ASP.NET Core 支持 Razor 页应用的单元测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-105">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="140fe-106">数据访问层（DAL）和页面模型的测试有助于确保：</span><span class="sxs-lookup"><span data-stu-id="140fe-106">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="140fe-107">在应用程序构建过程中，Razor Pages 应用程序的各个部分将独立工作，并作为一个单元一起工作。</span><span class="sxs-lookup"><span data-stu-id="140fe-107">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="140fe-108">类和方法的责任范围有限。</span><span class="sxs-lookup"><span data-stu-id="140fe-108">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="140fe-109">在应用程序的行为方式上还存在其他文档。</span><span class="sxs-lookup"><span data-stu-id="140fe-109">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="140fe-110">回归是指在自动生成和部署过程中发现的代码更新导致的错误。</span><span class="sxs-lookup"><span data-stu-id="140fe-110">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="140fe-111">本主题假定你基本了解 Razor Pages 应用和单元测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-111">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="140fe-112">如果不熟悉 Razor Pages 应用或测试概念，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="140fe-112">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [<span data-ttu-id="140fe-113">单元测试 C# 中使用 dotnet 测试和 xUnit 的.NET Core</span><span class="sxs-lookup"><span data-stu-id="140fe-113">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="140fe-114">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="140fe-114">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="140fe-115">示例项目包含两个应用：</span><span class="sxs-lookup"><span data-stu-id="140fe-115">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="140fe-116">应用</span><span class="sxs-lookup"><span data-stu-id="140fe-116">App</span></span>         | <span data-ttu-id="140fe-117">项目文件夹</span><span class="sxs-lookup"><span data-stu-id="140fe-117">Project folder</span></span>                     | <span data-ttu-id="140fe-118">描述</span><span class="sxs-lookup"><span data-stu-id="140fe-118">Description</span></span> |
| ----------- | ---------------------------------- | ----------- |
| <span data-ttu-id="140fe-119">消息应用</span><span class="sxs-lookup"><span data-stu-id="140fe-119">Message app</span></span> | <span data-ttu-id="140fe-120">*src/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="140fe-120">*src/RazorPagesTestSample*</span></span>         | <span data-ttu-id="140fe-121">允许用户添加消息、删除一条消息、删除所有消息和分析消息（查找每条消息的平均单词数）。</span><span class="sxs-lookup"><span data-stu-id="140fe-121">Allows a user to add a message, delete one message, delete all messages, and analyze messages (find the average number of words per message).</span></span> |
| <span data-ttu-id="140fe-122">测试应用</span><span class="sxs-lookup"><span data-stu-id="140fe-122">Test app</span></span>    | <span data-ttu-id="140fe-123">*tests/RazorPagesTestSample.Tests*</span><span class="sxs-lookup"><span data-stu-id="140fe-123">*tests/RazorPagesTestSample.Tests*</span></span> | <span data-ttu-id="140fe-124">用于对消息应用的 DAL 和索引页模型进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-124">Used to unit test the DAL and Index page model of the message app.</span></span> |

<span data-ttu-id="140fe-125">可以使用 IDE 的内置测试功能（如[Visual Studio](/visualstudio/test/unit-test-your-code)或[Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)）运行测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-125">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](/visualstudio/test/unit-test-your-code) or [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span></span> <span data-ttu-id="140fe-126">如果使用[Visual Studio Code](https://code.visualstudio.com/)或命令行，请在 " *RazorPagesTestSample* " 文件夹中的命令提示符处执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="140fe-126">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="140fe-127">消息应用组织</span><span class="sxs-lookup"><span data-stu-id="140fe-127">Message app organization</span></span>

<span data-ttu-id="140fe-128">消息应用是 Razor Pages 的消息系统，具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="140fe-128">The message app is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="140fe-129">应用的 "索引" 页（*Pages/索引. cshtml*和*pages/* node.js）提供了一个 UI 和页面模型方法来控制消息的添加、删除和分析（查找每条消息的平均单词数）。</span><span class="sxs-lookup"><span data-stu-id="140fe-129">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (find the average number of words per message).</span></span>
* <span data-ttu-id="140fe-130">消息由`Message`类（*Data/message .cs*）描述，具有两个属性： `Id` （键）和`Text` （message）。</span><span class="sxs-lookup"><span data-stu-id="140fe-130">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="140fe-131">此`Text`属性是必需的，并且限制为200个字符。</span><span class="sxs-lookup"><span data-stu-id="140fe-131">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="140fe-132">使用[实体框架的内存中数据库](/ef/core/providers/in-memory/)&#8224;来存储消息。</span><span class="sxs-lookup"><span data-stu-id="140fe-132">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="140fe-133">应用程序在其数据库上下文类`AppDbContext` （*Data/AppDbContext*）中包含 DAL。</span><span class="sxs-lookup"><span data-stu-id="140fe-133">The app contains a DAL in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="140fe-134">DAL 方法被标记`virtual`为，这允许模拟方法在测试中使用。</span><span class="sxs-lookup"><span data-stu-id="140fe-134">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="140fe-135">如果数据库在应用启动时为空，则会用三条消息初始化消息存储。</span><span class="sxs-lookup"><span data-stu-id="140fe-135">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="140fe-136">这些*种子消息*还在测试中使用。</span><span class="sxs-lookup"><span data-stu-id="140fe-136">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="140fe-137">&#8224;EF 主题[使用 InMemory 进行测试](/ef/core/miscellaneous/testing/in-memory)说明了如何将内存中数据库用于使用 MSTest 进行测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-137">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="140fe-138">本主题使用[xUnit](https://xunit.github.io/)测试框架。</span><span class="sxs-lookup"><span data-stu-id="140fe-138">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="140fe-139">不同测试框架中的测试概念和测试实现相似，但并不完全相同。</span><span class="sxs-lookup"><span data-stu-id="140fe-139">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="140fe-140">尽管该示例应用程序不使用存储库模式，并且不是[工作单元（UoW）模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效示例，但 Razor Pages 支持这些模式的开发模式。</span><span class="sxs-lookup"><span data-stu-id="140fe-140">Although the sample app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="140fe-141">有关详细信息，请参阅[设计基础结构持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和<xref:mvc/controllers/testing> （示例实现存储库模式）。</span><span class="sxs-lookup"><span data-stu-id="140fe-141">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and <xref:mvc/controllers/testing> (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="140fe-142">测试应用组织</span><span class="sxs-lookup"><span data-stu-id="140fe-142">Test app organization</span></span>

<span data-ttu-id="140fe-143">测试应用是 "*测试/RazorPagesTestSample* " 文件夹中的控制台应用。</span><span class="sxs-lookup"><span data-stu-id="140fe-143">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="140fe-144">测试应用文件夹</span><span class="sxs-lookup"><span data-stu-id="140fe-144">Test app folder</span></span> | <span data-ttu-id="140fe-145">描述</span><span class="sxs-lookup"><span data-stu-id="140fe-145">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="140fe-146">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="140fe-146">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="140fe-147">*DataAccessLayerTest.cs*包含 DAL 的单元测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-147">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="140fe-148">*IndexPageTests.cs*包含索引页模型的单元测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-148">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="140fe-149">*公用*</span><span class="sxs-lookup"><span data-stu-id="140fe-149">*Utilities*</span></span>     | <span data-ttu-id="140fe-150">包含用于为每个 DAL 单元测试创建新数据库上下文选项，以便将数据库重置为每个测试的基线条件的方法。`TestDbContextOptions`</span><span class="sxs-lookup"><span data-stu-id="140fe-150">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="140fe-151">测试框架为[xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="140fe-151">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="140fe-152">对象模拟框架为[Moq](https://github.com/moq/moq4)。</span><span class="sxs-lookup"><span data-stu-id="140fe-152">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="140fe-153">数据访问层（DAL）的单元测试</span><span class="sxs-lookup"><span data-stu-id="140fe-153">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="140fe-154">消息应用有一个 DAL，其中包含包含在`AppDbContext`类中的四个方法（*src/RazorPagesTestSample/Data/AppDbContext*）。</span><span class="sxs-lookup"><span data-stu-id="140fe-154">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="140fe-155">每个方法都在测试应用程序中有一个或两个单元测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-155">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="140fe-156">DAL 方法</span><span class="sxs-lookup"><span data-stu-id="140fe-156">DAL method</span></span>               | <span data-ttu-id="140fe-157">函数</span><span class="sxs-lookup"><span data-stu-id="140fe-157">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="140fe-158">从按`Text`属性排序的数据库中获取。 `List<Message>`</span><span class="sxs-lookup"><span data-stu-id="140fe-158">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="140fe-159">将添加`Message`到数据库中。</span><span class="sxs-lookup"><span data-stu-id="140fe-159">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="140fe-160">删除数据库`Message`中的所有条目。</span><span class="sxs-lookup"><span data-stu-id="140fe-160">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="140fe-161">`Message` 删除`Id`数据库中的单个。</span><span class="sxs-lookup"><span data-stu-id="140fe-161">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="140fe-162">为每个测试创建新<xref:Microsoft.EntityFrameworkCore.DbContextOptions> `AppDbContext`的时，DAL 的单元测试需要。</span><span class="sxs-lookup"><span data-stu-id="140fe-162">Unit tests of the DAL require <xref:Microsoft.EntityFrameworkCore.DbContextOptions> when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="140fe-163">为每个测试创建`DbContextOptions`的一种方法是<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>使用：</span><span class="sxs-lookup"><span data-stu-id="140fe-163">One approach to creating the `DbContextOptions` for each test is to use a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="140fe-164">此方法的问题是，每个测试都接收到数据库，并将其保留在上一个测试的任何状态。</span><span class="sxs-lookup"><span data-stu-id="140fe-164">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="140fe-165">尝试编写不相互干扰的原子单元测试时，这可能会出现问题。</span><span class="sxs-lookup"><span data-stu-id="140fe-165">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="140fe-166">若要强制`AppDbContext`将新的数据库上下文用于每个测试，请`DbContextOptions`提供基于新服务提供程序的实例。</span><span class="sxs-lookup"><span data-stu-id="140fe-166">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="140fe-167">测试应用程序演示如何使用其`Utilities`类方法`TestDbContextOptions` （test */RazorPagesTestSample/实用工具/实用工具*）执行此操作：</span><span class="sxs-lookup"><span data-stu-id="140fe-167">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="140fe-168">在 DAL `DbContextOptions`单元测试中使用，允许每个测试以原子方式使用全新的数据库实例运行：</span><span class="sxs-lookup"><span data-stu-id="140fe-168">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="140fe-169">`DataAccessLayerTest`类（*run-unittests/DataAccessLayerTest*）中的每个测试方法都遵循类似的 "顺序" 操作-断言模式：</span><span class="sxs-lookup"><span data-stu-id="140fe-169">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="140fe-170">按为测试配置了数据库，并定义了预期的结果。</span><span class="sxs-lookup"><span data-stu-id="140fe-170">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="140fe-171">意义执行测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-171">Act: The test is executed.</span></span>
1. <span data-ttu-id="140fe-172">断言断言用于确定测试结果是否成功。</span><span class="sxs-lookup"><span data-stu-id="140fe-172">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="140fe-173">例如，该`DeleteMessageAsync`方法负责删除由其`Id` （*src/RazorPagesTestSample/Data/AppDbContext*）标识的单个消息：</span><span class="sxs-lookup"><span data-stu-id="140fe-173">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="140fe-174">此方法有两个测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-174">There are two tests for this method.</span></span> <span data-ttu-id="140fe-175">一个测试检查方法是在数据库中存在消息时删除一条消息。</span><span class="sxs-lookup"><span data-stu-id="140fe-175">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="140fe-176">另一种方法测试如果要删除的消息`Id`不存在，数据库不会更改。</span><span class="sxs-lookup"><span data-stu-id="140fe-176">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="140fe-177">此`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`方法如下所示：</span><span class="sxs-lookup"><span data-stu-id="140fe-177">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="140fe-178">首先，方法执行 "排列" 步骤，在该步骤中执行 Act 步骤。</span><span class="sxs-lookup"><span data-stu-id="140fe-178">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="140fe-179">获取并保存`seedMessages`种子设定消息。</span><span class="sxs-lookup"><span data-stu-id="140fe-179">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="140fe-180">播种消息会保存到数据库中。</span><span class="sxs-lookup"><span data-stu-id="140fe-180">The seeding messages are saved into the database.</span></span> <span data-ttu-id="140fe-181">设置为`Id`的`1`消息将被设置为删除。</span><span class="sxs-lookup"><span data-stu-id="140fe-181">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="140fe-182">执行方法时，预期的消息应包含除为`Id`的`1`消息之外的所有消息。 `DeleteMessageAsync`</span><span class="sxs-lookup"><span data-stu-id="140fe-182">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="140fe-183">`expectedMessages`变量表示此预期结果。</span><span class="sxs-lookup"><span data-stu-id="140fe-183">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="140fe-184">方法的作用：执行方法，并传入`recId`的`1`： `DeleteMessageAsync`</span><span class="sxs-lookup"><span data-stu-id="140fe-184">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="140fe-185">最后，方法`Messages`从上下文中获取，并将其`expectedMessages`与断言等于二者相等：</span><span class="sxs-lookup"><span data-stu-id="140fe-185">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="140fe-186">为了比较这两个`List<Message>`是否相同：</span><span class="sxs-lookup"><span data-stu-id="140fe-186">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="140fe-187">消息按`Id`排序。</span><span class="sxs-lookup"><span data-stu-id="140fe-187">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="140fe-188">在`Text`属性上比较消息对。</span><span class="sxs-lookup"><span data-stu-id="140fe-188">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="140fe-189">类似的测试方法`DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound`会检查尝试删除不存在的消息的结果。</span><span class="sxs-lookup"><span data-stu-id="140fe-189">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="140fe-190">在这种情况下，数据库中的预期消息应该等于执行`DeleteMessageAsync`方法后的实际消息。</span><span class="sxs-lookup"><span data-stu-id="140fe-190">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="140fe-191">不应更改数据库的内容：</span><span class="sxs-lookup"><span data-stu-id="140fe-191">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="140fe-192">页面模型方法的单元测试</span><span class="sxs-lookup"><span data-stu-id="140fe-192">Unit tests of the page model methods</span></span>

<span data-ttu-id="140fe-193">另一组单元测试负责页面模型方法的测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-193">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="140fe-194">在 message 应用中，索引页模型`IndexModel`位于*src/RazorPagesTestSample/Pages/* 类中。</span><span class="sxs-lookup"><span data-stu-id="140fe-194">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="140fe-195">页面模型方法</span><span class="sxs-lookup"><span data-stu-id="140fe-195">Page model method</span></span> | <span data-ttu-id="140fe-196">函数</span><span class="sxs-lookup"><span data-stu-id="140fe-196">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="140fe-197">使用`GetMessagesAsync`方法获取来自该 UI 的 DAL 的消息。</span><span class="sxs-lookup"><span data-stu-id="140fe-197">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="140fe-198">如果[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)有效，则调用`AddMessageAsync`将消息添加到数据库。</span><span class="sxs-lookup"><span data-stu-id="140fe-198">If the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="140fe-199">调用`DeleteAllMessagesAsync`以删除数据库中的所有消息。</span><span class="sxs-lookup"><span data-stu-id="140fe-199">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="140fe-200">执行`DeleteMessageAsync`以删除具有指定的`Id`消息。</span><span class="sxs-lookup"><span data-stu-id="140fe-200">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="140fe-201">如果数据库中有一条或多条消息，则计算每条消息的平均字数。</span><span class="sxs-lookup"><span data-stu-id="140fe-201">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="140fe-202">使用`IndexPageTests`类中的七个测试（*RazorPagesTestSample/run-unittests/IndexPageTests*）测试页模型方法。</span><span class="sxs-lookup"><span data-stu-id="140fe-202">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="140fe-203">这些测试使用熟悉的 "排列方式-法" 模式。</span><span class="sxs-lookup"><span data-stu-id="140fe-203">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="140fe-204">这些测试重点关注：</span><span class="sxs-lookup"><span data-stu-id="140fe-204">These tests focus on:</span></span>

* <span data-ttu-id="140fe-205">确定在[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)无效时，方法是否遵循正确的行为。</span><span class="sxs-lookup"><span data-stu-id="140fe-205">Determining if the methods follow the correct behavior when the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is invalid.</span></span>
* <span data-ttu-id="140fe-206">确认方法生成正确<xref:Microsoft.AspNetCore.Mvc.IActionResult>。</span><span class="sxs-lookup"><span data-stu-id="140fe-206">Confirming the methods produce the correct <xref:Microsoft.AspNetCore.Mvc.IActionResult>.</span></span>
* <span data-ttu-id="140fe-207">检查是否已正确赋值。</span><span class="sxs-lookup"><span data-stu-id="140fe-207">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="140fe-208">这组测试通常模拟 DAL 的方法，以便为执行页面模型方法的 Act 步骤生成所需的数据。</span><span class="sxs-lookup"><span data-stu-id="140fe-208">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="140fe-209">例如， `GetMessagesAsync`的`AppDbContext`方法是模拟，以生成输出。</span><span class="sxs-lookup"><span data-stu-id="140fe-209">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="140fe-210">当页面模型方法执行此方法时，mock 返回结果。</span><span class="sxs-lookup"><span data-stu-id="140fe-210">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="140fe-211">数据不来自数据库。</span><span class="sxs-lookup"><span data-stu-id="140fe-211">The data doesn't come from the database.</span></span> <span data-ttu-id="140fe-212">这会创建可预测、可靠的测试条件，以便在页面模型测试中使用 DAL。</span><span class="sxs-lookup"><span data-stu-id="140fe-212">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="140fe-213">该`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`测试显示`GetMessagesAsync`了方法对于页面模型是模拟的：</span><span class="sxs-lookup"><span data-stu-id="140fe-213">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="140fe-214">在 Act 步骤中执行`GetMessagesAsync` 方法时，它将调用页模型的方法。`OnGetAsync`</span><span class="sxs-lookup"><span data-stu-id="140fe-214">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="140fe-215">单元测试 Act 步骤（test */RazorPagesTestSample/run-unittests/IndexPageTests*）：</span><span class="sxs-lookup"><span data-stu-id="140fe-215">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="140fe-216">`IndexPage`页模型的`OnGetAsync`方法（*src/RazorPagesTestSample/Pages/* ）：</span><span class="sxs-lookup"><span data-stu-id="140fe-216">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="140fe-217">DAL `GetMessagesAsync`中的方法不会返回此方法调用的结果。</span><span class="sxs-lookup"><span data-stu-id="140fe-217">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="140fe-218">此方法的模拟版本返回结果。</span><span class="sxs-lookup"><span data-stu-id="140fe-218">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="140fe-219">在该`Assert`步骤中，将从页面`actualMessages`模型的`Messages`属性中指定实际的消息（）。</span><span class="sxs-lookup"><span data-stu-id="140fe-219">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="140fe-220">分配消息时也会执行类型检查。</span><span class="sxs-lookup"><span data-stu-id="140fe-220">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="140fe-221">预期的和实际的消息按其`Text`属性进行比较。</span><span class="sxs-lookup"><span data-stu-id="140fe-221">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="140fe-222">测试将断言两个`List<Message>`实例包含相同的消息。</span><span class="sxs-lookup"><span data-stu-id="140fe-222">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="140fe-223">此组中的其他测试<xref:Microsoft.AspNetCore.Http.DefaultHttpContext>创建包含<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> `PageContext` <xref:Microsoft.AspNetCore.Mvc.ActionContext>的页模型对象，以及用于建立、 `ViewDataDictionary`和的`PageContext`。</span><span class="sxs-lookup"><span data-stu-id="140fe-223">Other tests in this group create page model objects that include the <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, an <xref:Microsoft.AspNetCore.Mvc.ActionContext> to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="140fe-224">它们在执行测试时很有用。</span><span class="sxs-lookup"><span data-stu-id="140fe-224">These are useful in conducting tests.</span></span> <span data-ttu-id="140fe-225">例如，消息`ModelState`应用程序与一起<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>建立错误，以检查在执行时<xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> `OnPostAddMessageAsync`是否返回了有效的：</span><span class="sxs-lookup"><span data-stu-id="140fe-225">For example, the message app establishes a `ModelState` error with <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> to check that a valid <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="140fe-226">其他资源</span><span class="sxs-lookup"><span data-stu-id="140fe-226">Additional resources</span></span>

* [<span data-ttu-id="140fe-227">单元测试 C# 中使用 dotnet 测试和 xUnit 的.NET Core</span><span class="sxs-lookup"><span data-stu-id="140fe-227">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* <span data-ttu-id="140fe-228">[对代码进行单元测试](/visualstudio/test/unit-test-your-code)（Visual Studio）</span><span class="sxs-lookup"><span data-stu-id="140fe-228">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* <xref:test/integration-tests>
* [<span data-ttu-id="140fe-229">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="140fe-229">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="140fe-230">使用 Visual Studio for Mac 在 macOS 上构建完整的 .NET Core 解决方案</span><span class="sxs-lookup"><span data-stu-id="140fe-230">Building a complete .NET Core solution on macOS using Visual Studio for Mac</span></span>](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [<span data-ttu-id="140fe-231">XUnit.net 入门：通过 .NET SDK 命令行使用 .NET Core</span><span class="sxs-lookup"><span data-stu-id="140fe-231">Getting started with xUnit.net: Using .NET Core with the .NET SDK command line</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="140fe-232">Moq</span><span class="sxs-lookup"><span data-stu-id="140fe-232">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="140fe-233">Moq 快速入门</span><span class="sxs-lookup"><span data-stu-id="140fe-233">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="140fe-234">ASP.NET Core 支持 Razor 页应用的单元测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-234">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="140fe-235">数据访问层（DAL）和页面模型的测试有助于确保：</span><span class="sxs-lookup"><span data-stu-id="140fe-235">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="140fe-236">在应用程序构建过程中，Razor Pages 应用程序的各个部分将独立工作，并作为一个单元一起工作。</span><span class="sxs-lookup"><span data-stu-id="140fe-236">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="140fe-237">类和方法的责任范围有限。</span><span class="sxs-lookup"><span data-stu-id="140fe-237">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="140fe-238">在应用程序的行为方式上还存在其他文档。</span><span class="sxs-lookup"><span data-stu-id="140fe-238">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="140fe-239">回归是指在自动生成和部署过程中发现的代码更新导致的错误。</span><span class="sxs-lookup"><span data-stu-id="140fe-239">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="140fe-240">本主题假定你基本了解 Razor Pages 应用和单元测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-240">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="140fe-241">如果不熟悉 Razor Pages 应用或测试概念，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="140fe-241">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [<span data-ttu-id="140fe-242">单元测试 C# 中使用 dotnet 测试和 xUnit 的.NET Core</span><span class="sxs-lookup"><span data-stu-id="140fe-242">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="140fe-243">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="140fe-243">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="140fe-244">示例项目包含两个应用：</span><span class="sxs-lookup"><span data-stu-id="140fe-244">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="140fe-245">应用</span><span class="sxs-lookup"><span data-stu-id="140fe-245">App</span></span>         | <span data-ttu-id="140fe-246">项目文件夹</span><span class="sxs-lookup"><span data-stu-id="140fe-246">Project folder</span></span>                     | <span data-ttu-id="140fe-247">描述</span><span class="sxs-lookup"><span data-stu-id="140fe-247">Description</span></span> |
| ----------- | ---------------------------------- | ----------- |
| <span data-ttu-id="140fe-248">消息应用</span><span class="sxs-lookup"><span data-stu-id="140fe-248">Message app</span></span> | <span data-ttu-id="140fe-249">*src/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="140fe-249">*src/RazorPagesTestSample*</span></span>         | <span data-ttu-id="140fe-250">允许用户添加消息、删除一条消息、删除所有消息和分析消息（查找每条消息的平均单词数）。</span><span class="sxs-lookup"><span data-stu-id="140fe-250">Allows a user to add a message, delete one message, delete all messages, and analyze messages (find the average number of words per message).</span></span> |
| <span data-ttu-id="140fe-251">测试应用</span><span class="sxs-lookup"><span data-stu-id="140fe-251">Test app</span></span>    | <span data-ttu-id="140fe-252">*tests/RazorPagesTestSample.Tests*</span><span class="sxs-lookup"><span data-stu-id="140fe-252">*tests/RazorPagesTestSample.Tests*</span></span> | <span data-ttu-id="140fe-253">用于对消息应用的 DAL 和索引页模型进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-253">Used to unit test the DAL and Index page model of the message app.</span></span> |

<span data-ttu-id="140fe-254">可以使用 IDE 的内置测试功能（如[Visual Studio](/visualstudio/test/unit-test-your-code)或[Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)）运行测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-254">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](/visualstudio/test/unit-test-your-code) or [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span></span> <span data-ttu-id="140fe-255">如果使用[Visual Studio Code](https://code.visualstudio.com/)或命令行，请在 " *RazorPagesTestSample* " 文件夹中的命令提示符处执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="140fe-255">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="140fe-256">消息应用组织</span><span class="sxs-lookup"><span data-stu-id="140fe-256">Message app organization</span></span>

<span data-ttu-id="140fe-257">消息应用是 Razor Pages 的消息系统，具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="140fe-257">The message app is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="140fe-258">应用的 "索引" 页（*Pages/索引. cshtml*和*pages/* node.js）提供了一个 UI 和页面模型方法来控制消息的添加、删除和分析（查找每条消息的平均单词数）。</span><span class="sxs-lookup"><span data-stu-id="140fe-258">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (find the average number of words per message).</span></span>
* <span data-ttu-id="140fe-259">消息由`Message`类（*Data/message .cs*）描述，具有两个属性： `Id` （键）和`Text` （message）。</span><span class="sxs-lookup"><span data-stu-id="140fe-259">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="140fe-260">此`Text`属性是必需的，并且限制为200个字符。</span><span class="sxs-lookup"><span data-stu-id="140fe-260">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="140fe-261">使用[实体框架的内存中数据库](/ef/core/providers/in-memory/)&#8224;来存储消息。</span><span class="sxs-lookup"><span data-stu-id="140fe-261">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="140fe-262">应用程序在其数据库上下文类`AppDbContext` （*Data/AppDbContext*）中包含 DAL。</span><span class="sxs-lookup"><span data-stu-id="140fe-262">The app contains a DAL in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="140fe-263">DAL 方法被标记`virtual`为，这允许模拟方法在测试中使用。</span><span class="sxs-lookup"><span data-stu-id="140fe-263">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="140fe-264">如果数据库在应用启动时为空，则会用三条消息初始化消息存储。</span><span class="sxs-lookup"><span data-stu-id="140fe-264">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="140fe-265">这些*种子消息*还在测试中使用。</span><span class="sxs-lookup"><span data-stu-id="140fe-265">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="140fe-266">&#8224;EF 主题[使用 InMemory 进行测试](/ef/core/miscellaneous/testing/in-memory)说明了如何将内存中数据库用于使用 MSTest 进行测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-266">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="140fe-267">本主题使用[xUnit](https://xunit.github.io/)测试框架。</span><span class="sxs-lookup"><span data-stu-id="140fe-267">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="140fe-268">不同测试框架中的测试概念和测试实现相似，但并不完全相同。</span><span class="sxs-lookup"><span data-stu-id="140fe-268">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="140fe-269">尽管该示例应用程序不使用存储库模式，并且不是[工作单元（UoW）模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效示例，但 Razor Pages 支持这些模式的开发模式。</span><span class="sxs-lookup"><span data-stu-id="140fe-269">Although the sample app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="140fe-270">有关详细信息，请参阅[设计基础结构持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和<xref:mvc/controllers/testing> （示例实现存储库模式）。</span><span class="sxs-lookup"><span data-stu-id="140fe-270">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and <xref:mvc/controllers/testing> (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="140fe-271">测试应用组织</span><span class="sxs-lookup"><span data-stu-id="140fe-271">Test app organization</span></span>

<span data-ttu-id="140fe-272">测试应用是 "*测试/RazorPagesTestSample* " 文件夹中的控制台应用。</span><span class="sxs-lookup"><span data-stu-id="140fe-272">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="140fe-273">测试应用文件夹</span><span class="sxs-lookup"><span data-stu-id="140fe-273">Test app folder</span></span> | <span data-ttu-id="140fe-274">描述</span><span class="sxs-lookup"><span data-stu-id="140fe-274">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="140fe-275">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="140fe-275">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="140fe-276">*DataAccessLayerTest.cs*包含 DAL 的单元测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-276">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="140fe-277">*IndexPageTests.cs*包含索引页模型的单元测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-277">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="140fe-278">*公用*</span><span class="sxs-lookup"><span data-stu-id="140fe-278">*Utilities*</span></span>     | <span data-ttu-id="140fe-279">包含用于为每个 DAL 单元测试创建新数据库上下文选项，以便将数据库重置为每个测试的基线条件的方法。`TestDbContextOptions`</span><span class="sxs-lookup"><span data-stu-id="140fe-279">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="140fe-280">测试框架为[xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="140fe-280">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="140fe-281">对象模拟框架为[Moq](https://github.com/moq/moq4)。</span><span class="sxs-lookup"><span data-stu-id="140fe-281">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="140fe-282">数据访问层（DAL）的单元测试</span><span class="sxs-lookup"><span data-stu-id="140fe-282">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="140fe-283">消息应用有一个 DAL，其中包含包含在`AppDbContext`类中的四个方法（*src/RazorPagesTestSample/Data/AppDbContext*）。</span><span class="sxs-lookup"><span data-stu-id="140fe-283">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="140fe-284">每个方法都在测试应用程序中有一个或两个单元测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-284">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="140fe-285">DAL 方法</span><span class="sxs-lookup"><span data-stu-id="140fe-285">DAL method</span></span>               | <span data-ttu-id="140fe-286">函数</span><span class="sxs-lookup"><span data-stu-id="140fe-286">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="140fe-287">从按`Text`属性排序的数据库中获取。 `List<Message>`</span><span class="sxs-lookup"><span data-stu-id="140fe-287">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="140fe-288">将添加`Message`到数据库中。</span><span class="sxs-lookup"><span data-stu-id="140fe-288">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="140fe-289">删除数据库`Message`中的所有条目。</span><span class="sxs-lookup"><span data-stu-id="140fe-289">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="140fe-290">`Message` 删除`Id`数据库中的单个。</span><span class="sxs-lookup"><span data-stu-id="140fe-290">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="140fe-291">为每个测试创建新<xref:Microsoft.EntityFrameworkCore.DbContextOptions> `AppDbContext`的时，DAL 的单元测试需要。</span><span class="sxs-lookup"><span data-stu-id="140fe-291">Unit tests of the DAL require <xref:Microsoft.EntityFrameworkCore.DbContextOptions> when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="140fe-292">为每个测试创建`DbContextOptions`的一种方法是<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>使用：</span><span class="sxs-lookup"><span data-stu-id="140fe-292">One approach to creating the `DbContextOptions` for each test is to use a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="140fe-293">此方法的问题是，每个测试都接收到数据库，并将其保留在上一个测试的任何状态。</span><span class="sxs-lookup"><span data-stu-id="140fe-293">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="140fe-294">尝试编写不相互干扰的原子单元测试时，这可能会出现问题。</span><span class="sxs-lookup"><span data-stu-id="140fe-294">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="140fe-295">若要强制`AppDbContext`将新的数据库上下文用于每个测试，请`DbContextOptions`提供基于新服务提供程序的实例。</span><span class="sxs-lookup"><span data-stu-id="140fe-295">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="140fe-296">测试应用程序演示如何使用其`Utilities`类方法`TestDbContextOptions` （test */RazorPagesTestSample/实用工具/实用工具*）执行此操作：</span><span class="sxs-lookup"><span data-stu-id="140fe-296">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="140fe-297">在 DAL `DbContextOptions`单元测试中使用，允许每个测试以原子方式使用全新的数据库实例运行：</span><span class="sxs-lookup"><span data-stu-id="140fe-297">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="140fe-298">`DataAccessLayerTest`类（*run-unittests/DataAccessLayerTest*）中的每个测试方法都遵循类似的 "顺序" 操作-断言模式：</span><span class="sxs-lookup"><span data-stu-id="140fe-298">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="140fe-299">按为测试配置了数据库，并定义了预期的结果。</span><span class="sxs-lookup"><span data-stu-id="140fe-299">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="140fe-300">意义执行测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-300">Act: The test is executed.</span></span>
1. <span data-ttu-id="140fe-301">断言断言用于确定测试结果是否成功。</span><span class="sxs-lookup"><span data-stu-id="140fe-301">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="140fe-302">例如，该`DeleteMessageAsync`方法负责删除由其`Id` （*src/RazorPagesTestSample/Data/AppDbContext*）标识的单个消息：</span><span class="sxs-lookup"><span data-stu-id="140fe-302">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="140fe-303">此方法有两个测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-303">There are two tests for this method.</span></span> <span data-ttu-id="140fe-304">一个测试检查方法是在数据库中存在消息时删除一条消息。</span><span class="sxs-lookup"><span data-stu-id="140fe-304">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="140fe-305">另一种方法测试如果要删除的消息`Id`不存在，数据库不会更改。</span><span class="sxs-lookup"><span data-stu-id="140fe-305">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="140fe-306">此`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`方法如下所示：</span><span class="sxs-lookup"><span data-stu-id="140fe-306">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="140fe-307">首先，方法执行 "排列" 步骤，在该步骤中执行 Act 步骤。</span><span class="sxs-lookup"><span data-stu-id="140fe-307">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="140fe-308">获取并保存`seedMessages`种子设定消息。</span><span class="sxs-lookup"><span data-stu-id="140fe-308">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="140fe-309">播种消息会保存到数据库中。</span><span class="sxs-lookup"><span data-stu-id="140fe-309">The seeding messages are saved into the database.</span></span> <span data-ttu-id="140fe-310">设置为`Id`的`1`消息将被设置为删除。</span><span class="sxs-lookup"><span data-stu-id="140fe-310">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="140fe-311">执行方法时，预期的消息应包含除为`Id`的`1`消息之外的所有消息。 `DeleteMessageAsync`</span><span class="sxs-lookup"><span data-stu-id="140fe-311">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="140fe-312">`expectedMessages`变量表示此预期结果。</span><span class="sxs-lookup"><span data-stu-id="140fe-312">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="140fe-313">方法的作用：执行方法，并传入`recId`的`1`： `DeleteMessageAsync`</span><span class="sxs-lookup"><span data-stu-id="140fe-313">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="140fe-314">最后，方法`Messages`从上下文中获取，并将其`expectedMessages`与断言等于二者相等：</span><span class="sxs-lookup"><span data-stu-id="140fe-314">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="140fe-315">为了比较这两个`List<Message>`是否相同：</span><span class="sxs-lookup"><span data-stu-id="140fe-315">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="140fe-316">消息按`Id`排序。</span><span class="sxs-lookup"><span data-stu-id="140fe-316">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="140fe-317">在`Text`属性上比较消息对。</span><span class="sxs-lookup"><span data-stu-id="140fe-317">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="140fe-318">类似的测试方法`DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound`会检查尝试删除不存在的消息的结果。</span><span class="sxs-lookup"><span data-stu-id="140fe-318">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="140fe-319">在这种情况下，数据库中的预期消息应该等于执行`DeleteMessageAsync`方法后的实际消息。</span><span class="sxs-lookup"><span data-stu-id="140fe-319">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="140fe-320">不应更改数据库的内容：</span><span class="sxs-lookup"><span data-stu-id="140fe-320">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="140fe-321">页面模型方法的单元测试</span><span class="sxs-lookup"><span data-stu-id="140fe-321">Unit tests of the page model methods</span></span>

<span data-ttu-id="140fe-322">另一组单元测试负责页面模型方法的测试。</span><span class="sxs-lookup"><span data-stu-id="140fe-322">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="140fe-323">在 message 应用中，索引页模型`IndexModel`位于*src/RazorPagesTestSample/Pages/* 类中。</span><span class="sxs-lookup"><span data-stu-id="140fe-323">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="140fe-324">页面模型方法</span><span class="sxs-lookup"><span data-stu-id="140fe-324">Page model method</span></span> | <span data-ttu-id="140fe-325">函数</span><span class="sxs-lookup"><span data-stu-id="140fe-325">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="140fe-326">使用`GetMessagesAsync`方法获取来自该 UI 的 DAL 的消息。</span><span class="sxs-lookup"><span data-stu-id="140fe-326">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="140fe-327">如果[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)有效，则调用`AddMessageAsync`将消息添加到数据库。</span><span class="sxs-lookup"><span data-stu-id="140fe-327">If the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="140fe-328">调用`DeleteAllMessagesAsync`以删除数据库中的所有消息。</span><span class="sxs-lookup"><span data-stu-id="140fe-328">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="140fe-329">执行`DeleteMessageAsync`以删除具有指定的`Id`消息。</span><span class="sxs-lookup"><span data-stu-id="140fe-329">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="140fe-330">如果数据库中有一条或多条消息，则计算每条消息的平均字数。</span><span class="sxs-lookup"><span data-stu-id="140fe-330">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="140fe-331">使用`IndexPageTests`类中的七个测试（*RazorPagesTestSample/run-unittests/IndexPageTests*）测试页模型方法。</span><span class="sxs-lookup"><span data-stu-id="140fe-331">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="140fe-332">这些测试使用熟悉的 "排列方式-法" 模式。</span><span class="sxs-lookup"><span data-stu-id="140fe-332">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="140fe-333">这些测试重点关注：</span><span class="sxs-lookup"><span data-stu-id="140fe-333">These tests focus on:</span></span>

* <span data-ttu-id="140fe-334">确定在[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)无效时，方法是否遵循正确的行为。</span><span class="sxs-lookup"><span data-stu-id="140fe-334">Determining if the methods follow the correct behavior when the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is invalid.</span></span>
* <span data-ttu-id="140fe-335">确认方法生成正确<xref:Microsoft.AspNetCore.Mvc.IActionResult>。</span><span class="sxs-lookup"><span data-stu-id="140fe-335">Confirming the methods produce the correct <xref:Microsoft.AspNetCore.Mvc.IActionResult>.</span></span>
* <span data-ttu-id="140fe-336">检查是否已正确赋值。</span><span class="sxs-lookup"><span data-stu-id="140fe-336">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="140fe-337">这组测试通常模拟 DAL 的方法，以便为执行页面模型方法的 Act 步骤生成所需的数据。</span><span class="sxs-lookup"><span data-stu-id="140fe-337">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="140fe-338">例如， `GetMessagesAsync`的`AppDbContext`方法是模拟，以生成输出。</span><span class="sxs-lookup"><span data-stu-id="140fe-338">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="140fe-339">当页面模型方法执行此方法时，mock 返回结果。</span><span class="sxs-lookup"><span data-stu-id="140fe-339">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="140fe-340">数据不来自数据库。</span><span class="sxs-lookup"><span data-stu-id="140fe-340">The data doesn't come from the database.</span></span> <span data-ttu-id="140fe-341">这会创建可预测、可靠的测试条件，以便在页面模型测试中使用 DAL。</span><span class="sxs-lookup"><span data-stu-id="140fe-341">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="140fe-342">该`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`测试显示`GetMessagesAsync`了方法对于页面模型是模拟的：</span><span class="sxs-lookup"><span data-stu-id="140fe-342">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="140fe-343">在 Act 步骤中执行`GetMessagesAsync` 方法时，它将调用页模型的方法。`OnGetAsync`</span><span class="sxs-lookup"><span data-stu-id="140fe-343">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="140fe-344">单元测试 Act 步骤（test */RazorPagesTestSample/run-unittests/IndexPageTests*）：</span><span class="sxs-lookup"><span data-stu-id="140fe-344">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="140fe-345">`IndexPage`页模型的`OnGetAsync`方法（*src/RazorPagesTestSample/Pages/* ）：</span><span class="sxs-lookup"><span data-stu-id="140fe-345">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="140fe-346">DAL `GetMessagesAsync`中的方法不会返回此方法调用的结果。</span><span class="sxs-lookup"><span data-stu-id="140fe-346">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="140fe-347">此方法的模拟版本返回结果。</span><span class="sxs-lookup"><span data-stu-id="140fe-347">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="140fe-348">在该`Assert`步骤中，将从页面`actualMessages`模型的`Messages`属性中指定实际的消息（）。</span><span class="sxs-lookup"><span data-stu-id="140fe-348">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="140fe-349">分配消息时也会执行类型检查。</span><span class="sxs-lookup"><span data-stu-id="140fe-349">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="140fe-350">预期的和实际的消息按其`Text`属性进行比较。</span><span class="sxs-lookup"><span data-stu-id="140fe-350">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="140fe-351">测试将断言两个`List<Message>`实例包含相同的消息。</span><span class="sxs-lookup"><span data-stu-id="140fe-351">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="140fe-352">此组中的其他测试<xref:Microsoft.AspNetCore.Http.DefaultHttpContext>创建包含<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> `PageContext` <xref:Microsoft.AspNetCore.Mvc.ActionContext>的页模型对象，以及用于建立、 `ViewDataDictionary`和的`PageContext`。</span><span class="sxs-lookup"><span data-stu-id="140fe-352">Other tests in this group create page model objects that include the <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, an <xref:Microsoft.AspNetCore.Mvc.ActionContext> to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="140fe-353">它们在执行测试时很有用。</span><span class="sxs-lookup"><span data-stu-id="140fe-353">These are useful in conducting tests.</span></span> <span data-ttu-id="140fe-354">例如，消息`ModelState`应用程序与一起<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>建立错误，以检查在执行时<xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> `OnPostAddMessageAsync`是否返回了有效的：</span><span class="sxs-lookup"><span data-stu-id="140fe-354">For example, the message app establishes a `ModelState` error with <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> to check that a valid <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="140fe-355">其他资源</span><span class="sxs-lookup"><span data-stu-id="140fe-355">Additional resources</span></span>

* [<span data-ttu-id="140fe-356">单元测试 C# 中使用 dotnet 测试和 xUnit 的.NET Core</span><span class="sxs-lookup"><span data-stu-id="140fe-356">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* <span data-ttu-id="140fe-357">[对代码进行单元测试](/visualstudio/test/unit-test-your-code)（Visual Studio）</span><span class="sxs-lookup"><span data-stu-id="140fe-357">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* <xref:test/integration-tests>
* [<span data-ttu-id="140fe-358">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="140fe-358">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="140fe-359">使用 Visual Studio for Mac 在 macOS 上构建完整的 .NET Core 解决方案</span><span class="sxs-lookup"><span data-stu-id="140fe-359">Building a complete .NET Core solution on macOS using Visual Studio for Mac</span></span>](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [<span data-ttu-id="140fe-360">XUnit.net 入门：通过 .NET SDK 命令行使用 .NET Core</span><span class="sxs-lookup"><span data-stu-id="140fe-360">Getting started with xUnit.net: Using .NET Core with the .NET SDK command line</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="140fe-361">Moq</span><span class="sxs-lookup"><span data-stu-id="140fe-361">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="140fe-362">Moq 快速入门</span><span class="sxs-lookup"><span data-stu-id="140fe-362">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end
