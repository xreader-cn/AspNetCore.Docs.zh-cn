::: moniker range=">= aspnetcore-3.0"
Visual Studio 会显示以下对话框：

![此项目配置为使用 SSL。 要避免浏览器中出现 SSL 警告，可以选择信任 ASP.NET Core 生成的自签名证书。 要信任 ASP.NET Core SSL 证书吗？](~/getting-started/_static/trustCert-3x.png)

如信任 ASP.NET Core SSL 证书，请选择“是”  。

将显示以下对话框：

![安全警告对话](~/getting-started/_static/cert.png)

如果你同意信任开发证书，请选择“是”。 

有关详细信息，请参阅[信任 ASP.NET Core HTTPS 开发证书](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)。
::: moniker-end

::: moniker range="< aspnetcore-3.0"
Visual Studio 会显示以下对话框：

![此项目配置为使用 SSL。 要避免浏览器中出现 SSL 警告，可以选择信任 IIS Express 生成的自签名证书。 要信任 IIS Express SSL 证书吗？](~/getting-started/_static/trustCert.png)

如果信任 IIS Express SSL 证书，请选择“是”  。

将显示以下对话框：

![安全警告对话](~/getting-started/_static/cert.png)

如果你同意信任开发证书，请选择“是”。 

有关详细信息，请参阅[信任 ASP.NET Core HTTPS 开发证书](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)。
::: moniker-end