# UI Components

Fuse comes with a number of UI components that can be used to construct a user interface. In UX you can add UI components by enclosing them in tags:

    <Image File="MyImage.png" />
    <Rectangle Width="50" Height="50" Fill="#888" />
    <Text>Hello world!</Text>

Note that just because something is enclosed in a tag doesn't necessarily mean it has to be a UI component. UX uses tags for other concepts also, such as @(Trigger:Triggers) and @(Actions).

## $(Text)

Here is a tiny app that renders text:

	<App>
		<Text>Hello, world!</Text>
	</App>

This is great but when you have longer passages of text, such as a _Lorem Ipsum_, you most likely want to enable word wrapping. In Fuse, this is done with the `$(TextWrapping)` property on the `Text` control:

	<Text TextWrapping="Wrap">Lorem Ipsum(...)</Text>

If you are still finding that wrapping the text still makes it hard to show all the contents you want, you probably want to consider adding the contents to a @(ScrollView), or changing @(FontSize). `TextWrapping` can be set to `Wrap` or `NoWrap` (default).

### $(Fonts)

You can import fonts from ttf files containing TrueType fonts. Because a font is typically referred to throughout an application, it is best to simply create a _@(Global Resources:global resource)_ for it.

	<App>
		<Font File="Roboto-Medium.ttf" ux:Global="Medium" />
		<Font File="Roboto-Light.ttf" ux:Global="Light" />
		<StackPanel>
			<Text Font="Light">Roboto Light</Text>
			<Text Font="Medium">Roboto Medium</Text>
			<Text Font="Light">Roboto Light again</Text>
		</StackPanel>
	</App>

In this example, the fonts are located in the same directory as the relevant UX file. This way of importing the font ensures that the font is available throughout the whole project, and is only loaded once.

Both iOS and Android support text rendering with multibyte character sets. This means that emojis work fine rendering on device.

_Note!_ There are currently some issues rendering multibyte character sets using desktop preview. Do not be surprised if the desktop rendering doesn't match device rendering 100%. This is an issue that is being worked on.

### Text properties

For further control over how your text is rendered, you can set `$(TextAlignment)`, `$(TextColor)` and `$(FontSize)`:

	<Text TextColor="#999">Left</Text>
	<Text TextAlignment="Center">Center</Text>
	<Text FontSize="24" TextAlignment="Right">Right</Text>

In this example, the first text element will be left aligned as this is the default, and have its color set to a medium light grey. The second text will be center aligned. The third will be right aligned and have a larger font. Valid values for `TextAlignment` are `Left` (default), `Right` and `Center`.

> ### $(Number)

