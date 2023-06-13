# xargs

# grep -A 5 -B 5

# base64 --decode

# awk

# sed

# dmesg

```

                   SUMMARY OF LESS COMMANDS

      Commands marked with * may be preceded by a number, N.
      Notes in parentheses indicate the behavior if N is given.
      A key preceded by a caret indicates the Ctrl key; thus ^K is ctrl-K.

  h  H                 Display this help.
  q  :q  Q  :Q  ZZ     Exit.
 ---------------------------------------------------------------------------

                           MOVING

  e  ^E  j  ^N  CR  *  Forward  one line   (or N lines).
  y  ^Y  k  ^K  ^P  *  Backward one line   (or N lines).
  f  ^F  ^V  SPACE  *  Forward  one window (or N lines).
  b  ^B  ESC-v      *  Backward one window (or N lines).
  z                 *  Forward  one window (and set window to N).
  w                 *  Backward one window (and set window to N).
  ESC-SPACE         *  Forward  one window, but don't stop at end-of-file.
  d  ^D             *  Forward  one half-window (and set half-window to N).
  u  ^U             *  Backward one half-window (and set half-window to N).
  ESC-)  RightArrow *  Left  one half screen width (or N positions).
  ESC-(  LeftArrow  *  Right one half screen width (or N positions).
  F                    Forward forever; like "tail -f".
  r  ^R  ^L            Repaint screen.
  R                    Repaint screen, discarding buffered input.
        ---------------------------------------------------
        Default "window" is the screen height.
        Default "half-window" is half of the screen height.
 ---------------------------------------------------------------------------

                          SEARCHING

  /pattern          *  Search forward for (N-th) matching line.
  ?pattern          *  Search backward for (N-th) matching line.
  n                 *  Repeat previous search (for N-th occurrence).
  N                 *  Repeat previous search in reverse direction.
  ESC-n             *  Repeat previous search, spanning files.
  ESC-N             *  Repeat previous search, reverse dir. & spanning files.
  ESC-u                Undo (toggle) search highlighting.
  &pattern          *  Display only matching lines
        ---------------------------------------------------
        A search pattern may be preceded by one or more of:
        ^N or !  Search for NON-matching lines.
        ^E or *  Search multiple files (pass thru END OF FILE).
        ^F or @  Start search at FIRST file (for /) or last file (for ?).
        ^K       Highlight matches, but don't move (KEEP position).
        ^R       Don't use REGULAR EXPRESSIONS.
 ---------------------------------------------------------------------------

                           JUMPING

  g  <  ESC-<       *  Go to first line in file (or line N).
  G  >  ESC->       *  Go to last line in file (or line N).
  p  %              *  Go to beginning of file (or N percent into file).
  t                 *  Go to the (N-th) next tag.
  T                 *  Go to the (N-th) previous tag.
  {  (  [           *  Find close bracket } ) ].
  }  )  ]           *  Find open bracket { ( [.
  ESC-^F <c1> <c2>  *  Find close bracket <c2>.
  ESC-^B <c1> <c2>  *  Find open bracket <c1> 
        ---------------------------------------------------
        Each "find close bracket" command goes forward to the close bracket 
          matching the (N-th) open bracket in the top line.
        Each "find open bracket" command goes backward to the open bracket 
          matching the (N-th) close bracket in the bottom line.

  m<letter>            Mark the current position with <letter>.
  '<letter>            Go to a previously marked position.
  ''                   Go to the previous position.
  ^X^X                 Same as '.
        ---------------------------------------------------
        A mark is any upper-case or lower-case letter.
        Certain marks are predefined:
             ^  means  beginning of the file
             $  means  end of the file
 ---------------------------------------------------------------------------

                        CHANGING FILES

  :e [file]            Examine a new file.
  ^X^V                 Same as :e.
  :n                *  Examine the (N-th) next file from the command line.
  :p                *  Examine the (N-th) previous file from the command line.
  :x                *  Examine the first (or N-th) file from the command line.
  :d                   Delete the current file from the command line list.
  =  ^G  :f            Print current file name.
 ---------------------------------------------------------------------------

                    MISCELLANEOUS COMMANDS

  -<flag>              Toggle a command line option [see OPTIONS below].
  --<name>             Toggle a command line option, by name.
  _<flag>              Display the setting of a command line option.
  __<name>             Display the setting of an option, by name.
  +cmd                 Execute the less cmd each time a new file is examined.

  !command             Execute the shell command with $SHELL.
  |Xcommand            Pipe file between current pos & mark X to shell command.
  v                    Edit the current file with $VISUAL or $EDITOR.
  V                    Print version number of "less".
 ---------------------------------------------------------------------------

                           OPTIONS

        Most options may be changed either on the command line,
        or from within less by using the - or -- command.
        Options may be given in one of two forms: either a single
        character preceded by a -, or a name preceded by --.

  -?  ........  --help
                  Display help (from command line).
  -a  ........  --search-skip-screen
                  Search skips current screen.
  -A  ........  --SEARCH-SKIP-SCREEN
                  Search starts just after target line.
  -b [N]  ....  --buffers=[N]
                  Number of buffers.
  -B  ........  --auto-buffers
                  Don't automatically allocate buffers for pipes.
  -c  -C  ....  --clear-screen --CLEAR-SCREEN
                  Repaint by clearing rather than scrolling.
  -d  ........  --dumb
                  Dumb terminal.
  -D [xn.n]  .  --color=xn.n
                  Set screen colors. (MS-DOS only)
  -e  -E  ....  --quit-at-eof  --QUIT-AT-EOF
                  Quit at end of file.
  -f  ........  --force
                  Force open non-regular files.
  -F  ........  --quit-if-one-screen
                  Quit if entire file fits on first screen.
  -g  ........  --hilite-search
                  Highlight only last match for searches.
  -G  ........  --HILITE-SEARCH
                  Don't highlight any matches for searches.
  --old-bot
                  Revert to the old bottom of screen behavior.
  -h [N]  ....  --max-back-scroll=[N]
                  Backward scroll limit.
  -i  ........  --ignore-case
                  Ignore case in searches that do not contain uppercase.
  -I  ........  --IGNORE-CASE
                  Ignore case in all searches.
  -j [N]  ....  --jump-target=[N]
                  Screen position of target lines.
  -J  ........  --status-column
                  Display a status column at left edge of screen.
  -k [file]  .  --lesskey-file=[file]
                  Use a lesskey file.
  -K            --quit-on-intr
                  Exit less in response to ctrl-C.
  -L  ........  --no-lessopen
                  Ignore the LESSOPEN environment variable.
  -m  -M  ....  --long-prompt  --LONG-PROMPT
                  Set prompt style.
  -n  ........  --line-numbers
                  Don't use line numbers.
  -N  ........  --LINE-NUMBERS
                  Use line numbers.
  -o [file]  .  --log-file=[file]
                  Copy to log file (standard input only).
  -O [file]  .  --LOG-FILE=[file]
                  Copy to log file (unconditionally overwrite).
  -p [pattern]  --pattern=[pattern]
                  Start at pattern (from command line).
  -P [prompt]   --prompt=[prompt]
                  Define new prompt.
  -q  -Q  ....  --quiet  --QUIET  --silent --SILENT
                  Quiet the terminal bell.
  -r  -R  ....  --raw-control-chars  --RAW-CONTROL-CHARS
                  Output "raw" control characters.
  -s  ........  --squeeze-blank-lines
                  Squeeze multiple blank lines.
  -S  ........  --chop-long-lines
                  Chop (truncate) long lines rather than wrapping.
  -t [tag]  ..  --tag=[tag]
                  Find a tag.
  -T [tagsfile] --tag-file=[tagsfile]
                  Use an alternate tags file.
  -u  -U  ....  --underline-special  --UNDERLINE-SPECIAL
                  Change handling of backspaces.
  -V  ........  --version
                  Display the version number of "less".
  -w  ........  --hilite-unread
                  Highlight first new line after forward-screen.
  -W  ........  --HILITE-UNREAD
                  Highlight first new line after any forward movement.
  -x [N[,...]]  --tabs=[N[,...]]
                  Set tab stops.
  -X  ........  --no-init
                  Don't use termcap init/deinit strings.
  -y [N]  ....  --max-forw-scroll=[N]
                  Forward scroll limit.
  -z [N]  ....  --window=[N]
                  Set size of window.
  -" [c[c]]  .  --quotes=[c[c]]
                  Set shell quote characters.
  -~  ........  --tilde
                  Don't display tildes after end of file.
  -# [N]  ....  --shift=[N]
                  Horizontal scroll amount (0 = one half screen width)
      ........  --no-keypad
                  Don't send termcap keypad init/deinit strings.
      ........  --follow-name
                  The F command changes files if the input file is renamed.
      ........  --use-backslash
                  Subsequent options use backslash as escape char.


 ---------------------------------------------------------------------------

                          LINE EDITING

        These keys can be used to edit text being entered 
        on the "command line" at the bottom of the screen.

 RightArrow                       ESC-l     Move cursor right one character.
 LeftArrow                        ESC-h     Move cursor left one character.
 ctrl-RightArrow  ESC-RightArrow  ESC-w     Move cursor right one word.
 ctrl-LeftArrow   ESC-LeftArrow   ESC-b     Move cursor left one word.
 HOME                             ESC-0     Move cursor to start of line.
 END                              ESC-$     Move cursor to end of line.
 BACKSPACE                                  Delete char to left of cursor.
 DELETE                           ESC-x     Delete char under cursor.
 ctrl-BACKSPACE   ESC-BACKSPACE             Delete word to left of cursor.
 ctrl-DELETE      ESC-DELETE      ESC-X     Delete word under cursor.
 ctrl-U           ESC (MS-DOS only)         Delete entire line.
 UpArrow                          ESC-k     Retrieve previous command line.
 DownArrow                        ESC-j     Retrieve next command line.
 TAB                                        Complete filename & cycle.
 SHIFT-TAB                        ESC-TAB   Complete filename & reverse cycle.
 ctrl-L                                     Complete filename, list all.
```

