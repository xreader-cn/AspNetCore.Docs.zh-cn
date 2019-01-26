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
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a>启用在 ASP.NET Core TOTP 身份验证器应用的 QR 代码生成

::: moniker range="<= aspnetcore-2.0"

QR 代码需要 ASP.NET Core 2.0 或更高版本。

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

ASP.NET Core 附带的单个身份验证的身份验证器应用程序的支持。 两个身份验证 (2FA) 身份验证器应用程序，使用基于时间的一次性密码算法 (TOTP)，是推荐的方法用于 2FA 的行业。 2FA 使用 TOTP 优于 SMS 2FA。 身份验证器应用程序提供了哪些用户必须输入其用户名和密码确认后一个 6 到 8 位代码。 通常智能手机上安装验证器应用。

ASP.NET Core web 应用程序模板支持身份验证器，但不提供对 QRCode 生成的支持。 QRCode 生成器简化 2FA 的安装程序。 本文档将指导你完成添加[QR 码](https://wikipedia.org/wiki/QR_code)生成到 2FA 配置页。

如使用外部身份验证提供程序，不会发生双因素身份验证[Google](xref:security/authentication/google-logins)或[Facebook](xref:security/authentication/facebook-logins)。 受保护的外部登录名通过任何机制提供外部登录提供程序。 例如，请考虑[Microsoft](xref:security/authentication/microsoft-logins)身份验证提供程序需要硬件密钥或另一种 2FA 方法。 如果默认模板强制实施"local"2FA 然后会要求用户以满足两个 2FA 方法，这是不常使用的方案。

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a>将 QR 代码添加到 2FA 配置页

使用这些说明*qrcode.js*从 https://davidshimjs.github.io/qrcodejs/存储库。

* 下载[qrcode.js javascript 库](https://davidshimjs.github.io/qrcodejs/)到`wwwroot\lib`项目文件夹中的。

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* 按照中的说明[基架标识](xref:security/authentication/scaffold-identity)生成 */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*。
* 在中 */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*，找到`Scripts`文件的末尾部分：

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* 在中*Pages/Account/Manage/EnableAuthenticator.cshtml* （Razor 页面） 或*Views/Manage/EnableAuthenticator.cshtml* (MVC) 中，找到`Scripts`文件的末尾部分：

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* 更新`Scripts`部分，以添加对引用`qrcodejs`您添加的库和生成 QR 代码的调用。 其外观应按如下所示：

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

* 删除的段落，提供了指向这些说明。

运行你的应用，并确保可以扫描 QR 代码并验证身份验证者证明的代码。

## <a name="change-the-site-name-in-the-qr-code"></a>更改 QR 代码中的站点名称

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

QR 代码中的站点名称是来自在最初创建你的项目时选择的项目名称。 您可以通过查找对其进行更改`GenerateQrCodeUri(string email, string unformattedKey)`中的方法 */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

QR 代码中的站点名称是来自在最初创建你的项目时选择的项目名称。 您可以通过查找对其进行更改`GenerateQrCodeUri(string email, string unformattedKey)`中的方法*Pages/Account/Manage/EnableAuthenticator.cshtml.cs* （Razor 页面） 文件或*Controllers/ManageController.cs* (MVC) 文件。

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

模板中的默认代码如下所示：

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

对的调用中的第二个参数`string.Format`是你的站点名称，从你的解决方案名称。 可以更改为任何值，但它必须始终为 URL 编码。

## <a name="using-a-different-qr-code-library"></a>使用不同的 QR 代码库

QR 代码库可以替换你首选的库。 包含 HTML`qrCode`元素在其中可以通过任何机制将 QR 代码库提供。

QR 码的格式正确 URL 现已推出:

* `AuthenticatorUri` 模型的属性。
* `data-url` 中的属性`qrCodeData`元素。

## <a name="totp-client-and-server-time-skew"></a>TOTP 客户端和服务器时间倾斜

TOTP （基于时间的一次性密码） 身份验证依赖于服务器和身份验证器的设备具有准确的时间。 令牌仅持续 30 秒。 如果 TOTP 2FA 登录名失败，则检查服务器时间是准确的并且最好是同步到准确的 NTP 服务。

::: moniker-end
