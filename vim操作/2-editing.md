# 基本结构

```
{operator}{count}{motion}
```
或
```
{count}{operator}{motion}
```

motion 即是怎么移动。

# operator

- `d` 删除
- `c` 删除并进入插入模式
- `y` 复制
- `<` 左移
- `>` 右移
- `=` format
- `g~` 反转大小写, `gu` 小写, `gU` 大写

(`p` 粘贴)

## 用例

- `d3w` 删除 3 个 word
- `df(` 删除到行内下一个 `(` 之间的内容
- `c/hello` 删除到 hello 之间的内容并进入插入模式

## 特殊用例

- `dd` 重复 operator ，操作一整行，即删除当前行
- `D` 大写 operator ，操作到行尾，即删除到行尾

# Text Object

```
{operator}{a|i}{text-object}
```

- `a` around
- `i` inner

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

## text object

- `w` word
- `s` sentence
- `p` paragraph
- `"` quotes, (also `'` , `` ` `` , `{` , `[` , `(` , `<` ... , and `t` represents a tag. only quotes have forward seeking)

## 用例

- `diw` 删除一个 word
- `cas` 删除一个句子并进入插入模式
- `ci"` 删除一个引号内的内容并进入插入模式

# 重复

- `.` 重复上一次操作

`c/word` 输入内容并 `<ESC>` 后算是一次操作， `.` 会重复这个操作。

- `;` , `,` 向前、向后重复上一次 f, F, t, T 操作
- `n` , `N` 向前、向后重复上一次 /, ? 操作

这两种重复可以与 `.` 一起交替使用。

# 撤销

- `u` 撤销上一次操作
- `<C-r>` 重做上一次被撤销的操作

# 单个字母修改

- `x` 删除当前字符, 相当于 `dl`
- `X` 删除前一个字符, 相当于 `dh`
- `s` 删除当前字符并进入插入模式, 相当于 `cl`
- `~` 反转大小写, 相当于 `g~l`

# 暂时切回 Normal Mode

- `<C-o>` 暂时切回 Normal Mode，执行一次命令后再切回 Insert Mode

