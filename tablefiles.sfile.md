Shortcut file to load tables from files and roll them.


__
```
^sfile setup$
```
__
```js
const confirmObjectPath =
	_inlineScripts.inlineScripts.helperFncs.confirmObjectPath;
confirmObjectPath("_inlineScripts.state.sessionState.tablefiles.paths");
confirmObjectPath("_inlineScripts.state.sessionState.tablefiles.settings");
confirmObjectPath(
	"_inlineScripts.state.listeners.onReset.tablefiles",
	function()
	{
		expand("tbl reset");
	});
confirmObjectPath("_inlineScripts.tablefiles.priorRoll");
_inlineScripts.inlineScripts.helperFncs.addCss("tableFiles", ".iscript_popupLabel { margin-right: .25em; white-space: nowrap; } .iscript_nextPopupLabel { margin-left: 1.5em } .iscript_popupRow { width: 100%; margin-bottom: 1em; max-width: 39em; } .iscript_smallButton { padding: 0.5em 0.5em; margin: 0 } .iscript_smallButtonDisabled { color: grey; cursor: unset }");


///////////////////////////////////////////////////////////////////////////////////

function roll(max) { return Math.trunc(Math.random() * max + 1); }
function aPick(a) { return a[roll(a.length)-1]; }
function aPickWeight(a, wIndex, theRoll)
{
	wIndex = wIndex || 1;
	theRoll = theRoll || roll(a.last()[wIndex]);
	for (const item of a)
	{
		if (item[wIndex] >= theRoll)
		{
			return item;
		}
	}
	return a.last();
}
function getTablePaths()
{
	let result = [];
	const paths = _inlineScripts.state.sessionState.tablefiles.paths;
	for (const path in paths)
	{
		const file = app.vault.fileMap[path];
		if (!file) { continue; }
		if (!file.children)
		{
			result.push(file.path);
		}
		else
		{
			for (const child of file.children)
			{
				if (child.children) { continue; }
				result.push(child.path);
			}
		}
	}
	return result;
}
function makeUiRow(contents)
{
	const tbl = document.createElement("table");
		tbl.classList.add("iscript_popupRow");
	const tr = document.createElement("tr");
		tbl.append(tr);
	for (const content of contents)
	{
		const td = document.createElement("td");
		td.append(content);
		tr.append(td);
	}
	return tbl;
}
async function getCurrentTableItems(data, offset)
{
	offset ||= data.current.settings.offset || 0;
	const file = app.vault.fileMap[data.current.path];
	if (!file) { return []; }
	const content = await app.vault.cachedRead(file);
	return content.split("\n").slice(offset).filter(v => v);
}
async function updateTableConfig(data)
{
	data.current = {};
	data.current.path = data.selectUi.value;
	data.current.settings =
		_inlineScripts.state.sessionState.tablefiles.settings[data.current.path];
	data.configUi.path.value = data.current.path;
	data.configUi.title.value = data.current.settings?.title || "";
	data.configUi.description.value = data.current.settings?.description || "";
	data.configUi.tags.value = data.current.settings?.tags || "";
	const tblLines = await getCurrentTableItems(data);
	data.configUi.startLine.value = tblLines[0];
	data.configUi.startLineUp.toggleClass(
		"iscript_smallButtonDisabled", !(data.current?.settings?.offset));
	data.configUi.startLineDown.toggleClass(
		"iscript_smallButtonDisabled", tblLines.length <= 1);
}
function refreshSelectUi(data)
{
	let titleWidth = 0;
	let optionData = [];

	// Figure out title and description for each option
	for (const option of data.selectUi.options)
	{
		let optionDatum =
		{
			ui: option, path: option.value, title: "", description: "",
			filterKeys: []
		};
		const config =
			_inlineScripts.state.sessionState.
			tablefiles.settings[optionDatum.path];
		if (config)
		{
			optionDatum.title = config.title || "";
			optionDatum.description = config.description || "";
			optionDatum.filterKeys =
				(config.tags || "").split(" ").filter(v => v).
				map(v => v.toLowerCase());
		}
		if (!optionDatum.title)
		{
			const p = optionDatum.path;
			optionDatum.title = p.slice(p.lastIndexOf("/")+1, p.lastIndexOf("."));
		}
		titleWidth = Math.max(titleWidth, optionDatum.title.length);
		optionDatum.filterKeys.push(optionDatum.title.toLowerCase());
		optionDatum.filterKeys.push(optionDatum.path.toLowerCase());
		optionData.push(optionDatum);
	}

	for (const optionDatum of optionData)
	{
		optionDatum.ui.innerHTML =
			optionDatum.title.padEnd(titleWidth + 4, "~").replaceAll("~", "&nbsp;")
			+ optionDatum.description;
	}

	// Sort by title
	optionData.sort((lhs, rhs) =>
	{
		return lhs.title.localeCompare(rhs.title);
	});

	// Re-add options with new sort order
	const oldValue = data.selectUi.value;

	// readd options sorted
	while (data.selectUi.options.length)
	{
		data.selectUi.remove(0);
	}
	for (let i = 0; i < optionData.length; i++)
	{
		data.selectUi.options[i] = optionData[i].ui;
	}

	// Prepare filter blocks
	data.filterKeys = {};
	for (const optionDatum of optionData)
	{
		for (const filterKey of optionDatum.filterKeys)
		{
			if (data.filterKeys[filterKey])
			{
				data.filterKeys[filterKey].push(optionDatum.path);
			}
			else
			{
				data.filterKeys[filterKey] = [ optionDatum.path ];
			}
		}
	}
}
function updateFilter(data)
{
	let unfiltered;
	let filters = data.filterUi.value;
	if (filters)
	{
		filters = filters.toLowerCase().split(" ").filter(v => v);
		for (const filter of filters)
		{
			let filterResult = {};
			for (const key in data.filterKeys)
			{
				if (key.includes(filter))
				{
					let matchResult = {};
					data.filterKeys[key].forEach(key => matchResult[key] = 1);
					Object.assign(filterResult, matchResult);
				}
			}
			if (!unfiltered)
			{
				unfiltered = filterResult;
			}
			else
			{
				const keys = Object.keys(unfiltered)
				for (const key of keys)
				{
					if (!filterResult[key])
					{
						delete unfiltered[key];
					}
				}
			}
		}
	}
	for (const option of data.selectUi.options)
	{
		if (!unfiltered || unfiltered[option.value])
		{
			option.style.display = "";
		}
		else
		{
			option.style.display = "none";
		}
	}
}

///////////////////////////////////////////////////////////////////////////////////

//confirmObjectPath("_inlineScripts.tablefiles.rollPopup",
_inlineScripts.tablefiles.rollPopup =
{
	buttonIds: [ "Roll", "Cancel" ],
	onOpen: async (data, parent, firstButton, SettingType, resolveFnc) =>
	{
		const paths = getTablePaths();
		if (paths.length === 0)
		{
			resolveFnc("No table rolled.  No tables available.\n\n");
			return true;
		}

		/////////////////
		// Row 1 of UI //
		/////////////////
		let uiRow = [ 0, 1 ];
		uiRow[0] = document.createElement("div");
			uiRow[0].innerText = "Filter";
			uiRow[0].classList.add("iscript_popupLabel");
		uiRow[1] = document.createElement("input");
			data.filterUi = uiRow[1];
			uiRow[1].type = "text";
			uiRow[1].setAttr("placeholder", "Space-separated filter texts");
			uiRow[1].addEventListener("keypress", e =>
			{
				if (e.key === "Enter") { firstButton.click(); }
			});
			uiRow[1].addEventListener("keyup", e =>
			{
				updateFilter(data);
			});
		let tbl = makeUiRow(uiRow);
			tbl.style["margin-bottom"] = 0;
			tbl.childNodes[0].childNodes[0].style.width = "0%";
			parent.append(tbl);

		/////////////////
		// Row 2 of UI //
		/////////////////
		const selectUi = document.createElement("select");
			data.selectUi = selectUi;
			// Find longest title
			for (const path of paths)
			{
				const label = path;
				const value = path;
				const isSelected =
					(value === _inlineScripts.tablefiles.priorRoll.table);
				selectUi.add(new Option(label, value, false, isSelected));
			}
			selectUi.setAttr("size", 10);
			selectUi.classList.add("dropdown");
			selectUi.classList.add("iscript_listSelect");
			selectUi.style["font-family"] = "monospace";
			selectUi.addEventListener("keypress", e =>
			{
				if (e.key === "Enter") { firstButton.click(); }
			});
			selectUi.addEventListener("change", e =>
			{
				updateTableConfig(data);
			});
			refreshSelectUi(data);
			parent.append(selectUi);

		/////////////////
		// Row 3 of ui //
		/////////////////
		uiRow = [ 0, 1, 2, 3, 4, 5 ];
		uiRow[0] = document.createElement("div");
			uiRow[0].innerText = "Pick count";
			uiRow[0].classList.add("iscript_popupLabel");
		uiRow[1] = document.createElement("input");
			data.countUi = uiRow[1];
			uiRow[1].type = "text";
			uiRow[1].setAttr("placeholder", 1);
			uiRow[1].style.width = "4em";
			uiRow[1].setAttr("maxlength", 4);
			uiRow[1].value = _inlineScripts.tablefiles.priorRoll.count || "";
			uiRow[1].addEventListener("keypress", e =>
			{
				if (e.key === "Enter") { firstButton.click(); }
				else if (e.keyCode < 48 || e.keyCode > 57)
				{
					window.event.returnValue = null;
				}
			});
		uiRow[2] = document.createElement("div");
			uiRow[2].innerText = "Pick format";
			uiRow[2].classList.add("iscript_popupLabel");
			uiRow[2].classList.add("iscript_nextPopupLabel");
		uiRow[3] = document.createElement("select");
			data.formatUi = uiRow[3];
			uiRow[3].classList.add("dropdown");
			uiRow[3].options[0] = new Option("Comma separated");
			uiRow[3].options[1] = new Option("Bulleted list");
			uiRow[3].options[2] = new Option("Perioded list");
			uiRow[3].value =
				_inlineScripts.tablefiles.priorRoll.format || "Comma separated";
			uiRow[3].addEventListener("keypress", e =>
			{
				if (e.key === "Enter") { firstButton.click(); }
			});
		uiRow[4] = document.createElement("div");
			uiRow[4].innerText = "Unique picks";
			uiRow[4].classList.add("iscript_popupLabel");
			uiRow[4].classList.add("iscript_nextPopupLabel");
		uiRow[5] = document.createElement("div");
			data.uniqueUi = uiRow[5];
			uiRow[5].classList.add("checkbox-container");
			uiRow[5].toggleClass(
				"is-enabled", _inlineScripts.tablefiles.priorRoll.unique);
			uiRow[5].addEventListener("click", e =>
			{
				e.target.toggleClass(
					"is-enabled",
					!e.target.classList.contains("is-enabled"));
			});
		tbl = makeUiRow(uiRow);
			parent.append(tbl);

		/////////////////
		// Config rows //
		/////////////////
		data.configUi = {};
		const configUi = document.createElement("div");
			data.configUi.root = configUi;
			configUi.style.display = "none";
			configUi.append(document.createElement("hr"));
			parent.append(configUi);
		const cfgBtnText = "Table configuration &nbsp; ";
		let cfgBtn = document.createElement("button");
			cfgBtn.innerHTML = cfgBtnText + "▼";
		    cfgBtn.style["margin-left"] = "0em";
    	    cfgBtn.style["margin-right"] = "0";
    	    cfgBtn.style["font-size"] = "15px";
    	    cfgBtn.style["padding"] = "6px 20px";
			cfgBtn.addEventListener("click", async () =>
			{
				const show = data.configUi.root.style.display === "none";
				data.configUi.root.style.display = show ? "block" : "none";
				cfgBtn.innerHTML = cfgBtnText + (show ? "▲" : "▼");
			});
			parent.nextSibling.prepend(cfgBtn);

		//////////////////////////
		// Row 4 of ui (config) //
		//////////////////////////
		uiRow = [ 0, 1 ];		
		uiRow[0] = document.createElement("div");
			uiRow[0].innerText = "Path";
			uiRow[0].classList.add("iscript_popupLabel");
		uiRow[1] = document.createElement("input");
			data.configUi.path = uiRow[1];
			uiRow[1].type = "text";
			uiRow[1].setAttr("readonly", true);
			uiRow[1].style["background-color"] = "var(--background-secondary)";
		tbl = makeUiRow(uiRow);
			tbl.childNodes[0].childNodes[0].style.width = "0%";
			configUi.append(tbl);

		//////////////////////////
		// Row 5 of ui (config) //
		//////////////////////////
		uiRow = [ 0, 1, 2, 3 ];
		uiRow[0] = document.createElement("div");
			uiRow[0].innerText = "Title";
			uiRow[0].classList.add("iscript_popupLabel");
		uiRow[1] = document.createElement("input");
			data.configUi.title = uiRow[1];
			uiRow[1].type = "text";
			uiRow[1].setAttr("placeholder", "Display");
			uiRow[1].style.width = "7em";
			uiRow[1].addEventListener("change", e =>
			{
				if (!data.current.settings)
				{
					data.current.settings =
						_inlineScripts.state.sessionState.tablefiles.
						settings[data.current.path] = {};
				}
				data.current.settings.title = e.target.value;
				refreshSelectUi(data);
			});
		uiRow[2] = document.createElement("div");
			uiRow[2].innerText = "Description";
			uiRow[2].classList.add("iscript_popupLabel");
			uiRow[2].classList.add("iscript_nextPopupLabel");
		uiRow[3] = document.createElement("input");
			data.configUi.description = uiRow[3];
			uiRow[3].type = "text";
			uiRow[3].addEventListener("change", e =>
			{
				if (!data.current.settings)
				{
					data.current.settings =
						_inlineScripts.state.sessionState.tablefiles.
						settings[data.current.path] = {};
				}
				data.current.settings.description = e.target.value;
				refreshSelectUi(data);
			});
		tbl = makeUiRow(uiRow);
			tbl.childNodes[0].childNodes[3].style.width = "100%";
			configUi.append(tbl);

		//////////////////////////
		// Row 6 of ui (config) //
		//////////////////////////
		uiRow = [ 0, 1, 2, 3, 4, 5 ];
		uiRow[0] = document.createElement("div");
			uiRow[0].innerText = "Filter-tags";
			uiRow[0].classList.add("iscript_popupLabel");
		uiRow[1] = document.createElement("input");
			data.configUi.tags = uiRow[1];
			uiRow[1].type = "text";
			uiRow[1].setAttr("placeholder", "Space-separated");
			uiRow[1].style.width = "10em";
			uiRow[1].addEventListener("change", e =>
			{
				if (!data.current.settings)
				{
					data.current.settings =
						_inlineScripts.state.sessionState.tablefiles.
						settings[data.current.path] = {};
				}
				data.current.settings.tags = e.target.value;
				refreshSelectUi(data);
				if (data.filterUi.value)
				{
					updateFilter(data);
				}
			});
		uiRow[2] = document.createElement("div");
			uiRow[2].innerText = "Table start line";
			uiRow[2].classList.add("iscript_popupLabel");
			uiRow[2].classList.add("iscript_nextPopupLabel");
		uiRow[3] = document.createElement("input");
			data.configUi.startLine = uiRow[3];
			uiRow[3].type = "text";
			uiRow[3].setAttr("readonly", true);
			uiRow[3].style["background-color"] = "var(--background-secondary)";
		uiRow[4] = document.createElement("button");
			data.configUi.startLineUp = uiRow[4];
			uiRow[4].innerText = "▲";
			uiRow[4].classList.add("iscript_smallButton");
			uiRow[4].classList.add("iscript_smallButtonDisabled");
			uiRow[4].addEventListener("click", async e =>
			{
				if (e.target.classList.contains("iscript_smallButtonDisabled"))
				{
					return;
				}
				if (!data.current.settings)
				{
					data.current.settings =
						 _inlineScripts.state.sessionState.tablefiles.
						 settings[data.current.path] = {};
				}
				if (!data.current.settings.offset)
				{
					data.current.settings.offset = 0;
				}
				data.current.settings.offset--;
				const tblLines =
					await getCurrentTableItems(data);
				data.configUi.startLine.value = tblLines[0];
				e.target.toggleClass(
					"iscript_smallButtonDisabled",
					data.current.settings.offset === 0);
				data.configUi.startLineDown.toggleClass(
					"iscript_smallButtonDisabled", false);
			});
		uiRow[5] = document.createElement("button");
			data.configUi.startLineDown = uiRow[5];
			uiRow[5].innerText = "▼";
			uiRow[5].classList.add("iscript_smallButton");
			uiRow[5].addEventListener("click", async e =>
			{
				if (e.target.classList.contains("iscript_smallButtonDisabled"))
				{
					return;
				}
				if (!data.current.settings)
				{
					data.current.settings =
						_inlineScripts.state.sessionState.tablefiles.
						settings[data.current.path] = {};
				}
				if (!data.current.settings.offset)
				{
					data.current.settings.offset = 0;
				}
				data.current.settings.offset++;
				const tblLines =
					await getCurrentTableItems(data, data.current.settings.offset);
				data.configUi.startLine.value = tblLines[0];
				e.target.toggleClass(
					"iscript_smallButtonDisabled", tblLines.length <= 1);
				data.configUi.startLineUp.toggleClass(
					"iscript_smallButtonDisabled", false);
			});
		tbl = makeUiRow(uiRow);
			configUi.append(tbl);

		updateTableConfig(data);
	},
	onClose: async (data, resolveFnc, buttonId) =>
	{
		if (buttonId !== "Roll") { return null; }
		const selected = data.selectUi.value;
		let count = Number(data.countUi.value) || 1;
		const format = data.formatUi.value;
		const unique = data.uniqueUi.classList.contains("is-enabled");

		_inlineScripts.tablefiles.priorRoll.table = selected;
		_inlineScripts.tablefiles.priorRoll.count = count === 1 ? "" : count;
		_inlineScripts.tablefiles.priorRoll.format = format;
		_inlineScripts.tablefiles.priorRoll.unique = unique;

		const items = await getCurrentTableItems(data);
		if (unique)
		{
			count = Math.min(count, items.length);
		}
		let result = [];
		for (let i = 0; i < count; i++)
		{
			let nextEntry;
			do
			{
				nextEntry = aPick(items);
			}
			while(unique && result.includes(nextEntry));
			result.push(nextEntry);
		}
		switch (format)
		{
			case "Bulleted list":
				result = "- " + result.join("\n- ") + "\n";
				break;
			case "Perioded list":
				result = ". " + result.join("\n. ") + "\n";
				break;
			default:
				result = result.join(", ");
				break;
		}
		resolveFnc(result);
	}
};//);
```
__
Sets up this shortcut-file


