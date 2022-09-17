Adds an Obsidian panel type that lets you view card-piles.


__
__
```js
const CARDPILE_VIEW_TYPE = "inline-scripts-cardpile-view";
```
__
Helper scripts - global constant


__
```
^sfile setup$
```
__
```js
const confirmObjectPath =
	_inlineScripts.inlineScripts.helperFncs.confirmObjectPath;
confirmObjectPath(
	"_inlineScripts.cards.listeners.onPileListChanged.cards_pileViewer",
	function()
	{
		const leaves = app.workspace.getLeavesOfType(CARDPILE_VIEW_TYPE);
		for (const leaf of leaves)
		{
			leaf.view.refreshPileList();
		}
	});
confirmObjectPath(
	"_inlineScripts.cards.listeners.onPileChanged.cards_pileViewer",
	function()
	{
		const leaves = app.workspace.getLeavesOfType(CARDPILE_VIEW_TYPE);
		for (const leaf of leaves)
		{
			leaf.view.refreshPile();
		}
	});

const plugin = _inlineScripts.inlineScripts.plugin;

if (!_inlineScripts.inlineScripts.hasRegisteredCardPileView)
{
	_inlineScripts.inlineScripts.hasRegisteredCardPileView = true;
	class CardPileView extends plugin.getObsidianInterfaces().ItemView
	{
		pileSelect;
		zoomSelect;
		cardDisplay;

		constructor(leaf)
		{
			super(leaf);
		}
	
		load()
		{
			const root = this.containerEl.children[1];
			root.style.overflow = "unset";
			const header = document.createElement('div');
			header.classList.add("iscript_pileViewer_header");
			root.appendChild(header);

			this.pileSelect = document.createElement("select");
			this.pileSelect.classList.add("iscript_pileViewer_select");
			this.pileSelect.options[this.pileSelect.options.length] =
				new Option("");
			this.pileSelect.onchange = () =>
			{
				this.refreshPile(true);
			}
			header.appendChild(this.pileSelect);

			this.zoomSelect = document.createElement("select");
			this.zoomSelect.options[this.zoomSelect.options.length] =
				new Option("25%");
			this.zoomSelect.options[this.zoomSelect.options.length] =
				new Option("50%");
			this.zoomSelect.options[this.zoomSelect.options.length] =
				new Option("75%");
			this.zoomSelect.options[this.zoomSelect.options.length] =
				new Option("100%", undefined, undefined, true);
			this.zoomSelect.options[this.zoomSelect.options.length] =
				new Option("200%");
			this.zoomSelect.options[this.zoomSelect.options.length] =
				new Option("300%");
			this.zoomSelect.onchange = () =>
			{
				this.refreshPile(true);
			}
			header.appendChild(this.zoomSelect);

			this.cardDisplay = document.createElement("div");
			this.cardDisplay.classList.add("iscript_pileViewer_content");
			root.appendChild(this.cardDisplay);

			this.refreshPileList();
		}

		refreshPileList()
		{
			const names = Object.keys(
				_inlineScripts.state.sessionState.cards.piles);
			names.sort();
			const oldValue = this.pileSelect.value;
			this.pileSelect.options.length = 0;
			this.pileSelect.options
				[this.pileSelect.options.length] =
				new Option("");
			for (const name of names)
			{
				this.pileSelect.options[this.pileSelect.options.length] =
					new Option(name || "<untitled>");
			}
			this.pileSelect.value = oldValue;
			if (this.pileSelect.value !== oldValue)
			{
				this.refreshPile(true);
			}
		}

		refreshPile(force)
		{
			if (_inlineScripts.cards.listeners.changedPile !==
			    this.pileSelect.value && !force &&
			    _inlineScripts.cards.listeners.changedPile)
			{
				return;
			}
			this.cardDisplay.innerText = "";
			if (!this.pileSelect.value) { return; }
			const pileName =
				this.pileSelect.value === "<untitled>" ?
				"" : this.pileSelect.value;
			const cards =
				_inlineScripts.state.sessionState.cards.piles
				[pileName].cards;
			let display = "";
			for (const card of cards)
			{
				const src =
					card.isFaceDown ? _inlineScripts.cards.backImage :
					app.vault.getResourcePath(app.vault.fileMap[card.path]);
				const cls =
					(card.isRotated && !card.isFaceDown) ?
					"rotated" : "";
				const zoom = Number(this.zoomSelect.value.slice(0,-1)) / 100.0;
				const style =
					"width:" + (card.width * zoom) + "px;height:" +
					(card.width * card.aspect * zoom) + "px";
				display +=
					"<img src='" + src + "' class='" + cls +
					"' style='" + style + "'/> ";
			}
			this.cardDisplay.innerHTML = display;
		}
	
		getViewType()
		{
			return CARDPILE_VIEW_TYPE;
		}
	
		getDisplayText()
		{
			return "Inline Scripts - Card-pile view";
		}
	
		getIcon()
		{
			return CARDPILE_VIEW_TYPE;
		}
	}
	plugin.registerView(CARDPILE_VIEW_TYPE, leaf => new CardPileView(leaf));
}
_inlineScripts.inlineScripts.helperFncs.addCss("cards_pileViewer", ".iscript_pileViewer_header { display: flex; margin-bottom: 0.5em; } .iscript_pileViewer_select { flex-grow: 1; margin-right: 0.25em; } .rotated { transform: scaleX(-1) scaleY(-1); } .iscript_pileViewer_content { overflow-y: scroll; height: calc(100% - 0.75em); }");

plugin.getObsidianInterfaces().addIcon(CARDPILE_VIEW_TYPE, `
<svg width="512" height="512" viewBox="0 0 512 512" xmlns="http://www.w3.org/2000/svg" xmlns:svg="http://www.w3.org/2000/svg" version="1.1">
 <g class="layer">
  <title>Layer 1</title>
  <g id="layer1">
   <text fill="currentcolor" font-family="Plantagenet Cherokee" font-size="20px" id="text3042" transform="matrix(0.337701, 0, 0, 0.337701, -1.70731, 69.2455)" x="1017.71" xml:space="preserve" y="-25.65"/>
   <g id="g5042" transform="matrix(0.311337, 0, 0, 0.311337, 11.6005, 17.4561)">
    <g fill="currentcolor" id="g5558" transform="matrix(0.94393, 0, 0, 0.94393, 14.85, 31.044)">
     <g fill="currentcolor" id="g4929" transform="matrix(-1.7171, 0.99137, 0.99137, 1.7171, 2387.6, -1958)">
      <path d="m1467.18,247.37c1.29,-0.73 2.69,-1.3 4.19,-1.7l14.08,-3.77l12.64,-7.3c5.37,-3.1 11.64,-3.12 16.8,-0.6l21.47,-5.75c9.58,-2.57 19.36,3.08 21.92,12.66l28.14,105c2.56,9.58 -3.09,19.36 -12.66,21.93l-32.53,8.72l-0.06,0.03l-23.81,23.81c-7.01,7.01 -18.31,7.01 -25.32,0l-76.86,-76.86c-7.01,-7.02 -7.02,-18.31 0,-25.33l15.71,-15.71c0.4,-5.73 3.55,-11.15 8.92,-14.25l12.64,-7.3l10.31,-10.31c1.22,-1.22 2.58,-2.22 4.01,-3.02c0.14,-0.08 0.27,-0.17 0.41,-0.25zm25.64,2.82l-3.29,1.9l59.3,59.31l-31.55,-54.66c-0.28,-0.48 -0.59,-0.95 -0.9,-1.4c-0.04,-0.04 -0.07,-0.09 -0.11,-0.14c-0.01,-0.01 -0.01,-0.03 -0.02,-0.04c-0.09,-0.13 -0.19,-0.25 -0.29,-0.37c-0.09,-0.13 -0.19,-0.25 -0.28,-0.37c-0.17,-0.21 -0.35,-0.42 -0.53,-0.62c-0.02,-0.03 -0.04,-0.06 -0.07,-0.09c-0.14,-0.15 -0.28,-0.3 -0.43,-0.45c-0.05,-0.05 -0.09,-0.11 -0.14,-0.16c-0.1,-0.1 -0.2,-0.19 -0.3,-0.29c-0.13,-0.12 -0.25,-0.25 -0.38,-0.37c-0.09,-0.08 -0.19,-0.16 -0.29,-0.24c-0.11,-0.1 -0.22,-0.2 -0.33,-0.29c-0.15,-0.13 -0.31,-0.25 -0.47,-0.37c-0.08,-0.06 -0.15,-0.11 -0.23,-0.17c-0.12,-0.09 -0.24,-0.19 -0.36,-0.28c-0.14,-0.1 -0.29,-0.2 -0.43,-0.29c-0.06,-0.04 -0.11,-0.07 -0.17,-0.11c-0.2,-0.13 -0.41,-0.27 -0.62,-0.4c-0.03,-0.02 -0.07,-0.03 -0.1,-0.05c-0.17,-0.1 -0.35,-0.19 -0.52,-0.29c-0.19,-0.1 -0.37,-0.21 -0.56,-0.3c-0.09,-0.04 -0.17,-0.08 -0.25,-0.12c-0.11,-0.05 -0.22,-0.09 -0.33,-0.14c-0.17,-0.08 -0.34,-0.16 -0.52,-0.23c-0.12,-0.06 -0.24,-0.11 -0.37,-0.16c-0.13,-0.05 -0.27,-0.11 -0.4,-0.16c-0.11,-0.04 -0.23,-0.06 -0.34,-0.1c-0.21,-0.07 -0.43,-0.14 -0.65,-0.21c-0.03,-0.01 -0.04,-0.03 -0.06,-0.04c-0.27,-0.08 -0.55,-0.14 -0.82,-0.21c-4.32,-1.05 -9.02,-0.49 -13.19,1.91zm-22.92,14.8c-0.22,0.12 -0.44,0.25 -0.65,0.38c-1.27,0.75 -2.47,1.68 -3.56,2.78l-33,33c-7.01,7.01 -7.02,18.3 -0.01,25.31l56.43,56.44c7.01,7.01 18.31,7.01 25.32,-0.01l33,-33c7.01,-7.01 7.02,-18.29 0.01,-25.31l-56.43,-56.43c-5.74,-5.74 -14.33,-6.79 -21.11,-3.16zm53.99,-21.5l40.28,69.77l-15.5,-57.86c-2.57,-9.58 -12.35,-15.23 -21.93,-12.67l-2.85,0.76zm-72.08,45.7c15.18,12.32 38.24,20.45 50.49,14.79c5.37,-2.47 12.13,-0.09 16.63,4.41c6.69,6.69 6.68,17.53 -0.01,24.22c-5.03,5.03 -12.41,6.27 -18.61,3.73c7.56,5.12 16.32,9.18 24.54,11.25l-14.68,14.68c-2.09,-8.32 -6.19,-17.19 -11.4,-24.82c2.71,6.26 1.51,13.8 -3.6,18.92c-6.69,6.68 -17.53,6.68 -24.22,-0.01c-4.48,-4.48 -6.91,-11.21 -4.43,-16.53c5.71,-12.27 -2.35,-35.4 -14.71,-50.64z" fill="currentcolor" id="rect4935"/>
     </g>
    </g>
   </g>
   <text fill="currentcolor" font-family="Plantagenet Cherokee" font-size="20px" id="text4266" transform="matrix(0.337701, 0, 0, 0.337701, -1.70731, 69.2455)" x="554.36" xml:space="preserve" y="-10.5"/>
  </g>
 </g>
</svg>`);
```
__
Sets up this shortcut-file


__
```
^sfile shutdown$
```
__
```js
delete _inlineScripts.cards?.listeners?.onPileListChanged?.cards_pileViewer;
delete _inlineScripts.cards?.listeners?.onPileChanged?.cards_pileViewer;
_inlineScripts.inlineScripts.helperFncs.removeCss("cards_pileViewer");
app.workspace.detachLeavesOfType(CARDPILE_VIEW_TYPE);
```
__
Shuts down this shortcut-file


__
```
^cards? open viewer$
```
__
```js
const plugin = _inlineScripts.inlineScripts.plugin;
await plugin.app.workspace.getRightLeaf(false).setViewState({ type: CARDPILE_VIEW_TYPE });
```
__
cards open viewer - Open a panel for viewing card-piles.