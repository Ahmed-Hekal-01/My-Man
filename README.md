### Ktor 429 HTML Parsing Crash

**Symptom:** The API returns `429 Too Many Requests` as an HTML page instead of JSON. Ktor catches it as a generic `Exception` (like `SerializationException`) instead of a `ClientRequestException`.

**Root Cause:** Ktor tries to parse the HTML body into your JSON data class *before* checking the HTTP status code. The parser panics and crashes the pipeline.

**The Fix:** Force Ktor to validate the HTTP status code first so it throws the `4xx` exception before touching the body. Add `expectSuccess = true` to your HTTP client builder.

```kotlin
val client = HttpClient { // CIO, OkHttp, etc.
    expectSuccess = true
    // ... ContentNegotiation, logging, etc.
}

# 🚀 IdeaVim Configuration & Cheat Sheet

This configuration is optimized for **Android Development** on **Arch Linux**. It prioritizes system clipboard synchronization (`unnamedplus`) and fast navigation without taking hands off the home row.

## ⌨️ Key Bindings Cheat Sheet

### 📋 Clipboard & Editing
| Key | Mode | Action | Description |
| :--- | :--- | :--- | :--- |
| **`p`** | Visual | **Clean Paste** | Replaces selection with system clipboard (no extra newlines). |
| **`<Space>p`** | Normal | **Inline Paste** | Pastes system clipboard **after** the cursor on the current line. |
| **`p` / `P`** | Normal | **Standard Paste** | Standard Vim paste behavior. |
| **`<Space>d`** | Both | **Void Delete** | Deletes text without overwriting your clipboard. |
| **`ga`** | Both | **Multi-Cursor** | Selects all occurrences of the word under cursor. (Exit with `Esc`). |

### ⚡ Navigation & Flow
| Key | Action | Description |
| :--- | :--- | :--- |
| **`<Space>l`** | **Last File** | Instantly toggles between the current and previous file. |
| **`md`** | **Method Down** | Jumps to the start of the **Next** method/function. |
| **`mu`** | **Method Up** | Jumps to the start of the **Previous** method/function. |
| **`mm`** | **Clear Search** | Clears the yellow search highlights (`:noh`). |
| **`<Space>t`** | **Terminal** | Toggles the internal IDE terminal. |

### 🪟 Window Management
| Key | Action | Description |
| :--- | :--- | :--- |
| **`<Space>w`** | **Close Tab** | Closes the current editor tab. |
| **`<Space>ea`** | **Close Others** | Closes **ALL** tabs except the active one. |

### 🛠️ Code Intelligence & Refactoring
| Key | Action | Description |
| :--- | :--- | :--- |
| **`<Space><Space>`** | **Quick Fix** | Opens the "Alt+Enter" intention actions menu. |
| **`<Space>rn`** | **Rename** | Rename variable/class/method (Refactor). |
| **`<Space>ff`** | **Format** | Reformat code (`Ctrl+Alt+L`). |
| **`<Space>o`** | **Optimize** | Optimize Imports (`Ctrl+Alt+O`). |
| **`gd`** | **Go to Def** | Go to Declaration. |
| **`gr`** | **Find Usages** | Find where the symbol is used. |
| **`<Space>j` / `k`** | **Error Jump** | Jump to Previous/Next error in file. |

### 🐞 Debugging
| Key | Action | Description |
| :--- | :--- | :--- |
| **`<Space>dr`** | **Run** | Run App. |
| **`<Space>db`** | **Debug** | Debug App. |
| **`<Space>ds`** | **Stop** | Stop App. |
| **`<Space>b`** | **Breakpoint** | Toggle Line Breakpoint. |

---

## ⚙️ The `.ideavimrc` Configuration

Copy the code below into your `~/.ideavimrc` file.

```vim
" =============================
"  Leader Setup
" =============================
let mapleader = " "

" =============================
"  Basic Settings
" =============================
set scrolloff=8
set number
set relativenumber
set showmode
set showcmd
set visualbell

" --- CLIPBOARD SETTINGS (Arch Linux) ---
" 'unnamedplus' = System clipboard (Ctrl+C/V)
set clipboard=unnamedplus
set ignorecase
set smartcase
set incsearch
set hlsearch
set ideajoin
set idearefactormode=normal

" =============================
"  Plugins
" =============================
Plug 'tpope/vim-surround'
Plug 'tpope/vim-commentary'
Plug 'easymotion/vim-easymotion'

set easymotion
set surround
set commentary

" =============================
"  General Keymaps
" =============================
" CRITICAL FIX: Allow Esc to exit Multi-Cursor mode (ga)
nnoremap <Esc> <Esc>

" Clear Search Highlights
nnoremap mm :noh<CR>

" --- CONFIG MANAGEMENT ---
nnoremap <leader>vr :source ~/.ideavimrc<CR>

" --- MOVEMENT ---
" Move 5 lines up/down
nnoremap <C-j> 5j
nnoremap <C-k> 5k
vnoremap <C-j> 5j
vnoremap <C-k> 5k

" Move selected lines up/down in visual mode
vnoremap J :m '>+1<CR>gv=gv
vnoremap K :m '<-2<CR>gv=gv

" Keep cursor centered when scrolling/searching
nnoremap <C-d> <C-d>zz
nnoremap <C-u> <C-u>zz
nnoremap n nzzzv
nnoremap N Nzzzv

" --- EDITING ---
" FIX: Visual Mode Paste (Replaces highlight without newlines/losing clipboard)
vnoremap p "_c<C-r>+<Esc>

" FIX: Normal Mode Paste (Inline after cursor)
nnoremap <leader>p a<C-r>+<Esc>

" Standard Paste
nnoremap p p
nnoremap P P

" Delete to void register (keeps clipboard intact)
nnoremap <leader>d "_d
vnoremap <leader>d "_d

" Multi-Cursor (ga)
nmap ga :action SelectAllOccurrences<CR>
xmap ga :action SelectAllOccurrences<CR>

" --- SURROUND SHORTCUTS ---
" Normal Mode: <Space>s" -> surrounds word with "
nmap <leader>s ysiw
" Visual Mode: Select text -> <Space>s} -> surrounds selection with {}
xmap <leader>s S

" =============================
"  IDE Actions
" =============================
" Navigation
nnoremap <leader>t  :action ActivateTerminalToolWindow<CR>
nnoremap <leader>w  :action CloseContent<CR>
nnoremap <leader>ea :action CloseAllEditorsButActive<CR>
nnoremap <leader>l  <C-^>

" Method Navigation
nnoremap md :action MethodDown<CR>
nnoremap mu :action MethodUp<CR>

" Code Manipulation
nnoremap <leader>rn :action RenameElement<CR>
nnoremap <leader>ff :action ReformatCode<CR>
nnoremap <leader>o  :action OptimizeImports<CR>

" Go To...
nnoremap gd :action GotoDeclaration<CR>
nnoremap gi :action GotoImplementation<CR>
nnoremap gr :action FindUsages<CR>

" Errors & Intentions
nnoremap <leader><leader> :action ShowIntentionActions<CR>
nnoremap <leader>e  :action ShowErrorDescription<CR>
nnoremap <leader>j  :action GotoPreviousError<CR>
nnoremap <leader>k  :action GotoNextError<CR>

" Debugging & Running
nnoremap <leader>b  :action ToggleLineBreakpoint<CR>
nnoremap <leader>db :action Debug<CR>
nnoremap <leader>dr :action Run<CR>
nnoremap <leader>ds :action Stop<CR>
