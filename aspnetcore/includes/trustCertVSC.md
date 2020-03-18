* 通过运行以下命令来信任 HTTPS 开发证书：

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  上述命令在 Linux 上无效。 有关信任证书的详细信息，请参阅 Linux 发行版的文档。

  以上命令会显示以下对话：

  ![安全警告对话](~/getting-started/_static/cert.png)

* 如果你同意信任开发证书，请选择“是”。

  有关详细信息，请参阅[信任 ASP.NET Core HTTPS 开发证书](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)。
  
