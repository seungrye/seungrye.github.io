---
title: "How to setup neovim for rust on windows"
date: 2020-01-13T23:46:32+09:00
categories: [howtolog]
tags: [neovim, nvim, rust, window]
draft: false
---

We need rust, neovim, python. that's it.

<!--more-->

First of all. 

# Install neovim.
I use chocolately. 

Note. Installing Chocolately guide : https://chocolatey.org/install

I can find neovim package in choco

```
C:\Windows\system32>choco search neovim
Chocolatey v0.10.15
neovim 0.4.3 [Approved]
1 packages found.
C:\Users\seung>choco install neovim -y
```

But to support rust on nvim properly. We need nvim 0.5.0+.
I just install 0.4.3 version via choco then overwrite archived neovim 0.5.0 package (nvim-win64.zip) located in https://github.com/neovim/neovim/releases

Note. neovim via choco will installed in C:\tools\neovim

Then I can get following version information.

```
C:\Windows\system32>nvim --version
NVIM v0.5.0-309-g05ea3c199
Build type: RelWithDebInfo
LuaJIT 2.1.0-beta3
Compilation: C:/Program Files (x86)/Microsoft Visual Studio/2017/Community/VC/Tools/MSVC/14.16.27023/bin/Hostx86/x64/cl.exe /DWIN32 /D_WINDOWS /W3 /MD /Zi /O2 /Ob1 /DNDEBUG /W3 -D_CRT_SECURE_NO_WARNINGS -D_CRT_NONSTDC_NO_DEPRECATE -DWIN32 -D_WIN32_WINNT=0x0600 -DINCLUDE_GENERATED_DECLARATIONS -DUTF8PROC_STATIC -DNVIM_MSGPACK_HAS_FLOAT32 -DNVIM_UNIBI_HAS_VAR_FROM -DMIN_LOG_LEVEL=3 -IC:/projects/neovim/build/config -IC:/projects/neovim/src -IC:/projects/nvim-deps/usr/include -IC:/projects/neovim/build/src/nvim/auto -IC:/projects/neovim/build/include
Compiled by appveyor@APPVYR-WIN

Features: -acl +iconv +tui
See ":help feature-compile"

   system vimrc file: "$VIM\sysinit.vim"
  fall-back for $VIM: "C:/Program Files/nvim/share/nvim"

Run :checkhealth for more info
```

# Install python
Do not install python via window store.
neovim can not find proper python path.

I use choco to install python3

```
C:\Users\seung>choco search python3
Chocolatey v0.10.15
python3 3.8.1.20200110 [Approved]
...
5 packages found.

C:\Users\seung>choco install python3 -y
```

# Install rust
Install the rust (https://www.rust-lang.org/tools/install) and some packages

```
rustup update
rustup component add rustfmt
rustup component add clippy
```

# Build and install `rust-analyzer`
Note. it require rust version 1.4.0+
Note. it requires terminal with Administrator permission.

```
C:\Windows\system32>cd %HOMEPATH%
C:\Users\seung>git clone https://github.com/rust-analyzer/rust-analyzer
Cloning into 'rust-analyzer'...
remote: Enumerating objects: 22, done.
remote: Counting objects: 100% (22/22), done.
remote: Compressing objects: 100% (19/19), done.
remote: Total 62042 (delta 4), reused 18 (delta 2), pack-reused 62020
Receiving objects: 100% (62042/62042), 16.27 MiB | 4.72 MiB/s, done.
Resolving deltas: 100% (40224/40224), done.
C:\Users\seung>cd rust-analyzer
C:\Users\seung\rust-analyzer>cargo xtask install --server
...
C:\Users\seung\rust-analyzer>cd ..
C:\Users\seung>rmdir /S /Q rust-analyzer
C:\Users\seung>ra_lsp_server --version
rust-analyzer 875352b
```


# Install nvim plugin `vim-plug`
Open PowerShell terminal and type following

Note. see https://jdhao.github.io/2018/11/15/neovim_configuration_windows/#install-plugin-manager-vim-plug

```
PS C:\Users\seung> md ~\AppData\Local\nvim\autoload


    디렉터리: C:\Users\seung\AppData\Local\nvim


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----     2020-01-13  오후 11:12                autoload


PS C:\Users\seung> $uri = 'https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'
PS C:\Users\seung> (New-Object Net.WebClient).DownloadFile(
>>   $uri,
>>   $ExecutionContext.SessionState.Path.GetUnresolvedProviderPathFromPSPath(
>>     "~\AppData\Local\nvim\autoload\plug.vim"
>>   )
>> )
PS C:\Users\seung>pip install pynvim
```

# Setup neovim for rust development
edit init.vim file on "~\AppData\Local\nvim\init.vim"

Note. following setting is in https://dev.to/drmason13/configure-neovim-for-rust-development-1fjn

```
call plug#begin()
Plug 'neovim/nvim-lsp'
Plug 'Shougo/deoplete.nvim', { 'do': ':UpdateRemotePlugins' }
Plug 'Shougo/deoplete-lsp'
Plug 'ervandew/supertab'
Plug 'Chiel92/vim-autoformat'
call plug#end()

let g:python3_host_prog = expand('C:/Python38/python.exe') 

" setup rust_analyzer LSP (IDE features)
lua require'nvim_lsp'.rust_analyzer.setup{}

" Use LSP omni-completion in Rust files
autocmd Filetype rust setlocal omnifunc=v:lua.vim.lsp.omnifunc

" Enable deoplete autocompletion in Rust files
let g:deoplete#enable_at_startup = 1

" customise deoplete                                                                                                                                                     " maximum candidate window length
call deoplete#custom#source('_', 'max_menu_width', 80)

" Press Tab to scroll _down_ a list of auto-completions
let g:SuperTabDefaultCompletionType = "<c-n>"

" rustfmt on write using autoformat
autocmd BufWrite * :Autoformat

"TODO: clippy on write
autocmd BufWrite * :Autoformat

nnoremap <leader>c :!cargo clippy
```

**Important.** I just combind the contents of the Internet, the source of each sections are included in the text.