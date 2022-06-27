Setup notes with variables.  Useful for reading raw data from a nicely formatted document.

Uses __tejs_state__ shortcut-file (optional).  It uses this to save & load the varnotes lists.

Can be used by other shortcut-files for dynamically reading data for other operations.

The syntax for a variable in a document is \*\*\%\%variable name\%\%\*\*\_variable value\_:
- double-asterisk
- double-percentages
- __variable name__
- double-percentages
- double-asterisk
-  single-underscore
- __variable value__
- single-underscore

Note that the variable name is case-INsensitive, so STR, str and Str are all considered the same name.

Examples:
- \*\*\%\%Name\%\%\*\*\_Maggie Smith\_.
    - This specifies that the data named "name" (case-insensitive) is set to value "Maggie Smith".
- \*\*\%\%Str\%\%\*\*\_18\_.
    - This specifies that the data named "str" (case-insensitive) is set to value 18.


~~
```
^tejs setup$
```
~~
```js
window._tejsState ||= {};
window._tejsState.varnotes ||= {};
window._tejsVarnotes ||= {};
window._tejsVarnotes.regex_vars ||= /\*\*%%([_a-zA-Z_][_a-zA-Z_0-9]*)%%\*\*_([^_]+)_/g;
window._tejsVarnotes.regex_var ||= "\*\*%%~1~%%\*\*_([^_]+)_";

if (!window._tejsVarnotes.revLookup)
{
	window._tejsVarnotes.revLookup = {};
	for (const noteName in window._tejsState.varnotes)
	{
		window._tejsVarnotes.revLookup
			[window._tejsState.varnotes[noteName]] = noteName;
	}
}

if (!window._tejsVarnotes.data)
{
	window._tejsVarnotes.data ||= {};
	for (const varnoteName in window._tejsState.varnotes)
	{
		getExpansion("varnotes refresh " + varnoteName);
	}
}

if (!window._tejsVarnotes.onModified)
{
	window._tejsVarnotes.onModified = file =>
	{
		let varnoteName = window._tejsVarnotes.revLookup[file.path];
		if (varnoteName)
		{
			getExpansion("varnotes refresh " + varnoteName);
		}
	};
	app.vault.on("modify", window._tejsVarnotes.onModified);
}
```
~~
Sets up a variable to store varnote data in the global tejs state variable.  Registers the file-modified event.


~~
```
^tejs shutdown$
```
~~
```js
if (window._tejsVarnotes?.onModified)
{
	app.vault.off("modify", window._tejsVarnotes.onModified);
	delete window._tejsVarnotes.onModified;
	delete window._tejsVarnotes.data;
}
```
~~
Unregisters the file-modified event.


~~
```
^reset varnotes$
```
~~
```js
window._tejsState ||= {};
window._tejsState.varnotes = {};
window._tejsVarnotes.data = {};
return "All varnotes cleared.\n\n";
```
~~
reset varnotes - Removes all stored varnotes from the session state.


~~
```
^varnotes$
```
~~
```js
let result = ["__Varnotes__:\n"];
let hasNone = true;
for (const key in window._tejsState.varnotes)
{
	result.push("- " + key + " - " + window._tejsState.varnotes[key] + "\n");
	hasNone = false;
}
if (hasNone) { result.push("NONE"); }
result.push("\n\n");
return result;
```
~~
varnotes - Lists all varnotes and the files they are connected to.


~~
```
^varnotes data ([a-zA-Z_][a-zA-Z_0-9]*)$
```
~~
```js
let result = ["__Varnotes - ", $1, "__:\n"];
const data = window._tejsVarnotes.data[$1];
let hasData = false;
if (data)
{
	for (const key in data)
	{
		result.push("- __" + key + "__ - " + data[key] + "\n");
		hasData = true;
	}
}
if (!hasData) { result.push("NO DATA"); }
result.push("\n\n");
return result;
```
~~
varnotes data {varnote name} - Lists all the data for varnote {varnote name} (an required name paroperty).


