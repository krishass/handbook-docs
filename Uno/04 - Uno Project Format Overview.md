# $(Uno Project Format Overview)

The Uno Project Format (`.unoproj`) describes what files and packages to include in a project, as well as settings for how the 
app should be built when exported.

Uno Project files can be edited through the Fuse desktop tools, by the `fuse` command line tool, or by editing the project file by hand
in a text editor.

## Structure

A default Uno Project file template looks like this:

    {
      "BuildDirectory": ".build",
      "CacheDirectory": ".cache",
      "RootNamespace": "$(QIdentifier)",
      "ReferenceUnoCore": true,
      "Version": "0.0.0",
      "VersionCount": 1,
      "Title": "$(Name)",
      "Copyright": "Copyright (C) 2015",
      "Publisher": "[Publisher]",
      "Mobile": {
        "KeepAlive": false,
        "ShowStatusbar": true,
        "RunsInBackground": true,
        "Orientations": "Auto"
      },
      "Android": {
        "ApplicationLabel": "$(Title)",
        "VersionCode": "$(VersionCount)",
        "VersionName": "$(Version)",
        "Description": "$(Description)",
        "Icons": {
          "LDPI": "$(Icon)",
          "MDPI": "$(Icon)",
          "HDPI": "$(Icon)",
          "XHDPI": "$(Icon)",
          "XXHDPI": "$(Icon)",
          "XXXHDPI": "$(Icon)"
        }
      },
      "iOS": {
        "Icons": {
          "iPhone_29_2x": "$(Icon)",
          "iPhone_29_3x": "$(Icon)",
          "iPhone_40_2x": "$(Icon)",
          "iPhone_40_3x": "$(Icon)",
          "iPhone_60_2x": "$(Icon)",
          "iPhone_60_3x": "$(Icon)",
          "iPad_29_1x": "$(Icon)",
          "iPad_29_2x": "$(Icon)",
          "iPad_40_1x": "$(Icon)",
          "iPad_40_2x": "$(Icon)",
          "iPad_76_1x": "$(Icon)",
          "iPad_76_2x": "$(Icon)"
        }
      },
      "HTML": {
        "Title": "$(Title)",
        "Favicon": "$(Icon)"
      },
      "InternalsVisibleTo": [],
      "Packages": [
        "Fuse.BasicTheme",
        "Fuse.Controls",
        "Fuse.Designer",
        "Fuse.Drawing",
        "Fuse.Effects",
        "Fuse.Elements",
        "Fuse.Gestures",
        "Fuse.Native",
        "Fuse.Navigation",
        "Fuse.Reactive",
        "Fuse.Scripting",
        "Fuse.Shapes",
        "Fuse.Triggers",
        "FuseCore"
      ],
      "Projects": [],
      "Includes": [
        "*"
      ],
      "Excludes": []
    }

## Globs
 
These glob features are supported in `Includes` and `Excludes` properties:

- Brace expansion
- Extended glob matching
- Globstar matching

Patterns without `/` in `.unoproj` or `.unoignore` files are matched recursively.

    *             # Matches all files.
    *.foo         # Mathces all files named *.foo.
    *.bar         # Matches all files named *.bar.
    *.+(bar|foo)  # Matches all files named *.bar or *.foo .
    foobar        # Matches all files named foobar.

Add `/` or `./` to disable recursion.

    /*.png        # Not recursive, matches all PNG files found directly in project directory.

Use globstar (`**`) for explicit recursion.

    Foo/**/*.uno  # Matches all files named *.uno in Foo directory recursively.

## $(Icons)

To set the default icon, use the `Icon` property (not the `Includes` property):

```
"Icon": "icon-default.png",
"Includes": [
	...
]
```

If you explicitly want to add specific resolutions, you can override specialized properties:

```
"Android": {
  "Icons": {
    "LDPI": "icon-ldpi.png",
    "MDPI": "icon-mdpi.png",
    "HDPI": "icon-hdpi.png",
   }
}
```

## $(Android signing:Signing for Android)

A Uno Project set up for signing can look like this:

    "Android": {
      "Key": {
        "Alias": "application",
        "AliasPassword": "android",
        "Store": "release.keystore",
        "StorePassword": "android"
      }
    }

A file named `release.keystore` is expected to be found in the same folder as the project.

This file can be created using the command:

    keytool -genkey -v -keystore release.keystore \
        -alias application -keyalg RSA -keysize 2048 -validity 10000

Note that only release builds are signed using the specified key. Debug builds are automatically signed using a debug key.
(`keytool` is found in Android SDK.)
