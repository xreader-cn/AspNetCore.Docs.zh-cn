---
uid: aspnet/overview/owin-and-katana/owin-startup-class-detection
title: OWIN 启动类检测 |Microsoft Docs
author: Praburaj
description: 本教程演示如何配置加载的 OWIN 启动类。 OWIN 的详细信息，请参阅项目 Katana 概述。 本教程是...
ms.author: riande
ms.date: 01/28/2019
ms.assetid: 08257f55-36f4-4e39-9c88-2a5602838c79
msc.legacyurl: /aspnet/overview/owin-and-katana/owin-startup-class-detection
msc.type: authoredcontent
ms.openlocfilehash: 0b34cca8b48383dbb028106651758dff889ed614
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667292"
---
<a name="owin-startup-class-detection"></a><span data-ttu-id="be50d-105">OWIN 启动类检测</span><span class="sxs-lookup"><span data-stu-id="be50d-105">OWIN Startup Class Detection</span></span>
====================

> <span data-ttu-id="be50d-106">本教程演示如何配置加载的 OWIN 启动类。</span><span class="sxs-lookup"><span data-stu-id="be50d-106">This tutorial shows how to configure which OWIN startup class is loaded.</span></span> <span data-ttu-id="be50d-107">OWIN 的详细信息，请参阅[项目 Katana 概述](an-overview-of-project-katana.md)。</span><span class="sxs-lookup"><span data-stu-id="be50d-107">For more information on OWIN, see [An Overview of Project Katana](an-overview-of-project-katana.md).</span></span> <span data-ttu-id="be50d-108">本教程由 Rick Anderson 编写 ( [ @RickAndMSFT ](https://twitter.com/#!/RickAndMSFT) )，Praburaj Thiagarajan 和 Howard Dierking ( [ @howard \_dierking](https://twitter.com/howard_dierking) )。</span><span class="sxs-lookup"><span data-stu-id="be50d-108">This tutorial was written by Rick Anderson ( [@RickAndMSFT](https://twitter.com/#!/RickAndMSFT) ), Praburaj Thiagarajan, and Howard Dierking ( [@howard\_dierking](https://twitter.com/howard_dierking) ).</span></span>
>
> ## <a name="prerequisites"></a><span data-ttu-id="be50d-109">系统必备</span><span class="sxs-lookup"><span data-stu-id="be50d-109">Prerequisites</span></span>
>
> [<span data-ttu-id="be50d-110">Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="be50d-110">Visual Studio 2017</span></span>](https://visualstudio.microsoft.com/downloads/)


## <a name="owin-startup-class-detection"></a><span data-ttu-id="be50d-111">OWIN 启动类检测</span><span class="sxs-lookup"><span data-stu-id="be50d-111">OWIN Startup Class Detection</span></span>

 <span data-ttu-id="be50d-112">每个 OWIN 应用程序有一个 startup 类在其中指定的应用程序管道组件。</span><span class="sxs-lookup"><span data-stu-id="be50d-112">Every OWIN Application has a startup class where you specify components for the application pipeline.</span></span> <span data-ttu-id="be50d-113">通过不同的方式可以与运行时连接 startup 类，具体取决于宿主模型选择 （OwinHost、 IIS 和 IIS Express）。</span><span class="sxs-lookup"><span data-stu-id="be50d-113">There are different ways you can connect your startup class with the runtime, depending on the hosting model you choose (OwinHost, IIS, and IIS-Express).</span></span> <span data-ttu-id="be50d-114">在本教程中所示的启动类可在每个托管应用程序。</span><span class="sxs-lookup"><span data-stu-id="be50d-114">The startup class shown in this tutorial can be used in every hosting application.</span></span> <span data-ttu-id="be50d-115">使用托管运行时使用其中一种方法连接 startup 类：</span><span class="sxs-lookup"><span data-stu-id="be50d-115">You connect the startup class with the hosting runtime using one of the these approaches:</span></span>

1. <span data-ttu-id="be50d-116">**命名约定**:Katana 寻找一个名为类`Startup`匹配的程序集名称或全局命名空间的命名空间中。</span><span class="sxs-lookup"><span data-stu-id="be50d-116">**Naming Convention**: Katana looks for a class named `Startup` in namespace matching the assembly name or the global namespace.</span></span>
2. <span data-ttu-id="be50d-117">**OwinStartup 属性**:这是大多数开发人员将采用指定 startup 类的方法。</span><span class="sxs-lookup"><span data-stu-id="be50d-117">**OwinStartup Attribute**: This is the approach most developers will take to specify the startup class.</span></span> <span data-ttu-id="be50d-118">以下属性将设置为 startup 类`TestStartup`类中`StartupDemo`命名空间。</span><span class="sxs-lookup"><span data-stu-id="be50d-118">The following attribute will set the startup class to the `TestStartup` class in the `StartupDemo` namespace.</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample1.cs)]

   <span data-ttu-id="be50d-119">`OwinStartup`特性将重写的命名约定。</span><span class="sxs-lookup"><span data-stu-id="be50d-119">The `OwinStartup` attribute overrides the naming convention.</span></span> <span data-ttu-id="be50d-120">此外可以使用此属性指定一个友好名称，但是，使用的友好名称要求您也使用`appSetting`配置文件中的元素。</span><span class="sxs-lookup"><span data-stu-id="be50d-120">You can also specify a friendly name with this attribute, however, using a friendly name requires you to also use the `appSetting` element in the configuration file.</span></span>
3. <span data-ttu-id="be50d-121">**配置文件中的 appSetting 元素**:`appSetting`元素会替代`OwinStartup`属性和命名约定。</span><span class="sxs-lookup"><span data-stu-id="be50d-121">**The appSetting element in the Configuration file**: The `appSetting` element overrides the `OwinStartup` attribute and naming convention.</span></span> <span data-ttu-id="be50d-122">可以有多个启动类 (每个使用`OwinStartup`属性) 和配置将使用标记类似于下面的配置文件中加载的 startup 类：</span><span class="sxs-lookup"><span data-stu-id="be50d-122">You can have multiple startup classes (each using an `OwinStartup` attribute) and configure which startup class will be loaded in a configuration file using markup similar to the following:</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample2.xml)]

   <span data-ttu-id="be50d-123">此外可以使用显式指定的启动类和程序集的以下项：</span><span class="sxs-lookup"><span data-stu-id="be50d-123">The following key, which explicitly specifies the startup class and assembly can also be used:</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample3.xml)]

   <span data-ttu-id="be50d-124">下面的 XML 配置文件中指定的友好启动类名称`ProductionConfiguration`。</span><span class="sxs-lookup"><span data-stu-id="be50d-124">The following XML in the configuration file specifies a friendly startup class name of `ProductionConfiguration`.</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample4.xml)]

   <span data-ttu-id="be50d-125">上述标记都必须使用以下`OwinStartup`属性指定一个友好名称，并会导致`ProductionStartup2`类来运行。</span><span class="sxs-lookup"><span data-stu-id="be50d-125">The above markup must be used with the following `OwinStartup` attribute which specifies a friendly name and causes the `ProductionStartup2` class to run.</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample5.cs?highlight=1,16)]
