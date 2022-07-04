Shortcuts that let the user manage clips of text.  A "clip" is a bit of named text that can be quickly added to your note.  Similar to a shortcut, but more simplistic.

Uses __tejs_state__ shortcut-file (optional).
It uses this to save & load the clips.


~~
```
^tejs setup$
```
~~
```js
window._tejs ||= {};
window._tejs.state ||= {};
window._tejs.state.clips ||= {};
window._tejs.clips ||= {};
window._tejs.clips.priorExpansion ||= "";

window._tejs.listeners ||= {};
window._tejs.listeners.state ||= {};
window._tejs.listeners.state.onReset ||= [];
window._tejs.listeners.state.onReset.clips ||= expand =>
{
	expand("reset clips");
};
window._tejs.listeners.tejs ||= {};
window._tejs.listeners.tejs.onExpansion ||= [];
// Undocumented feature: total undocumented features = 1 (just this one).
window._tejs.listeners.tejs.onExpansion.clips ||= expansion =>
{
	window._tejs.clips.priorExpansion = expansion;
};
```
~~
Sets up a state variable for the clips.  Sets up callback for state "reset" event to reset itself.


~~
```
^tejs shutdown$
```
~~
```js
delete window._tejs.listeners?.state?.onReset?.clips;
delete window._tejs.listeners?.tejs?.onExpansion?.clips;
```
~~
Unregisters event callbacks.


~~
```
^reset clips$
```
~~
```js
window._tejs.state.clips = {};
return "All clips cleared.\n\n";
```
~~
reset clips - Remove all clips.


~~
```
^clips?$
```
~~
```js
let clipNames = Object.keys(window._tejs.state.clips);
return "__Clips__\n" + (clipNames.length ? clipNames.join(", ") : "NONE") + "\n\n";
```
~~
clips - Lists all stored clips.


~~
```
^(?:clips? get|cg) ([_a-zA-Z][_a-zA-Z0-9]*)$
```
~~
```js
$1 = $1.toLowerCase();
let text = window._tejs.state.clips[$1];
return text || "";
```
~~
clips get {name} - Expands to the value stored in clip {name} (a required name string).
	- Alternative shortcut: __cg {name}__.


~~
```
^clips? add ([_a-zA-Z][_a-zA-Z0-9]*) (.+)$
```
~~
```js
$1 = $1.toLowerCase();
window._tejs.state.clips[$1] = $2;
return "__Clip \"" + $1 + "\" set to__\n" + $2 + "\n\n";
```
~~
clips add {name} {value} - Creates a clip named {name} (a required name string) that stores the string {value} (a required text).


~~
```
^clips? expansion ([_a-zA-Z][_a-zA-Z0-9]*)$
```
~~
```js
$1 = $1.toLowerCase();
console.log("Clipping: ''" + window._tejs.clips.priorExpansion + "'");
window._tejs.state.clips[$1] = window._tejs.clips.priorExpansion;
return "__Clip \"" + $1 + "\" set to__\n" + window._tejs.clips.priorExpansion + "\n\n";
```
~~
clips expansion {name} - Creates a clip named {name} (a required name string) that stores the previous expansion.


~~
```
^clips? remove ([_a-zA-Z][_a-zA-Z0-9]*)$
```
~~
```js
$1 = $1.toLowerCase();
if (window._tejs.state.clips[$1])
{
	delete window._tejs.state.clips[$1];
	return "Clip \"" + $1 + "\" removed.\n\n";
}
return "Failed to remove clip \"" + $1 + "\".  Does not exist.\n\n";
```
~~
clips remove {name} - Removes the clip named {name} (a required name string).
