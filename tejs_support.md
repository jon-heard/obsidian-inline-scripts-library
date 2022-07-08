---
obsidianUIMode: preview
---

Various helper shortcuts of minimal category.


~~
```
^rngseed((?: [1-9][0-9]*)?)$
```
~~
```js
if ($1)
{
	if (!Math._oldRandom)
	{
		Math._oldRandom = Math.random;
		Math.random = function()
		{
			var x = Math.sin(Math.rngseed++) * 10000;
			return x - Math.floor(x);
		};
	}
	Math.rngseed = $1;
	return "Random number generator set up with seed '" + $1 + "'.\n\n";
}
else
{
	if (Math._oldRandom)
	{
		Math.random = Math._oldRandom;
		delete Math._oldRandom;
		delete Math.rngsed;
		return "Random number generator reverted to javascript default.\n\n";
	}
	return "Random number generator unchanged.\n\n";
}
```
~~
rngseed {seed} - Sets Math.random to a custom random number generator with a seed of {seed}, a non-zero, positive integer.  Useful for testing.  If {seed} is omitted, the random number generator is returned to javascript default.
