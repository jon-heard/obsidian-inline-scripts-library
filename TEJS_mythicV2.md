Shortcuts for Mythic Variations 2.
Use TEJS_state to save & load the state of this shortcut file.

Enter the shortcut "state help" for reference.

~~
^mythic help$
~~
let result = "MYTHIC SHORTCUTS HELP\n";
result += "- mythic help - Display this help text.\n";
result += "- mythic reset - Reset mythic state to defaults and displays scene heading.\n";
result += "- detail - Make a details check.\n";
result += "- event - Make an event check.\n";
result += "- f[odds][wanted] - Make a fate check with the odds of [odds] (-4 to 4) and the preferred result of [wanted] (either 'n' or 'y').  The preferred result is optional and defaults to 'y'.  It specifies the direct of the chaos modifier.\n";
result += "- meaning action - Roll on the action meaning table.\n";
result += "- meaning discription - Roll on the description meaning table.\n";
result += "- meaning - Roll on the action meaning table. (short for \"meaning action\")\n";
result += "- list - Show a list of the lists.\n";
result += "- list [listName] - Show all items in the list named [listName].\n";
result += "- listadd [listName] [item] - Add [item] to the end of the list named [listName].\n";
result += "- listget [listName] - Get a random item from the list named [listName].\n";
result += "- listremove [listName] [item] - Remove last instance of [item] from the list named [listName].\n";
result += "- listremoveall [listName] - Remove the entire list of [listName].\n";
result += "- scene - Show the current scene.\n";
result += "- scene [chaosAdjust] - Shift the chaos value by [chaosAdjust] (1 or -1), increment the current scene, then output all.\n";
result += "- chaos - Show the current chaos value.\n";
result += "- chaos++ - Increase the chaos value by 1 (maximum of 6).\n";
result += "- chaos-- - Decrease the chaos value by 1 (minimum of 3).\n";
result += "- chaos=[value] - Set the chaos value to [value].\n";
result += "- descriptor - Rolls for a personality and activity.\n";
result += "- disposition [descriptorCount] - Rolls for a disposition, modified by [descriptorCount].\n";
result += "- disposition [base] [descriptorCount] - Displays a disposition given by the [base] disposition, modified by [descriptorCount].\n";
result += "- action [dispositionAdjust] - Action check, modified by [dispositionAdjust].\n";
return result + "\n";

~~
^tejs setup$
~~
window._tejsState ||= {};
window._tejsState.mythic ||= {};
window._tejsState.mythic.chaos ||= 4;
window._tejsState.mythic.scene ||= 1;
window._tejsState.mythic.lists ||= {};
window._tejsState.mythic.lists.pcs ||= [];
window._tejsState.mythic.lists.npcs ||= [];
window._tejsState.mythic.lists.threads ||= [];
window.roll = function(max) { return Math.trunc(Math.random() * max + 1); }

~~
^mythic reset$
~~
window._tejsState.mythic.chaos= 4;
window._tejsState.mythic.scene = 1;
window._tejsState.mythic.lists = {};
window._tejsState.mythic.lists.pcs = [];
window._tejsState.mythic.lists.npcs = [];
window._tejsState.mythic.lists.threads = [];
return "─".repeat(20) + "\n\n\n### SCENE 1\n\n";

~~
^detail$
~~
let outcomes = [ [4,"ANGER"],[5,"SADNESS"],[6,"FEAR"],[7,"THREAD NEGATIVE", "threads"],[8,"PC NEGATIVE", "pcs"],[9,"FOCUS NPC", "npcs"],[10,"NPC POSITIVE", "npcs"],[11,"FOCUS PC", "pcs"],[12,"NPC NEGATIVE", "npcs"],[13,"FOCUS THREAD", "threads"],[14,"PC POSITIVE", "pcs"],[15,"THREAD POSITIVE", "threads"],[16,"COURAGE"],[17,"HAPPINESS"],[99,"CALM"] ];
let roll1 = roll(10);
let roll2 = roll(10);
let chaos = window._tejsState.mythic.chaos;
let chaosAdjust = (chaos==3) ? 2 : (chaos==6) ? -2 : 0;
let result = roll1 + roll2 + chaosAdjust;
for (let i = 0; i < outcomes.length; i++)
{
	if (outcomes[i][0] >= result)
	{
		result = outcomes[i][1];
		let list = window._tejsState.mythic.lists[outcomes[i][2]];
		if (list && list.length)
		{
			result += " (" + list[roll(list.length)-1] + ")";
		}
		break;
	}
}
return "__Detail__\n" + result + "\n_roll1=" + roll1 + ",roll2=" + roll2 + ",chaosAdjust=" + chaosAdjust + "_\n\n";