# top

### important

- `E` / `e` - summary/task memory scale
- `f` / `F` - set or sort with field: 
  > Navigate with Up/Dn, Right selects for move then <Enter> or Left commits, 'd' or <Space> toggles display, 's' sets sort.  Use 'q' or <Esc> to end!

### meaning

cpu:

us - Time spent in user space
sy - Time spent in kernel space
ni - Time spent running niced user processes (User defined priority)
id - Time spent in idle operations
wa - Time spent on waiting on IO peripherals (eg. disk)
hi - Time spent handling hardware interrupt routines. (Whenever a peripheral unit want attention form the CPU, it literally pulls a line, to signal the CPU to service it)
si - Time spent handling software interrupt routines. (a piece of code, calls an interrupt routine...)
st - Time spent on involuntary waits by virtual cpu while hypervisor is servicing another processor (stolen from a virtual machine)

Iris mode, Solaris mode:

> 例えば、Linuxカーネルが認識するCPUが4個存在するとする。プロセスはこの4個の内のいずれかのCPU,ここではCPU3としよう、に存在する。そして、そのプロセスがそのCPU3の半分を使用しているのであるならば、IrisモードではCPU使用率が50%として表示される。もし4個すべてのCPUがフル稼働になると、CPU使用率の合計は400%となる。

