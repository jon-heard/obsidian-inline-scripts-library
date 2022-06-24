This shortcut-file includes shortcuts that let the user manage a session state.  Effectively allows saving and restoring data between sessions.  

This shortcut-file also includes shortcuts that let the user manage "clips" of text.

This shortcut file can be used by other shortcut files to help them save/restore their states.

To save the session state, use the "state" shortcut (no parameters).  This expands to a "state string", which represents the current session state, which you can leave as part of the note.  To load the session state, copy the state string and pass it into the "state" shortcut.


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
Sets up a global variable to hold the state for all shortcut-files.


~~
```
^reset state$
```
~~
```js
window._tejsState = {};
window._tejsState.clips = {};
return "All state cleared.\n\n";
```
~~
reset state - Clear all state.


~~
```
^state$
```
~~
```js
return "__State__\n" + JSON.stringify(window._tejsState) + "\n\n";
```
~~
state - Expands to a string representing the current state (for session saving).


~~
```
^state (.*)$
```
~~
```js
window._tejsState = JSON.parse($1);
return "State loaded.\n\n";
```
~~
state {state} - Sets the current state based on {state}: a string created with the parameterless "state" shortcut.
***


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
