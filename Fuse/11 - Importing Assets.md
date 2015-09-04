# Importing Assets

Fuse is working hard to make it easy to $(import) assets and designs from great third party design tools into Fuse. This section contains information on how to use them.

## From $(Sketch) (experimental)

Sketch is a great vector design tool for Mac. If you haven't tried it, <a href="https://www.sketchapp.com">check it out</a>!.

Fuse can:

* Import assets from `.sketch` files, into sliced images, fonts and ready-to-use UX tags.
* Live update the assets if you change the sketch file after initial import.


> *Note*: Importing `.sketch` files only works on Mac OSX (like Sketch itself), and is currently experimental.

> ### Best practices

When working on `.sketch` files for use in Fuse, keep the following in mind:


* Fuse tag names will correspond to the layer names in Sketch, so keep your layer names meaningful
* Use artboard sizes that match the target device. For example, if designing for iPhone 6, use the iPhone 6 artboard template in Sketch. This makes sure assets become the right size when exported
* Artboards in Sketch can (but doesn't have to) correspond to pages in Fuse


### Importing

To use the sketch importer, you first need to install SketchTool by Bohemian Coding. You can <a href="https://bohemiancoding.com/sketch/tool/">download it here</a>.
Extract the zip and run the install script.

To import a sketch document, say `MyDesign.sketch`, first copy the file into your fuse project folder.

Then using a terminal type:

	fuse import MyDesign.sketch

Import may take a while, depending on the amount of assets in your document.

> ### Using drag-and-drop to import

As an alternative to using the terminal, you can drag-and-drop your `.sketch` file into a Fuse preview window. This will copy the sketch file to your project's root folder (if it isn't there already) and then do the same thing. Any existing `.sketch` file with the same name will be overwritten.

### Resource library ($(.sketch.ux))

When import is done - if all went well - Fuse has generated a file called `MyDesign.sketch.ux` next to your `.sketch` file. Open it in your text editor to see what's inside.

This file is a resource library which contains all the assets Fuse was able to extract from your Sketch document. It is automatically included in your project, and you can start using the resources.

By itself, the resource library does nothing and does not add to your exported app size. Only the assets you reference from other UX files will be included in your final app, the rest will be stripped away.


### Images

Images from the sketch document will be represented as classes (`ux:Class`) which extend @(Image), and point to the rendered assets with a @(MultiDensityImageSource).

The names of the classes correspond to the layer names in Sketch. If your file was called `MyDesign.sketch`, and it contains an artboard called `Screen1`, which contains a layer group called `SomeGroup` and a layer called `SomeLayer`, you can create in instance of this image like this:

	<MyDesign.Screen1.SomeGroup.SomeLayer />

This tag is an @(Image), so it supports the same attributes as @(Image), e.g. @(Alignment), @(Margin), @(StretchMode) and @(Scale9Margin).

This is all possible because in the resource library file (`.sketch.ux`), you'll now have a generated class that looks something like:

	<Image ux:Class="MyDesign.Screen1.SomeGroup.SomeLayer">
	  <MultiDensityImageSource>
	   <FileImageSource File="MyDesign.sketch-assets/MyDesign.Screen1.SomeGroup.SomeLayer@1x.png" Density="1.0" />
	   <FileImageSource File="MyDesign.sketch-assets/MyDesign.Screen1.SomeGroup.SomeLayer@2x.png" Density="2.0" />
	  </MultiDensityImageSource>
	</Image>

The image files is placed in the `MyDesign.sketch-assets/` folder next to the `.sketch` file.

### Fonts

Fuse will automatically extract the fonts needed from your system and put them in the `-assets/` folder next to your `.sketch` file. It will also declare them as global resources in your resource library.

In the resource library they will look like this:

	<Font File="MyDesign.sketch-assets/HelveticaNeue.ttf" ux:Global="HelveticaNeue" />

Which means you can use them like this:

	<Text Font="HelveticaNeue">Hello, world!</Text>



### Auto-generated app layout

Fuse can also generate a app layout based on the imported resource library, so you get proper Fuse screens that look exactly like your sketch design. This includes @Text elements in addition to shapes and images, and can be a good starting point for creating a responsive app layout.

You do this by specifying the `--app` or (`--overwrite-app`) options to `fuse import`. Example:

	fuse import --app MyDesign.ux MyDesign.sketch

This will generate `MyDesign.ux` which will contain an @App tag, with a @PageControl, and one @Page for each *artboard* in your sketch file.

Fuse will as accurately as possible reproduce the layout from your sketch file. This currently means absolute positioning all elements to match the sketch file precisely.

> Fuse is working on tools for optimizing the UX code to auto-detect layout rules, etc. Check back for updates!

> ### $(sketch-update:Live update of assets)

If you run `fuse import` again after original import, the resource library will be updated (overwritten) with the latest assets, and any assets in use in the project will automatically update on in all preview sessions.

The names of the classes remain the same, as long as Sketch layers have not been renamed.

> ### Image densities

You can control the image densities Fuse generates when importing the Sketch document by passing options to the import command. For example:

	fuse import --1x --1.5x --2x MyDesign.sketch

Will import all image assets in 1.0, 1.5 and 2.0 density, respectively. If you don't specify anything, Fuse defaults to rendering 1.0 and 2.0 densities.

#### Asset aliasing

Sometimes when you import `.sketch` files you may get a lot of long, impractical names for your assets. This is simply because noone considered that the document would be imported to Fuse when it was made and thus didn't name the layers properly.

You would typically get things like :

	<Image ux:Class="activity.Activity___6.Rectangle_91___Bitmap_2">
		<MultiDensityImageSource>
			<FileImageSource File="activity.sketch-assets/Activity___6.Rectangle_91___Bitmap_2@1x.png" Density="1" />
			<FileImageSource File="activity.sketch-assets/Activity___6.Rectangle_91___Bitmap_2@2x.png" Density="2" />
		</MultiDensityImageSource>
	</Image>

This will make your UX code look not-so-nice when you start using these. At the same time, we don't want to rename the classes, because then we lose the link to the `.sketch` file and can no longer get @(sketch-update:live updates).

To deal with this, make yourself an extra UX file, for example called `AssetAliases.ux`. You can for instance use a `Panel` as the root tag.

In the above example we know that this bitmap is actually the background for the entire screen. We can therefore give it a more reasonable name, like `ActivityBackground`.

In `AssetAliases.ux` do:

	<Panel>
		<activity.Activity___6.Rectangle_91___Bitmap_2 ux:Class="ActivityBackground" />
	</Panel>

All other assets you want to alias can just be added to this list.

Then you can use this much nicer tag in your actual app code:

	<ActivityBackground />
