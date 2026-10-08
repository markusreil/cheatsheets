# Neovim Cheatsheet

Leader = `Space`. Live config: `~/.config/nvim/`. Details in [README.md](README.md).

## Moving around (core Vim motions)

| Keys | Action |
|------|--------|
| `h` / `j` / `k` / `l` | Left / down / up / right |
| `w` / `b` / `e` | Next word / back a word / end of word |
| `0` / `^` / `$` | Line start / first non-blank / line end |
| `gg` / `G` | First / last line (or `{n}G` for line n) |
| `{` / `}` | Previous / next blank-line-delimited block |
| `%` | Jump to matching bracket |
| `f{ch}` / `t{ch}` | Jump to / just before next `ch` on line (`F`/`T` backwards) |
| `;` / `,` | Repeat last `f`/`t` forward / backward |
| `*` / `#` | Next / previous occurrence of word under cursor |
| `n` / `N` | Next / previous search match (same direction as search) |
| `H` / `M` / `L` | Top / middle / bottom of screen |
| `zz` / `zt` / `zb` | Center / top / bottom current line on screen |
| `''` / `` `` `` | Back to line / exact position before last jump |
| `gi` | Insert at last insert position |

## Shortcuts

| Keys | Mode | Action |
|------|------|--------|
| `<leader>pl` | Normal | Open Lazy plugin manager |
| `<leader>sv` | Normal | Re-source `$MYVIMRC` |
| `<leader>cv` | Normal | Edit `$MYVIMRC` in vertical split |
| `<leader>e` | Normal | Toggle Neo-tree (reveal current file) |
| `<leader>E` | Normal | Reveal current file in Neo-tree and focus the tree |
| `<leader>gb` | Normal | Open buffers as a Neo-tree source |
| `<leader>gs` | Normal | Open git status as a Neo-tree source |
| `<leader>ff` | Normal | Telescope: find and open a file (sidebar auto-reveals it) |
| `<leader>fg` | Normal | Telescope: live grep file contents |
| `<leader>xo` | Normal | Open current file with system default app (PDFs, HTML, ...) |
| `<leader>?` | Normal | Show buffer-local keymaps (which-key) |
| `jk` / `jh` | Insert | Escape to Normal (cursor steps right) |
| `<C-s>` | Normal / Insert | Save file |
| `<C-s>` | Cmdline | Toggle Flash search mid-input (flaky on re-enable; prefer `<leader>tf`) |
| `<leader>tf` | Normal | Toggle Flash search (reliable; use before `/`) |
| `<C-q>` | Normal / Insert | Quit window |
| `PgUp` / `PgDn` | Normal / Visual | Scroll half a window (instead of full page) |
| `:qa` | Cmdline | Quit all windows |
| `:wqa` / `:xa` | Cmdline | Save all windows and quit |
| `:qa!` | Cmdline | Quit all windows without saving |
| `?` | Normal / Visual / Operator | Flash Treesitter jump |
| `r` | Operator-pending | Remote Flash action |
| `R` | Operator-pending / Visual | Flash Treesitter search |
| `s`-family | Normal / Visual / Operator | nvim-surround: add/change/delete surroundings (defaults) |
| `gcc` / `gc` | Normal (+ Visual for `gc`) | Comment line / motion or selection |
| `gb` | Normal / Visual | Block comment |
| `]c` / `[c` | Normal | Next / previous git hunk (gitsigns) |
| `<leader>hs` | Normal / Visual | Stage hunk (or visual selection) |
| `<leader>hr` | Normal / Visual | Reset hunk (or visual selection) |
| `<leader>hu` | Normal | Undo stage hunk |
| `<leader>hp` | Normal | Preview hunk |
| `<leader>hb` | Normal | Blame current line (full) |
| extra text objects | Visual / Operator | nvim-various-textobjs defaults (e.g. subword, value, indent) |

## Neo-tree (file explorer)

Open with `<leader>e` (toggles, reveals current file); `<leader>E` reveals and focuses. Defaults plus a custom `Z` binding (see README); cursor on a tree node:

### Window navigation

| Keys | Action |
|------|--------|
| `<C-w>l` | Move to window on the right (tree to editor) |
| `<C-w>h` | Move to window on the left (editor to tree) |
| `<C-w>w` | Cycle to next window (either direction) |
| `<C-w>p` | Jump back to previous window |