~~
^list$
~~
let listNames = Object.keys(window._tejsState.mythic.lists);
return "__Lists__\n" + (listNames.length ? listNames.join(", ") : "none") + "\n\n";

~~
^list ([a-zA-Z]+)$
~~
let list = window._tejsState.mythic.lists[$1];
return "__List \"" + $1 + "\"__\n" + (list && list.length ? list.join(", ") : "none") + "\n\n";

~~
^listadd ([a-zA-Z]+) ([a-zA-Z ]+)$
~~
window._tejsState.mythic.lists[$1] ||= [];
window._tejsState.mythic.lists[$1].push($2);
return "\"" + $2 + "\" added to list \"" + $1 + "\".\n\n";

~~
^listget ([a-zA-Z]+)$
~~
console.log("wtf");
let list = window._tejsState.mythic.lists[$1];
if (list && list.length)
{
	let result = list[roll(list.length)-1];
	return "\"" + result + "\" picked from list \"" + $1 + "\".\n\n";
}
return "Failed to pick from list \"" + $1 + "\".  List is empty.\n\n";


~~
^listremove ([a-zA-Z]+) ([a-zA-Z ]+)$
~~
if (window._tejsState.mythic.lists[$1])
{
	let i = window._tejsState.mythic.lists[$1].lastIndexOf($2);
	if (i >= 0)
	{
	    window._tejsState.mythic.lists[$1].splice(i, 1);
	    return "\"" + $2 + "\" removed from list \"" + $1 + "\".\n\n";
	}
}
return "Failed to remove \"" + $2 + "\" from list \"" + $1 + "\".  Not found in list.\n\n";


~~
^listremoveall ([a-zA-Z]+)$
~~
if (window._tejsState.mythic.lists[$1])
{
	delete window._tejsState.mythic.lists[$1];
	return "List \"" + $1 + "\" removed.\n\n";
}
return "Failed to remove list \"" + $1 + "\".  List does not exist.\n\n";


~~
^chaos$
~~
return "Chaos is " + window._tejsState.mythic.chaos + ".\n\n";

~~
^chaos=([3-6])$
~~
window._tejsState.mythic.chaos = $1;
return "Chaos set to " + $1 + ".\n\n";

~~
~~
function chaosDown()
{
	window._tejsState.mythic.chaos--;
	if (window._tejsState.mythic.chaos < 3)
	{
		window._tejsState.mythic.chaos = 3;
		return "Chaos remains at 3 (minimum).\n\n";
	}
	return "Chaos lowered to " + window._tejsState.mythic.chaos + ".\n\n";
}
function chaosUp()
{
	window._tejsState.mythic.chaos++;
	if (window._tejsState.mythic.chaos > 6)
	{
		window._tejsState.mythic.chaos = 6;
		return "Chaos remains at 6 (maximum).\n\n";
	}
	return "Chaos raised to " + window._tejsState.mythic.chaos + ".\n\n";
}

~~
^chaos--$
~~
return chaosDown();

~~
^chaos\+\+$
~~
return chaosUp();