4. <span data-ttu-id="be50d-126">若要禁用 OWIN 启动发现，请添加`appSetting owin:AutomaticAppStartup`值为`"false"`web.config 文件中。</span><span class="sxs-lookup"><span data-stu-id="be50d-126">To disable OWIN startup discovery add the `appSetting owin:AutomaticAppStartup` with a value of `"false"` in the web.config file.</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample6.xml)]

## <a name="create-an-aspnet-web-app-using-owin-startup"></a><span data-ttu-id="be50d-127">创建 ASP.NET Web 应用使用 OWIN Startup</span><span class="sxs-lookup"><span data-stu-id="be50d-127">Create an ASP.NET Web App using OWIN Startup</span></span>

1. <span data-ttu-id="be50d-128">创建空的 Asp.Net web 应用程序并将其命名**StartupDemo**。</span><span class="sxs-lookup"><span data-stu-id="be50d-128">Create an empty Asp.Net web application and name it **StartupDemo**.</span></span> <span data-ttu-id="be50d-129">-安装`Microsoft.Owin.Host.SystemWeb`使用 NuGet 包管理器。</span><span class="sxs-lookup"><span data-stu-id="be50d-129">- Install `Microsoft.Owin.Host.SystemWeb` using the NuGet package manager.</span></span> <span data-ttu-id="be50d-130">从**工具**菜单中，选择**NuGet 包管理器**，然后**程序包管理器控制台**。</span><span class="sxs-lookup"><span data-stu-id="be50d-130">From the **Tools** menu, select **NuGet Package Manager**, and then **Package Manager Console**.</span></span> <span data-ttu-id="be50d-131">输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="be50d-131">Enter the following command:</span></span>

    [!code-powershell[Main](owin-startup-class-detection/samples/sample7.ps1)]
