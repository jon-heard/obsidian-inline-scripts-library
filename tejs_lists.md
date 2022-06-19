Shortcuts for working with lists.

Uses __tejs_state__ shortcut-list (optional) to save & load this shortcut file's state.

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
result += "- __lists__ - Show all lists.\n";
result += "- __lists list {listName}__ - Show all items in the list {listName}.\n";
result += "- __lists add {listName} {item}__ - Add {item} to the basic-list {listName}.  Allows duplicate items.  Cannot add to folder-lists or combo-lists.\n";
result += "- __lists pick {listName}__ - Get a random item from the list {listName}.\n";
result += "- __lists remove {listName} {item}__ - Remove an instance of {item} from the basic-list {listName}.  Cannot remove from folder-lists or combo-lists.\n";
result += "- __lists removelist {listName}__ - Remove the entire list {listName}.\n";
result += "- __lists addfolder {listName} {folder}__ - Create a folder-list named {listName} that is linked to the folder {folder}.  A \"folder-list\" is a list who's items are the names of the files in the linked folder.\n";
result += "- __lists addcombo {listName} {subList1} {subList2}...__ - Create a combo-list named {listName} that is linked to the sublists given as {subList1}, {subList2}, etc.  A \"combo-list\" is a list who's items are all the items of its linked sublists.\n";
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
window._tejsState ||= {};
window._tejsState.lists = {};
return "All lists cleared.\n\n";
```


~~
```
^lists$
```
~~
```js
let listNames = Object.keys(window._tejsState.lists);
listNames.sort();
for (let i = 0; i < listNames.length; i++)
{
	let listType = window._tejsState.lists[listNames[i]].type;
	if (listType != "basic")
	{
		listNames[i] = listNames[i] + " _(" + listType + ")_";
	}
}
return [ "__Lists__\n", (listNames.length ? listNames.join(", ") : "none"), "\n\n" ];
```


~~
```
^lists removelist ([a-zA-Z]+)$
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
^lists add ([a-zA-Z]+) ([a-zA-Z ]+)$
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
^lists addfolder ([a-zA-Z]+) (.+)$
```
~~
```js
if (!$2.endsWith("/")) { $2 += "/"; }
window._tejsState.lists[$1] = { type: "folder", content: $2 };
return [ "List \"", $1, "\" added as a folder-list that is linked to the folder \"", $2, "\".\n\n" ];
```


~~
```
^lists addcombo ([a-zA-Z]+) ([a-zA-Z ]+)$
```
~~
```js
let links = $2.split(" ");
window._tejsState.lists[$1] = { type: "combo", content: links };
return [ "List \"", $1, "\" added as a combo-list linked to: ", links.join(", "), ".\n\n" ];
```


~~
```
^lists remove ([a-zA-Z]+) ([a-zA-Z ]+)$
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
function getListItems(name)
{
	let list = window._tejsState.lists[name];
	if (!list) { return []; }
	let result = [];
	switch (list.type)
	{
		case "basic":
			return list.content;
			break;
		case "folder":
			for (let key in app.fileManager.vault.fileMap)
			{
				if (key.startsWith(list.content))
				{
					key = key.substr(list.content.length);
					if (key.contains("/")) { continue; }
					if (!key.endsWith(".md")) { continue; }
					result.push("[[" + key.substring(0, key.length-3) + "]]");
				}
			}
			break;
		case "combo":
			for (let i = 0; i < list.content.length; i++)
			{
				result = result.concat(getListItems(list.content[i]));
			}
			break;
	}
	return result;
}
```


~~
```
^lists list ([a-zA-Z]+)$
```
~~
```js
let items = getListItems($1);
items = (items && items.length ? items.join(", ") : "none");
return [ "__List \"", $1, "\"__\n", items, "\n\n" ];
```


~~
```
^lists pick ([a-zA-Z]*)$
```
~~
```js
function roll(max) { return Math.trunc(Math.random() * max + 1); }
let items = getListItems($1);
if (items && items.length)
{
	let result = items[roll(items.length)-1];
	return [ "\"", result, "\" picked from list \"", $1, "\".\n\n" ];
}
return [ "Failed to pick from list \"", $1, "\".  List is empty.\n\n" ];
```