~~
```
^varnotes add ([a-zA-Z_][a-zA-Z_0-9]*) (.*)$
```
~~
```js
$2 = $2.endsWith(".md") ? $2 : $2 + ".md";
window._tejsState.varnotes[$1] = $2
window._tejsVarnotes.revLookup[$2] = $1;
getExpansion("varnotes refresh " + $1);
return "Varnote \"" + $1 + "\" added.\n\n";
```
~~
varnotes add {varnote name} {file address} - Adds a varnote named {varnote name} (a required name parameter) that is attached to the file at {file address} (a required file address parameter).


~~
```
^varnotes remove ([a-zA-Z_][a-zA-Z_0-9]*)$
```
~~
```js
if (!window._tejsState.varnotes[$1])
{
	return "Varnote \"" + %i + "\" not removed.  Does not exist.";
}
delete window._tejsVarnotes.revLookup[window._tejsState.varnotes[$1]];
delete window._tejsState.varnotes[$1];
delete window._tejsVarnotes.data[$1];
return "Varnote \"" + $1 + "\" removed.\n\n";
```
~~
varnotes remove {varnote name} - Removes the varnote {var name} (a required name parameter).


~~
```
^(?:varnotes get|vn) ([a-zA-Z_][a-zA-Z_0-9]*) ([a-zA-Z_][a-zA-Z_0-9]*)$
```
~~
```js
const data = window._tejsVarnotes.data[$1];
if (!data) { return "varnote \"" + $1 + "\" not found."; }
const datum = data[$2.toLowerCase()];
if (!datum) { return "data \"" + $2 + "\" not found."; }
return data;
```
~~
varnotes get {varnote name} {data name} - Gets the data named {data name} (a required name parameter) in the varnote named {varnote name} (a required name parameter).
    - Alternative shortcut: __vn {varnote name} {data name}__.


~~
```
^(?:varnotes set|vnset) ([a-zA-Z_][a-zA-Z_0-9]*) ([a-zA-Z_][a-zA-Z_0-9]*) (.+)$
```
~~
```js
const filename = window._tejsState.varnotes[$1];
if (!filename)
{
	return "Varnote \"" + $2 + "\" not set.  Varnote \"" + $1 + "\" not found.\n\n";
}
const file = app.vault.fileMap[filename];
if (!file)
{
	return "Varnote \"" + $2 + "\" not set.  File \"" + filename + "\" not found.\n\n";
}
app.vault.cachedRead(file).then(r =>
{
debugger;
	const regex = window._tejsVarnotes.regex_var.replace("~1~", $2);
console.log(regex);
});
return [ "Varnote \"", $2, "\" set.\n\n" ];
```
~~
varnotes set {varnote name} {data name} {value} - Sets the data named {data name} (a required name parameter) to {value} (a required text parameter), in the varnote named {varnote name} (a required name parameter).
    - Alternative shortcut: __vnset {varnote name} {data name} {value}__.


~~
```
^(?:varnotes|vn) refresh$
```
~~
```js
let result = [];
for (const name in window._tejsState.varnotes)
{
	const newResult = getExpansion("varnotes refresh " + name);
	if (!Array.isArray(newResult))
	{
		result.push(newResult, "\n");
	}
}
result.push("Varnote refreshing finished.\n\n");
return result;
```
~~
varnotes refresh - Pull the data from all varnotes.
    - Alternative shortcut: __vn refresh__.


~~
```
^varnotes refresh ([a-zA-Z_][a-zA-Z_0-9]*)$
```
~~
```js
const filename = window._tejsState.varnotes[$1];
if (!filename)
{
	return "Varnote \"" + $1 + "\" not refreshed.  Does not exist.\n\n";
}
const file = app.vault.fileMap[filename];
if (!file)
{
	return "Varnote \"" + $1 + "\" not refreshed.  File \"" + filename + "\" not found.\n\n";
}
app.vault.cachedRead(file).then(r =>
{
	window._tejsVarnotes.data[$1] = {};
	for (let ri of r.matchAll(window._tejsVarnotes.regex_vars))
	{
		window._tejsVarnotes.data[$1][ri[1].toLowerCase()] = ri[2];
	}
});
return [ "Varnote \"", $1, "\" refreshed.\n\n" ];
```
~~
varnotes refresh {varnote name} - Pull the data from varnote {varnote name} (a required "name" parameter).
