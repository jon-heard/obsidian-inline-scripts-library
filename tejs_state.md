This shortcut-file includes shortcuts that let the user manage a session state.  Effectively allows saving and restoring data between sessions.

This shortcut file can be used by other shortcut-files to help them save/restore their states.

To save the session state, use the "state" shortcut (no parameters).  This expands to a "state string", representing the current session state, which you can leave as part of the note.  To load the session state, copy the state string and pass it into the "state" shortcut.


~~
```
^tejs setup$
```
~~
```js
window._tejsState ||= {};
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
