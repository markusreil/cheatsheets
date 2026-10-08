# Neovim Config

Live config: `~/.config/nvim/`. Leader is `Space`, local-leader is `\`.

## `init.lua`

```lua
vim.g.mapleader = " "          -- Space as global leader
vim.g.maplocalleader = "\\"    -- Backslash as buffer-local leader

require("config.neovide")      -- GUI-only tweaks (cursor animation off)

require("config.lazy")         -- Bootstrap lazy.nvim + load lua/plugins/*
vim.keymap.set("n", "<leader>pl", "<esc>:Lazy<esc>")  -- Open plugin manager UI
```

### Options (`vim.cmd` block)

```vim
set nocompatible      " Disable Vi-compatible mode (use full Vim/Nvim features)
set ignorecase        " Case-insensitive search (no smartcase, so always insensitive)
set mouse=v           " (overridden below) mouse in visual mode
set mouse=a           " Enable mouse in all modes
set tabstop=4         " A <Tab> displays as 4 spaces
set softtabstop=4     " <Tab>/<BS> in insert mode act as 4 spaces
set expandtab         " Insert spaces instead of literal tabs
set shiftwidth=4      " Auto-indent width = 4 spaces
set autoindent        " Copy indent from current line on new line
set wildmode=longest,list  " Cmdline completion: longest match, then list
set number            " Show line numbers
filetype plugin indent on  " Enable filetype plugins + indent scripts
filetype plugin on         " (redundant with above) enable filetype plugins
set cursorline        " Highlight the current line
set ttyfast           " Fast terminal redraws
syntax on             " Enable syntax highlighting
set clipboard=unnamedplus  " Yank/paste uses system clipboard
set nohlsearch        " Don't keep search matches highlighted
```

### Keymaps (`init.lua`)

```lua
-- Reload / edit config
vim.keymap.set('n', '<leader>sv', ':source $MYVIMRC<enter>')   -- Re-source init.lua
vim.keymap.set('n', '<leader>cv', ':vsplit $MYVIMRC<enter>')    -- Edit init.lua in vsplit
-- Fast exit from insert mode (also moves one char right with `l`)
vim.keymap.set('i', 'jk', '<esc>l')  -- Type jk to escape
vim.keymap.set('i', 'jh', '<esc>l')  -- Type jh to escape (alt for typo-prone jk)
-- Save / quit
vim.keymap.set("n", "<c-s>", ":w<enter>")        -- Normal: save file
vim.keymap.set("i", "<c-s>", "<esc>:w<enter>li") -- Insert: save, return to insert
vim.keymap.set("n", "<c-q>", ":q<enter>")        -- Normal: quit window
vim.keymap.set("i", "<c-q>", "<esc>:q<enter>li") -- Insert: quit, return to insert
-- PgUp/PgDn scroll half a window (default is a full page like CTRL-F/CTRL-B)
vim.keymap.set({ "n", "v" }, "<PageUp>", "<C-u>")
vim.keymap.set({ "n", "v" }, "<PageDown>", "<C-d>")
-- File tree (toggle on `e`; `ev` deliberately avoided so <leader>e has no prefix clash)
-- Positions pinned: Neo-tree reuses the last position when omitted.
vim.keymap.set("n", "<leader>e", ":Neotree left reveal toggle<enter>")         -- Toggle Neo-tree, reveal current file
vim.keymap.set("n", "<leader>E", ":Neotree left reveal focus<enter>")          -- Reveal current file and focus the tree
vim.keymap.set("n", "<leader>gb", ":Neotree left buffers toggle<enter>")      -- Buffers as a Neo-tree source
vim.keymap.set("n", "<leader>gs", ":Neotree left git_status toggle<enter>")   -- Git status as a Neo-tree source
-- File search is Telescope's job now (see lua/plugins/telescope.lua):
-- <leader>ff finds files, <leader>fg greps contents. The sidebar reveals
-- each opened file via follow_current_file, so no manual reveal needed.
-- (No <leader>ff mapping here: lazy.nvim registers it from the spec.)
```

### Yank highlight (also duplicated in `lua/config/autocmds.lua`)

```lua
-- Flash yanked text for 500ms on every yank
vim.api.nvim_create_autocmd("TextYankPost", {
    group = vim.api.nvim_create_augroup('highlight_yank', {}), -- Group for the autocmd
    callback = function()
        (vim.hl or vim.highlight).on_yank({ timeout = 500 })   -- Compat shim: vim.hl (0.11+) or vim.highlight
    end,
})
```

## `lua/config/lazy.lua`

```lua
-- Bootstrap: clone folke/lazy.nvim (stable, blobless) into stdpath/data if missing
-- Abort with error message if git clone fails
vim.opt.rtp:prepend(lazypath)  -- Put lazy.nvim first on runtimepath
require("lazy").setup({
    spec = { { import = "plugins" } },          -- Load every file in lua/plugins/
    install = { colorscheme = { "habamax" } },  -- Fallback theme while installing
    checker = { enabled = true },               -- Auto-check for plugin updates
})
```

## `lua/config/neovide.lua`

```lua
if vim.g.neovide then
    vim.g.neovide_cursor_animation_length = 0  -- Disable cursor glide in Neovide GUI
end
```

## `lua/config/autocmds.lua`

Same yank-highlight autocmd as in `init.lua` (kept as the canonical home for autocmds).

## Plugins (`lua/plugins/`)

- `colorschemes.lua` — `gbprod/nord.nvim`, non-lazy, priority 1000, applies `colorscheme nord` at startup.
- `flash.lua` — `folke/flash.nvim` (VeryLazy): `jump.autojump = true`; search-mode integration on; char-mode shows jump labels, single-line only; `<leader>tf` toggles search-flash (reliable), cmdline `<C-s>` toggles mid-search (flaky).
- `nerdtree.lua` — `nvim-neo-tree/neo-tree.nvim` (v3.x, non-lazy) + plenary, devicons, nui deps; `close_if_last_window`, `follow_current_file`, libuv watcher, custom `Z` expand-all, `file_opened` handler that closes floats only.
- `surround.lua` — `kylechui/nvim-surround` v3, VeryLazy, default setup (add/change/delete surroundings).
- `telescope.lua` — `nvim-telescope/telescope.nvim` (master; 0.1.x is stale and calls the removed `ft_to_lang` API) + plenary; `<leader>ff` find files (fd backend), `<leader>fg` live grep (rg backend).
- `textobjects.lua` — `chrisgrieser/nvim-various-textobjs` with `keymaps.useDefaults = true` (extra text objects, e.g. subword/value/indent).
- `treesitter.lua` — `nvim-treesitter/nvim-treesitter` (new main-branch API: async `install()`, no auto-install from setup); 13 parsers (c, lua, vim, vimdoc, query, elixir, heex, javascript, html, java, kotlin, typescript, tsx); FileType autocmd enables core highlighting + TS indent. Needs the `tree-sitter` CLI to compile parsers.
- `lsp.lua` — `mason-org/mason.nvim` + `mason-lspconfig.nvim` (`ensure_installed` takes lspconfig names) + `nvim-lspconfig`; servers `lua_ls`, `jdtls`, `kotlin_language_server`, `ts_ls`, each with blink capabilities; JDK 21+ preferred for jdtls.
- `blink.lua` — `saghen/blink.cmp` (v1.* prebuilt); default preset keys + auto docs; `<C-space>` manual trigger.
- `trouble.lua` — `folke/trouble.nvim` (cmd + keys lazy-load); `<leader>xx`/`xX` workspace/buffer diagnostics, `<leader>cs` symbols, `<leader>xQ` quickfix.
- `whichkey.lua` — `folke/which-key.nvim` (VeryLazy), defaults; `<leader>?` shows buffer-local keymaps.
- `gitsigns.lua` — `lewis6991/gitsigns.nvim` (VeryLazy, not BufReadPre: otherwise the first file silently never attaches); gutter signs + `]c`/`[c` hunk nav, `<leader>hs/hr/hu/hp/hb` stage/reset/undo/preview/blame.
- `lualine.lua` — `nvim-lualine/lualine.nvim` (VeryLazy), `theme = "auto"` follows nord.
- `comment.lua` — `numToStr/Comment.nvim` (VeryLazy), defaults: `gcc` line, `gc` motion/selection, `gb` block.

## Cheatsheet

Shortcuts live in [CHEATSHEET.md](CHEATSHEET.md).

## Plugin examples

### Flash (`?`, `r`, `R`, search integration)

```
/foo<CR>      regular search, but every match gets a jump label;
              press the label to jump (single match jumps directly)
f( / t;       char motions show labels when several matches share a line
?             labels on Treesitter nodes (functions, classes, blocks);
              press a label to jump straight to that node
d? / y?       same node picker as an operator target: delete/yank a whole
              function or block by picking its label
v + R         Treesitter search on the visual selection
c + <C-s>     while typing a / search, toggles Flash for that search
              (flaky when re-enabling: use <leader>tf from normal mode first)
r (pending)   remote action, e.g. yr then a label: yank text at a distance
              without moving the cursor
```

### Surround (`ys`, `ds`, `cs`, visual `S`)

```
ysiw"          wrap word under cursor in quotes:  hello -> "hello"
yss)            wrap the whole line in parens
ds"             delete surrounding quotes:          "hello" -> hello
cs"'            change surrounding " into ':          "hello" -> 'hello'
ys2w]           wrap next 2 words in brackets
ve + S}        visual-select, then S}: wrap selection in { ... }
```

### Text objects (`nvim-various-textobjs` defaults, usable with `d`/`c`/`y`/`v`)

```
dii / dai     delete inner / outer indentation block (same indent level)
civ           change the value in `key = value` (keeps key, `=` and spacing)
dik           delete the key in `key = value`
cin / can    change / delete a number:  port = 8080 -> port = _
diS           delete one camelCase/snake_case segment under cursor
di,           delete the argument under cursor in f(a, b, c)
ygG           yank the entire buffer (gG = whole-buffer object)
ygw           yank everything visible in the window
daq           delete any quoted string ('..', ".."), whichever is nearest
dao           delete around the nearest bracket pair of any kind
viF           visually select the file path under cursor
yL            yank the URL under cursor
dn            delete to near end of line
```
