Shortcuts that let the user manage clips of text.  A "clip" is a bit of named text that can be quickly added to your note.  Similar to a shortcut, but more simplistic.


~~
```
^tejs setup$
```
~~
```js
window._tejsState ||= {};
window._tejsState.clips ||= {};
```
~~
Sets up a state variable for the clips.


~~
```
^reset clips$
```
~~
```js
window._tejsState.clips = {};
return "All clips cleared.\n\n";
```
~~
reset clips - Remove all clips.


~~
```
^clip$
```
~~
```js
let clipNames = Object.keys(window._tejsState.clips);
return "__Clips__\n" + (clipNames.length ? clipNames.join(", ") : "none") + "\n\n";
```
~~
clip - Lists all stored clips.


~~
```
^clip ([a-zA-Z]+)$
```
~~
```js
let text = window._tejsState.clips[$1];
return text || "";
```
~~
clip {name} - Expands to the value stored in clip {name}.


~~
```
^clipadd ([a-zA-Z]+) (.+)$
```
~~
```js
window._tejsState.clips[$1] = $2;
return "__Clip \"" + $1 + "\" set to__\n" + $2 + "\n\n";
```
~~
clipadd {name} {value} - Creates a clip {name} that stores the string {value}.


~~
```
^clipremove ([a-zA-Z]+)$
```
~~
```js
if (window._tejsState.clips.hasOwnProperty($1))
{
	delete window._tejsState.clips[$1];
	return "Clip \"" + $1 + "\" removed.\n\n";
}
return "Failed to remove clip \"" + $1 + "\".  Does not exist.\n\n";
```
~~
clipremove {name} - Removes the clip {name}.