__
```
^sfile shutdown$
```
__
```js
_inlineScripts.inlineScripts.helperFncs.removeCss("tablefiles");
delete _inlineScripts?.state?.sessionState?.tablefiles;
delete _inlineScripts?.state?.listeners?.onReset?.tablefiles;
```
__
Shuts down this shortcut-file


__
```
^tbl reset$
```
__
```js
const confirmObjectPath =
	_inlineScripts.inlineScripts.helperFncs.confirmObjectPath;
confirmObjectPath("_inlineScripts.state.sessionState.tablefiles");
_inlineScripts.state.sessionState.tablefiles.paths = {};
_inlineScripts.state.sessionState.tablefiles.settings = {};
```
__
tbl reset - Clears registered table paths and table path settings.


__
```
^tbl add ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+)$
```
__
```js
$1 = ($1.match(/\"[^\"]\"/)) ? $1.slice(1, -1) : $1;
let file = app.vault.fileMap[$1];
if (!file && !$1.endsWith(".md"))
{
	$1 += ".md";
	file = app.vault.fileMap[$1];
}
if (!file)
{
	return "Path not added.  Path __" + $1 + "__ not found.\n\n";
}
_inlineScripts.state.sessionState.tablefiles.paths[$1] = 1;
return (file.children ? "Folder" : "File") +
	" __" + $1 + "__ added to table paths.\n\n";
```
__
tbl add {path: path string} - Adds {path} to the list of registered table paths.  {path} is either an individual file, or a folder filled with table files.


__
```
^tbl list$
```
__
```js
const paths = Object.keys(_inlineScripts.state.sessionState.tablefiles.paths);
let result = "";
if (paths.length)
{
	for (const path of paths)
	{
		result += ". " + path + "\n";
	}
}
else
{
	result = "NONE\n";
}
return "Table paths:\n" + result + "\n";
```
__
tbl list - Get a list of the registered table paths.


__
```
^tbl roll$
```
__
```js
const result = popups.custom(
	"Select a table to pick from", _inlineScripts.tablefiles.rollPopup);
return result || "No table rolled.  User canceled.\n\n";
```
__
tbl roll - Get random results from one of the registered tables.  Shows a popup to allow selecting the table and how to get results from it.
