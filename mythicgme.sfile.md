---
obsidianUIMode: preview
---

Shortcuts for Mythic Game Master Emulator.  Mythic GME is an excellent "GM emulator" system for solo and GM'less gaming.  It was designed by Tana Pigeon.  You can find more info about Mythic GME at [wordmill games](http://wordmillgames.com/mythic-game-master-emulator.html).

Incompatible with __mythicv2.sfile__.  If __mythicv2.sfile__ comes before __mythicgme.sfile__ in the shortcut-list, then __mythicgme.sfile__ will be disabled.

Uses __state.sfile__ shortcut-file (optional).
It uses this to save & load the chaos value and the scene count.

Uses __lists.sfile__ shortcut-file (optional).
Adds and uses lists for pcs, npcs and threads.
This requires that __lists.sfile__ comes before __mythicgme.sfile__ in the shortcut-list.
If the pc, npc and thread lists have items, then the __event__ and __detail__ shortcuts will incorporate them into their results.


__
```
^sfile setup$
```
__
```js
if (expand("help").contains("\n    - mythicv2"))
{
	print("The mythicgme shortcut-file is disabled as it is incompatible with the mythicv2 shortcut-file.");
	return true;
}

window._inlineScripts ||= {};
window._inlineScripts.state ||= {};
window._inlineScripts.state.mythicgme ||= {};
window._inlineScripts.state.mythicgme.chaos ||= 5;
window._inlineScripts.state.mythicgme.scene ||= 1;

window._inlineScripts.state.lists ||= {};
window._inlineScripts.state.lists.pcs ||= { type: "basic", content: [] };
window._inlineScripts.state.lists.npcs ||= { type: "basic", content: [] };
window._inlineScripts.state.lists.threads ||= { type: "basic", content: [] };

window._inlineScripts.listeners ||= {};
window._inlineScripts.listeners.state ||= {};
window._inlineScripts.listeners.state.onReset ||= {};
window._inlineScripts.listeners.state.onReset.mythicgme ||= expand =>
{
	expand("reset mythicgme");
};
```
__
Disables mythicgme if mythicv2 is already registered.  Sets up a state variable for mythicgme.  Sets up lists for mythicgme.  Sets up a callback for the state "reset" event in order to to reset mythicgme.


__
```
^sfile shutdown$
```
__
```js
delete window._inlineScripts.listeners?.state?.onReset?.mythicgme;
```
__
Unregisters event callbacks.


__
```
^reset mythic(?:gme)?$
```
__
```js
window._inlineScripts ||= {};
window._inlineScripts.state ||= {};
window._inlineScripts.state.mythicgme ||= {};
window._inlineScripts.state.mythicgme.chaos = 5;
window._inlineScripts.state.mythicgme.scene = 1;
window._inlineScripts.state.lists ||= {};
window._inlineScripts.state.lists.pcs = { type: "basic", content: [] };
window._inlineScripts.state.lists.npcs = { type: "basic", content: [] };
window._inlineScripts.state.lists.threads = { type: "basic", content: [] };
return "***\n\n\n### SCENE " + window._inlineScripts.state.mythicgme.scene + "\n- Setup:\n    - ";
```
__
reset mythicgme - Reset mythic state to defaults and displays scene heading.
        Alternative shortcut: __reset mythic__.
