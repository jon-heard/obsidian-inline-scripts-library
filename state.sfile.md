---
obsidianUIMode: preview
---

This shortcut-file includes shortcuts to manage a session state.  This allows one to save and restore the state of other shortcut-files across Obsidian sessions.

This shortcut file can be used by other shortcut-files to let them create data that can persist across sessions of Obsidian.

This shortcut-file has 2 tutorial videos available.  The first one is right for most users, but if you are making your own shortcut-file, the second one shows you how to connect it to the state system:
- [Using the "state" shortcut-file to save and load session state](https://www.youtube.com/watch?v=WHQuQm3RieY) (runtime 2:23)
- [Setup a shortcut-file for saving & loading with the "state" shortcut-file](https://www.youtube.com/watch?v=K-6Xy3YLuh4) (runtime 6:12)


To save the session state, use the "state get" shortcut.  This expands into a "state-string"- a string which includes all data in the current session state.  You can save this state-string simply by leaving it as part of the note.  To load the session state, call the "state set" shortcut, passing in a state-string _(created earlier with the "state get" shortcut)_.

Alternately, "state set last" will load the state-string from the last call to the "state get" shortcut in the current document.


__
```
^sfile setup$
```
__
```js
const confirmObjectPath =
	_inlineScripts.inlineScripts.helperFncs.confirmObjectPath;
confirmObjectPath("_inlineScripts.state.sessionState");
confirmObjectPath("_inlineScripts.state.listeners.onReset");
confirmObjectPath("_inlineScripts.state.listeners.onLoad");
```
__
Sets up a global variable to hold the state for all shortcut-files.  Also, sets up an object that other shortcut-files can add callbacks to that get called when state is loaded or reset.


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
state reset - Clear all session state.


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
state restore - Load the session state from the last "state get" shortcut expansion in the current note.


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
