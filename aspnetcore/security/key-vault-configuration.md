---
title: ASP.NET核心中的 Azure 密钥保管库配置提供程序
author: rick-anderson
description: 了解如何使用 Azure 密钥保管库配置提供程序使用运行时加载的名称值对配置应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: security/key-vault-configuration
ms.openlocfilehash: d78584eaa3fcd50425698e4db581060b6ca845f8
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228161"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="9e709-103">ASP.NET核心中的 Azure 密钥保管库配置提供程序</span><span class="sxs-lookup"><span data-stu-id="9e709-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="9e709-104">由[安德鲁斯坦顿护士](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="9e709-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9e709-105">本文档介绍如何使用[Microsoft Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)配置提供程序从 Azure 密钥保管库机密加载应用配置值。</span><span class="sxs-lookup"><span data-stu-id="9e709-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="9e709-106">Azure 密钥保管库是一种基于云的服务，可帮助保护应用和服务使用的加密密钥和机密。</span><span class="sxs-lookup"><span data-stu-id="9e709-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="9e709-107">将 Azure 密钥保管库用于 ASP.NET 核心应用的常见方案包括：</span><span class="sxs-lookup"><span data-stu-id="9e709-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="9e709-108">控制对敏感配置数据的访问。</span><span class="sxs-lookup"><span data-stu-id="9e709-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="9e709-109">在存储配置数据时满足 FIPS 140-2 级验证的硬件安全模块 （HSM） 的要求。</span><span class="sxs-lookup"><span data-stu-id="9e709-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="9e709-110">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="9e709-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="9e709-111">包</span><span class="sxs-lookup"><span data-stu-id="9e709-111">Packages</span></span>

<span data-ttu-id="9e709-112">向[Microsoft.扩展.配置.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)包添加包引用。</span><span class="sxs-lookup"><span data-stu-id="9e709-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="9e709-113">示例应用</span><span class="sxs-lookup"><span data-stu-id="9e709-113">Sample app</span></span>

<span data-ttu-id="9e709-114">示例应用以`#define`*Program.cs*文件顶部的语句确定的两种模式之一运行：</span><span class="sxs-lookup"><span data-stu-id="9e709-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="9e709-115">`Certificate`&ndash;演示使用 Azure 密钥保管库客户端 ID 和 X.509 证书访问存储在 Azure 密钥保管库中的秘密。</span><span class="sxs-lookup"><span data-stu-id="9e709-115">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="9e709-116">此版本的示例可以从任何位置运行，部署到 Azure 应用服务或任何能够为 ASP.NET 酷应用提供服务的主机。</span><span class="sxs-lookup"><span data-stu-id="9e709-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="9e709-117">`Managed`&ndash;演示如何[使用 Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)使用 Azure AD 身份验证将应用验证为 Azure 密钥保管库，而无需在应用的代码或配置中存储凭据。</span><span class="sxs-lookup"><span data-stu-id="9e709-117">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="9e709-118">使用托管标识进行身份验证时，不需要 Azure AD 应用程序 ID 和密码（客户端密钥）。</span><span class="sxs-lookup"><span data-stu-id="9e709-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="9e709-119">必须`Managed`将示例的版本部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="9e709-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="9e709-120">按照"[为 Azure 资源使用托管标识](#use-managed-identities-for-azure-resources)"部分中的指南进行操作。</span><span class="sxs-lookup"><span data-stu-id="9e709-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="9e709-121">有关如何使用预处理器指令 （）`#define`配置示例应用的详细信息，请参阅<xref:index#preprocessor-directives-in-sample-code>。</span><span class="sxs-lookup"><span data-stu-id="9e709-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="9e709-122">开发环境中的秘密存储</span><span class="sxs-lookup"><span data-stu-id="9e709-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="9e709-123">使用[机密管理器工具](xref:security/app-secrets)在本地设置机密。</span><span class="sxs-lookup"><span data-stu-id="9e709-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="9e709-124">当示例应用在开发环境中的本地计算机上运行时，将从本地机密管理器存储加载机密。</span><span class="sxs-lookup"><span data-stu-id="9e709-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="9e709-125">"机密管理器"工具需要`<UserSecretsId>`应用的项目文件中的属性。</span><span class="sxs-lookup"><span data-stu-id="9e709-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="9e709-126">将属性值 （`{GUID}`） 设置为任何唯一的 GUID：</span><span class="sxs-lookup"><span data-stu-id="9e709-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="9e709-127">机密创建为名称值对。</span><span class="sxs-lookup"><span data-stu-id="9e709-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="9e709-128">层次结构值（配置部分）在`:`[ASP.NET核心配置](xref:fundamentals/configuration/index)密钥名称中使用（冒号）作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="9e709-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="9e709-129">机密管理器从打开到项目[内容根](xref:fundamentals/index#content-root)的命令外壳使用，其中`{SECRET NAME}`的名称和`{SECRET VALUE}`值是：</span><span class="sxs-lookup"><span data-stu-id="9e709-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="9e709-130">从项目[的内容根](xref:fundamentals/index#content-root)在命令 shell 中执行以下命令，以设置示例应用的秘密：</span><span class="sxs-lookup"><span data-stu-id="9e709-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="9e709-131">当这些机密存储在[Azure 密钥保管库部分的生产环境中的"机密存储"中的](#secret-storage-in-the-production-environment-with-azure-key-vault)Azure 密钥保管`_dev`库中时，后缀将`_prod`更改为 。</span><span class="sxs-lookup"><span data-stu-id="9e709-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="9e709-132">后缀在应用的输出中提供指示配置值来源的可视提示。</span><span class="sxs-lookup"><span data-stu-id="9e709-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="9e709-133">使用 Azure 密钥保管库的生产环境中的秘密存储</span><span class="sxs-lookup"><span data-stu-id="9e709-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="9e709-134">此处汇总了["快速入门：使用 Azure CLI"主题从 Azure 密钥保管库设置和检索机密](/azure/key-vault/quick-create-cli)的说明，用于创建 Azure 密钥保管库并存储示例应用使用的秘密。</span><span class="sxs-lookup"><span data-stu-id="9e709-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="9e709-135">有关详细信息，请参阅主题。</span><span class="sxs-lookup"><span data-stu-id="9e709-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="9e709-136">使用[Azure 门户](https://portal.azure.com/)中的以下任一方法打开 Azure 云外壳：</span><span class="sxs-lookup"><span data-stu-id="9e709-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="9e709-137">选择代码块右上角的“试用”。\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="9e709-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="9e709-138">在文本框中使用搜索字符串"Azure CLI"。</span><span class="sxs-lookup"><span data-stu-id="9e709-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="9e709-139">使用**启动云外壳**按钮在浏览器中打开云外壳。</span><span class="sxs-lookup"><span data-stu-id="9e709-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="9e709-140">选择 Azure 门户右上角菜单上的 **"云壳**"按钮。</span><span class="sxs-lookup"><span data-stu-id="9e709-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="9e709-141">有关详细信息，请参阅[Azure CLI](/cli/azure/)和[Azure 云外壳概述](/azure/cloud-shell/overview)。</span><span class="sxs-lookup"><span data-stu-id="9e709-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="9e709-142">如果您尚未通过身份验证，请使用 命令`az login`登录。</span><span class="sxs-lookup"><span data-stu-id="9e709-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="9e709-143">使用以下命令创建资源组，其中`{RESOURCE GROUP NAME}`为新资源组的资源组名称为`{LOCATION}`Azure 区域（数据中心）：</span><span class="sxs-lookup"><span data-stu-id="9e709-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="9e709-144">使用以下命令在资源组中创建密钥保管库，其中`{KEY VAULT NAME}`为新密钥保管库的名称，`{LOCATION}`是 Azure 区域（数据中心）：</span><span class="sxs-lookup"><span data-stu-id="9e709-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="9e709-145">在密钥保管库中创建机密作为名称值对。</span><span class="sxs-lookup"><span data-stu-id="9e709-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="9e709-146">Azure 密钥保管库密钥库密钥名称仅限于字母数字字符和破折号。</span><span class="sxs-lookup"><span data-stu-id="9e709-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="9e709-147">分层值（配置部分）使用`--`（两个破折号）作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="9e709-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="9e709-148">在密钥保管库密钥名称中，通常不允许从[ASP.NET Core 配置](xref:fundamentals/configuration/index)中从子键中分隔节的冒号。</span><span class="sxs-lookup"><span data-stu-id="9e709-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="9e709-149">因此，当机密加载到应用的配置中时，将使用两个破折号并将其交换为冒号。</span><span class="sxs-lookup"><span data-stu-id="9e709-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="9e709-150">以下机密用于示例应用。</span><span class="sxs-lookup"><span data-stu-id="9e709-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="9e709-151">这些值包括后`_prod`缀，用于将它们与在开发环境中`_dev`加载的后缀值与用户机密区分开来。</span><span class="sxs-lookup"><span data-stu-id="9e709-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="9e709-152">替换为`{KEY VAULT NAME}`在上一步骤中创建的密钥保管库的名称：</span><span class="sxs-lookup"><span data-stu-id="9e709-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="9e709-153">对非 Azure 托管应用使用应用程序 ID 和 X.509 证书</span><span class="sxs-lookup"><span data-stu-id="9e709-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="9e709-154">将 Azure AD、Azure 密钥保管库和应用配置为使用 Azure 活动目录应用程序 ID 和 X.509 证书在**应用托管在 Azure 之外时**对密钥保管库进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="9e709-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="9e709-155">有关详细信息，请参阅[关于密钥、机密和证书](/azure/key-vault/about-keys-secrets-and-certificates)。</span><span class="sxs-lookup"><span data-stu-id="9e709-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="9e709-156">尽管 Azure 中托管的应用支持使用应用程序 ID 和 X.509 证书，但我们建议在 Azure 中托管应用时[对 Azure 资源使用托管标识](#use-managed-identities-for-azure-resources)。</span><span class="sxs-lookup"><span data-stu-id="9e709-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="9e709-157">托管标识不需要在应用或开发环境中存储证书。</span><span class="sxs-lookup"><span data-stu-id="9e709-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="9e709-158">当*Program.cs*文件顶部的语句设置为`Certificate`时，`#define`示例应用使用应用程序 ID 和 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="9e709-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="9e709-159">创建 PKCS_12 存档 （*.pfx*） 证书。</span><span class="sxs-lookup"><span data-stu-id="9e709-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="9e709-160">创建证书的选项包括[Windows 上的 MakeCert](/windows/desktop/seccrypto/makecert)和[OpenSSL](https://www.openssl.org/)。</span><span class="sxs-lookup"><span data-stu-id="9e709-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="9e709-161">将证书安装到当前用户的个人证书存储中。</span><span class="sxs-lookup"><span data-stu-id="9e709-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="9e709-162">将密钥标记为可导出是可选的。</span><span class="sxs-lookup"><span data-stu-id="9e709-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="9e709-163">请注意证书的指纹，此过程稍后将使用。</span><span class="sxs-lookup"><span data-stu-id="9e709-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="9e709-164">将 PKCS_12 存档 *（.pfx*） 证书导出为 DER 编码证书 *（.cer*）。</span><span class="sxs-lookup"><span data-stu-id="9e709-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="9e709-165">使用 Azure AD（**应用注册）注册**应用。</span><span class="sxs-lookup"><span data-stu-id="9e709-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="9e709-166">将 DER 编码的证书 *（.cer*） 上载到 Azure AD：</span><span class="sxs-lookup"><span data-stu-id="9e709-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="9e709-167">在 Azure AD 中选择应用。</span><span class="sxs-lookup"><span data-stu-id="9e709-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="9e709-168">导航到**证书&机密**。</span><span class="sxs-lookup"><span data-stu-id="9e709-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="9e709-169">选择 **"上载证书**"以上载包含公钥的证书。</span><span class="sxs-lookup"><span data-stu-id="9e709-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="9e709-170">*.cer* *、.pem*或 *.crt*证书是可以接受的。</span><span class="sxs-lookup"><span data-stu-id="9e709-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="9e709-171">将密钥保管库名称、应用程序 ID 和证书指纹存储在应用*的应用设置.json*文件中。</span><span class="sxs-lookup"><span data-stu-id="9e709-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="9e709-172">导航到 Azure 门户中的**密钥保管库**。</span><span class="sxs-lookup"><span data-stu-id="9e709-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="9e709-173">[使用 Azure 密钥保管库部分在"生产环境"中的秘密存储中选择在"机密存储"中](#secret-storage-in-the-production-environment-with-azure-key-vault)创建的密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="9e709-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="9e709-174">选择**访问策略**。</span><span class="sxs-lookup"><span data-stu-id="9e709-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="9e709-175">选择 **"添加访问策略**"。</span><span class="sxs-lookup"><span data-stu-id="9e709-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="9e709-176">打开 **"机密"权限**，并提供具有**获取**和**列表**权限的应用。</span><span class="sxs-lookup"><span data-stu-id="9e709-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="9e709-177">选择 **"选择主体**"并按名称选择已注册的应用。</span><span class="sxs-lookup"><span data-stu-id="9e709-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="9e709-178">选择“选择”按钮  。</span><span class="sxs-lookup"><span data-stu-id="9e709-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="9e709-179">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="9e709-179">Select **OK**.</span></span>
1. <span data-ttu-id="9e709-180">选择“保存”。 </span><span class="sxs-lookup"><span data-stu-id="9e709-180">Select **Save**.</span></span>
1. <span data-ttu-id="9e709-181">部署应用。</span><span class="sxs-lookup"><span data-stu-id="9e709-181">Deploy the app.</span></span>

<span data-ttu-id="9e709-182">示例`Certificate`应用从`IConfigurationRoot`与机密名称同名获取其配置值：</span><span class="sxs-lookup"><span data-stu-id="9e709-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="9e709-183">非分层值：使用 获取`SecretName`的值`config["SecretName"]`。</span><span class="sxs-lookup"><span data-stu-id="9e709-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="9e709-184">分层值（节）：使用`:`（冒号）表示法或`GetSection`扩展方法。</span><span class="sxs-lookup"><span data-stu-id="9e709-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="9e709-185">使用以下任一方法获取配置值：</span><span class="sxs-lookup"><span data-stu-id="9e709-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="9e709-186">X.509 证书由操作系统管理。</span><span class="sxs-lookup"><span data-stu-id="9e709-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="9e709-187">应用调用<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>应用*设置.json*文件提供的值：</span><span class="sxs-lookup"><span data-stu-id="9e709-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="9e709-188">示例值：</span><span class="sxs-lookup"><span data-stu-id="9e709-188">Example values:</span></span>

* <span data-ttu-id="9e709-189">密钥保管库名称：`contosovault`</span><span class="sxs-lookup"><span data-stu-id="9e709-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="9e709-190">应用程序 ID：`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="9e709-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="9e709-191">证书指纹：`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="9e709-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="9e709-192">*应用程序设置.json*：</span><span class="sxs-lookup"><span data-stu-id="9e709-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="9e709-193">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="9e709-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="9e709-194">在开发环境中，机密值随后缀一`_dev`起加载。</span><span class="sxs-lookup"><span data-stu-id="9e709-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="9e709-195">在生产环境中，值使用`_prod`后缀加载。</span><span class="sxs-lookup"><span data-stu-id="9e709-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="9e709-196">对 Azure 资源使用托管标识</span><span class="sxs-lookup"><span data-stu-id="9e709-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="9e709-197">**部署到 Azure 的应用**可以利用 Azure[资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)，这允许应用使用 Azure 密钥保管库进行身份验证，而无需在应用中存储凭据（应用程序 ID 和密码/客户端密钥）。</span><span class="sxs-lookup"><span data-stu-id="9e709-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="9e709-198">当*Program.cs*文件顶部的语句设置为`Managed`时，`#define`示例应用对 Azure 资源使用托管标识。</span><span class="sxs-lookup"><span data-stu-id="9e709-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="9e709-199">在应用*的应用设置.json*文件中输入保管库名称。</span><span class="sxs-lookup"><span data-stu-id="9e709-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="9e709-200">当示例应用设置为`Managed`版本时，不需要应用程序 ID 和密码（客户端密钥），因此您可以忽略这些配置条目。</span><span class="sxs-lookup"><span data-stu-id="9e709-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="9e709-201">应用将部署到 Azure，Azure 会验证应用仅使用存储在*appsettings.json*文件中的保管库名称访问 Azure 密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="9e709-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="9e709-202">将示例应用部署到 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="9e709-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="9e709-203">创建服务时，部署到 Azure 应用服务的应用将自动注册到 Azure AD。</span><span class="sxs-lookup"><span data-stu-id="9e709-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="9e709-204">从部署中获取对象 ID，以便在以下命令中使用。</span><span class="sxs-lookup"><span data-stu-id="9e709-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="9e709-205">对象 ID 显示在应用服务的 **"标识"** 面板上的 Azure 门户中。</span><span class="sxs-lookup"><span data-stu-id="9e709-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="9e709-206">使用 Azure CLI 和应用的对象 ID，向应用提供`list`访问`get`密钥保管库的权限：</span><span class="sxs-lookup"><span data-stu-id="9e709-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="9e709-207">使用 Azure CLI、PowerShell 或 Azure 门户**重新启动应用**。</span><span class="sxs-lookup"><span data-stu-id="9e709-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="9e709-208">示例应用：</span><span class="sxs-lookup"><span data-stu-id="9e709-208">The sample app:</span></span>

* <span data-ttu-id="9e709-209">创建没有连接字符串的`AzureServiceTokenProvider`类实例。</span><span class="sxs-lookup"><span data-stu-id="9e709-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="9e709-210">未提供连接字符串时，提供程序将尝试从 Azure 资源的托管标识获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="9e709-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="9e709-211"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>使用`AzureServiceTokenProvider`实例令牌回调创建新。</span><span class="sxs-lookup"><span data-stu-id="9e709-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="9e709-212">实例<xref:Microsoft.Azure.KeyVault.KeyVaultClient>与加载所有机密值的默认实现<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>一起使用，并将双分线 （）`--`替换为键名中的冒号`:`（ ）。</span><span class="sxs-lookup"><span data-stu-id="9e709-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="9e709-213">密钥保管库名称示例值：`contosovault`</span><span class="sxs-lookup"><span data-stu-id="9e709-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="9e709-214">*应用程序设置.json*：</span><span class="sxs-lookup"><span data-stu-id="9e709-214">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="9e709-215">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="9e709-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="9e709-216">在开发环境中，机密值具有后缀`_dev`，因为它们由用户机密提供。</span><span class="sxs-lookup"><span data-stu-id="9e709-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="9e709-217">在生产环境中，值使用后缀加载，`_prod`因为它们由 Azure 密钥保管库提供。</span><span class="sxs-lookup"><span data-stu-id="9e709-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="9e709-218">如果收到错误`Access denied`，请确认应用已注册 Azure AD 并提供有关密钥保管库的访问权限。</span><span class="sxs-lookup"><span data-stu-id="9e709-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="9e709-219">确认已重新启动 Azure 中的服务。</span><span class="sxs-lookup"><span data-stu-id="9e709-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="9e709-220">有关将提供程序与托管标识和 Azure DevOps 管道一起使用的信息，请参阅创建 Azure[资源管理器服务连接到具有托管服务标识的 VM。](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)</span><span class="sxs-lookup"><span data-stu-id="9e709-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="9e709-221">配置选项</span><span class="sxs-lookup"><span data-stu-id="9e709-221">Configuration options</span></span>

<span data-ttu-id="9e709-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>可以接受 ： <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions></span><span class="sxs-lookup"><span data-stu-id="9e709-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="9e709-223">Property</span><span class="sxs-lookup"><span data-stu-id="9e709-223">Property</span></span>         | <span data-ttu-id="9e709-224">说明</span><span class="sxs-lookup"><span data-stu-id="9e709-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="9e709-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>用于检索值。</span><span class="sxs-lookup"><span data-stu-id="9e709-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="9e709-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>用于控制机密加载的实例。</span><span class="sxs-lookup"><span data-stu-id="9e709-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="9e709-227">`Timespan`在轮询密钥保管库以进行更改的尝试之间等待。</span><span class="sxs-lookup"><span data-stu-id="9e709-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="9e709-228">默认值为`null`（未重新加载配置）。</span><span class="sxs-lookup"><span data-stu-id="9e709-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="9e709-229">密钥保管库 URI。</span><span class="sxs-lookup"><span data-stu-id="9e709-229">Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="9e709-230">使用键名称前缀</span><span class="sxs-lookup"><span data-stu-id="9e709-230">Use a key name prefix</span></span>

<span data-ttu-id="9e709-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>提供接受 的实现的<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>重载，它允许您控制如何将密钥保管库机密转换为配置密钥。</span><span class="sxs-lookup"><span data-stu-id="9e709-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="9e709-232">例如，您可以实现接口，以基于您在应用启动时提供的前缀值加载机密值。</span><span class="sxs-lookup"><span data-stu-id="9e709-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="9e709-233">例如，这允许您根据应用版本加载机密。</span><span class="sxs-lookup"><span data-stu-id="9e709-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="9e709-234">不要使用密钥保管库机密的前缀将多个应用的机密放入同一密钥保管库或将环境机密（例如 *，开发*与*生产*机密）放入同一保管库。</span><span class="sxs-lookup"><span data-stu-id="9e709-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="9e709-235">我们建议不同的应用和开发/生产环境使用单独的密钥保管库来隔离应用环境，以确保最高级别的安全性。</span><span class="sxs-lookup"><span data-stu-id="9e709-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="9e709-236">在下面的示例中，在密钥保管库中建立了机密（并使用开发环境的密钥管理器工具）（`5000-AppSecret`密钥保管库机密名称中不允许周期）。</span><span class="sxs-lookup"><span data-stu-id="9e709-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="9e709-237">此机密表示应用版本 5.0.0.0 的应用机密。</span><span class="sxs-lookup"><span data-stu-id="9e709-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="9e709-238">对于应用的另一个版本 5.1.0.0，将机密添加到 的密钥保管库（并使用机密管理器工具）中`5100-AppSecret`。</span><span class="sxs-lookup"><span data-stu-id="9e709-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="9e709-239">每个应用版本将其版本控制的秘密值加载到其配置中`AppSecret`，作为 ，在加载密钥时剥离版本。</span><span class="sxs-lookup"><span data-stu-id="9e709-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="9e709-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>使用自定义<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>调用 ：</span><span class="sxs-lookup"><span data-stu-id="9e709-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="9e709-241">实现<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>对机密的版本前缀做出反应，以将正确的机密加载到配置中：</span><span class="sxs-lookup"><span data-stu-id="9e709-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="9e709-242">`Load`当机密的名称以前缀开头时加载它。</span><span class="sxs-lookup"><span data-stu-id="9e709-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="9e709-243">其他机密未加载。</span><span class="sxs-lookup"><span data-stu-id="9e709-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="9e709-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="9e709-244">`GetKey`:</span></span>
  * <span data-ttu-id="9e709-245">从机密名称中删除前缀。</span><span class="sxs-lookup"><span data-stu-id="9e709-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="9e709-246">将任何名称中的两个破折号替换为`KeyDelimiter`， 是 配置中使用的分隔符（通常是冒号）。</span><span class="sxs-lookup"><span data-stu-id="9e709-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="9e709-247">Azure 密钥保管库不允许在机密名称中冒号。</span><span class="sxs-lookup"><span data-stu-id="9e709-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="9e709-248">该方法`Load`由提供程序算法调用，该算法通过保管库机密进行遍舍以查找具有版本前缀的提供程序算法。</span><span class="sxs-lookup"><span data-stu-id="9e709-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="9e709-249">使用 找到`Load`版本前缀时，算法使用 方法`GetKey`返回机密名称的配置名称。</span><span class="sxs-lookup"><span data-stu-id="9e709-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="9e709-250">它将版本前缀从机密的名称中剥离，并返回用于加载到应用的配置名称值对中的其他机密名称。</span><span class="sxs-lookup"><span data-stu-id="9e709-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="9e709-251">实施此方法时：</span><span class="sxs-lookup"><span data-stu-id="9e709-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="9e709-252">应用在应用的项目文件中指定的版本。</span><span class="sxs-lookup"><span data-stu-id="9e709-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="9e709-253">在下面的示例中，应用的版本设置为`5.0.0.0`：</span><span class="sxs-lookup"><span data-stu-id="9e709-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="9e709-254">确认应用的项目`<UserSecretsId>`文件中存在属性，其中`{GUID}`是用户提供的 GUID：</span><span class="sxs-lookup"><span data-stu-id="9e709-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="9e709-255">使用[秘密管理器工具](xref:security/app-secrets)在本地保存以下机密：</span><span class="sxs-lookup"><span data-stu-id="9e709-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="9e709-256">机密使用以下 Azure CLI 命令保存在 Azure 密钥保管库中：</span><span class="sxs-lookup"><span data-stu-id="9e709-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="9e709-257">运行应用时，将加载密钥保管库机密。</span><span class="sxs-lookup"><span data-stu-id="9e709-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="9e709-258">的`5000-AppSecret`字符串机密与应用的项目文件 （）`5.0.0.0`中指定的应用版本匹配。</span><span class="sxs-lookup"><span data-stu-id="9e709-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="9e709-259">版本`5000`（使用破折号）从密钥名称中剥离。</span><span class="sxs-lookup"><span data-stu-id="9e709-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="9e709-260">在整个应用中，使用密钥`AppSecret`读取配置将加载机密值。</span><span class="sxs-lookup"><span data-stu-id="9e709-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="9e709-261">如果在项目文件中将应用的版本更改为，`5.1.0.0`并且应用再次运行，则返回的机密值位于`5.1.0.0_secret_value_dev`"开发"环境和`5.1.0.0_secret_value_prod`"生产"中。</span><span class="sxs-lookup"><span data-stu-id="9e709-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="9e709-262">您还可以向<xref:Microsoft.Azure.KeyVault.KeyVaultClient><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>提供您自己的实现。</span><span class="sxs-lookup"><span data-stu-id="9e709-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="9e709-263">自定义客户端允许跨应用共享客户端的单个实例。</span><span class="sxs-lookup"><span data-stu-id="9e709-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="9e709-264">将数组绑定至类</span><span class="sxs-lookup"><span data-stu-id="9e709-264">Bind an array to a class</span></span>

<span data-ttu-id="9e709-265">提供程序能够将配置值读取到数组中以绑定到 POCO 数组。</span><span class="sxs-lookup"><span data-stu-id="9e709-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="9e709-266">从允许键包含冒号`:`（） 分隔符的配置源读取时，使用数字键段来区分组成数组 （`:0:`的`:1:`&hellip;`:{n}:`键 ） 。</span><span class="sxs-lookup"><span data-stu-id="9e709-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="9e709-267">有关详细信息，请参阅[配置：将数组绑定到类](xref:fundamentals/configuration/index#bind-an-array-to-a-class)。</span><span class="sxs-lookup"><span data-stu-id="9e709-267">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="9e709-268">Azure 密钥保管库密钥不能将冒号用作分隔符。</span><span class="sxs-lookup"><span data-stu-id="9e709-268">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="9e709-269">本主题中描述的方法使用双破折号 （）`--`作为分层值（节）的分隔符。</span><span class="sxs-lookup"><span data-stu-id="9e709-269">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="9e709-270">数组键存储在 Azure 密钥保管库中，具有双破折号和数字键段`--0--` `--1--`（， &hellip; `--{n}--`。 。</span><span class="sxs-lookup"><span data-stu-id="9e709-270">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="9e709-271">检查 JSON 文件提供的以下[Serilog](https://serilog.net/)日志记录提供程序配置。</span><span class="sxs-lookup"><span data-stu-id="9e709-271">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="9e709-272">`WriteTo`数组中定义了两个对象文本，反映了两个 Serilog*接收器*，它们描述日志记录输出的目标：</span><span class="sxs-lookup"><span data-stu-id="9e709-272">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="9e709-273">前面 JSON 文件中显示的配置使用双破折号 （）`--`表示法和数字段存储在 Azure 密钥保管库中：</span><span class="sxs-lookup"><span data-stu-id="9e709-273">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="9e709-274">密钥</span><span class="sxs-lookup"><span data-stu-id="9e709-274">Key</span></span> | <span data-ttu-id="9e709-275">值</span><span class="sxs-lookup"><span data-stu-id="9e709-275">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="9e709-276">重新加载机密</span><span class="sxs-lookup"><span data-stu-id="9e709-276">Reload secrets</span></span>

<span data-ttu-id="9e709-277">机密被缓存，直到`IConfigurationRoot.Reload()`被调用。</span><span class="sxs-lookup"><span data-stu-id="9e709-277">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="9e709-278">在执行密钥保管库之前`Reload`，应用不会尊重密钥保管库中过期、禁用和更新的机密。</span><span class="sxs-lookup"><span data-stu-id="9e709-278">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="9e709-279">已禁用和过期的机密</span><span class="sxs-lookup"><span data-stu-id="9e709-279">Disabled and expired secrets</span></span>

<span data-ttu-id="9e709-280">禁用和过期的机密引发<xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>。</span><span class="sxs-lookup"><span data-stu-id="9e709-280">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="9e709-281">要防止应用引发，请使用其他配置提供程序提供配置或更新禁用或过期的机密。</span><span class="sxs-lookup"><span data-stu-id="9e709-281">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="9e709-282">疑难解答</span><span class="sxs-lookup"><span data-stu-id="9e709-282">Troubleshoot</span></span>

<span data-ttu-id="9e709-283">当应用无法使用提供程序加载配置时，将写入[ASP.NET核心日志记录基础结构](xref:fundamentals/logging/index)中的错误消息。</span><span class="sxs-lookup"><span data-stu-id="9e709-283">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="9e709-284">以下条件将阻止加载配置：</span><span class="sxs-lookup"><span data-stu-id="9e709-284">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="9e709-285">应用或证书未在 Azure 活动目录中正确配置。</span><span class="sxs-lookup"><span data-stu-id="9e709-285">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="9e709-286">密钥保管库在 Azure 密钥保管库中不存在。</span><span class="sxs-lookup"><span data-stu-id="9e709-286">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="9e709-287">应用程序无权访问密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="9e709-287">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="9e709-288">访问策略不包括`Get`和`List`权限。</span><span class="sxs-lookup"><span data-stu-id="9e709-288">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="9e709-289">在密钥保管库中，配置数据（名称值对）被错误地命名、丢失、禁用或过期。</span><span class="sxs-lookup"><span data-stu-id="9e709-289">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="9e709-290">应用具有错误的密钥保管库名称 （`KeyVaultName`）、Azure AD`AzureADApplicationId`应用程序 ID （） 或`AzureADCertThumbprint`Azure AD 证书指纹 （）。</span><span class="sxs-lookup"><span data-stu-id="9e709-290">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="9e709-291">对于要加载的值，配置键（名称）在应用中不正确。</span><span class="sxs-lookup"><span data-stu-id="9e709-291">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="9e709-292">将应用的访问策略添加到密钥保管库时，将创建该策略，但在**Access 策略**UI 中未选择 **"保存**"按钮。</span><span class="sxs-lookup"><span data-stu-id="9e709-292">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e709-293">其他资源</span><span class="sxs-lookup"><span data-stu-id="9e709-293">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="9e709-294">微软 Azure：密钥保管库</span><span class="sxs-lookup"><span data-stu-id="9e709-294">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="9e709-295">微软 Azure：密钥保管库文档</span><span class="sxs-lookup"><span data-stu-id="9e709-295">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="9e709-296">如何为 Azure 密钥保管库生成和传输受 HSM 保护的密钥</span><span class="sxs-lookup"><span data-stu-id="9e709-296">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="9e709-297">密钥库客户端类</span><span class="sxs-lookup"><span data-stu-id="9e709-297">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="9e709-298">快速入门：使用 .NET Web 应用在 Azure Key Vault 中设置和检索机密</span><span class="sxs-lookup"><span data-stu-id="9e709-298">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="9e709-299">教程：如何将 Azure Key Vault 与通过 .NET 编写的 Azure Windows 虚拟机配合使用</span><span class="sxs-lookup"><span data-stu-id="9e709-299">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9e709-300">本文档介绍如何使用[Microsoft Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)配置提供程序从 Azure 密钥保管库机密加载应用配置值。</span><span class="sxs-lookup"><span data-stu-id="9e709-300">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="9e709-301">Azure 密钥保管库是一种基于云的服务，可帮助保护应用和服务使用的加密密钥和机密。</span><span class="sxs-lookup"><span data-stu-id="9e709-301">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="9e709-302">将 Azure 密钥保管库用于 ASP.NET 核心应用的常见方案包括：</span><span class="sxs-lookup"><span data-stu-id="9e709-302">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="9e709-303">控制对敏感配置数据的访问。</span><span class="sxs-lookup"><span data-stu-id="9e709-303">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="9e709-304">在存储配置数据时满足 FIPS 140-2 级验证的硬件安全模块 （HSM） 的要求。</span><span class="sxs-lookup"><span data-stu-id="9e709-304">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="9e709-305">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="9e709-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="9e709-306">包</span><span class="sxs-lookup"><span data-stu-id="9e709-306">Packages</span></span>

<span data-ttu-id="9e709-307">向[Microsoft.扩展.配置.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)包添加包引用。</span><span class="sxs-lookup"><span data-stu-id="9e709-307">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="9e709-308">示例应用</span><span class="sxs-lookup"><span data-stu-id="9e709-308">Sample app</span></span>

<span data-ttu-id="9e709-309">示例应用以`#define`*Program.cs*文件顶部的语句确定的两种模式之一运行：</span><span class="sxs-lookup"><span data-stu-id="9e709-309">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="9e709-310">`Certificate`&ndash;演示使用 Azure 密钥保管库客户端 ID 和 X.509 证书访问存储在 Azure 密钥保管库中的秘密。</span><span class="sxs-lookup"><span data-stu-id="9e709-310">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="9e709-311">此版本的示例可以从任何位置运行，部署到 Azure 应用服务或任何能够为 ASP.NET 酷应用提供服务的主机。</span><span class="sxs-lookup"><span data-stu-id="9e709-311">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="9e709-312">`Managed`&ndash;演示如何[使用 Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)使用 Azure AD 身份验证将应用验证为 Azure 密钥保管库，而无需在应用的代码或配置中存储凭据。</span><span class="sxs-lookup"><span data-stu-id="9e709-312">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="9e709-313">使用托管标识进行身份验证时，不需要 Azure AD 应用程序 ID 和密码（客户端密钥）。</span><span class="sxs-lookup"><span data-stu-id="9e709-313">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="9e709-314">必须`Managed`将示例的版本部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="9e709-314">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="9e709-315">按照"[为 Azure 资源使用托管标识](#use-managed-identities-for-azure-resources)"部分中的指南进行操作。</span><span class="sxs-lookup"><span data-stu-id="9e709-315">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="9e709-316">有关如何使用预处理器指令 （）`#define`配置示例应用的详细信息，请参阅<xref:index#preprocessor-directives-in-sample-code>。</span><span class="sxs-lookup"><span data-stu-id="9e709-316">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="9e709-317">开发环境中的秘密存储</span><span class="sxs-lookup"><span data-stu-id="9e709-317">Secret storage in the Development environment</span></span>

<span data-ttu-id="9e709-318">使用[机密管理器工具](xref:security/app-secrets)在本地设置机密。</span><span class="sxs-lookup"><span data-stu-id="9e709-318">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="9e709-319">当示例应用在开发环境中的本地计算机上运行时，将从本地机密管理器存储加载机密。</span><span class="sxs-lookup"><span data-stu-id="9e709-319">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="9e709-320">"机密管理器"工具需要`<UserSecretsId>`应用的项目文件中的属性。</span><span class="sxs-lookup"><span data-stu-id="9e709-320">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="9e709-321">将属性值 （`{GUID}`） 设置为任何唯一的 GUID：</span><span class="sxs-lookup"><span data-stu-id="9e709-321">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="9e709-322">机密创建为名称值对。</span><span class="sxs-lookup"><span data-stu-id="9e709-322">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="9e709-323">层次结构值（配置部分）在`:`[ASP.NET核心配置](xref:fundamentals/configuration/index)密钥名称中使用（冒号）作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="9e709-323">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="9e709-324">机密管理器从打开到项目[内容根](xref:fundamentals/index#content-root)的命令外壳使用，其中`{SECRET NAME}`的名称和`{SECRET VALUE}`值是：</span><span class="sxs-lookup"><span data-stu-id="9e709-324">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="9e709-325">从项目[的内容根](xref:fundamentals/index#content-root)在命令 shell 中执行以下命令，以设置示例应用的秘密：</span><span class="sxs-lookup"><span data-stu-id="9e709-325">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="9e709-326">当这些机密存储在[Azure 密钥保管库部分的生产环境中的"机密存储"中的](#secret-storage-in-the-production-environment-with-azure-key-vault)Azure 密钥保管`_dev`库中时，后缀将`_prod`更改为 。</span><span class="sxs-lookup"><span data-stu-id="9e709-326">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="9e709-327">后缀在应用的输出中提供指示配置值来源的可视提示。</span><span class="sxs-lookup"><span data-stu-id="9e709-327">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="9e709-328">使用 Azure 密钥保管库的生产环境中的秘密存储</span><span class="sxs-lookup"><span data-stu-id="9e709-328">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="9e709-329">此处汇总了["快速入门：使用 Azure CLI"主题从 Azure 密钥保管库设置和检索机密](/azure/key-vault/quick-create-cli)的说明，用于创建 Azure 密钥保管库并存储示例应用使用的秘密。</span><span class="sxs-lookup"><span data-stu-id="9e709-329">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="9e709-330">有关详细信息，请参阅主题。</span><span class="sxs-lookup"><span data-stu-id="9e709-330">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="9e709-331">使用[Azure 门户](https://portal.azure.com/)中的以下任一方法打开 Azure 云外壳：</span><span class="sxs-lookup"><span data-stu-id="9e709-331">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="9e709-332">选择代码块右上角的“试用”。\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="9e709-332">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="9e709-333">在文本框中使用搜索字符串"Azure CLI"。</span><span class="sxs-lookup"><span data-stu-id="9e709-333">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="9e709-334">使用**启动云外壳**按钮在浏览器中打开云外壳。</span><span class="sxs-lookup"><span data-stu-id="9e709-334">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="9e709-335">选择 Azure 门户右上角菜单上的 **"云壳**"按钮。</span><span class="sxs-lookup"><span data-stu-id="9e709-335">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="9e709-336">有关详细信息，请参阅[Azure CLI](/cli/azure/)和[Azure 云外壳概述](/azure/cloud-shell/overview)。</span><span class="sxs-lookup"><span data-stu-id="9e709-336">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="9e709-337">如果您尚未通过身份验证，请使用 命令`az login`登录。</span><span class="sxs-lookup"><span data-stu-id="9e709-337">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="9e709-338">使用以下命令创建资源组，其中`{RESOURCE GROUP NAME}`为新资源组的资源组名称为`{LOCATION}`Azure 区域（数据中心）：</span><span class="sxs-lookup"><span data-stu-id="9e709-338">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="9e709-339">使用以下命令在资源组中创建密钥保管库，其中`{KEY VAULT NAME}`为新密钥保管库的名称，`{LOCATION}`是 Azure 区域（数据中心）：</span><span class="sxs-lookup"><span data-stu-id="9e709-339">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="9e709-340">在密钥保管库中创建机密作为名称值对。</span><span class="sxs-lookup"><span data-stu-id="9e709-340">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="9e709-341">Azure 密钥保管库密钥库密钥名称仅限于字母数字字符和破折号。</span><span class="sxs-lookup"><span data-stu-id="9e709-341">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="9e709-342">分层值（配置部分）使用`--`（两个破折号）作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="9e709-342">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="9e709-343">在密钥保管库密钥名称中，通常不允许从[ASP.NET Core 配置](xref:fundamentals/configuration/index)中从子键中分隔节的冒号。</span><span class="sxs-lookup"><span data-stu-id="9e709-343">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="9e709-344">因此，当机密加载到应用的配置中时，将使用两个破折号并将其交换为冒号。</span><span class="sxs-lookup"><span data-stu-id="9e709-344">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="9e709-345">以下机密用于示例应用。</span><span class="sxs-lookup"><span data-stu-id="9e709-345">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="9e709-346">这些值包括后`_prod`缀，用于将它们与在开发环境中`_dev`加载的后缀值与用户机密区分开来。</span><span class="sxs-lookup"><span data-stu-id="9e709-346">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="9e709-347">替换为`{KEY VAULT NAME}`在上一步骤中创建的密钥保管库的名称：</span><span class="sxs-lookup"><span data-stu-id="9e709-347">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="9e709-348">对非 Azure 托管应用使用应用程序 ID 和 X.509 证书</span><span class="sxs-lookup"><span data-stu-id="9e709-348">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="9e709-349">将 Azure AD、Azure 密钥保管库和应用配置为使用 Azure 活动目录应用程序 ID 和 X.509 证书在**应用托管在 Azure 之外时**对密钥保管库进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="9e709-349">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="9e709-350">有关详细信息，请参阅[关于密钥、机密和证书](/azure/key-vault/about-keys-secrets-and-certificates)。</span><span class="sxs-lookup"><span data-stu-id="9e709-350">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="9e709-351">尽管 Azure 中托管的应用支持使用应用程序 ID 和 X.509 证书，但我们建议在 Azure 中托管应用时[对 Azure 资源使用托管标识](#use-managed-identities-for-azure-resources)。</span><span class="sxs-lookup"><span data-stu-id="9e709-351">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="9e709-352">托管标识不需要在应用或开发环境中存储证书。</span><span class="sxs-lookup"><span data-stu-id="9e709-352">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="9e709-353">当*Program.cs*文件顶部的语句设置为`Certificate`时，`#define`示例应用使用应用程序 ID 和 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="9e709-353">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="9e709-354">创建 PKCS_12 存档 （*.pfx*） 证书。</span><span class="sxs-lookup"><span data-stu-id="9e709-354">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="9e709-355">创建证书的选项包括[Windows 上的 MakeCert](/windows/desktop/seccrypto/makecert)和[OpenSSL](https://www.openssl.org/)。</span><span class="sxs-lookup"><span data-stu-id="9e709-355">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="9e709-356">将证书安装到当前用户的个人证书存储中。</span><span class="sxs-lookup"><span data-stu-id="9e709-356">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="9e709-357">将密钥标记为可导出是可选的。</span><span class="sxs-lookup"><span data-stu-id="9e709-357">Marking the key as exportable is optional.</span></span> <span data-ttu-id="9e709-358">请注意证书的指纹，此过程稍后将使用。</span><span class="sxs-lookup"><span data-stu-id="9e709-358">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="9e709-359">将 PKCS_12 存档 *（.pfx*） 证书导出为 DER 编码证书 *（.cer*）。</span><span class="sxs-lookup"><span data-stu-id="9e709-359">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="9e709-360">使用 Azure AD（**应用注册）注册**应用。</span><span class="sxs-lookup"><span data-stu-id="9e709-360">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="9e709-361">将 DER 编码的证书 *（.cer*） 上载到 Azure AD：</span><span class="sxs-lookup"><span data-stu-id="9e709-361">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="9e709-362">在 Azure AD 中选择应用。</span><span class="sxs-lookup"><span data-stu-id="9e709-362">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="9e709-363">导航到**证书&机密**。</span><span class="sxs-lookup"><span data-stu-id="9e709-363">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="9e709-364">选择 **"上载证书**"以上载包含公钥的证书。</span><span class="sxs-lookup"><span data-stu-id="9e709-364">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="9e709-365">*.cer* *、.pem*或 *.crt*证书是可以接受的。</span><span class="sxs-lookup"><span data-stu-id="9e709-365">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="9e709-366">将密钥保管库名称、应用程序 ID 和证书指纹存储在应用*的应用设置.json*文件中。</span><span class="sxs-lookup"><span data-stu-id="9e709-366">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="9e709-367">导航到 Azure 门户中的**密钥保管库**。</span><span class="sxs-lookup"><span data-stu-id="9e709-367">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="9e709-368">[使用 Azure 密钥保管库部分在"生产环境"中的秘密存储中选择在"机密存储"中](#secret-storage-in-the-production-environment-with-azure-key-vault)创建的密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="9e709-368">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="9e709-369">选择**访问策略**。</span><span class="sxs-lookup"><span data-stu-id="9e709-369">Select **Access policies**.</span></span>
1. <span data-ttu-id="9e709-370">选择 **"添加访问策略**"。</span><span class="sxs-lookup"><span data-stu-id="9e709-370">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="9e709-371">打开 **"机密"权限**，并提供具有**获取**和**列表**权限的应用。</span><span class="sxs-lookup"><span data-stu-id="9e709-371">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="9e709-372">选择 **"选择主体**"并按名称选择已注册的应用。</span><span class="sxs-lookup"><span data-stu-id="9e709-372">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="9e709-373">选择“选择”按钮  。</span><span class="sxs-lookup"><span data-stu-id="9e709-373">Select the **Select** button.</span></span>
1. <span data-ttu-id="9e709-374">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="9e709-374">Select **OK**.</span></span>
1. <span data-ttu-id="9e709-375">选择“保存”。 </span><span class="sxs-lookup"><span data-stu-id="9e709-375">Select **Save**.</span></span>
1. <span data-ttu-id="9e709-376">部署应用。</span><span class="sxs-lookup"><span data-stu-id="9e709-376">Deploy the app.</span></span>

<span data-ttu-id="9e709-377">示例`Certificate`应用从`IConfigurationRoot`与机密名称同名获取其配置值：</span><span class="sxs-lookup"><span data-stu-id="9e709-377">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="9e709-378">非分层值：使用 获取`SecretName`的值`config["SecretName"]`。</span><span class="sxs-lookup"><span data-stu-id="9e709-378">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="9e709-379">分层值（节）：使用`:`（冒号）表示法或`GetSection`扩展方法。</span><span class="sxs-lookup"><span data-stu-id="9e709-379">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="9e709-380">使用以下任一方法获取配置值：</span><span class="sxs-lookup"><span data-stu-id="9e709-380">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="9e709-381">X.509 证书由操作系统管理。</span><span class="sxs-lookup"><span data-stu-id="9e709-381">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="9e709-382">应用调用<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>应用*设置.json*文件提供的值：</span><span class="sxs-lookup"><span data-stu-id="9e709-382">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="9e709-383">示例值：</span><span class="sxs-lookup"><span data-stu-id="9e709-383">Example values:</span></span>

* <span data-ttu-id="9e709-384">密钥保管库名称：`contosovault`</span><span class="sxs-lookup"><span data-stu-id="9e709-384">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="9e709-385">应用程序 ID：`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="9e709-385">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="9e709-386">证书指纹：`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="9e709-386">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="9e709-387">*应用程序设置.json*：</span><span class="sxs-lookup"><span data-stu-id="9e709-387">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="9e709-388">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="9e709-388">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="9e709-389">在开发环境中，机密值随后缀一`_dev`起加载。</span><span class="sxs-lookup"><span data-stu-id="9e709-389">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="9e709-390">在生产环境中，值使用`_prod`后缀加载。</span><span class="sxs-lookup"><span data-stu-id="9e709-390">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="9e709-391">对 Azure 资源使用托管标识</span><span class="sxs-lookup"><span data-stu-id="9e709-391">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="9e709-392">**部署到 Azure 的应用**可以利用 Azure[资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)，这允许应用使用 Azure 密钥保管库进行身份验证，而无需在应用中存储凭据（应用程序 ID 和密码/客户端密钥）。</span><span class="sxs-lookup"><span data-stu-id="9e709-392">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="9e709-393">当*Program.cs*文件顶部的语句设置为`Managed`时，`#define`示例应用对 Azure 资源使用托管标识。</span><span class="sxs-lookup"><span data-stu-id="9e709-393">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="9e709-394">在应用*的应用设置.json*文件中输入保管库名称。</span><span class="sxs-lookup"><span data-stu-id="9e709-394">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="9e709-395">当示例应用设置为`Managed`版本时，不需要应用程序 ID 和密码（客户端密钥），因此您可以忽略这些配置条目。</span><span class="sxs-lookup"><span data-stu-id="9e709-395">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="9e709-396">应用将部署到 Azure，Azure 会验证应用仅使用存储在*appsettings.json*文件中的保管库名称访问 Azure 密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="9e709-396">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="9e709-397">将示例应用部署到 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="9e709-397">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="9e709-398">创建服务时，部署到 Azure 应用服务的应用将自动注册到 Azure AD。</span><span class="sxs-lookup"><span data-stu-id="9e709-398">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="9e709-399">从部署中获取对象 ID，以便在以下命令中使用。</span><span class="sxs-lookup"><span data-stu-id="9e709-399">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="9e709-400">对象 ID 显示在应用服务的 **"标识"** 面板上的 Azure 门户中。</span><span class="sxs-lookup"><span data-stu-id="9e709-400">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="9e709-401">使用 Azure CLI 和应用的对象 ID，向应用提供`list`访问`get`密钥保管库的权限：</span><span class="sxs-lookup"><span data-stu-id="9e709-401">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="9e709-402">使用 Azure CLI、PowerShell 或 Azure 门户**重新启动应用**。</span><span class="sxs-lookup"><span data-stu-id="9e709-402">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="9e709-403">示例应用：</span><span class="sxs-lookup"><span data-stu-id="9e709-403">The sample app:</span></span>

* <span data-ttu-id="9e709-404">创建没有连接字符串的`AzureServiceTokenProvider`类实例。</span><span class="sxs-lookup"><span data-stu-id="9e709-404">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="9e709-405">未提供连接字符串时，提供程序将尝试从 Azure 资源的托管标识获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="9e709-405">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="9e709-406"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>使用`AzureServiceTokenProvider`实例令牌回调创建新。</span><span class="sxs-lookup"><span data-stu-id="9e709-406">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="9e709-407">实例<xref:Microsoft.Azure.KeyVault.KeyVaultClient>与加载所有机密值的默认实现<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>一起使用，并将双分线 （）`--`替换为键名中的冒号`:`（ ）。</span><span class="sxs-lookup"><span data-stu-id="9e709-407">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="9e709-408">密钥保管库名称示例值：`contosovault`</span><span class="sxs-lookup"><span data-stu-id="9e709-408">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="9e709-409">*应用程序设置.json*：</span><span class="sxs-lookup"><span data-stu-id="9e709-409">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="9e709-410">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="9e709-410">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="9e709-411">在开发环境中，机密值具有后缀`_dev`，因为它们由用户机密提供。</span><span class="sxs-lookup"><span data-stu-id="9e709-411">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="9e709-412">在生产环境中，值使用后缀加载，`_prod`因为它们由 Azure 密钥保管库提供。</span><span class="sxs-lookup"><span data-stu-id="9e709-412">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="9e709-413">如果收到错误`Access denied`，请确认应用已注册 Azure AD 并提供有关密钥保管库的访问权限。</span><span class="sxs-lookup"><span data-stu-id="9e709-413">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="9e709-414">确认已重新启动 Azure 中的服务。</span><span class="sxs-lookup"><span data-stu-id="9e709-414">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="9e709-415">有关将提供程序与托管标识和 Azure DevOps 管道一起使用的信息，请参阅创建 Azure[资源管理器服务连接到具有托管服务标识的 VM。](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)</span><span class="sxs-lookup"><span data-stu-id="9e709-415">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="9e709-416">使用键名称前缀</span><span class="sxs-lookup"><span data-stu-id="9e709-416">Use a key name prefix</span></span>

<span data-ttu-id="9e709-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>提供接受 的实现的<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>重载，它允许您控制如何将密钥保管库机密转换为配置密钥。</span><span class="sxs-lookup"><span data-stu-id="9e709-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="9e709-418">例如，您可以实现接口，以基于您在应用启动时提供的前缀值加载机密值。</span><span class="sxs-lookup"><span data-stu-id="9e709-418">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="9e709-419">例如，这允许您根据应用版本加载机密。</span><span class="sxs-lookup"><span data-stu-id="9e709-419">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="9e709-420">不要使用密钥保管库机密的前缀将多个应用的机密放入同一密钥保管库或将环境机密（例如 *，开发*与*生产*机密）放入同一保管库。</span><span class="sxs-lookup"><span data-stu-id="9e709-420">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="9e709-421">我们建议不同的应用和开发/生产环境使用单独的密钥保管库来隔离应用环境，以确保最高级别的安全性。</span><span class="sxs-lookup"><span data-stu-id="9e709-421">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="9e709-422">在下面的示例中，在密钥保管库中建立了机密（并使用开发环境的密钥管理器工具）（`5000-AppSecret`密钥保管库机密名称中不允许周期）。</span><span class="sxs-lookup"><span data-stu-id="9e709-422">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="9e709-423">此机密表示应用版本 5.0.0.0 的应用机密。</span><span class="sxs-lookup"><span data-stu-id="9e709-423">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="9e709-424">对于应用的另一个版本 5.1.0.0，将机密添加到 的密钥保管库（并使用机密管理器工具）中`5100-AppSecret`。</span><span class="sxs-lookup"><span data-stu-id="9e709-424">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="9e709-425">每个应用版本将其版本控制的秘密值加载到其配置中`AppSecret`，作为 ，在加载密钥时剥离版本。</span><span class="sxs-lookup"><span data-stu-id="9e709-425">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="9e709-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>使用自定义<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>调用 ：</span><span class="sxs-lookup"><span data-stu-id="9e709-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="9e709-427">实现<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>对机密的版本前缀做出反应，以将正确的机密加载到配置中：</span><span class="sxs-lookup"><span data-stu-id="9e709-427">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="9e709-428">`Load`当机密的名称以前缀开头时加载它。</span><span class="sxs-lookup"><span data-stu-id="9e709-428">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="9e709-429">其他机密未加载。</span><span class="sxs-lookup"><span data-stu-id="9e709-429">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="9e709-430">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="9e709-430">`GetKey`:</span></span>
  * <span data-ttu-id="9e709-431">从机密名称中删除前缀。</span><span class="sxs-lookup"><span data-stu-id="9e709-431">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="9e709-432">将任何名称中的两个破折号替换为`KeyDelimiter`， 是 配置中使用的分隔符（通常是冒号）。</span><span class="sxs-lookup"><span data-stu-id="9e709-432">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="9e709-433">Azure 密钥保管库不允许在机密名称中冒号。</span><span class="sxs-lookup"><span data-stu-id="9e709-433">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="9e709-434">该方法`Load`由提供程序算法调用，该算法通过保管库机密进行遍舍以查找具有版本前缀的提供程序算法。</span><span class="sxs-lookup"><span data-stu-id="9e709-434">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="9e709-435">使用 找到`Load`版本前缀时，算法使用 方法`GetKey`返回机密名称的配置名称。</span><span class="sxs-lookup"><span data-stu-id="9e709-435">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="9e709-436">它将版本前缀从机密的名称中剥离，并返回用于加载到应用的配置名称值对中的其他机密名称。</span><span class="sxs-lookup"><span data-stu-id="9e709-436">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="9e709-437">实施此方法时：</span><span class="sxs-lookup"><span data-stu-id="9e709-437">When this approach is implemented:</span></span>

1. <span data-ttu-id="9e709-438">应用在应用的项目文件中指定的版本。</span><span class="sxs-lookup"><span data-stu-id="9e709-438">The app's version specified in the app's project file.</span></span> <span data-ttu-id="9e709-439">在下面的示例中，应用的版本设置为`5.0.0.0`：</span><span class="sxs-lookup"><span data-stu-id="9e709-439">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="9e709-440">确认应用的项目`<UserSecretsId>`文件中存在属性，其中`{GUID}`是用户提供的 GUID：</span><span class="sxs-lookup"><span data-stu-id="9e709-440">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="9e709-441">使用[秘密管理器工具](xref:security/app-secrets)在本地保存以下机密：</span><span class="sxs-lookup"><span data-stu-id="9e709-441">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="9e709-442">机密使用以下 Azure CLI 命令保存在 Azure 密钥保管库中：</span><span class="sxs-lookup"><span data-stu-id="9e709-442">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="9e709-443">运行应用时，将加载密钥保管库机密。</span><span class="sxs-lookup"><span data-stu-id="9e709-443">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="9e709-444">的`5000-AppSecret`字符串机密与应用的项目文件 （）`5.0.0.0`中指定的应用版本匹配。</span><span class="sxs-lookup"><span data-stu-id="9e709-444">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="9e709-445">版本`5000`（使用破折号）从密钥名称中剥离。</span><span class="sxs-lookup"><span data-stu-id="9e709-445">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="9e709-446">在整个应用中，使用密钥`AppSecret`读取配置将加载机密值。</span><span class="sxs-lookup"><span data-stu-id="9e709-446">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="9e709-447">如果在项目文件中将应用的版本更改为，`5.1.0.0`并且应用再次运行，则返回的机密值位于`5.1.0.0_secret_value_dev`"开发"环境和`5.1.0.0_secret_value_prod`"生产"中。</span><span class="sxs-lookup"><span data-stu-id="9e709-447">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="9e709-448">您还可以向<xref:Microsoft.Azure.KeyVault.KeyVaultClient><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>提供您自己的实现。</span><span class="sxs-lookup"><span data-stu-id="9e709-448">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="9e709-449">自定义客户端允许跨应用共享客户端的单个实例。</span><span class="sxs-lookup"><span data-stu-id="9e709-449">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="9e709-450">将数组绑定至类</span><span class="sxs-lookup"><span data-stu-id="9e709-450">Bind an array to a class</span></span>

<span data-ttu-id="9e709-451">提供程序能够将配置值读取到数组中以绑定到 POCO 数组。</span><span class="sxs-lookup"><span data-stu-id="9e709-451">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="9e709-452">从允许键包含冒号`:`（） 分隔符的配置源读取时，使用数字键段来区分组成数组 （`:0:`的`:1:`&hellip;`:{n}:`键 ） 。</span><span class="sxs-lookup"><span data-stu-id="9e709-452">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="9e709-453">有关详细信息，请参阅[配置：将数组绑定到类](xref:fundamentals/configuration/index#bind-an-array-to-a-class)。</span><span class="sxs-lookup"><span data-stu-id="9e709-453">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="9e709-454">Azure 密钥保管库密钥不能将冒号用作分隔符。</span><span class="sxs-lookup"><span data-stu-id="9e709-454">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="9e709-455">本主题中描述的方法使用双破折号 （）`--`作为分层值（节）的分隔符。</span><span class="sxs-lookup"><span data-stu-id="9e709-455">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="9e709-456">数组键存储在 Azure 密钥保管库中，具有双破折号和数字键段`--0--` `--1--`（， &hellip; `--{n}--`。 。</span><span class="sxs-lookup"><span data-stu-id="9e709-456">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="9e709-457">检查 JSON 文件提供的以下[Serilog](https://serilog.net/)日志记录提供程序配置。</span><span class="sxs-lookup"><span data-stu-id="9e709-457">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="9e709-458">`WriteTo`数组中定义了两个对象文本，反映了两个 Serilog*接收器*，它们描述日志记录输出的目标：</span><span class="sxs-lookup"><span data-stu-id="9e709-458">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="9e709-459">前面 JSON 文件中显示的配置使用双破折号 （）`--`表示法和数字段存储在 Azure 密钥保管库中：</span><span class="sxs-lookup"><span data-stu-id="9e709-459">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="9e709-460">密钥</span><span class="sxs-lookup"><span data-stu-id="9e709-460">Key</span></span> | <span data-ttu-id="9e709-461">值</span><span class="sxs-lookup"><span data-stu-id="9e709-461">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="9e709-462">重新加载机密</span><span class="sxs-lookup"><span data-stu-id="9e709-462">Reload secrets</span></span>

<span data-ttu-id="9e709-463">机密被缓存，直到`IConfigurationRoot.Reload()`被调用。</span><span class="sxs-lookup"><span data-stu-id="9e709-463">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="9e709-464">在执行密钥保管库之前`Reload`，应用不会尊重密钥保管库中过期、禁用和更新的机密。</span><span class="sxs-lookup"><span data-stu-id="9e709-464">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="9e709-465">已禁用和过期的机密</span><span class="sxs-lookup"><span data-stu-id="9e709-465">Disabled and expired secrets</span></span>

<span data-ttu-id="9e709-466">禁用和过期的机密引发<xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>。</span><span class="sxs-lookup"><span data-stu-id="9e709-466">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="9e709-467">要防止应用引发，请使用其他配置提供程序提供配置或更新禁用或过期的机密。</span><span class="sxs-lookup"><span data-stu-id="9e709-467">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="9e709-468">疑难解答</span><span class="sxs-lookup"><span data-stu-id="9e709-468">Troubleshoot</span></span>

<span data-ttu-id="9e709-469">当应用无法使用提供程序加载配置时，将写入[ASP.NET核心日志记录基础结构](xref:fundamentals/logging/index)中的错误消息。</span><span class="sxs-lookup"><span data-stu-id="9e709-469">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="9e709-470">以下条件将阻止加载配置：</span><span class="sxs-lookup"><span data-stu-id="9e709-470">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="9e709-471">应用或证书未在 Azure 活动目录中正确配置。</span><span class="sxs-lookup"><span data-stu-id="9e709-471">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="9e709-472">密钥保管库在 Azure 密钥保管库中不存在。</span><span class="sxs-lookup"><span data-stu-id="9e709-472">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="9e709-473">应用程序无权访问密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="9e709-473">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="9e709-474">访问策略不包括`Get`和`List`权限。</span><span class="sxs-lookup"><span data-stu-id="9e709-474">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="9e709-475">在密钥保管库中，配置数据（名称值对）被错误地命名、丢失、禁用或过期。</span><span class="sxs-lookup"><span data-stu-id="9e709-475">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="9e709-476">应用具有错误的密钥保管库名称 （`KeyVaultName`）、Azure AD`AzureADApplicationId`应用程序 ID （） 或`AzureADCertThumbprint`Azure AD 证书指纹 （）。</span><span class="sxs-lookup"><span data-stu-id="9e709-476">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="9e709-477">对于要加载的值，配置键（名称）在应用中不正确。</span><span class="sxs-lookup"><span data-stu-id="9e709-477">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="9e709-478">将应用的访问策略添加到密钥保管库时，将创建该策略，但在**Access 策略**UI 中未选择 **"保存**"按钮。</span><span class="sxs-lookup"><span data-stu-id="9e709-478">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e709-479">其他资源</span><span class="sxs-lookup"><span data-stu-id="9e709-479">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="9e709-480">微软 Azure：密钥保管库</span><span class="sxs-lookup"><span data-stu-id="9e709-480">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="9e709-481">微软 Azure：密钥保管库文档</span><span class="sxs-lookup"><span data-stu-id="9e709-481">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="9e709-482">如何为 Azure 密钥保管库生成和传输受 HSM 保护的密钥</span><span class="sxs-lookup"><span data-stu-id="9e709-482">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="9e709-483">密钥库客户端类</span><span class="sxs-lookup"><span data-stu-id="9e709-483">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="9e709-484">快速入门：使用 .NET Web 应用在 Azure Key Vault 中设置和检索机密</span><span class="sxs-lookup"><span data-stu-id="9e709-484">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="9e709-485">教程：如何将 Azure Key Vault 与通过 .NET 编写的 Azure Windows 虚拟机配合使用</span><span class="sxs-lookup"><span data-stu-id="9e709-485">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

