---
obsidianUIMode: preview
---

Shortcuts to pick X random notes from a folder of notes and to get their front matter data.

This shortcut-file has a tutorial video available:
- [Using the "notepick" shortcut-file to randomly pick notes & get variables](https://www.youtube.com/watch?v=G1mvl-VwbIQ) (runtime 3:22)


__
```
^sfile setup$
```
__
```js
const confirmObjectPath =
	_inlineScripts.inlineScripts.helperFncs.confirmObjectPath;
confirmObjectPath("_inlineScripts.state.sessionState.notepick");
confirmObjectPath(
	"_inlineScripts.state.listeners.onReset.notepick",
	function()
	{
		expand("notepick reset");
	});
```
__
Sets up this shortcut-file


__
```
^sfile shutdown$
```
__
```js
delete _inlineScripts.state?.sessionState?.notepick;
delete _inlineScripts.state?.listeners?.onReset?.notepick;
```
__
Shuts down this shortcut-file


__
```
^notepick reset$
```
__
```js
const confirmObjectPath =
	_inlineScripts.inlineScripts.helperFncs.confirmObjectPath;
confirmObjectPath("_inlineScripts.state.sessionState");
_inlineScripts.state.sessionState.notepick = {};
return "All notepicks cleared.\n\n";
```
__
notepick reset - Clears all picks.
***


__
__
```js
function roll(max) { return Math.trunc(Math.random() * max + 1); }
function aPick(a) { return a[roll(a.length)-1]; }
```
__
Helper scripts


__
```
^notepick pickFromFolderAndGetPick ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ?([1-9][0-9]*|) ?([_a-zA-Z][_a-zA-Z0-9]*|) ?(.*)$
```
__
```js
$1 = $1.replaceAll(/^\"|\"$/g, "");
// Pick random items
let result = expand("notepick pickFromFolder " + $1 + " " + $2 + " " + $3 + " " + $4);
if (result[0])
{
	return result;
}

// Get the front matter for the items
return expand("notepick getPick " + $3);
```
__
notepick pickFromFolderAndGetPick {folder name: path text} {pick count: >0, default: 1} {pick id: text, default: ""} {to ignore: | separated filenames} - Combines the shortcuts "notepick pickFromFolder" and "notepick getPick".


__
```
^notepick pickFromFolderAndGetFrontmatter ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ?([1-9][0-9]*|) ?([_a-zA-Z][_a-zA-Z0-9]*|) ?(.*)$
```
__
```js
$1 = $1.replaceAll(/^\"|\"$/g, "");
// Pick random items
let result = expand("notepick pickFromFolder " + $1 + " " + $2 + " " + $3 + " " + $4);
if (result[0])
{
	return null;
}

// Get the front matter for the items
result = expand("notepick frontmatter " + $3);
if (result[0])
{
	return null;
}
return result[2];
```
__
notepick pickFromFolderAndGetFrontmatter {folder name: path text} {pick count: >0, default: 1} {pick id: text, default: ""} {to ignore: | separated filenames} - Combines the shortcuts "notepick pickFromFolder" and "notepick frontmatter".
***


__
```
^notepick pickFromFolder ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ?([1-9][0-9]*|) ?([_a-zA-Z][_a-zA-Z0-9]*|) ?(.*)$
```
__
```js
$1 = $1.replaceAll(/^\"|\"$/g, "");
$2 = Number($2 || 1);
$4 = $4.split("|");

const folder = app.vault.fileMap[$1];
if (!folder || !folder.children)
{
	return [
		"No files picked.  The folder __" + $1 + "__ was not found, " +
		"or is not a folder.\n\n" ];
}

let files = folder.children.filter(v => !v.children).map(v => v.path);
files = files.filter(v => !$4.includes(v));
if (files.length < $2)
{
	return [
		"No files picked.  The folder __" + $1 +
		"__ does not contain enough files.\n\n" ];
}

const pick = [];
while (pick.length < $2)
{
	const nextPickItem = aPick(files);
	if (!pick.includes(nextPickItem))
	{
		pick.push(nextPickItem);
	}
}
_inlineScripts.state.sessionState.notepick[$3] = pick;

return ["", $2 + " file(s) picked" + ($3 ? " for " + $3 : "") + ".\n\n" ];
```
__
notepick pickFromFolder {folder name: path text} {count: >0, default: 1} {pick id: name text, default: ""} {to ignore: | separated filenames} - Picks {count} random notes from folder {folder name} and remembers them as {pick id}.  Any files in {to ignore} are never picked.


__
```
^notepick getPick ?([_a-zA-Z][_a-zA-Z0-9]*|)$
```
__
```js
const picks = _inlineScripts.state.sessionState.notepick[$1];
if (!picks)
{
	return ["No pick.  Pick id __" + $1 + "__ not found." ];
}
return [
	"Pick" + ($1 ? " __" + $1 + "__" : "") + ":\n", picks.join("\n"), "\n\n" ];
```
__
notepick getPick {pick id: name text, default: ""} - Gets a list of the files last picked for {pick id}.


__
```
^notepick frontmatter ?([_a-zA-Z][_a-zA-Z0-9]*|)$
```
__
```js
const pick = _inlineScripts.state.sessionState.notepick[$1];
if (!pick)
{
	return [ "No frontmatter gathered.  Pick id __" + $1 + "__ not found." ];
}
const files = pick.map(v => app.vault.fileMap[v]);

let fileNotFound = null;
for (let i = 0; i < pick.length; i++)
{
	if (!files[i])
	{
		fileNotFound = pick[i];
		break;
	}
}
if (fileNotFound)
{
	return [ "No frontmatter gathered.  __" + fileNotFound + "__ not found." ];
}

let result = {};
for (const file of files)
{
	result[file.name.replace(/.md$/, "")] =
		app.metadataCache.getFileCache(file).frontmatter || {};
}

return [
	"", "Frontmatter gathered for " + pick.length + " note(s).\n", result,
	"\n\n" ];
```
__
notepick frontmatter {pick id: name text, default: ""} - Gets the frontmatter from the notes that are remembered in {pick id}.