2. <span data-ttu-id="be50d-132">添加 OWIN 启动类。</span><span class="sxs-lookup"><span data-stu-id="be50d-132">Add an OWIN startup class.</span></span> <span data-ttu-id="be50d-133">在 Visual Studio 2017 中，右键单击项目，然后选择**添加类**。-在**添加新项**对话框框中，输入*OWIN*中搜索字段中，并将名称更改为 Startup.cs，然后选择**添加**。</span><span class="sxs-lookup"><span data-stu-id="be50d-133">In Visual Studio 2017 right-click the project and select **Add Class**.- In the **Add New Item** dialog box, enter *OWIN* in the search field, and change the name to Startup.cs, and then select **Add**.</span></span>

     ![](owin-startup-class-detection/_static/image1.png)

   <span data-ttu-id="be50d-134">你想要添加的下一步时间*Owin 启动类*，则将为可从**添加**菜单。</span><span class="sxs-lookup"><span data-stu-id="be50d-134">The next time you want to add an *Owin Startup class*, it will be in available from the **Add** menu.</span></span>

     ![](owin-startup-class-detection/_static/image2.png)

   <span data-ttu-id="be50d-135">或者，可以右键单击该项目并选择**外**，然后选择**新项**，然后选择**Owin 启动类**。</span><span class="sxs-lookup"><span data-stu-id="be50d-135">Alternatively, you can right-click the project and select **Add**, then select **New Item**, and then select the **Owin Startup class**.</span></span>

     ![](owin-startup-class-detection/_static/image3.png)

- <span data-ttu-id="be50d-136">替换中生成的代码*Startup.cs*具有以下文件：</span><span class="sxs-lookup"><span data-stu-id="be50d-136">Replace the generated code in the *Startup.cs* file with the following:</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample8.cs?highlight=5,7,15-28,31-34)]

  <span data-ttu-id="be50d-137">`app.Use` Lambda 表达式用于注册指定的中间件组件到 OWIN 管道。</span><span class="sxs-lookup"><span data-stu-id="be50d-137">The `app.Use` lambda expression is used to register the specified middleware component to the OWIN pipeline.</span></span> <span data-ttu-id="be50d-138">在这种情况下，我们正在设置之前响应传入请求的传入请求的日志记录。</span><span class="sxs-lookup"><span data-stu-id="be50d-138">In this case we are setting up logging of incoming requests before responding to the incoming request.</span></span> <span data-ttu-id="be50d-139">`next`参数是委托 ( [Func](https://msdn.microsoft.com/library/bb534960(v=vs.100).aspx) &lt; [任务](https://msdn.microsoft.com/library/dd321424(v=vs.100).aspx) &gt; ) 到管道中的下一个组件。</span><span class="sxs-lookup"><span data-stu-id="be50d-139">The `next` parameter is the delegate ( [Func](https://msdn.microsoft.com/library/bb534960(v=vs.100).aspx) &lt; [Task](https://msdn.microsoft.com/library/dd321424(v=vs.100).aspx) &gt; ) to the next component in the pipeline.</span></span> <span data-ttu-id="be50d-140">`app.Run` Lambda 表达式挂接到传入的请求管道，并提供响应机制。</span><span class="sxs-lookup"><span data-stu-id="be50d-140">The `app.Run` lambda expression hooks up the pipeline to incoming requests and provides the response mechanism.</span></span>
     > [!NOTE]
     > <span data-ttu-id="be50d-141">在上面的代码中我们注释掉`OwinStartup`属性，我们要依赖于正在运行的名为的类的约定`Startup`。-按***F5***运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="be50d-141">In the code above we have commented out the `OwinStartup` attribute and we're relying on the convention of running the class named `Startup` .- Press ***F5*** to run the application.</span></span> <span data-ttu-id="be50d-142">命中刷新几次。</span><span class="sxs-lookup"><span data-stu-id="be50d-142">Hit refresh a few times.</span></span>

    <span data-ttu-id="be50d-143">![](owin-startup-class-detection/_static/image4.png) 注意：在本教程中显示图像中的数字将看到的数字不匹配。</span><span class="sxs-lookup"><span data-stu-id="be50d-143">![](owin-startup-class-detection/_static/image4.png) Note: The number shown in the images in this tutorial will not match the number you see.</span></span> <span data-ttu-id="be50d-144">毫秒字符串用于刷新页面时显示新的响应。</span><span class="sxs-lookup"><span data-stu-id="be50d-144">The millisecond string is used to show a new response when you refresh the page.</span></span>
  <span data-ttu-id="be50d-145">可以看到中的跟踪信息**输出**窗口。</span><span class="sxs-lookup"><span data-stu-id="be50d-145">You can see the trace information in the **Output** window.</span></span>

    ![](owin-startup-class-detection/_static/image5.png)

## <a name="add-more-startup-classes"></a><span data-ttu-id="be50d-146">添加更多的启动类</span><span class="sxs-lookup"><span data-stu-id="be50d-146">Add More Startup Classes</span></span>

<span data-ttu-id="be50d-147">在本部分中，我们将添加另一个 Startup 类。</span><span class="sxs-lookup"><span data-stu-id="be50d-147">In this section we'll add another Startup class.</span></span> <span data-ttu-id="be50d-148">可以将多个 OWIN 启动类添加到你的应用程序。</span><span class="sxs-lookup"><span data-stu-id="be50d-148">You can add multiple OWIN startup class to your application.</span></span> <span data-ttu-id="be50d-149">例如，你可能想要创建用于开发、 测试和生产环境的启动类。</span><span class="sxs-lookup"><span data-stu-id="be50d-149">For example, you might want to create startup classes for development, testing and production.</span></span>

1. <span data-ttu-id="be50d-150">创建一个新的 OWIN 启动类并将其命名`ProductionStartup`。</span><span class="sxs-lookup"><span data-stu-id="be50d-150">Create a new OWIN Startup class and name it `ProductionStartup`.</span></span>
2. <span data-ttu-id="be50d-151">将生成的代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="be50d-151">Replace the generated code with the following:</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample9.cs?highlight=14-18)]
3. <span data-ttu-id="be50d-152">按控件 F5 以运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="be50d-152">Press Control F5 to run the app.</span></span> <span data-ttu-id="be50d-153">`OwinStartup`属性指定运行生产 startup 类。</span><span class="sxs-lookup"><span data-stu-id="be50d-153">The `OwinStartup` attribute specifies the production startup class is run.</span></span>

    ![](owin-startup-class-detection/_static/image6.png)
