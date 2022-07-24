---
obsidianUIMode: preview
---

This shortcut-file includes shortcuts that let the user manage a session state.  Effectively allows saving and restoring data between sessions.

This shortcut file can be used by other shortcut-files to help them save/restore their states.

To save the session state, use the "state" shortcut (no parameters).  This expands to a "state string", representing the current session state, which you can leave as part of the note.  To load the session state, copy the state string and pass it into the "state" shortcut.


__
```
^tejs setup$
```
__
```js
window._tejs ||= {};
window._tejs.state ||= {};
window._tejs.listeners ||= {};
window._tejs.listeners.state ||= {};
window._tejs.listeners.state.onReset ||= {};
window._tejs.listeners.state.onLoad ||= {};
```
__
Sets up a global variable to hold the state for all shortcut-files.  Also, sets up an object that other shortcut-files can add callbacks to that get called when state is loaded or reset.


__
```
^reset state$
```
__
```js
window._tejs.state = {};

for (const key in window._tejs.listeners.state.onReset)
{
	const listener = window._tejs.listeners.state.onReset[key];
	if (typeof listener !== "function")
	{
		console.warn("Non-function is registered as a listener:" + listener);
		continue;
	}
	listener(expand);
}

return "All state cleared.\n\n";
```
__
reset state - Clear all state.


__
```
^state$
```
__
```js
return "State:\n" + JSON.stringify(window._tejs.state) + "\n\n";
```
__
state - Expands to a string representing the current state (for session saving).


__
```
^state (.*)$
```
__
```js
try
{
	window._tejs.state = JSON.parse($1);
}
catch (e)
{
	return "State loading failed due to invalid state:\n    " + $1 + "\n\n";
}

for (const key in window._tejs.listeners.state.onLoad)
{
	const listener = window._tejs.listeners.state.onLoad[key];
	if (typeof listener !== "function")
	{
		console.warn("Non-function is registered as a listener:" + listener);
		continue;
	}
	listener(expand);
}

return "State loaded.\n\n";
```
__
state {state} - Sets the current state based on {state}: a string created with the parameterless "state" shortcut.
