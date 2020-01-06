---
title: ASP.NET Core 中的 Azure Key Vault 配置提供程序
author: guardrex
description: 了解如何使用 Azure Key Vault 配置提供程序通过在运行时加载的名称/值对来配置应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/16/2019
uid: security/key-vault-configuration
ms.openlocfilehash: 37ba756cc4170c145d2ab1f9f0a465057cc826c1
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358703"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="e82ed-103">ASP.NET Core 中的 Azure Key Vault 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="e82ed-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="e82ed-104">作者： [Luke Latham](https://github.com/guardrex)和[Andrew Stanton](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="e82ed-104">By [Luke Latham](https://github.com/guardrex) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="e82ed-105">本文档说明如何使用[Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/)配置提供程序从 Azure Key Vault 机密加载应用配置值。</span><span class="sxs-lookup"><span data-stu-id="e82ed-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="e82ed-106">Azure Key Vault 是一项基于云的服务，可帮助保护应用程序和服务使用的加密密钥和机密。</span><span class="sxs-lookup"><span data-stu-id="e82ed-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="e82ed-107">将 Azure Key Vault 用于 ASP.NET Core 应用的常见方案包括：</span><span class="sxs-lookup"><span data-stu-id="e82ed-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="e82ed-108">控制对敏感配置数据的访问。</span><span class="sxs-lookup"><span data-stu-id="e82ed-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="e82ed-109">在存储配置数据时满足 FIPS 140-2 级别2验证的硬件安全模块（HSM）的要求。</span><span class="sxs-lookup"><span data-stu-id="e82ed-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="e82ed-110">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="e82ed-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="e82ed-111">package</span><span class="sxs-lookup"><span data-stu-id="e82ed-111">Packages</span></span>

<span data-ttu-id="e82ed-112">将包引用添加到[AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)包。</span><span class="sxs-lookup"><span data-stu-id="e82ed-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="e82ed-113">示例应用</span><span class="sxs-lookup"><span data-stu-id="e82ed-113">Sample app</span></span>

<span data-ttu-id="e82ed-114">该示例应用在*Program.cs*文件顶部的 `#define` 语句确定的两种模式中运行：</span><span class="sxs-lookup"><span data-stu-id="e82ed-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="e82ed-115">`Certificate` &ndash; 演示了如何使用 Azure Key Vault 的客户端 ID 和 x.509 证书来访问存储在 Azure Key Vault 中的机密。</span><span class="sxs-lookup"><span data-stu-id="e82ed-115">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="e82ed-116">可以从部署到 Azure App Service 的任何位置运行此版本的示例，也可以从部署到 ASP.NET Core 应用程序的任何主机上运行。</span><span class="sxs-lookup"><span data-stu-id="e82ed-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="e82ed-117">`Managed` &ndash; 演示了如何使用[Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)对应用进行身份验证，以使用 Azure AD 身份验证 Azure Key Vault，而不会在应用的代码或配置中存储凭据。</span><span class="sxs-lookup"><span data-stu-id="e82ed-117">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="e82ed-118">使用托管标识进行身份验证时，不需要 Azure AD 的应用程序 ID 和密码（客户端机密）。</span><span class="sxs-lookup"><span data-stu-id="e82ed-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="e82ed-119">必须将示例的 `Managed` 版本部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="e82ed-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="e82ed-120">按照[使用 Azure 资源的托管标识](#use-managed-identities-for-azure-resources)部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="e82ed-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="e82ed-121">有关如何使用预处理器指令（`#define`）配置示例应用的详细信息，请参阅 <xref:index#preprocessor-directives-in-sample-code>。</span><span class="sxs-lookup"><span data-stu-id="e82ed-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="e82ed-122">开发环境中的机密存储</span><span class="sxs-lookup"><span data-stu-id="e82ed-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="e82ed-123">使用[机密管理器工具](xref:security/app-secrets)在本地设置机密。</span><span class="sxs-lookup"><span data-stu-id="e82ed-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="e82ed-124">当在开发环境中的本地计算机上运行示例应用时，会从本地密钥管理器存储中加载密码。</span><span class="sxs-lookup"><span data-stu-id="e82ed-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="e82ed-125">机密管理器工具需要应用的项目文件中的 `<UserSecretsId>` 属性。</span><span class="sxs-lookup"><span data-stu-id="e82ed-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="e82ed-126">将属性值（`{GUID}`）设置为任何唯一 GUID：</span><span class="sxs-lookup"><span data-stu-id="e82ed-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="e82ed-127">机密以名称-值对的形式创建。</span><span class="sxs-lookup"><span data-stu-id="e82ed-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="e82ed-128">分层值（配置节）使用 `:` （冒号）作为[ASP.NET Core 配置](xref:fundamentals/configuration/index)项名称中的分隔符。</span><span class="sxs-lookup"><span data-stu-id="e82ed-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="e82ed-129">机密管理器是从打开到项目[内容根目录](xref:fundamentals/index#content-root)的命令 shell 使用的，其中 `{SECRET NAME}` 是名称，`{SECRET VALUE}` 是值：</span><span class="sxs-lookup"><span data-stu-id="e82ed-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="e82ed-130">从项目的[内容根](xref:fundamentals/index#content-root)在命令外壳中执行以下命令，为示例应用设置机密：</span><span class="sxs-lookup"><span data-stu-id="e82ed-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="e82ed-131">如果这些机密存储在[生产环境中的机密存储 Azure Key Vault 中，并具有 Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault)部分，则 `_dev` 后缀将更改为 `_prod`。</span><span class="sxs-lookup"><span data-stu-id="e82ed-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="e82ed-132">后缀在应用的输出中提供视觉提示，指示配置值的源。</span><span class="sxs-lookup"><span data-stu-id="e82ed-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="e82ed-133">Azure Key Vault 中的生产环境中的机密存储</span><span class="sxs-lookup"><span data-stu-id="e82ed-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="e82ed-134">本[快速入门：使用 Azure CLI 主题的 Azure Key Vault 设置和检索机密](/azure/key-vault/quick-create-cli)的说明，请参阅此处，以创建 Azure Key Vault 和存储示例应用使用的机密。</span><span class="sxs-lookup"><span data-stu-id="e82ed-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="e82ed-135">有关更多详细信息，请参阅主题。</span><span class="sxs-lookup"><span data-stu-id="e82ed-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="e82ed-136">使用[Azure 门户](https://portal.azure.com/)中的以下方法之一打开 Azure Cloud shell：</span><span class="sxs-lookup"><span data-stu-id="e82ed-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="e82ed-137">选择代码块右上角的“试用”。</span><span class="sxs-lookup"><span data-stu-id="e82ed-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="e82ed-138">在文本框中使用搜索字符串 "Azure CLI"。</span><span class="sxs-lookup"><span data-stu-id="e82ed-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="e82ed-139">在浏览器中打开 Cloud Shell，并提供 "**启动 Cloud Shell** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="e82ed-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="e82ed-140">选择 Azure 门户右上角菜单上的“Cloud Shell”**Cloud Shell**按钮。</span><span class="sxs-lookup"><span data-stu-id="e82ed-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="e82ed-141">有关详细信息，请参阅[Azure 命令行接口（CLI）](/cli/azure/)和[Azure Cloud Shell 概述](/azure/cloud-shell/overview)。</span><span class="sxs-lookup"><span data-stu-id="e82ed-141">For more information, see [Azure Command-Line Interface (CLI)](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="e82ed-142">如果尚未通过身份验证，请在 `az login` 命令中登录。</span><span class="sxs-lookup"><span data-stu-id="e82ed-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="e82ed-143">使用以下命令创建资源组，其中 `{RESOURCE GROUP NAME}` 是新资源组的资源组名称，`{LOCATION}` 是 Azure 区域（datacenter）：</span><span class="sxs-lookup"><span data-stu-id="e82ed-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azure-cli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="e82ed-144">使用以下命令在资源组中创建密钥保管库，其中 `{KEY VAULT NAME}` 是新密钥保管库的名称，`{LOCATION}` 是 Azure 区域（datacenter）：</span><span class="sxs-lookup"><span data-stu-id="e82ed-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azure-cli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="e82ed-145">在密钥保管库中，以名称-值对的形式创建机密。</span><span class="sxs-lookup"><span data-stu-id="e82ed-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="e82ed-146">Azure Key Vault 机密名称仅限字母数字字符和短划线。</span><span class="sxs-lookup"><span data-stu-id="e82ed-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="e82ed-147">分层值（配置节）使用 `--` （两个短划线）作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="e82ed-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="e82ed-148">冒号（通常用于在[ASP.NET Core 配置](xref:fundamentals/configuration/index)中的子项中分隔部分）在 key vault 机密名称中不允许使用。</span><span class="sxs-lookup"><span data-stu-id="e82ed-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="e82ed-149">因此，当机密加载到应用的配置中时，将使用两个短划线并为冒号交换。</span><span class="sxs-lookup"><span data-stu-id="e82ed-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="e82ed-150">以下机密用于示例应用。</span><span class="sxs-lookup"><span data-stu-id="e82ed-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="e82ed-151">这些值包括一个 `_prod` 后缀，以将其与从用户机密的开发环境中加载的 `_dev` 后缀值区分开来。</span><span class="sxs-lookup"><span data-stu-id="e82ed-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="e82ed-152">将 `{KEY VAULT NAME}` 替换为在上一步中创建的密钥保管库的名称：</span><span class="sxs-lookup"><span data-stu-id="e82ed-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azure-cli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="e82ed-153">为非 Azure 托管的应用使用应用程序 ID 和 x.509 证书</span><span class="sxs-lookup"><span data-stu-id="e82ed-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="e82ed-154">配置 Azure AD、Azure Key Vault 和应用，以便在**Azure 外托管应用时**使用 Azure Active Directory 应用程序 ID 和 x.509 证书对密钥保管库进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="e82ed-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="e82ed-155">有关详细信息，请参阅[关于密钥、机密和证书](/azure/key-vault/about-keys-secrets-and-certificates)。</span><span class="sxs-lookup"><span data-stu-id="e82ed-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="e82ed-156">尽管在 Azure 中托管的应用程序支持使用应用程序 ID 和 x.509 证书，但在 Azure 中托管应用程序时，我们建议使用[Azure 资源的托管标识](#use-managed-identities-for-azure-resources)。</span><span class="sxs-lookup"><span data-stu-id="e82ed-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="e82ed-157">托管标识不需要在应用或开发环境中存储证书。</span><span class="sxs-lookup"><span data-stu-id="e82ed-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="e82ed-158">当*Program.cs*文件顶部的 `#define` 语句设置为 `Certificate`时，示例应用使用应用程序 ID 和 x.509 证书。</span><span class="sxs-lookup"><span data-stu-id="e82ed-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="e82ed-159">创建 PKCS # 12 存档（ *.pfx*）证书。</span><span class="sxs-lookup"><span data-stu-id="e82ed-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="e82ed-160">用于创建证书的选项包括 Windows 和[OpenSSL](https://www.openssl.org/)[上的 MakeCert](/windows/desktop/seccrypto/makecert) 。</span><span class="sxs-lookup"><span data-stu-id="e82ed-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="e82ed-161">将证书安装到当前用户的个人证书存储中。</span><span class="sxs-lookup"><span data-stu-id="e82ed-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="e82ed-162">将密钥标记为可导出是可选的。</span><span class="sxs-lookup"><span data-stu-id="e82ed-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="e82ed-163">记下证书的指纹，此过程将在此过程中使用。</span><span class="sxs-lookup"><span data-stu-id="e82ed-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="e82ed-164">将 PKCS # 12 存档（ *.pfx*）证书导出为 DER 编码的证书（ *.cer*）。</span><span class="sxs-lookup"><span data-stu-id="e82ed-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="e82ed-165">向 Azure AD （**应用注册**）注册应用。</span><span class="sxs-lookup"><span data-stu-id="e82ed-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="e82ed-166">将 DER 编码的证书（ *.cer*）上载到 Azure AD：</span><span class="sxs-lookup"><span data-stu-id="e82ed-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="e82ed-167">在 Azure AD 中选择应用。</span><span class="sxs-lookup"><span data-stu-id="e82ed-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="e82ed-168">导航到 "**证书" & "机密**"。</span><span class="sxs-lookup"><span data-stu-id="e82ed-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="e82ed-169">选择 "**上传证书**"，上传包含公钥的证书。</span><span class="sxs-lookup"><span data-stu-id="e82ed-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="e82ed-170">*.Cer*、 *pem*或 *.crt*证书是可接受的。</span><span class="sxs-lookup"><span data-stu-id="e82ed-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="e82ed-171">将密钥保管库名称、应用程序 ID 和证书指纹存储在应用的*appsettings*文件中。</span><span class="sxs-lookup"><span data-stu-id="e82ed-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="e82ed-172">导航到 Azure 门户中的**密钥保管库**。</span><span class="sxs-lookup"><span data-stu-id="e82ed-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="e82ed-173">选择在[生产环境中的机密存储中](#secret-storage-in-the-production-environment-with-azure-key-vault)创建的密钥保管库，其中 Azure Key Vault "部分。</span><span class="sxs-lookup"><span data-stu-id="e82ed-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="e82ed-174">选择“访问策略”。</span><span class="sxs-lookup"><span data-stu-id="e82ed-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="e82ed-175">选择 "**添加访问策略**"。</span><span class="sxs-lookup"><span data-stu-id="e82ed-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="e82ed-176">打开**机密权限**，并为应用提供**Get**和**List**权限。</span><span class="sxs-lookup"><span data-stu-id="e82ed-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="e82ed-177">选择 "**选择主体**"，并按名称选择注册的应用。</span><span class="sxs-lookup"><span data-stu-id="e82ed-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="e82ed-178">选择“选择”按钮。</span><span class="sxs-lookup"><span data-stu-id="e82ed-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="e82ed-179">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="e82ed-179">Select **OK**.</span></span>
1. <span data-ttu-id="e82ed-180">选择“保存”。</span><span class="sxs-lookup"><span data-stu-id="e82ed-180">Select **Save**.</span></span>
1. <span data-ttu-id="e82ed-181">部署应用程序。</span><span class="sxs-lookup"><span data-stu-id="e82ed-181">Deploy the app.</span></span>

<span data-ttu-id="e82ed-182">`Certificate` 示例应用从 `IConfigurationRoot` 中获取其配置值，名称与机密名称相同：</span><span class="sxs-lookup"><span data-stu-id="e82ed-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="e82ed-183">非分层值： `SecretName` 的值是通过 `config["SecretName"]`获取的。</span><span class="sxs-lookup"><span data-stu-id="e82ed-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="e82ed-184">分层值（节）：使用 `:` （冒号）表示法或 `GetSection` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="e82ed-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="e82ed-185">使用以下任一方法来获取配置值：</span><span class="sxs-lookup"><span data-stu-id="e82ed-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="e82ed-186">X.509 证书由操作系统管理。</span><span class="sxs-lookup"><span data-stu-id="e82ed-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="e82ed-187">应用使用*appsettings*文件提供的值调用 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>：</span><span class="sxs-lookup"><span data-stu-id="e82ed-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

::: moniker-end

<span data-ttu-id="e82ed-188">示例值：</span><span class="sxs-lookup"><span data-stu-id="e82ed-188">Example values:</span></span>

* <span data-ttu-id="e82ed-189">密钥保管库名称： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="e82ed-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="e82ed-190">应用程序 ID： `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="e82ed-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="e82ed-191">证书指纹： `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="e82ed-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="e82ed-192">appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="e82ed-192">*appsettings.json*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

::: moniker-end

<span data-ttu-id="e82ed-193">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="e82ed-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="e82ed-194">在开发环境中，用 `_dev` 后缀加载机密值。</span><span class="sxs-lookup"><span data-stu-id="e82ed-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="e82ed-195">在生产环境中，值以 `_prod` 后缀进行加载。</span><span class="sxs-lookup"><span data-stu-id="e82ed-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="e82ed-196">使用 Azure 资源的托管标识</span><span class="sxs-lookup"><span data-stu-id="e82ed-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="e82ed-197">**部署到 azure 的应用**可以利用[Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)，这允许应用通过使用 Azure AD 身份验证而不使用应用中存储的凭据（应用程序 ID 和密码/客户端密码）进行身份验证 Azure Key Vault。</span><span class="sxs-lookup"><span data-stu-id="e82ed-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="e82ed-198">当*Program.cs*文件顶部的 `#define` 语句设置为 `Managed`时，示例应用使用 Azure 资源的托管标识。</span><span class="sxs-lookup"><span data-stu-id="e82ed-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="e82ed-199">在应用的*appsettings*文件中输入保管库名称。</span><span class="sxs-lookup"><span data-stu-id="e82ed-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="e82ed-200">设置为 `Managed` 版本时，示例应用不需要应用程序 ID 和密码（客户端密码），因此可以忽略这些配置条目。</span><span class="sxs-lookup"><span data-stu-id="e82ed-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="e82ed-201">应用将部署到 Azure，Azure 将对应用进行身份验证，以便仅使用存储在*appsettings*文件中的保管库名称访问 Azure Key Vault。</span><span class="sxs-lookup"><span data-stu-id="e82ed-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="e82ed-202">将示例应用部署到 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="e82ed-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="e82ed-203">在创建服务时，会自动向 Azure AD 注册部署到 Azure App Service 的应用。</span><span class="sxs-lookup"><span data-stu-id="e82ed-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="e82ed-204">从部署中获取对象 ID 以在下面的命令中使用。</span><span class="sxs-lookup"><span data-stu-id="e82ed-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="e82ed-205">对象 ID 显示在应用服务的 "**标识**" 面板上的 "Azure 门户中。</span><span class="sxs-lookup"><span data-stu-id="e82ed-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="e82ed-206">使用 Azure CLI 和应用程序的对象 ID，为应用程序提供 `list` 和 `get` 权限，以访问密钥保管库：</span><span class="sxs-lookup"><span data-stu-id="e82ed-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azure-cli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="e82ed-207">使用 Azure CLI、PowerShell 或 Azure 门户**重启应用**。</span><span class="sxs-lookup"><span data-stu-id="e82ed-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="e82ed-208">示例应用：</span><span class="sxs-lookup"><span data-stu-id="e82ed-208">The sample app:</span></span>

* <span data-ttu-id="e82ed-209">创建不带连接字符串的 `AzureServiceTokenProvider` 类的实例。</span><span class="sxs-lookup"><span data-stu-id="e82ed-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="e82ed-210">如果未提供连接字符串，提供程序将尝试从 Azure 资源的托管标识获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="e82ed-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="e82ed-211">使用 `AzureServiceTokenProvider` 实例令牌回调创建新 <xref:Microsoft.Azure.KeyVault.KeyVaultClient>。</span><span class="sxs-lookup"><span data-stu-id="e82ed-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="e82ed-212"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> 实例与加载所有机密值的 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 的默认实现一起使用，并将键名称中的双破折号（`--`）替换为冒号（`:`）。</span><span class="sxs-lookup"><span data-stu-id="e82ed-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

::: moniker-end

<span data-ttu-id="e82ed-213">Key vault 名称示例值： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="e82ed-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="e82ed-214">appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="e82ed-214">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="e82ed-215">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="e82ed-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="e82ed-216">在开发环境中，机密值具有 `_dev` 后缀，因为它们是由用户机密提供的。</span><span class="sxs-lookup"><span data-stu-id="e82ed-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="e82ed-217">在生产环境中，值使用 `_prod` 后缀加载，因为它们由 Azure Key Vault 提供。</span><span class="sxs-lookup"><span data-stu-id="e82ed-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="e82ed-218">如果收到 `Access denied` 错误，请确认已向应用程序注册了 Azure AD 并提供了对密钥保管库的访问权限。</span><span class="sxs-lookup"><span data-stu-id="e82ed-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="e82ed-219">确认已在 Azure 中重新启动该服务。</span><span class="sxs-lookup"><span data-stu-id="e82ed-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="e82ed-220">有关将提供程序与托管标识和 Azure DevOps 管道一起使用的信息，请参阅使用[托管服务标识创建与 VM 的 Azure 资源管理器服务连接](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)。</span><span class="sxs-lookup"><span data-stu-id="e82ed-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configuration-options"></a><span data-ttu-id="e82ed-221">配置选项</span><span class="sxs-lookup"><span data-stu-id="e82ed-221">Configuration options</span></span>

<span data-ttu-id="e82ed-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 可以接受 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>：</span><span class="sxs-lookup"><span data-stu-id="e82ed-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="e82ed-223">Property</span><span class="sxs-lookup"><span data-stu-id="e82ed-223">Property</span></span>         | <span data-ttu-id="e82ed-224">描述</span><span class="sxs-lookup"><span data-stu-id="e82ed-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="e82ed-225">用于检索值的 <xref:Microsoft.Azure.KeyVault.KeyVaultClient>。</span><span class="sxs-lookup"><span data-stu-id="e82ed-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="e82ed-226">用于控制机密加载的 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 实例。</span><span class="sxs-lookup"><span data-stu-id="e82ed-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="e82ed-227">`Timespan` 在轮询密钥保管库进行更改时等待。</span><span class="sxs-lookup"><span data-stu-id="e82ed-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="e82ed-228">默认值为 `null` （配置不会重新加载）。</span><span class="sxs-lookup"><span data-stu-id="e82ed-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="e82ed-229">密钥保管库 URI。</span><span class="sxs-lookup"><span data-stu-id="e82ed-229">Key vault URI.</span></span> |

::: moniker-end

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="e82ed-230">使用密钥名称前缀</span><span class="sxs-lookup"><span data-stu-id="e82ed-230">Use a key name prefix</span></span>

<span data-ttu-id="e82ed-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 提供一个重载，该重载接受 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>的实现，这允许你控制密钥保管库机密如何转换为配置密钥。</span><span class="sxs-lookup"><span data-stu-id="e82ed-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="e82ed-232">例如，你可以实现接口，以便基于在应用启动时提供的前缀值加载机密值。</span><span class="sxs-lookup"><span data-stu-id="e82ed-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="e82ed-233">例如，你可以根据应用程序的版本加载机密。</span><span class="sxs-lookup"><span data-stu-id="e82ed-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="e82ed-234">不要对 key vault 机密使用前缀，将多个应用的机密放入同一密钥保管库，或将环境机密（例如，*开发*与*生产*机密）放入同一保管库中。</span><span class="sxs-lookup"><span data-stu-id="e82ed-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="e82ed-235">建议不同的应用和开发/生产环境使用不同的密钥保管库，以便隔离应用环境以获得最高级别的安全性。</span><span class="sxs-lookup"><span data-stu-id="e82ed-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="e82ed-236">在下面的示例中，在密钥保管库中建立了机密（并使用开发环境的机密管理器工具）进行 `5000-AppSecret` （密钥保管库机密名称中不允许使用句点）。</span><span class="sxs-lookup"><span data-stu-id="e82ed-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="e82ed-237">此机密代表应用程序版本5.0.0.0 的应用程序机密。</span><span class="sxs-lookup"><span data-stu-id="e82ed-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="e82ed-238">对于其他版本的应用，5.1.0.0，会将机密添加到密钥保管库（并使用机密管理器工具）进行 `5100-AppSecret`。</span><span class="sxs-lookup"><span data-stu-id="e82ed-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="e82ed-239">每个应用版本会将其版本控制的机密值作为 `AppSecret`加载到其配置中，并在加载机密时将版本去除。</span><span class="sxs-lookup"><span data-stu-id="e82ed-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="e82ed-240">使用自定义 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>调用 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>：</span><span class="sxs-lookup"><span data-stu-id="e82ed-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="e82ed-241"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 实现将对机密的版本前缀做出反应，以将适当的机密加载到配置中：</span><span class="sxs-lookup"><span data-stu-id="e82ed-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="e82ed-242">`Load` 在其名称以前缀开头时加载密码。</span><span class="sxs-lookup"><span data-stu-id="e82ed-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="e82ed-243">未加载其他机密。</span><span class="sxs-lookup"><span data-stu-id="e82ed-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="e82ed-244">`GetKey`：</span><span class="sxs-lookup"><span data-stu-id="e82ed-244">`GetKey`:</span></span>
  * <span data-ttu-id="e82ed-245">从机密名称中删除前缀。</span><span class="sxs-lookup"><span data-stu-id="e82ed-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="e82ed-246">用 `KeyDelimiter`替换任意名称中的两个短划线，这是在配置中使用的分隔符（通常为冒号）。</span><span class="sxs-lookup"><span data-stu-id="e82ed-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="e82ed-247">Azure Key Vault 不允许在机密名称中使用冒号。</span><span class="sxs-lookup"><span data-stu-id="e82ed-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="e82ed-248">`Load` 方法由提供程序算法调用，该算法会循环访问保管库机密，查找具有版本前缀的文件。</span><span class="sxs-lookup"><span data-stu-id="e82ed-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="e82ed-249">在 `Load`找到版本前缀后，该算法使用 `GetKey` 方法返回密钥名称的配置名称。</span><span class="sxs-lookup"><span data-stu-id="e82ed-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="e82ed-250">它从机密名称中去除版本前缀，并返回密钥名称的其余部分，以便加载到应用的配置名称-值对中。</span><span class="sxs-lookup"><span data-stu-id="e82ed-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="e82ed-251">实现此方法时：</span><span class="sxs-lookup"><span data-stu-id="e82ed-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="e82ed-252">应用的项目文件中指定的应用版本。</span><span class="sxs-lookup"><span data-stu-id="e82ed-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="e82ed-253">在以下示例中，应用的版本设置为 `5.0.0.0`：</span><span class="sxs-lookup"><span data-stu-id="e82ed-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="e82ed-254">确认应用的项目文件中存在 `<UserSecretsId>` 属性，其中 `{GUID}` 是用户提供的 GUID：</span><span class="sxs-lookup"><span data-stu-id="e82ed-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="e82ed-255">用[机密管理器工具](xref:security/app-secrets)本地保存以下机密：</span><span class="sxs-lookup"><span data-stu-id="e82ed-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="e82ed-256">使用以下 Azure CLI 命令在 Azure Key Vault 中保存机密：</span><span class="sxs-lookup"><span data-stu-id="e82ed-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azure-cli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="e82ed-257">当应用运行时，将加载密钥保管库机密。</span><span class="sxs-lookup"><span data-stu-id="e82ed-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="e82ed-258">`5000-AppSecret` 的字符串机密与应用程序的项目文件（`5.0.0.0`）中指定的应用程序版本相匹配。</span><span class="sxs-lookup"><span data-stu-id="e82ed-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="e82ed-259">将从键名称中去除版本 `5000` （带有短划线）。</span><span class="sxs-lookup"><span data-stu-id="e82ed-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="e82ed-260">在整个应用程序中，读取带有密钥的配置 `AppSecret` 加载机密值。</span><span class="sxs-lookup"><span data-stu-id="e82ed-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="e82ed-261">如果在项目文件中将应用程序的版本更改为 `5.1.0.0` 并再次运行该应用程序，则返回的机密值将 `5.1.0.0_secret_value_dev` 在开发环境中，并 `5.1.0.0_secret_value_prod` 生产环境中。</span><span class="sxs-lookup"><span data-stu-id="e82ed-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="e82ed-262">你还可以提供自己的 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 实现以 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>。</span><span class="sxs-lookup"><span data-stu-id="e82ed-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="e82ed-263">自定义客户端允许跨应用共享客户端的单个实例。</span><span class="sxs-lookup"><span data-stu-id="e82ed-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="e82ed-264">将数组绑定至类</span><span class="sxs-lookup"><span data-stu-id="e82ed-264">Bind an array to a class</span></span>

<span data-ttu-id="e82ed-265">提供程序能够将配置值读入数组，以便绑定到 POCO 数组。</span><span class="sxs-lookup"><span data-stu-id="e82ed-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="e82ed-266">从允许键包含冒号（`:`）分隔符的配置源中进行读取时，将使用数字键段来区分组成数组的键（`:0:`、`:1:`。</span><span class="sxs-lookup"><span data-stu-id="e82ed-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, …</span></span> <span data-ttu-id="e82ed-267">`:{n}:`) 格式模式中出现的位置匹配。</span><span class="sxs-lookup"><span data-stu-id="e82ed-267">`:{n}:`).</span></span> <span data-ttu-id="e82ed-268">有关详细信息，请参阅[配置：将数组绑定到类](xref:fundamentals/configuration/index#bind-an-array-to-a-class)。</span><span class="sxs-lookup"><span data-stu-id="e82ed-268">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="e82ed-269">Azure Key Vault 密钥不能使用冒号作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="e82ed-269">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="e82ed-270">本主题中所述的方法使用双短划线（`--`）作为层次结构值（节）的分隔符。</span><span class="sxs-lookup"><span data-stu-id="e82ed-270">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="e82ed-271">数组键存储在具有双短划线和数值段（`--0--`、`--1--`&hellip; `--{n}--`） Azure Key Vault 中。</span><span class="sxs-lookup"><span data-stu-id="e82ed-271">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="e82ed-272">检查 JSON 文件提供的以下[Serilog](https://serilog.net/)日志记录提供程序配置。</span><span class="sxs-lookup"><span data-stu-id="e82ed-272">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="e82ed-273">`WriteTo` 数组中定义了两个对象文本，该对象反映了两个 Serilog*接收器*，它们描述了日志记录输出的目标：</span><span class="sxs-lookup"><span data-stu-id="e82ed-273">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="e82ed-274">使用双短划线（`--`）表示法和数值段 Azure Key Vault 前面的 JSON 文件中所示的配置：</span><span class="sxs-lookup"><span data-stu-id="e82ed-274">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="e82ed-275">键</span><span class="sxs-lookup"><span data-stu-id="e82ed-275">Key</span></span> | <span data-ttu-id="e82ed-276">{2&gt;值&lt;2}</span><span class="sxs-lookup"><span data-stu-id="e82ed-276">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="e82ed-277">重载机密</span><span class="sxs-lookup"><span data-stu-id="e82ed-277">Reload secrets</span></span>

<span data-ttu-id="e82ed-278">在调用 `IConfigurationRoot.Reload()` 之前会缓存机密。</span><span class="sxs-lookup"><span data-stu-id="e82ed-278">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="e82ed-279">在执行 `Reload` 之前，应用程序不会考虑到密钥保管库中已过期、已禁用和更新的机密。</span><span class="sxs-lookup"><span data-stu-id="e82ed-279">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="e82ed-280">禁用和过期的机密</span><span class="sxs-lookup"><span data-stu-id="e82ed-280">Disabled and expired secrets</span></span>

<span data-ttu-id="e82ed-281">禁用和过期的机密会引发 <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>。</span><span class="sxs-lookup"><span data-stu-id="e82ed-281">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="e82ed-282">若要防止应用程序引发，请使用不同的配置提供程序提供配置，或更新禁用或过期的机密。</span><span class="sxs-lookup"><span data-stu-id="e82ed-282">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="e82ed-283">疑难解答</span><span class="sxs-lookup"><span data-stu-id="e82ed-283">Troubleshoot</span></span>

<span data-ttu-id="e82ed-284">当应用无法使用访问接口加载配置时，会将错误消息写入[ASP.NET Core 日志记录基础结构](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="e82ed-284">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="e82ed-285">以下条件将阻止加载配置：</span><span class="sxs-lookup"><span data-stu-id="e82ed-285">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="e82ed-286">Azure Active Directory 中未正确配置应用或证书。</span><span class="sxs-lookup"><span data-stu-id="e82ed-286">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="e82ed-287">Azure Key Vault 中不存在密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="e82ed-287">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="e82ed-288">应用无权访问密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="e82ed-288">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="e82ed-289">访问策略不包括 `Get` 和 `List` 权限。</span><span class="sxs-lookup"><span data-stu-id="e82ed-289">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="e82ed-290">在密钥保管库中，配置数据（名称-值对）被错误地命名、缺失、禁用或过期。</span><span class="sxs-lookup"><span data-stu-id="e82ed-290">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="e82ed-291">应用具有错误的密钥保管库名称（`KeyVaultName`）、Azure AD 应用程序 Id （`AzureADApplicationId`）或 Azure AD 证书指纹（`AzureADCertThumbprint`）。</span><span class="sxs-lookup"><span data-stu-id="e82ed-291">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="e82ed-292">在应用程序中，配置项（名称）的值不正确。</span><span class="sxs-lookup"><span data-stu-id="e82ed-292">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="e82ed-293">向密钥保管库添加应用的访问策略时，策略已创建，但未在**访问策略**UI 中选择 "**保存**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="e82ed-293">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e82ed-294">其他资源</span><span class="sxs-lookup"><span data-stu-id="e82ed-294">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="e82ed-295">Microsoft Azure： Key Vault</span><span class="sxs-lookup"><span data-stu-id="e82ed-295">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="e82ed-296">Microsoft Azure： Key Vault 文档</span><span class="sxs-lookup"><span data-stu-id="e82ed-296">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="e82ed-297">如何为 Azure Key Vault 生成和传输受 HSM 保护的密钥</span><span class="sxs-lookup"><span data-stu-id="e82ed-297">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="e82ed-298">KeyVaultClient 类</span><span class="sxs-lookup"><span data-stu-id="e82ed-298">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="e82ed-299">快速入门：使用 .NET web 应用设置和检索 Azure Key Vault 中的机密</span><span class="sxs-lookup"><span data-stu-id="e82ed-299">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="e82ed-300">教程：如何在 .NET 中使用 Azure Windows 虚拟机 Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e82ed-300">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)
