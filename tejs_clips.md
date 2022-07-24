---
obsidianUIMode: preview
---

Shortcuts that let the user manage clips of text.  A "clip" is a bit of named text that can be quickly added to your note.  Similar to a shortcut, but simplistic and tied to the session state.

Uses __tejs_state__ shortcut-file (optional).
It uses this to save & load the clips.


__
```
^tejs setup$
```
__
```js
window._tejs ||= {};
window._tejs.state ||= {};
window._tejs.state.clips ||= {};
window._tejs.clips ||= {};
window._tejs.clips.priorExpansion ||= "";

window._tejs.listeners ||= {};
window._tejs.listeners.state ||= {};
window._tejs.listeners.state.onReset ||= {};
window._tejs.listeners.state.onReset.clips ||= expand =>
{
	expand("reset clips");
};
window._tejs.listeners.tejs ||= {};
window._tejs.listeners.tejs.onExpansion ||= {};
window._tejs.listeners.tejs.onExpansion.clips ||= (input, expansion) =>
{
	window._tejs.clips.priorExpansion = expansion;
};
```
__
Sets up a state variable for the clips.  Sets up callback for state "reset" event to reset itself.


__
```
^tejs shutdown$
```
__
```js
delete window._tejs.listeners?.state?.onReset?.clips;
delete window._tejs.listeners?.tejs?.onExpansion?.clips;
```
__
Unregisters event callbacks.


__
```
^reset clips?$
```
__
```js
window._tejs.state.clips = {};
return "All clips cleared.\n\n";
```
__
reset clips - Remove all clips.


__
```
^clips?$
```
__
```js
let clipNames = Object.keys(window._tejs.state.clips);
return "Clips:\n" + (clipNames.length ? clipNames.join(", ") : "NONE") + "\n\n";
```
__
clips - Lists all stored clips.


__
```
^(?:clips? get|cg) ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
$1 = $1.toLowerCase();
let text = window._tejs.state.clips[$1];
return text || "";
```
__
clips get {name} - Expands to the value stored in clip {name} (a required name string).
        Alternative shortcut: __cg {name}__.


__
```
^clips? add ([_a-zA-Z][_a-zA-Z0-9]*) (.+)$
```
__
```js
$1 = $1.toLowerCase();
window._tejs.state.clips[$1] = $2;
return "Clip __" + $1 + "__ set to:\n" + $2 + "\n\n";
```
__
clips add {name} {value} - Creates a clip named {name} (a required name string) that stores the string {value} (a required text).


__
```
^clips? expansion ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
$1 = $1.toLowerCase();
window._tejs.state.clips[$1] = window._tejs.clips.priorExpansion;
return "Clip __" + $1 + "__ set to:\n" + window._tejs.clips.priorExpansion + "\n\n";
```
__
clips expansion {name} - Creates a clip named {name} (a required name string) that stores the previous expansion.


__
```
^clips? remove ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
$1 = $1.toLowerCase();
if (window._tejs.state.clips[$1])
{
	delete window._tejs.state.clips[$1];
	return "Clip __" + $1 + "__ removed.\n\n";
}
return "Failed to remove clip __" + $1 + "__.  Does not exist.\n\n";
```
__
clips remove {name} - Removes the clip named {name} (a required name string).