While @(Text) is the way to go in most cases, you may use `Number` to display a formatted numeric value.
The format, specified via the `Format` property, must be in the form of a [Standard Numeric Format String](https://msdn.microsoft.com/en-us/library/dwhawy9k.aspx).

The only format currently supported by `Number` is `F`, which truncates the number of decimal places to be shown.

The following code will render `3.141`:

```
<Number Value="3.14159265359" Format="F03" />
```

## $(Image)

To display an image:

	<App>
		<Image File="FuseLogo.png" />
	</App>

This code assumes the file `FuseLogo.png` lives in the same directory as the UX-file. If you would rather load the contents of the image from the Internet, simply:

	<Image Url="http://path_to_image" />

_Note!_ If you come from a background as a web developer you might be used to assigning a URL to a `src`-attribute. While `Image` has a `Source`-property, it is used to assign a `Resource` to an image. In this context, this `Resource` is a `HttpImageSource`, but that is created behind the covers for you automatically, so stick to the `Url`-property to load contents from the web.

> ### Image contents from resources

For a small example of other ways to load image data, here is a small example that also uses databinding from JavaScript:

	<FileImageSource ux:Key="pic2" File="Images/Image2.jpg" />
	<StackPanel>
		<JavaScript>
			module.exports = {
				imageResource: "pic2",
				url: "http://somewhereontheinternet/Cute-Cat.jpg"
			}
		</JavaScript>
		<Image File="Images/Image1.jpg" />
		<Image Source="{DataToResource imageResource}" />
		<Image Url="{url}" />
	</StackPanel>

This code will show three images stacked on top of each other. The topmost image will be fetched as a file that's part of the project. At the top we've referenced a file by creating a `FileImageSource` that we bind to an image using `DataToResource`. This will lookup the resource from the key it gets form the JavaScript. We also get the URL for a picture on the web and bind it to the `Url`-property of an `Image`. If this looks complicated, don't fret: We'll look more at @(Data Binding) and @(JavaScript) shortly.

> ### Image Color

It is possible to tint an `Image` by using the `Color`-property. Note, that this will affect the parts of the image that is closest to white in the most predictable way. For example:

	<Image File="WhiteIcon.png" Color="#f00" />

This will turn a white icon red.

### $(StretchMode)

When added to a container, an `Image` will by default try to show as much of itself as possible. If the image isn't the same aspect as its container, there will be parts of the container that will not be covered.

There are a number of ways to address this issue. You can set the `StretchMode`-property on your `Image` to make it behave differently. Here are the different modes:

- `Fill` - Fill the available area in the container without necessarily preserving the aspect ratio.
- `PixelPrecise` - Use the pixels from the image as unit to make sure the image is crisp on all devices. This means that the image will be different sizes on different devices. It ignores the size of the `Image` container.
- `PointPrecise` - This uses the size of the image source as the `Image` in points, which guarantees that it will be the same on all devices. It ignores the size of the `Image` container. For example, if the image is 64x64 pixels in size, the resulting control will be 64x64 @(points)
- `PointPrefer` - The `PointPrefer` stretch mode will prioritize getting image size correct using `PointPrecise` stretch mode, but in cases where `PixelPrecise` would create an on-screen image with approximately the right size, it will use `PixelPrecise` to increase the clarity of the image.
- `Scale9` - If you use `Scale9`, the `Image` will be streched according to its $(Scale9Margin). This margin will decide which pixels will be streched and by how much. The margin divides the image into 9 areas, where the corners will retain their original aspect and the rest of the areas will be stretched to accomodate the desired size of the image.
- `Uniform` - This will make the picture as large as possible while preserving aspect ratio. This will often make the `Image` not cover the whole parent.
- `UniformToFill` - Fill the parent container while preserving aspect ratio. This will often mean that parts of the picture are left out, clipped by the parent

### $(StretchDirection)

You can set which directions you want the image to scale by setting the `StretchDirection`-property:

- `Both` - Allow both up- and downscaling
- `UpOnly` - Only upscale contents
- `DownOnly` - Only downscale contents

### $(ContentAlignment)

You can set an alignment on Image which will make the image align within its rectangle on screen if it fills more or less of the available space in its rectangle. Which might be common when using @(StretchDirection) and @(StretchMode):

- `Default`
- `Left`
- `HorizontalCenter`
- `Right`
- `Top`
- `VerticalCenter`
- `Bottom`
- `TopLeft`
- `TopCenter`
- `TopRight`
- `CenterLeft`
- `Center`
- `CenterRight`
- `BottomLeft`
- `BottomCenter`
- `BottomRight`

### $(Image sources)

Image sources can be specified directly on @(Image) by using the `File` or `Url` attributes.
This however, reduces control over several aspects of the image's lifetime.

Using $(ImageSource) objects instead of specifying the source directly on the @(Image) attribute
allows you to name and reuse the image source as a resource throughout your app, without duplicating
paths and metadata everywhere.

The following example shows you how you can declare an image as a resource:

	<FileImageSource ux:Global="CloseIcon" File="close.png" Density="2" />

And then you can use `CloseIcon` anywhere in your project:

	<Image Source="CloseIcon" />

Fuse currently supports the following image source types:

* `FileImageSource` - specifies a single local image file and its density
* @(HttpImageSource) - specifies a single image from a URL and its density
* @(MultiDensityImageSource) - allows you to specify multiple versions of the same image for use with different screen densities.

In addition, the following classes allow you to configure image sources further:

* `MemoryPolicy` - controls how long the image data is kept in memory when no longer in use.

> ### $(MultiDensityImageSource)

Because devices have widely different pixel densities, Fuse allows you to specify multiple image resources for the same logical `Image`:

	<Image StretchMode="PointPrefer">
		<MultiDensityImageSource>
			<FileImageSource File="Icon.png" Density="1"/>
			<FileImageSource File="Icon.png@2x.png" Density="2"/>
		</MultiDensityImageSource>
	</Image>

Fuse will then pick the resource best suited for the screen, respecting the $(StretchMode) of the image.

<!--
  ### Memory policy

TODO: Explain @mortoray? -->

> ### $(HttpImageSource)

`HttpImageSource` allows you to specify an image to be fetched from a HTTP and be displayed asynchronously.

	<Image>
		<MultiDensityImageSource>
			<HttpImageSource Url="{image_url_1x}" Density="1"/>
			<HttpImageSource Url="{image_url_2x}" Density="2"/>
		</MultiDensityImageSource>
	</Image>

Or alternatively, if you want to use a fixed density image, you can use the shorter form directly:

	<Image Url="{image_url}" />

Note that images fetched from Http may take some time to load, and until loaded, Fuse knows nothing about the dimensions of the image. You should therefore be careful about basing the layout of your application on the dimensions of HTTP-fetched images.

## $(Video)

To display a video:

	<Video File="fuse_video.mp4" />

Video allows playback of video from file or stream through its properties `File` and `Url` respectively. Video is similar to @(Image), they share the properties @(StretchMode), @(StretchDirection) and @(ContentAlignment) and they work in the same way for both.

### $(Video properties)

Video come with a set of properties that can be used to configure it or control it. In addition to the properties shared with @(Image):
- `Volume`: range from `0.0` to `1.0`, default is `1.0`
- `Duration`: the duration of the video in seconds
- `Position`: the current position of the video in seconds
- `IsLooping`: a `bool` specifying if the video should loop or not, default is `false`

### $(Useful triggers that can be used with Video)

	<Video>
		<WhilePlaying />	<!-- Active while the video is playing -->
		<WhilePaused />		<!-- Active while the video is paused -->
		<WhileCompleted />	<!-- Active while the video is done playing -->
		<WhileLoading />	<!-- Active while the video is loading -->
		<WhileFailed />		<!-- Active if the video failed to load or an error occured -->
	</Video>

### Supported platforms

Video is currently only supported on iOS and Android. Support for video playback on other platforms is in our roadmap

### Supported formats

Video is implemented by using the videodecoder provided by the export target and therefore supports whatever the platform supports.
Be aware that Android and iOS might not share support for some formats
- [Android supported formats](http://developer.android.com/guide/appendix/media-formats.html)
- [iOS supported formats (found under 'public.movie')](https://developer.apple.com/library/mac/documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html)

> ### $(Video example)

	<App Theme="Basic">
		<DockPanel>
			<Video ux:Name="video" Dock="Fill" File="fuse_video.mp4" IsLooping="true" StretchMode="UniformToFill">
				<ProgressAnimation>
					<Change progressBar.Width="100" />
				</ProgressAnimation>
			</Video>
			<Rectangle ux:Name="progressBar" Dock="Bottom" Fill="#f00" Width="0%" Height="10" />
			<Grid Dock="Bottom" ColumnCount="2" RowCount="1">
				<Button Text="Play">
					<Clicked>
						<Resume Target="video" />
					</Clicked>
				</Button>
				<Button>
					<Clicked>
						<Pause Target="video" />
					</Clicked>
				</Button>
			</Grid>
		</DockPanel>
	</App>


## $(Shapes)

Fuse can render `Rectangle`s and `Circle`s. These shapes can have multiple @(Fill:Fills) and @(Stroke:Strokes). These will be layered on top of each other.

### $(Rectangle)

To draw a `Rectangle`:

	<Rectangle Fill="#f00" />

In this example, the `Rectangle` will take up as much space as it is allowed by its parent and fill it with a red @(SolidColor).

If you want to have the `Rectangle` limit itself in size, you can add `Width` and `Height`:

	<Rectangle Fill="#f00" Width="50" Height="50" CornerRadius="5" />

This will render a red `Rectangle` with rounded corners. Note that these @(Units:units) are in @(Points), not @(Pixels), and the `Rectangle` will appear to be roughly the same size on most devices, regardless of pixel density and screen size.

### $(Circle)

It is equally simple to draw a `Circle`:


	<Circle Fill="#f00" Width="50" Height="50">
		<Stroke Width="5" Brush="#ff0" />
	</Circle>

In this example, we've taken it a bit further, and we're adding a yellow @(Stroke).

#### $(StartAngle) / $(EndAngle)

`StartAngle` and `EndAngle` can be used to only draw a slice of a @(Circle).
There are 6 different properties that can be used to control this in different ways.

* `StartAngle` - The angle in radians where the slice begins
* `EndAngle` - The angle in radians where the slice ends
* $(StartAngleDegrees) - The angle in degrees where the slice begins
* $(EndAngleDegrees) - The angle in degrees where the slice ends
* $(LengthAngle) - An offset in radians from @(StartAngle). This can be used instead of @(EndAngle)
* $(LengthAngleDegrees) - An offset in degrees from @(StartAngle). This can be used instead of @(EndAngleDegrees)

Note that using for example both @(StartAngle) and @(StartAngleDegrees) on the same @(Circle) will have an undefined behavior.

### $(Fill:Fills)

We've seen that shapes accept simple `Fill` properties:

	<Rectangle Fill="#f00" />

It is possible to use other kinds of brushes to fill shapes. For example:


	<StackPanel>
		<Circle Width="150" Height="150">
			<ImageFill File="Pictures/Picture1.jpg" />
		</Circle>
		<Rectangle Height="150">
			<LinearGradient StartPoint="0,0" EndPoint="1,0.75">
				<GradientStop Offset="0" Color="#FC3C47" />
				<GradientStop Offset="1" Color="#B73070" />
			</LinearGradient>
		</Rectangle>
	</StackPanel>

Here, we created a `Circle` that has been filled with an `ImageFill`-brush, great for creating a typical profile picture in a social app. Then under it, we created a @(Rectangle) that has a nice and subtle `LinearGradient`.

### $(Stroke:Strokes)

Strokes accept a brush the same way a @(Fill) does:

	<StackPanel>
		<Circle Width="150" Height="150">
			<Stroke Width="10">
				<ImageFill File="Pictures/Picture1.jpg" />
			</Stroke>
		</Circle>
		<Rectangle Height="150">
			<Stroke Width="15">
				<LinearGradient StartPoint="0,0" EndPoint="1,0.75">
					<GradientStop Offset="0" Color="#FC3C47" />
					<GradientStop Offset="1" Color="#B73070" />
				</LinearGradient>
			</Stroke>
		</Rectangle>
	</StackPanel>

It can also be set to be a @(SolidColor:SolidColor-brush) using the `Brush`-property:

	<Rectangle Fill="#f00" Width="50" Height="50">
		<Stroke Width="5" Brush="#ff0" />
	</Rectangle>


#### $(StrokeAlignment)

The @(Stroke) can be aligned:

	<Stroke StrokeAlignment="Center" />

Valid values are `Center`, `Inside` and `Outside`.

#### $(Stroke.Offset)

The @(Stroke) of a @(Shapes:Shape) can be `Offset`:

	<Stroke Width="10" Offset="10">
		<ImageFill File="Pictures/Picture1.jpg" />
	</Stroke>

A positive `Offset` will make the `Stroke` appear outside the `Shape` while a negative `Offset` will make it appear inside.


## Brushes

Fuse comes with different brush types that can be used as @(Stroke) and @(Fill) in @(Shapes).

### $(SolidColor)

If you want to make a simple continuous color, you can use a `SolidColor`:

	<SolidColor Color="#00f" />

This will create a brush that can be assigned to any place that accepts a brush, for example a @(Rectangle):

	<Rectangle>
		<SolidColor Color="#00f" />
	</Rectangle>

Note that this is equivalent of writing:

	<Rectangle Fill="#00f" />

### $(ImageFill)

You can fill a @(Shapes:Shape) with an image using `ImageFill`:

	<Circle Width="160" Height="160">
		<ImageFill File="Portrait.png" />
	</Circle>

The same way @(Image) allows you to, `ImageFill` lets you set @(StretchMode).

### $(LinearGradient)

You can describe a `LinearGradient`-brush using `LinearGradient` and `GradientStop`. For example, to create a grey ramp that is white in the top and black in the bottom:

	<LinearGradient StartPoint="0,0" EndPoint="0,1">
		<GradientStop Offset="0" Color="#fff" />
		<GradientStop Offset="1" Color="#000" />
	</LinearGradient>

The `StartPoint` and `EndPoint` are both X and Y offsets within the @(Shapes:Shape) the brush is used in, so you can specify a diagonal brush by using `StartPoint="0,0" EndPoint="1,1"`.

## $(Button)

<!-- TODO: Remove DebugAction and or rename to <Debug Message=, this has a pull request, but the examples needs a search/replace DebugAction -> Debug -->

Buttons are clickable controls that take their look and feel from the @(Theme).

	<App Theme="Basic">
		<Button Text="Click me!" ux:Name="button1">
			<Clicked>
				<Set button1.Text="Clicked!" />
			</Clicked>
		</Button>
	</App>

This small example will create a `Button` that covers the whole screen. When you click it, its label will change from "Click me!" to "Clicked!".

In Fuse, pretty much anything can easily be made @(Clicked:clickable) (and @(Tapped:tappable), etc). Thus, if you want to design a custom look and feel for a button, any component can be used:

	<App>
		<Rectangle Fill="#309">
			<Clicked>
				<DebugAction Message="Rectangle got clicked" />
			</Clicked>
		</Rectangle>
	</App>

Depending on where you started the preview process from, you'll see the `Message` output when you click the `Rectangle`.


### $(Event triggers)

The `Button` can also accept `Clicked` as an _event-trigger_:

	<App Theme="Basic">
		<JavaScript>
			module.exports = {
				buttonClick: function (args) { console.log("Button was clicked"); }
			}
		</JavaScript>
		<Button Text="Click me!" Clicked="{buttonClick}" />
	</App>

This is handy when you want to drive business logic from events.

## $(Switch)

To accept on/off-style input, Fuse has a `Switch`-control:

	<Switch />

To make it react to being switched on, you can use the @(WhileTrue:WhileTrue-trigger):

	<App Theme="Basic">
		<StackPanel>
			<Switch>
				<WhileTrue>
					<Change rectangle.Width="160" Duration="0.5"
						Easing="CircularInOut" />
				</WhileTrue>
			</Switch>
			<Rectangle ux:Name="rectangle" Width="70" Height="70" Fill="#909" />
		</StackPanel>
	</App>

To make it act on the opposite state, you can use @(WhileFalse), or `WhileTrue Invert="true"`.

If you want the `Switch` to start out being activated:

	<Switch Value="true" />

The events emitted by the `Switch` can also be handled from JavaScript:

	<App Theme="Basic">
		<JavaScript>
			module.exports = {
				switchChanged: function (args) {
					console.log("Switch value is: " + args.value);
				}
			};
		</JavaScript>
		<StackPanel>
			<Switch Value="true" ValueChanged="{switchChanged}" />
		</StackPanel>
	</App>

> ### Databinding switch

You can also databind the `Switch` using its `Value`-property:

	<App Theme="Basic">
		<JavaScript>
			var Observable = require("FuseJS/Observable");

			var switchValue = Observable(false);

			module.exports = {
				switchValue: switchValue,
				enableSwitch: function () { switchValue.value = true; },
				disableSwitch: function () { switchValue.value = false; },
				switchChanged: function (args) {
					console.log("Switch value is: " + args.value);
				}
			};
		</JavaScript>
		<StackPanel>
			<Switch Value="{switchValue}" ValueChanged="{switchChanged}" />
			<Grid ColumnCount="2">
				<Button Text="Disable" Clicked="{disableSwitch}" />
				<Button Text="Enable" Clicked="{enableSwitch}" />
			</Grid>
		</StackPanel>
	</App>

## $(Slider)

To display a slider:

	<Slider />

To react to the slider being moved, use `ProgressAnimation`. Consider this code which allows you to `Rotate` a `Rectangle` from 0 to 90 degrees:

	<StackPanel>
		<Slider>
			<ProgressAnimation>
				<Rotate Target="rectangle" Degrees="90" />
			</ProgressAnimation>
		</Slider>
		<Rectangle ux:Name="rectangle" Width="100" Height="100" Fill="#808" />
	</StackPanel>

You can also listen to the `ValueChanged`-event:

	<App Theme="Basic">
		<JavaScript>
			module.exports = {
				sliderValueChanged: function (args)
				{
					console.log("Value: " + args.value);
				}
			};
		</JavaScript>
		<Slider ValueChanged="{sliderValueChanged}" />
	</App>

When moving the slider from the far left to the far right, your console will output floating point numbers from 0 to 100, which are the default `Minimum` and `Maximum` values. These properties can also be set:

	<Slider Minimum="4" Maximum="42" />

> ### Databinding slider

It is also possible to databind the slider position:

	<App Theme="Basic">
		<JavaScript>
			var Observable = require("FuseJS/Observable");

			var sliderValue = Observable(42);
			var sliderLabel = sliderValue.map(function (val) {
				return "Current position: " + val;
			});

			module.exports = {
				sliderValue: sliderValue,
				sliderLabel: sliderLabel
			};
		</JavaScript>
		<StackPanel>
			<Slider Value="{sliderValue}" />
			<Text TextAlignment="Center" Value="{sliderLabel}" />
		</StackPanel>
	</App>

## $(TextInput)

Fuse provides a `TextInput`-control to allow for user input of text:


	<JavaScript>
		var valueChanged = function (args) {
			console.log(args.value);
		}

		module.exports = {
			valueChanged: valueChanged
		};
	</JavaScript>

	<TextInput ValueChanged="{valueChanged}" />

You can also easily do two-way databinding:

	<App Theme="Basic">
		<JavaScript>
			var Observable = require("FuseJS/Observable");

			var name = Observable("");

			var greeting = name.map(function (name) {
				if (name == "") {
					return "Type your name above";
				} else {
					return "Hello there, " + name + "!";
				}
			});

			var clearName = function () {
				name.value = "";
			}

			module.exports = {
				name: name,
				greeting: greeting,
				clearName: clearName
			};
		</JavaScript>

		<StackPanel>
			<StatusBarBackground />
			<DockPanel>
				<Text Dock="Left" Alignment="VerticalCenter">Name:</Text>
				<TextInput Value="{name}" Alignment="VerticalCenter" />
			</DockPanel>
			<Text TextAlignment="Center" Value="{greeting}" />
			<Button Clicked="{clearName}" Text="Clear" />
		</StackPanel>
	</App>

If you want to accept a password, you might want to mask the user input:

	<TextInput IsPassword="true" />

If you want to accept numeric values mainly, you can set an `InputHint`:

	<TextInput InputHint="Number" />

Valid values for `InputHint` are `Default`, `Email`, `Number`, `Phone`, `Url`. These are called "hints" because they might not do anything, depending on which platform you are on. For example, when on the desktop, the keyboard will be the same no matter which hint is added to the `TextInput`.

`TextInput` also allows you to input contents over multiple lines instead of scrolling off to the right, which it does by default:

	<TextInput IsMultiline="true" />

You may also choose to disable editing altogether:

	<TextInput IsReadOnly="true" />

When a `TextInput` gets focus, it will often summon the device's on-screen keyboard. Fuse provides a number of mechanisms to react to this event, one of which is `WhileKeyboardVisible`:

	<Text Value="Instructions of some kind">
		<WhileKeyboardVisible>
			<Move Y="-1" RelativeTo="Keyboard" />
		</WhileKeyboardVisible>
	</Text>
	<TextInput />

As you can see, `WhileKeyboardVisible` can be attached to an arbitrary element, and you can do pretty much anything you want as a response to the on-screen keyboard taking up space on the screen.

The read-only `RenderValue` property gives you the string that is actually being drawn. For instance, if `IsPassword` is true, it will contain the masked password (i.e. `•••••••`).

> ### Styling TextInput

The `CaretColor` property allows you to change the color of the caret:

	<TextInput CaretColor="#ff0000" />

If you want to change the color of the selection, `SelectionColor` enables you to do just that:

	<TextInput SelectionColor="#00ffaa" />

<!--
- WhileFocused TODO: I am not sure what exactly this is supposed to demonstrate
- WhileEmpty TODO: This doesn't exist, should it? It is good for implementing placeholder data
- link to styling?
- text edit TODO: What is this?

TODO: Consider not documenting these here but in the chapter on Triggers and Animators -->

## $(ScrollView)

Fuse has a `ScrollView` that can be used to navigate contents that are larger than the available size.

	<ScrollView>
		<Panel Width="2000" Height="2000" />
	</ScrollView>

To limit the behavior of a `ScrollView`, you can set the ScrollDirection:

	<ScrollView AllowedScrollDirections="Horizontal">
		<!-- Contents -->
	</ScrollView>

Valid settings for `AllowedScrollDirections` include `Horizontal`, `Both` and `Vertical` (default). <!--TODO: Check that this is true. There are also a bunch of AllowedScrollDirections in the enum I believe are not used -->

> ### $(ScrollingAnimation)

It is possible to animate properties based on absolute `ScrollView` position. For example, let's remove a top ledge as a `ScrollView` scrolls down:
<!-- TODO: Link to triggers and animation -->

	<App Theme="Basic" Background="#fff">
		<Panel>
			<Panel Alignment="Top" Height="50" ux:Name="ledge">
				<Text Alignment="Center" TextAlignment="Center" TextColor="#fff" Value="TopLedge" />
				<Rectangle  Fill="#000" />
			</Panel>
			<ScrollView>
				<ScrollingAnimation From="0" To="50">
					<Change ledge.Opacity="0" />
				</ScrollingAnimation>
				<StackPanel>
					<!-- Block out the top ledge in the scrollview -->
					<Panel Height="50" />
					<!-- ... Content ... -->
				</StackPanel>
			</ScrollView>
		</Panel>
	</App>

## $(NativeViewHost)
Some views are only available as native components, so how can we avoid the limitation of having to use the Native theme and combine these native views with our custom Fuse views? By compositing them with the NativeViewHost! As an example, here is a @(WebView) added with a NativeViewHost:

```
<App Theme="Basic">
	<Panel>
		<NativeViewHost>
			<WebView Url="http://interwebs.com" />
		</NativeViewHost>
	</Panel>
</App>
```

Note that views added in a NativeViewHost are always rendered _in front_ of your other Graphics theme content, so your use of depth and render order will need to take this into account.

To achieve the opposite -Adding Fuse views in a Native theme- read on.

## $(GraphicsView)
The `GraphicsView` is the counterpart to the @(NativeViewHost) and allows you to add Fuse views to an @(App) using the Native theme.

```
<App Theme="Native">
	<StackPanel>
		<Button Text="I'm a Native button!" />
		<GraphicsView>
			<Button Text="I'm a Fuse button!" />
		</GraphicsView>
	</StackPanel>
</App>
```

As with the @(NativeViewHost) note that depth ordering will behave differently when mixing Native and Fuse views.


## $(WebView)

To include web content Fuse offers a native WebView component for Android and iOS. The WebView is native only, and as such needs to be contained in a @(NativeViewHost) if you wish to use it with Graphics themes.

The WebView can be used to present web content over the http protocol, and hooks into some useful triggers for building a customized browsing experience, such as @(PageBeginLoading), @(WhilePageLoading) and @(PageLoaded). Navigation triggers like @(GoBack) and @(GoForward) are complemented with WebView-specific ones, like @(Reload) and @(LoadUrl). It can also be used to feed a @(ProgressAnimation).

Of particular notice is the @(EvaluateJS) trigger, which allows arbitrary JavaScript to be run in the WebView's context and the resulting data be fed back into Fuse:

```
<App Theme="Native" Background="#333">
	<JavaScript>
			module.exports = {
				onPageLoaded : function(res) {
					console.log("WebView arrived at "+ JSON.parse(res.json).url);
			}
		};
	</JavaScript>
	<DockPanel>
		<StatusBarBackground Dock="Top"/>

		<WebView Dock="Fill" Url="http://www.google.com">
			<PageLoaded>
				<EvaluateJS Handler="{onPageLoaded}">
					var result = {
						url : document.location.href
					};
					return result;
				</EvaluateJS>
			</PageLoaded>
		</WebView>

		<BottomBarBackground Dock="Bottom" />
	</DockPanel>
</App>
```


## $(Element)

Here are some properties that are common for all `Element` types:

### $(HitTestMode)

When interacting with an element, it is sometimes desirable to be able to differentiate which elements can be interacted with and how. This is typically referred to as "hit testing". In Fuse, how elements interact with user input can be set using `HitTestMode`.

Consider this code:

	<Grid ColumnCount="2">
		<Rectangle Width="100" Height="100" Fill="#808" >
			<Clicked>
				<DebugAction Message="Clicked left" />
			</Clicked>
		</Rectangle>
		<Rectangle Width="100" Height="100" Fill="#808" HitTestMode="None" >
			<Clicked>
				<DebugAction Message="Clicked right" />
			</Clicked>
		</Rectangle>
	</Grid>

It will layout two `Rectangle`s and add `Clicked`-triggers to both of them. However, only the left one will output anything when clicked, as the hit testing has been explicitly disabled on the right rectangle. This can be very helpful if you have visual elements obscuring elements below it, where you want the lower elements respond to input.

Valid values for `HitTestMode` are:

- $(HitTestMode.None:None) - This element will not do hit testing
- $(HitTestMode.LocalBounds:LocalBounds) - This element will be hit tested based on its size
- $(HitTestMode.LocalVisual:LocalVisual) - This element will be hit tested based on its appearance
- $(HitTestMode.LocalBoundsAndChildren:LocalBoundsAndChildren) - Hit testing will include the bounds of the element and its children
- $(HitTestMode.LocalVisualAndChildren:LocalVisualAndChildren) - Hit testing will include the appearance of the element and its children

Note that if you set the @(Opacity) of an element below or equal its `HitTestOpacityThreshold` (which defaults to being 0), hit testing will be disabled for that object. This means that you can click an element as you fade it out, but it will stop accepting clicks at a certain point.

### $(ClipToBounds)

Normally, when laying out an element inside the other, the inner element can freely live outside the parent element:

	<Panel Width="100" Height="100">
			<Image Margin="-100" File="Pictures/Picture1.jpg"
				StretchMode="UniformToFill" />
	</Panel>

This `Image` will appear to be 300pt wide and tall, as the `Panel` doesn't clip children to its bounds.

If you intend to have the `Image` clip to its parent size, simply add $(ClipToBounds) to the `Panel`:

		<Panel Width="100" Height="100" ClipToBounds="true">
			<Image Margin="-100" File="Pictures/Picture1.jpg"
				StretchMode="UniformToFill" />
		</Panel>

Now, the `Image` will not overflow the bounds of the `Panel`.

> ### $(Opacity)

You can set the transparency of objects using the `Opacity`-property.

	<Panel>
		<Opacity Value="0.5" />
	</Panel>

When the `Opacity` is set to 0.0, the element is fully transparent and will no longer respond to @(HitTestMode:HitTests). When the `Opacity` is set to 1.0, the element will be at its default state.

> ### Layers

It is often helpful to redefine what existing controls should look like. Elements that are added to containers can be assigned to different layers. If you want a button to appear with a red background, you can redefine its `Background` `Layer`:

	<Button Text="Hello!">
		<Rectangle Fill="#931" Layer="Background" />
	</Button>

This will not change the layout or behavior of the `Button`, but its appearance will change.

Valid values for `Layer` are:

- `Background`
- `Layout`
- `Overlay`

## $(Effects)

Fuse has the ability to render a set of visual effects that can be added to most controls. It is important to understand that in order for these to work, you need to be in graphics mode; native themes are limited in their ability to render these effects.

### $(DropShadow)

To add a `DropShadow` to an element:

	<Rectangle Width="50" Height="50" Fill="#808">
		<DropShadow />
	</Rectangle>

To make a soft `DropShadow` from the top down:

	<Rectangle Width="50" Height="50" Fill="#808">
		<DropShadow Angle="90" Distance="12" Size="20" Spread="0.1"  />
	</Rectangle>

It can also be used to create artistic effects like outer glow:

	<Panel Background="#000">
		<Circle Width="50" Height="50" Fill="#808">
			<DropShadow Distance="0" Size="50" Spread="0.2" Color="#ff06" />
		</Circle>
	</Panel>

`DropShadow` has these properties:

- `Angle` - Which direction the light comes from:
	- 0 - right
	- 90 - top
	- 180 - left
	- 270 - bottom
- `Distance` - The distance in points from the source of the shadow
- `Size` - The size of the dropshadow
- `Spread` - How the shadow drops off. The closer to 0, the more linear. Keep this value low (experiment below 1.0), or you will get artifacts
- `Color` - Which color the dropshadow should have. Note that this also supports alpha channel for adjusting shadow transparency (declare with using a fourth digit for your hex color values, such as #FFF0 to #FFFF).

### $(Blur)

To blur an element:

	<Text Value="Hello there!">
		<Blur Radius="0.9" />
	</Text>

Note that while the `Radius` of the `Blur` can be animated like most other properties, this is potentially an expensive operation, and should be tested on devices to make sure it behaves properly.

<!-- The original author was concerned about there being parameters in the doc he couldn't speak of with confidence. There are no such parameters  -->

### $(Desaturate)

To `Desaturate` an element, fully or partially:

	<Image File="Pictures/Picture1.jpg">
		<Desaturate Amount="0.4" />
	</Image>

An amount of 1.0 will fully `Desaturate` the element.

### $(Halftone)

Add a classic halftone effect:

	<Image File="Pictures/Picture1.jpg">
		<Halftone />
	</Image>

`Halftone` accepts:

- `Spacing` - The distance between the dots (`float`, default 0.5)
- `Smoothness` - How hard or soft the dot edges are (`float`, default 2)
- `Intensity` - How much of the effect is applied (`float`, default 1)
- `DotTint` - Tint amount of the dots (`float`, default 0.5)
- `PaperTint` - Tint amount of the paper (`float`, default 0.2)

### $(Mask)

Fuse allows you to mask an element with an image or `ImageSource`.

	<Rectangle Width="50" Height="50">
		<Mask File="Masks/Flower.png" />
	</Rectangle>

The `Mask` effects accepts the following properties:

- `Mode` - How to interpret the mask source
	- `RGBA` (default) - Use the alpha channel of the source image as the mask and multiply the RGB values from the mask with the RGB values from the element to be masked
	- `Alpha` - Use the alpha channel of the source image without touching the RGB values from the masked element
	- `Greyscale` - Use the color component of the greyscale mask as a multiplication factor with the original alpha value
- `File` - If you have a picture bundled as part of the project
- `Source` - If you have another source for the `Image`, for instance a @(HttpImageSource)

If you use a white image with alpha channel, `RGBA` and `Alpha` will have the same result.

The mask will always stretch itself to match the size of the element to be masked.

### About $(Control:Controls)

In Fuse, we use the word _control_ to describe a UI component that has a semantic function, but could vary largely in visual appearance.

For example, @(Button) defines certain properties and events, such as @(Clicked), but what a button looks and feels like may be very different between different @(Theme:themes) or @(Style:styles).
