Shortcut file to load tables from files and roll them.


__
__
```js
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
function removeHeader(path)
{
	return path.match("^(?:~.*~)?(.*)")[1];
}
async function getTableItems(path, offset)
{
	if (!path.startsWith("~files~"))
	{
		const file = app.vault.fileMap[path];
		if (!file) { return []; }
		const content = await app.vault.cachedRead(file);
		return content.split("\n").slice(offset || 0).filter(v => v);
	}
	else
	{
		const file = app.vault.fileMap[removeHeader(path)];
		if (!file || !file.children)
		{
			return [];
		}
		return file.children.map(v => v.path);
	}
}
async function rollTable(
	tablePath, count, uniquePicks, format, isFileTable, startOffset, itemFormat)
{
	const file = app.vault.fileMap[tablePath];
	if (!file)
	{
		return "No table rolled.  Path __" + tablePath + "__ not found.\n\n";
	}
	if (file.children && !isFileTable)
	{
		return "No table rolled.  Path __" +
			tablePath + "__ is not a table file.\n\n";
	}
	if (!file.children && isFileTable)
	{
		return "No table rolled.  'isFileTable' specified, but path __" +
			$1 + "__ is not a folder.\n\n";
	}
	if (!Number.isInteger(count) || count < 1)
	{
		return "No table rolled.  Invalid count given.\n\n";
	}
	if (!Number.isInteger(startOffset) || startOffset < 0)
	{
		return "No table rolled.  Invalid startOffset given.\n\n";
	}
	try
	{
		itemFormat = new RegExp(itemFormat || "(.*)");
	}
	catch (e)
	{
		return "No table rolled.  Invalid itemFormat given.\n\n";
	}
	tablePath = (isFileTable ? "~files~" : "") + tablePath;
	let items = await getTableItems(tablePath, startOffset);
	if (!items.length)
	{
		return "";
	}
	let itemsHaveRange = false;
	for (let i = 0; i < items.length; i++)
	{
		let match = items[i].match(itemFormat);
		items[i] =
		[
			match?.groups?.item || match?.[1] || items[i],
			match?.groups?.range || match?.[2] || null
		];
		if (items[i][1])
		{
			itemsHaveRange = true;
		}
	}
	if (itemsHaveRange)
	{
		items.filter(v => v[1]);
		items.sort((lhs, rhs) =>
		{
			lhs[1] - rhs[1];
		});
	}
	else
	{
		for (let i = 0; i < items.length; i++)
		{
			items[i][1] = (i+1);
		}
	}

	count = (uniquePicks && count >= items.length) ? items.length : count;
	let result = [];
	for (let i = 0; i < count; i++)
	{
		let nextEntry;
		do
		{
			nextEntry = aPickWeight(items);
		}
		while(uniquePicks && result.includes(nextEntry[0]));
		result.push(nextEntry[0]);
	}
	switch (format)
	{
		case "bullets":
			result = "- " + result.join("\n- ") + "\n";
			break;
		case "periods":
			result = ". " + result.join("\n. ") + "\n";
			break;
		default:
			result = result.join(", ");
			break;
	}
	return result;
}
```
__
Helper functions


