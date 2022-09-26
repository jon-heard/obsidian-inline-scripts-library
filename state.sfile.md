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
const AUTOSAVE_INTERVAL_SECS = 10;

const confirmObjectPath =
	_inlineScripts.inlineScripts.helperFncs.confirmObjectPath;
confirmObjectPath("_inlineScripts.state.listeners.onReset");
confirmObjectPath("_inlineScripts.state.listeners.onLoad");

// Restore state (if-block makes sure this only happens once)
if (!_inlineScripts.state.sessionState)
{
	_inlineScripts.state.sessionState = {};
	confirmObjectPath(
		"_inlineScripts.inlineScripts.listeners.onShortcutsLoaded.state",
		async () =>
		{
			// Remove this event listener (to only trigger once)
			delete _inlineScripts.inlineScripts.listeners.onShortcutsLoaded.state;

			// Figure out which state file to use (if either)
			const stateDataFile_onQuit =
				".obsidian/plugins/obsidian-text-expander-js/" +
				"state_onquit.data.txt";
			const stateDataFile_auto =
				".obsidian/plugins/obsidian-text-expander-js/" +
				"state_auto.data.txt";
			const stateDataFile_onQuit_exists =
				await app.vault.adapter.exists(stateDataFile_onQuit);
			const stateDataFile_auto_exists =
				await app.vault.adapter.exists(stateDataFile_auto);
			let stateDataFile = null;
			let otherStateDataFile = null;
			if (!stateDataFile_onQuit_exists && stateDataFile_auto_exists)
			{
				stateDataFile = stateDataFile_auto;
			}
			else if (stateDataFile_onQuit_exists && !stateDataFile_auto_exists)
			{
				stateDataFile = stateDataFile_onQuit;
			}
			else if (stateDataFile_onQuit_exists && stateDataFile_auto_exists)
			{
				const stateDataFile_onQuit_mtime =
					(await app.vault.adapter.stat(stateDataFile_onQuit)).mtime;
				const stateDataFile_auto_mtime =
					(await app.vault.adapter.stat(stateDataFile_auto)).mtime;
				if (stateDataFile_onQuit_mtime > stateDataFile_auto_mtime)
				{
					stateDataFile = stateDataFile_onQuit;
					otherStateDataFile = stateDataFile_auto;
				}
				else
				{
					stateDataFile = stateDataFile_auto;
					otherStateDataFile = stateDataFile_onQuit;
				}
			}

			// Load the appropriate state file
			if (stateDataFile)
			{
				try
				{
					let fileName = stateDataFile.match(/([^/]+)$/)[1];
					console.log(
						"Inline Scripts - state.sfile\n\tLoading state \"" +
						fileName + "\"");
					let data = await app.vault.adapter.read(stateDataFile);
					if (!data && otherStateDataFile)
					{
						fileName = otherStateDataFile.match(/([^/]+)$/)[1];
						console.log(
							"Inline Scripts - state.sfile\n\tLoaded state " +
							"invalid.\n\tLoading state \"" + fileName + "\"");
						data = await app.vault.adapter.read(otherStateDataFile);
					}
					if (data)
					{
						expand("state set " + data);
					}
					else
					{
						throw null;
					}
				}
				catch(e)
				{
					console.log(
						"Inline Scripts - state.sfile\n\tLoaded state invalid.\n" +
						"\tUnable to load a valid state.");
				}
			}
		});
}

confirmObjectPath(
	"_inlineScripts.state.autosave.intervalFnc",
	setInterval(async () =>
	{
		const stateData = JSON.stringify(_inlineScripts.state.sessionState);
		if (stateData === _inlineScripts.state.autosave.priorStateData)
		{
			return;
		}
		const stateDataFile =
			".obsidian/plugins/obsidian-text-expander-js/state_auto.data.txt";
		try
		{
			await app.vault.adapter.write(stateDataFile, stateData);
			_inlineScripts.state.autosave.priorStateData = stateData;
		}
		catch(e)
		{
			console.error("Auto-save failed", e);
		}
	}, 1000/*ms-sec*/ * AUTOSAVE_INTERVAL_SECS));

```
__
Sets up this shortcut-file


__
```
^sfile shutdown$
```
__
```js
// Save state
const stateDataFile =
	".obsidian/plugins/obsidian-text-expander-js/state_onquit.data.txt";
const stateData = JSON.stringify(_inlineScripts.state.sessionState);
await app.vault.adapter.write(stateDataFile, stateData);

// Cleanup vars
clearInterval(_inlineScripts.state.autosave.intervalFnc);
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
_inlineScripts.inlineScripts.helperFncs.callEventListenerCollection(
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
_inlineScripts.inlineScripts.helperFncs.callEventListenerCollection(
	"state.onLoad", _inlineScripts.state.listeners.onLoad);

return "State loaded.\n\n";
```
__
state set {state: text} - Loads the session state from {state}, a state-string created with the "state get" shortcut.
