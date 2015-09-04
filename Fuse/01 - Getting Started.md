# $(Getting Started)

Getting up and running with Fuse is super easy. We'll just need to install a few things, then we're good to go!

## $(Setup)

We're always working hard on making this process smooth as silk, but occasionally things can still go wrong. If something happens, please let us know.

Setup instructions for OS X: https://www.fusetools.com/developers/guides/setup/install-osx

Uninstall instructions for OS X: https://gist.github.com/Tapped/daa78c08882f33b0c7c3

Setup instructions for Windows: https://www.fusetools.com/developers/guides/setup/install-win

## Tutorial

We suggest you start by [following our tutorial](https://www.fusetools.com/developers/guides/tutorial). You can also find a wide range of ready-to-run examples [on our Examples-page](https://www.fusetools.com/examples), as well as a (constantly growing) list of [tutorials on Youtube](https://www.youtube.com/playlist?list=PLdlqWm6b-XALJgM3fGa4q95Yipsgb8Q1o).

## $(Project structure)

After having created a new project, either by using the dashboard or the `fuse` command line command, you will find three files in the project directory:

- `ProjectName.unoproj` - This is the project file, and basically keeps track of which files compose the project, which packages it depends on and also other handy values like your api keys.
- `devices.json` - When previewing your code, Fuse needs to know which devices you would like to simulate. The `devices.json`-file contains definitions for quite a few devices you are likely to want to deploy your app to. If none of the shipped device definitions match what you intend to ship to, feel free to add your own here. This does not affect what real devices your project can run on.
- `MainView.ux` - This is the main starting point for your app, mainly because it contains the `App`-tag. Under normal circumstances you will delete most of the contents of this file, but feel free to examine the default application and see what is needed to make a bare bones surface with some controls.

Note: JavaScript do not need to be referenced from the `unoproj`-file. JavaScript files are referenced directly from UX.

> ### $(Project file structure)

The `unoproj` file has the following structure (incomplete):

Divide the solution into multiple projects and reference them from the `unoproj`-file:

```
"Projects" : [
	"path_to_other_project.unoproj"
]
```

__Note__: This section of the documentation is incomplete, a full description of the `unoproj` file is coming.



> ### $(Sublime Text projects)

When you drop a folder into Sublime Text 3, it will by default search through all files in all subfolders. When building a Fuse project, this isn't always what you want.

If you create a file called `ProjectName.sublime-project`, you can drop this into it to make it ignore the `.cache` and `.build` directories:

```
{
	"folders":
	[
		{
			"folder_exclude_patterns":
			[
				".build",
				".cache",
			],
			"path": "."
		}
	]
}
```

This file can then be opened from the `Project` -> `Open Project...`-dialog.

> ### Git

If you are using Git for version control, you can put the following in your .gitignore file.

	.build
	.cache

## Preview

Live preview is a key feature of Fuse. You can live preview simultaneously on multiple devices (and in the desktop simulator), so you no longer need to build your project for specific devices between edits; just save and they will appear instantly on all of your devices.

For instructions on how to enable iOS preview, go here: https://www.fusetools.com/developers/guides/previewandexport/devicepreview

For instructions on how to enable preview on an Android device, go here: https://www.fusetools.com/developers/guides/previewandexport/devicepreview

Starting a preview of your project on your desktop can be done in a couple of ways. If you have Sublime Text 3 installed with the supported plugin, you can right click on the UX-file and select "Begin Fuse preview" and select the "Local" option. Preview will then open the simulator, and it will sync automatically as you edit your files.

Alternatively, you can navigate on the command line to your project root directory and type `fuse preview`.

You can read more about starting Fuse preview on the desktop here: https://www.fusetools.com/developers/guides/previewandexport/toolpreview

## Export

When running preview, Fuse will create a shell application on your device that connects to the Fuse daemon running on your system to quickly display changes you make to the project. However, if you want to bring the device with you to a meeting or to show people away from your development environment, you will need to export the project to the device you are targeting.

> ### Exporting to iOS

On iOS, you need to have a machine running OS X and Xcode installed (as for preview). You also need an Apple developer account. In the project root, simply type:

`fuse build --target=iOS --run`

This will open the built project in Xcode. If you select your device from the pulldown list of available output targets and press the "run" button, the application will deploy to your device and run.

> ### Exporting to Android

To export to Android, you go to the project root and type:

`fuse build --target=Android --run`

This will deploy and start the project on your connected Android device.
