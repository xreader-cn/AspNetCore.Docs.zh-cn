---
title: 启用在 ASP.NET Core TOTP 身份验证器应用的 QR 代码生成
author: rick-anderson
description: 了解如何启用 TOTP 使用 ASP.NET Core 双因素身份验证的身份验证器应用的 QR 代码生成。
ms.author: riande
ms.date: 08/14/2018
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: cf99cc21a7a1bb4d01c7cc092106d23375a1a76f
ms.sourcegitcommit: ca5f03210bedc61c6639a734ae5674bfe095dee8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2019
ms.locfileid: "55073122"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a><span data-ttu-id="54a18-103">启用在 ASP.NET Core TOTP 身份验证器应用的 QR 代码生成</span><span class="sxs-lookup"><span data-stu-id="54a18-103">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="54a18-104">QR 代码需要 ASP.NET Core 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="54a18-104">QR Codes requires ASP.NET Core 2.0 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="54a18-105">ASP.NET Core 附带的单个身份验证的身份验证器应用程序的支持。</span><span class="sxs-lookup"><span data-stu-id="54a18-105">ASP.NET Core ships with support for authenticator applications for individual authentication.</span></span> <span data-ttu-id="54a18-106">两个身份验证 (2FA) 身份验证器应用程序，使用基于时间的一次性密码算法 (TOTP)，是推荐的方法用于 2FA 的行业。</span><span class="sxs-lookup"><span data-stu-id="54a18-106">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="54a18-107">2FA 使用 TOTP 优于 SMS 2FA。</span><span class="sxs-lookup"><span data-stu-id="54a18-107">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="54a18-108">身份验证器应用程序提供了哪些用户必须输入其用户名和密码确认后一个 6 到 8 位代码。</span><span class="sxs-lookup"><span data-stu-id="54a18-108">An authenticator app provides a 6 to 8 digit code which users must enter after confirming their username and password.</span></span> <span data-ttu-id="54a18-109">通常智能手机上安装验证器应用。</span><span class="sxs-lookup"><span data-stu-id="54a18-109">Typically an authenticator app is installed on a smart phone.</span></span>

<span data-ttu-id="54a18-110">ASP.NET Core web 应用程序模板支持身份验证器，但不提供对 QRCode 生成的支持。</span><span class="sxs-lookup"><span data-stu-id="54a18-110">The ASP.NET Core web app templates support authenticators, but don't provide support for QRCode generation.</span></span> <span data-ttu-id="54a18-111">QRCode 生成器简化 2FA 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="54a18-111">QRCode generators ease the setup of 2FA.</span></span> <span data-ttu-id="54a18-112">本文档将指导你完成添加[QR 码](https://wikipedia.org/wiki/QR_code)生成到 2FA 配置页。</span><span class="sxs-lookup"><span data-stu-id="54a18-112">This document will guide you through adding [QR Code](https://wikipedia.org/wiki/QR_code) generation to the 2FA configuration page.</span></span>

<span data-ttu-id="54a18-113">如使用外部身份验证提供程序，不会发生双因素身份验证[Google](xref:security/authentication/google-logins)或[Facebook](xref:security/authentication/facebook-logins)。</span><span class="sxs-lookup"><span data-stu-id="54a18-113">Two factor authentication does not happen using an external authentication provider, such as [Google](xref:security/authentication/google-logins) or [Facebook](xref:security/authentication/facebook-logins).</span></span> <span data-ttu-id="54a18-114">受保护的外部登录名通过任何机制提供外部登录提供程序。</span><span class="sxs-lookup"><span data-stu-id="54a18-114">External logins are protected by whatever mechanism the external login provider provides.</span></span> <span data-ttu-id="54a18-115">例如，请考虑[Microsoft](xref:security/authentication/microsoft-logins)身份验证提供程序需要硬件密钥或另一种 2FA 方法。</span><span class="sxs-lookup"><span data-stu-id="54a18-115">Consider, for example, the [Microsoft](xref:security/authentication/microsoft-logins) authentication provider requires a hardware key or another 2FA approach.</span></span> <span data-ttu-id="54a18-116">如果默认模板强制实施"local"2FA 然后会要求用户以满足两个 2FA 方法，这是不常使用的方案。</span><span class="sxs-lookup"><span data-stu-id="54a18-116">If the default templates enforced "local" 2FA then users would be required to satisfy two 2FA approaches, which is not a commonly used scenario.</span></span>

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a><span data-ttu-id="54a18-117">将 QR 代码添加到 2FA 配置页</span><span class="sxs-lookup"><span data-stu-id="54a18-117">Adding QR Codes to the 2FA configuration page</span></span>

