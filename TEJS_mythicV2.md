[
	{
		"regex": "^mythic help$",
		"expansion": [
			"let result = \"MYTHIC SHORTCUTS HELP\\n\";",
			"result += \"- mythic help - Display this help text.\\n\"",
			"result += \"- mythic state - Display the current state of mythic variables (for session saving).\\n\"",
			"result += \"- mythic state [state] - Sets the state of mythic variables based on string [state], created previously with the \\\"mythic state\\\" shortcut.\\n\"",
			"result += \"- mythic reset - Reset all of mythic state to defaults and print new scene.\\n\"",
			"result += \"- detail - Make a details check.\\n\"",
			"result += \"- event - Make an event check.\\n\"",
			"result += \"- f[odds][wanted] - Make a fate check with the odds of [odds] (-4 to 4) and the preferred result of [wanted] (either 'n' or 'y').  The preferred result is optional and defaults to 'y'.  It specifies the direct of the chaos modifier.\\n\"",
			"result += \"- meaning - Roll on the action meaning table.\\n\"",
			"result += \"- meaning action - Roll on the action meaning table (same as \\\"meaning\\\").\\n\"",
			"result += \"- meaning discription - Roll on the description meaning table.\\n\"",
			"result += \"- list - Show a list of the lists.\\n\"",
			"result += \"- list [listName] - Show all items in the list named [listName].\\n\"",
			"result += \"- listadd [listName] [item] - Add [item] to the end of the list named [listName].\\n\"",
			"result += \"- listremove [listName] [item] - Remove last instance of [item] from the list named [listName].\\n\"",
			"result += \"- listget [listName] - Get a random item from the list named [listName].\\n\"",
			"result += \"- scene - Show the current scene.\\n\"",
			"result += \"- scene [chaosAdjust] - Shift the chaos value by [chaosAdjust] (1 or -1), increment the current scene, then output all.\\n\"",
			"result += \"- chaos - Show the current chaos value.\\n\"",
			"result += \"- chaos++ - Increase the chaos value by 1 (max of 6).\\n\"",
			"result += \"- chaos-- - Decrease the chaos value by 1 (min of 3).\\n\"",
			"result += \"- chaos=[value] - Set the chaos value to [value].\\n\\n\"",
			"return result;"
		]
	},
	{
		"regex": "^mythic state$",
		"expansion": [
			"let result = { chaos: (window._mChaos || 4), scene: (window._mScene || 1), lists: (window._mLists) || {} };",
			"return \"Mythic state:\\n\" + JSON.stringify(result) + \"\\n\\n\";"
		]
	},
	{
		"regex": "^mythic state (.*)$",
		"expansion": [
			"let state = JSON.parse($1);",
			"if (state.chaos) window._mChaos = state.chaos;",
			"if (state.lists) window._mLists = state.lists;",
			"if (state.scene) window._mScene = state.scene;",
			"return \"Mythic state loaded.\\n\\n\""
		]
	},
	{
		"regex": "^mythic reset$",
		"expansion": [
			"window._mChaos= 4;",
			"window._mLists = {};",
			"window._mScene = 1;",
			"return \".......................................................................\\n\\n\\n### SCENE 1\\n\\n\";"
		]
	},
	{
		"regex": "",
		"expansion": "function roll(max) { return Math.trunc(Math.random() * max + 1); }"
	},
	{
		"regex": "^detail$",
		"expansion": [
			"let f = [[4,\"ANGER\"],[5,\"SADNESS\"],[6,\"FEAR\"],[7,\"THREAD NEGATIVE\"],[8,\"PC NEGATIVE\"],[9,\"FOCUS NPC\"],[10,\"NPC POSITIVE\"],[11,\"FOCUS PC\"],[12,\"NPC NEGATIVE\"],[13,\"FOCUS THREAD\"],[14,\"PC POSITIVE\"],[15,\"THREAD POSITIVE\"],[16,\"COURAGE\"],[17,\"HAPPINESS\"],[99,\"CALM\"]];",
			"let roll1 = roll(10);",
			"let roll2 = roll(10);",
			"let chaos = (window._mChaos || 4);",
			"let chaosAdjust = ((chaos==3) ? 2 : (chaos==6) ? -2 : 0);",
			"let r = roll1 + roll2 + chaosAdjust;",
			"for (let i = 0; i < f.length; i++) if (f[i][0] >= r) { r = f[i]; break; }",
			"return \"Detail: \" + r[1] + \"\\n\";"
		]
	},
	{
		"regex": "^list$",
		"expansion": [
			"let result = \"none\"",
			"if (window._mLists && Object.keys(window._mLists).length)",
			"  result = Object.keys(window._mLists).join(\", \");",
			"return \"Lists: \" + result + \"\\n\";"
		]
	},
	{
		"regex": "^list ([a-zA-Z]+)$",
		"expansion": [
			"let result = \"none\"",
			"if (window._mLists && window._mLists[$1])",
			"  result = window._mLists[$1].join(\", \");",
			"return $1 + \": \" + result + \"\\n\";"
		]
	},
	{
		"regex": "^listadd ([a-zA-Z]+) ([a-zA-Z ]+)$",
		"expansion": [
			"window._mLists = window._mLists || {};",
			"window._mLists[$1] = window._mLists[$1] || []",
			"window._mLists[$1].push($2);",
			"return \"\\\"\" + $2 + \"\\\" added to list \\\"\" + $1 + \"\\\".\\n\";"
		]
	},
	{
		"regex": "^listremove ([a-zA-Z]+) ([a-zA-Z ]+)$",
		"expansion": [
		    "if (window._mLists && window._mLists[$1]) {", 
			"  let i = window._mLists[$1].lastIndexOf($2);",
			"  if (i != -1) {",
			"    window._mLists[$1].splice(i, 1);",
			"    return \"\\\"\" + $2 + \"\\\" removed from list \\\"\" + $1 + \"\\\".\\n\"; } }",
			"return \"\\\"\" + $1 + \"\\\" list does not contain \\\"\" + $2 + \"\\\".\\n\""
		]
	},
	{
		"regex": "^listget ([a-zA-Z]+)$",
		"expansion": [
		    "if (window._mLists && window._mLists[$1] && window._mLists[$1].length) {", 
			"  let result = window._mLists[$1][roll(window._mLists[$1].length)-1];",
			"  return \"\\\"\" + result + \"\\\" picked from \\\"\" + $1 + \"\\\" list.\\n\"; }",
			"return \"\\\"\" + $1 + \"\\\" is empty.\\n\""
		]
	},
	{
		"regex": "^chaos$",
		"expansion": "return \"CHAOS is \" + (window._mChaos || 4) + \".\\n\";"
	},
	{
		"regex": "^chaos=([3-6])$",
		"expansion": [
			"window._mChaos = $1",
			"return \"CHAOS set to \" + $1 + \".\\n\";"
		]
	},
	{
		"regex": "",
		"expansion": [
			"function chaosDown() {",
			"  window._mChaos = (window._mChaos || 4) - 1;",
			"  if (window._mChaos < 3) { window._mChaos = 3; return false; }",
			"  return true; }",
			"function chaosUp() {",
			"  window._mChaos = (window._mChaos || 4) + 1;",
			"  if (window._mChaos > 6) { window._mChaos = 6; return false; }",
			"  return true; }"
		]
	},
	{
		"regex": "^chaos--$",
		"expansion": "return \"CHAOS \" + (chaosDown() ? (\"lowered to \" + window._mChaos) : (\"remains at 3 (minimum)\")) + \".\\n\";"
	},
	{
		"regex": "^chaos\\+\\+$",
		"expansion": "return \"CHAOS \" + (chaosUp() ? (\"raised to \" + window._mChaos) : (\"remains at 6 (maximum)\")) + \".\\n\";"
	},
	{
		"regex": "",
		"expansion": [
			"function rollAction() {",
			"  let v1 = [\"ATTAINMENT\",\"STARTING\",\"NEGLECT\",\"FIGHT\",\"RECRUIT\",\"TRIUMPH\",\"VIOLATE\",\"OPPOSE\",\"MALICE\",\"COMMUNICATE\",\"PERSECUTE\",\"INCREASE\",\"DECREASE\",\"ABANDON\",\"GRATIFY\",\"INQUIRE\",\"ANTAGONISE\",\"MOVE\",\"WASTE\",\"TRUCE\",\"RELEASE\",\"BEFRIEND\",\"JUDGE\",\"DESERT\",\"DOMINATE\",\"PROCRASTINATE\",\"PRAISE\",\"SEPARATE\",\"TAKE\",\"BREAK\",\"HEAL\",\"DELAY\",\"STOP\",\"LIE\",\"RETURN\",\"IMMITATE\",\"STRUGGLE\",\"INFORM\",\"BESTOW\",\"POSTPONE\",\"EXPOSE\",\"HAGGLE\",\"IMPRISON\",\"RELEASE\",\"CELEBRATE\",\"DEVELOP\",\"TRAVEL\",\"BLOCK\",\"HARM\",\"DEBASE\",\"OVERINDULGE\",\"ADJOURN\",\"ADVERSITY\",\"KILL\",\"DISRUPT\",\"USURP\",\"CREATE\",\"BETRAY\",\"AGREE\",\"ABUSE\",\"OPPRESS\",\"INSPECT\",\"AMBUSH\",\"SPY\",\"ATTACH\",\"CARRY\",\"OPEN\",\"CARELESSNESS\",\"RUIN\",\"EXTRAVAGANCE\",\"TRICK\",\"ARRIVE\",\"PROPOSE\",\"DIVIDE\",\"REFUSE\",\"MISTRUST\",\"DECEIVE\",\"CRUELTY\",\"INTOLERANCE\",\"TRUST\",\"EXCITEMENT\",\"ACTIVITY\",\"ASSIST\",\"CARE\",\"NEGLIGENCE\",\"PASSION\",\"WORK_HARD\",\"CONTROL\",\"ATTRACT\",\"FAILURE\",\"PURSUE\",\"VENGEANCE\",\"PROCEEDINGS\",\"DISPUTE\",\"PUNISH\",\"GUIDE\",\"TRANSFORM\",\"OVERTHROW\",\"OPPRESS\",\"CHANGE\"];",
			"  let v2 = [\"GOALS\",\"DREAMS\",\"ENVIRONMENT\",\"OUTSIDE\",\"INSIDE\",\"REALITY\",\"ALLIES\",\"ENEMIES\",\"EVIL\",\"GOOD\",\"EMOTIONS\",\"OPPOSITION\",\"WAR\",\"PEACE\",\"THE_INNOCENT\",\"LOVE\",\"THE_SPIRITUAL\",\"THE_INTELLECTUAL\",\"NEW_IDEAS\",\"JOY\",\"MESSAGES\",\"ENERGY\",\"BALANCE\",\"TENSION\",\"FRIENDSHIP\",\"THE_PHYSICAL\",\"A_PROJECT\",\"PLEASURES\",\"PAIN\",\"POSSESSIONS\",\"BENEFITS\",\"PLANS\",\"LIES\",\"EXPECTATIONS\",\"LEGAL_MATTERS\",\"BUREAUCRACY\",\"BUSINESS\",\"A_PATH\",\"NEWS\",\"EXTERIOR_FACTORS\",\"ADVICE\",\"A_PLOT\",\"COMPETITION\",\"PRISON\",\"ILLNESS\",\"FOOD\",\"ATTENTION\",\"SUCCESS\",\"FAILURE\",\"TRAVEL\",\"JEALOUSY\",\"DISPUTE\",\"HOME\",\"INVESTMENT\",\"SUFFERING\",\"WISHES\",\"TACTICS\",\"STALEMATE\",\"RANDOMNESS\",\"MISFORTUNE\",\"DEATH\",\"DISRUPTION\",\"POWER\",\"A_BURDEN\",\"INTRIGUES\",\"FEARS\",\"AMBUSH\",\"RUMOR\",\"WOUNDS\",\"EXTRAVAGANCE\",\"A_REPRESENTATIVE\",\"ADVERSITIES\",\"OPULENCE\",\"LIBERTY\",\"MILITARY\",\"THE_MUNDANE\",\"TRIALS\",\"MASSES\",\"VEHICLE\",\"ART\",\"\",\"VICTORY\",\"DISPUTE\",\"RICHES\",\"STATUS_QUO\",\"TECHNOLOGY\",\"HOPE\",\"MAGIC\",\"ILLUSIONS\",\"PORTALS\",\"DANGER\",\"WEAPONS\",\"ANIMALS\",\"WEATHER\",\"ELEMENTS\",\"NATURE\",\"THE_PUBLIC\",\"LEADERSHIP\",\"FAME\",\"ANGER\",\"INFORMATION\"];",
			"  return v1[roll(100)-1] + \" \" + v2[roll(100)-1]; }"
		]
	},
	{
		"regex": "^meaning( action|)$",
		"expansion": "return \"Meaning: action: \" + rollAction() + \"\\n\";"
	},
	{
		"regex": "",
		"expansion": [
			"function rollDescription() {",
			"  let v1 = [\"ABNORMALLY\",\"ADVENTUROUSLY\",\"AGGRESSIVELY\",\"ANGRILY\",\"ANXIOUSLY\",\"AWKWARDLY\",\"BEAUTIFULLY\",\"BLEAKLY\",\"BOLDLY\",\"BRAVELY\",\"BUSILY\",\"CALMLY\",\"CAREFULLY\",\"CARELESSLY\",\"CAUTIOUSLY\",\"CEASELESSLY\",\"CHEERFULLY\",\"COMBATIVELY\",\"COOLLY\",\"CRAZILY\",\"CURIOUSLY\",\"DAINTILY\",\"DANGEROUSLY\",\"DEFIANTLY\",\"DELIBERATELY\",\"DELIGHTFULLY\",\"DIMLY\",\"EFFICIENTLY\",\"ENERGETICALLY\",\"ENORMOUSLY\",\"ENTHUSIASTICALLY\",\"EXCITEDLY\",\"FEARFULLY\",\"FEROCIOUSLY\",\"FIERCELY\",\"FOOLISHLY\",\"FORTUNATELY\",\"FRANTICALLY\",\"FREELY\",\"FRIGHTENINGLY\",\"FULLY\",\"GENEROUSLY\",\"GENTLY\",\"GLADLY\",\"GRACEFULLY\",\"GRATEFULLY\",\"HAPPILY\",\"HASTILY\",\"HEALTHILY\",\"HELPFULLY\",\"HELPLESSLY\",\"HOPELESSLY\",\"INNOCENTLY\",\"INTENSELY\",\"INTERESTINGLY\",\"IRRITATINGLY\",\"JOVIALLY\",\"JOYFULLY\",\"JUDGEMENTALLY\",\"KINDLY\",\"KOOKILY\",\"LAZILY\",\"LIGHTLY\",\"LOOSELY\",\"LOUDLY\",\"LOVINGLY\",\"LOYALLY\",\"MAJESTICALLY\",\"MEANINGFULLY\",\"MECHANICALLY\",\"MISERABLY\",\"MOCKINGLY\",\"MYSTERIOUSLY\",\"NATURALLY\",\"NEATLY\",\"NICELY\",\"ODDLY\",\"OFFENSIVELY\",\"OFFICIALLY\",\"PARTIALLY\",\"PEACEFULLY\",\"PERFECTLY\",\"PLAYFULLY\",\"POLITELY\",\"POSITIVELY\",\"POWERFULLY\",\"QUAINTLY\",\"QUARRELSOMELY\",\"QUIETLY\",\"ROUGHLY\",\"RUDELY\",\"RUTHLESSLY\",\"SLOWLY\",\"SOFTLY\",\"SWIFTLY\",\"THREATENINGLY\",\"VERY\",\"VIOLENTLY\",\"WILDLY\",\"YIELDINGLY\"];",
			"  let v2 = [\"ABANDONED\",\"ABNORMAL\",\"AMUSING\",\"ANCIENT\",\"AROMATIC\",\"AVERAGE\",\"BEAUTIFUL\",\"BIZARRE\",\"CLASSY\",\"CLEAN\",\"COLD\",\"COLORFUL\",\"CREEPY\",\"CUTE\",\"DAMAGED\",\"DARK\",\"DEFEATED\",\"DELICATE\",\"DELIGHTFUL\",\"DIRTY\",\"DISAGREEABLE\",\"DISGUSTING\",\"DRAB\",\"DRY\",\"DULL\",\"EMPTY\",\"ENORMOUS\",\"EXOTIC\",\"FADED\",\"FAMILIAR\",\"FANCY\",\"FAT\",\"FEEBLE\",\"FEMININE\",\"FESTIVE\",\"FLAWLESS\",\"FRESH\",\"FULL\",\"GLORIOUS\",\"GOOD\",\"GRACEFUL\",\"HARD\",\"HARSH\",\"HEALTHY\",\"HEAVY\",\"HISTORICAL\",\"HORRIBLE\",\"IMPORTANT\",\"INTERESTING\",\"JUVENILE\",\"LACKING\",\"LAME\",\"LARGE\",\"LAVISH\",\"LEAN\",\"LESS\",\"LETHAL\",\"LONELY\",\"LOVELY\",\"MACABRE\",\"MAGNIFICENT\",\"MASCULINE\",\"MATURE\",\"MESSY\",\"MIGHTY\",\"MILITARY\",\"MODERN\",\"EXTRAVAGANT\",\"MUNDANE\",\"MYSTERIOUS\",\"NATURAL\",\"NONDESCRIPT\",\"ODD\",\"PALE\",\"PETITE\",\"POOR\",\"POWERFUL\",\"QUAINT\",\"RARE\",\"REASSURING\",\"REMARKABLE\",\"ROTTEN\",\"ROUGH\",\"RUINED\",\"RUSTIC\",\"SCARY\",\"SIMPLE\",\"SMALL\",\"SMELLY\",\"SMOOTH\",\"SOFT\",\"STRONG\",\"TRANQUIL\",\"UGLY\",\"VALUABLE\",\"WARLIKE\",\"WARM\",\"WATERY\",\"WEAK\",\"YOUNG\"];",
			"  return v1[roll(100)-1] + \" \" + v2[roll(100)-1]; }"
		]
	},
	{
		"regex": "^meaning description$",
		"expansion": "return \"Meaning: description: \" +rollDescription() + \"\\n\";"
	},
	{
		"regex": "",
		"expansion": [
			"function eventCheck() {",
			"  let f = [[7,\"REMOTE\", 0],[28,\"NPC ACTS\",0],[35,\"NEW NPC\",1],[45,\"THREAD ADVANCE\",0],[52,\"THREAD LOSS\",0],[55,\"THREAD END\",0],[67,\"PC NEGATIVE\",0],[75,\"PC POSITIVE\",0],[83,\"AMBIGUOUS\",0],[92,\"NPC NEGATIVE\",0],[100,\"NPC POSITIVE\",0]];",
			"  let r = roll(100);",
			"  for (let i = 0; i < f.length; i++) if (f[i][0] >= r) { r = f[i]; break; }",
			"  return \"Event: \" + r[1] + \": \" + (r[2] ? rollDescription() : rollAction()); }"
		]
	},
	{
		"regex": "^event$",
		"expansion": "return eventCheck() + \"\\n\";"
	},
	{
		"regex": "^scene$",
		"expansion": "return \"The current scene is \" + (window._mScene || 1) + \".\";"
	},
	{
		"regex": "^scene (1|-1)$",
		"expansion": [
			"let result = \"\";",
			"if ($1 == 1)",
			"  result = \"CHAOS \" + (chaosUp() ? (\"raised to \" + window._mChaos) : (\"remains at 6 (maximum)\"));",
			"else if ($1 == -1)",
			"  result = \"CHAOS \" + (chaosDown() ? (\"lowered to \" + window._mChaos) : (\"remains at 3 (minimum)\"));",
			"result += \"\\n.......................................................................\\n\\n\\n\";",
			"window._mScene = (window._mScene || 1) + 1;",
			"result += \"### SCENE \" + window._mScene;",
			"let c = roll(10);",
			"if (c <= (window._mChaos || 4))",
			"  result += \"\\n\" + ((c%2) ? \"Scene modified\" : \"Scene replaced\\n\" + eventCheck());",
			"return result + \"\\n\\n\";"
		]
	},
	{
		"regex": "^[f|F](-?[0-4])(y|n|Y|N|)$",
		"expansion" : [
			"let odds = ['impossible','no way','very unlikely','unlikely','50/50','likely','very likely','sure thing','has to be'];",
			"$1 = Number($1);",
			"let aRoll1 = roll(10);",
			"let aRoll2 = roll(10);",
			"let cRoll = roll(10);",
			"let oddsAdjust = $1 * 2;",
			"let chaos = (window._mChaos || 4);",
			"let chaosAdjust = ((chaos==3) ? 2 : (chaos==6) ? -2 : 0)  *  ($2.toLowerCase() == \"n\" ? -1 : 1);",
			"let isExtreme = (cRoll <= chaos) && !!(aRoll1 % 2) && !!(aRoll2 % 2);",
			"let isEvented = (cRoll <= chaos) && !(aRoll1 % 2) && !(aRoll2 % 2);",
			"if (cRoll < chaos && aRoll1 == aRoll2) isExtreme = isEvented = true;",
			"let answer = (isExtreme ? \"EXTREME \" : \"\") + (aRoll1+aRoll2+oddsAdjust+chaosAdjust > 10 ? \"YES\" : \"NO\");",
			"let evtText = isEvented ? (\"\\n\" + eventCheck()) : \"\";",
			"return \"Fate check: \" + answer + \"\\n(odds='\" + odds[$1+4] + \"',fateRoll1=\" + aRoll1 + \",fateRoll2=\" + aRoll2 + \",chaosRoll=\" + cRoll + \",oddsAdjust=\" + oddsAdjust + \",chaosAdjust=\" + chaosAdjust + \")\" + evtText + \"\\n\";"
		]
	}
]
