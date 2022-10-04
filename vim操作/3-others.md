# inserting

- `i` lets you *i*nsert text before the cursor
- `a` lets you *a*ppend text after the cursor
- `I` lets you *i*nsert text at the beginning of a line
- `A` lets you *a*ppend text at the end of a line
- `o` lets you *o*pen a new line below the current line
- `O` lets you *o*pen a new line above the current line
- `gi` lets you *g*o *i*nto insert mode at the last change

## in insert mode

- `<C-h>` delete one character to the left
- `<C-w>` delete one word to the left
- `<C-u>` delete the whole line to the left

# selecting

- `v` lets you select text in *v*isual mode
- `V` lets you select text in *V*isual line mode
- `<C-v>` lets you select text in *V*isual block mode

In normal mode, you:

```
{operator}{count}{motion}
```

But with visual mode, you could:

```
{trigger visual mode}{motion}{operator}
```

# 查找与 operator

前情提要： `/` 与 `?` 可以查找，然后 `n` 与 `N` 可以移动到下一个位置，都是一种 motion 。

- `gn` `gN` 查找下一个/上一个匹配项，并进入 Visual Mode 。相当于选择整个匹配项。

`gn` `gN` 是一个 motion ，可以与 `d` `c` `y` 等 operator 一起使用。

1. 查找匹配项 `/cucumber`
2. 删除 `dgn`
3. 重复 `.`

# 复制粘贴

- `y` 与 `p`

## registers

- `"` 默认 register
- `0` yank register
- `1` - `9` cut register
- `a` - `z` user defined register

```
"{register}{y|d|c}{motion}
```

用 `:reg` 可以查看 register 的内容。

## insert mode 中的粘贴

- `<C-r>{register}`

# surrounding

- `ds'` delete surrounding `'`
- `cs'"` change surrounding `'` to `"`
- `ysaptli>` surround a paragraph with `<li>` tag:
- `ys` yank surrounding... (`ys{motion}{char})
    - `ap` around paragraph (text object, the motion)
    - `t` tag...
    - `li>` what a tag like

and, yank a surrounding do not means copy it, but add a surrounding.

# multiple cursor

## each word

> tips: in `settings.json` , in `vim.handleKeys` make sure `<C-d>` is `false` .

- `<C-d>` or `gb` : select a word and add cursors on next word.

## rectangle

- `<C-v>` : start a visual block mode
- `I` `A` for append or insert each line

相当于 VSCode 里的 `alt + shift + <鼠标选择>` 。

# other

`gh` : hovering mouse over the cursor

