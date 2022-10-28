---
obsidianUIMode: preview
---

This shortcut-file works with the state of many other shortcut-files.  Notably, it automatically maintains their state between Obsidian sessions.  It also includes shortcuts to allow you to save, load and reset states.

This shortcut file can be used by other shortcut-files to let them setup their own state that persists across Obsidian sessions.

This shortcut-file has 2 tutorial videos available.  The first video is the best for most users, but if you are writing shortcuts, then the second video shows you how to setup your own shortcut-file to use the state system:
- [Using the "state" shortcut-file to save and load session state](https://www.youtube.com/watch?v=IiRHB0FfJcI) (runtime 2:45)
- [Setup a shortcut-file for saving & loading with the "state" shortcut-file](https://www.youtube.com/watch?v=K-6Xy3YLuh4) (runtime 6:12)


To save the session state, use the "state get" shortcut.  This expands into a "state-string"- a string which includes all data in the current session state.  You can save this state-string simply by leaving it as part of the note.  To load the session state, call the "state set" shortcut, passing in a state-string _(created earlier with the "state get" shortcut)_.

Alternately, "state set last" will load the state-string from the last call to the "state get" shortcut in the current document.


__
```
^sfile setup$
```
__
```js
if (_inlineScripts?.state?.inSetup) { return; }

const confirmObjectPath =
	_inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;
confirmObjectPath("_inlineScripts.state.inSetup", true);

const STATE_FILE_NAME = "Ξ_state.data.md";

async function removeFile(filename)
{
	if (await app.vault.adapter.exists(filename))
	{
		await app.vault.adapter.remove(filename);
	}
}

confirmObjectPath("_inlineScripts.state.listeners.onReset");
confirmObjectPath("_inlineScripts.state.listeners.onLoad");

// Determine root folder for state file (every time, in case sfiles are moved)
confirmObjectPath("_inlineScripts.state.stateFile");
let stateFileHasBeenSet = false;
for (const sfile of _inlineScripts.inlineScripts.plugin.settings.shortcutFiles)
{
	if (sfile.address.endsWith("state.sfile.md"))
	{
		const path = sfile.address.slice(0, -14) + STATE_FILE_NAME;
		if (_inlineScripts.state.stateFile.path != path)
		{
			_inlineScripts.state.stateFile.path = path;
			stateFileHasBeenSet = true;
		}
		break;
	}
}

// Handle old state file locations (if state isn't already setup)
if (!_inlineScripts.state.sessionState)
{
	const oldStateFilePossibilities =
	[
		".obsidian/plugins/obsidian-text-expander-js/state.data.txt",
		".obsidian/plugins/obsidian-text-expander-js/state_onquit.data.txt",
		".obsidian/plugins/obsidian-text-expander-js/state_auto.data.txt"
	];
	let oldStateFiles = [];
	for (let i = oldStateFilePossibilities.length - 1; i >= 0; i--)
	{
		if (await app.vault.adapter.exists(oldStateFilePossibilities[i]))
		{
			oldStateFiles.push(oldStateFilePossibilities[i]);
		}
	}
	if (oldStateFiles.length > 1)
	{
		let times = [];
		for (let i = 0; i < oldStateFiles.length; i++)
		{
			times.push((await app.vault.adapter.stat(oldStateFiles[i])).mtime);
		}
		let latest = 0;
		let latestTime = times[0];
		for (let i = 1; i < oldStateFiles.length; i++)
		{
			if (times[i] > latestTime)
			{
				latest = i;
				latestTime = times[i];
			}
		}
		oldStateFiles = [ oldStateFiles[latest] ];
	}
	if (oldStateFiles.length == 1)
	{
		// Remove current state file (if it exists)
		await removeFile(_inlineScripts.state.stateFile.path);
		// Copy the latest old state file into the current state file
		await app.vault.adapter.copy(
			oldStateFiles[0],
			_inlineScripts.state.stateFile.path);
		stateFileHasBeenSet = true;
		// Delete oldStateFilePossibilities
		for (const oldStateFilePossibility of oldStateFilePossibilities)
		{
			await removeFile(oldStateFilePossibility);
		}
	}
}

// Load the state file if state is not defined (initial load, or sfile moved)
if (!_inlineScripts.state.sessionState || stateFileHasBeenSet)
{
	try
	{
		const stateString =
			await app.vault.adapter.read(_inlineScripts.state.stateFile.path);
		const state = JSON.parse(stateString);
		_inlineScripts.state.sessionState = state;
		_inlineScripts.state.stateFile.priorString = stateString;
	}
	catch (e)
	{
		if (e.code !== "ENOENT")
		{
			console.error(
				"state.sfile\n\tUnable to load from state file:\n\t" +
				_inlineScripts.state.stateFile.path + "\n\t", e);
		}
		_inlineScripts.state.sessionState = {};
		expand("state save");
	}
}

// React to every user-triggered shortcut expansion by re-saving the state
confirmObjectPath(
	"_inlineScripts.inlineScripts.listeners.onExpansion.state",
	async (expansionInfo) =>
	{
		if (expansionInfo.isUserTriggered)
		{
			expand("state save");
		}
	});

_inlineScripts.state.inSetup = false;
```
__
Sets up this shortcut-file


__
```
^sfile shutdown$
```
__
```js
// Cleanup vars
delete _inlineScripts.state;
```
__
Shuts down this shortcut-file


__
```
^state reset$
```
__
```js
_inlineScripts.state.sessionState = {};

// Notify listeners of state.onReset event
_inlineScripts.inlineScripts.HelperFncs.callEventListenerCollection(
	"state.onReset", _inlineScripts.state.listeners.onReset);

return "All state cleared.\n\n";
```
__
state reset - Clears all session state.


__
```
^state get$
```
__
```js
return "State:\n" + JSON.stringify(_inlineScripts.state.sessionState) + "\n\n";
```
__
state get - Expands to a state-string - a string containing all data for the current session state.


__
```
^state restore$
```
__
```js
const GET_PREFIX = "State:\n";
const content = await app.vault.cachedRead( app.workspace.getActiveFile());
const startIndex = content.lastIndexOf(GET_PREFIX);
if (startIndex < 0)
{
	return "State not loaded.  Last state not found.";
}
const endIndex = content.indexOf("\n", startIndex + GET_PREFIX.length);
if (endIndex < 0)
{
	return "State not loaded.  Last state has a bad format.";
}
const stateString = content.slice(startIndex + GET_PREFIX.length, endIndex);
return expand("state set " + stateString);
```
__
state restore - Loads the session state from the last "state get" shortcut expansion in the current note.


__
```
^state set (.+)$
```
__
```js
try
{
	_inlineScripts.state.sessionState = JSON.parse($1);
}
catch (e)
{
	return "State not loaded. Invalid state:\n" + $1 + "\n\n";
}

// Notify listeners of state.onLoad event
_inlineScripts.inlineScripts.HelperFncs.callEventListenerCollection(
	"state.onLoad", _inlineScripts.state.listeners.onLoad);

return "State set.\n\n";
```
__
state set {state: text} - Loads the session state from {state}, a state-string created with the "state get" shortcut.


__
```
^state save$
```
__
```js
const stateString = JSON.stringify(_inlineScripts.state.sessionState);
if (stateString === _inlineScripts.state.stateFile.priorString) { return; }
await app.vault.adapter.write(
	_inlineScripts.state.stateFile.path, stateString);
_inlineScripts.state.stateFile.priorString = stateString;
return "State saved.\n\n";
```
__
state save - Store the state to file.  This is called automatically after each shortcut expansion that triggers a state change.