__
```
^sfile setup$
```
__
```js
const confirmObjectPath =
	_inlineScripts.inlineScripts.helperFncs.confirmObjectPath;
confirmObjectPath("_inlineScripts.state.sessionState.tablefiles.paths");
confirmObjectPath("_inlineScripts.state.sessionState.tablefiles.configuration");
confirmObjectPath(
	"_inlineScripts.state.listeners.onReset.tablefiles",
	function()
	{
		expand("tbl reset");
	});
confirmObjectPath("_inlineScripts.tablefiles.priorRoll");
_inlineScripts.inlineScripts.helperFncs.addCss("tableFiles", ".iscript_popupLabel { margin-right: .25em; white-space: nowrap; } .iscript_nextPopupLabel { margin-left: 1.5em } .iscript_popupRow { width: 100%; margin-bottom: 1em; } .iscript_smallButton { padding: 0.5em 0.5em; margin: 0 } .iscript_smallButtonDisabled { color: grey; cursor: unset } .iscript_nextPopupLabelSquished { margin-left: .5em } .iscript_minWidth { width: 0% } .iscript_textbox_squished { padding: 4px !important; }");


///////////////////////////////////////////////////////////////////////////////////

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
		if (content.tagName === "DIV" || content.tagName === "BUTTON")
		{
			td.classList.add("iscript_minWidth");
		}
		tr.append(td);
	}
	return tbl;
}
async function getCurrentTableItems(data, offset)
{
	if (offset === undefined)
	{
		offset = data.current.configuration?.offset || 0;
	}
	return getTableItems(data.current.path, offset);
}
async function updateTableConfig(data)
{
	const isFileTable = data.selectUi.value.startsWith("~files~");
	data.current = {};
	data.current.path = data.selectUi.value;
	data.current.configuration =
		_inlineScripts.state.sessionState.tablefiles.
		configuration[data.current.path];
	data.configUi.path.value = data.current.path;
	data.configUi.title.value = data.current.configuration?.title || "";
	data.configUi.description.value =
		data.current.configuration?.description || "";
	data.configUi.tags.value = data.current.configuration?.tags || "";
	const tblLines = await getCurrentTableItems(data);
	data.configUi.startLine.value = tblLines[0] || "";
	data.configUi.startLineUp.toggleClass(
		"iscript_smallButtonDisabled",
		!(data.current?.configuration?.offset) || isFileTable);
	data.configUi.startLineDown.toggleClass(
		"iscript_smallButtonDisabled",
		tblLines.length <= 1 || isFileTable);
	data.configUi.itemFormat.value = data.current.configuration?.itemFormat || "";
	refreshItemFormatSample(data);
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
			tablefiles.configuration[optionDatum.path];
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
			const p = removeHeader(optionDatum.path);
			const startIndex = p.lastIndexOf("/")+1;
			let endIndex = p.lastIndexOf(".");
			if (endIndex === -1) { endIndex = undefined; }
			optionDatum.title = p.slice(startIndex, endIndex);
			if (optionDatum.path.startsWith("~files~"))
			{
				optionDatum.title += " (file table)";
			}
		}

		// Track max title size
		titleWidth = Math.max(titleWidth, optionDatum.title.length);

		// Add title and path to filterkeys
		optionDatum.filterKeys.push(optionDatum.title.toLowerCase());
		optionDatum.filterKeys.push(optionDatum.path.toLowerCase());

		optionData.push(optionDatum);
	}

	// Set the ui.  Needs its own loop, since titleWidth needs a completed loop.
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
function refreshItemFormatSample(data)
{
	let match;
	try
	{
		match =
			data.configUi.startLine.value.match(
			new RegExp(data.configUi.itemFormat.value || "(.*)"));
	}
	catch(e){}
	data.configUi.sampleItem.value = match?.groups?.item || match?.[1] || "";
	data.configUi.sampleRange.value = match?.groups?.range || match?.[2] || "";
}

///////////////////////////////////////////////////////////////////////////////////

//confirmObjectPath("_inlineScripts.tablefiles.rollPopup",
_inlineScripts.tablefiles.rollPopup =
{
	buttonIds: [ "Roll", "Cancel" ],
	onOpen: async (data, parent, firstButton, SettingType, resolveFnc) =>
	{
		//////////////////////////
		// Row 1 of UI (filter) //
		//////////////////////////
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
			uiRow[1].addEventListener("input", e =>
			{
				updateFilter(data);
			});
		let tbl = makeUiRow(uiRow);
			tbl.style["margin-bottom"] = 0;
			parent.append(tbl);

		//////////////////////////
		// Row 2 of UI (select) //
		//////////////////////////
		let tablePaths = [];
		const rawPaths = _inlineScripts.state.sessionState.tablefiles.paths;
		for (const path in rawPaths)
		{
			const file = app.vault.fileMap[removeHeader(path)];
			if (!file) { continue; }
			if (!file.children || path.startsWith("~files~"))
			{
				tablePaths.push(path);
			}
			else
			{
				for (const child of file.children)
				{
					if (child.children) { continue; }
					tablePaths.push(child.path);
				}
			}
		}
		if (tablePaths.length === 0)
		{
			resolveFnc("No table rolled.  No tables available.\n\n");
			return true;
		}
		const selectUi = document.createElement("select");
			data.selectUi = selectUi;
			// Find longest title
			for (const path of tablePaths)
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

		/////////////////////////////////////////
		// Row 3 of ui (count, format, unique) //
		/////////////////////////////////////////
		uiRow = [ 0, 1, 2, 3, 4, 5 ];
		uiRow[0] = document.createElement("div");
			uiRow[0].innerText = "Pick count";
			uiRow[0].classList.add("iscript_popupLabel");
		uiRow[1] = document.createElement("input");
			data.countUi = uiRow[1];
			uiRow[1].type = "text";
			uiRow[1].classList.add("iscript_textbox_squished");
			uiRow[1].setAttr("placeholder", 1);
			uiRow[1].style.width = "3em";
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
			uiRow[2].innerText = "Unique picks";
			uiRow[2].classList.add("iscript_popupLabel");
			uiRow[2].classList.add("iscript_nextPopupLabel");
		uiRow[3] = document.createElement("div");
			data.uniqueUi = uiRow[3];
			uiRow[3].classList.add("checkbox-container");
			uiRow[3].toggleClass(
				"is-enabled", _inlineScripts.tablefiles.priorRoll.unique);
			uiRow[3].addEventListener("click", e =>
			{
				e.target.toggleClass(
					"is-enabled",
					!e.target.classList.contains("is-enabled"));
			});
		uiRow[4] = document.createElement("div");
			uiRow[4].innerText = "Pick format";
			uiRow[4].classList.add("iscript_popupLabel");
			uiRow[4].classList.add("iscript_nextPopupLabel");
		uiRow[5] = document.createElement("select");
			data.formatUi = uiRow[5];
			uiRow[5].classList.add("dropdown");
			uiRow[5].style["padding-left"] = "6px";
			uiRow[5].style["padding-right"] = "27px";
			uiRow[5].options[0] = new Option("Comma separated", "commas");
			uiRow[5].options[1] = new Option("Bulleted list", "bullets");
			uiRow[5].options[2] = new Option("Perioded list", "periods");
			uiRow[5].value =
				_inlineScripts.tablefiles.priorRoll.format || "commas";
			uiRow[5].addEventListener("keypress", e =>
			{
				if (e.key === "Enter") { firstButton.click(); }
			});
		tbl = makeUiRow(uiRow);
			tbl.childNodes[0].childNodes[1].style.width = "0%";
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
		    cfgBtn.style["margin"] = "0";
    	    cfgBtn.style["font-size"] = "15px";
    	    cfgBtn.style["padding"] = "6px 20px";
			cfgBtn.addEventListener("click", async () =>
			{
				const show = data.configUi.root.style.display === "none";
				data.configUi.root.style.display = show ? "block" : "none";
				cfgBtn.innerHTML = cfgBtnText + (show ? "▲" : "▼");
			});
			parent.nextSibling.prepend(cfgBtn);

		////////////////////////////////////////////
		// Row 4 of ui (title, description, path) //
		////////////////////////////////////////////
		uiRow = [ 0, 1, 2, 3, 4, 5 ];
		uiRow[0] = document.createElement("div");
			uiRow[0].innerText = "Title";
			uiRow[0].classList.add("iscript_popupLabel");
		uiRow[1] = document.createElement("input");
			data.configUi.title = uiRow[1];
			uiRow[1].classList.add("iscript_textbox_squished");
			uiRow[1].type = "text";
			uiRow[1].setAttr("placeholder", "Display");
			uiRow[1].style["min-width"] = "7em !important";
			uiRow[1].addEventListener("change", e =>
			{
				if (!data.current.configuration)
				{
					data.current.configuration =
						_inlineScripts.state.sessionState.tablefiles.
						configuration[data.current.path] = {};
				}
				data.current.configuration.title = e.target.value;
				refreshSelectUi(data);
			});
		uiRow[2] = document.createElement("div");
			uiRow[2].innerText = "Description";
			uiRow[2].classList.add("iscript_popupLabel");
			uiRow[2].classList.add("iscript_nextPopupLabel");
		uiRow[3] = document.createElement("input");
			data.configUi.description = uiRow[3];
			uiRow[3].type = "text";
			uiRow[3].classList.add("iscript_textbox_squished");
			uiRow[3].addEventListener("change", e =>
			{
				if (!data.current.configuration)
				{
					data.current.configuration =
						_inlineScripts.state.sessionState.tablefiles.
						configuration[data.current.path] = {};
				}
				data.current.configuration.description = e.target.value;
				refreshSelectUi(data);
			});
		uiRow[4] = document.createElement("div");
			uiRow[4].innerText = "Path";
			uiRow[4].classList.add("iscript_popupLabel");
			uiRow[4].classList.add("iscript_nextPopupLabel");
		uiRow[5] = document.createElement("input");
			data.configUi.path = uiRow[5];
			uiRow[5].type = "text";
			uiRow[5].classList.add("iscript_textbox_squished");
			uiRow[5].setAttr("readonly", true);
			uiRow[5].style["min-width"] = "12em !important";
			uiRow[5].style["background-color"] = "var(--background-secondary)";
		tbl = makeUiRow(uiRow);
			configUi.append(tbl);

		////////////////////////////////////
		// Row 5 of ui (tags, start-line) //
		////////////////////////////////////
		uiRow = [ 0, 1, 2, 3, 4, 5 ];
		uiRow[0] = document.createElement("div");
			uiRow[0].innerText = "Filter-tags";
			uiRow[0].classList.add("iscript_popupLabel");
		uiRow[1] = document.createElement("input");
			data.configUi.tags = uiRow[1];
			uiRow[1].type = "text";
			uiRow[1].classList.add("iscript_textbox_squished");
			uiRow[1].setAttr("placeholder", "Space-separated");
			uiRow[1].addEventListener("change", e =>
			{
				if (!data.current.configuration)
				{
					data.current.configuration =
						_inlineScripts.state.sessionState.tablefiles.
						configuration[data.current.path] = {};
				}
				data.current.configuration.tags = e.target.value;
				refreshSelectUi(data);
				if (data.filterUi.value)
				{
					updateFilter(data);
				}
			});
		uiRow[2] = document.createElement("div");
			uiRow[2].innerText = "Starting line";
			uiRow[2].classList.add("iscript_popupLabel");
			uiRow[2].classList.add("iscript_nextPopupLabel");
		uiRow[3] = document.createElement("input");
			data.configUi.startLine = uiRow[3];
			uiRow[3].type = "text";
			uiRow[3].classList.add("iscript_textbox_squished");
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
				if (!data.current.configuration)
				{
					data.current.configuration =
						 _inlineScripts.state.sessionState.tablefiles.
						 configuration[data.current.path] = {};
				}
				if (!data.current.configuration.offset)
				{
					data.current.configuration.offset = 0;
				}
				data.current.configuration.offset--;
				const tblLines =
					await getCurrentTableItems(data);
				data.configUi.startLine.value = tblLines[0] || "";
				e.target.toggleClass(
					"iscript_smallButtonDisabled",
					data.current.configuration.offset === 0);
				data.configUi.startLineDown.toggleClass(
					"iscript_smallButtonDisabled", false);
				refreshItemFormatSample(data);
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
				if (!data.current.configuration)
				{
					data.current.configuration =
						_inlineScripts.state.sessionState.tablefiles.
						configuration[data.current.path] = {};
				}
				if (!data.current.configuration.offset)
				{
					data.current.configuration.offset = 0;
				}
				data.current.configuration.offset++;
				const tblLines =
					await getCurrentTableItems(
					data, data.current.configuration.offset);
				data.configUi.startLine.value = tblLines[0] || "";
				e.target.toggleClass(
					"iscript_smallButtonDisabled", tblLines.length <= 1);
				data.configUi.startLineUp.toggleClass(
					"iscript_smallButtonDisabled", false);
				refreshItemFormatSample(data);
			});
		tbl = makeUiRow(uiRow);
			configUi.append(tbl);

		////////////////////////////////////////
		// Row 6 of ui (item format, samples) //
		////////////////////////////////////////
		uiRow = [ 0, 1, 2, 3, 4, 5 ];
		uiRow[0] = document.createElement("div");
			uiRow[0].innerText = "Item format";
			uiRow[0].classList.add("iscript_popupLabel");
		uiRow[1] = document.createElement("input");
			data.configUi.itemFormat = uiRow[1];
			uiRow[1].type = "text";
			uiRow[1].classList.add("iscript_textbox_squished");
			uiRow[1].setAttr("placeholder", "(.*)");
			uiRow[1].setAttr("list", "itemFormatOptions");
			uiRow[1].style["max-width"] = "11.5em !important";
			uiRow[1].addEventListener("input", e =>
			{
				if (e.target.value[0] === " ")
				{
					for (const option of data.configUi.itemFormatOptions.options)
					{
						if (option.value === e.target.value)
						{
							e.target.value = option.dataset.regex;
						}
					}
				}
				refreshItemFormatSample(data);
			});
			uiRow[1].addEventListener("change", e =>
			{
				if (!data.current.configuration)
				{
					data.current.configuration =
						_inlineScripts.state.sessionState.tablefiles.
						configuration[data.current.path] = {};
				}
				data.current.configuration.itemFormat =
					e.target.value;
			});
		uiRow[2] = document.createElement("div");
			uiRow[2].innerText = "Sample item";
			uiRow[2].classList.add("iscript_popupLabel");
			uiRow[2].classList.add("iscript_nextPopupLabelSquished");
		uiRow[3] = document.createElement("input");
			data.configUi.sampleItem = uiRow[3];
			uiRow[3].type = "text";
			uiRow[3].classList.add("iscript_textbox_squished");
			uiRow[3].setAttr("readonly", true);
			uiRow[3].style["background-color"] = "var(--background-secondary)";
		uiRow[4] = document.createElement("div");
			uiRow[4].innerText = "Sample Range";
			uiRow[4].classList.add("iscript_popupLabel");
			uiRow[4].classList.add("iscript_nextPopupLabelSquished");
		uiRow[5] = document.createElement("input");
			data.configUi.sampleRange = uiRow[5];
			uiRow[5].type = "text";
			uiRow[5].classList.add("iscript_textbox_squished");
			uiRow[5].style.width = "3em";
			uiRow[5].setAttr("readonly", true);
			uiRow[5].style["background-color"] = "var(--background-secondary)";
		tbl = makeUiRow(uiRow);
			configUi.append(tbl);

		/////////////////////////////////
		// Item format option dropdown //
		/////////////////////////////////
		let itemFormatOptions = document.createElement("datalist");
		data.configUi.itemFormatOptions = itemFormatOptions;
		itemFormatOptions.id = "itemFormatOptions";
		itemFormatOptions.innerHTML =
			"<option value='&thinsp;Basic item' data-regex='(.*)'></option>" +
			"<option value='&thinsp;Weighted item 1-5' " +
				"data-regex='(.*) [0-9]+-([0-9]+)'></option>" +
			"<option value='&thinsp;1-5 Weighted item' " +
				"data-regex='[0-9]+-(?<range>[0-9]+) (?<item>.*)'></option>" +
			"<option value='&thinsp;Path - base file name' " +
				"data-regex='.*?([^/]+)\\.[^.]*$'></option>" +
			"<option value='&thinsp;Path - full file name' " +
				"data-regex='.*?([^/]+)$'></option>" +
		configUi.append(itemFormatOptions);

		updateTableConfig(data);
	},
	onClose: async (data, resolveFnc, buttonId) =>
	{
		if (buttonId !== "Roll") { return null; }
		const selected = data.selectUi.value;
		const path = removeHeader(selected);
		const count = Number(data.countUi.value) || 1;
		const unique = data.uniqueUi.classList.contains("is-enabled");
		const format = data.formatUi.value;
		const isFileTable = selected.startsWith("~files~");
		const startOffset = data.current.configuration?.offset || 0;
		const itemFormat = data.current.configuration?.itemFormat || "";

		_inlineScripts.tablefiles.priorRoll.table = selected;
		_inlineScripts.tablefiles.priorRoll.count = count === 1 ? "" : count;
		_inlineScripts.tablefiles.priorRoll.format = format;
		_inlineScripts.tablefiles.priorRoll.unique = unique;

		resolveFnc(rollTable(
			path, count, unique, format, isFileTable, startOffset, itemFormat));
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
_inlineScripts.state.sessionState.tablefiles.configuration = {};
```
__
tbl reset - Clears registered table paths and table path configurations.


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
tbl add {path: path string} - Adds {path} to the list of registered table paths.  {path} is either an individual table file, or a folder filled with table files.


