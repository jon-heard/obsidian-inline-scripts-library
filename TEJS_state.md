[
	{
		"shortcut": "^state help$",
		"expansion": [
			"let result = \"TEJS STATE SHORTCUTS HELP\\n\";",
			"result += \"- state - Display the current state (for session saving).\\n\"",
			"result += \"- state [state] - Sets the state based on input [state], created previously with the \\\"state\\\" shortcut.\\n\"",
			"result += \"- clip - List the stored clips.\\n\"",
			"result += \"- clip [name] - Displays the value stored in clip [name].\\n\"",
			"result += \"- clipadd [name] [value] - Creates a clip [name] to store [value].\\n\"",
			"result += \"- clipremove [name] - Removes the clip [name].\\n\"",
			"return result + \"\\n\";"
		]
	},
	{
		"shortcut": "^tejs setup$",
		"expansion": [
			"window._tejsState ||= {};",
			"window._tejsState.clips ||= {}"
		]
	},
	{
		"shortcut": "^state$",
		"expansion": "return \"TEJS state:\\n\" + JSON.stringify(window._tejsState) + \"\\n\\n\";"
	},
	{
		"shortcut": "^state (.*)$",
		"expansion": [
			"window._tejsState = JSON.parse($1);",
			"return \"TEJS state loaded.\\n\\n\""
		]
	},
	{
		"shortcut": "^clip$",
		"expansion": [
			"let result = \"none\"",
			"if (Object.keys(window._tejsState.clips).length)",
			"  result = Object.keys(window._tejsState.clips).join(\", \");",
			"return \"Clips: \" + result + \"\\n\";"
		]
	},
	{
		"shortcut": "^clip ([a-zA-Z]+)$",
		"expansion": [
			"let result = \"unset\"",
			"if (window._tejsState.clips[$1])",
			"  result = window._tejsState.clips[$1];",
			"return \"Clip \" + $1 + \":\\n\" + result + \"\\n\";"
		]
	},
	{
		"shortcut": "^clipadd ([a-zA-Z]+) (.+)$",
		"expansion": [
			"window._tejsState.clips[$1] = $2;",
			"return \"Clip \\\"\" + $1 + \"\\\" set to:\\n\" + $2 + \".\\n\";"
		]
	},
	{
		"shortcut": "^clipremove ([a-zA-Z]+)$",
		"expansion": [
			"delete window._tejsState.clips[$1];",
			"return \"Clip \\\"\" + $1 + \"\\\" removed.\\n\";"
		]
	}
]
