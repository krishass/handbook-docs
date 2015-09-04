# APIs

<!-- ## EcmaScript features

TODO for the below: write some basic inline docs & examples, then point to the MDN docs for more info

> * : May have limited functionality compared to browser implementations -->

## Polyfills

FuseJS executes in a (minimum) EcmaScript 5.1 environment on all supported platforms.
There is no web browser involved, FuseJS only provides a subset of the browser's standard libraries. FuseJS provides polyfills for some features (typically browser features) that are mainly provided to make $(third party libraries) work. These implementations are not neccessarily complete at this point, but check the @(FuseJS Roadmap) to see what the status is for development.

## $(fetch)

This is the main way to do HTTP requests.

This is an example on how to take a JavaScript object, POST it as JSON and receive JSON data which is turned back into a JavaScript object using `fetch`:

	var status = 0;
	var response_ok = false;

	fetch('http://example.com', {
		method: 'POST',
		headers: { "Content-type": "application/json"},
		body: JSON.stringify(requestObject)
	}).then(function(response) {
		status = response.status;  // Get the HTTP status code
		response_ok = response.ok; // Is response.status in the 200-range?
		return response.json();    // This returns a promise
	}).then(function(responseObject) {
		// Do something with the result
    }).catch(function(err) {
    	// An error occured parsing Json
	});

* Complete documentation for [`fetch`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)


> ### $(XMLHttpRequest)

FuseJS supports `XMLHttpRequest`, go here for more information:

* https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest for more info


> ### $(Promise)

FuseJS has support for promises, go here for more information:

