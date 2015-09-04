# $(Working with uno code)

While UX and JavaScript is the recommended way of developing apps with Fuse, advanced users can also dive into Uno-code if they want to do more advanced things, like custom rendering. While it doesn't have the live-reload features of JavaScript, and takes a bit of time to set up, it is compiled to native code, and is therefore a nice asset when you need fast custom elements in your UI.

Uno is closely related to C# - check out the [Uno Language Reference](https://www.fusetools.com/developers/guides/unolang) for more information.

## $(Adding uno-files to a project)

To add an uno file, simply type `fuse create uno <filename>`. This will automatically create an uno file with a template, and add it to the project. You can alternatively add an existing file manually by adding it as an entry to the `Includes` array in the project file. The entry should be in the format `"<filename>:SourceFile"`.

## AutoCtor and codebehinds

To add a codebehind to an UX file, simply set the `ux:Class` property on the `<App>` tag to some classname, and create an uno file with the same classname. Make sure the class is `partial`, as it will be a partial class together with the UX file, which is converted to uno compile-time.

If you want to have your own constructor, the first thing you need to do is turn off `AutoCtor` in the `<App>` tag to stop Fuse from creating one in the generated code. When that is done, you are free to create your own constructor. However, you need to call `InitializeUX()` in the constructor in order for the UX elements to be initialized.

The following is a simple example of a simple project that `debug_log`'s a string when the constructor is called:

### MainView.ux

```ux
<App ux:Class="MainView" ux:AutoCtor="false">
</App>
```

### MainView.uno

```uno
using Uno;

public partial class MainView
{
	public MainView() {
		debug_log("App init!");
		InitializeUX();
	}
}
```

## $(Compiling uno:Compiling)

Because the codebehind is compiled to C++ and can *not* be changed while the app is running, we don't support codebehinds in live preview. Instead, you have to build and run the app to see the results using `uno build`

Simply run `uno build --target=DotNetExe --run` if you are on windows, or `uno build --target=cmake --run` if you are on a mac.

## $(OSX compiling:Compiling on Mac OS X)
Mac users need a copy of CMake to build Uno code.
If you don't have CMake installed on your machine, you can find instructions for installing it [here](http://www.cmake.org/install/).

If you have [Homebrew](http://brew.sh/) installed, simply run `brew install cmake`.
