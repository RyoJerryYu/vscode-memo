# concept

1. editor moving and splitting
    1. split vertical for vscode `ctrl + shift + \`
    2. focus around `ctrl + shift + hjkl`
    3. move around `ctrl + shift + alt + hjkl`
2. quick moving and slow moving
    1. quick: `shift + j` `shift + k` = `5j` `5k`
    2. slow: `ctrl + j` `ctrl + k` = `gj` `gk`
3. tabbing: `tab` `shift + tab` = `>>` `<<`
4. quick exit edit mode: `jj` = `esc`
5. VSCode original keymapping: `ctrl+w` , `ctrl+t` , `ctrl+s` , `ctrl+z` , ` ctrl + shift + f`


# vscode shortcut binding


| command                                                                      | key before              | key after                | why                           |
| ---------------------------------------------------------------------------- | ----------------------- | ------------------------ | ----------------------------- |
| Delete Line                                                                  | `ctrl + shift + k`      | -                        | For Focus Editor up           |
| Go to  Bracket                                                               | `ctrl + shift + \`      | -                        | For Vertical Split            |
| Search: Replace in Files                                                     | `ctrl + shift + h`      | -                        | For Focus Editor Left         |
| View: Split Editor Orthogonal `workbench.action.splitEditorOrthogonal`       | `ctrl + K ctrl + \ `    | `ctrl + shift + \`       | I'm sure we need it.          |
| View: Focus Left Editor Group `workbench.action.focusLeftGroup`              | `ctrl + K ctrl + Left`  | `ctrl + shift + h`       | For moving windows with hjkl. |
| View: Focus Right Editor Group `workbench.action.focusRightGroup`            | `ctrl + K ctrl + Right` | `ctrl + shift + l`       | For moving windows with hjkl. |
| View: Focus Editor Group Above `workbench.action.focusAboveGroup`            | `ctrl + K ctrl + Up`    | `ctrl + shift + k`       | For moving windows with hjkl. |
| View: Focus Editor Group Below `workbench.action.focusBelowGroup`            | `ctrl + K ctrl + Down`  | `ctrl + shift + j`       | For moving windows with hjkl. |
| View: Move Editor into Group Above `workbench.action.moveEditorToAboveGroup` | -                       | `ctrl + shift + alt + k` | For moving windows with hjkl. |
| View: Move Editor into Group Below `workbench.action.moveEditorToBelowGroup` | -                       | `ctrl + shift + alt + j` | For moving windows with hjkl. |
| View: Move Editor into Group Left `workbench.action.moveEditorToLeftGroup`   | -                       | `ctrl + shift + alt + h` | For moving windows with hjkl. |
| View: Move Editor into Group Right `workbench.action.moveEditorToRightGroup` | -                       | `ctrl + shift + alt + l` | For moving windows with hjkl. |

```json
[
    {
        "key": "ctrl+shift+\\",
        "command": "-editor.action.jumpToBracket",
        "when": "editorTextFocus"
    },
    {
        "key": "ctrl+shift+\\",
        "command": "workbench.action.splitEditorOrthogonal"
    },
    {
        "key": "ctrl+k ctrl+\\",
        "command": "-workbench.action.splitEditorOrthogonal"
    },
    {
        "key": "ctrl+shift+k",
        "command": "-editor.action.deleteLines",
        "when": "textInputFocus && !editorReadonly"
    },
    {
        "key": "ctrl+shift+l",
        "command": "workbench.action.focusRightGroup"
    },
    {
        "key": "ctrl+k ctrl+right",
        "command": "-workbench.action.focusRightGroup"
    },
    {
        "key": "ctrl+shift+k",
        "command": "workbench.action.focusAboveGroup"
    },
    {
        "key": "ctrl+k ctrl+up",
        "command": "-workbench.action.focusAboveGroup"
    },
    {
        "key": "ctrl+shift+j",
        "command": "workbench.action.focusBelowGroup"
    },
    {
        "key": "ctrl+k ctrl+down",
        "command": "-workbench.action.focusBelowGroup"
    },
    {
        "key": "ctrl+shift+h",
        "command": "workbench.action.focusLeftGroup"
    },
    {
        "key": "ctrl+k ctrl+left",
        "command": "-workbench.action.focusLeftGroup"
    },
    {
        "key": "ctrl+shift+h",
        "command": "-workbench.action.replaceInFiles"
    },
    {
        "key": "ctrl+shift+alt+k",
        "command": "workbench.action.moveEditorToAboveGroup"
    },
    {
        "key": "ctrl+shift+alt+j",
        "command": "workbench.action.moveEditorToBelowGroup"
    },
    {
        "key": "ctrl+shift+alt+h",
        "command": "workbench.action.moveEditorToLeftGroup"
    },
    {
        "key": "ctrl+shift+alt+l",
        "command": "workbench.action.moveEditorToRightGroup"
    }
]
```


# vim shortcut binding

```json
{
    "vim.useSystemClipboard": true,
    "vim.incsearch": true,
    "vim.hlsearch": true,
    "vim.insertModeKeyBindingsNonRecursive": [
        {
            "before": [
                "j",
                "j"
            ],
            "after": [
                "<Esc>"
            ]
        }
    ],
    "vim.normalModeKeyBindingsNonRecursive": [
        {
            "before": [
                "J"
            ],
            "after": [
                "5",
                "j"
            ]
        },
        {
            "before": [
                "K"
            ],
            "after": [
                "5",
                "k"
            ]
        },
        {
            "before": [
                "<C-j>"
            ],
            "after": [
                "g",
                "j"
            ]
        },
        {
            "before": [
                "<C-k>"
            ],
            "after": [
                "g",
                "k"
            ]
        },
        {
            "before": [
                "<C-H>"
            ],
            "after": [
                "workbench.action.focusLeftGroup"
            ]
        },
        {
            "before": [
                "<C-L>"
            ],
            "after": [
                "workbench.action.focusRightGroup"
            ]
        },
        {
            "before": [
                "<C-J>"
            ],
            "after": [
                "workbench.action.focusBelowGroup"
            ]
        },
        {
            "before": [
                "<C-K>"
            ],
            "after": [
                "workbench.action.focusAboveGroup"
            ]
        },
        {
            "before": ["<C-M-H>"],
            "after": ["workbench.action.moveEditorLeftInGroup"]
        },
        {
            "before": ["<C-M-L>"],
            "after": ["workbench.action.moveEditorRightInGroup"]
        },
        {
            "before": ["<C-M-J>"],
            "after": ["workbench.action.moveEditorDownInGroup"]
        },
        {
            "before": ["<C-M-K>"],
            "after": ["workbench.action.moveEditorUpInGroup"]
        },
        {
            "before": [
                "<Tab>"
            ],
            "after": [
                ">",
                ">"
            ]
        },
        {
            "before": [
                "<S-Tab>"
            ],
            "after": [
                "<",
                "<"
            ]
        }
    ],
    "vim.handleKeys": {
        "<C-w>": false,
        "<C-t>": false,
        "<C-d>": true,
        "<C-s>": false,
        "<C-z>": false,
        "<C-F>": false,
        "<C-M-H>": true,
        "<C-M-L>": true,
        "<C-M-J>": true,
        "<C-M-K>": true,
    },
    "vim.statusBarColorControl": true,
    "vim.statusBarColors.normal": [
        "#8FBCBB",
        "#000"
    ],
    "vim.statusBarColors.insert": [
        "#BF616A",
        "#000"
    ],
    "vim.statusBarColors.visual": [
        "#B48EAD",
        "#000"
    ],
    "vim.statusBarColors.visualline": [
        "#B48EAD",
        "#000"
    ],
    "vim.statusBarColors.visualblock": [
        "#A3BE8C",
        "#000"
    ],
    "vim.statusBarColors.replace": "#D08770",
    "vim.statusBarColors.commandlineinprogress": "#007ACC",
    "vim.statusBarColors.searchinprogressmode": "#007ACC",
    "vim.statusBarColors.easymotionmode": "#007ACC",
    "vim.statusBarColors.easymotioninputmode": "#007ACC",
    "vim.statusBarColors.surroundinputmode": "#007ACC",
}
```

