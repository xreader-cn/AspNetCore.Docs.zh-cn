---
title: 为 ASP.NET Core 中的 TOTP 验证器应用启用 QR 代码生成
author: rick-anderson
description: 了解如何为使用 ASP.NET Core 双因素身份验证的 TOTP 验证器应用启用 QR 码生成。
ms.author: riande
ms.date: 08/14/2018
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
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: b778e7238911ec9966edf7f0f7becd113b1e197a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060828"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a><span data-ttu-id="c5ccb-103">为 ASP.NET Core 中的 TOTP 验证器应用启用 QR 代码生成</span><span class="sxs-lookup"><span data-stu-id="c5ccb-103">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="c5ccb-104">QR 码需要 ASP.NET Core 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-104">QR Codes requires ASP.NET Core 2.0 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="c5ccb-105">ASP.NET Core 附带了对验证程序应用程序的支持，以便进行单个身份验证。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-105">ASP.NET Core ships with support for authenticator applications for individual authentication.</span></span> <span data-ttu-id="c5ccb-106">双因素身份验证 (2FA) 验证器应用，使用基于时间的一次性密码算法 (TOTP) ，是2FA 的行业建议方法。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-106">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="c5ccb-107">2FA 使用 TOTP 是 SMS 2FA 的首选。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-107">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="c5ccb-108">验证器应用提供6到8位的数字代码，用户在确认其用户名和密码后必须输入。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-108">An authenticator app provides a 6 to 8 digit code which users must enter after confirming their username and password.</span></span> <span data-ttu-id="c5ccb-109">通常会在智能手机上安装验证器应用。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-109">Typically an authenticator app is installed on a smart phone.</span></span>