### Expand / collapse

| Keys | Action |
|------|--------|
| `<Space>` | Toggle node under cursor (expand/collapse directory) |
| `<CR>` | Open file, or expand directory |
| `C` | Close node under cursor |
| `z` | Close all nodes |
| `Z` | Expand all nodes (bound in our opts; upstream leaves it commented out) |
| `<BS>` | Navigate up to parent directory |
| `.` | Set current node as tree root |

### Find / filter (file search)

| Keys | Action |
|------|--------|
| `/` | Fuzzy finder: filter tree as you type (`<Esc>` closes, `<C-x>` in tree clears filter) |
| `D` | Fuzzy finder rooted at directory under cursor |
| `#` | Fuzzy sort results (fzy algorithm) |
| `f` | Filter on submit (keep only matches) |
| `<C-x>` | Clear active filter |
| `H` | Toggle hidden files (dotfiles) |
| `?` | Show all tree keymaps (help popup) |

### Refresh

| Keys | Action |
|------|--------|
| `R` | Refresh the tree (needed for external changes: git checkout, other programs; saves from inside Neovim refresh automatically) |

## Plugin examples

### Flash

| Keys | Action |
|------|--------|
| `/foo` | Search with a jump label on every match; press label to jump (single match jumps directly) |
| `f(` / `t;` | Char motions show labels when several matches share a line |
| `?` | Labels on Treesitter nodes; press one to jump to that function/block |
| `d?` / `y?` | Delete/yank a whole node by picking its label |
| `v` then `R` | Treesitter search on the visual selection |
| `/` then `<C-s>` | Toggle Flash for the search being typed (flaky on re-enable; prefer `<leader>tf`) |
| `yr` + label | Yank text at a distance without moving the cursor (remote action) |

### Surround

| Keys | Result |
|------|--------|
| `ysiw"` | `hello` -> `"hello"` |
| `yss)` | Wrap whole line in parens |
| `ds"` | `"hello"` -> `hello` |
| `cs"'` | `"hello"` -> `'hello'` |
| `ys2w]` | Wrap next 2 words in brackets |
| visual `S}` | Wrap selection in `{ ... }` |

### Text objects (work with `d`/`c`/`y`/`v`)

| Keys | Action |
|------|--------|
| `ii` / `ai` | Inner / outer indentation block |
| `iv` | Change the value in `key = value` |
| `ik` | Delete the key in `key = value` |
| `in` / `an` | Change / delete a number |
| `iS` | Delete one camelCase/snake_case segment |
| `i,` | Delete the argument under cursor |
| `gG` | Entire buffer (e.g. `ygG` yanks all) |
| `gw` | Visible window content (e.g. `ygw`) |
| `aq` | Delete any quoted string |
| `ao` | Operate around nearest bracket pair |
| `iF` | Select the file path under cursor |
| `L` | Operate on the URL under cursor (e.g. `yL`) |
| `n` | Operate to near end of line (e.g. `dn`) |

## Completion (blink.cmp, default preset)

| Keys | Mode | Action |
|------|------|--------|
| `<C-space>` | Insert | Show completion menu / docs |
| `<C-n>` / `<C-p>` (or Up/Down) | Insert | Next / previous item |
| `<C-y>` | Insert | Accept selected item |
| `<C-e>` | Insert | Dismiss menu |
| `<C-b>` / `<C-f>` | Insert | Scroll docs pane |
| `Tab` / `S-Tab` | Insert | Next / previous snippet placeholder |
| `<C-k>` | Insert | Toggle signature help |

## LSP (built-in defaults, active when a server is attached)

| Keys | Mode | Action |
|------|------|--------|
| `K` | Normal | Hover documentation |
| `grn` | Normal | Rename symbol |
| `gra` | Normal / Visual | Code action |
| `grr` | Normal | Go to references |
| `gri` | Normal | Go to implementation |
| `gO` | Normal | Document symbols |
| `[d` / `]d` | Normal | Previous / next diagnostic |
| `:Mason` | Cmdline | Manage installed language servers |

## Trouble (diagnostics and lists)

| Keys | Mode | Action |
|------|------|--------|
| `<leader>xx` | Normal | Workspace diagnostics |
| `<leader>xX` | Normal | Current buffer diagnostics |
| `<leader>cs` | Normal | Document symbols browser |
| `<leader>xQ` | Normal | Quickfix list |
