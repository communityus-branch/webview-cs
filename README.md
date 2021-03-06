# webview-cs

[![Build status](https://ci.appveyor.com/api/projects/status/4f4843txvwxaays3/branch/master?svg=true)](https://ci.appveyor.com/project/iwillspeak/webview-cs/branch/master)

C# Bindings to https://github.com/zserge/webview. Easily create cross-platform desktop applications with web technologies.

## Installation

You can get your hands on Webview.Core from NuGet:

    PM> Install-Package Webview.Core

or from the dotnet command line:

    $ dotnet add package Webview.Core

## Examples

There are two main APIs to create a webview; the simple API, and the builder API. With the simple API all interaction takes place via the `Webview.Webview.Simple` method. This allows you to quickly get a webview running, but doesn't provide the ability to register an invoke callback for JS amongst other things. This is a good starting place.

```cs
using Webview

Webview.Webview.Simple("Window Title", "https://google.com"))
```

You can also provide an initial window size and control if the window can be resized:

```cs
using Webview;
using System.Drawing;

Webview.Webview.Simple(
  "Title",
  Content.FromHtml("<html><h1>Hello World!"),
  size: new Size(500, 200),
  resizable: true
)
```

For the builder API you first need to create a `WebviewBuilder`. A fluent API allows you to choose how the `Webview` is created. Once built the resulting view must manually be `Run` to display the window.

```cs
using Webview;
using System.Drawing;

new WebviewBuilder(new Uri("http://google.com"))
    .WithSize(new Size(1024, 768))
    .Resizable()
    .Debug()
    .WithInvokeCallback((webview, action) => {
      Console.WriteLine("Action: {0}", action);
    })
    .Build()
    .Run();
```

If you're program is going to outlive the `Webview` you can wrap it in a `using` statement to make sure unmanaged resources are disposed of when you expect them to be:

```cs
using Webview;

using (var webview = new WebviewBuider("Title", Content.FromHtml("<p>Hello World")
                        .WithCallback(MyCallbackFunction))
                        .Build())
{
    webview.Run();
}
```

### ASP .NET Core WebHost

To create a standalone desktop application out of an ASP.NET Core website, use the Webview.WebHost nuget package.

    PM> Install-Package Webview.WebHost

or from the dotnet command line:

    $ dotnet add package Webview.WebHost


Replace the default Main function in Program.cs with this:

```cs
using Webview.WebHost;
...

    public static void Main(string[] args)
        {
            CreateWebHostBuilder(args).WithDynamicPort().WithNoOutput().Build().RunWebview();
        }

```

To create the standalone executable, the project must be published.  

    $ dotnet publish -r win10-x64 -c Release

Supported runtime identifiers are 'win10-x64', 'win10-x64', 'osx-x64', and 'linux-x64'.

## Windows Console Gotchas

*Note* - Windows has the concept of console and windows applications, but .NET Core only understands console applications.  To make the resulting .NET Core console application behave as a windows application (detatches console), you can use the `editbin` utility. The easiest way to get `editbin` is to install Visual Studio Community and include the C++ tools.

From a Visual Studio Developer command prompt:

    > editbin /subsystem:windows app.exe

This not an issue under linux and osx.

## Adding an Application Icon on Windows

To add an icon to your Windows application create a .NET Framework executable instead of a .NET Core one and attach an icon to that. See [this issue](https://github.com/webview-cs/webview-cs/issues/17) for more information.

## Feature Status

 * [x] Run webview with standard parameters.
 * [x] Builder API for creating webviews.
 * [x] Native binaries for:
  * Linux x64
  * macOS x66
  * Windows10 x64
 * [x] WebHostBuilder and WebHost extensions for running a .NET Core website as a desktop application