> これに対して、SolarisモードではCPU3に乗っているプロセスがそのCPUの半分を使用しているとすると、CPU全体に対する割合、つまり50÷4＝12.5%と表示される。Solarisモードでは、すべてのプロセスのCPU使用率を合計すると、きっちり100%となる。

process
PR	NI	VIRT	RES	SHR	S	%CPU	%MEM	TIME+
優先度	相対優先度	仮想メモリ	物理メモリ	共有メモリ	状態	CPU使用率	メモリ使用率	実行時間

- NI: Nice value。相対優先度。0が基準で、負だと優先度が高く、正だと優先度が低い。
- VIRT: Virtual Image。確保された仮想メモリ全て。スワップしたメモリ使用量を含む。
- RES: Resident size。 スワップしていない、使用した物理メモリのサイズ。
- SHR: Shared Mem size。 他のプロセスと共有される可能性のあるメモリのサイズ。
- S: Process Status。 以下のいずれの状態であるかを示している。
    - D: 割り込み不能
    - R: 実行中
    - S: スリープ状態
    - T: 停止中
    - Z: ゾンビプロセス

```
Help for Interactive Commands - procps-ng version 3.3.10
Window 1:Def: Cumulative mode Off.  System: Delay 3.0 secs; Secure mode Off.

  Z,B,E,e   Global: 'Z' colors; 'B' bold; 'E'/'e' summary/task memory scale
  l,t,m     Toggle Summary: 'l' load avg; 't' task/cpu stats; 'm' memory info
  0,1,2,3,I Toggle: '0' zeros; '1/2/3' cpus or numa node views; 'I' Irix mode
  f,F,X     Fields: 'f'/'F' add/remove/order/sort; 'X' increase fixed-width

  L,&,<,> . Locate: 'L'/'&' find/again; Move sort column: '<'/'>' left/right
  R,H,V,J . Toggle: 'R' Sort; 'H' Threads; 'V' Forest view; 'J' Num justify
  c,i,S,j . Toggle: 'c' Cmd name/line; 'i' Idle; 'S' Time; 'j' Str justify
  x,y     . Toggle highlights: 'x' sort field; 'y' running tasks
  z,b     . Toggle: 'z' color/mono; 'b' bold/reverse (only if 'x' or 'y')
  u,U,o,O . Filter by: 'u'/'U' effective/any user; 'o'/'O' other criteria
  n,#,^O  . Set: 'n'/'#' max tasks displayed; Show: Ctrl+'O' other filter(s)
  C,...   . Toggle scroll coordinates msg for: up,down,left,right,home,end

  k,r       Manipulate tasks: 'k' kill; 'r' renice
  d or s    Set update interval
  W,Y       Write configuration file 'W'; Inspect other output 'Y'
  q         Quit
          ( commands shown with '.' require a visible task display window ) 
Press 'h' or '?' for help with Windows,
Type 'q' or <Esc> to continue 
```
