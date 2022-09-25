Shortcuts for working with files in the vault.


__
```
^files extensionchange ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ?([^ \t\\:*?"<>|]+|)$
```
__
```js
$2 ||= "md";
if (!app.vault.fileMap[$1])
{
	return "Extension not changed.  File __" + $1 + "__ not found.\n\n";
}
let extensionIndex = $1.lastIndexOf(".");
if (extensionIndex == -1) { extensionIndex = $1.length; }
const newName = $1.slice(0, extensionIndex) + "." + $2;
app.vault.adapter.rename($1, newName);
return "File __" + $1 + "__ is now __" + newName + "__.\n\n";
```
__
files extensionchange {file name: path text} {extension: nospace text, default: md} - Changes the extension of file {file name} to {extension}.


__
```
^files? shortcutbatch ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) -- (.*)$
```
__
```js
const folder = app.vault.fileMap[$1];
if (!folder)
{
	return "Extension not changed.  Folder __" + $1 + "__ not found.\n\n";
}
if (!folder.children)
{
	return "Extension not changed.  __" + $1 + "__ is not a folder.\n\n";
}
// Find unused list name
let tmpList = 0;
while (_inlineScripts.state.sessionState.lists["L" + tmpList])
{
	tmpList++;
}
tmpList = "L" + tmpList;
// Fill unused list name with file paths from the given folder
for (const child of folder.children)
{
	expand("lists add " + tmpList + " " + child.path);
}
// Run shortcutbatch on the list, then convert the output text to say "folder"
let result = expand("lists shortcutbatch " + tmpList + " -- " + $2);
result = result.replace("for list __" + tmpList, "for folder __" + $1);
// Remove the list now that we're done with it
expand("lists removelist l" + tmpList);
// Return the result
return result;
```
__
files shortcutbatch {folder name: path text} -- {shortcut: text} - Runs shortcut {shortcut} once for each file in folder {folder name}, replacing "%1" in {shortcut} with the file's name.