~~
~~
function rollAction()
{
	let value1 = ["ATTAINMENT","STARTING","NEGLECT","FIGHT","RECRUIT","TRIUMPH","VIOLATE","OPPOSE","MALICE","COMMUNICATE","PERSECUTE","INCREASE","DECREASE","ABANDON","GRATIFY","INQUIRE","ANTAGONISE","MOVE","WASTE","TRUCE","RELEASE","BEFRIEND","JUDGE","DESERT","DOMINATE","PROCRASTINATE","PRAISE","SEPARATE","TAKE","BREAK","HEAL","DELAY","STOP","LIE","RETURN","IMMITATE","STRUGGLE","INFORM","BESTOW","POSTPONE","EXPOSE","HAGGLE","IMPRISON","RELEASE","CELEBRATE","DEVELOP","TRAVEL","BLOCK","HARM","DEBASE","OVERINDULGE","ADJOURN","ADVERSITY","KILL","DISRUPT","USURP","CREATE","BETRAY","AGREE","ABUSE","OPPRESS","INSPECT","AMBUSH","SPY","ATTACH","CARRY","OPEN","CARELESSNESS","RUIN","EXTRAVAGANCE","TRICK","ARRIVE","PROPOSE","DIVIDE","REFUSE","MISTRUST","DECEIVE","CRUELTY","INTOLERANCE","TRUST","EXCITEMENT","ACTIVITY","ASSIST","CARE","NEGLIGENCE","PASSION","WORK_HARD","CONTROL","ATTRACT","FAILURE","PURSUE","VENGEANCE","PROCEEDINGS","DISPUTE","PUNISH","GUIDE","TRANSFORM","OVERTHROW","OPPRESS","CHANGE"];
	let value2 = ["GOALS","DREAMS","ENVIRONMENT","OUTSIDE","INSIDE","REALITY","ALLIES","ENEMIES","EVIL","GOOD","EMOTIONS","OPPOSITION","WAR","PEACE","THE_INNOCENT","LOVE","THE_SPIRITUAL","THE_INTELLECTUAL","NEW_IDEAS","JOY","MESSAGES","ENERGY","BALANCE","TENSION","FRIENDSHIP","THE_PHYSICAL","A_PROJECT","PLEASURES","PAIN","POSSESSIONS","BENEFITS","PLANS","LIES","EXPECTATIONS","LEGAL_MATTERS","BUREAUCRACY","BUSINESS","A_PATH","NEWS","EXTERIOR_FACTORS","ADVICE","A_PLOT","COMPETITION","PRISON","ILLNESS","FOOD","ATTENTION","SUCCESS","FAILURE","TRAVEL","JEALOUSY","DISPUTE","HOME","INVESTMENT","SUFFERING","WISHES","TACTICS","STALEMATE","RANDOMNESS","MISFORTUNE","DEATH","DISRUPTION","POWER","A_BURDEN","INTRIGUES","FEARS","AMBUSH","RUMOR","WOUNDS","EXTRAVAGANCE","A_REPRESENTATIVE","ADVERSITIES","OPULENCE","LIBERTY","MILITARY","THE_MUNDANE","TRIALS","MASSES","VEHICLE","ART","","VICTORY","DISPUTE","RICHES","STATUS_QUO","TECHNOLOGY","HOPE","MAGIC","ILLUSIONS","PORTALS","DANGER","WEAPONS","ANIMALS","WEATHER","ELEMENTS","NATURE","THE_PUBLIC","LEADERSHIP","FAME","ANGER","INFORMATION"];
	return value1[roll(100)-1] + " " + value2[roll(100)-1];
}

~~
^meaning( action|)$
~~
return "__Meaning (action)__\n" + rollAction() + "\n\n";

~~
~~
function rollDescription()
{
	let value1 = ["ABNORMALLY","ADVENTUROUSLY","AGGRESSIVELY","ANGRILY","ANXIOUSLY","AWKWARDLY","BEAUTIFULLY","BLEAKLY","BOLDLY","BRAVELY","BUSILY","CALMLY","CAREFULLY","CARELESSLY","CAUTIOUSLY","CEASELESSLY","CHEERFULLY","COMBATIVELY","COOLLY","CRAZILY","CURIOUSLY","DAINTILY","DANGEROUSLY","DEFIANTLY","DELIBERATELY","DELIGHTFULLY","DIMLY","EFFICIENTLY","ENERGETICALLY","ENORMOUSLY","ENTHUSIASTICALLY","EXCITEDLY","FEARFULLY","FEROCIOUSLY","FIERCELY","FOOLISHLY","FORTUNATELY","FRANTICALLY","FREELY","FRIGHTENINGLY","FULLY","GENEROUSLY","GENTLY","GLADLY","GRACEFULLY","GRATEFULLY","HAPPILY","HASTILY","HEALTHILY","HELPFULLY","HELPLESSLY","HOPELESSLY","INNOCENTLY","INTENSELY","INTERESTINGLY","IRRITATINGLY","JOVIALLY","JOYFULLY","JUDGEMENTALLY","KINDLY","KOOKILY","LAZILY","LIGHTLY","LOOSELY","LOUDLY","LOVINGLY","LOYALLY","MAJESTICALLY","MEANINGFULLY","MECHANICALLY","MISERABLY","MOCKINGLY","MYSTERIOUSLY","NATURALLY","NEATLY","NICELY","ODDLY","OFFENSIVELY","OFFICIALLY","PARTIALLY","PEACEFULLY","PERFECTLY","PLAYFULLY","POLITELY","POSITIVELY","POWERFULLY","QUAINTLY","QUARRELSOMELY","QUIETLY","ROUGHLY","RUDELY","RUTHLESSLY","SLOWLY","SOFTLY","SWIFTLY","THREATENINGLY","VERY","VIOLENTLY","WILDLY","YIELDINGLY"];
	let value2 = ["ABANDONED","ABNORMAL","AMUSING","ANCIENT","AROMATIC","AVERAGE","BEAUTIFUL","BIZARRE","CLASSY","CLEAN","COLD","COLORFUL","CREEPY","CUTE","DAMAGED","DARK","DEFEATED","DELICATE","DELIGHTFUL","DIRTY","DISAGREEABLE","DISGUSTING","DRAB","DRY","DULL","EMPTY","ENORMOUS","EXOTIC","FADED","FAMILIAR","FANCY","FAT","FEEBLE","FEMININE","FESTIVE","FLAWLESS","FRESH","FULL","GLORIOUS","GOOD","GRACEFUL","HARD","HARSH","HEALTHY","HEAVY","HISTORICAL","HORRIBLE","IMPORTANT","INTERESTING","JUVENILE","LACKING","LAME","LARGE","LAVISH","LEAN","LESS","LETHAL","LONELY","LOVELY","MACABRE","MAGNIFICENT","MASCULINE","MATURE","MESSY","MIGHTY","MILITARY","MODERN","EXTRAVAGANT","MUNDANE","MYSTERIOUS","NATURAL","NONDESCRIPT","ODD","PALE","PETITE","POOR","POWERFUL","QUAINT","RARE","REASSURING","REMARKABLE","ROTTEN","ROUGH","RUINED","RUSTIC","SCARY","SIMPLE","SMALL","SMELLY","SMOOTH","SOFT","STRONG","TRANQUIL","UGLY","VALUABLE","WARLIKE","WARM","WATERY","WEAK","YOUNG"];
	return value1[roll(100)-1] + " " + value2[roll(100)-1];
}