***


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
```
__
Some useful functions


__
```
^f(?:ate)?\s?(|-4|-3|-2|-1|0|1|2|3|4|5|6)$
```
__
```js
const ODDS = ["impossible","no way","very unlikely","unlikely","50/50","somewhat likely","likely","very likely","near sure thing","sure thing","has to be"];
const FATE_CHART = [
[ [ 10, 50, 91],[  5, 25, 86],[  3, 15, 84],[  2, 10, 83],[  1,  5, 82],[  1,  5, 82],[  0,  0, 81],[  0,  0, 81],[  0,-20, 77] ],
[ [ 15, 75, 96],[ 10, 50, 91],[  7, 35, 88],[  5, 25, 86],[  3, 15, 84],[  2, 10, 83],[  1,  5, 82],[  1,  5, 82],[  0,  0, 81] ],
[ [ 16, 85, 97],[ 13, 65, 94],[ 10, 50, 91],[  9, 45, 90],[  5, 25, 86],[  3, 15, 84],[  2, 10, 83],[  1,  5, 82],[  1,  5, 82] ],
[ [ 18, 90, 99],[ 15, 75, 96],[ 11, 55, 92],[ 10, 50, 91],[  7, 35, 88],[  4, 20, 85],[  3, 15, 84],[  2, 10, 83],[  1,  5, 82] ],
[ [ 19, 95,100],[ 16, 85, 97],[ 15, 75, 96],[ 13, 65, 94],[ 10, 50, 91],[  7, 35, 88],[  5, 25, 86],[  3, 15, 84],[  2, 10, 83] ],
[ [ 19, 95,100],[ 18, 90, 99],[ 16, 85, 97],[ 16, 80, 97],[ 13, 65, 94],[ 10, 50, 91],[  9, 45, 90],[  5, 25, 86],[  4, 20, 85] ],
[ [ 20,100,  0],[ 19, 95,100],[ 18, 90, 99],[ 16, 85, 97],[ 15, 75, 96],[ 11, 55, 92],[ 10, 50, 91],[  7, 35, 88],[  5, 25, 86] ],
[ [ 21,105,  0],[ 19, 95,100],[ 19, 95,100],[ 18, 90, 99],[ 16, 85, 97],[ 15, 75, 96],[ 13, 65, 94],[ 10, 50, 91],[  9, 45, 90] ],
[ [ 23,115,  0],[ 20,100,  0],[ 19, 95,100],[ 19, 95,100],[ 18, 90, 99],[ 16, 80, 97],[ 15, 75, 96],[ 11, 55, 92],[ 10, 50, 91] ],
[ [ 25,125,  0],[ 22,110,  0],[ 19, 95,100],[ 19, 95,100],[ 18, 90, 99],[ 16, 85, 97],[ 16, 80, 97],[ 13, 65, 94],[ 11, 55, 92] ],
[ [ 26,145,  0],[ 26,130,  0],[ 20,100,  0],[ 20,100,  0],[ 19, 95,100],[ 19, 95,100],[ 18, 90, 99],[ 16, 85, 97],[ 16, 80, 97] ] ];
const r = roll(100);
$1 = Number($1) | 0;
const ranges = FATE_CHART[$1+4][9-window._inlineScripts.state.mythicgme.chaos];
let result =
	(r <= ranges[0]) ? "EXTEREME YES" :
	(r <= ranges[1]) ? "YES" :
	(r <= ranges[2]) ? "NO" :
	"EXTREME NO";
let eventOutput = "";
if (Math.trunc(r/10) == r%10 &&
    r%10 < window._inlineScripts.state.mythicgme.chaos)
{
	eventOutput =
		"\nevent - " + expand("event")[1];
}
return "Fate check (" + ODDS[$1+4] + "):\n" + result + eventOutput + "\n\n";
```
__
fate {odds: optional (~0), -4 to 6} - Make a fate check based on {odds}.  Odds can be from -4 (impossible) to 6 (has to be) and defaults 0 (50/50).
        Alternative shortcut: __f {odds}__.


__
```
^f(?:ate)?\s?(.*)$
```
__
```js
const INPUT_ODDS =
{
	"impossible": -4, "no way": -3, "very unlikely": -2, "unlikely": -1, "50/50": 0, "somewhat likely": 1, "likely": 2, "very likely": 3, "near sure thing": 4, "sure thing": 5, "has to be": 6
};
return expand("fate " + (INPUT_ODDS[$1.trim().toLowerCase()] ?? 0));
```
__
fate {odds: optional ("50/50"), text} - Make a fate check based on {odds}: a specific text, such as "impossible", "sure thing", etc.
        Alternative shortcut: __f {odds}__.

__
```
^scene$
```
__
```js
return "The current scene is " + window._inlineScripts.state.mythicgme.scene + ".\n\n";
```
__
scene - Show the current scene.


__
```
^scene (1|-1)$
```
__
```js
let result =
	($1 === "1") ? expand("chaos++")[0] :
	($1 === "-1") ? expand("chaos--")[0] :
	"Chaos is unchanged at " + expand("chaos")[1];
