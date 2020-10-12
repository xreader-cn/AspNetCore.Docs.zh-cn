---
title: ASP.NET Core Blazor 文件上传
author: guardrex
description: 了解如何使用 InputFile 组件在 Blazor 中上传文件。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/file-uploads
ms.openlocfilehash: 06d1464cb731a8008362fc911f463e4ff8a37b6b
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606654"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="c7c40-103">ASP.NET Core Blazor 文件上传</span><span class="sxs-lookup"><span data-stu-id="c7c40-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="c7c40-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="c7c40-104">By [Daniel Roth](https://github.com/danroth27) and [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="c7c40-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="c7c40-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c7c40-106">使用 `InputFile` 组件将浏览器文件数据（包括文件上传）读入 .NET 代码。</span><span class="sxs-lookup"><span data-stu-id="c7c40-106">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="c7c40-107">请始终遵循文件上传安全最佳做法。</span><span class="sxs-lookup"><span data-stu-id="c7c40-107">Always follow file upload security best practices.</span></span> <span data-ttu-id="c7c40-108">有关详细信息，请参阅 <xref:mvc/models/file-uploads#security-considerations>。</span><span class="sxs-lookup"><span data-stu-id="c7c40-108">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="c7c40-109">`InputFile` 组件</span><span class="sxs-lookup"><span data-stu-id="c7c40-109">`InputFile` component</span></span>

<span data-ttu-id="c7c40-110">`InputFile` 组件呈现为 `file` 类型的 HTML 输入。</span><span class="sxs-lookup"><span data-stu-id="c7c40-110">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="c7c40-111">默认情况下，用户选择单个文件。</span><span class="sxs-lookup"><span data-stu-id="c7c40-111">By default, the user selects single files.</span></span> <span data-ttu-id="c7c40-112">可添加 `multiple` 属性以允许用户一次上传多个文件。</span><span class="sxs-lookup"><span data-stu-id="c7c40-112">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="c7c40-113">当用户选择了一个或多个文件时，`InputFile` 组件将触发 `OnChange` 事件并传入 `InputFileChangeEventArgs`，以允许访问所选文件列表和各文件的详细信息。</span><span class="sxs-lookup"><span data-stu-id="c7c40-113">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="c7c40-114">从用户选择的文件中读取数据：</span><span class="sxs-lookup"><span data-stu-id="c7c40-114">To read data from a user-selected file:</span></span>

