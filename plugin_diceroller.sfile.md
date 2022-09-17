A shortcut to run commands through the Dice Roller plugin.

__
```
^diceroller (.+)$
```
__
```js
const diceRollerPlugin = app.plugins.getPlugin("obsidian-dice-roller");
if (!diceRollerPlugin)
{
	return "Command not run.  Dice Roller plugin not available.\n\n";
}
const diceRoller = await diceRollerPlugin.getRoller($1);
return await diceRoller.roll();
```
__
diceroller {command: text} - Runs {command} through the Dice Roller plugin and expands to the result.
