# MediaService

The MediaService acts as a "gateway" to Umbraco data for operations which are related to media.

[Browse the API documentation for IMediaService interface](https://apidocs.umbraco.com/v13/csharp/api/Umbraco.Cms.Core.Services.IMediaService.html).

* **Namespace:** `Umbraco.Cms.Core.Services`
* **Assembly:** `Umbraco.Core.dll`

 All samples in this document will require reference to the following packages:

* [`Umbraco.Cms.Core`](https://www.nuget.org/packages/Umbraco.Cms.Core/)

Samples in this document will require the following using statements:

```csharp
using Umbraco.Cms.Core;
using Umbraco.Cms.Core.IO;
using Umbraco.Cms.Core.Models;
using Umbraco.Cms.Core.PropertyEditors;
using Umbraco.Cms.Core.Services;
using Umbraco.Cms.Core.Strings;
using Umbraco.Extensions;
```

## Getting the service

### Dependency Injection

If you wish to use the media service in a class, you need to specify the `IMediaService` interface in your constructor:

```csharp
public class MyClass
{
    private IMediaService _mediaService;

    public MyClass(IMediaService mediaService)
    {
        _mediaService = mediaService;
    }
}
```

In Razor views, you can access the media service through the `@inject` directive:

```csharp
@inject IMediaService MediaService
```

## Samples

### Creating a new folder

To create a new folder at the root of the media archive, your code could look like the following:

```csharp
// Initialize a new media at the root of the media archive
IMedia folder = _mediaService.CreateMedia("Samples Media Item Folder", Constants.System.Root, Constants.Conventions.MediaTypes.Folder);

// Save the folder
var result = _mediaService.Save(folder);
```

Alternatively, you can replace the Constants in the above sample with hardcoded values.

```csharp
// Initialize a new media at the root of the media archive
IMedia folder = _mediaService.CreateMedia("Samples Media Item Folder", -1, "Folder");

// Save the folder
var result = _mediaService.Save(folder);
```

For the `CreateMedia` method, the first parameter is the name of the folder to be created.

The second parameter is the ID of the parent media item. `Constants.System.Root` is a constant defined in Umbraco with the value of `-1`, which is used for indicating the root of the media archive. Instead of specifying the numeric ID of the parent, you may instead specify either a `Guid` ID or an `IMedia` instance representing the parent media.

The third parameter is the alias of the Media Type. As Umbraco comes with a Folder Type by default, we can use the `Constants.Conventions.MediaTypes.Folder` constant to specify that the alias of the Media Type is `Folder`.

In addition to the three mandatory parameters as shown above, you may also specify a numeric ID for a user to which the creation of the media should be attributed. If not specified, the media will be attributed to the user with ID `-1`, which corresponds to the built-in "Administrator" user.

### Creating a new media item from a stream

You can specify a `Stream` for the contents of the file that should be created.

As an example, if you have an image on disk named `unicorn.jpg` in the images folder of `wwwroot`. You can open a new stream for a file on the disk, and then create a new media item for that file in Umbraco:

Note that you will need to inject the following services:
* `MediaFileManager _mediaFileManager`
* `IShortStringHelper _shortStringHelper`
* `IContentTypeBaseServiceProvider _contentTypeBaseServiceProvider`
* `MediaUrlGeneratorCollection _mediaUrlGeneratorCollection`
* `IMediaService _mediaService`
* `IWebHostEnvironment _webHostEnvironment`

```csharp
string webRootPath = _webHostEnvironment.WebRootPath;
var path = Path.Combine(webRootPath, "images", "unicorn.jpg");

// Open a new stream to the file
using (Stream stream = System.IO.File.OpenRead(path))
{
    // Initialize a new image at the root of the media archive
    IMedia media = _mediaService.CreateMedia("Unicorn", Constants.System.Root, Constants.Conventions.MediaTypes.Image);
    // Set the property value (Umbraco will handle the underlying magic)
    media.SetValue(_mediaFileManager, _mediaUrlGeneratorCollection, _shortStringHelper, _contentTypeBaseServiceProvider, Constants.Conventions.Media.File, "unicorn.jpg", stream);

    // Save the media
    var result = _mediaService.Save(media);
}
}
```

Again Umbraco will make sure the necessary properties are updated.
