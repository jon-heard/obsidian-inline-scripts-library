---
obsidianUIMode: preview
---

Shortcuts for Mythic Variations 2.  Mythic GME, along with it's "Variations 2" supplement, is an excellent "GM emulator" system for solo and GM'less gaming.  It was designed by Tana Pigeon.  You can find more info about Mythic Variations 2 at [wordmill games](http://wordmillgames.com/mythic-variations-2.html).

Incompatible with __mythicgme.sfile__.  If __mythicgme.sfile__ comes before __mythicv2.sfile__ in the shortcut-list, then __mythicv2.sfile__ will be disabled.

Uses __state.sfile__ shortcut-file (optional).
It uses this to save & load the chaos value, the scene count, and "Details" mode.

Uses __lists.sfile__ shortcut-file (optional).
Adds and uses lists for pcs, npcs and threads.
This requires that __lists.sfile__ comes before __mythicv2.sfile__ in the shortcut-list.
If the pc, npc and thread lists have items, then the __event__ and __detail__ shortcuts will incorporate them into their results.


__
```
^sfile setup$
```
__
```js
if (expand("help").contains("\n    - mythicgme"))
{
	print("The mythicv2 shortcut-file is disabled as it is incompatible with the mythicgme shortcut-file.");
	return true;
}

window._inlineScripts ||= {};
window._inlineScripts.state ||= {};
window._inlineScripts.state.mythicv2 ||= {};
window._inlineScripts.state.mythicv2.chaos ||= 4;
window._inlineScripts.state.mythicv2.scene ||= 1;

window._inlineScripts.state.lists ||= {};
window._inlineScripts.state.lists.pcs ||= { type: "basic", content: [] };
window._inlineScripts.state.lists.npcs ||= { type: "basic", content: [] };
window._inlineScripts.state.lists.threads ||= { type: "basic", content: [] };

window._inlineScripts.mythicv2 ||= {};
window._inlineScripts.mythicv2.details ||= [];

window._inlineScripts.listeners ||= {};
window._inlineScripts.listeners.state ||= {};
window._inlineScripts.listeners.state.onReset ||= {};
window._inlineScripts.listeners.state.onReset.mythicv2 ||= function(expand)
{
	expand("reset mythicv2");
};
```
__
Disables mythicv2 if mythicgme is already registered.  Sets up a state variable for mythicv2.  Sets up lists for mythicv2.  Sets up a callback for the state "reset" event in order to to reset mythicv2.



__
```
^sfile shutdown$
```
__
```js
delete window._inlineScripts.listeners?.state?.onReset?.mythicv2;
```
__
Unregisters event callbacks.


__
```
^reset mythic(?:v2)?$
```
__
```js
window._inlineScripts ||= {};
window._inlineScripts.state ||= {};
window._inlineScripts.state.mythicv2 ||= {};
window._inlineScripts.state.mythicv2.chaos = 4;
window._inlineScripts.state.mythicv2.scene = 1;
window._inlineScripts.state.lists ||= {};
window._inlineScripts.state.lists.pcs = { type: "basic", content: [] };
window._inlineScripts.state.lists.npcs = { type: "basic", content: [] };
window._inlineScripts.state.lists.threads = { type: "basic", content: [] };
window._inlineScripts.mythicv2 ||= {};
window._inlineScripts.mythicv2.details ||= []; // Track the details of a shortcut
return "***\n\n\n### SCENE " + window._inlineScripts.state.mythicv2.scene + "\n- Setup:\n    - ";
```
__
reset mythicv2 - Reset mythic state to defaults and displays scene heading.
        Alternative shortcut: __reset mythic__.


__
```
^mythicv2 details(| [y|n])$
```
__
```js
if ($1)
{
	window._inlineScripts.state.mythicv2.showDetails = ($1 === " y");
}
return "Mythic details are " + (window._inlineScripts.state.mythicv2.showDetails ? "ENABLED" : "DISABLED") + "\n\n";
```
__
mythicv2 details {state: optional, y or n} - If {state} is given, assigns it to the mythicv2 "details" mode.  Otherwise, displays the current "details" mode.
***

__
__
```js
function roll(name, max)
{
	return addDetails(name, Math.trunc(Math.random() * max + 1));
}
function aPick(name, a) { return a[roll(name, a.length)-1]; }
function aPickWeight(name, a, wIndex, theRoll)
{
	wIndex = wIndex || 1;
	theRoll = theRoll || roll("",a.last()[wIndex]);
	addDetails(name, theRoll);
	for (const item of a)
	{
		if (item[wIndex] >= theRoll)
		{
			return item;
		}
	}
	return a.last();
}
function addDetails()
{
	for (let i = 0; i < arguments.length; i+= 2)
	{
		if (!arguments[i]) { continue; }
		window._inlineScripts.mythicv2.details.push(arguments[i] + "=" + arguments[i+1]);
	}
	return arguments[arguments.length - 1];
}
function getDetails()
{
	if (!window._inlineScripts.state.mythicv2.showDetails ||
	    !window._inlineScripts.mythicv2.details.length)
	{
		return "";
	}
	return "\n- _" + window._inlineScripts.mythicv2.details.join(" ") + "_";
}
clearDetailsIfUserTriggered = function()
{
	if (expansionInfo.isUserTriggered)
	{
		window._inlineScripts.mythicv2.details = [];
	}
}
function getChaosAdjust(multiplier)
{
	const chaos = Number(window._inlineScripts.state.mythicv2.chaos);
	const result = ( (chaos === 3) ? 2 : (chaos === 6) ? -2 : 0 ) * (multiplier || 1);
	return addDetails("chaosAdjust", result);
}
```
__
Some useful functions.  Suped up versions to incorporate  details mode.


__
```
^f(?:ate)?\s?(|-4|-3|-2|-1|0|1|2|3|4)\s?(|y|n)$
```
__
```js
clearDetailsIfUserTriggered();
const ODDS = ["impossible","no way","very unlikely","unlikely","50/50","likely","very likely","sure thing","has to be"];
$1 = Number($1) || 0;
let wanted = "n" ? false : true;
let fateRoll1 = roll("fateRoll1", 10);
let fateRoll2 = roll("fateRoll2", 10);
let chaosRoll = roll("chaosRoll", 10);
let result =
	fateRoll1 + fateRoll2 +
	addDetails("oddsAdjust", ($1) * 2) +
	getChaosAdjust($2==="n" ? -1 : 1);
result = result > 10 ? "YES" : "NO";

let isChaotic = (chaosRoll <= window._inlineScripts.state.mythicv2.chaos);
let isExtreme = isChaotic && !!(fateRoll1 % 2) && !!(fateRoll2 % 2);
let isEvent = isChaotic && !(fateRoll1 % 2) && !(fateRoll2 % 2);
if (isChaotic && fateRoll1 === fateRoll2) isExtreme = isEvent = true;

result = (isExtreme ? "EXTREME " : "") + result;
let evtText = isEvent ? ( "\nevent - " + expand("event")[1] ) : "";
return "Fate check (" + ODDS[$1 + 4] + "):\n" + result + evtText + getDetails() + "\n\n";
```
__
fate {odds: optional (~0), -4 to 4} {wanted: optional (y), y or n} - Make a fate check based on {odds}: a value from -4 (impossible) to 4 (has to be), defaulting to 0 {50/50}.
This fate check is also based on {wanted}: the desired outcome.  Used for the direction of the chaos modifier.
        Alternative shortcut: __f {odds} {wanted}__.


__
```
^f(?:ate)?\s?(.*)$
```
__
```js
clearDetailsIfUserTriggered();
const INPUT_ODDS =
{
	"impossible": -4, "no way": -3, "very unlikely": -2, "unlikely": -1, "50/50": 0, "likely": 1, "very likely": 2, "sure thing": 3, "has to be": 4
};
$1 = $1.trim().toLowerCase();
let wanted = " y";
if ($1.endsWith(" y"))
{
	$1 = $1.slice(0,-2);
}
else if ($1.endsWith(" n"))
{
	wanted = " n";
	$1 = $1.slice(0,-2);
}
const inputOdds = INPUT_ODDS[$1.trim()] || 0;
return expand("fate " + inputOdds + wanted);
```
__
fate {odds: optional ("50/50"), text} {wanted: optional (y), y or n} - Make a fate check based on {odds}: a specific text such as "impossible", "sure thing", etc.
This fate check is also based on {wanted}: the desired outcome.  Used for the direction of the chaos modifier.
        Alternative shortcut: __f {odds} {wanted}__.


__
```
^detail$
```
__
```js
clearDetailsIfUserTriggered();
let outcomes = [ ["ANGER",4],["SADNESS",5],["FEAR",6],["THREAD NEGATIVE",7,"threads"],["PC NEGATIVE",8,"pcs"],["FOCUS NPC",9,"npcs"],["NPC POSITIVE",10,"npcs"],["FOCUS PC",11,"pcs"],["NPC NEGATIVE",12,"npcs"],["FOCUS THREAD",13,"threads"],["PC POSITIVE",14,"pcs"],["THREAD POSITIVE",15,"threads"],["COURAGE",16],["HAPPINESS",17],["CALM",99] ];
let result = roll("roll1",10) + roll("roll2",10) + getChaosAdjust();
result = aPickWeight("", outcomes, 1, result);
let focus = expand("lists pick " + (result[2] || ""));
focus = (focus.length < 2) ? "" : (" (" + focus[1] + ")");
return "Detail:\n" + result[0] + focus + getDetails() + "\n\n";
```
__
detail - Make a detail check.


__
```
^event$
```
__
```js
clearDetailsIfUserTriggered();
let outcomes = [ ["REMOTE",7],["NPC ACTS",28,"npcs"],["NEW NPC",35,null,true],["THREAD ADVANCE",45,"threads"],["THREAD LOSS",52,"threads"],["THREAD END",55,"threads"],["PC NEGATIVE",67,"pcs"],["PC POSITIVE",75,"pcs"],["AMBIGUOUS",83],["NPC NEGATIVE",92,"npcs"],["NPC POSITIVE",100,"npcs"] ];
let result = aPickWeight("eventRoll", outcomes);
let focus = expand("lists pick " + (result[2] || ""));
focus = (focus.length < 2) ? "" : (" (" + focus[1] + ")");
let meaning = expand("meaning " + (result[3] ? "description" : "action"))[1];
return [ "Event:\n", result[0] + focus + " - " + meaning, getDetails(), "\n\n" ];
```
__
event - Make an event check.
***


__
```
^meaning(?:| action)$
```
__
```js
clearDetailsIfUserTriggered();
let value1 = ["ATTAINMENT","STARTING","NEGLECT","FIGHT","RECRUIT","TRIUMPH","VIOLATE","OPPOSE","MALICE","COMMUNICATE","PERSECUTE","INCREASE","DECREASE","ABANDON","GRATIFY","INQUIRE","ANTAGONISE","MOVE","WASTE","TRUCE","RELEASE","BEFRIEND","JUDGE","DESERT","DOMINATE","PROCRASTINATE","PRAISE","SEPARATE","TAKE","BREAK","HEAL","DELAY","STOP","LIE","RETURN","IMMITATE","STRUGGLE","INFORM","BESTOW","POSTPONE","EXPOSE","HAGGLE","IMPRISON","RELEASE","CELEBRATE","DEVELOP","TRAVEL","BLOCK","HARM","DEBASE","OVERINDULGE","ADJOURN","ADVERSITY","KILL","DISRUPT","USURP","CREATE","BETRAY","AGREE","ABUSE","OPPRESS","INSPECT","AMBUSH","SPY","ATTACH","CARRY","OPEN","CARELESSNESS","RUIN","EXTRAVAGANCE","TRICK","ARRIVE","PROPOSE","DIVIDE","REFUSE","MISTRUST","DECEIVE","CRUELTY","INTOLERANCE","TRUST","EXCITEMENT","ACTIVITY","ASSIST","CARE","NEGLIGENCE","PASSION","WORK_HARD","CONTROL","ATTRACT","FAILURE","PURSUE","VENGEANCE","PROCEEDINGS","DISPUTE","PUNISH","GUIDE","TRANSFORM","OVERTHROW","OPPRESS","CHANGE"];
let value2 = ["GOALS","DREAMS","ENVIRONMENT","OUTSIDE","INSIDE","REALITY","ALLIES","ENEMIES","EVIL","GOOD","EMOTIONS","OPPOSITION","WAR","PEACE","THE_INNOCENT","LOVE","THE_SPIRITUAL","THE_INTELLECTUAL","NEW_IDEAS","JOY","MESSAGES","ENERGY","BALANCE","TENSION","FRIENDSHIP","THE_PHYSICAL","A_PROJECT","PLEASURES","PAIN","POSSESSIONS","BENEFITS","PLANS","LIES","EXPECTATIONS","LEGAL_MATTERS","BUREAUCRACY","BUSINESS","A_PATH","NEWS","EXTERIOR_FACTORS","ADVICE","A_PLOT","COMPETITION","PRISON","ILLNESS","FOOD","ATTENTION","SUCCESS","FAILURE","TRAVEL","JEALOUSY","DISPUTE","HOME","INVESTMENT","SUFFERING","WISHES","TACTICS","STALEMATE","RANDOMNESS","MISFORTUNE","DEATH","DISRUPTION","POWER","A_BURDEN","INTRIGUES","FEARS","AMBUSH","RUMOR","WOUNDS","EXTRAVAGANCE","A_REPRESENTATIVE","ADVERSITIES","OPULENCE","LIBERTY","MILITARY","THE_MUNDANE","TRIALS","MASSES","VEHICLE","ART","VICTORY","DISPUTE","RICHES","STATUS_QUO","TECHNOLOGY","HOPE","MAGIC","ILLUSIONS","PORTALS","DANGER","WEAPONS","ANIMALS","WEATHER","ELEMENTS","NATURE","THE_PUBLIC","LEADERSHIP","FAME","ANGER","INFORMATION"];
let result = aPick("actionRoll1", value1) + " _(of)_ " + aPick("actionRoll2", value2);
return [ "Meaning (action):\n", result, getDetails(), "\n\n" ];
```
__
meaning action - Roll on the action meaning tables.
        Alternative shortcut: __meaning__.


__
```
^meaning description$
```
__
```js
clearDetailsIfUserTriggered();
let value1 = ["ABNORMALLY","ADVENTUROUSLY","AGGRESSIVELY","ANGRILY","ANXIOUSLY","AWKWARDLY","BEAUTIFULLY","BLEAKLY","BOLDLY","BRAVELY","BUSILY","CALMLY","CAREFULLY","CARELESSLY","CAUTIOUSLY","CEASELESSLY","CHEERFULLY","COMBATIVELY","COOLLY","CRAZILY","CURIOUSLY","DAINTILY","DANGEROUSLY","DEFIANTLY","DELIBERATELY","DELIGHTFULLY","DIMLY","EFFICIENTLY","ENERGETICALLY","ENORMOUSLY","ENTHUSIASTICALLY","EXCITEDLY","FEARFULLY","FEROCIOUSLY","FIERCELY","FOOLISHLY","FORTUNATELY","FRANTICALLY","FREELY","FRIGHTENINGLY","FULLY","GENEROUSLY","GENTLY","GLADLY","GRACEFULLY","GRATEFULLY","HAPPILY","HASTILY","HEALTHILY","HELPFULLY","HELPLESSLY","HOPELESSLY","INNOCENTLY","INTENSELY","INTERESTINGLY","IRRITATINGLY","JOVIALLY","JOYFULLY","JUDGEMENTALLY","KINDLY","KOOKILY","LAZILY","LIGHTLY","LOOSELY","LOUDLY","LOVINGLY","LOYALLY","MAJESTICALLY","MEANINGFULLY","MECHANICALLY","MISERABLY","MOCKINGLY","MYSTERIOUSLY","NATURALLY","NEATLY","NICELY","ODDLY","OFFENSIVELY","OFFICIALLY","PARTIALLY","PEACEFULLY","PERFECTLY","PLAYFULLY","POLITELY","POSITIVELY","POWERFULLY","QUAINTLY","QUARRELSOMELY","QUIETLY","ROUGHLY","RUDELY","RUTHLESSLY","SLOWLY","SOFTLY","SWIFTLY","THREATENINGLY","VERY","VIOLENTLY","WILDLY","YIELDINGLY"];
let value2 = ["ABANDONED","ABNORMAL","AMUSING","ANCIENT","AROMATIC","AVERAGE","BEAUTIFUL","BIZARRE","CLASSY","CLEAN","COLD","COLORFUL","CREEPY","CUTE","DAMAGED","DARK","DEFEATED","DELICATE","DELIGHTFUL","DIRTY","DISAGREEABLE","DISGUSTING","DRAB","DRY","DULL","EMPTY","ENORMOUS","EXOTIC","FADED","FAMILIAR","FANCY","FAT","FEEBLE","FEMININE","FESTIVE","FLAWLESS","FRESH","FULL","GLORIOUS","GOOD","GRACEFUL","HARD","HARSH","HEALTHY","HEAVY","HISTORICAL","HORRIBLE","IMPORTANT","INTERESTING","JUVENILE","LACKING","LAME","LARGE","LAVISH","LEAN","LESS","LETHAL","LONELY","LOVELY","MACABRE","MAGNIFICENT","MASCULINE","MATURE","MESSY","MIGHTY","MILITARY","MODERN","EXTRAVAGANT","MUNDANE","MYSTERIOUS","NATURAL","NONDESCRIPT","ODD","PALE","PETITE","POOR","POWERFUL","QUAINT","RARE","REASSURING","REMARKABLE","ROTTEN","ROUGH","RUINED","RUSTIC","SCARY","SIMPLE","SMALL","SMELLY","SMOOTH","SOFT","STRONG","TRANQUIL","UGLY","VALUABLE","WARLIKE","WARM","WATERY","WEAK","YOUNG"];
let result = aPick("descriptorRoll1", value1) + " " + aPick("descriptorRoll2", value2);
return [ "Meaning (description):\n", result, getDetails(), "\n\n" ];
```
__
meaning description - Roll on the description meaning tables.
***


__
```
^scene$
```
__
```js
return "The current scene is " + window._inlineScripts.state.mythicv2.scene + ".\n\n";
```
__
scene - Show the current scene.


__
```
^scene (1|-1)$
```
__
```js
clearDetailsIfUserTriggered();
let result =
	($1 === "1") ? expand("chaos++")[0] :
	($1 === "-1") ? expand("chaos--")[0] :
	"Chaos is unchanged at " + expand("chaos")[1];
result += "\n\n\n***\n\n\n";
window._inlineScripts.state.mythicv2.scene++;
result += "### SCENE " + window._inlineScripts.state.mythicv2.scene;
let chk = roll("sceneCheck", 10);
if (chk <= window._inlineScripts.state.mythicv2.chaos)
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
return result + getDetails() + "\n- setup:\n    - ";
```
__
scene {chaos adjust: required, -1 or 1} - Shift the chaos value by {chaosAdjust}, then increment the current scene and run a scene check.


__
```
^chaos$
```
__
```js
return [ "Chaos is __", window._inlineScripts.state.mythicv2.chaos, "__.\n\n" ];
```
__
chaos - Show the current chaos value.


__
```
^chaos--$
```
__
```js
window._inlineScripts.state.mythicv2.chaos--;
if (window._inlineScripts.state.mythicv2.chaos < 3)
{
	window._inlineScripts.state.mythicv2.chaos = 3;
	return [ "Chaos remains at __3__ (hit minimum).", "\n\n" ];
}
return [ "Chaos lowered to __" + window._inlineScripts.state.mythicv2.chaos + "__.", "\n\n" ];
```
__
chaos-- - Decrease the chaos value by 1 (minimum of 3).


__
```
^chaos\+\+$
```
__
```js
window._inlineScripts.state.mythicv2.chaos++;
if (window._inlineScripts.state.mythicv2.chaos > 6)
{
	window._inlineScripts.state.mythicv2.chaos = 6;
	return [ "Chaos remains at __6__ (hit maximum).", "\n\n" ];
}
return [ "Chaos raised to __" + window._inlineScripts.state.mythicv2.chaos + "__.", "\n\n" ];
```
__
chaos++ - Increase the chaos value by 1 (maximum of 6).


__
```
^chaos=([3-6])$
```
__
```js
window._inlineScripts.state.mythicv2.chaos = $1;
return "Chaos set to __" + $1 + "__.\n\n";
```
__
chaos={value: required, 3 to 6} - Set the chaos value to {value}, an integer from 3 to 6.
***


__
```
^descriptors?$
```
__
```js
clearDetailsIfUserTriggered();
return "Descriptors:\n" + "- personality:\n    - " + expand("meaning description")[1] + "\n- activity:\n    - " + expand("meaning action")[1] + getDetails() + "\n\n";
```
__
descriptors - Generates a personality and activity descriptor for an NPC.
        Alternative shortcut: __descriptor__.


__
```
^disposition (-?[0-3])$
```
__
```js
clearDetailsIfUserTriggered();
let outcomes = [ ["PASSIVE (-2)",5],["MODERATE (0)",10],["ACTIVE (+2)",15],["AGGRESSIVE (+4)",99] ];
let base = roll("roll1", 10) + roll("roll2", 10);
let result = base + addDetails("descriptorAdjust", Number($1) * 2);
result = aPickWeight("", outcomes, 1, result)[0] + "\n. BASE = " + base;
return "Disposition:\n. " + result + getDetails() + "\n\n";
```
__
disposition {descriptor count: required, -3 to 3} - Rolls for an NPC's disposition, modified by {descriptor count}, which represents the total of the NPC's activated descriptors.
    - Example: 2 positively activated descriptors and 1 negatively activated descriptor would make a {descriptor count} of __1+1+(-1) = 1__.


__
```
^disposition (-?[0-3]) ([2-9]|1[0-9]|20)$
```
__
```js
clearDetailsIfUserTriggered();
let outcomes = [ ["PASSIVE (-2)",5],["MODERATE (0)",10],["ACTIVE (+2)",15],["AGGRESSIVE (+4)",99] ];
let result =
		addDetails("base", Number($1)) +
		addDetails("descriptorAdjust", Number($2) * 2);
result = aPickWeight("", outcomes, 1, result);
return "Disposition:\n" + result[0] + getDetails() + "\n\n";
```
__
disposition {descriptorCount: required, -3 to 3} {base: required, 2 to 20} - Displays the NPC disposition determined by the {base} disposition, modified by {descriptorCount}.  {descriptor count} represents the total of the NPC's activated descriptors.
    - Example: 2 positively activated descriptors and 1 negatively activated descriptor would make a {descriptor count} of __1+1+(-1) = 1__.


__
```
^action (-2|0|2|4)$
```
__
```js
clearDetailsIfUserTriggered();
let outcomes1 = [ ["NEW ACTION BASED ON THEME",3],["CONTINUE CURRENT ACTION",5],["CONTINUE CURRENT ACTION, +2 DISPOSITION",6],["CONTINUE CURRENT ACTION, -2 DISPOSITION",7],[false,8,0],[false,9,-4],[false,10,4] ];
let outcomes2 = [ ["TALK / EXPOSITION",6],["NEUTRAL, DISCONNECTED ACTION",8],["ACTION BENEFITS THE PC",10],["GIVE SOMETHING TO THE PC",11],["TRY TO END ENCOUNTER",12],["CHANGE THEME",13],["CHANGE \"%1\" DESCRIPTOR. IT IS ACTIVATED & DECIDES NEXT ACTION.",14,true],["ACT OUT OF SELF INTEREST",17],["TAKE SOMETHING",18],["HURT THE PC",99] ];
let descriptors = ["IDENTITY","PERSONALITY","ACTIVITY"];
result = aPickWeight("table1Roll", outcomes1);
if (!result[0])
{
	result =
		roll("table2Roll1", 10) + roll("table2Roll2", 10) +
		addDetails("dispositionAdjust", Number($1)) +
		addDetails("tableAdjust", result[2]);
	result = aPickWeight("", outcomes2, 1, result);
	if (result[2])
	{
		result[0] = result[0].replace("%1", aPick("descriptorRoll", descriptors));
	}
	result[0] += "\n+2/0/-2 DISPOSITION TO MATCH ACTION.";
}
return "Action:\n" + result[0] + getDetails() + "\n\n";
```
__
action {dispositionAdjust: required, -2, 0, 2, 4} - Makes an NPC behavior check, modified by {dispositionAdjust}: the modifier of the NPC's disposition.


__
```
^lists? pick (|[_a-zA-Z][_a-zA-Z0-9]*)(| [1-9][0-9]*)$
```
__
```js
return [];
```
__
hidden - A placeholder shortcut in case the shortcut-file lists.sfile isn't available.
