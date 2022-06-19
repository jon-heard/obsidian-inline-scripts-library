Shortcuts for writing various arrows:
→←↑↓
▶◀▲▼
⇒⇐⇑⇓
↔↕⇔⇕⇄⇅
↗↘↖↙
↪↩↻↺↝↜

Enter the shortcut "help arrows" for reference.


~~
```
^help arrows$
```
~~
```js
let result = "### ARROW SHORTCUTS HELP\n";
result += "- __help arrows__ - Display this help text.\n";
result += "***\n";
result += "- __-<wbr>>__ - → (RIGHT arrow)\n";
result += "- __<<wbr>-__ - ← (LEFT arrow)\n";
result += "- __-^__ - ↑ (UP arrow)\n";
result += "- __-v__ - ↓ (DOWN arrow)\n";
result += "- __arrow right__ - → (RIGHT arrow)\n";
result += "- __arrow left__ - ← (LEFT arrow)\n";
result += "- __arrow up__ - ↑ (UP arrow)\n";
result += "- __arrow down__ - ↓ (DOWN arrow)\n";
result += "***\n";
result += "- __|>__ - ▶ (RIGHT triangle)\n";
result += "- __<|__ - ◀ (LEFT triangle)\n";
result += "- __|^__ - ▲ (UP triangle)\n";
result += "- __|v__ - ▼ (DOWN triangle)\n";
result += "- __tri right__ - ▶ (RIGHT triangle)\n";
result += "- __tri left__ - ◀ (LEFT triangle)\n";
result += "- __tri up__ - ▲ (UP triangle)\n";
result += "- __tri down__ - ▼ (DOWN triangle)\n";
result += "***\n";
result += "- __=<wbr>>__ - ⇒ (RIGHT double-arrow)\n";
result += "- __<<wbr>=__ - ⇐ (LEFT double-arrow)\n";
result += "- __=^__ - ⇑ (UP double-arrow)\n";
result += "- __=v__ - ⇓ (DOWN double-arrow)\n";
result += "- __arrow dbl right__ - ⇒ (RIGHT double-arrow)\n";
result += "- __arrow dbl left__ - ⇐ (LEFT double-arrow)\n";
result += "- __arrow dbl up__ - ⇑ (UP double-arrow)\n";
result += "- __arrow dbl down__ - ⇓ (DOWN double-arrow)\n";
result += "***\n";
result += "- __<<wbr>-<wbr>>__ - ↔ (LEFT / RIGHT arrow)\n";
result += "- __^-v__ - ↕ (UP / DOWN arrow)\n";
result += "- __<<wbr>=<wbr>>__ - ⇔ (LEFT / RIGHT double arrow)\n";
result += "- __^=v__ - ⇕ (UP / DOWN double arrow)\n";
result += "- __<//>__ - ⇄ (LEFT arrow & RIGHT arrow)\n";
result += "- __^//v__ - ⇅ (UP arrow & DOWN arrow)\n";
result += "- __arrow leftright__ - ↔ (LEFT / RIGHT arrow)\n";
result += "- __arrow updown__ - ↕ (UP / DOWN arrow)\n";
result += "- __arrow dbl leftright__ - ⇔ (LEFT / RIGHT double arrow)\n";
result += "- __arrow dbl updown__ - ⇕ (UP / DOWN double arrow)\n";
result += "- __arrow left right__ - ⇄ (LEFT arrow & RIGHT arrow)\n";
result += "- __arrow up down__ - ⇅ (UP arrow & DOWN arrow)\n";
result += "***\n";
result += "- __-^>__ - ↗ (RIGHT / UP arrow)\n";
result += "- __-v>__ - ↘ (RIGHT / DOWN arrow)\n";
result += "- __<^-__ - ↖ (LEFT / UP arrow)\n";
result += "- __<v-__ - ↙ (LEFT / DOWN arrow)\n";
result += "- __arrow rightup__ - ↗ (RIGHT / UP arrow)\n";
result += "- __arrow rightdown__ - ↘ (RIGHT / DOWN arrow)\n";
result += "- __arrow leftup__ - ↖ (LEFT / UP arrow)\n";
result += "- __arrow leftdown__ - ↙ (LEFT / DOWN arrow)\n";
result += "***\n";
result += "- __u>__ - ↪ (RIGHT curve arrow)\n";
result += "- __<u__ - ↩ (LEFT curve arrow)\n";
result += "- __c>__ - ↻ (CLOCKWISE arrow)\n";
result += "- __<c__ - ↺ (COUNTER-CLOCKWISE arrow)\n";
result += "- __~>__ - ↝ (RIGHT wavy arrow)\n";
result += "- __<~__ - ↜ (LEFT wavy arrow)\n";
result += "- __arrow curve right__ - ↪ (RIGHT swoop arrow)\n";
result += "- __arrow curve left__ - ↩ (LEFT swoop arrow)\n";
result += "- __arrow clock__ - ↻ (CLOCKWISE arrow)\n";
result += "- __arrow cclock__ - ↺ (COUNTER-CLOCKWISE arrow)\n";
result += "- __arrow wavy right__ - ↝ (RIGHT wavy arrow)\n";
result += "- __arrow wavy left__ - ↜ (LEFT wavy arrow)\n";
return result + "\n";
```


