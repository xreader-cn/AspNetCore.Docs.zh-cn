---
<span data-ttu-id="76383-101">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="76383-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="76383-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="76383-102">'Blazor'</span></span>
- <span data-ttu-id="76383-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="76383-103">'Identity'</span></span>
- <span data-ttu-id="76383-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="76383-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="76383-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="76383-105">'Razor'</span></span>
- <span data-ttu-id="76383-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="76383-106">'SignalR' uid:</span></span> 

---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="76383-107">ASP.NET Core 中的 Azure Key Vault 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="76383-107">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="76383-108">作者： [Andrew Stanton](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="76383-108">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="76383-109">本文档说明如何使用[Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/)配置提供程序从 Azure Key Vault 机密加载应用配置值。</span><span class="sxs-lookup"><span data-stu-id="76383-109">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="76383-110">Azure Key Vault 是一项基于云的服务，可帮助保护应用程序和服务使用的加密密钥和机密。</span><span class="sxs-lookup"><span data-stu-id="76383-110">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="76383-111">将 Azure Key Vault 用于 ASP.NET Core 应用的常见方案包括：</span><span class="sxs-lookup"><span data-stu-id="76383-111">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="76383-112">控制对敏感配置数据的访问。</span><span class="sxs-lookup"><span data-stu-id="76383-112">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="76383-113">在存储配置数据时满足 FIPS 140-2 级别2验证的硬件安全模块（HSM）的要求。</span><span class="sxs-lookup"><span data-stu-id="76383-113">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="76383-114">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="76383-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="76383-115">包</span><span class="sxs-lookup"><span data-stu-id="76383-115">Packages</span></span>

<span data-ttu-id="76383-116">将包引用添加到[AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)包。</span><span class="sxs-lookup"><span data-stu-id="76383-116">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="76383-117">示例应用</span><span class="sxs-lookup"><span data-stu-id="76383-117">Sample app</span></span>

<span data-ttu-id="76383-118">该示例应用在由 `#define` *Program.cs*文件顶部的语句确定的两种模式中运行：</span><span class="sxs-lookup"><span data-stu-id="76383-118">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="76383-119">`Certificate`：演示如何使用 Azure Key Vault 的客户端 ID 和 x.509 证书来访问存储在 Azure Key Vault 中的机密。</span><span class="sxs-lookup"><span data-stu-id="76383-119">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="76383-120">可以从部署到 Azure App Service 的任何位置运行此版本的示例，也可以从部署到 ASP.NET Core 应用程序的任何主机上运行。</span><span class="sxs-lookup"><span data-stu-id="76383-120">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="76383-121">`Managed`：演示如何使用[Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)对应用进行身份验证，以通过 Azure AD 身份验证 Azure Key Vault，而不会在应用的代码或配置中存储凭据。</span><span class="sxs-lookup"><span data-stu-id="76383-121">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="76383-122">使用托管标识进行身份验证时，不需要 Azure AD 的应用程序 ID 和密码（客户端机密）。</span><span class="sxs-lookup"><span data-stu-id="76383-122">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="76383-123">`Managed`必须将示例的版本部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="76383-123">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="76383-124">按照[使用 Azure 资源的托管标识](#use-managed-identities-for-azure-resources)部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="76383-124">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="76383-125">有关如何使用预处理器指令（）配置示例应用的详细信息 `#define` ，请参阅 <xref:index#preprocessor-directives-in-sample-code> 。</span><span class="sxs-lookup"><span data-stu-id="76383-125">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="76383-126">开发环境中的机密存储</span><span class="sxs-lookup"><span data-stu-id="76383-126">Secret storage in the Development environment</span></span>

<span data-ttu-id="76383-127">使用[机密管理器工具](xref:security/app-secrets)在本地设置机密。</span><span class="sxs-lookup"><span data-stu-id="76383-127">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="76383-128">当在开发环境中的本地计算机上运行示例应用时，会从本地密钥管理器存储中加载密码。</span><span class="sxs-lookup"><span data-stu-id="76383-128">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="76383-129">机密管理器工具需要 `<UserSecretsId>` 应用的项目文件中的属性。</span><span class="sxs-lookup"><span data-stu-id="76383-129">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="76383-130">将属性值（ `{GUID}` ）设置为任何唯一 GUID：</span><span class="sxs-lookup"><span data-stu-id="76383-130">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="76383-131">机密以名称-值对的形式创建。</span><span class="sxs-lookup"><span data-stu-id="76383-131">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="76383-132">分层值（配置节） `:` 在[ASP.NET Core 配置](xref:fundamentals/configuration/index)项名称中使用（冒号）作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="76383-132">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="76383-133">机密管理器用于从打开的命令 shell 到项目的[内容根目录](xref:fundamentals/index#content-root)，其中 `{SECRET NAME}` 是名称， `{SECRET VALUE}` 是值：</span><span class="sxs-lookup"><span data-stu-id="76383-133">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="76383-134">从项目的[内容根](xref:fundamentals/index#content-root)在命令外壳中执行以下命令，为示例应用设置机密：</span><span class="sxs-lookup"><span data-stu-id="76383-134">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="76383-135">如果这些机密存储在[生产环境中的机密存储](#secret-storage-in-the-production-environment-with-azure-key-vault)Azure Key Vault 中，并 Azure Key Vault 部分，则 `_dev` 后缀将更改为 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="76383-135">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="76383-136">后缀在应用的输出中提供视觉提示，指示配置值的源。</span><span class="sxs-lookup"><span data-stu-id="76383-136">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="76383-137">Azure Key Vault 中的生产环境中的机密存储</span><span class="sxs-lookup"><span data-stu-id="76383-137">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="76383-138">本[快速入门：使用 Azure CLI 主题的 Azure Key Vault 设置和检索机密](/azure/key-vault/quick-create-cli)的说明，请参阅此处，以创建 Azure Key Vault 和存储示例应用使用的机密。</span><span class="sxs-lookup"><span data-stu-id="76383-138">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="76383-139">有关更多详细信息，请参阅主题。</span><span class="sxs-lookup"><span data-stu-id="76383-139">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="76383-140">使用[Azure 门户](https://portal.azure.com/)中的以下方法之一打开 Azure Cloud shell：</span><span class="sxs-lookup"><span data-stu-id="76383-140">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="76383-141">选择代码块右上角的“试用”。 </span><span class="sxs-lookup"><span data-stu-id="76383-141">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="76383-142">在文本框中使用搜索字符串 "Azure CLI"。</span><span class="sxs-lookup"><span data-stu-id="76383-142">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="76383-143">在浏览器中打开 Cloud Shell，并提供 "**启动 Cloud Shell** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="76383-143">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="76383-144">选择 Azure 门户右上角菜单中的 " **Cloud Shell** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="76383-144">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="76383-145">有关详细信息，请参阅 Azure Cloud Shell [Azure CLI](/cli/azure/)和[概述](/azure/cloud-shell/overview)。</span><span class="sxs-lookup"><span data-stu-id="76383-145">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="76383-146">如果尚未通过身份验证，请在命令中登录 `az login` 。</span><span class="sxs-lookup"><span data-stu-id="76383-146">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="76383-147">使用以下命令创建资源组，其中 `{RESOURCE GROUP NAME}` 是新资源组的资源组名称， `{LOCATION}` 是 Azure 区域（数据中心）：</span><span class="sxs-lookup"><span data-stu-id="76383-147">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="76383-148">使用以下命令在资源组中创建密钥保管库，其中 `{KEY VAULT NAME}` 是新密钥保管库的名称， `{LOCATION}` 是 Azure 区域（datacenter）：</span><span class="sxs-lookup"><span data-stu-id="76383-148">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="76383-149">在密钥保管库中，以名称-值对的形式创建机密。</span><span class="sxs-lookup"><span data-stu-id="76383-149">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="76383-150">Azure Key Vault 机密名称仅限字母数字字符和短划线。</span><span class="sxs-lookup"><span data-stu-id="76383-150">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="76383-151">分层值（配置节）使用 `--` （两个短划线）作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="76383-151">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="76383-152">冒号（通常用于在[ASP.NET Core 配置](xref:fundamentals/configuration/index)中的子项中分隔部分）在 key vault 机密名称中不允许使用。</span><span class="sxs-lookup"><span data-stu-id="76383-152">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="76383-153">因此，当机密加载到应用的配置中时，将使用两个短划线并为冒号交换。</span><span class="sxs-lookup"><span data-stu-id="76383-153">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="76383-154">以下机密用于示例应用。</span><span class="sxs-lookup"><span data-stu-id="76383-154">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="76383-155">这些值包含 `_prod` 后缀，以将其与 `_dev` 从用户机密的开发环境中加载的后缀值区分开来。</span><span class="sxs-lookup"><span data-stu-id="76383-155">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="76383-156">将替换 `{KEY VAULT NAME}` 为在上一步中创建的密钥保管库的名称：</span><span class="sxs-lookup"><span data-stu-id="76383-156">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="76383-157">为非 Azure 托管的应用使用应用程序 ID 和 x.509 证书</span><span class="sxs-lookup"><span data-stu-id="76383-157">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="76383-158">配置 Azure AD、Azure Key Vault 和应用，以便在**Azure 外托管应用时**使用 Azure Active Directory 应用程序 ID 和 x.509 证书对密钥保管库进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="76383-158">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="76383-159">有关详细信息，请参阅[关于密钥、机密和证书](/azure/key-vault/about-keys-secrets-and-certificates)。</span><span class="sxs-lookup"><span data-stu-id="76383-159">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="76383-160">尽管在 Azure 中托管的应用程序支持使用应用程序 ID 和 x.509 证书，但在 Azure 中托管应用程序时，我们建议使用[Azure 资源的托管标识](#use-managed-identities-for-azure-resources)。</span><span class="sxs-lookup"><span data-stu-id="76383-160">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="76383-161">托管标识不需要在应用或开发环境中存储证书。</span><span class="sxs-lookup"><span data-stu-id="76383-161">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="76383-162">当 `#define` *Program.cs*文件顶部的语句设置为时，示例应用使用应用程序 ID 和 x.509 证书 `Certificate` 。</span><span class="sxs-lookup"><span data-stu-id="76383-162">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="76383-163">创建 PKCS # 12 存档（*.pfx*）证书。</span><span class="sxs-lookup"><span data-stu-id="76383-163">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="76383-164">用于创建证书的选项包括 Windows 和[OpenSSL](https://www.openssl.org/)[上的 MakeCert](/windows/desktop/seccrypto/makecert) 。</span><span class="sxs-lookup"><span data-stu-id="76383-164">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="76383-165">将证书安装到当前用户的个人证书存储中。</span><span class="sxs-lookup"><span data-stu-id="76383-165">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="76383-166">将密钥标记为可导出是可选的。</span><span class="sxs-lookup"><span data-stu-id="76383-166">Marking the key as exportable is optional.</span></span> <span data-ttu-id="76383-167">记下证书的指纹，此过程将在此过程中使用。</span><span class="sxs-lookup"><span data-stu-id="76383-167">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="76383-168">将 PKCS # 12 存档（*.pfx*）证书导出为 DER 编码的证书（*.cer*）。</span><span class="sxs-lookup"><span data-stu-id="76383-168">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="76383-169">向 Azure AD （**应用注册**）注册应用。</span><span class="sxs-lookup"><span data-stu-id="76383-169">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="76383-170">将 DER 编码的证书（*.cer*）上载到 Azure AD：</span><span class="sxs-lookup"><span data-stu-id="76383-170">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="76383-171">在 Azure AD 中选择应用。</span><span class="sxs-lookup"><span data-stu-id="76383-171">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="76383-172">导航到 "**证书" & "机密**"。</span><span class="sxs-lookup"><span data-stu-id="76383-172">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="76383-173">选择 "**上传证书**"，上传包含公钥的证书。</span><span class="sxs-lookup"><span data-stu-id="76383-173">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="76383-174">*.Cer*、 *pem*或 *.crt*证书是可接受的。</span><span class="sxs-lookup"><span data-stu-id="76383-174">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="76383-175">将密钥保管库名称、应用程序 ID 和证书指纹存储在应用的*appsettings*文件中。</span><span class="sxs-lookup"><span data-stu-id="76383-175">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="76383-176">导航到 Azure 门户中的**密钥保管库**。</span><span class="sxs-lookup"><span data-stu-id="76383-176">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="76383-177">选择在[生产环境中的机密存储中](#secret-storage-in-the-production-environment-with-azure-key-vault)创建的密钥保管库，其中 Azure Key Vault "部分。</span><span class="sxs-lookup"><span data-stu-id="76383-177">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="76383-178">选择 "**访问策略**"。</span><span class="sxs-lookup"><span data-stu-id="76383-178">Select **Access policies**.</span></span>
1. <span data-ttu-id="76383-179">选择 "**添加访问策略**"。</span><span class="sxs-lookup"><span data-stu-id="76383-179">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="76383-180">打开**机密权限**，并为应用提供**Get**和**List**权限。</span><span class="sxs-lookup"><span data-stu-id="76383-180">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="76383-181">选择 "**选择主体**"，并按名称选择注册的应用。</span><span class="sxs-lookup"><span data-stu-id="76383-181">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="76383-182">选择“选择”按钮  。</span><span class="sxs-lookup"><span data-stu-id="76383-182">Select the **Select** button.</span></span>
1. <span data-ttu-id="76383-183">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="76383-183">Select **OK**.</span></span>
1. <span data-ttu-id="76383-184">选择“保存”。 </span><span class="sxs-lookup"><span data-stu-id="76383-184">Select **Save**.</span></span>
1. <span data-ttu-id="76383-185">部署应用。</span><span class="sxs-lookup"><span data-stu-id="76383-185">Deploy the app.</span></span>

<span data-ttu-id="76383-186">`Certificate`示例应用从中获取其配置值，其 `IConfigurationRoot` 名称与机密名称相同：</span><span class="sxs-lookup"><span data-stu-id="76383-186">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="76383-187">非分层值：通过获取的值 `SecretName` `config["SecretName"]` 。</span><span class="sxs-lookup"><span data-stu-id="76383-187">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="76383-188">分层值（节）：使用 `:` （冒号）表示法或 `GetSection` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="76383-188">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="76383-189">使用以下任一方法来获取配置值：</span><span class="sxs-lookup"><span data-stu-id="76383-189">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="76383-190">X.509 证书由操作系统管理。</span><span class="sxs-lookup"><span data-stu-id="76383-190">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="76383-191">应用 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 使用*appsettings*文件提供的值调用：</span><span class="sxs-lookup"><span data-stu-id="76383-191">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="76383-192">示例值：</span><span class="sxs-lookup"><span data-stu-id="76383-192">Example values:</span></span>

* <span data-ttu-id="76383-193">密钥保管库名称：`contosovault`</span><span class="sxs-lookup"><span data-stu-id="76383-193">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="76383-194">应用程序 ID：`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="76383-194">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="76383-195">证书指纹：`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="76383-195">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="76383-196">appsettings.json  ：</span><span class="sxs-lookup"><span data-stu-id="76383-196">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="76383-197">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="76383-197">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="76383-198">在开发环境中，机密值用后缀进行加载 `_dev` 。</span><span class="sxs-lookup"><span data-stu-id="76383-198">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="76383-199">在生产环境中，值以后缀进行加载 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="76383-199">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="76383-200">使用 Azure 资源的托管标识</span><span class="sxs-lookup"><span data-stu-id="76383-200">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="76383-201">**部署到 azure 的应用**可以利用[Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)，这允许应用通过使用 Azure AD 身份验证而不使用应用中存储的凭据（应用程序 ID 和密码/客户端密码）进行身份验证 Azure Key Vault。</span><span class="sxs-lookup"><span data-stu-id="76383-201">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="76383-202">当 `#define` *Program.cs*文件顶部的语句设置为时，示例应用使用 Azure 资源的托管标识 `Managed` 。</span><span class="sxs-lookup"><span data-stu-id="76383-202">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="76383-203">在应用的*appsettings*文件中输入保管库名称。</span><span class="sxs-lookup"><span data-stu-id="76383-203">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="76383-204">当设置为版本时，示例应用不需要应用程序 ID 和密码（客户端密码） `Managed` ，因此可以忽略这些配置条目。</span><span class="sxs-lookup"><span data-stu-id="76383-204">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="76383-205">应用将部署到 Azure，Azure 将对应用进行身份验证，以便仅使用存储在*appsettings*文件中的保管库名称访问 Azure Key Vault。</span><span class="sxs-lookup"><span data-stu-id="76383-205">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="76383-206">将示例应用部署到 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="76383-206">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="76383-207">在创建服务时，会自动向 Azure AD 注册部署到 Azure App Service 的应用。</span><span class="sxs-lookup"><span data-stu-id="76383-207">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="76383-208">从部署中获取对象 ID 以在下面的命令中使用。</span><span class="sxs-lookup"><span data-stu-id="76383-208">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="76383-209">对象 ID 显示在应用服务面板上的 "Azure 门户中 **Identity** 。</span><span class="sxs-lookup"><span data-stu-id="76383-209">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="76383-210">使用 Azure CLI 和应用程序的对象 ID，为应用程序提供 `list` `get` 访问密钥保管库的和权限：</span><span class="sxs-lookup"><span data-stu-id="76383-210">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="76383-211">使用 Azure CLI、PowerShell 或 Azure 门户**重启应用**。</span><span class="sxs-lookup"><span data-stu-id="76383-211">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="76383-212">示例应用：</span><span class="sxs-lookup"><span data-stu-id="76383-212">The sample app:</span></span>

* <span data-ttu-id="76383-213">创建 `AzureServiceTokenProvider` 不带连接字符串的类的实例。</span><span class="sxs-lookup"><span data-stu-id="76383-213">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="76383-214">如果未提供连接字符串，提供程序将尝试从 Azure 资源的托管标识获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="76383-214">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="76383-215"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>使用 `AzureServiceTokenProvider` 实例标记回调创建新的。</span><span class="sxs-lookup"><span data-stu-id="76383-215">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="76383-216"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>实例与的默认实现一起使用 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ，该实现加载所有机密值，并将 `--` 键名称中的双划线（）替换为冒号（ `:` ）。</span><span class="sxs-lookup"><span data-stu-id="76383-216">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="76383-217">Key vault 名称示例值：`contosovault`</span><span class="sxs-lookup"><span data-stu-id="76383-217">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="76383-218">appsettings.json  ：</span><span class="sxs-lookup"><span data-stu-id="76383-218">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="76383-219">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="76383-219">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="76383-220">在开发环境中，机密值具有 `_dev` 后缀，因为它们是由用户机密提供的。</span><span class="sxs-lookup"><span data-stu-id="76383-220">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="76383-221">在生产环境中，值加载时使用 `_prod` 后缀，因为它们由 Azure Key Vault 提供。</span><span class="sxs-lookup"><span data-stu-id="76383-221">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="76383-222">如果收到 `Access denied` 错误，请确认该应用已注册到 Azure AD，并提供对密钥保管库的访问权限。</span><span class="sxs-lookup"><span data-stu-id="76383-222">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="76383-223">确认已在 Azure 中重新启动该服务。</span><span class="sxs-lookup"><span data-stu-id="76383-223">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="76383-224">有关将提供程序与托管标识和 Azure DevOps 管道一起使用的信息，请参阅使用[托管服务标识创建与 VM 的 Azure 资源管理器服务连接](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)。</span><span class="sxs-lookup"><span data-stu-id="76383-224">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="76383-225">配置选项</span><span class="sxs-lookup"><span data-stu-id="76383-225">Configuration options</span></span>

<span data-ttu-id="76383-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>可以接受 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> ：</span><span class="sxs-lookup"><span data-stu-id="76383-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="76383-227">属性</span><span class="sxs-lookup"><span data-stu-id="76383-227">Property</span></span>         | <span data-ttu-id="76383-228">说明</span><span class="sxs-lookup"><span data-stu-id="76383-228">Description</span></span> |
| ---
<span data-ttu-id="76383-229">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="76383-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="76383-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="76383-230">'Blazor'</span></span>
- <span data-ttu-id="76383-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="76383-231">'Identity'</span></span>
- <span data-ttu-id="76383-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="76383-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="76383-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="76383-233">'Razor'</span></span>
- <span data-ttu-id="76383-234">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="76383-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="76383-235">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="76383-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="76383-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="76383-236">'Blazor'</span></span>
- <span data-ttu-id="76383-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="76383-237">'Identity'</span></span>
- <span data-ttu-id="76383-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="76383-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="76383-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="76383-239">'Razor'</span></span>
- <span data-ttu-id="76383-240">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="76383-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="76383-241">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="76383-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="76383-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="76383-242">'Blazor'</span></span>
- <span data-ttu-id="76383-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="76383-243">'Identity'</span></span>
- <span data-ttu-id="76383-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="76383-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="76383-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="76383-245">'Razor'</span></span>
- <span data-ttu-id="76383-246">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="76383-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="76383-247">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="76383-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="76383-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="76383-248">'Blazor'</span></span>
- <span data-ttu-id="76383-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="76383-249">'Identity'</span></span>
- <span data-ttu-id="76383-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="76383-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="76383-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="76383-251">'Razor'</span></span>
- <span data-ttu-id="76383-252">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="76383-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="76383-253">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="76383-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="76383-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="76383-254">'Blazor'</span></span>
- <span data-ttu-id="76383-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="76383-255">'Identity'</span></span>
- <span data-ttu-id="76383-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="76383-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="76383-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="76383-257">'Razor'</span></span>
- <span data-ttu-id="76383-258">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="76383-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="76383-259">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="76383-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="76383-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="76383-260">'Blazor'</span></span>
- <span data-ttu-id="76383-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="76383-261">'Identity'</span></span>
- <span data-ttu-id="76383-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="76383-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="76383-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="76383-263">'Razor'</span></span>
- <span data-ttu-id="76383-264">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="76383-264">'SignalR' uid:</span></span> 

<span data-ttu-id="76383-265">-------- |---标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="76383-265">-------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="76383-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="76383-266">'Blazor'</span></span>
- <span data-ttu-id="76383-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="76383-267">'Identity'</span></span>
- <span data-ttu-id="76383-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="76383-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="76383-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="76383-269">'Razor'</span></span>
- <span data-ttu-id="76383-270">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="76383-270">'SignalR' uid:</span></span> 

-
<span data-ttu-id="76383-271">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="76383-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="76383-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="76383-272">'Blazor'</span></span>
- <span data-ttu-id="76383-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="76383-273">'Identity'</span></span>
- <span data-ttu-id="76383-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="76383-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="76383-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="76383-275">'Razor'</span></span>
- <span data-ttu-id="76383-276">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="76383-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="76383-277">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="76383-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="76383-278">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="76383-278">'Blazor'</span></span>
- <span data-ttu-id="76383-279">'Identity'</span><span class="sxs-lookup"><span data-stu-id="76383-279">'Identity'</span></span>
- <span data-ttu-id="76383-280">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="76383-280">'Let's Encrypt'</span></span>
- <span data-ttu-id="76383-281">'Razor'</span><span class="sxs-lookup"><span data-stu-id="76383-281">'Razor'</span></span>
- <span data-ttu-id="76383-282">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="76383-282">'SignalR' uid:</span></span> 

<span data-ttu-id="76383-283">------ | |`Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient>用于检索值。</span><span class="sxs-lookup"><span data-stu-id="76383-283">------ | | `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> <span data-ttu-id="76383-284">| |`Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>用于控制机密加载的实例。</span><span class="sxs-lookup"><span data-stu-id="76383-284">| | `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> <span data-ttu-id="76383-285">| |`ReloadInterval` | `Timespan`如果为，则在轮询密钥保管库以进行更改之间等待。</span><span class="sxs-lookup"><span data-stu-id="76383-285">| | `ReloadInterval` | `Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="76383-286">默认值为 `null` （配置不重新加载）。</span><span class="sxs-lookup"><span data-stu-id="76383-286">The default value is `null` (configuration isn't reloaded).</span></span> <span data-ttu-id="76383-287">| |`Vault`          |密钥保管库 URI。</span><span class="sxs-lookup"><span data-stu-id="76383-287">| | `Vault`          | Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="76383-288">使用密钥名称前缀</span><span class="sxs-lookup"><span data-stu-id="76383-288">Use a key name prefix</span></span>

<span data-ttu-id="76383-289"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>提供一个重载，该重载接受的实现 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ，该实现允许您控制密钥保管库机密如何转换为配置密钥。</span><span class="sxs-lookup"><span data-stu-id="76383-289"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="76383-290">例如，你可以实现接口，以便基于在应用启动时提供的前缀值加载机密值。</span><span class="sxs-lookup"><span data-stu-id="76383-290">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="76383-291">例如，你可以根据应用程序的版本加载机密。</span><span class="sxs-lookup"><span data-stu-id="76383-291">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="76383-292">不要对 key vault 机密使用前缀，将多个应用的机密放入同一密钥保管库，或将环境机密（例如，*开发*与*生产*机密）放入同一保管库中。</span><span class="sxs-lookup"><span data-stu-id="76383-292">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="76383-293">建议不同的应用和开发/生产环境使用不同的密钥保管库，以便隔离应用环境以获得最高级别的安全性。</span><span class="sxs-lookup"><span data-stu-id="76383-293">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="76383-294">在以下示例中，密钥保管库（和使用开发环境的机密管理器工具）在密钥保管库中建立了机密 `5000-AppSecret` （密钥保管库机密名称中不允许使用句点）。</span><span class="sxs-lookup"><span data-stu-id="76383-294">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="76383-295">此机密代表应用程序版本5.0.0.0 的应用程序机密。</span><span class="sxs-lookup"><span data-stu-id="76383-295">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="76383-296">对于其他版本的应用，5.1.0.0，会将机密添加到密钥保管库（并使用机密管理器工具） `5100-AppSecret` 。</span><span class="sxs-lookup"><span data-stu-id="76383-296">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="76383-297">每个应用版本会将其版本控制的机密值加载到其配置中，并在 `AppSecret` 加载机密时去除版本。</span><span class="sxs-lookup"><span data-stu-id="76383-297">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="76383-298"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>使用自定义调用 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ：</span><span class="sxs-lookup"><span data-stu-id="76383-298"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="76383-299">该 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 实现将对机密的版本前缀做出反应，以将适当的机密加载到配置中：</span><span class="sxs-lookup"><span data-stu-id="76383-299">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="76383-300">`Load`当机密名称以前缀开头时，加载密码。</span><span class="sxs-lookup"><span data-stu-id="76383-300">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="76383-301">未加载其他机密。</span><span class="sxs-lookup"><span data-stu-id="76383-301">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="76383-302">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="76383-302">`GetKey`:</span></span>
  * <span data-ttu-id="76383-303">从机密名称中删除前缀。</span><span class="sxs-lookup"><span data-stu-id="76383-303">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="76383-304">将任何名称中的两个短划线替换为（ `KeyDelimiter` 在配置中使用的分隔符，通常为冒号）。</span><span class="sxs-lookup"><span data-stu-id="76383-304">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="76383-305">Azure Key Vault 不允许在机密名称中使用冒号。</span><span class="sxs-lookup"><span data-stu-id="76383-305">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="76383-306">`Load`方法由提供程序算法调用，该算法循环访问保管库机密以查找具有版本前缀的文件。</span><span class="sxs-lookup"><span data-stu-id="76383-306">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="76383-307">如果找到了版本前缀 `Load` ，则该算法将使用 `GetKey` 方法来返回密钥名称的配置名称。</span><span class="sxs-lookup"><span data-stu-id="76383-307">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="76383-308">它从机密名称中去除版本前缀，并返回密钥名称的其余部分，以便加载到应用的配置名称-值对中。</span><span class="sxs-lookup"><span data-stu-id="76383-308">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="76383-309">实现此方法时：</span><span class="sxs-lookup"><span data-stu-id="76383-309">When this approach is implemented:</span></span>

1. <span data-ttu-id="76383-310">应用的项目文件中指定的应用版本。</span><span class="sxs-lookup"><span data-stu-id="76383-310">The app's version specified in the app's project file.</span></span> <span data-ttu-id="76383-311">在以下示例中，应用的版本设置为 `5.0.0.0` ：</span><span class="sxs-lookup"><span data-stu-id="76383-311">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="76383-312">确认 `<UserSecretsId>` 应用的项目文件中存在属性，其中 `{GUID}` 是用户提供的 GUID：</span><span class="sxs-lookup"><span data-stu-id="76383-312">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="76383-313">用[机密管理器工具](xref:security/app-secrets)本地保存以下机密：</span><span class="sxs-lookup"><span data-stu-id="76383-313">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="76383-314">使用以下 Azure CLI 命令在 Azure Key Vault 中保存机密：</span><span class="sxs-lookup"><span data-stu-id="76383-314">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="76383-315">当应用运行时，将加载密钥保管库机密。</span><span class="sxs-lookup"><span data-stu-id="76383-315">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="76383-316">的字符串机密与 `5000-AppSecret` 应用程序在应用程序的项目文件（）中指定的版本相匹配 `5.0.0.0` 。</span><span class="sxs-lookup"><span data-stu-id="76383-316">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="76383-317">`5000`将从键名称中去除版本（带有短划线）。</span><span class="sxs-lookup"><span data-stu-id="76383-317">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="76383-318">在整个应用程序中，通过密钥读取配置会 `AppSecret` 加载机密值。</span><span class="sxs-lookup"><span data-stu-id="76383-318">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="76383-319">如果在项目文件中将应用程序的版本更改为 `5.1.0.0` ，并再次运行应用程序，则返回的机密值位于 `5.1.0.0_secret_value_dev` 开发环境和生产环境中 `5.1.0.0_secret_value_prod` 。</span><span class="sxs-lookup"><span data-stu-id="76383-319">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="76383-320">你还可以向提供自己的 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 实现 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 。</span><span class="sxs-lookup"><span data-stu-id="76383-320">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="76383-321">自定义客户端允许跨应用共享客户端的单个实例。</span><span class="sxs-lookup"><span data-stu-id="76383-321">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="76383-322">将数组绑定至类</span><span class="sxs-lookup"><span data-stu-id="76383-322">Bind an array to a class</span></span>

<span data-ttu-id="76383-323">提供程序能够将配置值读入数组，以便绑定到 POCO 数组。</span><span class="sxs-lookup"><span data-stu-id="76383-323">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="76383-324">从允许键包含冒号（）分隔符的配置源中进行读取时 `:` ，将使用数字键段来区分组成数组的键（ `:0:` 、 `:1:` 、 &hellip; `:{n}:` ）。</span><span class="sxs-lookup"><span data-stu-id="76383-324">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="76383-325">有关详细信息，请参阅[配置：将数组绑定到类](xref:fundamentals/configuration/index#bind-an-array-to-a-class)。</span><span class="sxs-lookup"><span data-stu-id="76383-325">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="76383-326">Azure Key Vault 密钥不能使用冒号作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="76383-326">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="76383-327">本主题中所述的方法使用双短划线（ `--` ）作为层次结构值（节）的分隔符。</span><span class="sxs-lookup"><span data-stu-id="76383-327">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="76383-328">数组键存储在带双短划线和数值段（ `--0--` 、、）的 Azure Key Vault 中 `--1--` &hellip; `--{n}--` 。</span><span class="sxs-lookup"><span data-stu-id="76383-328">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="76383-329">检查 JSON 文件提供的以下[Serilog](https://serilog.net/)日志记录提供程序配置。</span><span class="sxs-lookup"><span data-stu-id="76383-329">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="76383-330">在数组中定义了两个对象文本 `WriteTo` ，它们反映了两个 Serilog*接收器*，它们描述了日志记录输出的目标：</span><span class="sxs-lookup"><span data-stu-id="76383-330">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="76383-331">使用双短划线（ `--` ）表示法和数值段在 Azure Key Vault 中存储前面的 JSON 文件中所示的配置：</span><span class="sxs-lookup"><span data-stu-id="76383-331">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="76383-332">密钥</span><span class="sxs-lookup"><span data-stu-id="76383-332">Key</span></span> | <span data-ttu-id="76383-333">值</span><span class="sxs-lookup"><span data-stu-id="76383-333">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="76383-334">重载机密</span><span class="sxs-lookup"><span data-stu-id="76383-334">Reload secrets</span></span>

<span data-ttu-id="76383-335">在调用之前会缓存机密 `IConfigurationRoot.Reload()` 。</span><span class="sxs-lookup"><span data-stu-id="76383-335">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="76383-336">在执行之前，应用程序不会考虑到密钥保管库中已过期、已禁用和更新的机密 `Reload` 。</span><span class="sxs-lookup"><span data-stu-id="76383-336">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="76383-337">禁用和过期的机密</span><span class="sxs-lookup"><span data-stu-id="76383-337">Disabled and expired secrets</span></span>

<span data-ttu-id="76383-338">禁用和过期的机密会引发 <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> 。</span><span class="sxs-lookup"><span data-stu-id="76383-338">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="76383-339">若要防止应用程序引发，请使用不同的配置提供程序提供配置，或更新禁用或过期的机密。</span><span class="sxs-lookup"><span data-stu-id="76383-339">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="76383-340">疑难解答</span><span class="sxs-lookup"><span data-stu-id="76383-340">Troubleshoot</span></span>

<span data-ttu-id="76383-341">当应用无法使用访问接口加载配置时，会将错误消息写入[ASP.NET Core 日志记录基础结构](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="76383-341">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="76383-342">以下条件将阻止加载配置：</span><span class="sxs-lookup"><span data-stu-id="76383-342">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="76383-343">Azure Active Directory 中未正确配置应用或证书。</span><span class="sxs-lookup"><span data-stu-id="76383-343">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="76383-344">Azure Key Vault 中不存在密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="76383-344">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="76383-345">应用无权访问密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="76383-345">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="76383-346">访问策略不包括 `Get` 和 `List` 权限。</span><span class="sxs-lookup"><span data-stu-id="76383-346">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="76383-347">在密钥保管库中，配置数据（名称-值对）被错误地命名、缺失、禁用或过期。</span><span class="sxs-lookup"><span data-stu-id="76383-347">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="76383-348">应用具有错误的密钥保管库名称（ `KeyVaultName` ）、Azure AD 应用程序 Id （ `AzureADApplicationId` ）或 Azure AD 证书指纹（ `AzureADCertThumbprint` ）。</span><span class="sxs-lookup"><span data-stu-id="76383-348">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="76383-349">在应用程序中，配置项（名称）的值不正确。</span><span class="sxs-lookup"><span data-stu-id="76383-349">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="76383-350">向密钥保管库添加应用的访问策略时，策略已创建，但未在**访问策略**UI 中选择 "**保存**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="76383-350">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="76383-351">其他资源</span><span class="sxs-lookup"><span data-stu-id="76383-351">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="76383-352">Microsoft Azure： Key Vault</span><span class="sxs-lookup"><span data-stu-id="76383-352">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="76383-353">Microsoft Azure： Key Vault 文档</span><span class="sxs-lookup"><span data-stu-id="76383-353">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="76383-354">如何为 Azure 密钥保管库生成和传输受 HSM 保护的密钥</span><span class="sxs-lookup"><span data-stu-id="76383-354">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="76383-355">KeyVaultClient 类</span><span class="sxs-lookup"><span data-stu-id="76383-355">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="76383-356">快速入门：使用 .NET Web 应用在 Azure Key Vault 中设置和检索机密</span><span class="sxs-lookup"><span data-stu-id="76383-356">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="76383-357">教程：如何将 Azure Key Vault 与通过 .NET 编写的 Azure Windows 虚拟机配合使用</span><span class="sxs-lookup"><span data-stu-id="76383-357">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="76383-358">本文档说明如何使用[Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/)配置提供程序从 Azure Key Vault 机密加载应用配置值。</span><span class="sxs-lookup"><span data-stu-id="76383-358">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="76383-359">Azure Key Vault 是一项基于云的服务，可帮助保护应用程序和服务使用的加密密钥和机密。</span><span class="sxs-lookup"><span data-stu-id="76383-359">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="76383-360">将 Azure Key Vault 用于 ASP.NET Core 应用的常见方案包括：</span><span class="sxs-lookup"><span data-stu-id="76383-360">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="76383-361">控制对敏感配置数据的访问。</span><span class="sxs-lookup"><span data-stu-id="76383-361">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="76383-362">在存储配置数据时满足 FIPS 140-2 级别2验证的硬件安全模块（HSM）的要求。</span><span class="sxs-lookup"><span data-stu-id="76383-362">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="76383-363">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="76383-363">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="76383-364">包</span><span class="sxs-lookup"><span data-stu-id="76383-364">Packages</span></span>

<span data-ttu-id="76383-365">将包引用添加到[AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)包。</span><span class="sxs-lookup"><span data-stu-id="76383-365">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="76383-366">示例应用</span><span class="sxs-lookup"><span data-stu-id="76383-366">Sample app</span></span>

<span data-ttu-id="76383-367">该示例应用在由 `#define` *Program.cs*文件顶部的语句确定的两种模式中运行：</span><span class="sxs-lookup"><span data-stu-id="76383-367">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="76383-368">`Certificate`：演示如何使用 Azure Key Vault 的客户端 ID 和 x.509 证书来访问存储在 Azure Key Vault 中的机密。</span><span class="sxs-lookup"><span data-stu-id="76383-368">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="76383-369">可以从部署到 Azure App Service 的任何位置运行此版本的示例，也可以从部署到 ASP.NET Core 应用程序的任何主机上运行。</span><span class="sxs-lookup"><span data-stu-id="76383-369">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="76383-370">`Managed`：演示如何使用[Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)对应用进行身份验证，以通过 Azure AD 身份验证 Azure Key Vault，而不会在应用的代码或配置中存储凭据。</span><span class="sxs-lookup"><span data-stu-id="76383-370">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="76383-371">使用托管标识进行身份验证时，不需要 Azure AD 的应用程序 ID 和密码（客户端机密）。</span><span class="sxs-lookup"><span data-stu-id="76383-371">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="76383-372">`Managed`必须将示例的版本部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="76383-372">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="76383-373">按照[使用 Azure 资源的托管标识](#use-managed-identities-for-azure-resources)部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="76383-373">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="76383-374">有关如何使用预处理器指令（）配置示例应用的详细信息 `#define` ，请参阅 <xref:index#preprocessor-directives-in-sample-code> 。</span><span class="sxs-lookup"><span data-stu-id="76383-374">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="76383-375">开发环境中的机密存储</span><span class="sxs-lookup"><span data-stu-id="76383-375">Secret storage in the Development environment</span></span>

<span data-ttu-id="76383-376">使用[机密管理器工具](xref:security/app-secrets)在本地设置机密。</span><span class="sxs-lookup"><span data-stu-id="76383-376">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="76383-377">当在开发环境中的本地计算机上运行示例应用时，会从本地密钥管理器存储中加载密码。</span><span class="sxs-lookup"><span data-stu-id="76383-377">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="76383-378">机密管理器工具需要 `<UserSecretsId>` 应用的项目文件中的属性。</span><span class="sxs-lookup"><span data-stu-id="76383-378">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="76383-379">将属性值（ `{GUID}` ）设置为任何唯一 GUID：</span><span class="sxs-lookup"><span data-stu-id="76383-379">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="76383-380">机密以名称-值对的形式创建。</span><span class="sxs-lookup"><span data-stu-id="76383-380">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="76383-381">分层值（配置节） `:` 在[ASP.NET Core 配置](xref:fundamentals/configuration/index)项名称中使用（冒号）作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="76383-381">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="76383-382">机密管理器用于从打开的命令 shell 到项目的[内容根目录](xref:fundamentals/index#content-root)，其中 `{SECRET NAME}` 是名称， `{SECRET VALUE}` 是值：</span><span class="sxs-lookup"><span data-stu-id="76383-382">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="76383-383">从项目的[内容根](xref:fundamentals/index#content-root)在命令外壳中执行以下命令，为示例应用设置机密：</span><span class="sxs-lookup"><span data-stu-id="76383-383">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="76383-384">如果这些机密存储在[生产环境中的机密存储](#secret-storage-in-the-production-environment-with-azure-key-vault)Azure Key Vault 中，并 Azure Key Vault 部分，则 `_dev` 后缀将更改为 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="76383-384">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="76383-385">后缀在应用的输出中提供视觉提示，指示配置值的源。</span><span class="sxs-lookup"><span data-stu-id="76383-385">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="76383-386">Azure Key Vault 中的生产环境中的机密存储</span><span class="sxs-lookup"><span data-stu-id="76383-386">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="76383-387">本[快速入门：使用 Azure CLI 主题的 Azure Key Vault 设置和检索机密](/azure/key-vault/quick-create-cli)的说明，请参阅此处，以创建 Azure Key Vault 和存储示例应用使用的机密。</span><span class="sxs-lookup"><span data-stu-id="76383-387">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="76383-388">有关更多详细信息，请参阅主题。</span><span class="sxs-lookup"><span data-stu-id="76383-388">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="76383-389">使用[Azure 门户](https://portal.azure.com/)中的以下方法之一打开 Azure Cloud shell：</span><span class="sxs-lookup"><span data-stu-id="76383-389">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="76383-390">选择代码块右上角的“试用”。 </span><span class="sxs-lookup"><span data-stu-id="76383-390">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="76383-391">在文本框中使用搜索字符串 "Azure CLI"。</span><span class="sxs-lookup"><span data-stu-id="76383-391">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="76383-392">在浏览器中打开 Cloud Shell，并提供 "**启动 Cloud Shell** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="76383-392">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="76383-393">选择 Azure 门户右上角菜单中的 " **Cloud Shell** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="76383-393">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="76383-394">有关详细信息，请参阅 Azure Cloud Shell [Azure CLI](/cli/azure/)和[概述](/azure/cloud-shell/overview)。</span><span class="sxs-lookup"><span data-stu-id="76383-394">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="76383-395">如果尚未通过身份验证，请在命令中登录 `az login` 。</span><span class="sxs-lookup"><span data-stu-id="76383-395">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="76383-396">使用以下命令创建资源组，其中 `{RESOURCE GROUP NAME}` 是新资源组的资源组名称， `{LOCATION}` 是 Azure 区域（数据中心）：</span><span class="sxs-lookup"><span data-stu-id="76383-396">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="76383-397">使用以下命令在资源组中创建密钥保管库，其中 `{KEY VAULT NAME}` 是新密钥保管库的名称， `{LOCATION}` 是 Azure 区域（datacenter）：</span><span class="sxs-lookup"><span data-stu-id="76383-397">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="76383-398">在密钥保管库中，以名称-值对的形式创建机密。</span><span class="sxs-lookup"><span data-stu-id="76383-398">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="76383-399">Azure Key Vault 机密名称仅限字母数字字符和短划线。</span><span class="sxs-lookup"><span data-stu-id="76383-399">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="76383-400">分层值（配置节）使用 `--` （两个短划线）作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="76383-400">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="76383-401">冒号（通常用于在[ASP.NET Core 配置](xref:fundamentals/configuration/index)中的子项中分隔部分）在 key vault 机密名称中不允许使用。</span><span class="sxs-lookup"><span data-stu-id="76383-401">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="76383-402">因此，当机密加载到应用的配置中时，将使用两个短划线并为冒号交换。</span><span class="sxs-lookup"><span data-stu-id="76383-402">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="76383-403">以下机密用于示例应用。</span><span class="sxs-lookup"><span data-stu-id="76383-403">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="76383-404">这些值包含 `_prod` 后缀，以将其与 `_dev` 从用户机密的开发环境中加载的后缀值区分开来。</span><span class="sxs-lookup"><span data-stu-id="76383-404">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="76383-405">将替换 `{KEY VAULT NAME}` 为在上一步中创建的密钥保管库的名称：</span><span class="sxs-lookup"><span data-stu-id="76383-405">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="76383-406">为非 Azure 托管的应用使用应用程序 ID 和 x.509 证书</span><span class="sxs-lookup"><span data-stu-id="76383-406">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="76383-407">配置 Azure AD、Azure Key Vault 和应用，以便在**Azure 外托管应用时**使用 Azure Active Directory 应用程序 ID 和 x.509 证书对密钥保管库进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="76383-407">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="76383-408">有关详细信息，请参阅[关于密钥、机密和证书](/azure/key-vault/about-keys-secrets-and-certificates)。</span><span class="sxs-lookup"><span data-stu-id="76383-408">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="76383-409">尽管在 Azure 中托管的应用程序支持使用应用程序 ID 和 x.509 证书，但在 Azure 中托管应用程序时，我们建议使用[Azure 资源的托管标识](#use-managed-identities-for-azure-resources)。</span><span class="sxs-lookup"><span data-stu-id="76383-409">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="76383-410">托管标识不需要在应用或开发环境中存储证书。</span><span class="sxs-lookup"><span data-stu-id="76383-410">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="76383-411">当 `#define` *Program.cs*文件顶部的语句设置为时，示例应用使用应用程序 ID 和 x.509 证书 `Certificate` 。</span><span class="sxs-lookup"><span data-stu-id="76383-411">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="76383-412">创建 PKCS # 12 存档（*.pfx*）证书。</span><span class="sxs-lookup"><span data-stu-id="76383-412">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="76383-413">用于创建证书的选项包括 Windows 和[OpenSSL](https://www.openssl.org/)[上的 MakeCert](/windows/desktop/seccrypto/makecert) 。</span><span class="sxs-lookup"><span data-stu-id="76383-413">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="76383-414">将证书安装到当前用户的个人证书存储中。</span><span class="sxs-lookup"><span data-stu-id="76383-414">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="76383-415">将密钥标记为可导出是可选的。</span><span class="sxs-lookup"><span data-stu-id="76383-415">Marking the key as exportable is optional.</span></span> <span data-ttu-id="76383-416">记下证书的指纹，此过程将在此过程中使用。</span><span class="sxs-lookup"><span data-stu-id="76383-416">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="76383-417">将 PKCS # 12 存档（*.pfx*）证书导出为 DER 编码的证书（*.cer*）。</span><span class="sxs-lookup"><span data-stu-id="76383-417">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="76383-418">向 Azure AD （**应用注册**）注册应用。</span><span class="sxs-lookup"><span data-stu-id="76383-418">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="76383-419">将 DER 编码的证书（*.cer*）上载到 Azure AD：</span><span class="sxs-lookup"><span data-stu-id="76383-419">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="76383-420">在 Azure AD 中选择应用。</span><span class="sxs-lookup"><span data-stu-id="76383-420">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="76383-421">导航到 "**证书" & "机密**"。</span><span class="sxs-lookup"><span data-stu-id="76383-421">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="76383-422">选择 "**上传证书**"，上传包含公钥的证书。</span><span class="sxs-lookup"><span data-stu-id="76383-422">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="76383-423">*.Cer*、 *pem*或 *.crt*证书是可接受的。</span><span class="sxs-lookup"><span data-stu-id="76383-423">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="76383-424">将密钥保管库名称、应用程序 ID 和证书指纹存储在应用的*appsettings*文件中。</span><span class="sxs-lookup"><span data-stu-id="76383-424">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="76383-425">导航到 Azure 门户中的**密钥保管库**。</span><span class="sxs-lookup"><span data-stu-id="76383-425">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="76383-426">选择在[生产环境中的机密存储中](#secret-storage-in-the-production-environment-with-azure-key-vault)创建的密钥保管库，其中 Azure Key Vault "部分。</span><span class="sxs-lookup"><span data-stu-id="76383-426">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="76383-427">选择 "**访问策略**"。</span><span class="sxs-lookup"><span data-stu-id="76383-427">Select **Access policies**.</span></span>
1. <span data-ttu-id="76383-428">选择 "**添加访问策略**"。</span><span class="sxs-lookup"><span data-stu-id="76383-428">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="76383-429">打开**机密权限**，并为应用提供**Get**和**List**权限。</span><span class="sxs-lookup"><span data-stu-id="76383-429">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="76383-430">选择 "**选择主体**"，并按名称选择注册的应用。</span><span class="sxs-lookup"><span data-stu-id="76383-430">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="76383-431">选择“选择”按钮  。</span><span class="sxs-lookup"><span data-stu-id="76383-431">Select the **Select** button.</span></span>
1. <span data-ttu-id="76383-432">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="76383-432">Select **OK**.</span></span>
1. <span data-ttu-id="76383-433">选择“保存”。 </span><span class="sxs-lookup"><span data-stu-id="76383-433">Select **Save**.</span></span>
1. <span data-ttu-id="76383-434">部署应用。</span><span class="sxs-lookup"><span data-stu-id="76383-434">Deploy the app.</span></span>

<span data-ttu-id="76383-435">`Certificate`示例应用从中获取其配置值，其 `IConfigurationRoot` 名称与机密名称相同：</span><span class="sxs-lookup"><span data-stu-id="76383-435">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="76383-436">非分层值：通过获取的值 `SecretName` `config["SecretName"]` 。</span><span class="sxs-lookup"><span data-stu-id="76383-436">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="76383-437">分层值（节）：使用 `:` （冒号）表示法或 `GetSection` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="76383-437">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="76383-438">使用以下任一方法来获取配置值：</span><span class="sxs-lookup"><span data-stu-id="76383-438">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="76383-439">X.509 证书由操作系统管理。</span><span class="sxs-lookup"><span data-stu-id="76383-439">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="76383-440">应用 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 使用*appsettings*文件提供的值调用：</span><span class="sxs-lookup"><span data-stu-id="76383-440">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="76383-441">示例值：</span><span class="sxs-lookup"><span data-stu-id="76383-441">Example values:</span></span>

* <span data-ttu-id="76383-442">密钥保管库名称：`contosovault`</span><span class="sxs-lookup"><span data-stu-id="76383-442">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="76383-443">应用程序 ID：`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="76383-443">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="76383-444">证书指纹：`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="76383-444">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="76383-445">appsettings.json  ：</span><span class="sxs-lookup"><span data-stu-id="76383-445">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="76383-446">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="76383-446">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="76383-447">在开发环境中，机密值用后缀进行加载 `_dev` 。</span><span class="sxs-lookup"><span data-stu-id="76383-447">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="76383-448">在生产环境中，值以后缀进行加载 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="76383-448">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="76383-449">使用 Azure 资源的托管标识</span><span class="sxs-lookup"><span data-stu-id="76383-449">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="76383-450">**部署到 azure 的应用**可以利用[Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)，这允许应用通过使用 Azure AD 身份验证而不使用应用中存储的凭据（应用程序 ID 和密码/客户端密码）进行身份验证 Azure Key Vault。</span><span class="sxs-lookup"><span data-stu-id="76383-450">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="76383-451">当 `#define` *Program.cs*文件顶部的语句设置为时，示例应用使用 Azure 资源的托管标识 `Managed` 。</span><span class="sxs-lookup"><span data-stu-id="76383-451">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="76383-452">在应用的*appsettings*文件中输入保管库名称。</span><span class="sxs-lookup"><span data-stu-id="76383-452">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="76383-453">当设置为版本时，示例应用不需要应用程序 ID 和密码（客户端密码） `Managed` ，因此可以忽略这些配置条目。</span><span class="sxs-lookup"><span data-stu-id="76383-453">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="76383-454">应用将部署到 Azure，Azure 将对应用进行身份验证，以便仅使用存储在*appsettings*文件中的保管库名称访问 Azure Key Vault。</span><span class="sxs-lookup"><span data-stu-id="76383-454">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="76383-455">将示例应用部署到 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="76383-455">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="76383-456">在创建服务时，会自动向 Azure AD 注册部署到 Azure App Service 的应用。</span><span class="sxs-lookup"><span data-stu-id="76383-456">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="76383-457">从部署中获取对象 ID 以在下面的命令中使用。</span><span class="sxs-lookup"><span data-stu-id="76383-457">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="76383-458">对象 ID 显示在应用服务面板上的 "Azure 门户中 **Identity** 。</span><span class="sxs-lookup"><span data-stu-id="76383-458">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="76383-459">使用 Azure CLI 和应用程序的对象 ID，为应用程序提供 `list` `get` 访问密钥保管库的和权限：</span><span class="sxs-lookup"><span data-stu-id="76383-459">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="76383-460">使用 Azure CLI、PowerShell 或 Azure 门户**重启应用**。</span><span class="sxs-lookup"><span data-stu-id="76383-460">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="76383-461">示例应用：</span><span class="sxs-lookup"><span data-stu-id="76383-461">The sample app:</span></span>

* <span data-ttu-id="76383-462">创建 `AzureServiceTokenProvider` 不带连接字符串的类的实例。</span><span class="sxs-lookup"><span data-stu-id="76383-462">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="76383-463">如果未提供连接字符串，提供程序将尝试从 Azure 资源的托管标识获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="76383-463">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="76383-464"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>使用 `AzureServiceTokenProvider` 实例标记回调创建新的。</span><span class="sxs-lookup"><span data-stu-id="76383-464">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="76383-465"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>实例与的默认实现一起使用 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ，该实现加载所有机密值，并将 `--` 键名称中的双划线（）替换为冒号（ `:` ）。</span><span class="sxs-lookup"><span data-stu-id="76383-465">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="76383-466">Key vault 名称示例值：`contosovault`</span><span class="sxs-lookup"><span data-stu-id="76383-466">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="76383-467">appsettings.json  ：</span><span class="sxs-lookup"><span data-stu-id="76383-467">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="76383-468">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="76383-468">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="76383-469">在开发环境中，机密值具有 `_dev` 后缀，因为它们是由用户机密提供的。</span><span class="sxs-lookup"><span data-stu-id="76383-469">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="76383-470">在生产环境中，值加载时使用 `_prod` 后缀，因为它们由 Azure Key Vault 提供。</span><span class="sxs-lookup"><span data-stu-id="76383-470">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="76383-471">如果收到 `Access denied` 错误，请确认该应用已注册到 Azure AD，并提供对密钥保管库的访问权限。</span><span class="sxs-lookup"><span data-stu-id="76383-471">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="76383-472">确认已在 Azure 中重新启动该服务。</span><span class="sxs-lookup"><span data-stu-id="76383-472">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="76383-473">有关将提供程序与托管标识和 Azure DevOps 管道一起使用的信息，请参阅使用[托管服务标识创建与 VM 的 Azure 资源管理器服务连接](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)。</span><span class="sxs-lookup"><span data-stu-id="76383-473">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="76383-474">使用密钥名称前缀</span><span class="sxs-lookup"><span data-stu-id="76383-474">Use a key name prefix</span></span>

<span data-ttu-id="76383-475"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>提供一个重载，该重载接受的实现 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ，该实现允许您控制密钥保管库机密如何转换为配置密钥。</span><span class="sxs-lookup"><span data-stu-id="76383-475"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="76383-476">例如，你可以实现接口，以便基于在应用启动时提供的前缀值加载机密值。</span><span class="sxs-lookup"><span data-stu-id="76383-476">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="76383-477">例如，你可以根据应用程序的版本加载机密。</span><span class="sxs-lookup"><span data-stu-id="76383-477">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="76383-478">不要对 key vault 机密使用前缀，将多个应用的机密放入同一密钥保管库，或将环境机密（例如，*开发*与*生产*机密）放入同一保管库中。</span><span class="sxs-lookup"><span data-stu-id="76383-478">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="76383-479">建议不同的应用和开发/生产环境使用不同的密钥保管库，以便隔离应用环境以获得最高级别的安全性。</span><span class="sxs-lookup"><span data-stu-id="76383-479">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="76383-480">在以下示例中，密钥保管库（和使用开发环境的机密管理器工具）在密钥保管库中建立了机密 `5000-AppSecret` （密钥保管库机密名称中不允许使用句点）。</span><span class="sxs-lookup"><span data-stu-id="76383-480">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="76383-481">此机密代表应用程序版本5.0.0.0 的应用程序机密。</span><span class="sxs-lookup"><span data-stu-id="76383-481">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="76383-482">对于其他版本的应用，5.1.0.0，会将机密添加到密钥保管库（并使用机密管理器工具） `5100-AppSecret` 。</span><span class="sxs-lookup"><span data-stu-id="76383-482">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="76383-483">每个应用版本会将其版本控制的机密值加载到其配置中，并在 `AppSecret` 加载机密时去除版本。</span><span class="sxs-lookup"><span data-stu-id="76383-483">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="76383-484"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>使用自定义调用 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ：</span><span class="sxs-lookup"><span data-stu-id="76383-484"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="76383-485">该 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 实现将对机密的版本前缀做出反应，以将适当的机密加载到配置中：</span><span class="sxs-lookup"><span data-stu-id="76383-485">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="76383-486">`Load`当机密名称以前缀开头时，加载密码。</span><span class="sxs-lookup"><span data-stu-id="76383-486">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="76383-487">未加载其他机密。</span><span class="sxs-lookup"><span data-stu-id="76383-487">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="76383-488">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="76383-488">`GetKey`:</span></span>
  * <span data-ttu-id="76383-489">从机密名称中删除前缀。</span><span class="sxs-lookup"><span data-stu-id="76383-489">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="76383-490">将任何名称中的两个短划线替换为（ `KeyDelimiter` 在配置中使用的分隔符，通常为冒号）。</span><span class="sxs-lookup"><span data-stu-id="76383-490">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="76383-491">Azure Key Vault 不允许在机密名称中使用冒号。</span><span class="sxs-lookup"><span data-stu-id="76383-491">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="76383-492">`Load`方法由提供程序算法调用，该算法循环访问保管库机密以查找具有版本前缀的文件。</span><span class="sxs-lookup"><span data-stu-id="76383-492">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="76383-493">如果找到了版本前缀 `Load` ，则该算法将使用 `GetKey` 方法来返回密钥名称的配置名称。</span><span class="sxs-lookup"><span data-stu-id="76383-493">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="76383-494">它从机密名称中去除版本前缀，并返回密钥名称的其余部分，以便加载到应用的配置名称-值对中。</span><span class="sxs-lookup"><span data-stu-id="76383-494">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="76383-495">实现此方法时：</span><span class="sxs-lookup"><span data-stu-id="76383-495">When this approach is implemented:</span></span>

1. <span data-ttu-id="76383-496">应用的项目文件中指定的应用版本。</span><span class="sxs-lookup"><span data-stu-id="76383-496">The app's version specified in the app's project file.</span></span> <span data-ttu-id="76383-497">在以下示例中，应用的版本设置为 `5.0.0.0` ：</span><span class="sxs-lookup"><span data-stu-id="76383-497">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="76383-498">确认 `<UserSecretsId>` 应用的项目文件中存在属性，其中 `{GUID}` 是用户提供的 GUID：</span><span class="sxs-lookup"><span data-stu-id="76383-498">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="76383-499">用[机密管理器工具](xref:security/app-secrets)本地保存以下机密：</span><span class="sxs-lookup"><span data-stu-id="76383-499">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="76383-500">使用以下 Azure CLI 命令在 Azure Key Vault 中保存机密：</span><span class="sxs-lookup"><span data-stu-id="76383-500">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="76383-501">当应用运行时，将加载密钥保管库机密。</span><span class="sxs-lookup"><span data-stu-id="76383-501">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="76383-502">的字符串机密与 `5000-AppSecret` 应用程序在应用程序的项目文件（）中指定的版本相匹配 `5.0.0.0` 。</span><span class="sxs-lookup"><span data-stu-id="76383-502">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="76383-503">`5000`将从键名称中去除版本（带有短划线）。</span><span class="sxs-lookup"><span data-stu-id="76383-503">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="76383-504">在整个应用程序中，通过密钥读取配置会 `AppSecret` 加载机密值。</span><span class="sxs-lookup"><span data-stu-id="76383-504">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="76383-505">如果在项目文件中将应用程序的版本更改为 `5.1.0.0` ，并再次运行应用程序，则返回的机密值位于 `5.1.0.0_secret_value_dev` 开发环境和生产环境中 `5.1.0.0_secret_value_prod` 。</span><span class="sxs-lookup"><span data-stu-id="76383-505">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="76383-506">你还可以向提供自己的 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 实现 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 。</span><span class="sxs-lookup"><span data-stu-id="76383-506">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="76383-507">自定义客户端允许跨应用共享客户端的单个实例。</span><span class="sxs-lookup"><span data-stu-id="76383-507">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="76383-508">将数组绑定至类</span><span class="sxs-lookup"><span data-stu-id="76383-508">Bind an array to a class</span></span>

<span data-ttu-id="76383-509">提供程序能够将配置值读入数组，以便绑定到 POCO 数组。</span><span class="sxs-lookup"><span data-stu-id="76383-509">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="76383-510">从允许键包含冒号（）分隔符的配置源中进行读取时 `:` ，将使用数字键段来区分组成数组的键（ `:0:` 、 `:1:` 、 &hellip; `:{n}:` ）。</span><span class="sxs-lookup"><span data-stu-id="76383-510">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="76383-511">有关详细信息，请参阅[配置：将数组绑定到类](xref:fundamentals/configuration/index#bind-an-array-to-a-class)。</span><span class="sxs-lookup"><span data-stu-id="76383-511">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="76383-512">Azure Key Vault 密钥不能使用冒号作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="76383-512">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="76383-513">本主题中所述的方法使用双短划线（ `--` ）作为层次结构值（节）的分隔符。</span><span class="sxs-lookup"><span data-stu-id="76383-513">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="76383-514">数组键存储在带双短划线和数值段（ `--0--` 、、）的 Azure Key Vault 中 `--1--` &hellip; `--{n}--` 。</span><span class="sxs-lookup"><span data-stu-id="76383-514">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="76383-515">检查 JSON 文件提供的以下[Serilog](https://serilog.net/)日志记录提供程序配置。</span><span class="sxs-lookup"><span data-stu-id="76383-515">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="76383-516">在数组中定义了两个对象文本 `WriteTo` ，它们反映了两个 Serilog*接收器*，它们描述了日志记录输出的目标：</span><span class="sxs-lookup"><span data-stu-id="76383-516">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="76383-517">使用双短划线（ `--` ）表示法和数值段在 Azure Key Vault 中存储前面的 JSON 文件中所示的配置：</span><span class="sxs-lookup"><span data-stu-id="76383-517">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="76383-518">密钥</span><span class="sxs-lookup"><span data-stu-id="76383-518">Key</span></span> | <span data-ttu-id="76383-519">值</span><span class="sxs-lookup"><span data-stu-id="76383-519">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="76383-520">重载机密</span><span class="sxs-lookup"><span data-stu-id="76383-520">Reload secrets</span></span>

<span data-ttu-id="76383-521">在调用之前会缓存机密 `IConfigurationRoot.Reload()` 。</span><span class="sxs-lookup"><span data-stu-id="76383-521">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="76383-522">在执行之前，应用程序不会考虑到密钥保管库中已过期、已禁用和更新的机密 `Reload` 。</span><span class="sxs-lookup"><span data-stu-id="76383-522">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="76383-523">禁用和过期的机密</span><span class="sxs-lookup"><span data-stu-id="76383-523">Disabled and expired secrets</span></span>

<span data-ttu-id="76383-524">禁用和过期的机密会引发 <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> 。</span><span class="sxs-lookup"><span data-stu-id="76383-524">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="76383-525">若要防止应用程序引发，请使用不同的配置提供程序提供配置，或更新禁用或过期的机密。</span><span class="sxs-lookup"><span data-stu-id="76383-525">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="76383-526">疑难解答</span><span class="sxs-lookup"><span data-stu-id="76383-526">Troubleshoot</span></span>

<span data-ttu-id="76383-527">当应用无法使用访问接口加载配置时，会将错误消息写入[ASP.NET Core 日志记录基础结构](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="76383-527">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="76383-528">以下条件将阻止加载配置：</span><span class="sxs-lookup"><span data-stu-id="76383-528">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="76383-529">Azure Active Directory 中未正确配置应用或证书。</span><span class="sxs-lookup"><span data-stu-id="76383-529">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="76383-530">Azure Key Vault 中不存在密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="76383-530">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="76383-531">应用无权访问密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="76383-531">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="76383-532">访问策略不包括 `Get` 和 `List` 权限。</span><span class="sxs-lookup"><span data-stu-id="76383-532">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="76383-533">在密钥保管库中，配置数据（名称-值对）被错误地命名、缺失、禁用或过期。</span><span class="sxs-lookup"><span data-stu-id="76383-533">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="76383-534">应用具有错误的密钥保管库名称（ `KeyVaultName` ）、Azure AD 应用程序 Id （ `AzureADApplicationId` ）或 Azure AD 证书指纹（ `AzureADCertThumbprint` ）。</span><span class="sxs-lookup"><span data-stu-id="76383-534">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="76383-535">在应用程序中，配置项（名称）的值不正确。</span><span class="sxs-lookup"><span data-stu-id="76383-535">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="76383-536">向密钥保管库添加应用的访问策略时，策略已创建，但未在**访问策略**UI 中选择 "**保存**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="76383-536">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="76383-537">其他资源</span><span class="sxs-lookup"><span data-stu-id="76383-537">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="76383-538">Microsoft Azure： Key Vault</span><span class="sxs-lookup"><span data-stu-id="76383-538">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="76383-539">Microsoft Azure： Key Vault 文档</span><span class="sxs-lookup"><span data-stu-id="76383-539">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="76383-540">如何为 Azure 密钥保管库生成和传输受 HSM 保护的密钥</span><span class="sxs-lookup"><span data-stu-id="76383-540">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="76383-541">KeyVaultClient 类</span><span class="sxs-lookup"><span data-stu-id="76383-541">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="76383-542">快速入门：使用 .NET Web 应用在 Azure Key Vault 中设置和检索机密</span><span class="sxs-lookup"><span data-stu-id="76383-542">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="76383-543">教程：如何将 Azure Key Vault 与通过 .NET 编写的 Azure Windows 虚拟机配合使用</span><span class="sxs-lookup"><span data-stu-id="76383-543">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

