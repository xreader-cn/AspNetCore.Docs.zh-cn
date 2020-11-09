---
title: ASP.NET Core 中的 Azure Key Vault 配置提供程序
author: rick-anderson
description: 了解如何使用 Azure Key Vault 配置提供程序通过在运行时加载的名称/值对来配置应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, devx-track-azurecli
ms.date: 02/07/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/key-vault-configuration
ms.openlocfilehash: 10a949831c180f51bc6bb9b8294150a558f9343c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060126"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="9dd46-103">ASP.NET Core 中的 Azure Key Vault 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="9dd46-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="9dd46-104">作者： [Andrew Stanton](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="9dd46-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9dd46-105">本文档说明如何使用 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 配置提供程序从 Azure Key Vault 机密加载应用配置值。</span><span class="sxs-lookup"><span data-stu-id="9dd46-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="9dd46-106">Azure Key Vault 是一项基于云的服务，可帮助保护应用程序和服务使用的加密密钥和机密。</span><span class="sxs-lookup"><span data-stu-id="9dd46-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="9dd46-107">将 Azure Key Vault 用于 ASP.NET Core 应用的常见方案包括：</span><span class="sxs-lookup"><span data-stu-id="9dd46-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="9dd46-108">控制对敏感配置数据的访问。</span><span class="sxs-lookup"><span data-stu-id="9dd46-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="9dd46-109">满足 FIPS 140-2 第2级验证的硬件安全模块在存储配置数据时 (HSM) 的要求。</span><span class="sxs-lookup"><span data-stu-id="9dd46-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="9dd46-110">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="9dd46-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="9dd46-111">包</span><span class="sxs-lookup"><span data-stu-id="9dd46-111">Packages</span></span>

<span data-ttu-id="9dd46-112">向Microsoft.Extensions.Configu 添加包引用 [ 。AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) 包。</span><span class="sxs-lookup"><span data-stu-id="9dd46-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="9dd46-113">示例应用</span><span class="sxs-lookup"><span data-stu-id="9dd46-113">Sample app</span></span>

<span data-ttu-id="9dd46-114">该示例应用在由 `#define` *Program.cs* 文件顶部的语句确定的两种模式中运行：</span><span class="sxs-lookup"><span data-stu-id="9dd46-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="9dd46-115">`Certificate`：演示如何使用 Azure Key Vault 的客户端 ID 和 x.509 证书来访问存储在 Azure Key Vault 中的机密。</span><span class="sxs-lookup"><span data-stu-id="9dd46-115">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="9dd46-116">可以从部署到 Azure App Service 的任何位置运行此版本的示例，也可以从部署到 ASP.NET Core 应用程序的任何主机上运行。</span><span class="sxs-lookup"><span data-stu-id="9dd46-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="9dd46-117">`Managed`：演示如何使用 [Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview) 对应用进行身份验证，以通过 Azure AD 身份验证 Azure Key Vault，而不会在应用的代码或配置中存储凭据。</span><span class="sxs-lookup"><span data-stu-id="9dd46-117">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="9dd46-118">使用托管标识进行身份验证时，不需要 Azure AD 的应用程序 ID 和密码 (客户端密钥) 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="9dd46-119">`Managed`必须将示例的版本部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="9dd46-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="9dd46-120">按照 [使用 Azure 资源的托管标识](#use-managed-identities-for-azure-resources) 部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="9dd46-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="9dd46-121">有关如何使用预处理器指令 () 配置示例应用的详细信息 `#define` ，请参阅 <xref:index#preprocessor-directives-in-sample-code> 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="9dd46-122">开发环境中的机密存储</span><span class="sxs-lookup"><span data-stu-id="9dd46-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="9dd46-123">使用 [机密管理器工具](xref:security/app-secrets)在本地设置机密。</span><span class="sxs-lookup"><span data-stu-id="9dd46-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="9dd46-124">当在开发环境中的本地计算机上运行示例应用时，会从本地密钥管理器存储中加载密码。</span><span class="sxs-lookup"><span data-stu-id="9dd46-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="9dd46-125">机密管理器工具需要 `<UserSecretsId>` 应用的项目文件中的属性。</span><span class="sxs-lookup"><span data-stu-id="9dd46-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="9dd46-126">将属性值 (`{GUID}`) 设置为任何唯一 GUID：</span><span class="sxs-lookup"><span data-stu-id="9dd46-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="9dd46-127">机密以名称-值对的形式创建。</span><span class="sxs-lookup"><span data-stu-id="9dd46-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="9dd46-128">配置节 (层次结构值) `:` 在 [ASP.NET Core 配置](xref:fundamentals/configuration/index) 项名称中将 (冒号) 作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="9dd46-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="9dd46-129">机密管理器用于从打开的命令 shell 到项目的 [内容根目录](xref:fundamentals/index#content-root)，其中 `{SECRET NAME}` 是名称， `{SECRET VALUE}` 是值：</span><span class="sxs-lookup"><span data-stu-id="9dd46-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="9dd46-130">从项目的 [内容根](xref:fundamentals/index#content-root) 在命令外壳中执行以下命令，为示例应用设置机密：</span><span class="sxs-lookup"><span data-stu-id="9dd46-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="9dd46-131">如果这些机密存储在 [生产环境中的机密存储](#secret-storage-in-the-production-environment-with-azure-key-vault) Azure Key Vault 中，并 Azure Key Vault 部分，则 `_dev` 后缀将更改为 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="9dd46-132">后缀在应用的输出中提供视觉提示，指示配置值的源。</span><span class="sxs-lookup"><span data-stu-id="9dd46-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="9dd46-133">Azure Key Vault 中的生产环境中的机密存储</span><span class="sxs-lookup"><span data-stu-id="9dd46-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="9dd46-134">本 [快速入门：使用 Azure CLI 主题的 Azure Key Vault 设置和检索机密](/azure/key-vault/quick-create-cli) 的说明，请参阅此处，以创建 Azure Key Vault 和存储示例应用使用的机密。</span><span class="sxs-lookup"><span data-stu-id="9dd46-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="9dd46-135">有关更多详细信息，请参阅主题。</span><span class="sxs-lookup"><span data-stu-id="9dd46-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="9dd46-136">使用 [Azure 门户](https://portal.azure.com/)中的以下方法之一打开 Azure Cloud shell：</span><span class="sxs-lookup"><span data-stu-id="9dd46-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="9dd46-137">选择代码块右上角的“试用”。</span><span class="sxs-lookup"><span data-stu-id="9dd46-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="9dd46-138">在文本框中使用搜索字符串 "Azure CLI"。</span><span class="sxs-lookup"><span data-stu-id="9dd46-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="9dd46-139">在浏览器中打开 Cloud Shell，并提供 " **启动 Cloud Shell** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="9dd46-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="9dd46-140">选择 Azure 门户右上角菜单中的 " **Cloud Shell** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="9dd46-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="9dd46-141">有关详细信息，请参阅 Azure Cloud Shell [Azure CLI](/cli/azure/) 和 [概述](/azure/cloud-shell/overview)。</span><span class="sxs-lookup"><span data-stu-id="9dd46-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="9dd46-142">如果尚未通过身份验证，请在命令中登录 `az login` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="9dd46-143">使用以下命令创建资源组，其中 `{RESOURCE GROUP NAME}` 是新资源组的资源组名称， `{LOCATION}` 是 Azure 区域 (datacenter) ：</span><span class="sxs-lookup"><span data-stu-id="9dd46-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="9dd46-144">使用以下命令在资源组中创建密钥保管库，其中 `{KEY VAULT NAME}` 是新密钥保管库的名称， `{LOCATION}` 是 Azure 区域 (datacenter) ：</span><span class="sxs-lookup"><span data-stu-id="9dd46-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="9dd46-145">在密钥保管库中，以名称-值对的形式创建机密。</span><span class="sxs-lookup"><span data-stu-id="9dd46-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="9dd46-146">Azure Key Vault 机密名称仅限字母数字字符和短划线。</span><span class="sxs-lookup"><span data-stu-id="9dd46-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="9dd46-147">配置节 (层次结构值) 使用 `--` (两个短划线) 作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="9dd46-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="9dd46-148">冒号（通常用于在 [ASP.NET Core 配置](xref:fundamentals/configuration/index)中的子项中分隔部分）在 key vault 机密名称中不允许使用。</span><span class="sxs-lookup"><span data-stu-id="9dd46-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="9dd46-149">因此，当机密加载到应用的配置中时，将使用两个短划线并为冒号交换。</span><span class="sxs-lookup"><span data-stu-id="9dd46-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="9dd46-150">以下机密用于示例应用。</span><span class="sxs-lookup"><span data-stu-id="9dd46-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="9dd46-151">这些值包含 `_prod` 后缀，以将其与 `_dev` 从用户机密的开发环境中加载的后缀值区分开来。</span><span class="sxs-lookup"><span data-stu-id="9dd46-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="9dd46-152">将替换 `{KEY VAULT NAME}` 为在上一步中创建的密钥保管库的名称：</span><span class="sxs-lookup"><span data-stu-id="9dd46-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="9dd46-153">为非 Azure 托管的应用使用应用程序 ID 和 x.509 证书</span><span class="sxs-lookup"><span data-stu-id="9dd46-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="9dd46-154">配置 Azure AD、Azure Key Vault 和应用，以便在 **Azure 外托管应用时** 使用 Azure Active Directory 应用程序 ID 和 x.509 证书对密钥保管库进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="9dd46-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure** .</span></span> <span data-ttu-id="9dd46-155">有关详细信息，请参阅[关于密钥、机密和证书](/azure/key-vault/about-keys-secrets-and-certificates)。</span><span class="sxs-lookup"><span data-stu-id="9dd46-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="9dd46-156">尽管在 Azure 中托管的应用程序支持使用应用程序 ID 和 x.509 证书，但在 Azure 中托管应用程序时，我们建议使用 [Azure 资源的托管标识](#use-managed-identities-for-azure-resources) 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="9dd46-157">托管标识不需要在应用或开发环境中存储证书。</span><span class="sxs-lookup"><span data-stu-id="9dd46-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="9dd46-158">当 `#define` *Program.cs* 文件顶部的语句设置为时，示例应用使用应用程序 ID 和 x.509 证书 `Certificate` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="9dd46-159">创建 PKCS # 12 存档 ( *.pfx* ) 证书。</span><span class="sxs-lookup"><span data-stu-id="9dd46-159">Create a PKCS#12 archive ( *.pfx* ) certificate.</span></span> <span data-ttu-id="9dd46-160">用于创建证书的选项包括 Windows 和[OpenSSL](https://www.openssl.org/)[上的 MakeCert](/windows/desktop/seccrypto/makecert) 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="9dd46-161">将证书安装到当前用户的个人证书存储中。</span><span class="sxs-lookup"><span data-stu-id="9dd46-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="9dd46-162">将密钥标记为可导出是可选的。</span><span class="sxs-lookup"><span data-stu-id="9dd46-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="9dd46-163">记下证书的指纹，此过程将在此过程中使用。</span><span class="sxs-lookup"><span data-stu-id="9dd46-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="9dd46-164">将 PKCS # 12 存档 ( *.pfx* ) 证书导 () *.cer* 编码的证书。</span><span class="sxs-lookup"><span data-stu-id="9dd46-164">Export the PKCS#12 archive ( *.pfx* ) certificate as a DER-encoded certificate ( *.cer* ).</span></span>
1. <span data-ttu-id="9dd46-165">将应用注册 Azure AD ( **应用注册** ) 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-165">Register the app with Azure AD ( **App registrations** ).</span></span>
1. <span data-ttu-id="9dd46-166">将 DER 编码的证书 ( *.cer* ) 上传到 Azure AD：</span><span class="sxs-lookup"><span data-stu-id="9dd46-166">Upload the DER-encoded certificate ( *.cer* ) to Azure AD:</span></span>
   1. <span data-ttu-id="9dd46-167">在 Azure AD 中选择应用。</span><span class="sxs-lookup"><span data-stu-id="9dd46-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="9dd46-168">导航到 " **证书" & "机密** "。</span><span class="sxs-lookup"><span data-stu-id="9dd46-168">Navigate to **Certificates & secrets** .</span></span>
   1. <span data-ttu-id="9dd46-169">选择 " **上传证书** "，上传包含公钥的证书。</span><span class="sxs-lookup"><span data-stu-id="9dd46-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="9dd46-170">*.Cer* 、 *pem* 或 *.crt* 证书是可接受的。</span><span class="sxs-lookup"><span data-stu-id="9dd46-170">A *.cer* , *.pem* , or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="9dd46-171">在应用的文件中存储密钥保管库名称、应用程序 ID 和证书指纹 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="9dd46-172">导航到 Azure 门户中的 **密钥保管库** 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="9dd46-173">选择在 [生产环境中的机密存储中](#secret-storage-in-the-production-environment-with-azure-key-vault) 创建的密钥保管库，其中 Azure Key Vault "部分。</span><span class="sxs-lookup"><span data-stu-id="9dd46-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="9dd46-174">选择“访问策略”。</span><span class="sxs-lookup"><span data-stu-id="9dd46-174">Select **Access policies** .</span></span>
1. <span data-ttu-id="9dd46-175">选择“添加访问策略”。</span><span class="sxs-lookup"><span data-stu-id="9dd46-175">Select **Add Access Policy** .</span></span>
1. <span data-ttu-id="9dd46-176">打开 **机密权限** ，并为应用提供 **Get** 和 **List** 权限。</span><span class="sxs-lookup"><span data-stu-id="9dd46-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="9dd46-177">选择 " **选择主体** "，并按名称选择注册的应用。</span><span class="sxs-lookup"><span data-stu-id="9dd46-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="9dd46-178">选择“选择”按钮  。</span><span class="sxs-lookup"><span data-stu-id="9dd46-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="9dd46-179">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="9dd46-179">Select **OK** .</span></span>
1. <span data-ttu-id="9dd46-180">选择“保存”  。</span><span class="sxs-lookup"><span data-stu-id="9dd46-180">Select **Save** .</span></span>
1. <span data-ttu-id="9dd46-181">部署应用。</span><span class="sxs-lookup"><span data-stu-id="9dd46-181">Deploy the app.</span></span>

<span data-ttu-id="9dd46-182">`Certificate`示例应用从中获取其配置值，其 `IConfigurationRoot` 名称与机密名称相同：</span><span class="sxs-lookup"><span data-stu-id="9dd46-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="9dd46-183">非分层值：通过获取的值 `SecretName` `config["SecretName"]` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="9dd46-184">) 部分 (层次结构值：使用 `:` (冒号) 表示法或 `GetSection` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="9dd46-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="9dd46-185">使用以下任一方法来获取配置值：</span><span class="sxs-lookup"><span data-stu-id="9dd46-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="9dd46-186">X.509 证书由操作系统管理。</span><span class="sxs-lookup"><span data-stu-id="9dd46-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="9dd46-187">应用 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 使用文件提供的值调用 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="9dd46-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="9dd46-188">示例值：</span><span class="sxs-lookup"><span data-stu-id="9dd46-188">Example values:</span></span>

* <span data-ttu-id="9dd46-189">密钥保管库名称： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="9dd46-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="9dd46-190">应用程序 ID： `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="9dd46-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="9dd46-191">证书指纹： `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="9dd46-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="9dd46-192">*appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="9dd46-192">*appsettings.json* :</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="9dd46-193">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="9dd46-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="9dd46-194">在开发环境中，机密值用后缀进行加载 `_dev` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="9dd46-195">在生产环境中，值以后缀进行加载 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="9dd46-196">使用 Azure 资源的托管标识</span><span class="sxs-lookup"><span data-stu-id="9dd46-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="9dd46-197">**部署到 azure 的应用** 可以利用 [Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)，这允许应用使用 Azure AD 身份验证（不含凭据 (应用程序 ID 和密码/客户端密钥) 存储在应用中）对 Azure Key Vault 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="9dd46-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="9dd46-198">当 `#define` *Program.cs* 文件顶部的语句设置为时，示例应用使用 Azure 资源的托管标识 `Managed` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="9dd46-199">在应用的文件中输入保管库名称 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="9dd46-200">当设置为版本时，示例应用不需要应用程序 ID 和密码 (的客户端机密) `Managed` ，因此你可以忽略这些配置条目。</span><span class="sxs-lookup"><span data-stu-id="9dd46-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="9dd46-201">应用将部署到 Azure，Azure 将对应用进行身份验证，以便仅使用存储在文件中的保管库名称访问 Azure Key Vault *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="9dd46-202">将示例应用部署到 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="9dd46-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="9dd46-203">在创建服务时，会自动向 Azure AD 注册部署到 Azure App Service 的应用。</span><span class="sxs-lookup"><span data-stu-id="9dd46-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="9dd46-204">从部署中获取对象 ID 以在下面的命令中使用。</span><span class="sxs-lookup"><span data-stu-id="9dd46-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="9dd46-205">对象 ID 显示在应用服务面板上的 "Azure 门户中 **Identity** 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="9dd46-206">使用 Azure CLI 和应用程序的对象 ID，为应用程序提供 `list` `get` 访问密钥保管库的和权限：</span><span class="sxs-lookup"><span data-stu-id="9dd46-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="9dd46-207">使用 Azure CLI、PowerShell 或 Azure 门户 **重启应用** 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="9dd46-208">示例应用：</span><span class="sxs-lookup"><span data-stu-id="9dd46-208">The sample app:</span></span>

* <span data-ttu-id="9dd46-209">创建 `AzureServiceTokenProvider` 不带连接字符串的类的实例。</span><span class="sxs-lookup"><span data-stu-id="9dd46-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="9dd46-210">如果未提供连接字符串，提供程序将尝试从 Azure 资源的托管标识获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="9dd46-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="9dd46-211"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>使用 `AzureServiceTokenProvider` 实例标记回调创建新的。</span><span class="sxs-lookup"><span data-stu-id="9dd46-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="9dd46-212"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>实例与加载所有机密值的的默认实现一起使用， <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 并将 `--` 冒号 () 替换为 `:` 键名称中 () 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="9dd46-213">Key vault 名称示例值： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="9dd46-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="9dd46-214">*appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="9dd46-214">*appsettings.json* :</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="9dd46-215">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="9dd46-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="9dd46-216">在开发环境中，机密值具有 `_dev` 后缀，因为它们是由用户机密提供的。</span><span class="sxs-lookup"><span data-stu-id="9dd46-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="9dd46-217">在生产环境中，值加载时使用 `_prod` 后缀，因为它们由 Azure Key Vault 提供。</span><span class="sxs-lookup"><span data-stu-id="9dd46-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="9dd46-218">如果收到 `Access denied` 错误，请确认该应用已注册到 Azure AD，并提供对密钥保管库的访问权限。</span><span class="sxs-lookup"><span data-stu-id="9dd46-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="9dd46-219">确认已在 Azure 中重新启动该服务。</span><span class="sxs-lookup"><span data-stu-id="9dd46-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="9dd46-220">有关将提供程序与托管标识和 Azure DevOps 管道一起使用的信息，请参阅使用 [托管服务标识创建与 VM 的 Azure 资源管理器服务连接](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)。</span><span class="sxs-lookup"><span data-stu-id="9dd46-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="9dd46-221">配置选项</span><span class="sxs-lookup"><span data-stu-id="9dd46-221">Configuration options</span></span>

<span data-ttu-id="9dd46-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 可以接受 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> ：</span><span class="sxs-lookup"><span data-stu-id="9dd46-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="9dd46-223">属性</span><span class="sxs-lookup"><span data-stu-id="9dd46-223">Property</span></span>         | <span data-ttu-id="9dd46-224">说明</span><span class="sxs-lookup"><span data-stu-id="9dd46-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="9dd46-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> 用于检索值。</span><span class="sxs-lookup"><span data-stu-id="9dd46-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="9dd46-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 用于控制机密加载的实例。</span><span class="sxs-lookup"><span data-stu-id="9dd46-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="9dd46-227">`Timespan` 如果为，则在轮询密钥保管库以进行更改之间等待。</span><span class="sxs-lookup"><span data-stu-id="9dd46-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="9dd46-228">默认值为 `null` (不) 重新加载配置。</span><span class="sxs-lookup"><span data-stu-id="9dd46-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="9dd46-229">密钥保管库 URI。</span><span class="sxs-lookup"><span data-stu-id="9dd46-229">Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="9dd46-230">使用密钥名称前缀</span><span class="sxs-lookup"><span data-stu-id="9dd46-230">Use a key name prefix</span></span>

<span data-ttu-id="9dd46-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 提供一个重载，该重载接受的实现 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ，该实现允许您控制密钥保管库机密如何转换为配置密钥。</span><span class="sxs-lookup"><span data-stu-id="9dd46-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="9dd46-232">例如，你可以实现接口，以便基于在应用启动时提供的前缀值加载机密值。</span><span class="sxs-lookup"><span data-stu-id="9dd46-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="9dd46-233">例如，你可以根据应用程序的版本加载机密。</span><span class="sxs-lookup"><span data-stu-id="9dd46-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="9dd46-234">不要对 key vault 机密使用前缀，将多个应用的机密放入同一密钥保管库，或放置环境机密 (例如， *开发* 与 *生产* 机密) 到同一保管库中。</span><span class="sxs-lookup"><span data-stu-id="9dd46-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="9dd46-235">建议不同的应用和开发/生产环境使用不同的密钥保管库，以便隔离应用环境以获得最高级别的安全性。</span><span class="sxs-lookup"><span data-stu-id="9dd46-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="9dd46-236">在下面的示例中，密钥保管库中建立了机密 (和使用开发环境的机密管理器工具) 对于 `5000-AppSecret` 密钥保管库机密名称) 中不允许使用 (期间。</span><span class="sxs-lookup"><span data-stu-id="9dd46-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="9dd46-237">此机密代表应用程序版本5.0.0.0 的应用程序机密。</span><span class="sxs-lookup"><span data-stu-id="9dd46-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="9dd46-238">对于其他版本的应用，5.1.0.0 会将机密添加到密钥保管库 (并使用机密管理器工具) `5100-AppSecret` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="9dd46-239">每个应用版本会将其版本控制的机密值加载到其配置中，并在 `AppSecret` 加载机密时去除版本。</span><span class="sxs-lookup"><span data-stu-id="9dd46-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="9dd46-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 使用自定义调用 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ：</span><span class="sxs-lookup"><span data-stu-id="9dd46-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="9dd46-241">该 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 实现将对机密的版本前缀做出反应，以将适当的机密加载到配置中：</span><span class="sxs-lookup"><span data-stu-id="9dd46-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="9dd46-242">`Load` 当机密名称以前缀开头时，加载密码。</span><span class="sxs-lookup"><span data-stu-id="9dd46-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="9dd46-243">未加载其他机密。</span><span class="sxs-lookup"><span data-stu-id="9dd46-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="9dd46-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="9dd46-244">`GetKey`:</span></span>
  * <span data-ttu-id="9dd46-245">从机密名称中删除前缀。</span><span class="sxs-lookup"><span data-stu-id="9dd46-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="9dd46-246">将任何名称中的两个短划线替换为 `KeyDelimiter` ，它是在配置 (中使用的分隔符，通常为冒号) 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="9dd46-247">Azure Key Vault 不允许在机密名称中使用冒号。</span><span class="sxs-lookup"><span data-stu-id="9dd46-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="9dd46-248">`Load`方法由提供程序算法调用，该算法循环访问保管库机密以查找具有版本前缀的文件。</span><span class="sxs-lookup"><span data-stu-id="9dd46-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="9dd46-249">如果找到了版本前缀 `Load` ，则该算法将使用 `GetKey` 方法来返回密钥名称的配置名称。</span><span class="sxs-lookup"><span data-stu-id="9dd46-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="9dd46-250">它从机密名称中去除版本前缀，并返回密钥名称的其余部分，以便加载到应用的配置名称-值对中。</span><span class="sxs-lookup"><span data-stu-id="9dd46-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="9dd46-251">实现此方法时：</span><span class="sxs-lookup"><span data-stu-id="9dd46-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="9dd46-252">应用的项目文件中指定的应用版本。</span><span class="sxs-lookup"><span data-stu-id="9dd46-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="9dd46-253">在以下示例中，应用的版本设置为 `5.0.0.0` ：</span><span class="sxs-lookup"><span data-stu-id="9dd46-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="9dd46-254">确认 `<UserSecretsId>` 应用的项目文件中存在属性，其中 `{GUID}` 是用户提供的 GUID：</span><span class="sxs-lookup"><span data-stu-id="9dd46-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="9dd46-255">用 [机密管理器工具](xref:security/app-secrets)本地保存以下机密：</span><span class="sxs-lookup"><span data-stu-id="9dd46-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="9dd46-256">使用以下 Azure CLI 命令在 Azure Key Vault 中保存机密：</span><span class="sxs-lookup"><span data-stu-id="9dd46-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="9dd46-257">当应用运行时，将加载密钥保管库机密。</span><span class="sxs-lookup"><span data-stu-id="9dd46-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="9dd46-258">的字符串机密与 `5000-AppSecret` 应用程序的项目文件中指定的应用版本匹配 (`5.0.0.0`) 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="9dd46-259">`5000`用破折号)  (的版本将从键名称中去除。</span><span class="sxs-lookup"><span data-stu-id="9dd46-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="9dd46-260">在整个应用程序中，通过密钥读取配置会 `AppSecret` 加载机密值。</span><span class="sxs-lookup"><span data-stu-id="9dd46-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="9dd46-261">如果在项目文件中将应用程序的版本更改为 `5.1.0.0` ，并再次运行应用程序，则返回的机密值位于 `5.1.0.0_secret_value_dev` 开发环境和生产环境中 `5.1.0.0_secret_value_prod` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="9dd46-262">你还可以向提供自己的 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 实现 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="9dd46-263">自定义客户端允许跨应用共享客户端的单个实例。</span><span class="sxs-lookup"><span data-stu-id="9dd46-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="9dd46-264">将数组绑定至类</span><span class="sxs-lookup"><span data-stu-id="9dd46-264">Bind an array to a class</span></span>

<span data-ttu-id="9dd46-265">提供程序能够将配置值读入数组，以便绑定到 POCO 数组。</span><span class="sxs-lookup"><span data-stu-id="9dd46-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="9dd46-266">当从允许键包含冒号 () 分隔符的配置源中进行读取时 `:` ，将使用数字键段来区分组成数组 (`:0:` 、) 的键 `:1:` &hellip; `:{n}:` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="9dd46-267">有关详细信息，请参阅 [配置：将数组绑定到类](xref:fundamentals/configuration/index#bind-an-array-to-a-class)。</span><span class="sxs-lookup"><span data-stu-id="9dd46-267">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="9dd46-268">Azure Key Vault 密钥不能使用冒号作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="9dd46-268">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="9dd46-269">本主题中所述的方法使用双短划线 (`--`) 作为层次结构值 (节) 的分隔符。</span><span class="sxs-lookup"><span data-stu-id="9dd46-269">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="9dd46-270">数组键存储在 Azure Key Vault 中 `--0--` ， (，，) 为双短划线和数值段 `--1--` &hellip; `--{n}--` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-270">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="9dd46-271">检查 JSON 文件提供的以下 [Serilog](https://serilog.net/) 日志记录提供程序配置。</span><span class="sxs-lookup"><span data-stu-id="9dd46-271">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="9dd46-272">在数组中定义了两个对象文本 `WriteTo` ，它们反映了两个 Serilog *接收器* ，它们描述了日志记录输出的目标：</span><span class="sxs-lookup"><span data-stu-id="9dd46-272">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks* , which describe destinations for logging output:</span></span>

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

<span data-ttu-id="9dd46-273">前面的 JSON 文件中所示的配置将存储在 Azure Key Vault 中，使用双划线 (`--`) 表示法和数值段：</span><span class="sxs-lookup"><span data-stu-id="9dd46-273">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="9dd46-274">密钥</span><span class="sxs-lookup"><span data-stu-id="9dd46-274">Key</span></span> | <span data-ttu-id="9dd46-275">值</span><span class="sxs-lookup"><span data-stu-id="9dd46-275">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="9dd46-276">重载机密</span><span class="sxs-lookup"><span data-stu-id="9dd46-276">Reload secrets</span></span>

<span data-ttu-id="9dd46-277">在调用之前会缓存机密 `IConfigurationRoot.Reload()` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-277">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="9dd46-278">在执行之前，应用程序不会考虑到密钥保管库中已过期、已禁用和更新的机密 `Reload` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-278">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="9dd46-279">禁用和过期的机密</span><span class="sxs-lookup"><span data-stu-id="9dd46-279">Disabled and expired secrets</span></span>

<span data-ttu-id="9dd46-280">禁用和过期的机密会引发 <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-280">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="9dd46-281">若要防止应用程序引发，请使用不同的配置提供程序提供配置，或更新禁用或过期的机密。</span><span class="sxs-lookup"><span data-stu-id="9dd46-281">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="9dd46-282">疑难解答</span><span class="sxs-lookup"><span data-stu-id="9dd46-282">Troubleshoot</span></span>

<span data-ttu-id="9dd46-283">当应用无法使用访问接口加载配置时，会将错误消息写入 [ASP.NET Core 日志记录基础结构](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="9dd46-283">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="9dd46-284">以下条件将阻止加载配置：</span><span class="sxs-lookup"><span data-stu-id="9dd46-284">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="9dd46-285">Azure Active Directory 中未正确配置应用或证书。</span><span class="sxs-lookup"><span data-stu-id="9dd46-285">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="9dd46-286">Azure Key Vault 中不存在密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="9dd46-286">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="9dd46-287">应用无权访问密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="9dd46-287">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="9dd46-288">访问策略不包括 `Get` 和 `List` 权限。</span><span class="sxs-lookup"><span data-stu-id="9dd46-288">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="9dd46-289">在密钥保管库中，配置数据 (名称-值对) 错误地命名、缺失、禁用或过期。</span><span class="sxs-lookup"><span data-stu-id="9dd46-289">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="9dd46-290">应用具有错误的密钥保管库名称 (`KeyVaultName`) 、Azure AD 应用程序 Id (`AzureADApplicationId`) 或 Azure AD 证书指纹 (`AzureADCertThumbprint`) 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-290">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="9dd46-291">要尝试加载的值在应用中 (名称) 的配置密钥不正确。</span><span class="sxs-lookup"><span data-stu-id="9dd46-291">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="9dd46-292">向密钥保管库添加应用的访问策略时，策略已创建，但未在 **访问策略** UI 中选择 " **保存** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="9dd46-292">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9dd46-293">其他资源</span><span class="sxs-lookup"><span data-stu-id="9dd46-293">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="9dd46-294">Microsoft Azure： Key Vault</span><span class="sxs-lookup"><span data-stu-id="9dd46-294">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="9dd46-295">Microsoft Azure： Key Vault 文档</span><span class="sxs-lookup"><span data-stu-id="9dd46-295">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="9dd46-296">如何为 Azure Key Vault 生成和传输受 HSM 保护的密钥</span><span class="sxs-lookup"><span data-stu-id="9dd46-296">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="9dd46-297">KeyVaultClient 类</span><span class="sxs-lookup"><span data-stu-id="9dd46-297">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="9dd46-298">快速入门：使用 .NET Web 应用在 Azure Key Vault 中设置和检索机密</span><span class="sxs-lookup"><span data-stu-id="9dd46-298">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="9dd46-299">教程：如何将 Azure Key Vault 与通过 .NET 编写的 Azure Windows 虚拟机配合使用</span><span class="sxs-lookup"><span data-stu-id="9dd46-299">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9dd46-300">本文档说明如何使用 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 配置提供程序从 Azure Key Vault 机密加载应用配置值。</span><span class="sxs-lookup"><span data-stu-id="9dd46-300">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="9dd46-301">Azure Key Vault 是一项基于云的服务，可帮助保护应用程序和服务使用的加密密钥和机密。</span><span class="sxs-lookup"><span data-stu-id="9dd46-301">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="9dd46-302">将 Azure Key Vault 用于 ASP.NET Core 应用的常见方案包括：</span><span class="sxs-lookup"><span data-stu-id="9dd46-302">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="9dd46-303">控制对敏感配置数据的访问。</span><span class="sxs-lookup"><span data-stu-id="9dd46-303">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="9dd46-304">满足 FIPS 140-2 第2级验证的硬件安全模块在存储配置数据时 (HSM) 的要求。</span><span class="sxs-lookup"><span data-stu-id="9dd46-304">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="9dd46-305">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="9dd46-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="9dd46-306">包</span><span class="sxs-lookup"><span data-stu-id="9dd46-306">Packages</span></span>

<span data-ttu-id="9dd46-307">向Microsoft.Extensions.Configu 添加包引用 [ 。AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) 包。</span><span class="sxs-lookup"><span data-stu-id="9dd46-307">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="9dd46-308">示例应用</span><span class="sxs-lookup"><span data-stu-id="9dd46-308">Sample app</span></span>

<span data-ttu-id="9dd46-309">该示例应用在由 `#define` *Program.cs* 文件顶部的语句确定的两种模式中运行：</span><span class="sxs-lookup"><span data-stu-id="9dd46-309">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="9dd46-310">`Certificate`：演示如何使用 Azure Key Vault 的客户端 ID 和 x.509 证书来访问存储在 Azure Key Vault 中的机密。</span><span class="sxs-lookup"><span data-stu-id="9dd46-310">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="9dd46-311">可以从部署到 Azure App Service 的任何位置运行此版本的示例，也可以从部署到 ASP.NET Core 应用程序的任何主机上运行。</span><span class="sxs-lookup"><span data-stu-id="9dd46-311">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="9dd46-312">`Managed`：演示如何使用 [Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview) 对应用进行身份验证，以通过 Azure AD 身份验证 Azure Key Vault，而不会在应用的代码或配置中存储凭据。</span><span class="sxs-lookup"><span data-stu-id="9dd46-312">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="9dd46-313">使用托管标识进行身份验证时，不需要 Azure AD 的应用程序 ID 和密码 (客户端密钥) 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-313">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="9dd46-314">`Managed`必须将示例的版本部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="9dd46-314">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="9dd46-315">按照 [使用 Azure 资源的托管标识](#use-managed-identities-for-azure-resources) 部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="9dd46-315">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="9dd46-316">有关如何使用预处理器指令 () 配置示例应用的详细信息 `#define` ，请参阅 <xref:index#preprocessor-directives-in-sample-code> 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-316">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="9dd46-317">开发环境中的机密存储</span><span class="sxs-lookup"><span data-stu-id="9dd46-317">Secret storage in the Development environment</span></span>

<span data-ttu-id="9dd46-318">使用 [机密管理器工具](xref:security/app-secrets)在本地设置机密。</span><span class="sxs-lookup"><span data-stu-id="9dd46-318">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="9dd46-319">当在开发环境中的本地计算机上运行示例应用时，会从本地密钥管理器存储中加载密码。</span><span class="sxs-lookup"><span data-stu-id="9dd46-319">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="9dd46-320">机密管理器工具需要 `<UserSecretsId>` 应用的项目文件中的属性。</span><span class="sxs-lookup"><span data-stu-id="9dd46-320">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="9dd46-321">将属性值 (`{GUID}`) 设置为任何唯一 GUID：</span><span class="sxs-lookup"><span data-stu-id="9dd46-321">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="9dd46-322">机密以名称-值对的形式创建。</span><span class="sxs-lookup"><span data-stu-id="9dd46-322">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="9dd46-323">配置节 (层次结构值) `:` 在 [ASP.NET Core 配置](xref:fundamentals/configuration/index) 项名称中将 (冒号) 作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="9dd46-323">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="9dd46-324">机密管理器用于从打开的命令 shell 到项目的 [内容根目录](xref:fundamentals/index#content-root)，其中 `{SECRET NAME}` 是名称， `{SECRET VALUE}` 是值：</span><span class="sxs-lookup"><span data-stu-id="9dd46-324">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="9dd46-325">从项目的 [内容根](xref:fundamentals/index#content-root) 在命令外壳中执行以下命令，为示例应用设置机密：</span><span class="sxs-lookup"><span data-stu-id="9dd46-325">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="9dd46-326">如果这些机密存储在 [生产环境中的机密存储](#secret-storage-in-the-production-environment-with-azure-key-vault) Azure Key Vault 中，并 Azure Key Vault 部分，则 `_dev` 后缀将更改为 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-326">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="9dd46-327">后缀在应用的输出中提供视觉提示，指示配置值的源。</span><span class="sxs-lookup"><span data-stu-id="9dd46-327">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="9dd46-328">Azure Key Vault 中的生产环境中的机密存储</span><span class="sxs-lookup"><span data-stu-id="9dd46-328">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="9dd46-329">本 [快速入门：使用 Azure CLI 主题的 Azure Key Vault 设置和检索机密](/azure/key-vault/quick-create-cli) 的说明，请参阅此处，以创建 Azure Key Vault 和存储示例应用使用的机密。</span><span class="sxs-lookup"><span data-stu-id="9dd46-329">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="9dd46-330">有关更多详细信息，请参阅主题。</span><span class="sxs-lookup"><span data-stu-id="9dd46-330">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="9dd46-331">使用 [Azure 门户](https://portal.azure.com/)中的以下方法之一打开 Azure Cloud shell：</span><span class="sxs-lookup"><span data-stu-id="9dd46-331">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="9dd46-332">选择代码块右上角的“试用”。</span><span class="sxs-lookup"><span data-stu-id="9dd46-332">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="9dd46-333">在文本框中使用搜索字符串 "Azure CLI"。</span><span class="sxs-lookup"><span data-stu-id="9dd46-333">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="9dd46-334">在浏览器中打开 Cloud Shell，并提供 " **启动 Cloud Shell** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="9dd46-334">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="9dd46-335">选择 Azure 门户右上角菜单中的 " **Cloud Shell** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="9dd46-335">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="9dd46-336">有关详细信息，请参阅 Azure Cloud Shell [Azure CLI](/cli/azure/) 和 [概述](/azure/cloud-shell/overview)。</span><span class="sxs-lookup"><span data-stu-id="9dd46-336">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="9dd46-337">如果尚未通过身份验证，请在命令中登录 `az login` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-337">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="9dd46-338">使用以下命令创建资源组，其中 `{RESOURCE GROUP NAME}` 是新资源组的资源组名称， `{LOCATION}` 是 Azure 区域 (datacenter) ：</span><span class="sxs-lookup"><span data-stu-id="9dd46-338">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="9dd46-339">使用以下命令在资源组中创建密钥保管库，其中 `{KEY VAULT NAME}` 是新密钥保管库的名称， `{LOCATION}` 是 Azure 区域 (datacenter) ：</span><span class="sxs-lookup"><span data-stu-id="9dd46-339">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="9dd46-340">在密钥保管库中，以名称-值对的形式创建机密。</span><span class="sxs-lookup"><span data-stu-id="9dd46-340">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="9dd46-341">Azure Key Vault 机密名称仅限字母数字字符和短划线。</span><span class="sxs-lookup"><span data-stu-id="9dd46-341">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="9dd46-342">配置节 (层次结构值) 使用 `--` (两个短划线) 作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="9dd46-342">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="9dd46-343">冒号（通常用于在 [ASP.NET Core 配置](xref:fundamentals/configuration/index)中的子项中分隔部分）在 key vault 机密名称中不允许使用。</span><span class="sxs-lookup"><span data-stu-id="9dd46-343">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="9dd46-344">因此，当机密加载到应用的配置中时，将使用两个短划线并为冒号交换。</span><span class="sxs-lookup"><span data-stu-id="9dd46-344">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="9dd46-345">以下机密用于示例应用。</span><span class="sxs-lookup"><span data-stu-id="9dd46-345">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="9dd46-346">这些值包含 `_prod` 后缀，以将其与 `_dev` 从用户机密的开发环境中加载的后缀值区分开来。</span><span class="sxs-lookup"><span data-stu-id="9dd46-346">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="9dd46-347">将替换 `{KEY VAULT NAME}` 为在上一步中创建的密钥保管库的名称：</span><span class="sxs-lookup"><span data-stu-id="9dd46-347">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="9dd46-348">为非 Azure 托管的应用使用应用程序 ID 和 x.509 证书</span><span class="sxs-lookup"><span data-stu-id="9dd46-348">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="9dd46-349">配置 Azure AD、Azure Key Vault 和应用，以便在 **Azure 外托管应用时** 使用 Azure Active Directory 应用程序 ID 和 x.509 证书对密钥保管库进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="9dd46-349">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure** .</span></span> <span data-ttu-id="9dd46-350">有关详细信息，请参阅[关于密钥、机密和证书](/azure/key-vault/about-keys-secrets-and-certificates)。</span><span class="sxs-lookup"><span data-stu-id="9dd46-350">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="9dd46-351">尽管在 Azure 中托管的应用程序支持使用应用程序 ID 和 x.509 证书，但在 Azure 中托管应用程序时，我们建议使用 [Azure 资源的托管标识](#use-managed-identities-for-azure-resources) 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-351">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="9dd46-352">托管标识不需要在应用或开发环境中存储证书。</span><span class="sxs-lookup"><span data-stu-id="9dd46-352">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="9dd46-353">当 `#define` *Program.cs* 文件顶部的语句设置为时，示例应用使用应用程序 ID 和 x.509 证书 `Certificate` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-353">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="9dd46-354">创建 PKCS # 12 存档 ( *.pfx* ) 证书。</span><span class="sxs-lookup"><span data-stu-id="9dd46-354">Create a PKCS#12 archive ( *.pfx* ) certificate.</span></span> <span data-ttu-id="9dd46-355">用于创建证书的选项包括 Windows 和[OpenSSL](https://www.openssl.org/)[上的 MakeCert](/windows/desktop/seccrypto/makecert) 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-355">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="9dd46-356">将证书安装到当前用户的个人证书存储中。</span><span class="sxs-lookup"><span data-stu-id="9dd46-356">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="9dd46-357">将密钥标记为可导出是可选的。</span><span class="sxs-lookup"><span data-stu-id="9dd46-357">Marking the key as exportable is optional.</span></span> <span data-ttu-id="9dd46-358">记下证书的指纹，此过程将在此过程中使用。</span><span class="sxs-lookup"><span data-stu-id="9dd46-358">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="9dd46-359">将 PKCS # 12 存档 ( *.pfx* ) 证书导 () *.cer* 编码的证书。</span><span class="sxs-lookup"><span data-stu-id="9dd46-359">Export the PKCS#12 archive ( *.pfx* ) certificate as a DER-encoded certificate ( *.cer* ).</span></span>
1. <span data-ttu-id="9dd46-360">将应用注册 Azure AD ( **应用注册** ) 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-360">Register the app with Azure AD ( **App registrations** ).</span></span>
1. <span data-ttu-id="9dd46-361">将 DER 编码的证书 ( *.cer* ) 上传到 Azure AD：</span><span class="sxs-lookup"><span data-stu-id="9dd46-361">Upload the DER-encoded certificate ( *.cer* ) to Azure AD:</span></span>
   1. <span data-ttu-id="9dd46-362">在 Azure AD 中选择应用。</span><span class="sxs-lookup"><span data-stu-id="9dd46-362">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="9dd46-363">导航到 " **证书" & "机密** "。</span><span class="sxs-lookup"><span data-stu-id="9dd46-363">Navigate to **Certificates & secrets** .</span></span>
   1. <span data-ttu-id="9dd46-364">选择 " **上传证书** "，上传包含公钥的证书。</span><span class="sxs-lookup"><span data-stu-id="9dd46-364">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="9dd46-365">*.Cer* 、 *pem* 或 *.crt* 证书是可接受的。</span><span class="sxs-lookup"><span data-stu-id="9dd46-365">A *.cer* , *.pem* , or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="9dd46-366">在应用的文件中存储密钥保管库名称、应用程序 ID 和证书指纹 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-366">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="9dd46-367">导航到 Azure 门户中的 **密钥保管库** 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-367">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="9dd46-368">选择在 [生产环境中的机密存储中](#secret-storage-in-the-production-environment-with-azure-key-vault) 创建的密钥保管库，其中 Azure Key Vault "部分。</span><span class="sxs-lookup"><span data-stu-id="9dd46-368">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="9dd46-369">选择“访问策略”。</span><span class="sxs-lookup"><span data-stu-id="9dd46-369">Select **Access policies** .</span></span>
1. <span data-ttu-id="9dd46-370">选择“添加访问策略”。</span><span class="sxs-lookup"><span data-stu-id="9dd46-370">Select **Add Access Policy** .</span></span>
1. <span data-ttu-id="9dd46-371">打开 **机密权限** ，并为应用提供 **Get** 和 **List** 权限。</span><span class="sxs-lookup"><span data-stu-id="9dd46-371">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="9dd46-372">选择 " **选择主体** "，并按名称选择注册的应用。</span><span class="sxs-lookup"><span data-stu-id="9dd46-372">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="9dd46-373">选择“选择”按钮  。</span><span class="sxs-lookup"><span data-stu-id="9dd46-373">Select the **Select** button.</span></span>
1. <span data-ttu-id="9dd46-374">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="9dd46-374">Select **OK** .</span></span>
1. <span data-ttu-id="9dd46-375">选择“保存”  。</span><span class="sxs-lookup"><span data-stu-id="9dd46-375">Select **Save** .</span></span>
1. <span data-ttu-id="9dd46-376">部署应用。</span><span class="sxs-lookup"><span data-stu-id="9dd46-376">Deploy the app.</span></span>

<span data-ttu-id="9dd46-377">`Certificate`示例应用从中获取其配置值，其 `IConfigurationRoot` 名称与机密名称相同：</span><span class="sxs-lookup"><span data-stu-id="9dd46-377">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="9dd46-378">非分层值：通过获取的值 `SecretName` `config["SecretName"]` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-378">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="9dd46-379">) 部分 (层次结构值：使用 `:` (冒号) 表示法或 `GetSection` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="9dd46-379">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="9dd46-380">使用以下任一方法来获取配置值：</span><span class="sxs-lookup"><span data-stu-id="9dd46-380">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="9dd46-381">X.509 证书由操作系统管理。</span><span class="sxs-lookup"><span data-stu-id="9dd46-381">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="9dd46-382">应用 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 使用文件提供的值调用 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="9dd46-382">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="9dd46-383">示例值：</span><span class="sxs-lookup"><span data-stu-id="9dd46-383">Example values:</span></span>

* <span data-ttu-id="9dd46-384">密钥保管库名称： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="9dd46-384">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="9dd46-385">应用程序 ID： `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="9dd46-385">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="9dd46-386">证书指纹： `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="9dd46-386">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="9dd46-387">*appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="9dd46-387">*appsettings.json* :</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="9dd46-388">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="9dd46-388">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="9dd46-389">在开发环境中，机密值用后缀进行加载 `_dev` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-389">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="9dd46-390">在生产环境中，值以后缀进行加载 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-390">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="9dd46-391">使用 Azure 资源的托管标识</span><span class="sxs-lookup"><span data-stu-id="9dd46-391">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="9dd46-392">**部署到 azure 的应用** 可以利用 [Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)，这允许应用使用 Azure AD 身份验证（不含凭据 (应用程序 ID 和密码/客户端密钥) 存储在应用中）对 Azure Key Vault 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="9dd46-392">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="9dd46-393">当 `#define` *Program.cs* 文件顶部的语句设置为时，示例应用使用 Azure 资源的托管标识 `Managed` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-393">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="9dd46-394">在应用的文件中输入保管库名称 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-394">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="9dd46-395">当设置为版本时，示例应用不需要应用程序 ID 和密码 (的客户端机密) `Managed` ，因此你可以忽略这些配置条目。</span><span class="sxs-lookup"><span data-stu-id="9dd46-395">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="9dd46-396">应用将部署到 Azure，Azure 将对应用进行身份验证，以便仅使用存储在文件中的保管库名称访问 Azure Key Vault *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-396">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="9dd46-397">将示例应用部署到 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="9dd46-397">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="9dd46-398">在创建服务时，会自动向 Azure AD 注册部署到 Azure App Service 的应用。</span><span class="sxs-lookup"><span data-stu-id="9dd46-398">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="9dd46-399">从部署中获取对象 ID 以在下面的命令中使用。</span><span class="sxs-lookup"><span data-stu-id="9dd46-399">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="9dd46-400">对象 ID 显示在应用服务面板上的 "Azure 门户中 **Identity** 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-400">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="9dd46-401">使用 Azure CLI 和应用程序的对象 ID，为应用程序提供 `list` `get` 访问密钥保管库的和权限：</span><span class="sxs-lookup"><span data-stu-id="9dd46-401">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="9dd46-402">使用 Azure CLI、PowerShell 或 Azure 门户 **重启应用** 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-402">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="9dd46-403">示例应用：</span><span class="sxs-lookup"><span data-stu-id="9dd46-403">The sample app:</span></span>

* <span data-ttu-id="9dd46-404">创建 `AzureServiceTokenProvider` 不带连接字符串的类的实例。</span><span class="sxs-lookup"><span data-stu-id="9dd46-404">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="9dd46-405">如果未提供连接字符串，提供程序将尝试从 Azure 资源的托管标识获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="9dd46-405">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="9dd46-406"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>使用 `AzureServiceTokenProvider` 实例标记回调创建新的。</span><span class="sxs-lookup"><span data-stu-id="9dd46-406">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="9dd46-407"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>实例与加载所有机密值的的默认实现一起使用， <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 并将 `--` 冒号 () 替换为 `:` 键名称中 () 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-407">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="9dd46-408">Key vault 名称示例值： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="9dd46-408">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="9dd46-409">*appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="9dd46-409">*appsettings.json* :</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="9dd46-410">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="9dd46-410">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="9dd46-411">在开发环境中，机密值具有 `_dev` 后缀，因为它们是由用户机密提供的。</span><span class="sxs-lookup"><span data-stu-id="9dd46-411">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="9dd46-412">在生产环境中，值加载时使用 `_prod` 后缀，因为它们由 Azure Key Vault 提供。</span><span class="sxs-lookup"><span data-stu-id="9dd46-412">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="9dd46-413">如果收到 `Access denied` 错误，请确认该应用已注册到 Azure AD，并提供对密钥保管库的访问权限。</span><span class="sxs-lookup"><span data-stu-id="9dd46-413">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="9dd46-414">确认已在 Azure 中重新启动该服务。</span><span class="sxs-lookup"><span data-stu-id="9dd46-414">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="9dd46-415">有关将提供程序与托管标识和 Azure DevOps 管道一起使用的信息，请参阅使用 [托管服务标识创建与 VM 的 Azure 资源管理器服务连接](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)。</span><span class="sxs-lookup"><span data-stu-id="9dd46-415">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="9dd46-416">使用密钥名称前缀</span><span class="sxs-lookup"><span data-stu-id="9dd46-416">Use a key name prefix</span></span>

<span data-ttu-id="9dd46-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 提供一个重载，该重载接受的实现 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ，该实现允许您控制密钥保管库机密如何转换为配置密钥。</span><span class="sxs-lookup"><span data-stu-id="9dd46-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="9dd46-418">例如，你可以实现接口，以便基于在应用启动时提供的前缀值加载机密值。</span><span class="sxs-lookup"><span data-stu-id="9dd46-418">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="9dd46-419">例如，你可以根据应用程序的版本加载机密。</span><span class="sxs-lookup"><span data-stu-id="9dd46-419">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="9dd46-420">不要对 key vault 机密使用前缀，将多个应用的机密放入同一密钥保管库，或放置环境机密 (例如， *开发* 与 *生产* 机密) 到同一保管库中。</span><span class="sxs-lookup"><span data-stu-id="9dd46-420">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="9dd46-421">建议不同的应用和开发/生产环境使用不同的密钥保管库，以便隔离应用环境以获得最高级别的安全性。</span><span class="sxs-lookup"><span data-stu-id="9dd46-421">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="9dd46-422">在下面的示例中，密钥保管库中建立了机密 (和使用开发环境的机密管理器工具) 对于 `5000-AppSecret` 密钥保管库机密名称) 中不允许使用 (期间。</span><span class="sxs-lookup"><span data-stu-id="9dd46-422">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="9dd46-423">此机密代表应用程序版本5.0.0.0 的应用程序机密。</span><span class="sxs-lookup"><span data-stu-id="9dd46-423">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="9dd46-424">对于其他版本的应用，5.1.0.0 会将机密添加到密钥保管库 (并使用机密管理器工具) `5100-AppSecret` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-424">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="9dd46-425">每个应用版本会将其版本控制的机密值加载到其配置中，并在 `AppSecret` 加载机密时去除版本。</span><span class="sxs-lookup"><span data-stu-id="9dd46-425">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="9dd46-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 使用自定义调用 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ：</span><span class="sxs-lookup"><span data-stu-id="9dd46-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="9dd46-427">该 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 实现将对机密的版本前缀做出反应，以将适当的机密加载到配置中：</span><span class="sxs-lookup"><span data-stu-id="9dd46-427">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="9dd46-428">`Load` 当机密名称以前缀开头时，加载密码。</span><span class="sxs-lookup"><span data-stu-id="9dd46-428">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="9dd46-429">未加载其他机密。</span><span class="sxs-lookup"><span data-stu-id="9dd46-429">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="9dd46-430">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="9dd46-430">`GetKey`:</span></span>
  * <span data-ttu-id="9dd46-431">从机密名称中删除前缀。</span><span class="sxs-lookup"><span data-stu-id="9dd46-431">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="9dd46-432">将任何名称中的两个短划线替换为 `KeyDelimiter` ，它是在配置 (中使用的分隔符，通常为冒号) 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-432">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="9dd46-433">Azure Key Vault 不允许在机密名称中使用冒号。</span><span class="sxs-lookup"><span data-stu-id="9dd46-433">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="9dd46-434">`Load`方法由提供程序算法调用，该算法循环访问保管库机密以查找具有版本前缀的文件。</span><span class="sxs-lookup"><span data-stu-id="9dd46-434">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="9dd46-435">如果找到了版本前缀 `Load` ，则该算法将使用 `GetKey` 方法来返回密钥名称的配置名称。</span><span class="sxs-lookup"><span data-stu-id="9dd46-435">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="9dd46-436">它从机密名称中去除版本前缀，并返回密钥名称的其余部分，以便加载到应用的配置名称-值对中。</span><span class="sxs-lookup"><span data-stu-id="9dd46-436">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="9dd46-437">实现此方法时：</span><span class="sxs-lookup"><span data-stu-id="9dd46-437">When this approach is implemented:</span></span>

1. <span data-ttu-id="9dd46-438">应用的项目文件中指定的应用版本。</span><span class="sxs-lookup"><span data-stu-id="9dd46-438">The app's version specified in the app's project file.</span></span> <span data-ttu-id="9dd46-439">在以下示例中，应用的版本设置为 `5.0.0.0` ：</span><span class="sxs-lookup"><span data-stu-id="9dd46-439">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="9dd46-440">确认 `<UserSecretsId>` 应用的项目文件中存在属性，其中 `{GUID}` 是用户提供的 GUID：</span><span class="sxs-lookup"><span data-stu-id="9dd46-440">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="9dd46-441">用 [机密管理器工具](xref:security/app-secrets)本地保存以下机密：</span><span class="sxs-lookup"><span data-stu-id="9dd46-441">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="9dd46-442">使用以下 Azure CLI 命令在 Azure Key Vault 中保存机密：</span><span class="sxs-lookup"><span data-stu-id="9dd46-442">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="9dd46-443">当应用运行时，将加载密钥保管库机密。</span><span class="sxs-lookup"><span data-stu-id="9dd46-443">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="9dd46-444">的字符串机密与 `5000-AppSecret` 应用程序的项目文件中指定的应用版本匹配 (`5.0.0.0`) 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-444">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="9dd46-445">`5000`用破折号)  (的版本将从键名称中去除。</span><span class="sxs-lookup"><span data-stu-id="9dd46-445">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="9dd46-446">在整个应用程序中，通过密钥读取配置会 `AppSecret` 加载机密值。</span><span class="sxs-lookup"><span data-stu-id="9dd46-446">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="9dd46-447">如果在项目文件中将应用程序的版本更改为 `5.1.0.0` ，并再次运行应用程序，则返回的机密值位于 `5.1.0.0_secret_value_dev` 开发环境和生产环境中 `5.1.0.0_secret_value_prod` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-447">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="9dd46-448">你还可以向提供自己的 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 实现 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-448">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="9dd46-449">自定义客户端允许跨应用共享客户端的单个实例。</span><span class="sxs-lookup"><span data-stu-id="9dd46-449">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="9dd46-450">将数组绑定至类</span><span class="sxs-lookup"><span data-stu-id="9dd46-450">Bind an array to a class</span></span>

<span data-ttu-id="9dd46-451">提供程序能够将配置值读入数组，以便绑定到 POCO 数组。</span><span class="sxs-lookup"><span data-stu-id="9dd46-451">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="9dd46-452">当从允许键包含冒号 () 分隔符的配置源中进行读取时 `:` ，将使用数字键段来区分组成数组 (`:0:` 、) 的键 `:1:` &hellip; `:{n}:` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-452">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="9dd46-453">有关详细信息，请参阅 [配置：将数组绑定到类](xref:fundamentals/configuration/index#bind-an-array-to-a-class)。</span><span class="sxs-lookup"><span data-stu-id="9dd46-453">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="9dd46-454">Azure Key Vault 密钥不能使用冒号作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="9dd46-454">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="9dd46-455">本主题中所述的方法使用双短划线 (`--`) 作为层次结构值 (节) 的分隔符。</span><span class="sxs-lookup"><span data-stu-id="9dd46-455">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="9dd46-456">数组键存储在 Azure Key Vault 中 `--0--` ， (，，) 为双短划线和数值段 `--1--` &hellip; `--{n}--` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-456">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="9dd46-457">检查 JSON 文件提供的以下 [Serilog](https://serilog.net/) 日志记录提供程序配置。</span><span class="sxs-lookup"><span data-stu-id="9dd46-457">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="9dd46-458">在数组中定义了两个对象文本 `WriteTo` ，它们反映了两个 Serilog *接收器* ，它们描述了日志记录输出的目标：</span><span class="sxs-lookup"><span data-stu-id="9dd46-458">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks* , which describe destinations for logging output:</span></span>

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

<span data-ttu-id="9dd46-459">前面的 JSON 文件中所示的配置将存储在 Azure Key Vault 中，使用双划线 (`--`) 表示法和数值段：</span><span class="sxs-lookup"><span data-stu-id="9dd46-459">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="9dd46-460">密钥</span><span class="sxs-lookup"><span data-stu-id="9dd46-460">Key</span></span> | <span data-ttu-id="9dd46-461">值</span><span class="sxs-lookup"><span data-stu-id="9dd46-461">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="9dd46-462">重载机密</span><span class="sxs-lookup"><span data-stu-id="9dd46-462">Reload secrets</span></span>

<span data-ttu-id="9dd46-463">在调用之前会缓存机密 `IConfigurationRoot.Reload()` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-463">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="9dd46-464">在执行之前，应用程序不会考虑到密钥保管库中已过期、已禁用和更新的机密 `Reload` 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-464">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="9dd46-465">禁用和过期的机密</span><span class="sxs-lookup"><span data-stu-id="9dd46-465">Disabled and expired secrets</span></span>

<span data-ttu-id="9dd46-466">禁用和过期的机密会引发 <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-466">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="9dd46-467">若要防止应用程序引发，请使用不同的配置提供程序提供配置，或更新禁用或过期的机密。</span><span class="sxs-lookup"><span data-stu-id="9dd46-467">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="9dd46-468">疑难解答</span><span class="sxs-lookup"><span data-stu-id="9dd46-468">Troubleshoot</span></span>

<span data-ttu-id="9dd46-469">当应用无法使用访问接口加载配置时，会将错误消息写入 [ASP.NET Core 日志记录基础结构](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="9dd46-469">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="9dd46-470">以下条件将阻止加载配置：</span><span class="sxs-lookup"><span data-stu-id="9dd46-470">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="9dd46-471">Azure Active Directory 中未正确配置应用或证书。</span><span class="sxs-lookup"><span data-stu-id="9dd46-471">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="9dd46-472">Azure Key Vault 中不存在密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="9dd46-472">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="9dd46-473">应用无权访问密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="9dd46-473">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="9dd46-474">访问策略不包括 `Get` 和 `List` 权限。</span><span class="sxs-lookup"><span data-stu-id="9dd46-474">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="9dd46-475">在密钥保管库中，配置数据 (名称-值对) 错误地命名、缺失、禁用或过期。</span><span class="sxs-lookup"><span data-stu-id="9dd46-475">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="9dd46-476">应用具有错误的密钥保管库名称 (`KeyVaultName`) 、Azure AD 应用程序 Id (`AzureADApplicationId`) 或 Azure AD 证书指纹 (`AzureADCertThumbprint`) 。</span><span class="sxs-lookup"><span data-stu-id="9dd46-476">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="9dd46-477">要尝试加载的值在应用中 (名称) 的配置密钥不正确。</span><span class="sxs-lookup"><span data-stu-id="9dd46-477">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="9dd46-478">向密钥保管库添加应用的访问策略时，策略已创建，但未在 **访问策略** UI 中选择 " **保存** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="9dd46-478">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9dd46-479">其他资源</span><span class="sxs-lookup"><span data-stu-id="9dd46-479">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="9dd46-480">Microsoft Azure： Key Vault</span><span class="sxs-lookup"><span data-stu-id="9dd46-480">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="9dd46-481">Microsoft Azure： Key Vault 文档</span><span class="sxs-lookup"><span data-stu-id="9dd46-481">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="9dd46-482">如何为 Azure Key Vault 生成和传输受 HSM 保护的密钥</span><span class="sxs-lookup"><span data-stu-id="9dd46-482">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="9dd46-483">KeyVaultClient 类</span><span class="sxs-lookup"><span data-stu-id="9dd46-483">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="9dd46-484">快速入门：使用 .NET Web 应用在 Azure Key Vault 中设置和检索机密</span><span class="sxs-lookup"><span data-stu-id="9dd46-484">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="9dd46-485">教程：如何将 Azure Key Vault 与通过 .NET 编写的 Azure Windows 虚拟机配合使用</span><span class="sxs-lookup"><span data-stu-id="9dd46-485">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

