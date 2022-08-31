---
obsidianUIMode: preview
---

This shortcut-file includes shortcuts to get and set note-variables.  Note-variables are variables set in the YAML frontmatter.  They can be used by many plugins, including DataView and TTRPG StatBlocks.

This shortcut file can be used by other shortcut-files to let them read and manipulate data in notes for many uses, including TTRPG character sheets.

Uses __state.sfile__ shortcut-file (optional).
It uses this to save & load the isMarkdownRefreshed flag.


__
```
^sfile setup$
```
__
```js
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

confirmObjPath(
	"_inlineScripts.state.notevars.isMarkdownRefreshed",
	true);
```
__
Sets up the notevars sate (just the "isMarkdownRefreshed" flag).


__
```
^notevars isMarkdownRefreshed ?(|[y|n])$
```
__
```js
if ($1)
{
	_inlineScripts.state.notevars.isMarkdownRefreshed =
		($1 === "y");
}
return "notevars isMarkdownRefreshed is " +
	(_inlineScripts.state.notevars.isMarkdownRefreshed ?
	"__enabled__" : "__disabled__") + ".\n\n";
```
__
notevars isMarkdownRefreshed {state: y OR n, default: ""} - If {state} is given, assigns it to the notevars "isMarkdownRefreshed" flag.  Otherwise, displays the current "isMarkdownRefreshed" flag.
If isMarkdownRefreshed flag is set then a note's markdown is refreshed each time one of it's variables is set.
***


__
```
^notevars get (\.|[_a-zA-Z][_a-zA-Z0-9/\.]*) ([_a-zA-Z][_a-zA-Z0-9]*)
```
__
```js
if ($1 === ".") { $1 = app.workspace.getActiveFile()?.basename; }
const file = $1 ? app.vault.fileMap[$1 + ".md"] : null;
if (!file)
{
	return [ "", "No value.  Note __" + $1 + "__ not found.\n\n" ];
}
const cache = app.metadataCache.getFileCache(file);
if (!cache)
{
	return [ "", "No value.  Note __" + $1 + "__ is not properly cached by Obsidian.\n\n" ];
}
const fm = cache.frontmatter;
if (!fm)
{
	return null;
}
const result =
	fm[$2] || fm[$2 + ":"] || null;
return Array.isArray(result) ? result.join(",") : result;
```
__
notevars get {note name: path text} {variable name: name text} - Expands to the value of variable {variable name} in note {note name}.  If {note name} is "." then it represents the current note.


__
```
^notevars getArray (\.|[_a-zA-Z][_a-zA-Z0-9/\.]*) ([_a-zA-Z][_a-zA-Z0-9]*) ([0-9]+)$
```
__
```js
if ($1 === ".") { $1 = app.workspace.getActiveFile()?.basename; }
const file = $1 ? app.vault.fileMap[$1 + ".md"] : null;
if (!file)
{
	return [ "", "No value.  Note __" + $1 + "__ not found.\n\n" ];
}
const cache = app.metadataCache.getFileCache(file);
if (!cache)
{
	return [ "", "No value.  Note __" + $1 + "__ is not properly cached by Obsidian.\n\n" ];
}
const fm = cache.frontmatter;
if (!fm)
{
	return null;
}
const result =
	fm[$2] || fm[$2 + ":"] || null;
return Array.isArray(result) ? result[$3] : ($3 === "0") ? result : null;
```
__
notevars getArray {note name: path text} {array name: name text} {index: >=0} - Expands to the value of item {index} of array {array name} in note {note name}.  If {note name} is "." then it represents the current note.


__
__
```js
const REFRESH_MARKDOWN_DELAY = 500;
function refreshPreviewOnNextModify(file)
{
	if (!_inlineScripts.state.notevars.isMarkdownRefreshed)
	{
		return;
	}

	// Find the view for the given file
	let view = null;
	for (const leaf of app.workspace.getLeavesOfType("markdown"))
	{
		if (leaf.view.file === file)
		{
			view = leaf.view;
			break;
		}
	}
	if (!view) { return; }
	const onChanged = (changedFile) =>
	{
		if (changedFile === file)
		{
			app.metadataCache.off("changed", onChanged);
			setTimeout(() =>
				view.modes.preview.rerender(true), 0);
		}
	}
	app.metadataCache.on("changed", onChanged);
}
```
__
Allow refreshing a  file's markdown