~~
^meaning description$
~~
return "__Meaning (description)__\n" +rollDescription() + "\n\n";


~~
~~
function eventCheck()
{
	let outcomes = [ [7,"REMOTE"],[28,"NPC ACTS","npcs"],[35,"NEW NPC","pc",true],[45,"THREAD ADVANCE","threads"],[52,"THREAD LOSS","threads"],[55,"THREAD END","threads"],[67,"PC NEGATIVE","pcs"],[75,"PC POSITIVE","pcs"],[83,"AMBIGUOUS"],[92,"NPC NEGATIVE","npcs"],[100,"NPC POSITIVE","npcs"] ];
	let result = roll(100);
	let meaningType = false;
	for (let i = 0; i < outcomes.length; i++)
	{
		if (outcomes[i][0] >= result)
		{
			result = outcomes[i][1];
			meaningType = outcomes[i][3];
			let list = window._tejsState.mythic.lists[outcomes[i][2]];
			if (list && list.length)
			{
				result += " (" + list[roll(list.length)-1] + ")";
			}
			break;
		}
	}
	return "__Event__\n" + result + "\n" + (meaningType ? rollDescription() : rollAction());
}


~~
^event$
~~
return eventCheck() + "\n\n";

~~
^scene$
~~
return "The current scene is " + window._tejsState.mythic.scene + ".\n\n";


~~
^scene (1|-1)$
~~
let result = "";
if ($1 == 1)
{
	result += chaosUp();
}
else if ($1 == -1)
{
	result += chaosDown();
}
result += "─".repeat(20) + "\n\n\n";
window._tejsState.mythic.scene++;
result += "### SCENE " + window._tejsState.mythic.scene;
let chk = roll(10);
if (chk <= window._tejsState.mythic.chaos)
{
	result += "\n" + ((chk % 2) ? "Scene modified" : "Scene replaced\n" + eventCheck());
}
return result + "\n\n";


~~
^[f|F](-?[0-4])(y|n|Y|N|)$
~~
let odds = ["impossible","no way","very unlikely","unlikely","50/50","likely","very likely","sure thing","has to be"];
$1 = Number($1);
let aRoll1 = roll(10);
let aRoll2 = roll(10);
let cRoll = roll(10);
let oddsLabel = odds[$1+4];
let oddsAdjust = $1 * 2;
let chaos = window._tejsState.mythic.chaos;
let chaosAdjust = ((chaos==3) ? 2 : (chaos==6) ? -2 : 0)
chaosAdjust *=  ($2.toLowerCase() == "n" ? -1 : 1);
let answer = (aRoll1+aRoll2+oddsAdjust+chaosAdjust > 10 ? "YES" : "NO");

let isExtreme = (cRoll <= chaos) && !!(aRoll1 % 2) && !!(aRoll2 % 2);
let isEvent = (cRoll <= chaos) && !(aRoll1 % 2) && !(aRoll2 % 2);
if (cRoll < chaos && aRoll1 == aRoll2) isExtreme = isEvent = true;

