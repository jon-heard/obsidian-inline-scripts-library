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
^list ([a-zA-Z]+)$
```
~~
```js
let list = window._tejsState.lists[$1];
return [ "__List \"", $1, "\"__\n", (list && list.length ? list.join(", ") : "none"), "\n\n" ];
```


~~
```
^listadd ([a-zA-Z]+) ([a-zA-Z ]+)$
```
~~
```js
window._tejsState.lists[$1] ||= [];
window._tejsState.lists[$1].push($2);
return ["\"", $2, "\" added to list \"", $1, "\".\n\n" ];
```


~~
```
^listget ([a-zA-Z]*)$
```
~~
```js
function roll(max) { return Math.trunc(Math.random() * max + 1); }
let list = window._tejsState.lists[$1];
if (list && list.length)
{
	let result = list[roll(list.length)-1];
	return [ "\"", result, "\" picked from list \"", $1, "\".\n\n" ];
}
return [ "Failed to pick from list \"", $1, "\".  List is empty.\n\n" ];
```


~~
```
^listremove ([a-zA-Z]+) ([a-zA-Z ]+)$
```
~~
```js
if (window._tejsState.lists[$1])
{
	let i = window._tejsState.lists[$1].lastIndexOf($2);
	if (i >= 0)
	{
	    window._tejsState.lists[$1].splice(i, 1);
	    return [ "\"", $2, "\" removed from list \"", $1, "\".\n\n" ];
	}
}
return [ "Failed to remove \"", $2, "\" from list \"", $1, "\".  Not found in list.\n\n" ];
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
