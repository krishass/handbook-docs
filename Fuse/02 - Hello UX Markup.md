# Hello, $(UX Markup)!

Fuse can be used in two primary ways:

* @(Creating apps) or prototypes with cross-platform JavaScript logic
* Creating components, UI views and designs for native apps

The main representation of both these things in Fuse is through the use of UX Markup.

UX Markup is an XML-based format that should be immediately familiar to anyone who has
worked with similar formats. For an in-depth look at its quirks and detailed features, make
sure you read:

<!-- * TODO: Links to detailed UX specs -->


## $(Creating apps) : $(App)

When creating stand-alone apps or prototypes in Fuse, we use the `<App>` tag.

	<App Background="#436EEE">
		<Text>Hello, world!</Text>
	</App>

Inside your `<App>` tag you can put any `Node`, `Behavior` or `Theme` tag. The above example
simply displays text using the default font.

The `<App>` tag itself bootstraps the app and takes care of application lifecycle and @(Theme).

The $(Background) property controls the color of the root view of the app.


## UX tags

UX documents consists of XML tags. Each available UX tag corresponds to a *class* implemented in Uno code. Each tag corresponds to one (or multiple) runtime objects.

The available tags (classes) come in these categories:

* The @(App) class is the root of the app, and can contain exactly one Node
* Many `Node` types, most of which are @(Element:UI Elements)
* Behaviors which modify nodes. Behaviors come in many flavors:
  * Gestures
  * Triggers
  * Scripts
  * Visual @(effects) which can be applied to elements
* $(Styles:Styling) which allow consistent look and feel of components without repeating data

## $(Theme:Themes)

`App` offers a setting called `Theme`, which specifies how standard components in
the app will look and feel. You set it like this:

	<App Theme="..name of theme..">

If not specified, `App` uses a plain `GraphicsTheme` by default.

### $(NativeTheme)

When using `NativeTheme`, Fuse will use the native controls from the target platform. To use a native theme, do:

	<App Theme="Native">
		<StackPanel>
			<Button />
			<Switch />
			<Slider />
		</StackPanel>
	</App>

In this example, the displayed controls will have native appearance on iOS and Android, and *no appearance* in desktop preview.

> If you still want to make use of desktop preview while working with native controls, you can use the `NativeWithFallback` theme. This will give you native controls on iOS and Android, while falling back to a (unspecified) basic theme on desktop.

### $(GraphicsTheme)

By default, `App` uses a `GraphicsTheme`, which will give your app an identical look on all platforms, with the exception of:

* Status bars will behave differently across platforms
* `TextInput` is a higher-level control and will by default not be rendered since it relies on native platform controls. Either define your own style for this or use a @(BasicTheme) instead.

The main benefits of working with `GraphicsTheme` are:

* You can preview your app on Mac and PC using Fuse's realtime preview window,
  which offers a much smoother experience than the iOS and Android emulators.
* Your designs and animations will look and behave identically on all platforms.

Since `GraphicsTheme` is the default theme, you don't have to specify this explicitly,
but if you did, it would look like this:

	<App Theme="Graphics">

Or like this, which means exactly the same:

	<App>
		<GraphicsTheme />
	</App>

> #### Creating your own GraphicsTheme
<!-- TODO: Consider moving to Styling and resources chapter -->

It is possible to *extend* `GraphicsTheme` to specify specific
look and feel for controls like `Slider` and `Button`.

You can make your own `GraphicsTheme` by using it as a base class:

	<GraphicsTheme ux:Class="MyGraphicsTheme">
		<Button>
			<!-- how a button looks goes here -->
		</Button>
	</GraphicsTheme>

And use it like this:

	<App>
		<MyGraphicsTheme />
	</App>

You can also create a global alias for it, like this:

	<MyGraphicsTheme ux:Global="MyGraphics" />

And then this works:

	<App Theme="MyGraphics">
		...
	</App>

### $(BasicTheme)

The `BasicTheme` is a `GraphicsTheme` that ships with Fuse and gives controls a
look and feel inspired by material design. This can be useful when you want a starting
point for UIs that are supposed to look the same on all platforms.

	<App Theme="Basic">
