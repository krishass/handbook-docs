# $(Styling) and Resources

In Fuse, styling refers to setting properties such as fonts, colors, margins and appearances in a consistent way throughout your app.

## $(Class Resources)

You can introduce a _Class Resource_ at any point in your code:

	<Text ux:Class="Header" FontSize="22" />
	<Header>Welcome</Header>
	<Text>This document will change your life.</Text>
	<Header>Introduction</Header>
	<Text>Are we there yet?</Text>

In this case, the `Header`-class will be a distinctly different class from `Text`, although it inherits from it so all properties on `Text` will be available on `Header`. Note that the Class Resource can be defined in a separate file, and often is:

__Header.ux__:

	<!-- Setting 'ux:Class' is superfluous in this case,
		 as it is implicitly set on the root element in
		 a UX-file, but included here for clarity -->
	<Text ux:Class="Header FontSize="22" />

__MainView.ux__:

	<Header>Welcome</Header>

Class Resources can be as complex as you want:

	<Image ux:Class="BurgerIcon">
		<MultiDensityImageSource>
			<FileImageSource File="Burger.png" Density="1"/>
			<FileImageSource File="Burger.png@2x.png" Density="2"/>
		</MultiDenistyImageSoruce>
	</Image>

	<!-- ... -->

	<Panel>
		<BurgerIcon />
	</Panel>

Class Resources can also contain their own logic using @(Triggers:Trigger) and @(Actions).

## $(Style)

To add a `Style` to a `Node`:

	<StackPanel>
		<Style>
			<Text FontSize="32" />
		</Style>
		<Text>Biggie!</Text>
		<Panel>
			<Text>Another biggie!</Text>
		</Panel>
	</StackPanel>

The `Text` instances created at the level where the `Style` is introduced _or below_ will be affected by the `Style`. In this case, adding the `Style` will have the same appearance as adding the `FontSize` to all the `Text`-elements separately.

## Styling Class Resources

When introducing @(Class Resources) you create _new types_:

	<Text ux:Class="Header" />
	<Style>
		<Header FontSize="22" />
	</Style>
	<Header>Welcome</Header>
	<Text>This document will change your life.</Text>

In this case we've introduced a new _semantic class_ when we create `Header`, as the properties are the same as the default `Text` properties, but we've created another class that can be styled separately.

## $(Theme)

The `Theme` property of `App` decides whether your app is set up with *native UI controls* or *OpenGL ES powered graphics* as the root-level presentation technology, as well as the default look and feel of @(control).

For more information see @(Themes).

## $(Global resources)

Global resources are shared objects that are created once and used throughout your application. Global resources are recommended for objects that never change during the application lifetime, such as fonts, colors and sounds.

Global resources are created in UX markup by using the `ux:Global` attribute. `ux:Global` creates a global alias. This means you can refer to it by its `ResourceAlias`.

A resource alias is local to the type of the resource. If for example you have both a `float4` color resource named `Coral` and a `SolidColor` resource named `Coral`, Fuse will understand from context which is which.

This is how you import a TTF file and create a global resource from it:

	<Font File="HelveticaNeue-MediumCondensed.ttf" ux:Global="Helvetica" />

The above line can be placed in any file in your project, but you typically stuff these in your Style UX file or a special UX file dedicated to resources.

Once you've created a `ux:Global` alias, you can refer to your font by just the alias from anywhere in your code:

	<Text Font="Helvetica">Hello, yes, this is text.</Text>

## $(Resource)s and $(ux:Key)

To declare a Dynamic Resource, simply:

	<string ux:Key="WelcomeMessage" ux:Value="Hello!">

The resource will then be available to any nodes below it in the tree where it is defined. It can then be used as:

	<Text Value="{Resource WelcomeMessage}" />

### $(Localization)

Localization of strings, values, icons and other resources can be easily acheived with *resources*.

	<Match Value="{language}">
		<Case Value="English">
			<string ux:Key="Greeting" ux:Value="Hello, world!" />
		</Case>
		<Case Value="Norwegian">
			<string ux:Key="Greeting" ux:Value="Hei, verden!" />
		</Case>
	</Match>

Then, instead of specifying the greeting string directly throughout the app, we can use a resource binding instead:

	<Text Value="{Resource Greeting}" />


<!--## The anatomy of controls

TODO: Explain appearances, overlays, child/parent, how

## Tweaking existing styles

- InheritStyle = false ? -->