answer = (isExtreme ? "EXTREME " : "") + answer;
let evtText = isEvent ? ("\n" + eventCheck()) : "";
return "__Fate check (" + oddsLabel + ")__\n" +answer + "\n_fateRoll1=" + aRoll1 + ",fateRoll2=" + aRoll2 + ",chaosRoll=" + cRoll + ",oddsAdjust=" + oddsAdjust + ",chaosAdjust=" + chaosAdjust + "_" + evtText + "\n\n";


~~
^descriptor$
~~
return "__Descriptor__\nPersonality: " + rollDescription() + "\nActivity: " + rollAction() + "\n\n";


~~
^disposition (-?[0-3])$
~~
let outcomes = [ [5,"PASSIVE (-2)"],[10,"MODERATE (0)"],[15,"ACTIVE (+2)"],[99,"AGGRESSIVE (+4)"] ];
let roll1 = roll(10);
let roll2 = roll(10);
let descriptorAdjust = Number($1) * 2;
let result = roll1 + roll2 + descriptorAdjust;
for (let i = 0; i < outcomes.length; i++)
{
	if (outcomes[i][0] >= result)
	{
		result = outcomes[i][1];
		break;
	}
}
return "__Disposition__\n" + result + "\n_base=" + (roll1+roll2) + ",roll1=" + roll1 + ",roll2=" + roll2 + ",descriptorAdjust=" + descriptorAdjust + "_\n\n";


~~
^disposition ([0-2]?[0-9]) (-?[0-3])$
~~
let outcomes = [ [5,"PASSIVE (-2)"],[10,"MODERATE (0)"],[15,"ACTIVE (+2)"],[99,"AGGRESSIVE (+4)"] ];
let descriptorAdjust = Number($2) * 2;
let result = Number($1) + descriptorAdjust;
for (let i = 0; i < outcomes.length; i++)
{
	if (outcomes[i][0] >= result)
	{
		result = outcomes[i][1];
		break;
	}
}
return "__Disposition__\n" + result + "\n_base=" + $1 + ",descriptorAdjust=" + descriptorAdjust + "_\n\n";


~~
^action (-2|0|2|4)$
~~
let outcomes1 = [ [3,"NEW ACTION BASED ON THEME"],[5,"CONTINUE CURRENT ACTION"],[6,"CONTINUE CURRENT ACTION, +2 DISPOSITION"],[7,"CONTINUE CURRENT ACTION, -2 DISPOSITION"],[8,false,0],[9,false,-4],[99,false,4] ];
let outcomes2 = [ [6,"TALK / EXPOSITION"],[8,"NEUTRAL, DISCONNECTED ACTION"],[10,"ACTION BENEFITS THE PC"],[11,"GIVE SOMETHING TO THE PC"],[12,"TRY TO END ENCOUNTER"],[13,"CHANGE THEME"],[14,"CHANGE %1 DESCRIPTOR. IT IS ACTIVATED & DECIDES NEXT ACTION.", true],[17,"ACT OUT OF SELF INTEREST"],[18,"TAKE SOMETHING"],[99,"HURT THE PC"] ];
let result = roll(10);
let details = "table1roll=" + result;
for (let i = 0; i < outcomes1.length; i++)
{
	if (outcomes1[i][0] >= result)
	{
		if (outcomes1[i][1])
		{
			result = outcomes1[i][1];
			break;
		}
		else
		{
			let table2roll1 = roll(10);
			let table2roll2 = roll(10);
			let table1Adjust = outcomes1[i][2];
			let dispositionAdjust = Number($1);
			details += ",table2roll1=" + table2roll1 + ",table2roll2=" + table2roll2 + ",table1Adjust=" + table1Adjust + ",dispositionAdjust=" + dispositionAdjust;
			result = table2roll1 + table2roll2 + dispositionAdjust + table1Adjust;
			for (let k = 0; k < outcomes2.length; k++)
			{
				if (outcomes2[k][0] >= result)
				{
					result = outcomes2[k][1];
					if (outcomes2[k][2])
					{
						let dr = roll(3);
						details += ",descriptorRoll=" + dr;
						dr = (dr==1 ? "IDENTITY" : dr==2 ? "PERSONALITY" : "ACTIVITY");
						result = result.replace("%1", dr);
					}
					result += "\nADJUST DISPOSITION (+2/0/-2) TO MATCH ACTION.";
					break;
				}
			}
			break;
		}
	}
}
return "__Action__\n" + result + "\n_" + details + "_\n\n";
