# <a name="upload-files-sample-app"></a><span data-ttu-id="f5c0b-101">上传文件示例应用</span><span class="sxs-lookup"><span data-stu-id="f5c0b-101">Upload Files Sample App</span></span>

<span data-ttu-id="f5c0b-102">本示例应用演示[在 ASP.NET Core 中上传文件](https://docs.microsoft.com/aspnet/core/mvc/models/file-uploads)主题中所述的概念。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-102">This sample app demonstrates concepts described in the [Upload files in ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/models/file-uploads) topic.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="f5c0b-103">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="f5c0b-103">Security considerations</span></span>

<span data-ttu-id="f5c0b-104">向用户提供向服务器上传文件的功能时，必须格外小心。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-104">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="f5c0b-105">攻击者可能会执行[拒绝服务](/windows-hardware/drivers/ifs/denial-of-service)攻击，尝试上传病毒或恶意软件，或尝试以其他方式破坏网络和服务器。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-105">Attackers may execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks, attempt to upload viruses or malware, or attempt to compromise networks and servers in other ways.</span></span>

<span data-ttu-id="f5c0b-106">降低成功攻击可能性的安全措施如下：</span><span class="sxs-lookup"><span data-stu-id="f5c0b-106">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="f5c0b-107">将文件上传到系统的专用文件上传区域，最好是非系统驱动器。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-107">Upload files to a dedicated file upload area on the system, preferably to a non-system drive.</span></span> <span data-ttu-id="f5c0b-108">使用专用位置便于对上传的文件实施安全措施。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-108">Use of a dedicated location makes it easier to impose security measures on uploaded files.</span></span> <span data-ttu-id="f5c0b-109">禁用对文件上传位置的执行权限。&dagger;</span><span class="sxs-lookup"><span data-stu-id="f5c0b-109">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="f5c0b-110">切勿将上传的文件保存在与应用相同的目录树中。&dagger;</span><span class="sxs-lookup"><span data-stu-id="f5c0b-110">Never persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="f5c0b-111">使用应用确定的安全的文件名。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-111">Use a safe file name determined by the app.</span></span> <span data-ttu-id="f5c0b-112">请勿使用用户输入提供的文件名，或上传的文件的不受信任的文件名。&dagger;</span><span class="sxs-lookup"><span data-stu-id="f5c0b-112">Don't use a file name provided by user input or the untrusted file name of the uploaded file.&dagger;</span></span>
* <span data-ttu-id="f5c0b-113">仅允许使用一组特定的已批准文件扩展名。&dagger;</span><span class="sxs-lookup"><span data-stu-id="f5c0b-113">Only allow a specific set of approved file extensions.&dagger;</span></span>
* <span data-ttu-id="f5c0b-114">检查文件格式签名，防止用户上传伪装的文件（例如，上传带有 .txt 扩展名的 .exe 文件）。&dagger;</span><span class="sxs-lookup"><span data-stu-id="f5c0b-114">Check the file format signature to prevent a user from uploading a masqueraded file (for example, uploading an *.exe* file with a *.txt* extension).&dagger;</span></span>
* <span data-ttu-id="f5c0b-115">验证是否也在服务器上执行了客户端检查。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-115">Verify that client-side checks are also performed on the server.</span></span> <span data-ttu-id="f5c0b-116">客户端检查很容易规避。&dagger;</span><span class="sxs-lookup"><span data-stu-id="f5c0b-116">Client-side checks are easy to circumvent.&dagger;</span></span>
* <span data-ttu-id="f5c0b-117">检查上传文件大小，防止上传比预期大的文件。&dagger;</span><span class="sxs-lookup"><span data-stu-id="f5c0b-117">Check the size of the upload and prevent uploads that are larger than expected.&dagger;</span></span>
* <span data-ttu-id="f5c0b-118">文件不应该被具有相同名称的上传文件覆盖时，先在数据库或物理存储上检查文件名，然后再上传文件。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-118">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="f5c0b-119">**先对上传的内容运行病毒/恶意软件扫描程序，然后再存储文件。**</span><span class="sxs-lookup"><span data-stu-id="f5c0b-119">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="f5c0b-120">&dagger;示例应用演示了符合条件的方法。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-120">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="f5c0b-121">将恶意代码上传到系统通常是执行代码的第一步，这些代码可以：</span><span class="sxs-lookup"><span data-stu-id="f5c0b-121">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="f5c0b-122">完全接管系统。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-122">Completely takeover a system.</span></span>
> * <span data-ttu-id="f5c0b-123">重载系统，导致系统崩溃。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-123">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="f5c0b-124">泄露用户或系统数据。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-124">Compromise user or system data.</span></span>
> * <span data-ttu-id="f5c0b-125">将涂鸦应用于公共 UI。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-125">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="f5c0b-126">有关在接受用户文件时减少攻击外围应用的信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="f5c0b-126">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="f5c0b-127">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload)（不受限制的文件上传）</span><span class="sxs-lookup"><span data-stu-id="f5c0b-127">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="f5c0b-128">Azure 安全性：确保在接受用户文件时采取适当的控制措施</span><span class="sxs-lookup"><span data-stu-id="f5c0b-128">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="f5c0b-129">有关其他信息，请参阅[在 ASP.NET Core 中上传文件](https://docs.microsoft.com/aspnet/core/mvc/models/file-uploads)。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-129">For additional information, see [Upload files in ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/models/file-uploads).</span></span>

## <a name="how-to-use-the-sample"></a><span data-ttu-id="f5c0b-130">如何使用示例</span><span class="sxs-lookup"><span data-stu-id="f5c0b-130">How to use the sample</span></span>

<span data-ttu-id="f5c0b-131">在 appsettings.json 文件中：</span><span class="sxs-lookup"><span data-stu-id="f5c0b-131">In the *appsettings.json* file:</span></span>

1. <span data-ttu-id="f5c0b-132">设置存储文件的路径 (`StoredFilesPath`)。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-132">Set the path for stored files (`StoredFilesPath`).</span></span>

   * <span data-ttu-id="f5c0b-133">示例应用将值设置为 `c:\\files`，此值假定系统的 C: 驱动器根目录中存在名为“files”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-133">The sample app sets the value to `c:\\files`, which assumes that a folder named *files* exists at the system's C: drive root.</span></span>
   * <span data-ttu-id="f5c0b-134">该路径必须存在。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-134">The path must exist.</span></span> <span data-ttu-id="f5c0b-135">在系统的 C: 驱动器上创建“files”文件夹，或将路径设置为合适的位置。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-135">Create a *files* folder on the system's C: drive or set the path to a suitable location.</span></span>
   * <span data-ttu-id="f5c0b-136">应用的进程需要对路径具有读取/写入权限。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-136">The app's process requires read/write permissions to the path.</span></span>
   * <span data-ttu-id="f5c0b-137">**重要说明！**</span><span class="sxs-lookup"><span data-stu-id="f5c0b-137">**IMPORTANT!**</span></span> <span data-ttu-id="f5c0b-138">禁用路径中所有用户的执行权限。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-138">Disable execute permissions for all users at the path.</span></span>

1. <span data-ttu-id="f5c0b-139">设置文件大小上限 (`FileSizeLimit`)（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-139">Set the file size limit (`FileSizeLimit`) in bytes.</span></span> <span data-ttu-id="f5c0b-140">示例应用的默认值 `2097152`（2,097,152 字节）允许最多上传 2 MB 的文件。</span><span class="sxs-lookup"><span data-stu-id="f5c0b-140">The sample app's default value of `2097152` (2,097,152 bytes) permits file uploads up to 2 MB.</span></span>