4. <span data-ttu-id="be50d-154">创建另一个 OWIN 启动类并将其命名`TestStartup`。</span><span class="sxs-lookup"><span data-stu-id="be50d-154">Create another OWIN Startup class and name it `TestStartup`.</span></span>
5. <span data-ttu-id="be50d-155">将生成的代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="be50d-155">Replace the generated code with the following:</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample10.cs?highlight=6,14-18)]

   <span data-ttu-id="be50d-156">`OwinStartup`上述属性重载指定`TestingConfiguration`作为*友好*Startup 类的名称。</span><span class="sxs-lookup"><span data-stu-id="be50d-156">The `OwinStartup` attribute overload above specifies `TestingConfiguration` as the *friendly* name of the Startup class.</span></span>
6. <span data-ttu-id="be50d-157">打开*web.config*文件，并添加 OWIN 应用程序启动密钥，它指定在启动类的友好名称：</span><span class="sxs-lookup"><span data-stu-id="be50d-157">Open the *web.config* file and add the OWIN App startup key which specifies the friendly name of the Startup class:</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample11.xml?highlight=3-5)]
7. <span data-ttu-id="be50d-158">按控件 F5 以运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="be50d-158">Press Control F5 to run the app.</span></span> <span data-ttu-id="be50d-159">应用设置元素采用引用单元格，并在测试运行配置。</span><span class="sxs-lookup"><span data-stu-id="be50d-159">The app settings element takes precedent, and the test configuration is run.</span></span>

    ![](owin-startup-class-detection/_static/image7.png)
8. <span data-ttu-id="be50d-160">删除*友好*名称`OwinStartup`属性中`TestStartup`类。</span><span class="sxs-lookup"><span data-stu-id="be50d-160">Remove the *friendly* name from the `OwinStartup` attribute in the `TestStartup` class.</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample12.cs)]
9. <span data-ttu-id="be50d-161">替换中的 OWIN 应用程序启动密钥*web.config*具有以下文件：</span><span class="sxs-lookup"><span data-stu-id="be50d-161">Replace the OWIN App startup key in the *web.config* file with the following:</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample13.xml)]
10. <span data-ttu-id="be50d-162">还原`OwinStartup`中对默认属性代码 Visual Studio 生成的每个类属性：</span><span class="sxs-lookup"><span data-stu-id="be50d-162">Revert the `OwinStartup` attribute in each class to the default attribute code generated by Visual Studio:</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample14.cs)]

    <span data-ttu-id="be50d-163">OWIN 应用程序的启动密钥下的每个将导致生产类来运行。</span><span class="sxs-lookup"><span data-stu-id="be50d-163">Each of the OWIN App startup keys below will cause the production class to run.</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample15.xml)]

    <span data-ttu-id="be50d-164">最后一个的启动密钥指定的启动配置方法。</span><span class="sxs-lookup"><span data-stu-id="be50d-164">The last startup key specifies the startup configuration method.</span></span> <span data-ttu-id="be50d-165">以下的 OWIN 应用程序启动密钥，可更改到的配置类的名称`MyConfiguration`。</span><span class="sxs-lookup"><span data-stu-id="be50d-165">The following OWIN App startup key allows you to change the name of the configuration class to `MyConfiguration` .</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample16.xml)]

