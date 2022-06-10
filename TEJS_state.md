Shortcuts that let the user manage a state.  Effectively allows saving and loading data between sessions.
Also, shortcuts that let the user manage "clips" of text (stored in state).
This shortcut file is used by other shortcut files to let them keep their states too.

Enter the shortcut "state help" for reference.

~~
^help state$
~~
let result = "### TEJS STATE SHORTCUTS HELP\n";
result += "- __help state__ - Display this help text.\n";
result += "---\n";
result += "- __state__ - Expands to the current state (for session saving).\n";
result += "- __state {state}__ - Sets the current state based on {state}: a string created previously with the \"state\" shortcut.\n";
result += "---\n";
result += "- __clip__ - List all stored clips.\n";
result += "- __clip {name}__ - Expands to the value stored in clip {name}.\n";
result += "- __clipadd {name} {value}__ - Creates a clip {name} that stores {value}.\n";
result += "- __clipremove {name}__ - Removes the clip {name}.\n";
return result + "\n";

~~
^tejs setup$
~~
window._tejsState ||= {};
window._tejsState.clips ||= {};

~~
^state$
~~
return "__TEJS state__\n" + JSON.stringify(window._tejsState) + "\n\n";

~~
^state (.*)$
~~
window._tejsState = JSON.parse($1);
return "TEJS state loaded.\n\n";

~~
^clip$
~~
let clipNames = Object.keys(window._tejsState.clips);
return "__Clips__\n" + (clipNames.length ? clipNames.join(", ") : "none") + "\n\n";

~~
^clip ([a-zA-Z]+)$
~~
let text = window._tejsState.clips[$1];
return text || "";

~~
^clipadd ([a-zA-Z]+) (.+)$
~~
window._tejsState.clips[$1] = $2;
return "__Clip \"" + $1 + "\" set to__\n" + $2 + "\n\n";

~~
^clipremove ([a-zA-Z]+)$
~~
if (window._tejsState.clips.hasOwnProperty($1))
{
	delete window._tejsState.clips[$1];
	return "Clip \"" + $1 + "\" removed.\n\n";
}
return "Failed to remove clip \"" + $1 + "\".  Does not exist.\n\n";
