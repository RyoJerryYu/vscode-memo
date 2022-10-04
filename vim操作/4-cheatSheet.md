# cheat sheet

## shortest

Vim 有 Normal Mode ， Insert Mode , Command Mode 等模式。一直按 `<Esc>` 总能回到 Normal Mode 。 如果不知道自己是在什么模式，先不要慌，一直按 `<Esc>` 回到 Normal Mode 再说。

在 Normal Mode 下，按 `i` 进入 Insert Mode 。
在 Insert Mode 下，可以像其他编辑器一样编辑文本，按 `<Esc>` 回到 Normal Mode 。
在 Normal Mode 下， 按 `h` , `j`, `k`, `l` 可以上下左右移动光标
在 Normal Mode 下，按 `:` 进入 Command Mode ，可以看到屏幕下方出现 `:` ，输入命令后按 `<Enter>` 执行命令。在 Normal Mode 下不论何时按 `<Esc>` 也能回到 Normal Mode 。
Normal Mode 输入 `:q` (也就是 Command Mode 输入 `q` ) 退出 Vim 。输入 `:w` 保存文件，输入 `:wq` 保存并退出，输入 `:q!` 不保存强制退出。

# normal mode

```
{operator}{count}{motion}
{count}{operator}{motion}
```

## moving motions

basic:

move one character to the left/down/up/right
- `h` 
- `j` 
- `k` 
- `l` 

horizontal:

move to next word, previous word, end of word, end of previous word. Upper case means the WORD.
- `w`
- `b`
- `e` 
- `ge` 

move to the next occurrence of `{char}`, previous occurrence of `{char}`
- `f{char}` `F{char}`
- `t{char}` `T{char}`

move to the head of the line, end of the line
- `0`
- `$`
- `^`
- `g_`

vertical:

- `{` `}` move to the previous/next paragraph
- `/` `?` search for the next/previous occurrence of `{pattern}`
- `gg` `G` move to the first/last line of the file
- `{count}G` move to the `{count}` line of the file

counts could be repeat

- `5j` move down 5 lines
- etc...

## operators

- `d` delete
- `c` change
- `y` yank
- `>` indent, `<` unindent, `=` format
- `g~` toggle case, `gu` lower case, `gU` upper case

repeat means apply a line, Upper case means apply until the end. (`Y` is same as `yy`, only apply a line.)

## Text object

```
            |- `a` means around
            |- `i` means inner
           /
          /
         /
        {a|i}{text-object}
                  /
                 /
                | w - word
                | s - sentence
                | p - paragraph
                | " - quotes
```

- `a` `i` around, inner
- `w` `W` `s` `p` `"` word, WORD, sentence, paragraph, quotes (also brackets, tags.)

## search result

- `gn` `gN` search next/previous match, enter visual mode
- `dgn` `cgn` `ygn` delete/change/yank next match, could be repeat

## surrounding

- `ds{char}` delete surrounding char.
- `cs{char}{char}` change surrounding char with. 
- `ys{motion}{char}` surround motion with `{char}`


## repeat

- `.` repeat the last command

## undo 

- `u` undo

## only modify one character

- `r` replace
- `x` delete one character
- `X` delete one character to the left
- `s` delete one character and enter insert mode
- `~` toggle case

# visual mode and multi cursor

```
{trigger visual mode}{motion}{operator}
```

- `v` `V` `<C-v>` trigger visual mode
- `gv` reselect last visual selection
- `<C-d>` `gb` multi cursor for current word
- `I` `A` insert at the beginning/end of the selected cursor

# insert mode

- `i` `a` insert before/after the cursor
- `I` `A` insert at the beginning/end of the line or the selected cursor
- `o` `O` insert a new line below/above the current line
- `gi` insert at the last insert position

## in insert mode

- `<C-h>` `<C-w>` `<C-u>` delete one character/word/line
- `<C-r>{register}` insert the content of `{register}`

# others

## copy paste and register

```
"{register}{operator}{motion}
```

- `"` default register
- `0` yank register
- `1-9` cut register
- `a-z` user defined register

`:reg` show all registers
Insert mode 中 `<C-r>{register}` paste register content