result += "\n\n\n***\n\n\n";
window._inlineScripts.state.mythicgme.scene++;
result += "### SCENE " + window._inlineScripts.state.mythicgme.scene;
let chk = roll(10);
if (chk <= window._inlineScripts.state.mythicgme.chaos)
{
	if (chk % 2)
	{
		result +=
			"\n- Scene replaced:\n" +
			"    - event - " + expand("event")[1];
	}
	else
	{
		result += "\n- Scene modified";
	}
}
return result + "\n- setup:\n    - ";
```
__
scene {chaosAdjust: required, -1 or 1} - Shift the chaos value by {chaosAdjust}, then increment the current scene and run a scene check.
***


__
```
^event$
```
__
```js
const FOCUS_TABLE = [ ["REMOTE",7],["NPC ACTS",28,"npcs"],["NEW NPC",35],["THREAD ADVANCE",45,"threads"],["THREAD LOSS",52,"threads"],["THREAD END",55,"threads"],["PC NEGATIVE",67,"pcs"],["PC POSITIVE",75,"pcs"],["AMBIGUOUS",83],["NPC NEGATIVE",92,"npcs"],["NPC POSITIVE",100,"npcs"] ];
let result = aPickWeight(FOCUS_TABLE);
let focus = expand("lists pick " + (result[2] || ""));
focus = (focus.length < 2) ? "" : (" (" + focus[1] + ")");
let meaning = expand("meaning")[1];
return [ "Event:\n", result[0] + focus + " - " + meaning, "\n\n" ];
```
__
event - Make an event check.


__
```
^meaning$
```
__
```js
const ACTION_TABLE = ["ATTAINMENT","STARTING","NEGLECT","FIGHT","RECRUIT","TRIUMPH","VIOLATE","OPPOSE","MALICE","COMMUNICATE","PERSECUTE","INCREASE","DECREASE","ABANDON","GRATIFY","INQUIRE","ANTAGONISE","MOVE","WASTE","TRUCE","RELEASE","BEFRIEND","JUDGE","DESERT","DOMINATE","PROCRASTINATE","PRAISE","SEPARATE","TAKE","BREAK","HEAL","DELAY","STOP","LIE","RETURN","IMMITATE","STRUGGLE","INFORM","BESTOW","POSTPONE","EXPOSE","HAGGLE","IMPRISON","RELEASE","CELEBRATE","DEVELOP","TRAVEL","BLOCK","HARM","DEBASE","OVERINDULGE","ADJOURN","ADVERSITY","KILL","DISRUPT","USURP","CREATE","BETRAY","AGREE","ABUSE","OPPRESS","INSPECT","AMBUSH","SPY","ATTACH","CARRY","OPEN","CARELESSNESS","RUIN","EXTRAVAGANCE","TRICK","ARRIVE","PROPOSE","DIVIDE","REFUSE","MISTRUST","DECEIVE","CRUELTY","INTOLERANCE","TRUST","EXCITEMENT","ACTIVITY","ASSIST","CARE","NEGLIGENCE","PASSION","WORK_HARD","CONTROL","ATTRACT","FAILURE","PURSUE","VENGEANCE","PROCEEDINGS","DISPUTE","PUNISH","GUIDE","TRANSFORM","OVERTHROW","OPPRESS","CHANGE"];
const SUBJECT_TABLE = ["GOALS","DREAMS","ENVIRONMENT","OUTSIDE","INSIDE","REALITY","ALLIES","ENEMIES","EVIL","GOOD","EMOTIONS","OPPOSITION","WAR","PEACE","THE_INNOCENT","LOVE","THE_SPIRITUAL","THE_INTELLECTUAL","NEW_IDEAS","JOY","MESSAGES","ENERGY","BALANCE","TENSION","FRIENDSHIP","THE_PHYSICAL","A_PROJECT","PLEASURES","PAIN","POSSESSIONS","BENEFITS","PLANS","LIES","EXPECTATIONS","LEGAL_MATTERS","BUREAUCRACY","BUSINESS","A_PATH","NEWS","EXTERIOR_FACTORS","ADVICE","A_PLOT","COMPETITION","PRISON","ILLNESS","FOOD","ATTENTION","SUCCESS","FAILURE","TRAVEL","JEALOUSY","DISPUTE","HOME","INVESTMENT","SUFFERING","WISHES","TACTICS","STALEMATE","RANDOMNESS","MISFORTUNE","DEATH","DISRUPTION","POWER","A_BURDEN","INTRIGUES","FEARS","AMBUSH","RUMOR","WOUNDS","EXTRAVAGANCE","A_REPRESENTATIVE","ADVERSITIES","OPULENCE","LIBERTY","MILITARY","THE_MUNDANE","TRIALS","MASSES","VEHICLE","ART","VICTORY","DISPUTE","RICHES","STATUS_QUO","TECHNOLOGY","HOPE","MAGIC","ILLUSIONS","PORTALS","DANGER","WEAPONS","ANIMALS","WEATHER","ELEMENTS","NATURE","THE_PUBLIC","LEADERSHIP","FAME","ANGER","INFORMATION"];
let result = aPick(ACTION_TABLE) + " _(of)_ " + aPick(SUBJECT_TABLE);
return [ "Meaning:\n", result, "\n\n" ];
```
__
meaning - Roll on the meaning tables.
***


__
```
^chaos$
```
__
```js
return [ "Chaos is __", window._inlineScripts.state.mythicgme.chaos, "__.\n\n" ];
```
__
chaos - Show the current chaos value.


__
```
^chaos--$
```
__
```js
window._inlineScripts.state.mythicgme.chaos--;
if (window._inlineScripts.state.mythicgme.chaos < 1)
{
	window._inlineScripts.state.mythicgme.chaos = 1;
	return [ "Chaos remains at __1__ (hit minimum).", "\n\n" ];
}
return [ "Chaos lowered to __" + window._inlineScripts.state.mythicgme.chaos + "__.", "\n\n" ];
```
__
chaos-- - Decrease the chaos value by 1 (minimum of 1).


__
```
^chaos\+\+$
```
__
```js
window._inlineScripts.state.mythicgme.chaos++;
if (window._inlineScripts.state.mythicgme.chaos > 9)
{
	window._inlineScripts.state.mythicgme.chaos = 9;
	return [ "Chaos remains at __9__ (hit maximum).", "\n\n" ];
}
return [ "Chaos raised to __" + window._inlineScripts.state.mythicgme.chaos + "__.", "\n\n" ];
```
__
chaos++ - Increase the chaos value by 1 (maximum of 9).


__
```
^chaos=([1-9])$
```
__
```js
window._inlineScripts.state.mythicgme.chaos = $1;
return "Chaos set to __" + $1 + "__.\n\n";
```
__
chaos={value: required, 1 to 9} - Set the chaos value to {value}.


__
```
^lists? pick ((?:[_a-zA-Z][_a-zA-Z0-9]*)?)((?: [1-9][0-9]*)?)$
```
__
```js
return [];
```
__
hidden - A placeholder shortcut in case the shortcut-file lists.sfile isn't available.