* [`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)

<!-- TODO: Cleanup the difference between polyfill and API -->

> ### $(setTimeout)

To call a function in 2 seconds:

	setTimeout(function() { alert("Alert"); }, 2000);

You can also use `setTimeout` to create loops:

	function poller() {
		// Do work (...)

		// And again in 1 second
		setTimeout(poller, 1000);
	}

* [`setTimeout`](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers/setTimeout)

## Storage

The storage API allows you to save text to files in the application directory.

	var storage = require('FuseJS/Storage');

### $(storage.write:write)

Write a string value to specified file.

	storage.write(filename, value)

* `filename` - Desired name of the file
* `value` - The content that will be written to the file

Returns a `Promise` with a `bool` which tells if the content was successfully written.

	storage.write("filename.txt", "filecontent");

### $(Storage.read:read)

	storage.read(filename)

* `filename` - The file that should be read

Returns a `Promise` with a `string` containing the content of the file.

	storage.read("filename.txt").then(function(content) {
		console.log(content);
	}, function(error) {
		console.log(error);
	});


The actual folder where the file will be saved will vary depending on platform.

### `writeSync`

Synchrounously write data to the application folder, returning `true` on a successful write:

	var wasWritten = storage.writeSync("filename.txt", "filecontent");

> Warning: This call will block, if you are writing large amounts of data, use @(Storage.write).

### `readSync`

Synchronously read data from a file in the application folder:

	var data = storage.readSync("filename.txt");

> Warning: This call will block, if you are writing large amounts of data, use @(Storage.read).

### `deleteSync`

Delete a file from the application folder, returning `true` on a success:

	var wasDeleted = storage.deleteSync("filename.txt");

## $(Lifecycle)

You can react to lifecycle events using JavaScript:

	var lifecycle = require('FuseJS/Lifecycle');
	lifecycle.onEnteringForeground = function() {
  		initialize();
	};

The following lifecycle events will be raised:

The app start event is implicit as this is when your js code is first evaluated.

- $(onEnteringForeground) - The app has left the suspended state and now is running. You will get this event when the app starts.
- $(onEnteringBackground) - The app is leaving the running state and is about to be suspended.
- $(onEnteringInteractive) - The app is entering a state where it is fully focused and receiving events.
- $(onExitedInteractive) - The app is partially obscured or is no longer the focus (e.g. when you drag open the notification bar)
- $(onTerminating) - The app is about to shut down.

## $(Phone)

You can use the Phone-API to request a $(Phone.call:call) initiation:

	var phone = require('FuseJS/Phone');
	phone.call("number");

## Camera

Allows you to take pictures with the device's camera.

	var camera = require('FuseJS/Camera');

Use it like so:

	camera.takePicture({ targetWidth: 640, targetHeight: 360}).then(function(file)
	{
		// file is a standard JavaScript File object
		// file.path contains the path to the saved image on the device
	}).catch(function(e) {
		console.log(e);
	});

You can optionally add a `correctOrientation`-value to the original request if you don't want the call to automatically rotate the picture according to EXIF data. This will be done by default.

### `takePicture` function

Takes a picture with the device's camera.

	camera.takePicture({ targetWidth: width, targetHeight: height, correctOrientation: shouldCorrect })

The option object can contain the following properties:

* `targetWidth` - Desired width of the picture, in pixels. If omitted, the default is used.
* `targetHeight` - Desired height of the picture, in pixels. If omitted, the default is used.
* `correctOrientation` - True if you want to correct orientation of the picture

The method returns a `Promise` that if successful yields a `File` object that points to the
image saved as a JPG image on disk.

Note that the `targetWith` and `targetHeight` are just hints, the returned image can be any size.

## Vibration

Allows you to use the devices vibration functionality.

	var vibration = require('FuseJS/Vibration');

User it like so:

	vibration.vibrate(200);

Vibrates for 200 milliseconds.

<!-- TODO: Document localstorage -->


## InterApp

The InterApp module is designed to simplify communication between apps.

	var interApp = require('FuseJS/InterApp');

Right now it handles two cases:
- Request launching another app with a Uri
- Receiving Uri launch requests from other apps

This is exposed via a method and a callback.

### launchUri(uri)

Call this method with a string and fuse will request the system to open the app that handles that Uri.

```
interApp.launchUri ('purple://some/uri')
```

### onReceivedUri(uri)

Is a callback telling you if another app launches your app with a Uri. To receive this first you need to give your app a custom Uri scheme. We can do this easily by adding the "UriScheme" element to the "Mobile" section of the unoproj file.

After you have done that your "Mobile" section will look something like this
```
"Mobile": {
  "UriScheme": "YourScheme",
  "Orientations": "Auto",
},
```

From now on, any time a Uri is opened on the device starting with `YourScheme://` your app will be opened and the `onReceivedUri` callback will be fired with the full Uri.

If your app is already open `onReceivedUri` will just fire. Your app may momentarily leave interactive mode, but this is perfectly normal.

To use the callback in your javascript code you can simply do the following:

```
var interApp = require('FuseJS/InterApp1);

interApp.onReceivedUri = function(uri) {
    console.log ("js recieved Uri: " + uri);
};
```


## Geolocation

Get the geographic location of the user.
```
var geoLocation = require('FuseJS/GeoLocation');
```

The location API have three possible ways of getting the location data.

### location

If this or some other application recently requested a location update, this property will immediately get the last known position. If no location data is available this property returns `null`.
```
if(geo.location != null)
    console.log(JSON.stringify(geo.location));
```
### Single update: getLocation(timeout)

Get best possible position within the given timeout.
```
var timeout = 2000;
geoLocation.getLocation(timeout).then(function(location) {
    console.log(JSON.stringify(location));
});
```

### Continues updates: onChanged, startListening, stopListening

Start listening for continues position changes using the desired settings. Remember that getting a lot of location data at fast interval and accuracy will drain the battery fast.
```
geoLocation.onChanged = function(location) {
    console.log(JSON.stringify(location));
};
var minimumReportInterval = 1000, desiredAccuracyInMeters = 10;
geoLocation.startListening(minimumReportInterval, desiredAccuracyInMeters);

function stop() {
   geoLocation.stopListening();
}
```