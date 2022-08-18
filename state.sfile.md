---
obsidianUIMode: preview
---

This shortcut-file includes shortcuts to manage a session state.  This allows one to save and restore the state of other shortcut-files across Obsidian sessions.

This shortcut file can be used by other shortcut-files to let them create data that can persist across sessions of Obsidian.

To save the session state, use the "state get" shortcut.  This expands into a "state-string"- a string which includes all data in the current session state.  You can save this state-string simply by leaving it as part of the note.  To load the session state, call the "state set" shortcut, passing in a state-string _(created earlier with the "state get" shortcut)_.

Alternately, "state set last" will load the state-string from the last call to the "state get" shortcut in the current document.


__
__
```js
// confirm that an object path is available
function confirmObjPath(path, leaf)
{
    path = path.split(".");
    let parent = window;
    for (let i = 0; i < path.length-1; i++)
    {
        parent = (parent[path[i]] ||= {});
    }
    parent[path[path.length-1]] ||= (leaf || {});
}
// Call an array or object of functions.
// Useful for listener collections.
function callFunctionCollection(colName, collection)
{
	if (!Array.isArray(collection))
	{
		collection = Object.values(collection);
	}
	for (const fnc of collection)
	{
		if (typeof fnc === "function")
		{
			fnc(expand);
		}
		else
		{
			console.warn(
				"Non-function in collection \"" +
				colName + "\": " + fnc);
		}
	}
}
```
__
Some userful functions


__
```
^sfile setup$
```
__
```js
confirmObjPath("_inlineScripts.state");
confirmObjPath("_inlineScripts.listeners.state.onReset");
confirmObjPath("_inlineScripts.listeners.state.onLoad");
```
__
Sets up a global variable to hold the state for all shortcut-files.  Also, sets up an object that other shortcut-files can add callbacks to that get called when state is loaded or reset.


__
```
^reset state$
```
__
```js
_inlineScripts.state = {};

// Notify listeners of state.onReset event
callFunctionCollection(
	"state.onReset",
	_inlineScripts.listeners.state.onReset);

return "All state cleared.\n\n";
```
__
reset state - Clear all session state.


__
```
^state get$
```
__
```js
return "State:\n" + JSON.stringify(_inlineScripts.state) + "\n\n";
```
__
state get - Expands to a state-string - a string which includes all data in the current session state.


__
```
^state restore$
```
__
```js
const GET_PREFIX = "State:\n";
const content =
	  await app.vault.cachedRead(
	  app.workspace.getActiveFile());
const startIndex = content.lastIndexOf(GET_PREFIX);
if (startIndex < 0)
{
	return "State not loaded.  Last state not found.";
}
const endIndex =
	  content.indexOf("\n", startIndex + GET_PREFIX.length);
if (endIndex < 0)
{
	return "State not loaded.  Last state has a bad format.";
}
const stateString =
	  content.slice(startIndex + GET_PREFIX.length, endIndex);
return expand("state set " + stateString);
```
__
state restore - Load the session state from the final "state get" shortcut expansion in the current note.


__
```
^state set (.*)$
```
__
```js
try
{
	_inlineScripts.state = JSON.parse($1);
}
catch (e)
{
	return "State loading failed. Invalid state:\n" +
		$1 + "\n\n";
}

// Notify listeners of state.onLoad event
callFunctionCollection(
	"state.onLoad",
	_inlineScripts.listeners.state.onLoad);

return "State loaded.\n\n";
```
__
state set {state: required, text} - Loads the session state from {state}, a state-string created with the "state get" shortcut.