* <span data-ttu-id="c7c40-115">对文件调用 `OpenReadStream`，并从返回的流中进行读取。</span><span class="sxs-lookup"><span data-stu-id="c7c40-115">Call `OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="c7c40-116">有关详细信息，请参阅[文件流](#file-streams)部分。</span><span class="sxs-lookup"><span data-stu-id="c7c40-116">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="c7c40-117">请使用 `ReadAsync`。</span><span class="sxs-lookup"><span data-stu-id="c7c40-117">Use `ReadAsync`.</span></span> <span data-ttu-id="c7c40-118">默认情况下，`ReadAsync` 只允许读取 524,288 KB (512 KB) 以下的文件。</span><span class="sxs-lookup"><span data-stu-id="c7c40-118">By default, `ReadAsync` only allows reading a file smaller than 524,288 KB (512 KB) in size.</span></span> <span data-ttu-id="c7c40-119">此限制旨在防止开发人员意外地将大型文件读入到内存中。</span><span class="sxs-lookup"><span data-stu-id="c7c40-119">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="c7c40-120">如果必须支持较大的文件，则为所需的最大文件大小指定合理的近似值。</span><span class="sxs-lookup"><span data-stu-id="c7c40-120">Specify a reasonable approximation for the maximum expected file size if larger files must be supported.</span></span> <span data-ttu-id="c7c40-121">避免将传入的文件流直接读入到内存中。</span><span class="sxs-lookup"><span data-stu-id="c7c40-121">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="c7c40-122">例如，不要将文件字节复制到 <xref:System.IO.MemoryStream>，也不要以字节数组的形式进行读取。</span><span class="sxs-lookup"><span data-stu-id="c7c40-122">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="c7c40-123">这些方法可能会导致性能和安全问题，尤其是在 Blazor Server 中。</span><span class="sxs-lookup"><span data-stu-id="c7c40-123">These approaches can result in performance and security problems, especially in Blazor Server.</span></span> <span data-ttu-id="c7c40-124">请考虑将文件字节复制到外部存储（如 blob 或磁盘上的文件）。</span><span class="sxs-lookup"><span data-stu-id="c7c40-124">Instead, consider copying file bytes to an external store, such as a a blob or a file on disk.</span></span>

<span data-ttu-id="c7c40-125">接收图像文件的组件可以对文件调用 `RequestImageFileAsync` 便利方法，在图像流式传入应用之前，在浏览器的 JavaScript 运行时内调整图像数据的大小。</span><span class="sxs-lookup"><span data-stu-id="c7c40-125">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="c7c40-126">以下示例演示在组件中上传多个图像文件。</span><span class="sxs-lookup"><span data-stu-id="c7c40-126">The following example demonstrates multiple image file upload in a component.</span></span> <span data-ttu-id="c7c40-127">通过 `InputFileChangeEventArgs.GetMultipleFiles`，可以读取多个文件。</span><span class="sxs-lookup"><span data-stu-id="c7c40-127">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="c7c40-128">请指定希望读取的最大文件数，以防止恶意用户上传的文件数超过应用的预期值。</span><span class="sxs-lookup"><span data-stu-id="c7c40-128">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="c7c40-129">如果文件上传不支持多个文件，则可以通过 `InputFileChangeEventArgs.File` 读取第一个文件，并且只能读取此文件。</span><span class="sxs-lookup"><span data-stu-id="c7c40-129">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload does not support multiple files.</span></span>

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h4>Images</h4>

    <div class="card" style="width:30rem;">
        <div class="card-body">
            @foreach (var imageDataUrl in imageDataUrls)
            {
                <img class="rounded m-1" src="@imageDataUrl" />
            }
        </div>
    </div>
}

@code {
    IList<string> imageDataUrls = new List<string>();

    private async Task OnInputFileChange(InputFileChangeEventArgs e)
    {
        var maxAllowedFiles = 3;
        var format = "image/png";

        foreach (var imageFile in e.GetMultipleFiles(maxAllowedFiles))
        {
            var resizedImageFile = await imageFile.RequestImageFileAsync(format, 
                100, 100);
            var buffer = new byte[resizedImageFile.Size];
            await resizedImageFile.OpenReadStream().ReadAsync(buffer);
            var imageDataUrl = 
                $"data:{format};base64,{Convert.ToBase64String(buffer)}";
            imageDataUrls.Add(imageDataUrl);
        }
    }
}
```

<span data-ttu-id="c7c40-130">`IBrowserFile` 会以属性形式返回[浏览器公开的元数据](https://developer.mozilla.org/docs/Web/API/File#Instance_properties)。</span><span class="sxs-lookup"><span data-stu-id="c7c40-130">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="c7c40-131">此元数据可用于进行初步验证。</span><span class="sxs-lookup"><span data-stu-id="c7c40-131">This metadata can be useful to preliminary validation.</span></span> <span data-ttu-id="c7c40-132">有关示例，请参阅 [`FileUpload.razor` 和 `FilePreview.razor` 示例组件](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/)。</span><span class="sxs-lookup"><span data-stu-id="c7c40-132">For example, see the [`FileUpload.razor` and `FilePreview.razor` sample components](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span></span>

## <a name="file-streams"></a><span data-ttu-id="c7c40-133">文件流</span><span class="sxs-lookup"><span data-stu-id="c7c40-133">File streams</span></span>

<span data-ttu-id="c7c40-134">在 Blazor WebAssembly 应用中，数据直接流式传入浏览器中的 .NET 代码。</span><span class="sxs-lookup"><span data-stu-id="c7c40-134">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="c7c40-135">在 Blazor Server 应用中，从流中读取文件时，文件数据通过 SignalR 连接流式传入服务器上的 .NET 代码。</span><span class="sxs-lookup"><span data-stu-id="c7c40-135">In a Blazor Server app, the file data is streamed over the SignalR connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="c7c40-136">通过 [`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs)，可以配置 Blazor Server 的文件上传特性。</span><span class="sxs-lookup"><span data-stu-id="c7c40-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) allows configuring file upload characteristics for Blazor Server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c7c40-137">其他资源</span><span class="sxs-lookup"><span data-stu-id="c7c40-137">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
