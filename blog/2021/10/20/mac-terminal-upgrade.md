---
title: Mac Terminal Upgrade
description: Sharp new terminal. Same old you.
date: 2021-10-20
readingTime: 5 minutes
tags: ["shell", "zsh", "vim", "macOS"]
---

{% image "./blog/2021/10/20/terminal.jpeg", "Computer terminal command lines", "(max-width: 640px) 100%, 100%", page.url %}

I've been spending a lot more time in the terminal lately so I decided it was time for an upgrade. This post will go through the four things I did to level up my macOS terminal.

1. Switch from the Mac Terminal to [iTerm2](https://iterm2.com/).
2. Install [Oh My Zsh](https://ohmyz.sh/) and some plugins
3. Install the [Powerlevel10k](https://github.com/romkatv/powerlevel10k) Zsh theme.
4. Theme Vim using a package manager

This post assumes a basic knowledge of using a terminal and shell commands. I'm using the Z shell on macOS. Bash is widely used but zsh is picking up more steam now that it is the default shell of macOS since late 2019.

The zsh configuration is managed in the **_~/.zshrc_** file in your home directory. If you're using a bash shell it's **_~/.bashrc_** and the only information in this post that applies to you is iTerm2 and the Vim setup.

## Mac Terminal to iTerm2

This one is easy. Go forth and [download iTerm2](https://iterm2.com/downloads.html) from the internet. Enable [Shell Integration](https://iterm2.com/documentation-shell-integration.html) for some additional features like command autocomplete using `cmd + ;` and command history using `cmd + shift + ;`. Spend some time going through all of the preferences and customizing them.

One cool feature I'll mention here is the "Status Bar" that can be docked on the top or bottom of your terminal and customized to show things like CPU / memory usage, git status, search/replace text-box, etc. Many will consider it useless clutter. But how else are you going to show off your elite hacker skills?

Go to **_Preferences > Profiles > Session > Status bar enabled/Configure Status Bar_** all the way at the bottom. To change where it's docked go to **_Preferences > Appearance > Status bar location_**.

iTerm2 is not required for any of the below items to work. You can continue to use the built-in Mac terminal if you want.

## Oh My Zsh

I've been using [Oh My Zsh](https://ohmyz.sh/) with the default theme and no customizations for a long time. It's pretty good like that but has many [themes](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes) and [plugins](https://github.com/ohmyzsh/ohmyzsh/wiki/Plugins) to choose from. Install it by running one of the below commands in your terminal and following the on-screen prompts.

```shell
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

OR

sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```

### Install command line auto-suggestions and syntax highlighting

```shell
git clone --depth=1 https://github.com/zsh-users/zsh-autosuggestions.git $ZSH_CUSTOM/plugins/zsh-autosuggestions

git clone --depth=1 https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
```

Add them to the plugins array in your .zshrc file and source the file.

```shell
# My current plugins in .zshrc
 plugins=(git docker vscode zsh-autosuggestions zsh-syntax-highlighting)

# After writing .zshrc
source ~/.zshrc
```

At this point, it appeared that Oh My Zsh auto-completion and zsh-autosuggestions were fighting with each other. Based on the documentation for [overriding internals](https://github.com/ohmyzsh/ohmyzsh/wiki/Customization#overriding-internals) I just copied **_~/.oh-my-zsh/custom/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh_** to **_~/.oh-my-zsh/custom/lib_** and renamed it to completion.zsh so it would override the default completion behavior in **_~/.oh-my-zsh/lib/completion.zsh_**. This appears to work but there could be a better way.

### Pro-Tip

Oh My Zsh provides a bunch of [default command aliases](https://github.com/ohmyzsh/ohmyzsh/wiki/Cheatsheet) that you should know about and use. You can also easily add more via plugins. It also has built-in `tab` command completion.

For example, you can omit the `cd` command and just type a directory name or `..`, `...`, etc. to change directories.

Instead of `ls -al` just use `l`.

The git plugin is enabled by default. You can use the alias `gst` instead of typing `git status`, `gb` for `git branch`, `gco` for `git checkout`, etc. Run `alias` in the terminal to view all current aliases.

For tab-completion trying typing `cd + space` then tab. You can then tab through the currently available directories.

## Install the Powerlevel10k theme for zsh

You can use any of the Oh My Zsh [included themes](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes) by just changing the value of `ZSH_THEME` in your **_~/.zshrc_** file. There are also many other themes that you can use by cloning them to your **_.oh-my-zsh/custom/themes_** directory.

The [Powerlevel10k theme](https://github.com/romkatv/powerlevel10k) caught my eye so I decided to give it a shot. It has a configuration wizard that makes setup a breeze and it looks great.

I manually installed the recommended font first as outlined in [the documentation](https://github.com/romkatv/powerlevel10k#meslo-nerd-font-patched-for-powerlevel10k). Also, check out [Nerd Fonts](https://github.com/ryanoasis/nerd-fonts) which patches many popular fonts by bundling a large number of glyphs (icons) into them. If you're seeing little question marks inside a box showing up in your terminal after all of this it's because the font you're using is missing those particular glyphs.

Create a shallow clone of the theme to your **_.oh-my-zsh/custom/themes_** directory.

```git
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

Open your **_~/.zshrc_** file and change the theme to Powerlevel10k.

```shell
vim ~/.zshrc

# Edit the ZSH_THEME value
ZSH_THEME="powerlevel10k/powerlevel10k"

# Write and quit Vim
:wq

# Source your new .zshrc config or just restart your shell
source ~/.zshrc
```

At this point, the Powerlevel10k configuration wizard should come up in your terminal. If it didn't try quitting and re-opening your terminal. Go through the configuration wizard and select the options you like best. Once completed the wizard will write some options to your **_~/.zshrc_** file and it will also create a **_~/.p10k.zsh_** file containing the theme configuration. Restart your terminal and you should be good to go.

## Install a theme for Vim

This section is about customizing the Vim text editor and is not related to your Z shell configuration like the previous items. I use VS Code as my primary editor but I'm still in the habit of opening a file with Vim in the terminal if I just need to make a quick change. Many people use Vim as their primary text editor for software development and the sky is the limit on customizations. The steps below will basically just pretty up Vim with some syntax highlighting.

### Install vim-plug and Gruvbox theme

Download **_vim-plug_** to the **_~/.vim/autoload_** directory.

```shell
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

Create or update your **_~/.vimrc_** configuration file and add the below code.

```vim
" Specify a directory for plugins
" Avoid using standard Vim directory names like 'plugin'
call plug#begin('~/.vim/plugged')

" Shorthand notation; fetches https://github.com/morhetz/gruvbox
Plug 'morhetz/gruvbox'

" Initialize plugin system
call plug#end()

autocmd vimenter * ++nested colorscheme gruvbox

" Explicitly set theme background to dark mode
set bg=dark
```

### Noob-Tip

I'm not going to lie, this tripped me up for a few minutes. You need to run Vim commands (prefixed by `:`) from **_within Vim_**. Not in a shell. Specifically, if you try to `source` your updated **_~/.vimrc_** configuration file you need to enter Vim first. Sourcing from your shell command prompt will not work. Or you can just quit and re-open your terminal and the changes will be applied.

```shell
# Open Vim from your shell
vim

# Now you can source your .vimrc configuration file
:source ~/.vimrc
```

### Fix TypeScript syntax highlighting

I just so happened to be looking at a couple of TypeScript files to test that the new theme was working. I noticed on a "longer" file (177 lines omg!) that it would only format a small section and then I got an error: `'redrawtime' exceeded, syntax highlighting disabled`. Thanks to [this post](https://jameschambers.co.uk/vim-typescript-slow) from James Chambers this issue is quick and easy to resolve.

```shell
" Tell Vim to use the new regular expression engine in your .vimrc file.
set re=0
```

### Add line numbers

Add the following line to your **_~/.vimrc_** file to enable absolute line numbers in Vim. Check out [this post](https://jeffkreeftmeijer.com/vim-number/) to read a bit about line number options in Vim.

```shell
set number
```

## Final .vimrc file

```vim
" Specify a directory for plugins
" Avoid using standard Vim directory names like 'plugin'
call plug#begin('~/.vim/plugged')
" Shorthand notation; fetches https://github.com/morhetz/gruvbox
Plug 'morhetz/gruvbox'
" Initialize plugin system
call plug#end()

" Ensures plugins are loaded before using gruvbox
autocmd vimenter * ++nested colorscheme gruvbox

" Set gruvbox to dark theme
set bg=dark

" Enable absolute line numbers
set number

" Specify the new regular expression engine for performance
set re=0
```

## Conclusion

You now have a beautiful terminal shell environment with command suggestions / completion and a bunch of useful shorthand command aliases. And when you hop into Vim you'll have syntax highlighting with line numbers. Happy coding!

## Sources

[iTerm2 Shell Integration](https://iterm2.com/documentation-shell-integration.html)
[Oh My Zsh](https://ohmyz.sh/)
[zsh-users](https://github.com/zsh-users)
[Powerlevel10k Zsh Theme](https://github.com/romkatv/powerlevel10k)
[vim-plug](https://github.com/junegunn/vim-plug)
[Gruvbox Vim Theme](https://github.com/morhetz/gruvbox)
[How to fix slow Typescript syntax highlighting in Vim](https://jameschambers.co.uk/vim-typescript-slow)
[Vim’s absolute, relative and hybrid line numbers](https://jeffkreeftmeijer.com/vim-number/)
