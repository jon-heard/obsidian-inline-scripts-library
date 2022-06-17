Shortcuts for working with lists.

Enter the shortcut "help lists" for reference.


~~
```
^help lists$
```
~~
```js
let result = "### LISTS SHORTCUTS HELP\n";
result += "- __help lists__ - Display this help text.\n";
result += "- __reset lists__ - Clear all lists.\n";
result += "***\n";
result += "- __list__ - Show all lists.\n";
result += "- __list {listName}__ - Show all items in the list {listName}.\n";
result += "- __listadd {listName} {item}__ - Add {item} to the end of the list {listName}.\n";
result += "- __listaddfolder {listName} {folder}__ - Create a folder-list named {listName} that is linked to the folder {folder}.  A \"folder-list\" is a list who's items are the names of the files in the linked folder.  A folder-list cannot be added to or removed from through the list shortcuts.\n";
result += "- __listget {listName}__ - Get a random item from the list named {listName}.\n";
result += "- __listremove {listName} {item}__ - Remove last instance of {item} from the list {listName}.\n";
result += "- __listremoveall {listName}__ - Remove the entire list of {listName}.\n";
return result + "\n";
```


~~
```
^tejs setup$
```
~~
```js
window._tejsState ||= {};
window._tejsState.lists ||= {};
```


~~
```
^reset lists$
```
~~
```js
window._tejsState.lists = {};
return "All lists cleared.\n\n";
```

~~
```
^list$
```
~~
```js
let listNames = Object.keys(window._tejsState.lists);
return [ "__Lists__\n", (listNames.length ? listNames.join(", ") : "none"), "\n\n" ];
```


~~
```
^listremoveall ([a-zA-Z]+)$
```
~~
```js
if (window._tejsState.lists[$1])
{
	delete window._tejsState.lists[$1];
	return [ "List \"", $1, "\" removed.\n\n" ];
}
return [ "Failed to remove list \"", $1, "\".  List does not exist.\n\n" ];
```


~~
```
^listadd ([a-zA-Z]+) ([a-zA-Z ]+)$
```
~~
```js
window._tejsState.lists[$1] ||= { type: "basic", content: [] };
if (window._tejsState.lists[$1].type != "basic")
{
	return [ "Failed to add to list \"", $1, "\".  Not a basic list.\n\n" ];
}
window._tejsState.lists[$1].content.push($2);
window._tejsState.lists[$1].content.sort();
return ["\"", $2, "\" added to list \"", $1, "\".\n\n" ];
```

~~
```
^listaddfolder ([a-zA-Z]+) (.+)$
```
~~
```js
if (!$2.endsWith("/")) { $2 += "/"; }
window._tejsState.lists[$1] = { type: "folder", content: $2 };
return [ "List \"", $1, "\" added as a folder-list that is linked to the folder \"", $2, "\".\n\n" ];
```


~~
```
^listremove ([a-zA-Z]+) ([a-zA-Z ]+)$
```
~~
```js
if (window._tejsState.lists[$1])
{
	if (window._tejsState.lists[$1].type != "basic")
	{
		return [ "Failed to remove \"", $2, "\" from list \"", $1, "\".  Not a basic list.\n\n" ];
	}
	let i = window._tejsState.lists[$1].content.lastIndexOf($2);
	if (i >= 0)
	{
	    window._tejsState.lists[$1].content.splice(i, 1);
	    return [ "\"", $2, "\" removed from list \"", $1, "\".\n\n" ];
	}
}
return [ "Failed to remove \"", $2, "\" from list \"", $1, "\".  Not found in list.\n\n" ];
```

~~
~~
```js
function getListContent(name)
{
	if (!window._tejsState.lists[name]) { return []; }
	switch (window._tejsState.lists[name].type)
	{
		case "basic":
			return window._tejsState.lists[name].content;
			break;
		case "folder":
			let folder = window._tejsState.lists[name].content;
			let result = [];
			for (let key in app.fileManager.vault.fileMap)
			{
				if (key.startsWith(folder))
				{
					key = key.substr(folder.length);
					if (key.contains("/")) { continue; }
					if (!key.endsWith(".md")) { continue; }
					result.push("[[" + key.substring(0, key.length-3) + "]]");
				}
			}
			return result;
			break;
		default:
			return [];
			break;
	}
}
```

~~
```
^list ([a-zA-Z]+)$
```
~~
```js
let content = getListContent($1);
content = (content && content.length ? content.join(", ") : "none");
return [ "__List \"", $1, "\"__\n", content, "\n\n" ];
```


~~
```
^listget ([a-zA-Z]*)$
```
~~
```js
function roll(max) { return Math.trunc(Math.random() * max + 1); }
let content = getListContent($1);
if (content && content.length)
{
	let result = content[roll(content.length)-1];
	return [ "\"", result, "\" picked from list \"", $1, "\".\n\n" ];
}
return [ "Failed to pick from list \"", $1, "\".  List is empty.\n\n" ];
```