<span data-ttu-id="54a18-118">使用这些说明*qrcode.js*从 https://davidshimjs.github.io/qrcodejs/存储库。</span><span class="sxs-lookup"><span data-stu-id="54a18-118">These instructions use *qrcode.js* from the https://davidshimjs.github.io/qrcodejs/ repo.</span></span>

* <span data-ttu-id="54a18-119">下载[qrcode.js javascript 库](https://davidshimjs.github.io/qrcodejs/)到`wwwroot\lib`项目文件夹中的。</span><span class="sxs-lookup"><span data-stu-id="54a18-119">Download the [qrcode.js javascript library](https://davidshimjs.github.io/qrcodejs/) to the `wwwroot\lib` folder in your project.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="54a18-120">按照中的说明[基架标识](xref:security/authentication/scaffold-identity)生成 */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="54a18-120">Follow the instructions in [Scaffold Identity](xref:security/authentication/scaffold-identity) to generate */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span></span>
* <span data-ttu-id="54a18-121">在中 */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*，找到`Scripts`文件的末尾部分：</span><span class="sxs-lookup"><span data-stu-id="54a18-121">In */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*, locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="54a18-122">在中*Pages/Account/Manage/EnableAuthenticator.cshtml* （Razor 页面） 或*Views/Manage/EnableAuthenticator.cshtml* (MVC) 中，找到`Scripts`文件的末尾部分：</span><span class="sxs-lookup"><span data-stu-id="54a18-122">In *Pages/Account/Manage/EnableAuthenticator.cshtml* (Razor Pages) or *Views/Manage/EnableAuthenticator.cshtml* (MVC), locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* <span data-ttu-id="54a18-123">更新`Scripts`部分，以添加对引用`qrcodejs`您添加的库和生成 QR 代码的调用。</span><span class="sxs-lookup"><span data-stu-id="54a18-123">Update the `Scripts` section to add a reference to the `qrcodejs` library you added and a call to generate the QR Code.</span></span> <span data-ttu-id="54a18-124">其外观应按如下所示：</span><span class="sxs-lookup"><span data-stu-id="54a18-124">It should look as follows:</span></span>

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* <span data-ttu-id="54a18-125">删除的段落，提供了指向这些说明。</span><span class="sxs-lookup"><span data-stu-id="54a18-125">Delete the paragraph which links you to these instructions.</span></span>

<span data-ttu-id="54a18-126">运行你的应用，并确保可以扫描 QR 代码并验证身份验证者证明的代码。</span><span class="sxs-lookup"><span data-stu-id="54a18-126">Run your app and ensure that you can scan the QR code and validate the code the authenticator proves.</span></span>

## <a name="change-the-site-name-in-the-qr-code"></a><span data-ttu-id="54a18-127">更改 QR 代码中的站点名称</span><span class="sxs-lookup"><span data-stu-id="54a18-127">Change the site name in the QR Code</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="54a18-128">QR 代码中的站点名称是来自在最初创建你的项目时选择的项目名称。</span><span class="sxs-lookup"><span data-stu-id="54a18-128">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="54a18-129">您可以通过查找对其进行更改`GenerateQrCodeUri(string email, string unformattedKey)`中的方法 */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="54a18-129">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="54a18-130">QR 代码中的站点名称是来自在最初创建你的项目时选择的项目名称。</span><span class="sxs-lookup"><span data-stu-id="54a18-130">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="54a18-131">您可以通过查找对其进行更改`GenerateQrCodeUri(string email, string unformattedKey)`中的方法*Pages/Account/Manage/EnableAuthenticator.cshtml.cs* （Razor 页面） 文件或*Controllers/ManageController.cs* (MVC) 文件。</span><span class="sxs-lookup"><span data-stu-id="54a18-131">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (Razor Pages) file or the *Controllers/ManageController.cs* (MVC) file.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="54a18-132">模板中的默认代码如下所示：</span><span class="sxs-lookup"><span data-stu-id="54a18-132">The default code from the template looks as follows:</span></span>

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenicatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

<span data-ttu-id="54a18-133">对的调用中的第二个参数`string.Format`是你的站点名称，从你的解决方案名称。</span><span class="sxs-lookup"><span data-stu-id="54a18-133">The second parameter in the call to `string.Format` is your site name, taken from your solution name.</span></span> <span data-ttu-id="54a18-134">可以更改为任何值，但它必须始终为 URL 编码。</span><span class="sxs-lookup"><span data-stu-id="54a18-134">It can be changed to any value, but it must always be URL encoded.</span></span>

## <a name="using-a-different-qr-code-library"></a><span data-ttu-id="54a18-135">使用不同的 QR 代码库</span><span class="sxs-lookup"><span data-stu-id="54a18-135">Using a different QR Code library</span></span>

<span data-ttu-id="54a18-136">QR 代码库可以替换你首选的库。</span><span class="sxs-lookup"><span data-stu-id="54a18-136">You can replace the QR Code library with your preferred library.</span></span> <span data-ttu-id="54a18-137">包含 HTML`qrCode`元素在其中可以通过任何机制将 QR 代码库提供。</span><span class="sxs-lookup"><span data-stu-id="54a18-137">The HTML contains a `qrCode` element into which you can place a QR Code by whatever mechanism your library provides.</span></span>

<span data-ttu-id="54a18-138">QR 码的格式正确 URL 现已推出:</span><span class="sxs-lookup"><span data-stu-id="54a18-138">The correctly formatted URL for the QR Code is available in the:</span></span>

* <span data-ttu-id="54a18-139">`AuthenticatorUri` 模型的属性。</span><span class="sxs-lookup"><span data-stu-id="54a18-139">`AuthenticatorUri` property of the model.</span></span>
* <span data-ttu-id="54a18-140">`data-url` 中的属性`qrCodeData`元素。</span><span class="sxs-lookup"><span data-stu-id="54a18-140">`data-url` property in the `qrCodeData` element.</span></span>

## <a name="totp-client-and-server-time-skew"></a><span data-ttu-id="54a18-141">TOTP 客户端和服务器时间倾斜</span><span class="sxs-lookup"><span data-stu-id="54a18-141">TOTP client and server time skew</span></span>

<span data-ttu-id="54a18-142">TOTP （基于时间的一次性密码） 身份验证依赖于服务器和身份验证器的设备具有准确的时间。</span><span class="sxs-lookup"><span data-stu-id="54a18-142">TOTP (Time-based One-Time Password) authentication depends on both the server and authenticator device having an accurate time.</span></span> <span data-ttu-id="54a18-143">令牌仅持续 30 秒。</span><span class="sxs-lookup"><span data-stu-id="54a18-143">Tokens only last for 30 seconds.</span></span> <span data-ttu-id="54a18-144">如果 TOTP 2FA 登录名失败，则检查服务器时间是准确的并且最好是同步到准确的 NTP 服务。</span><span class="sxs-lookup"><span data-stu-id="54a18-144">If TOTP 2FA logins are failing, check that the server time is accurate, and preferably synchronized to an accurate NTP service.</span></span>

::: moniker-end
