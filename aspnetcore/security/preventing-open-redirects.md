---
title: 防止在 ASP.NET Core 中的打开重定向攻击
author: ardalis
description: 演示如何阻止对 ASP.NET Core 应用打开重定向攻击
ms.author: riande
ms.date: 07/07/2017
uid: security/preventing-open-redirects
ms.openlocfilehash: 9d8cac8708fe9aeadba5af1287362a20df7f6bfe
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815489"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a>防止在 ASP.NET Core 中的打开重定向攻击

将重定向到的 URL，例如查询字符串或窗体数据的请求通过指定的 web 应用可以有可能被篡改，同时将用户重定向到恶意的外部 URL。 此篡改称为开放重定向攻击。

每当应用程序逻辑将重定向到指定的 URL，必须验证重定向 URL 尚未被篡改。 ASP.NET Core 具有内置功能来防止应用打开重定向 （也称为打开重定向） 攻击。

## <a name="what-is-an-open-redirect-attack"></a>打开重定向攻击是什么？

Web 应用程序频繁地将用户重定向到登录页时他们访问要求进行身份验证的资源。 重定向通常包括`returnUrl`查询字符串参数，以便用户可以返回到最初请求的 URL 后它们已成功登录。 对用户进行身份验证后，它们重定向到他们具有最初请求的 URL。

因为请求的查询字符串中指定的目标 URL，则恶意用户可能篡改在查询字符串。 被篡改的查询字符串可能允许将用户重定向到外部、 恶意站点的站点。 这一技术称为开放重定向 （或重定向） 攻击。

### <a name="an-example-attack"></a>示例攻击

恶意用户可以开发目的是允许对用户的凭据和敏感信息的恶意用户访问的攻击。 若要开始攻击，恶意用户使用户能够单击指向你网站的登录页面的`returnUrl`添加到的 URL 查询字符串值。 例如，假设应用程序在`contoso.com`包括在登录页`http://contoso.com/Account/LogOn?returnUrl=/Home/About`。 攻击遵循以下步骤：

1. 用户单击的恶意链接`http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn`(第二个 URL 为"contoso**1**.com"，而非"contoso.com")。
2. 在用户成功登录。
3. 用户 （按站点） 重定向到`http://contoso1.com/Account/LogOn`（看上去确实像真正的站点的恶意网站）。
4. 在用户再次登录 （使恶意网站其凭据） 和重定向回真正的站点。

用户可能认为其第一次尝试登录失败，且其第二次尝试成功。 用户很可能仍不知道其凭据被透露。

![打开重定向攻击过程](preventing-open-redirects/_static/open-redirection-attack-process.png)

除了登录页的某些站点提供重定向页或终结点。 假设您的应用程序的页面包含打开的重定向， `/Home/Redirect`。 攻击者可以创建，例如，将转到一封电子邮件中的链接`[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`。 典型的用户将查看该 URL，请参阅它开始于你的站点名称。 信任的他们会单击此链接。 打开重定向然后会将用户发送到网页仿冒站点，这看起来与你相同，并且用户很可能会登录到他们认为是你的站点。

## <a name="protecting-against-open-redirect-attacks"></a>打开重定向攻击保护

当开发 web 应用程序，将所有用户提供的数据视为不受信任。 如果你的应用程序具有基于 URL 的内容将用户重定向的功能，请确保，这种重定向仅这样的是本地应用程序中 （或已知的 URL，没有任何可能会在查询字符串中提供的 URL）。

### <a name="localredirect"></a>LocalRedirect

使用`LocalRedirect`帮助器方法基`Controller`类：

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

`LocalRedirect` 如果指定了非本地 URL，将引发异常。 否则，它的行为就像`Redirect`方法。

### <a name="islocalurl"></a>IsLocalUrl

使用[IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_)方法来测试之前将重定向 Url:

下面的示例演示如何检查 URL 重定向之前是否是本地。

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

`IsLocalUrl`方法可防止用户无意中重定向到恶意站点。 你可以记录非本地 URL 提供在本地 URL 的预期的情况下时提供的 URL 的详细信息。 日志记录重定向 Url 可用于诊断重定向攻击。
