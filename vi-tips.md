## Input commands (end with Esc)
```
a Append after cursor
i Insert before cursor
o Open line below
O Open line above
:r file Insert file after current line
```

## Change word
```
c{w,$,^} Change word, line begin/end
d{w,$,^} delete word, line begin/end
rc Replace character with c
R Replace (Esc) - typeover
. Repeat last change
```

## Undo commands
```
u Undo last change
U Undo all changes on line
```

## Cursor motions
```
H Upper left corner (home)
L Lower left corner
h [left]  l [right] 
j [up]    k [down]
^ Beginning of line
$ End of line
w One word forward
b Back one word
:0 Beginning of file
:$ End of file
ctrl f/b page up/down
fx find x
; repeat find
<< >> indent
```

## Rearrangement commands
```
yy line to general buffer
p Put general buffer after cursor
P Put general buffer before cursor
J Join lines
```

## search
```
:s/pattern/string/flags Replace pattern with string according to flags.
g   Flag - Replace all occurences of pattern
c   Flag - Confirm replaces.
&   Repeat last :s command
```

### seach example
insert '# ' in front of range of line'
```
:10,20s/^/\# /
```

## markers
```
mc  Set marker c on this line
`c  Go to beginning of marker c line.
```

### 
insert '# ' in front of range of line'
move the cursor to the first line of the range, 
then enter `ma` to set the marker a to the current line. 
Move to the end of the range and enter
'.' means current line. from marker to current line 
```
:'a,.s/^/\# /
```

## buffer
```
"l set buffer to l
```

### buffer examples
```
"z6yy Yank 6 lines to buffer z
"a9dd Delete 9 lines to buffer a
"A9dd Delete 9 lines; Append to buffer a
"ap Put text from buffer a after cursor
```


## append line to get current working directory. 
cd /home/al/

## show number line.
:set number

## ex commands
:d (delete lines)
:co (copy lines)
:m (move lines).

### ex commands mix example
:21,25t 30 copy lines 21..25 after line 30
