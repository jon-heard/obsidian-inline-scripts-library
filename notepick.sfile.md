---
obsidianUIMode: preview
---

Shortcuts to pick X random notes from a folder of notes and to get their front matter data.

__
```
^sfile setup$
```
__
```js
const confirmObjectPath =
	_inlineScripts.inlineScripts.helperFncs.confirmObjectPath;

confirmObjectPath("_inlineScripts.notepick");
```
__


__
__
```js
function roll(max) { return Math.trunc(Math.random() * max + 1); }
function aPick(a) { return a[roll(a.length)-1]; }
```
__
Some useful functions


__
```
^notepick pickFromFolderAndGetFrontmatter ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ?([1-9][0-9]*|) ?(.*)$
```
__
```js
// Pick random items
let result = expand(
	"notepick pickFromFolder " +
	$1 + " " + $2 + " " + $3);
if (result[0])
{
	return null;
}

// Get the front matter for the items
result =
	expand("notepick frontmatter " + $3);
if (result[0])
{
	return null;
}
return result[2];
```
__
notepick pickFromFolderAndGetFrontmatter {folder name: path text} {pick count: >0, default: 1} {pick id: text, default: ""} - Combines the shortcuts "notepick pickFromFolder" and "notepick frontmatter".
***


__
```
^notepick pickFromFolder ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ?([1-9][0-9]*|) ?(.*)$
```
__
```js
$1 = $1.replaceAll(/^\"|\"$/g, "");
$2 = Number($2 || 1);

const folder = app.vault.fileMap[$1];
if (!folder || !folder.children)
{
	return [ "No notes picked." +
		"  The folder __" + $1 +
		"__ was not found, " +
		"or is not a folder.\n\n" ];
}

const files =
	folder.children
	.filter(v => v.extension == "md")
	.map(v => v.path);
if (files.length < $2)
{
	return [ "No notes picked." +
		"  The folder __" + $1 +
		"__ does not contain enough notes.\n\n" ];
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
_inlineScripts.notepick[$3] = pick;

return [ "",
	$2 + " note(s) picked" +
	($3 ? " for " + $3 : "") + ".\n\n" ];
```
__
notepick pickFromFolder {folder name: path text} {pick count: >0, default: 1} {pick id: text, default: ""} - Pick {pick count} random notes from folder {folder name} and remember them as {pick id}.


__
```
^notepick getPick ?(.*)$
```
__
```js
const picks = _inlineScripts.notepick[$1];
if (!picks)
{
	return [
		"No pick.  " +
		"Pick id __" + $1 + "__ not found." ];
}
return [
	"Pick" +
	($1 ? " __" + $1 + "__" : "") +
	":\n",
	picks.map(v => ". " + v + "\n").join(""),
	"\n" ];
```
__
notepick getPick {pick id: text, default: ""} - Get a list of the files last picked for {pick id}.


__
```
^notepick frontmatter ?(.*)$
```
__
```js
const pick = _inlineScripts.notepick[$1];
if (!pick)
{
	return [
		"No frontmatter gathered.  " +
		"Pick id __" + $1 + "__ not found." ];
}
const files = pick.map(v => app.vault.fileMap[v]);

let fileNotFound = null;
for (let i = 0; i < pick.length; i++)
{
	if (!files[i])
	{
		fileNotFound = pick[i];
	}
	break;
}
if (fileNotFound)
{
	return [
		"No frontmatter gathered.  " +
		"Note __" + fileNotFound +
		"__ not found." ];
}

let result = {};
for (const file of files)
{
	result[file.basename] =
		app.metadataCache.getFileCache(
			file).frontmatter;
}

return [ "", "Frontmatter gathered for " + pick.length + " note(s).\n", result, "\n\n" ];
```
__
notepick frontmatter {pick id} - Get the frontmatter from the notes that are remembered in {pick id}.