__
```
^notevars set (\.|[_a-zA-Z][_a-zA-Z0-9/\.]*) ([_a-zA-Z][_a-zA-Z0-9]*) (.*)$
```
__
```js
if ($1 === ".") { $1 = app.workspace.getActiveFile()?.basename; }
const file = $1 ? app.vault.fileMap[$1 + ".md"] : null;
if (!file)
{
	return [ "",
		"Variable __" + $2 +
		"__ not set.  Note __" +
		$1 + "__ not found.\n\n" ];
}
const content = await app.vault.cachedRead(file);
if (content === null || content === undefined)
{
	return [ "",
		"Variable __" + $2 +
		"__ not set.  Error reading content of note __" +
		$1 + "__.\n\n" ];
}
$3 = $3
	.replaceAll("\\n", "\n")
	.replaceAll("\\t", "\t");
const fmMatch = content.match(/^(\n*---)(\n[\S\s]*\n)(\n*---\n)/);
let result = content;
if (!fmMatch)
{
	result = "---\n" + $2 + ": " + $3 + "\n---\n\n" + content;
}
else
{
	const varMatch =
		fmMatch[2].match("(\n" + $2 + ":)[^\n]*\n");
	if (varMatch)
	{
		let valueEndIndex =
			varMatch.index + varMatch[0].length;
		while(fmMatch[2][valueEndIndex] === "-" ||
		      fmMatch[2][valueEndIndex] === " ")
		{
			valueEndIndex =
				fmMatch[2].indexOf("\n", valueEndIndex)
				+ 1;
		}
		result =
			fmMatch[1] +
			fmMatch[2].slice(0, varMatch.index) +
			varMatch[1] + " " + $3 + "\n" +
			fmMatch[2].slice(valueEndIndex) +
			fmMatch[3] +
			content.slice(fmMatch[0].length);
	}
	else
	{
		result =
			fmMatch[1] +
			fmMatch[2] +
			$2 + ": " + $3 + "\n" +
			fmMatch[3] +
			content.slice(fmMatch[0].length);
	}
}
refreshPreviewOnNextModify(file);
app.vault.modify(file, result);
return "Note __" +
	$1 + "__, variable __" + $2 +
	"__ set to __" + $3 + "__.\n\n";
```
__
notevars set {note name: path text} {variable name: name text} {value: text} - Sets the value of variable {variable name} to {value} in note {note name}.  If {note name} is "." then it represents the current note.


__
```
^notevars setArray (\.|[_a-zA-Z][_a-zA-Z0-9/\.]*) ([_a-zA-Z][_a-zA-Z0-9]*) (.*)$
```
__
```js
if ($1 === ".") { $1 = app.workspace.getActiveFile()?.basename; }
const file = $1 ? app.vault.fileMap[$1 + ".md"] : null;
if (!file)
{
	return [ "",
		"Variable __" + $2 +
		"__ not set.  Note __" +
		$1 + "__ not found.\n\n" ];
}
const content = await app.vault.cachedRead(file);
if (content === null || content === undefined)
{
	return [ "",
		"Variable __" + $2 +
		"__ not set.  Error reading content of note __" +
		$1 + "__.\n\n" ];
}
$3 = $3
	.replaceAll("\\n", "\n")
	.replaceAll("\\t", "\t")
	.split(",")
	.filter(v => v);
const fmMatch = content.match(/^(\n*---)(\n[\S\s]*\n)(\n*---\n)/);
let result;
if (!fmMatch)
{
	result = "---\n" + $2 + ":\n";
	for (let i = 0; i < $3.length; i++)
	{
		result += "- " + $3[i] + "\n";
	}
	result += "---\n\n" + content;
}
else
{
	const varMatch =
		fmMatch[2].match("(\n" + $2 + ":)[^\n]*\n");
	if (varMatch)
	{
		let valueEndIndex =
			varMatch.index + varMatch[0].length;
		while(fmMatch[2][valueEndIndex] === "-" ||
		      fmMatch[2][valueEndIndex] === " ")
		{
			valueEndIndex =
				fmMatch[2].indexOf("\n", valueEndIndex)
				+ 1;
		}
		result =
			fmMatch[1] +
			fmMatch[2].slice(0, varMatch.index) +
			varMatch[1] + "\n";
		for (let i = 0; i < $3.length; i++)
		{
			result += "- " + $3[i] + "\n";
		}
		result +=
			fmMatch[2].slice(valueEndIndex) +
			fmMatch[3] +
			content.slice(fmMatch[0].length);
	}
	else
	{
		result =
			fmMatch[1] +
			fmMatch[2] +
			$2 + ":\n";
		for (let i = 0; i < $3.length; i++)
		{
			result += "- " + $3[i] + "\n";
		}
		result +=
			fmMatch[3] +
			content.slice(fmMatch[0].length);
	}
}
refreshPreviewOnNextModify(file);
app.vault.modify(file, result);
return "Note __" +
	$1 + "__, variable __" + $2 +
	"__ set to __" + $3 + "__.\n\n";
```
__
notevars setArray {note name: path text} {array name: name text} {value1: text},{value2: text},... - Sets the values of array {array name} to {value1}, {value2}, etc. in note {note name}.  If {note name} is "." then it represents the current note.