## <a name="using-owinhostexe"></a><span data-ttu-id="be50d-166">使用 Owinhost.exe</span><span class="sxs-lookup"><span data-stu-id="be50d-166">Using Owinhost.exe</span></span>

1. <span data-ttu-id="be50d-167">使用以下标记替换 Web.config 文件：</span><span class="sxs-lookup"><span data-stu-id="be50d-167">Replace the Web.config file with the following markup:</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample17.xml?highlight=3-6)]

   <span data-ttu-id="be50d-168">最后一个键 wins，因此，在这种情况下`TestStartup`指定。</span><span class="sxs-lookup"><span data-stu-id="be50d-168">The last key wins, so in this case `TestStartup` is specified.</span></span>
2. <span data-ttu-id="be50d-169">从 PMC 安装 Owinhost:</span><span class="sxs-lookup"><span data-stu-id="be50d-169">Install Owinhost from the PMC:</span></span>

    [!code-console[Main](owin-startup-class-detection/samples/sample18.cmd)]
3. <span data-ttu-id="be50d-170">导航到应用程序文件夹 (包含的文件夹*Web.config*文件) 并在命令提示符并键入：</span><span class="sxs-lookup"><span data-stu-id="be50d-170">Navigate to the application folder (the folder containing the *Web.config* file) and in a command prompt and type:</span></span>

    [!code-console[Main](owin-startup-class-detection/samples/sample19.cmd)]

   <span data-ttu-id="be50d-171">命令窗口将显示：</span><span class="sxs-lookup"><span data-stu-id="be50d-171">The command window will show:</span></span>

    [!code-console[Main](owin-startup-class-detection/samples/sample20.cmd)]
4. <span data-ttu-id="be50d-172">启动浏览器的 url `http://localhost:5000/`。</span><span class="sxs-lookup"><span data-stu-id="be50d-172">Launch a browser with the URL `http://localhost:5000/`.</span></span>

    ![](owin-startup-class-detection/_static/image8.png)

   <span data-ttu-id="be50d-173">OwinHost 遵循上面列出的启动约定。</span><span class="sxs-lookup"><span data-stu-id="be50d-173">OwinHost honored the startup conventions listed above.</span></span>
5. <span data-ttu-id="be50d-174">在命令窗口中，按 Enter 退出 OwinHost。</span><span class="sxs-lookup"><span data-stu-id="be50d-174">In the command window, press Enter to exit OwinHost.</span></span>
6. <span data-ttu-id="be50d-175">在中`ProductionStartup`类中，添加以下 OwinStartup 特性指定的友好名称*ProductionConfiguration*。</span><span class="sxs-lookup"><span data-stu-id="be50d-175">In the `ProductionStartup` class, add the following OwinStartup attribute which specifies a friendly name of *ProductionConfiguration*.</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample21.cs)]
7. <span data-ttu-id="be50d-176">在命令提示符并键入：</span><span class="sxs-lookup"><span data-stu-id="be50d-176">In the command prompt and type:</span></span>

    [!code-console[Main](owin-startup-class-detection/samples/sample22.cmd)]

   <span data-ttu-id="be50d-177">加载生产 startup 类。</span><span class="sxs-lookup"><span data-stu-id="be50d-177">The Production startup class is loaded.</span></span>
    ![](owin-startup-class-detection/_static/image9.png)

   <span data-ttu-id="be50d-178">我们的应用程序具有多个启动类，并在此示例中我们具有延迟到运行时加载的 startup 类。</span><span class="sxs-lookup"><span data-stu-id="be50d-178">Our application has multiple startup classes, and in this example we have deferred which startup class to load until runtime.</span></span>
8. <span data-ttu-id="be50d-179">测试以下运行时启动选项：</span><span class="sxs-lookup"><span data-stu-id="be50d-179">Test the following runtime startup options:</span></span>

    [!code-console[Main](owin-startup-class-detection/samples/sample23.cmd)]