~~
```
(?:^arrow right$)|(?:^\-\>$)
```
~~
```js
return "→";
```


~~
```
(?:^arrow left$)|(?:^\<\-$)
```
~~
```js
return "←";
```


~~
```
(?:^arrow up$)|(?:^\-\^$)
```
~~
```js
return "↑";
```


~~
```
(?:^arrow down$)|(?:^\-v$)
```
~~
```js
return "↓";
```


~~
```
(?:^tri right$)|(?:^\|\>$)
```
~~
```js
return "▶";
```


~~
```
(?:^tri left$)|(?:^\<\|$)
```
~~
```js
return "◀";
```


~~
```
(?:^tri up$)|(?:^\|\^$)
```
~~
```js
return "▲";
```


~~
```
(?:^tri down$)|(?:^\|v$)
```
~~
```js
return "▼";
```


~~
```
(?:^arrow dbl right$)|(?:^\=\>$)
```
~~
```js
return "⇒";
```


~~
```
(?:^arrow dbl left$)|(?:^\<\=$)
```
~~
```js
return "⇐";
```


~~
```
(?:^arrow dbl up$)|(?:^\=\^$)
```
~~
```js
return "⇑";
```


~~
```
(?:^arrow dbl down$)|(?:^\=v$)
```
~~
```js
return "⇓";
```


~~
```
(?:^arrow leftright$)|(?:^\<\-\>$)
```
~~
```js
return "↔";
```


~~
```
(?:^arrow updown$)|(?:^\^\-v$)
```
~~
```js
return "↕";
```


~~
```
(?:^arrow dbl leftright$)|(?:^\<\=\>$)
```
~~
```js
return "⇔";
```


~~
```
(?:^arrow dbl updown$)|(?:^\^\=v$)
```
~~
```js
return "⇕";
```


~~
```
(?:^arrow left right$)|(?:^\<\/\/\>$)
```
~~
```js
return "⇄";
```


~~
```
(?:^arrow up down$)|(?:^\^\/\/v$)
```
~~
```js
return "⇅";
```


~~
```
(?:^arrow rightup$)|(?:^\-\^\>$)
```
~~
```js
return "↗";
```


~~
```
(?:^arrow rightdown$)|(?:^\-v\>$)
```
~~
```js
return "↘";
```


~~
```
(?:^arrow leftup$)|(?:^\<\^\-$)
```
~~
```js
return "↖";
```


~~
```
(?:^arrow leftdown$)|(?:^\<v\-$)
```
~~
```js
return "↙";
```

~~
```
(?:^arrow curve right$)|(?:^u\>$)
```
~~
```js
return "↪";
```


~~
```
(?:^arrow curve left$)|(?:^\<u$)
```
~~
```js
return "↩";
```


~~
```
(?:^arrow clock$)|(?:^c\>$)
```
~~
```js
return "↻";
```


~~
```
(?:^arrow cclock$)|(?:^\<c$)
```
~~
```js
return "↺";
```


~~
```
(?:^arrow wavy right$)|(?:^\~\>$)
```
~~
```js
return "↝";
```


~~
```
(?:^arrow wavy left$)|(?:^\<\~$)
```
~~
```js
return "↜";
```
