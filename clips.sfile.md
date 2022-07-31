---
obsidianUIMode: preview
---

Shortcuts that let the user manage clips of text.  A "clip" is a bit of named text that can be quickly added to your note.  Similar to a shortcut, but simplistic and tied to the session state.

Uses __state.sfile__ shortcut-file (optional).
It uses this to save & load the clips.


__
```
^sfile setup$
```
__
```js
window._inlineScripts ||= {};
window._inlineScripts.state ||= {};
window._inlineScripts.state.clips ||= {};
window._inlineScripts.clips ||= {};
window._inlineScripts.clips.priorExpansion ||= "";

window._inlineScripts.listeners ||= {};
window._inlineScripts.listeners.state ||= {};
window._inlineScripts.listeners.state.onReset ||= {};
window._inlineScripts.listeners.state.onReset.clips ||= async expand =>
{
	expand("reset clips");
};
window._inlineScripts.listeners.inlineScripts ||= {};
window._inlineScripts.listeners.inlineScripts.onExpansion ||= {};
window._inlineScripts.listeners.inlineScripts.onExpansion.clips ||= (input, expansion) =>
{
	window._inlineScripts.clips.priorExpansion = expansion;
};
```
__
Sets up a state variable for the clips.  Sets up callback for state "reset" event to reset itself.


__
```
^sfile shutdown$
```
__
```js
delete window._inlineScripts.listeners?.state?.onReset?.clips;
delete window._inlineScripts.listeners?.inlineScripts?.onExpansion?.clips;
```
__
Unregisters event callbacks.


__
```
^reset clips?$
```
__
```js
window._inlineScripts.state.clips = {};
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
let clipNames = Object.keys(window._inlineScripts.state.clips);
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
let text = window._inlineScripts.state.clips[$1];
return text || "";
```
__
clips get {name: required, name} - Expands to the value stored in clip {name}.
        Alternative shortcut: __cg {name}__.


__
```
^clips? add ([_a-zA-Z][_a-zA-Z0-9]*) (.+)$
```
__
```js
$1 = $1.toLowerCase();
window._inlineScripts.state.clips[$1] = $2;
return "Clip __" + $1 + "__ set to:\n" + $2 + "\n\n";
```
__
clips add {name: required, name} {value, required, text} - Creates a clip named {name} that stores the string {value}.


__
```
^clips? expansion ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
$1 = $1.toLowerCase();
window._inlineScripts.state.clips[$1] = window._inlineScripts.clips.priorExpansion;
return "Clip __" + $1 + "__ set to:\n" + window._inlineScripts.clips.priorExpansion + "\n\n";
```
__
clips expansion {name: required name} - Creates a clip named {name} that stores the previous expansion.


__
```
^clips? remove ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
$1 = $1.toLowerCase();
if (window._inlineScripts.state.clips[$1])
{
	delete window._inlineScripts.state.clips[$1];
	return "Clip __" + $1 + "__ removed.\n\n";
}
return "Failed to remove clip __" + $1 + "__.  Does not exist.\n\n";
```
__
clips remove {name: required, name} - Removes the clip named {name}.
