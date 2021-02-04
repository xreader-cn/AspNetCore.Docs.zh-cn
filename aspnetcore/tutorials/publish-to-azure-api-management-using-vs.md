---
title: 使用 Visual Studio 将 ASP.NET Core Web API 发布到 Azure API 管理
author: codemillmatt
description: 了解如何使用 Visual Studio 将 ASP.NET Core Web API 发布到 Azure API 管理。
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 11/22/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: ddff54bbd146c98cf83a865910401df26e7ac4ec
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217578"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a><span data-ttu-id="7542d-103">使用 Visual Studio 将 ASP.NET Core Web API 发布到 Azure API 管理</span><span class="sxs-lookup"><span data-stu-id="7542d-103">Publish an ASP.NET Core web API to Azure API Management with Visual Studio</span></span>

<span data-ttu-id="7542d-104">作者：[Matt Soucoup](https://twitter.com/codemillmatt)</span><span class="sxs-lookup"><span data-stu-id="7542d-104">By [Matt Soucoup](https://twitter.com/codemillmatt)</span></span>

## <a name="set-up"></a><span data-ttu-id="7542d-105">设置</span><span class="sxs-lookup"><span data-stu-id="7542d-105">Set up</span></span>

- <span data-ttu-id="7542d-106">如果没有[免费 Azure 帐户](https://azure.microsoft.com/free/dotnet/)，请开设一个。</span><span class="sxs-lookup"><span data-stu-id="7542d-106">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="7542d-107">[创建新的 Azure API 管理实例](/azure/api-management/get-started-create-service-instance)（如果尚未创建）。</span><span class="sxs-lookup"><span data-stu-id="7542d-107">[Create a new Azure API Management instance](/azure/api-management/get-started-create-service-instance) if you haven't already.</span></span>
- <span data-ttu-id="7542d-108">[创建 Web API 应用项目](xref:tutorials/first-web-api#create-a-web-project).</span><span class="sxs-lookup"><span data-stu-id="7542d-108">[Create a web API app project](xref:tutorials/first-web-api#create-a-web-project).</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="7542d-109">配置应用</span><span class="sxs-lookup"><span data-stu-id="7542d-109">Configure the app</span></span>

<span data-ttu-id="7542d-110">通过将 Swagger 定义添加到 ASP.NET Core Web API，Azure API 管理可读取应用的 API 定义。</span><span class="sxs-lookup"><span data-stu-id="7542d-110">Adding Swagger definitions to the ASP.NET Core web API allows Azure API Management to read the app's API definitions.</span></span> <span data-ttu-id="7542d-111">完成以下步骤。</span><span class="sxs-lookup"><span data-stu-id="7542d-111">Complete the following steps.</span></span>

### <a name="add-swagger"></a><span data-ttu-id="7542d-112">添加 Swagger</span><span class="sxs-lookup"><span data-stu-id="7542d-112">Add Swagger</span></span>

1. <span data-ttu-id="7542d-113">将 [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet 包添加到 ASP.NET Core Web API 项目：</span><span class="sxs-lookup"><span data-stu-id="7542d-113">Add the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet package to the ASP.NET Core web API project:</span></span>

    ![配置 NuGet 对话框的屏幕截图](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. <span data-ttu-id="7542d-115">将以下行添加到 `Startup.ConfigureServices` 方法中：</span><span class="sxs-lookup"><span data-stu-id="7542d-115">Add the following line to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. <span data-ttu-id="7542d-116">将以下行添加到 `Startup.Configure` 方法中：</span><span class="sxs-lookup"><span data-stu-id="7542d-116">Add the following line to the `Startup.Configure` method:</span></span>

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a><span data-ttu-id="7542d-117">更改 API 路由</span><span class="sxs-lookup"><span data-stu-id="7542d-117">Change the API routing</span></span>

<span data-ttu-id="7542d-118">接下来将更改访问 `WeatherForecastController` 的 `Get` 操作所需的 URL 结构。</span><span class="sxs-lookup"><span data-stu-id="7542d-118">Next, you'll change the URL structure needed to access the `Get` action of the `WeatherForecastController`.</span></span> <span data-ttu-id="7542d-119">完成以下步骤：</span><span class="sxs-lookup"><span data-stu-id="7542d-119">Complete the following steps:</span></span>

1. <span data-ttu-id="7542d-120">打开 WeatherForecastController.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="7542d-120">Open the *WeatherForecastController.cs* file.</span></span>
1. <span data-ttu-id="7542d-121">删除 `[Route("[controller]")]` 类级特性。</span><span class="sxs-lookup"><span data-stu-id="7542d-121">Delete the `[Route("[controller]")]` class-level attribute.</span></span> <span data-ttu-id="7542d-122">类定义将如下所示：</span><span class="sxs-lookup"><span data-stu-id="7542d-122">The class definition will look like the following:</span></span>

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. <span data-ttu-id="7542d-123">向 `Get()` 操作添加 `[Route("/")]` 特性。</span><span class="sxs-lookup"><span data-stu-id="7542d-123">Add a `[Route("/")]` attribute to the `Get()` action.</span></span> <span data-ttu-id="7542d-124">函数定义将如下所示：</span><span class="sxs-lookup"><span data-stu-id="7542d-124">The function definition will look like the following:</span></span>

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a><span data-ttu-id="7542d-125">将 Web API 发布到 Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="7542d-125">Publish the web API to Azure App Service</span></span>

<span data-ttu-id="7542d-126">完成以下步骤以将 ASP.NET Core Web API 发布到 Azure API 管理：</span><span class="sxs-lookup"><span data-stu-id="7542d-126">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="7542d-127">将 API 应用发布到 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="7542d-127">Publish the API app to Azure App Service.</span></span>
1. <span data-ttu-id="7542d-128">将空白 API 添加到 Azure API 管理服务实例。</span><span class="sxs-lookup"><span data-stu-id="7542d-128">Add a blank API to the Azure API Management service instance.</span></span>
1. <span data-ttu-id="7542d-129">将 ASP.NET Core Web API 应用发布到 Azure API 管理服务实例。</span><span class="sxs-lookup"><span data-stu-id="7542d-129">Publish the ASP.NET Core web API app to the Azure API Management service instance.</span></span>

### <a name="publish-the-api-app-to-azure-app-service"></a><span data-ttu-id="7542d-130">将 API 应用发布到 Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="7542d-130">Publish the API app to Azure App Service</span></span>

<span data-ttu-id="7542d-131">完成以下步骤以将 ASP.NET Core Web API 发布到 Azure API 管理：</span><span class="sxs-lookup"><span data-stu-id="7542d-131">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="7542d-132">在“解决方案资源管理器”中，右键单击项目，然后选择“发布” ：</span><span class="sxs-lookup"><span data-stu-id="7542d-132">In **Solution Explorer**, right-click the project and select **Publish**:</span></span>

    ![打开且突出显示“发布”链接的上下文菜单](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. <span data-ttu-id="7542d-134">在“发布”对话框中，选择“Azure”，然后选择“下一步”按钮  ：</span><span class="sxs-lookup"><span data-stu-id="7542d-134">In the **Publish** dialog, select **Azure** and select the **Next** button:</span></span>

    ![“发布”对话框](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. <span data-ttu-id="7542d-136">选择“Azure 应用服务(Windows)”，然后选择“下一步”按钮 ：</span><span class="sxs-lookup"><span data-stu-id="7542d-136">Select **Azure App Service (Windows)** and select the **Next** button:</span></span>

    ![“发布”对话框：选择应用服务](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. <span data-ttu-id="7542d-138">选择“创建新的 Azure 应用服务”。</span><span class="sxs-lookup"><span data-stu-id="7542d-138">Select **Create a new Azure App Service**.</span></span>

    ![“发布”对话框：选择 Azure 服务实例](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    <span data-ttu-id="7542d-140">此时会显示“创建应用服务”对话框。</span><span class="sxs-lookup"><span data-stu-id="7542d-140">The **Create App Service** dialog appears.</span></span> <span data-ttu-id="7542d-141">“应用名称”、“资源组”和“应用服务计划”输入字段已填充  。</span><span class="sxs-lookup"><span data-stu-id="7542d-141">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="7542d-142">可以保留这些名称，也可以进行更改。</span><span class="sxs-lookup"><span data-stu-id="7542d-142">You can keep these names or change them.</span></span>
1. <span data-ttu-id="7542d-143">选择“创建”按钮。</span><span class="sxs-lookup"><span data-stu-id="7542d-143">Select the **Create** button.</span></span>

    ![“创建应用服务”对话框](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

<span data-ttu-id="7542d-145">创建完成后，对话框将自动关闭，“发布”对话框将再次成为焦点。</span><span class="sxs-lookup"><span data-stu-id="7542d-145">After creation is completed, the dialog is automatically closed and the **Publish** dialog gets focus again.</span></span> <span data-ttu-id="7542d-146">将自动选择创建的实例。</span><span class="sxs-lookup"><span data-stu-id="7542d-146">The instance that was created is automatically selected.</span></span>

![“发布”对话框：选择应用服务实例](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

<span data-ttu-id="7542d-148">此时，需要将 API 添加到 Azure API 管理服务。</span><span class="sxs-lookup"><span data-stu-id="7542d-148">At this point, you need to add an API to the Azure API Management service.</span></span> <span data-ttu-id="7542d-149">完成以下任务时，请使 Visual Studio 保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="7542d-149">Leave Visual Studio open while you complete the following tasks.</span></span>

### <a name="add-an-api-to-azure-api-management"></a><span data-ttu-id="7542d-150">将 API 添加到 Azure API 管理</span><span class="sxs-lookup"><span data-stu-id="7542d-150">Add an API to Azure API Management</span></span>

1. <span data-ttu-id="7542d-151">在 Azure 门户中打开先前创建的 API 管理服务实例。</span><span class="sxs-lookup"><span data-stu-id="7542d-151">Open the API Management Service instance created previously in the Azure portal.</span></span> <span data-ttu-id="7542d-152">选择“API”边栏选项卡：</span><span class="sxs-lookup"><span data-stu-id="7542d-152">Select the **APIs** blade:</span></span>

  ![从 API 管理服务实例中选择了“API”边栏选项卡](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. <span data-ttu-id="7542d-154">选择 Echo API 旁边的 3 个点，然后从弹出菜单中选择“删除”以将其删除 。</span><span class="sxs-lookup"><span data-stu-id="7542d-154">Select the 3 dots next to **Echo API** and then select **Delete** from the pop-up menu to remove it.</span></span>

  ![从 API 管理服务实例中删除 echo API](publish-to-azure-api-management-using-vs/_static/portal_delete_echo.png)

1. <span data-ttu-id="7542d-156">从“添加新 API”面板中，选择“空白 API”磁贴 ：</span><span class="sxs-lookup"><span data-stu-id="7542d-156">From the **Add a new API** panel, select the **Blank API** tile:</span></span>

  ![突出显示了“空白 API”磁贴的屏幕](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. <span data-ttu-id="7542d-158">在出现的“创建空白 API”对话框中输入以下值：</span><span class="sxs-lookup"><span data-stu-id="7542d-158">Enter the following values in the **Create a blank API** dialog that appears:</span></span>    

    - <span data-ttu-id="7542d-159">**显示名称**：WeatherForecasts</span><span class="sxs-lookup"><span data-stu-id="7542d-159">**Display Name**: *WeatherForecasts*</span></span>
    - <span data-ttu-id="7542d-160">**名称**：weatherforecasts</span><span class="sxs-lookup"><span data-stu-id="7542d-160">**Name**: *weatherforecasts*</span></span>
    - <span data-ttu-id="7542d-161">**API URL 后缀**：v1</span><span class="sxs-lookup"><span data-stu-id="7542d-161">**API Url suffix**: *v1*</span></span>
    - <span data-ttu-id="7542d-162">将“Web 服务 URL”字段保留为空。</span><span class="sxs-lookup"><span data-stu-id="7542d-162">Leave the **Web service URL** field empty.</span></span>

1. <span data-ttu-id="7542d-163">选择“创建”按钮。</span><span class="sxs-lookup"><span data-stu-id="7542d-163">Select the **Create** button.</span></span>

    ![“创建空白 API”对话完成时的屏幕截图](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

<span data-ttu-id="7542d-165">已创建空白 API。</span><span class="sxs-lookup"><span data-stu-id="7542d-165">The blank API is created.</span></span>

![API 管理门户中空白 API 的屏幕截图](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a><span data-ttu-id="7542d-167">将 ASP.NET Core Web API 发布到 Azure API 管理</span><span class="sxs-lookup"><span data-stu-id="7542d-167">Publish the ASP.NET Core web API to Azure API Management</span></span>

1. <span data-ttu-id="7542d-168">切换回 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="7542d-168">Switch back to Visual Studio.</span></span> <span data-ttu-id="7542d-169">“发布”对话框应在你之前离开的位置保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="7542d-169">The **Publish** dialog should still be open where you left off before.</span></span>
1. <span data-ttu-id="7542d-170">选择新发布的 Azure 应用服务，使其突出显示。</span><span class="sxs-lookup"><span data-stu-id="7542d-170">Select the newly published Azure App Service so it's highlighted.</span></span>
1. <span data-ttu-id="7542d-171">选择“下一步”按钮。</span><span class="sxs-lookup"><span data-stu-id="7542d-171">Select the **Next** button.</span></span>

    ![“发布”对话框的屏幕截图，其中已选择应用服务](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. <span data-ttu-id="7542d-173">此对话框现在显示之前创建的 Azure API 管理服务。</span><span class="sxs-lookup"><span data-stu-id="7542d-173">The dialog now shows the Azure API Management service created before.</span></span> <span data-ttu-id="7542d-174">将其和 APIs 文件夹展开，查看你创建的空白 API。</span><span class="sxs-lookup"><span data-stu-id="7542d-174">Expand it and the *APIs* folder to see the blank API you created.</span></span>
1. <span data-ttu-id="7542d-175">选择空白 API 的名称，然后选择“完成”按钮。</span><span class="sxs-lookup"><span data-stu-id="7542d-175">Select the blank API's name and select the **Finish** button.</span></span>

    ![“发布”对话框的屏幕截图，其中已选择新创建的 Azure API 管理空白 API](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. <span data-ttu-id="7542d-177">该对话框关闭并显示一个摘要屏幕，提供有关发布的信息。</span><span class="sxs-lookup"><span data-stu-id="7542d-177">The dialog closes and a summary screen appears with information about the publish.</span></span> <span data-ttu-id="7542d-178">选择“发布”按钮。</span><span class="sxs-lookup"><span data-stu-id="7542d-178">Select the **Publish** button.</span></span>

    ![Visual Studio 的屏幕截图，其中显示了发布配置文件](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    <span data-ttu-id="7542d-180">Web API 将同时发布到 Azure 应用服务和 Azure API 管理。</span><span class="sxs-lookup"><span data-stu-id="7542d-180">The web API will publish to both Azure App Service and Azure API Management.</span></span> <span data-ttu-id="7542d-181">将显示一个新的浏览器窗口，并显示在 Azure 应用服务中运行的 API。</span><span class="sxs-lookup"><span data-stu-id="7542d-181">A new browser window will appear and show the API running in Azure App Service.</span></span> <span data-ttu-id="7542d-182">可关闭该窗口。</span><span class="sxs-lookup"><span data-stu-id="7542d-182">You can close that window.</span></span>

1. <span data-ttu-id="7542d-183">在 Azure 门户中切换回 Azure API 管理实例。</span><span class="sxs-lookup"><span data-stu-id="7542d-183">Switch back to the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="7542d-184">刷新浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="7542d-184">Refresh the browser window.</span></span>
1. <span data-ttu-id="7542d-185">选择在前面步骤中创建的空白 API。</span><span class="sxs-lookup"><span data-stu-id="7542d-185">Select the blank API you created in the preceding steps.</span></span> <span data-ttu-id="7542d-186">它现在已填充，你可以仔细浏览。</span><span class="sxs-lookup"><span data-stu-id="7542d-186">It's now populated and you can explore around.</span></span>

    ![Azure API 管理中已填充的 API 的屏幕截图](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a><span data-ttu-id="7542d-188">配置已发布的 API 名称</span><span class="sxs-lookup"><span data-stu-id="7542d-188">Configure the published API name</span></span>

<span data-ttu-id="7542d-189">请注意，API 名称不同于你命名的名称。</span><span class="sxs-lookup"><span data-stu-id="7542d-189">Notice the name of the API is different than what you named it.</span></span> <span data-ttu-id="7542d-190">已发布的 API 名称为 WeatherAPI；但你在创建时将其命名为 WeatherForecasts 。</span><span class="sxs-lookup"><span data-stu-id="7542d-190">The published API is named *WeatherAPI*; however, you named it *WeatherForecasts* when you created it.</span></span> <span data-ttu-id="7542d-191">完成以下步骤来修复该名称：</span><span class="sxs-lookup"><span data-stu-id="7542d-191">Complete the following steps to fix the name:</span></span>

![已填充 API 的屏幕截图，其中突出显示了不同名称](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. <span data-ttu-id="7542d-193">删除 `Startup.ConfigureServices` 方法中的以下行：</span><span class="sxs-lookup"><span data-stu-id="7542d-193">Delete the following line from the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. <span data-ttu-id="7542d-194">将以下代码添加到 `Startup.ConfigureServices` 方法中：</span><span class="sxs-lookup"><span data-stu-id="7542d-194">Add the following code to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("v1", new Microsoft.OpenApi.Models.OpenApiInfo
        {
            Title = "WeatherForecasts",
            Version = "v1"
        });
    });
    ```

1. <span data-ttu-id="7542d-195">打开新创建的发布配置文件。</span><span class="sxs-lookup"><span data-stu-id="7542d-195">Open the newly created publish profile.</span></span> <span data-ttu-id="7542d-196">可从“解决方案资源管理器”在 Properties/PublishProfiles 文件夹中找到它。</span><span class="sxs-lookup"><span data-stu-id="7542d-196">It can be found from **Solution Explorer** in the *Properties/PublishProfiles* folder.</span></span>

    ![突出显示发布配置文件位置的屏幕截图](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. <span data-ttu-id="7542d-198">将 `<OpenAPIDocumentName>` 元素的值从 `v1` 更改为 `WeatherForecasts`。</span><span class="sxs-lookup"><span data-stu-id="7542d-198">Change the `<OpenAPIDocumentName>` element's value from `v1` to `WeatherForecasts`.</span></span>

    ![发布配置文件所需更改的屏幕截图](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. <span data-ttu-id="7542d-200">重新发布 ASP.NET Core Web API 并在 Azure 门户中打开 Azure API 管理实例。</span><span class="sxs-lookup"><span data-stu-id="7542d-200">Republish the ASP.NET Core web API and open the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="7542d-201">在浏览器中刷新页面。</span><span class="sxs-lookup"><span data-stu-id="7542d-201">Refresh the page in your browser.</span></span> <span data-ttu-id="7542d-202">此时会显示 API 的名称是正确的。</span><span class="sxs-lookup"><span data-stu-id="7542d-202">You'll see the name of the API is now correct.</span></span>

    ![门户中已完成的 API 的屏幕截图](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a><span data-ttu-id="7542d-204">验证 Web API 是否正常工作</span><span class="sxs-lookup"><span data-stu-id="7542d-204">Verify the web API is working</span></span>

<span data-ttu-id="7542d-205">可通过以下步骤从 Azure 门户的“Azure API 管理”中测试已部署的 ASP.NET Core Web API：</span><span class="sxs-lookup"><span data-stu-id="7542d-205">You can test the deployed ASP.NET Core web API in Azure API Management from the Azure portal with the following steps:</span></span>

1. <span data-ttu-id="7542d-206">打开“测试”选项卡。 </span><span class="sxs-lookup"><span data-stu-id="7542d-206">Open the **Test** tab.</span></span>
1. <span data-ttu-id="7542d-207">选择 / 或 Get 操作 。</span><span class="sxs-lookup"><span data-stu-id="7542d-207">Select **/** or the **Get** operation.</span></span>
1. <span data-ttu-id="7542d-208">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="7542d-208">Select **Send**.</span></span>

    ![测试前门户的屏幕截图](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

<span data-ttu-id="7542d-210">成功的响应如下所示：</span><span class="sxs-lookup"><span data-stu-id="7542d-210">A successful response will look like the following:</span></span>

![API 管理中成功的响应的屏幕截图](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a><span data-ttu-id="7542d-212">知识巩固</span><span class="sxs-lookup"><span data-stu-id="7542d-212">Clean up</span></span>

<span data-ttu-id="7542d-213">完成应用测试后，转到 [Azure 门户](https://portal.azure.com/)并删除该应用。</span><span class="sxs-lookup"><span data-stu-id="7542d-213">When you've finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

1. <span data-ttu-id="7542d-214">选择“资源组”，然后选择所创建的资源组。</span><span class="sxs-lookup"><span data-stu-id="7542d-214">Select **Resource groups**, then select the resource group you created.</span></span>

    ![Azure 门户：侧边栏菜单中的资源组](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. <span data-ttu-id="7542d-216">在“资源组”页面中，选择“删除” 。</span><span class="sxs-lookup"><span data-stu-id="7542d-216">In the **Resource groups** page, select **Delete**.</span></span>

    ![Azure 门户：“资源组”页](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. <span data-ttu-id="7542d-218">输入资源组的名称并选择“删除”。</span><span class="sxs-lookup"><span data-stu-id="7542d-218">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="7542d-219">现已从 Azure 中删除了本教程中创建的应用和其他所有资源。</span><span class="sxs-lookup"><span data-stu-id="7542d-219">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7542d-220">后续步骤</span><span class="sxs-lookup"><span data-stu-id="7542d-220">Next steps</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="7542d-221">其他资源</span><span class="sxs-lookup"><span data-stu-id="7542d-221">Additional resources</span></span>

- [<span data-ttu-id="7542d-222">Azure API 管理</span><span class="sxs-lookup"><span data-stu-id="7542d-222">Azure API Management</span></span>](/azure/api-management/api-management-key-concepts)
- [<span data-ttu-id="7542d-223">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="7542d-223">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
