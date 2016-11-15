---
layout: post
title: 'VimLeaning: How to create vim colorscheme?'
tags: [vim, GUI, themes]
status: publish
type: post
published: true
meta: Vim, Colorscheme
author: Sandeep Kumar
comments: true
category: vim
date: 2015-01-18
---

Vim is one of the popular choice as text editor for power users. One of the most important aspect to use a text editor is the look and feel. Vim is quite rich in terms of themes, either pre-bundled with vim distribution or added by community users. If you want to use an existing color scheme with vim, the simplest way is to:

```script
  Create `~/.vim/colors` folder if not exists already.
  Add the `.vim` colorscheme file to the colors folder.
  Go to vim/mvim and use `colorscheme scheme_name` to use the corresponding colorscheme.
  To apply the corresponding colorscheme everytime vim/mvim runs, add the `colorscheme scheme_name` to your ~/.vimrc or ~/.vimrc.after or any other config file which loads everytime you runs vim/mvim.
```

A good ColorScheme is the key to work long hours on any editor. Mostly people use one of the existing theme. But when they get more familiar with the structure, insights of vim, they either `customize existing theme` or `create their own`. Here I am going to discuss about how we can create a new vim theme.

First of all we need to decide what we are actually targeting in terms of vim color-scheme i.e. Are we looking for dark/light color palate, Are we planning to support GUI and terminal mode of vim etc. For this particular use blog post I will be concentrating on __GUI based dark color-scheme__.

First, we need to clear any predefined colors or background.

```bash
  highlight clear
  if exists("syntax_on")
    syntax reset
  endif
```

`hi[ghlight] clear` will reset all the highlighting for groups added by user. Uses the current value of 'background' to decide which default color group to use. `syntax reset` gets the colors back to default ones, but doesn't change color for highlight options.

Now, set the global variables. background and name of your theme so that its easier to include & manipulate.

```bash
  set background=dark
  set g:colors_name="my-theme"
```

`set background` will set the theme palate for the us. Setting a global colors_name helps in identifying the themes.

```bash
  function!  GuiFor(group, ...)
    let histring = 'hi ' . a:group . ' '

    if strlen(a:1)
      let histring .= 'guibg=' . a:1 . ' '
    endif

    if strlen(a:2)
      let histring .= 'guifg=' . a:2 . ' '
    endif

    if a:0 >= 3 && strlen(a:3)
      let histring .= 'gui=' . a:3 . ' '
    endif

    execute histring
  endfunction
```

This method will be the corner stone how we are going to add highlight rules for different hilight groups. This method is expecting first argument to be the `highlight group`. Also, the 2nd and 3rd arguments are `guibg` & `guifg` respectively and are mandatory to be pass. The last argument is `gui` which is optional. This method concatenate the arguments with their respective values and evaluate the resultant string with `execute` command.

A sample method call can be something like 

```bash
  GuiFor("Normal","#000000", "#afafaf")
  GuiFor("Constant", "NONE", "#gray80")
```

With this, you will be able to create your own vim colorscheme. Apart from the happy flow mentioned above, we can also have some sort of compatibility matrices to be fulfilled like `based on the version of vim, whether 256 color palate or not, etc`

In case of any further query, put it either in comments or write to me at [konflikt](mailto:skr.ymca@gmail.com)
