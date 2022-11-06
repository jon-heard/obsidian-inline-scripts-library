Shortcut file to load tables from files and roll them.

This shortcut-file has a tutorial video available:
[Using the "tablefiles" shortcut-file to roll tables in text files](https://www.youtube.com/watch?v=kE3IlJn7COM) (runtime 9:00)


__
__
```js
// The heading for a folder-table
const HEADING_FTABLE = "~folder~";

// Make a roll from 1 to max.
function roll(max) { return Math.trunc(Math.random() * max + 1); }

// Pick an item from array a, weighted by element wIndex of the item.  If theRoll is
// passed, use that as the roll.
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

// Remove any tilde-encased text from the start of the string
function removeTildeHeader(path)
{
	return path.match("^(?:~.*~)?(.*)")[1];
}

function getTableFilesFromPath(path)
{
	// Get the file object for the given path, early out if not available
	const file = app.vault.fileMap[removeTildeHeader(path)];
	if (!file) { return null; }

	// Folder-tables MUST be folders and are just themselves
	if (path.startsWith(HEADING_FTABLE))
	{
		if (!file.children) { return null; }
		return [ file ];
	}

	// Path isn't a folder-table.  If path is a file, just return itself
	if(!file.children)
	{
		return [ file ];
	}

	// Path is a folder.  Gather all the child files recursively.
	result = [];
	toRecurse = [ file ];
	// Keep going while we have folders left to pull files from
	while (toRecurse.length)
	{
		// Remove one of the folders in "toRecurse" to iterate over it
		const beingRecursed = toRecurse.pop();

		// Go over the children for the folder
		for (const child of beingRecursed.children)
		{
			// If a file, just add it to the result list
			if (!child.children)
			{
				result.push(child);
			}

			// If a folder, add it to the "toRecurse" list to go over it later
			else
			{
				toRecurse.push(child);
			}
		}
	}

	return result;
}

function getAllTableFiles()
{
	let result = [];

	// Get the stored paths of tables, folders, folder-tables
	const rawPaths = _inlineScripts.state.sessionState.tablefiles.paths;

	// Iterate over the paths to get just the table paths
	for (const path in rawPaths)
	{
		// Get all files associated with the current path
		const tableFilesFromPath = getTableFilesFromPath(path);

		// If path had no files, skip it
		if (!tableFilesFromPath) { continue; }

		// Add all files to the tablePaths (as their paths)
		if (path.startsWith(HEADING_FTABLE))
		{
			result.push(HEADING_FTABLE + tableFilesFromPath[0]);
		}
		else
		{
			result = result.concat(tableFilesFromPath);
		}
	}

	return result;
}

// Get the tablepath data - the lines and the frontmatter configuration (if there)
async function getTableFileContent(path)
{
	// If the path is a file, the items are the lines in the file
	if (!path.startsWith(HEADING_FTABLE))
	{
		// Get the file object, or return an empty array if none
		const file = app.vault.fileMap[path];
		if (!file) { return []; }

		// Read the file's contents, split into non-empty lines
		let rawContent = await app.vault.cachedRead(file);
		let content = (await app.vault.cachedRead(file)).split("\n").filter(v => v);

		// Frontmatter
		let configuration = null;
		const REGEX_FRONT_MATTER_VARIABLE =
			/^(title|description|tags|startLine|itemFormat)\s*:\s*(.*)$/i;
		if (content[0] === "---")
		{
			// There's a frontmatter, so there's a frontmatter-configuration.
			// Start with defaults, then add from any variables
			configuration =
				{ title: "", description: "", tags: "", startLine: 0,
				itemFormat: "" };

			// Go over each line of the frontmatter
			for (let i = 1; i < content.length; i++)
			{
				// Handle any configuration variables in the frontmatter
				const varMatch = content[i].match(REGEX_FRONT_MATTER_VARIABLE);
				if (varMatch)
				{
					configuration[varMatch[1]] = varMatch[2];
				}

				// Once we hit the end of the frontmatter, cut it from the content
				if (content[i] === "---")
				{
					content = content.slice(i+1);
					break;
				}
			}

			// Make sure the startLine is the right format
			configuration.startLine =
				Math.max(0, Math.trunc(  Number(configuration.startLine) || 0  ));
		}

		// Return the file's lines, skipping the first "startLine" lines
		return { lines: content, configuration };
	}
	// If the path is a folder, the items are the files in the folder
	else
	{
		// Get the folder object, or return an empty array if none or if not a folder
		const file = app.vault.fileMap[removeTildeHeader(path)];
		if (!file || !file.children)
		{
			return [];
		}

		// Return the folder's child files/folders as links
		return {
			lines: file.children.map(v => "[[" + v.path + "|" + v.basename + "]]") };
	}
}

// Take a table-path & parameters for how to roll it.  Roll it & return the result.
// The is the MAIN function of the tablefiles system.  Each table roll happens here.
async function rollTable(
	tablePath, count, uniquePicks, format, useExpFormat, useConfig,
	startLine, itemFormat)
{
	// A wrapper for expFormat only formats if "useExpFormat" is true
	function cndFormat(text)
	{
		return useExpFormat ? expFormat(text) : text;
	}

	// Remember whether the tablePath is a folder, then remove any tilde header
	const isFolderTable = tablePath.startsWith(HEADING_FTABLE);
	let baseTablePath = removeTildeHeader(tablePath);

	// Get the file object for tablePath, or early out if it's invalid
	const file =
		app.vault.fileMap[baseTablePath] || app.vault.fileMap[baseTablePath + ".md"];
	if (!file)
	{
		return cndFormat(
			[ "", "No table rolled.  Path __" + baseTablePath + "__ not found." ]);
	}
	if (file.children && !isFolderTable)
	{
		return cndFormat(
			[ "", "No table rolled.  Path __" + baseTablePath +
			"__ isn't a table file." ]);
	}
	if (!file.children && isFolderTable)
	{
		return cndFormat(
			[ "", "No table rolled.  Path __" + baseTablePath +
			"__ is not a folder." ]);
	}

	// Reassign tablePath & baseTablePath from the file, in case ".md" was added
	tablePath = (isFolderTable?  HEADING_FTABLE : "") + file.path;
	baseTablePath = file.path;

	// If count parameter isn't valid, early out
	if (!Number.isInteger(count) || count < 1)
	{
		return cndFormat([ "", "No table rolled.  Invalid count given." ]);
	}

	// Get table items. No items? return null
	const content = await getTableFileContent(tablePath, startLine);
	let lines = content?.lines;
	let config = content.configuration;
	if (!lines?.length) { return null; }

	// If useConfig, set some of the parameters to the config saved for tablePath
	if (useConfig)
	{
		// If no frontmatter configuration, pull one from the settings, if possible
		if (!config)
		{
			config =
				_inlineScripts.state.sessionState.tablefiles.
				configuration[tablePath] || {};
		}
		// Set the parameters from the config (or use defaults, if undefined)
		startLine = config.startLine || 0;
		itemFormat = config.itemFormat || "";
	}
	// If startLine parameter isn't valid, early out
	if (!Number.isInteger(startLine) || startLine < 0)
	{
		return cndFormat([ "", "No table rolled.  Invalid startLine given." ]);
	}

	// Use start line now
	lines = lines.slice(startLine);

	// Convert itemFormat parameter to RegExp object.  If not valid, early out
	try
	{
		itemFormat = new RegExp(itemFormat || "(.*)");
	}
	catch (e)
	{
		return cndFormat(
			[ "", "No table rolled.  Invalid itemFormat given: __" + itemFormat +
			"__." ]);
	}

	// That's all the early-outs finished, record the roll for re-rolling purposes
	_inlineScripts.tablefiles.priorRoll =
		{ tablePath, count, uniquePicks, format, useExpFormat, useConfig,
		startLine, itemFormat };

	// Use itemFormat parameter to break the lines up into the item and range
	let linesHaveRange = false;
	for (let i = 0; i < lines.length; i++)
	{
		let match = lines[i].match(itemFormat);
		lines[i] =
		{
			item: match?.groups?.item || match?.[1] || lines[i],
			range: match?.groups?.range || match?.[2] || null
		};
		if (lines[i].range)
		{
			linesHaveRange = true;
		}
	}

	// If lines include a range, remove any un-ranged, then sort them by range
	if (linesHaveRange)
	{
		lines.filter(v => v.range);
		lines.sort((lhs, rhs) => { lhs.range - rhs.range; });
	}

	// If items do NOT come with a range, add a consecutive, incrementing range
	else
	{
		for (let i = 0; i < lines.length; i++)
		{
			lines[i].range = (i+1);
		}
	}

	// If uniquePicks parameter, ceiling the count by the lines count
	count = (uniquePicks && count >= lines.length) ? lines.length : count;

	// Pick "count" lines.  If uniquePicks parameter, make sure each pick is unique.
	let result = [];
	for (let i = 0; i < count; i++)
	{
		let nextEntry;
		do
		{
			nextEntry = aPickWeight(lines, "range");
		}
		while(uniquePicks && result.includes(nextEntry.item));
		result.push(nextEntry.item);
	}

	// If any items are "pick-and-replace", re-pick from the item-specified table
	for (let i = 0; i < result.length; i++)
	{
		// Only deal with "pick-and-replace" items
		if (!result[i].startsWith("~pick-and-replace~")) { continue; }

		// Get the path of the item-specified table
		let nextEntryPath = removeTildeHeader(result[i]);

		// If necessary, add a ".md" extension to the path
		if (!app.vault.fileMap[nextEntryPath] &&
			app.vault.fileMap[nextEntryPath + ".md"])
		{
			nextEntryPath += ".md";
		}

		// If the path isn't valid, try turning it from relative (to the current
		// table) to absolute (to the vault)
		if (!app.vault.fileMap[nextEntryPath])
		{
			nextEntryPath =
				(app.vault.fileMap[tablePath]?.parent?.path || "") + "/" +
				nextEntryPath;
		}

		// If necessary, add a ".md" extension to the path
		if (!app.vault.fileMap[nextEntryPath] &&
			app.vault.fileMap[nextEntryPath + ".md"])
		{
			nextEntryPath += ".md";
		}

		// If the item-specified table path is valid, re-pick from that table
		if (app.vault.fileMap[nextEntryPath])
		{
			const replaceValue =
				(await rollTable(nextEntryPath, 1, false, format, false, true));
			if (replaceValue[i][0])
			{
				result[i] = replaceValue.join("");
			}
		}
	}

	// Format the results into a string, based on the format parameter
	switch (format)
	{
		case "bullets":
			result = "- " + result.join("\n- ") + (useExpFormat ? "" : "\n");
			break;
		case "periods":
			result = ". " + result.join("\n. ") + (useExpFormat ? "" : "\n");
			break;
		default:
			result = result.join(", ");
			break;
	}

	return cndFormat([ result ]);
}
```
__
Helper script


__
```
^sfile setup$
```
__
```js
const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

// Initialize the state
confirmObjectPath("_inlineScripts.state.sessionState.tablefiles.paths");
confirmObjectPath("_inlineScripts.state.sessionState.tablefiles.configuration");

// Event callback - state.onReset
confirmObjectPath(
	"_inlineScripts.state.listeners.onReset.tablefiles",
	function()
	{
		expand("tbl reset noconfirm");
	});

// Initialize session state
confirmObjectPath("_inlineScripts.tablefiles");

// Custom CSS
_inlineScripts.inlineScripts.HelperFncs.addCss("tableFiles", ".iscript_popupLabel { margin-right: .25em; white-space: nowrap; } .iscript_nextPopupLabel { margin-left: 1.5em } .iscript_popupRow { width: 100%; margin-bottom: 1em; } .iscript_smallButton { padding: 0.5em 0.5em; margin: 0 } .iscript_smallButtonDisabled { color: grey; cursor: unset } .iscript_nextPopupLabelSquished { margin-left: .5em } .iscript_minWidth { width: 0% } .iscript_textbox_squished { padding: 4px !important; }");

// Convert from the old syntax
for (const key in _inlineScripts.state.sessionState.tablefiles.configuration)
{
	let config = _inlineScripts.state.sessionState.tablefiles.configuration[key];
	if (config.offset !== undefined)
	{
		config.startLine = config.offset;
		delete config.offset;
	}
}

///////////////////////////////////////////////////////////////////////////////////

// Helper function - Take an array of elements and make a row of ui from them
function makeUiRow(elements)
{
	// Setup the table for the row
	const tbl = document.createElement("table");
		tbl.classList.add("iscript_popupRow");
	const tr = document.createElement("tr");
		tbl.append(tr);

	// Create a cell for each element
	for (const element of elements)
	{
		const td = document.createElement("td");
		td.append(element);
		if (element.tagName === "DIV" || element.tagName === "BUTTON")
		{
			td.classList.add("iscript_minWidth");
		}
		tr.append(td);
	}

	return tbl;
}

// The table configuration can come from the table-file's frontmatter or from user
// entering settings.  Get the appropriate one for the given table-file path
function getTableConfiguration(data, path)
{
	return data.fmConfigurations[path] || 
		_inlineScripts.state.sessionState.tablefiles.
		configuration[path];
}

// Helper function - Called each time a different table is selected from the list.
// Updates the ui and data to match the selected table.
async function updateTableConfig(data)
{
	// Set the current table path and table lines
	data.current = {};
	data.current.path = data.selectUi.value;
	data.current.lines = (await getTableFileContent(data.current.path)).lines

	// Get the table's configuration and lines
	const config = getTableConfiguration(data, data.current.path) || {};

	// Update the ui - path, title, description, tags, startLine visualization
	data.configUi.path.value = data.current.path;
	data.configUi.title.value = config.title || "";
	data.configUi.description.value = config.description || "";
	data.configUi.tags.value = config.tags || "";
	data.configUi.startLine.value = data.current.lines[config.startLine || 0] || "";

	// Determine if the table is a files-table
	const isFolderTable = data.selectUi.value.startsWith(HEADING_FTABLE);

	// Update the ui - startLine button enable-state, itemFormat
	data.configUi.startLineUp.toggleClass(
		"iscript_smallButtonDisabled", !config.startLine || isFolderTable);
	data.configUi.startLineDown.toggleClass(
		"iscript_smallButtonDisabled",
		(config.startLine >= data.current.lines.length - 1) || isFolderTable);
	data.configUi.itemFormat.value = config.itemFormat || "";

	// Update the ui - Format sample visualization
	refreshItemFormatSample(data);
}

// Helper function - Called when data-changes occur which may affect the
// table-select-list-ui
function refreshTableListUi(data)
{
	let maxTitleWidth = 0;
	let tableItemData = [];

	// Fill tableItemData with an item for each table item.  Figure out maxTitleWidth
	for (const tableItem of data.selectUi.options)
	{
		// Setup the initial tableItemDatum for this tableItem
		let tableItemDatum =
		{
			ui: tableItem, path: tableItem.value, title: "", description: "",
			filterKeys: []
		};

		// Get the configuration for the table of this tableItem
		const config = getTableConfiguration(data, tableItemDatum.path);

		// If the configuration exists, update the tableItemDatum with its values
		if (config)
		{
			tableItemDatum.title = config.title || "";
			tableItemDatum.description = config.description || "";
			tableItemDatum.filterKeys =
				(config.tags || "").split(" ").filter(v => v).
				map(v => v.toLowerCase());
		}

		// If the tableItem has no title, make one from the path (i.e. the basename)
		if (!tableItemDatum.title)
		{
			// We'll use Obsidian's file system to make this easier
			const file = app.vault.fileMap[removeTildeHeader(tableItemDatum.path)];

			// File checks should have been resolved by now.  This is exceptional!
			if (!file) { console.error("File not found: " + tableItemDatum.path); }

			// Use table file's basename for its title (or "name" if it's a folder)
			tableItemDatum.title = file?.basename || file?.name || "<Undefined>";

			// If this is a folder table, include that in the title
			if (tableItemDatum.path.startsWith(HEADING_FTABLE))
			{
				tableItemDatum.title += " (folder table)";
			}
		}

		// Track max title size
		maxTitleWidth = Math.max(maxTitleWidth, tableItemDatum.title.length);

		// Add the title and path to the filterkeys
		tableItemDatum.filterKeys.push(tableItemDatum.title.toLowerCase());
		tableItemDatum.filterKeys.push(tableItemDatum.path.toLowerCase());

		// Add the datum to the data array
		tableItemData.push(tableItemDatum);
	}

	// Setup the table item displays.  This needs its own loop, since maxTitleWidth
	// is required, and calculated in the previous loop.
	for (const tableItemDatum of tableItemData)
	{
		// We need to pad with "&nbsp;" (which will be a single character in the
		// html).  Since it's not a single character now, we pad with "~", then
		// replace all "~" with "&nbsp;".
		tableItemDatum.ui.innerHTML =
			tableItemDatum.title.padEnd(maxTitleWidth + 4, "~").
			replaceAll("~", "&nbsp;") + tableItemDatum.description;
	}

	// Sort table items by title
	tableItemData.sort((lhs, rhs) =>
	{
		return lhs.title.localeCompare(rhs.title);
	});

	// Clear and re-add the select options for table items from the table list ui
	while (data.selectUi.options.length)
	{
		data.selectUi.remove(0);
	}
	for (let i = 0; i < tableItemData.length; i++)
	{
		data.selectUi.options[i] = tableItemData[i].ui;
	}

	// Recreate the filter-keys data structure for filtering
	data.filterKeys = {};
	for (const tableItemDatum of tableItemData)
	{
		for (const filterKey of tableItemDatum.filterKeys)
		{
			if (data.filterKeys[filterKey])
			{
				data.filterKeys[filterKey].push(tableItemDatum.path);
			}
			else
			{
				data.filterKeys[filterKey] = [ tableItemDatum.path ];
			}
		}
	}

	// If any filtering is applied, recalculate it now
	if (data.filterUi.value)
	{
		updateFilter(data);
	}
}

// Helper function - Called whenever time the filter or the filterkeys change
function updateFilter(data)
{
	// Start with all table entries, to be reduced by each filter entry's non-hits
	let unfilteredItems = {};
	for (const tableItemUi of data.selectUi.options)
	{
		unfilteredItems[tableItemUi.value] = tableItemUi;
		// All table items start unfiltered
		tableItemUi.style.display = "";
	}

	// if Filter string is blank, early out
	if (!data.filterUi.value) { return; }

	// Split the filter string into individual filter entries
	const filters = data.filterUi.value.toLowerCase().split(" ").filter(v => v);

	// Handle each filter entry individually
	for (const filter of filters)
	{
		let filterMatches = new Set();

		// Check the filter against all filterKeys for partial matches.
		// If found, add all files associated with the filterKey to unfiltered
		for (const key in data.filterKeys)
		{
			if (key.includes(filter))
			{
				data.filterKeys[key].forEach( path => filterMatches.add(path) );
			}
		}

		// "filterMatches" contains all paths to not filtered by the current filter.
		// Filter tableItems down to only those included in "filterMatches".
		for (const key in unfilteredItems)
		{
			if (!filterMatches.has(key))
			{
				unfilteredItems[key].style.display = "none";
				delete unfilteredItems[key];
			}
		}
	}
}

// Helper function - Called when anything item-format samples are based on changes
function refreshItemFormatSample(data)
{
	// Use the startLine value for the sample.  Get a match with itemFormat
	let match;
	try
	{
		match = data.configUi.startLine.value.match(
			new RegExp(data.configUi.itemFormat.value || "(.*)")
		);
	}
	catch(e){}

	// Update the sample item and range based on the match
	data.configUi.sampleItem.value = match?.groups?.item || match?.[1] || "";
	data.configUi.sampleRange.value = match?.groups?.range || match?.[2] || "";
}

///////////////////////////////////////////////////////////////////////////////////

// Definition for the table-roller custom popup, central to this tablefiles system
//confirmObjectPath("_inlineScripts.tablefiles.rollPopup",
_inlineScripts.tablefiles.rollPopup =
{
	buttonIds: [ "Roll", "Cancel" ],
	onOpen: async (data, parent, firstButton, SettingType, resolveFnc) =>
	{
		// Get table paths, early out if none
		let tablePaths = getAllTableFiles().map(v => v.path);
		if (tablePaths.length === 0)
		{
			resolveFnc(expFormat("No table rolled.  No tables available."));
			return true;
		}

		// Load frontmatter configurations from tablepaths
		data.fmConfigurations = {};
		for (const tablePath of tablePaths)
		{
			const c = (await getTableFileContent(tablePath)).configuration;
			if (!c) { continue; }
			c.isFrontmatter = true;
			data.fmConfigurations[tablePath] = c;
		}

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
		const selectUi = document.createElement("select");
			data.selectUi = selectUi;
			let hasItemSelected = false;
			// Find longest title
			for (const path of tablePaths)
			{
				const label = path;
				const value = path;
				const isSelected =
					(value === _inlineScripts.tablefiles.priorRoll?.tablePath);
				selectUi.add(new Option(label, value, false, isSelected));
				if (isSelected) { hasItemSelected = true; }
			}
			selectUi.setAttr("size", 10);
			selectUi.classList.add("dropdown");
			selectUi.classList.add("iscript_listSelect");
			selectUi.style["font-family"] = "monospace";
			selectUi.style.margin = "0.5em 0em";
			selectUi.addEventListener("keypress", e =>
			{
				if (e.key === "Enter") { firstButton.click(); }
			});
			selectUi.addEventListener("change", e => updateTableConfig(data));
			refreshTableListUi(data);
			if (!hasItemSelected) { selectUi.selectedIndex = 0; }
			parent.append(selectUi);

		/////////////////////////////////
		// Row 3 of ui (count, format) //
		/////////////////////////////////
		uiRow = [ 0, 1, 2, 3];
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
			uiRow[1].value = _inlineScripts.tablefiles.priorRoll?.count || "";
			if (uiRow[1].value == 1) { uiRow[1].value = ""; }
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
			uiRow[3].style["padding-left"] = "6px";
			uiRow[3].style["padding-right"] = "27px";
			uiRow[3].options[0] = new Option("Comma separated", "commas");
			uiRow[3].options[1] = new Option("Bulleted list", "bullets");
			uiRow[3].options[2] = new Option("Perioded list", "periods");
			uiRow[3].value =
				_inlineScripts.tablefiles.priorRoll?.format || "commas";
			uiRow[3].addEventListener("keypress", e =>
			{
				if (e.key === "Enter") { firstButton.click(); }
			});
		tbl = makeUiRow(uiRow);
			tbl.style["margin-bottom"] = ".25em";
			parent.append(tbl);

		/////////////////////////////////////////////////
		// Row 4 of ui (unique, use expansion format) //
		/////////////////////////////////////////////////
		uiRow = [ 0, 1, 2, 3, 4 ];
		uiRow[0] = document.createElement("div");
			uiRow[0].innerText = "Unique picks";
			uiRow[0].classList.add("iscript_popupLabel");
		uiRow[1] = document.createElement("div");
			data.uniqueUi = uiRow[1];
			uiRow[1].classList.add("checkbox-container");
			uiRow[1].toggleClass(
				"is-enabled", _inlineScripts.tablefiles.priorRoll?.uniquePicks);
			uiRow[1].addEventListener("click", e =>
			{
				e.target.classList.toggle("is-enabled");
			});
		uiRow[2] = document.createElement("div");
		uiRow[3] = document.createElement("div");
			uiRow[3].innerText = "Use expansion format";
			uiRow[3].classList.add("iscript_popupLabel");
			uiRow[3].classList.add("iscript_nextPopupLabel");
		uiRow[4] = document.createElement("div");
			data.useExpFormatUi = uiRow[4];
			uiRow[4].classList.add("checkbox-container");
			uiRow[4].toggleClass(
				"is-enabled", _inlineScripts.tablefiles.priorRoll?.useExpFormat);
			uiRow[4].addEventListener("click", e =>
			{
				e.target.classList.toggle("is-enabled");
			});
		tbl = makeUiRow(uiRow);
			tbl.childNodes[0].childNodes[2].style.width = "100%";
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
		// Row 5 of ui (title, description, path) //
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
			uiRow[1].addEventListener("change", async e =>
			{
				let config = getTableConfiguration(data, data.current.path);
				if (config?.isFrontmatter)
				{
					expand(
						"notevars set \"" + data.current.path + "\" title " +
						e.target.value);
				}
				// Make sure the state config is setup
				else if (!config)
				{
					config = _inlineScripts.state.sessionState.tablefiles.
						configuration[data.current.path] = {};
				}
				// Add the new value to the configuration
				config.title = e.target.value;
				// Refresh the table list
				refreshTableListUi(data);
			});
		uiRow[2] = document.createElement("div");
			uiRow[2].innerText = "Description";
			uiRow[2].classList.add("iscript_popupLabel");
			uiRow[2].classList.add("iscript_nextPopupLabel");
		uiRow[3] = document.createElement("input");
			data.configUi.description = uiRow[3];
			uiRow[3].type = "text";
			uiRow[3].classList.add("iscript_textbox_squished");
			uiRow[3].addEventListener("change", async e =>
			{
				let config = getTableConfiguration(data, data.current.path);
				if (config?.isFrontmatter)
				{
					expand(
						"notevars set \"" + data.current.path + "\" description " +
						e.target.value);
				}
				// Make sure the state config is setup
				else if (!config)
				{
					config = _inlineScripts.state.sessionState.tablefiles.
						configuration[data.current.path] = {};
				}
				// Add the new value to the configuration
				config.description = e.target.value;
				// Refresh the table list
				refreshTableListUi(data);
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
		// Row 6 of ui (tags, start-line) //
		////////////////////////////////////
		uiRow = [ 0, 1, 2, 3, 4, 5 ];
		uiRow[0] = document.createElement("div");
			uiRow[0].innerText = "Tags";
			uiRow[0].classList.add("iscript_popupLabel");
		uiRow[1] = document.createElement("input");
			data.configUi.tags = uiRow[1];
			uiRow[1].type = "text";
			uiRow[1].classList.add("iscript_textbox_squished");
			uiRow[1].setAttr("placeholder", "Space-separated");
			uiRow[1].addEventListener("change", async e =>
			{
				let config = getTableConfiguration(data, data.current.path);
				if (config?.isFrontmatter)
				{
					expand(
						"notevars set \"" + data.current.path + "\" tags " +
						e.target.value);
				}
				// Make sure the state config is setup
				else if (!config)
				{
					config = _inlineScripts.state.sessionState.tablefiles.
						configuration[data.current.path] = {};
				}
				// Add the new value to the configuration
				config.tags = e.target.value;
				// Refresh the table list
				refreshTableListUi(data);
			});
		uiRow[2] = document.createElement("div");
			uiRow[2].innerText = "Start line";
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
				// Ignore disabled button
				if (e.target.classList.contains("iscript_smallButtonDisabled"))
				{
					return;
				}

				// Get the configuration, and setup (in state) if there isn't one
				let config = getTableConfiguration(data, data.current.path);
				if (!config)
				{
					config = _inlineScripts.state.sessionState.tablefiles.
						configuration[data.current.path] = {};

				}
				// Add the value to the configuration
				config.startLine = Math.max(0, (config.startLine || 0) - 1);
				// If the configuration is from frontmatter, update frontmatter
				if (config?.isFrontmatter)
				{
					if (!data.delayedStartLineSet)
					{
						data.delayedStartLineSet = setTimeout(async () =>
						{
							delete data.delayedStartLineSet;
							expand(
								"notevars set \"" + data.current.path +
								"\" startLine " + config.startLine);
						}, 3000);
					}
				}
				// Update the display
				data.configUi.startLine.value =
					data.current.lines[config.startLine] || "";
				data.configUi.startLineUp.toggleClass(
					"iscript_smallButtonDisabled", config.startLine === 0);
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
				// Ignore disabled button
				if (e.target.classList.contains("iscript_smallButtonDisabled"))
				{
					return;
				}

				// Get the configuration, and setup (in state) if there isn't one
				let config = getTableConfiguration(data, data.current.path);
				if (!config)
				{
					config = _inlineScripts.state.sessionState.tablefiles.
						configuration[data.current.path] = {};

				}
				// Add the value to the configuration
				config.startLine = (config.startLine || 0) + 1;
				// If the configuration is from frontmatter, update frontmatter
				if (config?.isFrontmatter)
				{
					if (!data.delayedStartLineSet)
					{
						data.delayedStartLineSet = setTimeout(async () =>
						{
							delete data.delayedStartLineSet;
							expand(
								"notevars set \"" + data.current.path +
								"\" startLine " + config.startLine);
						}, 3000);
					}
				}
				// Update the display
				data.configUi.startLine.value =
					data.current.lines[config.startLine] || "";
				data.configUi.startLineUp.toggleClass(
					"iscript_smallButtonDisabled", false);
				data.configUi.startLineDown.toggleClass(
					"iscript_smallButtonDisabled",
					config.startLine >= data.current.lines.length - 1);
				refreshItemFormatSample(data);
			});
		tbl = makeUiRow(uiRow);
			tbl.childNodes[0].childNodes[1].style.width = "30%";
			configUi.append(tbl);

		////////////////////////////////////////
		// Row 7 of ui (item format, samples) //
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
				// If the user selected one of the presets, replace their selection
				// with the associated regex string.
				// NOTE: That's not a space character being checked.  It's an exotic
				// character: a thin space, which also prepends all of the presets.
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
				// Refresh the samples
				refreshItemFormatSample(data);
			});
			uiRow[1].addEventListener("change", async e =>
			{
				let config = getTableConfiguration(data, data.current.path);
				if (config?.isFrontmatter)
				{
					expand(
						"notevars set \"" + data.current.path + "\" itemFormat " +
						e.target.value);
				}
				// Make sure the state config is setup
				else if (!config)
				{
					config = _inlineScripts.state.sessionState.tablefiles.
						configuration[data.current.path] = {};
				}
				// Add the new value to the configuration
				config.itemFormat = e.target.value;
				// Refresh the samples
				refreshItemFormatSample(data);
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
				"data-regex='\\|(.*)]]$'></option>" +
			"<option value='&thinsp;Path - full file name' " +
				"data-regex='.*?([^/]+)\\|.*$'></option>" +
		configUi.append(itemFormatOptions);

		updateTableConfig(data);
	},
	onClose: async (data, resolveFnc, buttonId) =>
	{
		// Do nothing unless the "Rolled" button was hit
		if (buttonId !== "Roll") { return null; }

		// Get parameters from ui
		const path = data.selectUi.value;
		const count = Number(data.countUi.value) || 1;
		const unique = data.uniqueUi.classList.contains("is-enabled");
		const format = data.formatUi.value;
		const useExpFormat = data.useExpFormatUi.classList.contains("is-enabled");

		// Do the roll
		resolveFnc(await rollTable(
			path, count, unique, format, useExpFormat, true));
	}
}//);
```
__
Sets up this shortcut-file


__
```
^sfile shutdown$
```
__
```js
// Event callback removal
delete _inlineScripts?.state?.listeners?.onReset?.tablefiles;

// State removal
delete _inlineScripts?.state?.sessionState?.tablefiles;

// Custom CSS removal
_inlineScripts.inlineScripts.HelperFncs.removeCss("tablefiles");
```
__
Shuts down this shortcut-file


__
```
^tbl reset$
```
__
```js
// Confirm
if (!popups.confirm("Confirm resetting the <b>Table files</b> system"))
{
	return null;
}

// Reset
expand("tbl reset noconfirm");

return expFormat("tablefiles system reset");
```
__
tbl reset - Clears registered table paths and table path configurations.
***


__
```
^tbl reset noconfirm$
```
__
```js
const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

// Reset the state
confirmObjectPath("_inlineScripts.state.sessionState.tablefiles");
_inlineScripts.state.sessionState.tablefiles.paths = {};
_inlineScripts.state.sessionState.tablefiles.configuration = {};
```
__
hidden - No-confirm reset


__
```
^tbl add ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+)$
```
__
```js
// Remove any quotes around the parameter
$1 = $1.replaceAll(/^\"|\"$/g, "");

// If neither path nor path.md is valid, early out
const file = app.vault.fileMap[$1] || app.vault.fileMap[$1 + ".md"];
if (!file)
{
	return expFormat("Path not added.  Path __" + $1 + "__ not found.");
}

// Add the path to the state. Use file.path instead of $1 in case the ".md" was added
_inlineScripts.state.sessionState.tablefiles.paths[file.path] = 1;

return expFormat(
	(file.children ? "Folder" : "File") + " __" + $1 + "__ added to table paths.");
```
__
tbl add {path: path text} - Adds {path} to the list of registered table paths.  {path} is either an individual table file, or a folder filled with table files.


__
```
^tbl addfoldertable ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+)$
```
__
```js
// Remove any quotes around the parameter
$1 = $1.replaceAll(/^\"|\"$/g, "");

// If path parameter isn't a valid folder, early out
let file = app.vault.fileMap[$1];
if (!file || !file.children)
{
	return expFormat("Folder-table not added.  Folder __" + $1 + "__ not found.");
}

// Add the path to the table paths as a folder-table
_inlineScripts.state.sessionState.tablefiles.paths[HEADING_FTABLE + $1] = 1;

return expFormat("Folder-table __" + $1 + "__ added to table paths.");
```
__
tbl addfoldertable {path: path text} - Adds the folder {path} to the list of registered table paths as a folder-table.  A folder-table is a virtual table where each item is a file in the {path} folder.


__
```
^tbl list$
```
__
```js
// Get the paths.  Early out if empty
const paths = Object.keys(_inlineScripts.state.sessionState.tablefiles.paths);
if (!paths.length)
{
	return expFormat("Table paths:\n. NONE");
}

// Expansion string is a list of the paths
let result = [];

// Fill the expansion string with the paths
for (const path of paths)
{
	// Normal paths are just added.  Folder-table paths are added with the tilde-
	// header removed and a "folder table" suffix added.
	result.push(
		!path.startsWith(HEADING_FTABLE) ?
		path :
		removeTildeHeader(path) + " _(folder table)_");
}

return expFormat("Table paths:\n. " + result.join("\n. "));
```
__
tbl list - Get a list of the registered table paths.


__
```
^tbl clearpaths$
```
__
```js
const count = Object.keys(_inlineScripts.state.sessionState.tablefiles.paths).length;

// Confirm before removing
if (!popups.confirm("Confirm clearing all of <b>" + count + "</b> table path(s)."))
{
	return;
}

// Clear the paths set
_inlineScripts.state.sessionState.tablefiles.paths = {};

return expFormat("All of __" + count + "__ path(s) cleared.");
```
__
tbl clearpaths - Remove all table paths.
***


__
```
^tbl roll$
```
__
```js
// Show the table-roller custom popup
const result = popups.custom(
	"Select a table to pick from", _inlineScripts.tablefiles.rollPopup);
if (result === null) { return null; }

// Return the result of the table-roller custom popup.  Don't use expFormat() since
// this could very well be used inline.
return result;
```
__
tbl roll - Get random results from one of the registered tables.  Shows a popup to allow selecting the table and how to get results from it.


__
```
^tbl roll ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ?(.*)$
```
__
```js
// Remove any quotes around the table file parameter
$1 = $1.replaceAll(/^\"|\"$/g, "");

// Make parameters case-insensitive
$2 = $2.toLowerCase();

// Allow format values without quotes (by adding missing quotes here)
$2 = $2.replace(/format:\s*([^,]+)/, "format: \"$1\"");

// The "parameters" parameter is basically an object definition without the curlys.
// Load it as an object.  Early out on failure.
let parameters;
try
{
	parameters = (new Function("return { " + $2 + "}"))();
}
catch(e)
{
	return expFormat([ "", "No table rolled.  Unable to parse parameters." ]);
}

// The official formats are "commas", "bullets" and "periods".  Alow "comma", "bullet" and "period", as well as untrimmed versions.
if (parameters.format)
{
	parameters.format = parameters.format.trim();
	if (parameters.format === "comma") { parameters.format = "commas"; }
	if (parameters.format === "bullet") { parameters.format = "bullets"; }
	if (parameters.format === "period") { parameters.format = "periods"; }
}

// Load the "parameters" parameter over the default parameters object.  This provides
// default parameters for anything that's not specified in the "parameters" parameter
const defaultParameters =
	{
		count: 1, uniquepicks: false, format: "commas", useexpansionformat: false,
		isfoldertable: false, useconfig: true, startLine: 0, itemformat: ""
	};
parameters = Object.assign({}, defaultParameters, parameters);

// Take the "isFolderTable" parameter and change the table path to match
if (!$1.startsWith(HEADING_FTABLE) && parameters.isfoldertable)
{
	$1 = HEADING_FTABLE + $1;
}

// Try the $1 as a path, else try as table title, else try as table file basename,
// else early out
// NOTE - This needs to happen this late for standardized folder-table knowledge
let foundPath = false;
if (!foundPath)
{
	const barePath = removeTildeHeader($1);
	const file =
		app.vault.fileMap[barePath] || app.vault.fileMap[[barePath + ".md"]];
	if (file && !!file.children === barePath.startsWith(HEADING_FTABLE))
	{
		foundPath = true;
	}
}
if (!foundPath)
{
	for (const key in _inlineScripts.state.sessionState.tablefiles.configuration)
	{
		const configuration =
			_inlineScripts.state.sessionState.tablefiles.configuration[key];
		if ($1.toLowerCase() === configuration.title?.toLowerCase())
		{
			$1 = key;
			foundPath = true;
			break;
		}
	}
}
if (!foundPath)
{
	const tableFiles = getAllTableFiles();
	for (const tableFile of tableFiles)
	{
		if ($1.toLowerCase() === tableFile.basename.toLowerCase())
		{
			$1 = tableFile.path;
			foundPath = true;
			break;
		}
	}
}
if (!foundPath)
{
	return expFormat(
		[ "", "Table not rolled.  __" + $1 + "__ not recognized as a table." ]);
}

// Expand to the result of the table roll specified by the parameters.  Don't use
// expFormat() since this could very well be used inline.
return await rollTable(
	$1, parameters.count, parameters.uniquepicks, parameters.format,
	parameters.useexpansionformat, parameters.useconfig, parameters.startLine,
	parameters.itemformat);
```
__
tbl roll {table file: path text} {parameters: text, default: ""} - Get random results from table {table file}.  If provided, {parameters} can alter the results.  {parameters} is expected to be a comma-separated list of parameters in "key: value" form.  Here are accepted parameters:
	- __count__ - A positive integer defaulting to 1.  Determines number of items picked.
	- __uniquePicks__ - "true" or "false", defaulting to "false".  If true, each item can be picked only once for this roll.
	- __format__ - "commas", "bullets" or "periods", defaulting to "commas".  Determines the format of the output.
	- __useExpansionFormat__ - "true" or "false", defaulting to "false".  If true, the result is outputted in the standard expansion format.
	- __isFolderTable__ - "true" or "false", defaulting to "false".  If true, {table file} must be a folder path, and the result is picks from the files within it.
	- __useConfig__ - "true" or "false", defaulting to "true".  If true, __startLine__ and __itemFormat__ are determined by the current configuration for the given table file.
	- __startLine__ - A positive integer defaulting to 0.  Defines what line the table starts on in {table file}.  This is ignored for folder-tables.
	- __itemFormat__ - A regex string defaulting to `(.*)`.  Determines what part of each item is printed out, as well as what part of each item is used as the weight value.  The default prints out the entire item.


__
```
^tbl reroll$
```
__
```js
// Get the prior roll parameters
// priorRoll = { tablePath, count, uniquePicks, format, useExpFormat, useConfig,
// startLine, itemFormat };
const p = _inlineScripts.tablefiles.priorRoll;

// If there wasn't a prior roll, early out
if (!p)
{
	return expFormat([ "", "Table not re-rolled.  No prior roll to re-roll." ]);
}

// Run a new roll with the priorRoll values
return await rollTable(
	p.tablePath, p.count, p.uniquePicks, p.format, p.useExpFormat, p.useConfig,
	p.startLine, p.itemFormat);
```
__
tbl reroll - Re-rolls the last _successful_ table roll.
