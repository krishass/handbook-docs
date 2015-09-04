# $(Fuse Troubleshooting Guide)

## Cannot connect to daemon

### Symptom
- You get a message `fuse: A network error occurred: Could not resolve host '<your hostname>' Please check your network setup and try again.`

### Solution
- You have an issue with your network. Try doing `ping $(hostname)` in a terminal. If you get an error message, you have to fix your network setup before you continue.

### Symptom
- You get a stack trace with `FailedToConnectToDaemon` somewhere in it
 
### Solution
- First, try to stop the Fuse daemon.
 - Windows: Right click on the Fuse icon in the tray, click "Exit"
 - OS X: Control-click on the Fuse icon in the menu bar, click "Quit"
- A fresh daemon will then start automatically the next time you interact with Fuse.
- If that did not help, please try logging out of and back in to Windows / OS X.
- If that did not help, please try to reboot your computer.

## Sublime plugin does not work
### Symptom
- You get the message "Error loading: Packages/Fuse/UX.tmLanguage"
- You don't get syntax highlighting in Sublime

### Solution
- Open the dashboard, click "Sublime Text Setup" and follow the wizard.
- If that didn't work, delete the files staring with `Fuse` in `%APPDATA%\Sublime Text 3\Installed Packages` (Windows) or `~/Library/Application Support/Sublime Text 3/Installed Packages` (OS X), and try the wizard again.

## My Uno code is not run/updated in preview
### Symptom
- Changes to your Uno code is not reflected in preview
- Your Uno code is not executed in preview

### Explanation
- Unlike ux/js, Uno-code is not refreshed when you save and auto-refresh preview, you'll have to restart Fuse. A better solution for this is coming soon.
 - Windows: Right click on the Fuse icon in the tray, click "Exit"
 - OS X: Control-click on the Fuse icon in the menu bar, click "Quit"
- In addition, any Uno-code you have in `ux.uno` files is not run at all in preview.
- All in all, it can be easier to work with Uno code through `uno build` than through preview. A better solution for this is coming soon.

## Preview says "Oops! Something went wrong here"
### Symptom
- When previewing your app, the "Oops! Something went wrong here" screen appears.

### Solution
- Open the Fuse Monitor from the Tray or Dashboard to see details about the problem.