<span data-ttu-id="c5ccb-110">ASP.NET Core web 应用模板支持验证器，但不要为 QRCode 生成提供支持。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-110">The ASP.NET Core web app templates support authenticators, but don't provide support for QRCode generation.</span></span> <span data-ttu-id="c5ccb-111">QRCode 生成器简化了2FA 的设置。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-111">QRCode generators ease the setup of 2FA.</span></span> <span data-ttu-id="c5ccb-112">本文档将指导你完成将 [QR 代码](https://wikipedia.org/wiki/QR_code) 生成添加到 "2FA" 配置页的步骤。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-112">This document will guide you through adding [QR Code](https://wikipedia.org/wiki/QR_code) generation to the 2FA configuration page.</span></span>

<span data-ttu-id="c5ccb-113">使用外部身份验证提供程序（如 [Google](xref:security/authentication/google-logins) 或 [Facebook](xref:security/authentication/facebook-logins)）不会进行双重身份验证。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-113">Two factor authentication does not happen using an external authentication provider, such as [Google](xref:security/authentication/google-logins) or [Facebook](xref:security/authentication/facebook-logins).</span></span> <span data-ttu-id="c5ccb-114">外部登录名受外部登录提供程序所提供的任何机制的保护。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-114">External logins are protected by whatever mechanism the external login provider provides.</span></span> <span data-ttu-id="c5ccb-115">例如，请考虑 [Microsoft](xref:security/authentication/microsoft-logins) 身份验证提供程序需要硬件密钥或其他2FA 方法。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-115">Consider, for example, the [Microsoft](xref:security/authentication/microsoft-logins) authentication provider requires a hardware key or another 2FA approach.</span></span> <span data-ttu-id="c5ccb-116">如果默认模板强制实施了 "本地" 2FA，则用户需要满足两种2FA 方法，这并不是一种常用方案。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-116">If the default templates enforced "local" 2FA then users would be required to satisfy two 2FA approaches, which is not a commonly used scenario.</span></span>

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a><span data-ttu-id="c5ccb-117">将 QR 代码添加到 "2FA" 配置页</span><span class="sxs-lookup"><span data-stu-id="c5ccb-117">Adding QR Codes to the 2FA configuration page</span></span>

<span data-ttu-id="c5ccb-118">这些说明使用存储库中的 *qrcode.js* https://davidshimjs.github.io/qrcodejs/ 。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-118">These instructions use *qrcode.js* from the https://davidshimjs.github.io/qrcodejs/ repo.</span></span>

* <span data-ttu-id="c5ccb-119">将 [qrcode.js javascript 库](https://davidshimjs.github.io/qrcodejs/) 下载到 `wwwroot\lib` 项目中的文件夹。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-119">Download the [qrcode.js javascript library](https://davidshimjs.github.io/qrcodejs/) to the `wwwroot\lib` folder in your project.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="c5ccb-120">按照 [基架 Identity](xref:security/authentication/scaffold-identity)中的说明生成 */Areas/ Identity /Pages/Account/Manage/EnableAuthenticator.cshtml* 。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-120">Follow the instructions in [Scaffold Identity](xref:security/authentication/scaffold-identity) to generate */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml* .</span></span>
* <span data-ttu-id="c5ccb-121">在 */Areas/ Identity /Pages/Account/Manage/EnableAuthenticator.cshtml* 中，找到 `Scripts` 文件末尾的部分：</span><span class="sxs-lookup"><span data-stu-id="c5ccb-121">In */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml* , locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="c5ccb-122">在 " *页面/帐户/管理/EnableAuthenticator* " (Razor 页面) 或 *视图/管理/EnableAuthenticator* (MVC) 中，找到 `Scripts` 文件末尾的部分：</span><span class="sxs-lookup"><span data-stu-id="c5ccb-122">In *Pages/Account/Manage/EnableAuthenticator.cshtml* (Razor Pages) or *Views/Manage/EnableAuthenticator.cshtml* (MVC), locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* <span data-ttu-id="c5ccb-123">更新 `Scripts` 节以添加对添加的库的引用 `qrcodejs` ，并调用以生成 QR 代码。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-123">Update the `Scripts` section to add a reference to the `qrcodejs` library you added and a call to generate the QR Code.</span></span> <span data-ttu-id="c5ccb-124">其外观应如下所示：</span><span class="sxs-lookup"><span data-stu-id="c5ccb-124">It should look as follows:</span></span>

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

* <span data-ttu-id="c5ccb-125">删除链接到这些说明的段落。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-125">Delete the paragraph which links you to these instructions.</span></span>

<span data-ttu-id="c5ccb-126">运行你的应用，并确保你可以扫描 QR 代码并验证验证器证明的代码。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-126">Run your app and ensure that you can scan the QR code and validate the code the authenticator proves.</span></span>

## <a name="change-the-site-name-in-the-qr-code"></a><span data-ttu-id="c5ccb-127">更改 QR 码中的站点名称</span><span class="sxs-lookup"><span data-stu-id="c5ccb-127">Change the site name in the QR Code</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="c5ccb-128">QR 代码中的站点名称取自最初创建项目时选择的项目名称。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-128">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="c5ccb-129">可以通过在 `GenerateQrCodeUri(string email, string unformattedKey)` */Areas/ Identity /Pages/Account/Manage/EnableAuthenticator.cshtml.cs* 中查找方法来更改该方法。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-129">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml.cs* .</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="c5ccb-130">QR 代码中的站点名称取自最初创建项目时选择的项目名称。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-130">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="c5ccb-131">您可以通过在 `GenerateQrCodeUri(string email, string unformattedKey)` " *页面/帐户/管理/EnableAuthenticator* " (Razor 页面) 文件或 *控制器/ManageController* (MVC) 文件中查找方法来更改该方法。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-131">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (Razor Pages) file or the *Controllers/ManageController.cs* (MVC) file.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="c5ccb-132">模板中的默认代码如下所示：</span><span class="sxs-lookup"><span data-stu-id="c5ccb-132">The default code from the template looks as follows:</span></span>

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenticatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

<span data-ttu-id="c5ccb-133">对的调用中的第二个参数 `string.Format` 是您的站点名称，从您的解决方案名称获取。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-133">The second parameter in the call to `string.Format` is your site name, taken from your solution name.</span></span> <span data-ttu-id="c5ccb-134">可以将其更改为任何值，但必须始终以 URL 编码。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-134">It can be changed to any value, but it must always be URL encoded.</span></span>

## <a name="using-a-different-qr-code-library"></a><span data-ttu-id="c5ccb-135">使用其他 QR 码库</span><span class="sxs-lookup"><span data-stu-id="c5ccb-135">Using a different QR Code library</span></span>

<span data-ttu-id="c5ccb-136">可以将 QR 代码库替换为首选库。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-136">You can replace the QR Code library with your preferred library.</span></span> <span data-ttu-id="c5ccb-137">HTML 包含一个 `qrCode` 元素，你可以在其中放置 QR 码，方法是使用库提供的任何机制。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-137">The HTML contains a `qrCode` element into which you can place a QR Code by whatever mechanism your library provides.</span></span>

<span data-ttu-id="c5ccb-138">可以在中找到 QR 码的格式正确的 URL：</span><span class="sxs-lookup"><span data-stu-id="c5ccb-138">The correctly formatted URL for the QR Code is available in the:</span></span>

* <span data-ttu-id="c5ccb-139">`AuthenticatorUri` 模型的属性。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-139">`AuthenticatorUri` property of the model.</span></span>
* <span data-ttu-id="c5ccb-140">`data-url` 元素中的属性 `qrCodeData` 。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-140">`data-url` property in the `qrCodeData` element.</span></span>

## <a name="totp-client-and-server-time-skew"></a><span data-ttu-id="c5ccb-141">TOTP 客户端和服务器时间偏差</span><span class="sxs-lookup"><span data-stu-id="c5ccb-141">TOTP client and server time skew</span></span>

<span data-ttu-id="c5ccb-142">TOTP (基于时间的 One-Time 密码) 身份验证取决于服务器和验证器设备的时间是否准确。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-142">TOTP (Time-based One-Time Password) authentication depends on both the server and authenticator device having an accurate time.</span></span> <span data-ttu-id="c5ccb-143">标记只持续30秒。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-143">Tokens only last for 30 seconds.</span></span> <span data-ttu-id="c5ccb-144">如果 TOTP 2FA 登录失败，请检查服务器时间是否准确，并最好是同步到准确的 NTP 服务。</span><span class="sxs-lookup"><span data-stu-id="c5ccb-144">If TOTP 2FA logins are failing, check that the server time is accurate, and preferably synchronized to an accurate NTP service.</span></span>

::: moniker-end