__
```
^tbl addfiletable ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+)$
```
__
```js
$1 = ($1.match(/\"[^\"]\"/)) ? $1.slice(1, -1) : $1;
let file = app.vault.fileMap[$1];
if (!file || !file.children)
{
	return "File-table not added.  Folder __" + $1 + "__ not found.\n\n";
}
_inlineScripts.state.sessionState.tablefiles.paths["~files~" + $1] = 1;
return "File-table __" + $1 + "__ added to table paths.\n\n";
```
__
tbl addfiletable {path: path string} - Adds the folder {path} to the list of registered table paths as a file-table.  A file-table is a virtual table where each item is a file in the {path} folder.


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
		if (!path.startsWith("~files~"))
		{
			result += ". " + path + "\n";
		}
		else
		{
			result += ". " + removeHeader(path) + " _(file table)_\n";
		}
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
return result !== null ? result : "No table rolled.  User canceled.\n\n";
```
__
tbl roll - Get random results from one of the registered tables.  Shows a popup to allow selecting the table and how to get results from it.


__
```
^tbl roll ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ?(.*)$
```
__
```js
let parameters;
try
{
	parameters = (new Function("return { " + $2 + "}"))();
}
catch(e)
{
	return "No table rolled.  Unable to parse parameters.\n\n";
}
const defaultParameters =
	{
		count: 1, uniquePicks: false, format: "commas", isFileTable: false,
		startOffset: 0, itemFormat: ""
	};
parameters = Object.assign({}, defaultParameters, parameters);
return rollTable(
	$1, parameters.count, parameters.uniquePicks, parameters.format,
	parameters.isFileTable, parameters.startOffset, parameters.itemFormat);
```
__
tbl roll {table file: path text} {parameters: text, default: ""} - Get random results from table {table file}.  If provided, {parameters} can alter the results.  {parameters} is expected to be a comma-separated list of parameters in "key: value" form.  Here are accepted parameters:
	- __count__ - A positive integer defaulting to 1.  Determines number of items picked.
	- __uniquePicks__ - "true" or "false", defaulting to "false".  If true, each item can be picked only once for this roll.
	- __format__ - "commas", "bullets" or "periods", defaulting to "commas".  Determines the format of the output.
	- __isFileTable__ - "true" or "false", defaulting to "false".  If true, {table file} must be a folder path, and the result is picks from the files within it.
	- __startOffset__ - A positive integer defaulting to 0.  Defines what line the table starts on in {table file}.  This is ignored for file-tables.
	- __itemFormat__ - A regex string defaulting to `(.*)`.  Determines what part of each item is printed out, as well as what part of each item is used as the weight value.  The default prints out the entire item.
