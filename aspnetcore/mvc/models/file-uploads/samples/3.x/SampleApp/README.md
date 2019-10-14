# <a name="upload-files-sample-app"></a>上传文件示例应用

本示例应用演示[在 ASP.NET Core 中上传文件](https://docs.microsoft.com/aspnet/core/mvc/models/file-uploads)主题中所述的概念。

## <a name="security-considerations"></a>安全注意事项

向用户提供向服务器上传文件的功能时，必须格外小心。 攻击者可能会执行[拒绝服务](/windows-hardware/drivers/ifs/denial-of-service)攻击，尝试上传病毒或恶意软件，或尝试以其他方式破坏网络和服务器。

降低成功攻击可能性的安全措施如下：

* 将文件上传到系统的专用文件上传区域，最好是非系统驱动器。 使用专用位置便于对上传的文件实施安全措施。 禁用对文件上传位置的执行权限。&dagger;
* 切勿将上传的文件保存在与应用相同的目录树中。&dagger;
* 使用应用确定的安全的文件名。 请勿使用用户输入提供的文件名，或上传的文件的不受信任的文件名。&dagger;
* 仅允许使用一组特定的已批准文件扩展名。&dagger;
* 检查文件格式签名，防止用户上传伪装的文件（例如，上传带有 .txt 扩展名的 .exe 文件）。   &dagger;
* 验证是否也在服务器上执行了客户端检查。 客户端检查很容易规避。&dagger;
* 检查上传文件大小，防止上传比预期大的文件。&dagger;
* 文件不应该被具有相同名称的上传文件覆盖时，先在数据库或物理存储上检查文件名，然后再上传文件。
* **先对上传的内容运行病毒/恶意软件扫描程序，然后再存储文件。**

&dagger;示例应用演示了符合条件的方法。

> [!WARNING]
> 将恶意代码上传到系统通常是执行代码的第一步，这些代码可以：
>
> * 完全接管系统。
> * 重载系统，导致系统崩溃。
> * 泄露用户或系统数据。
> * 将涂鸦应用于公共 UI。
>
> 有关在接受用户文件时减少攻击外围应用的信息，请参阅以下资源：
>
> * [Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload)（不受限制的文件上传）
> * [Azure 安全性：确保接受用户的文件时实施适当的控制](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

有关其他信息，请参阅[在 ASP.NET Core 中上传文件](https://docs.microsoft.com/aspnet/core/mvc/models/file-uploads)。

## <a name="how-to-use-the-sample"></a>如何使用示例

在 appsettings.json 文件中： 

1. 设置存储文件的路径 (`StoredFilesPath`)。

   * 示例应用将值设置为 `c:\\files`，此值假定系统的 C: 驱动器根目录中存在名为“files”  的文件夹。
   * 该路径必须存在。 在系统的 C: 驱动器上创建“files”  文件夹，或将路径设置为合适的位置。
   * 应用的进程需要对路径具有读取/写入权限。
   * **重要说明！** 禁用路径中所有用户的执行权限。

1. 设置文件大小上限 (`FileSizeLimit`)（以字节为单位）。 示例应用的默认值 `2097152`（2,097,152 字节）允许最多上传 2 MB 的文件。
