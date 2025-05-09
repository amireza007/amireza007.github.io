---
authors: amireza007
layout: post
title: C++ in Termux
Date: '2025-04-12 14:02 +0330'
Categories: [Software Fun]
Tags: [Android Hacks, Termux, Neovim]
---

In this, I'd like to provide a quick guide for setting up the `Termux` app for C++ dev.

## Steps
0. `pkg install nodejs`
1. First install neovim
2. Install Vim-plug
3. `apt install clang`
4. put the following commands into your `init.vim` file:
   ```
   set number
    " set relativenumber
    set autoindent
    set tabstop=4
    set shiftwidth=4
    set smarttab
    set softtabstop=4
    set mouse=a
    set encoding=UTF-8
    set clipboard+=unnamedplus
    " Make sure you use single juotes
    call plug#begin('~/.config/nvim/autoload')

    " Shorthand notation; fetches https://github.com/junegunn/vim-easy-align
    Plug 'junegunn/vim-easy-align'

    " Any valid git URL is allowed
    Plug 'https://github.com/junegunn/vim-github-dashboard.git'

    " Multiple Plug commands can be written in a single line using | separators
    Plug 'SirVer/ultisnips' | Plug 'honza/vim-snippets'

    " On-demand loading
    Plug 'scrooloose/nerdtree', { 'on':  'NERDTreeToggle' }
    Plug 'tpope/vim-fireplace', { 'for': 'clojure' }

    " Using a non-default branch
    Plug 'rdnetto/YCM-Generator', { 'branch': 'stable' }

    " Using a tagged release; wildcard allowed (requires git 1.9.2 or above)
    Plug 'fatih/vim-go', { 'tag': '*' }

    " Plugin options
    Plug 'nsf/gocode', { 'tag': 'v.20150303', 'rtp': 'vim' }

    " Plugin outside ~/.vim/plugged with post-update hook
    Plug 'junegunn/fzf', { 'dir': '~/.fzf', 'do': './install --all' }

    " Unmanaged plugin (manually installed and updated)
    Plug '~/my-prototype-plugin'

    " Surrounding ysw)
    Plug 'http://github.com/tpope/vim-surround' 

    " NerdTree
    Plug 'https://github.com/preservim/nerdtree' 

    " For Commenting gcc & gc
    Plug 'https://github.com/tpope/vim-commentary' 

    " Status bar
    Plug 'https://github.com/vim-airline/vim-airline' 

    " PSQL Pluging needs :SQLSetType pgsql.vim
    "  -  Plug 'https://github.com/lifepillar/pgsql.vim' 

    " CSS Color Preview
    "  -  Plug 'https://github.com/ap/vim-css-color' 

    " Retro Scheme
    Plug 'https://github.com/rafi/awesome-vim-colorschemes' 

    " Auto Completion, Only Available in NeoVim (Not Vim), Needed Nodejs for Build Plugin
    Plug 'neoclide/coc.nvim', {'branch': 'release'}
    " Developer Icons
    " clie/coc.nvim',{':ranch':'release'}
    Plug 'https://github.com/ryanoasis/vim-devicons' 

    " Vim Terminal
    Plug 'https://github.com/tc50cal/vim-terminal' 

    " Tagbar for code navigation
    Plug 'https://github.com/preservim/tagbar' 
    " For this Plugin Need $sudo apt install exuberant-ctags

    " CTRL + N for multiple cursors
    Plug 'https://github.com/terryma/vim-multiple-cursors' 

    " Initialize plugin system
    call plug#end()

    colorscheme jellybeans

    nnoremap <C-f> :NERDTreeFocus<CR>
    nnoremap <C-n> :NERDTree<CR>
    nnoremap <C-t> :NERDTreeToggle<CR>
    nnoremap <C-l> :call CocActionAsync('jumpDefinition')<CR>

    nmap <F8> :TagbarToggle<CR>
    " For No Previews
    set completeopt-=preview 

    let g:NERDTreeDirArrowExpandable="+"
    let g:NERDTreeDirArrowCollapsible="~"

    "--- Just Some Notes ---
    " :PlugClean :PlugInstall :UpdateRemotePlugins
    "
    " :CocInstall coc-python
    " :CocInstall coc-clangd
    " :CocInstall coc-snippets
    " :CocCommand snippets.edit... FOR EACH FILE TYPE

    " air-line
    let g:airline_powerline_fonts = 1

    if !exists('g:airline_symbols')
    let g:airline_symbols = {}
    endif

    " airline symbols
    let g:airline_left_sep = ''
    let g:airline_left_alt_sep = ''
    let g:airline_right_sep = ''
    let g:airline_right_alt_sep = ''
    let g:airline_symbols.branch = ''
    let g:airline_symbols.readonly = ''
    let g:airline_symbols.linenr = ''

    inoremap <expr> <Tab> pumvisible() ? coc#_select_confirm() : "<Tab>"
    ```
5. `PlugInstall` 
6. Install coc-clangd with coc-install