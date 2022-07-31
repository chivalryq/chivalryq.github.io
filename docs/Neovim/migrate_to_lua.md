---
title: "Neovim: 从VimScript迁移到Lua"
date: 2022-02-15T22:01:32+08:00
description: '我如何把Neovim的init.vim迁移到Lua'
image: img/cctv.jpeg
---

在迁移到neovim之后，我更喜欢模块化更好的Lua作为nvim的配置语言。虽然vimL我还没完全理解，例如函数（因为他奇怪的标志符和语法，还有我的懒）OK这虽然并不重要，但是在neovim势头正劲的情况下，生态的差距似乎正在拉开。我觉得及早转到neovim可能会更好融入这种生态。因为Lua是neovim生态的一等公民。

如何完整的迁移我仍在学习当中，但是这不妨碍我利用我已经学到的几点，在不牺牲我当前配置使用的情况下，我要把配置逐步取代为Lua。vim是个养成游戏，哎，就得这么玩。

所以这会是一个四不像的配置）

## 入门 -- 迁移Options

没必要上来就把Lua怎么用看一遍，学过Python，会使用其他语言，直到什么叫脚本语言，完全可以随用随查。

我们先把options转换为Lua，相信Options也是当初学习配置Vim的第一步。 首先把创建一个`init.lua`。在里面对照你原来的Options写配置：

类似set xxx=yyy的配置，会被转换为vim.o.xxx
```vimL
set expandtab=false
```

将会被转换为
```Lua
vim.bo.expandtab=false
```

注意，这里可能是以下三种之一，具体取决于你的Option是什么
- `vim.o.xxx`（全局Option)
- `vim.wo.xxx`（window Option)
- `vim.bo.xxx`（Buffer Option)

如何得知呢？键入`:help xxx` 去看一下，这是一个例子

![:help expandtab](/img/show_expandtab.jpg)

大多数Options都提到这个是什么类型，有的没有提到不过也不必慌，一会你`source init.lua`的时候，有错误他会提示你的。

值得注意的是，这里有一个小问题，数字，布尔这种类型可以直接转换过去，如果是类似encoding=UTF-8这种选项，记得把UTF-8加引号，like

```
vim.o.encoding='UTF-8'
```

做到这里也许已经挺累了，记得使用宏帮助完成这些重复工作

## 更深一步 -- mapping

mapping更容易迁移，这是一个简单的例子，`vim.api.nvim_set_keymap` 是一个neovim暴露的接口，由于函数是Lua里的一等公民，可以引入到一个变量中后面使用。参数分别是：
1. 模式
2. 映射前的键
3. 映射后的键
4. 选项
其中选项我几乎总是设置noremap，silent也可以设置，为了简单起见我们先使用同一种选项。

```Lua
local keymap = vim.api.nvim_set_keymap
local opts ={noremap= true,silent=true}
keymap('n','H' ,'^',opts)
keymap('n','L', '$',opts)

```

BTW这里也可以使用宏来帮助快速完成这些迁移。

## 使用packer.nvim来管理插件（可选）

老实说，这是我更改的，或者今晚刚刚采用的插件，Vim-plug很好，资历很老而且几乎没有什么问题，Pakcer相对来说是个新家伙，只不过他可以提供插件懒加载，这对以后是有好处的。

这部分完全选做。

```lua
-- 这部分帮助你在自动下载所需的packers.nvim文件
local fn = vim.fn
local install_path = fn.stdpath('data')..'/site/pack/packer/start/packer.nvim'
if fn.empty(fn.glob(install_path)) > 0 then
  packer_bootstrap = fn.system({'git', 'clone', '--depth', '1', 'https://github.com/wbthomason/packer.nvim', install_path})
end

require('packer').startup(function(use)
  -- 有意思的是，packer可以用自己管理自己。
  use 'wbthomason/packer.nvim'

  use 'tpope/vim-sensible'
  -- your plugins here

  if packer_bootstrap then
    require('packer').sync()
  end
end)
```

使用`:PackerSync`更新插件

## 妥协的部分

好了，说起来是容易的，真正调试完却并不容易。也许你已经想去喝杯咖啡了。然而望着你的四五个（甚至十几个二十几个）配置文件，你想：“我总不能先用一个残缺版的nvim吧，我已经做了那么多自定义了"。显然，一晚上是不够把所有的配置全部迁移过来的，另外还有一些配置只能用vimL，neovim内嵌lua还没有开放接口的，例如autocmd（PR还在review中）

那么接下来我们总要做一点妥协，让一部分继续以vimL的方式存在，也许此时你的init.lua中还有一部分没有修改。没关系，有一个接口帮助我们先延缓这些修改

```lua
vim.cmd()
```

这个函数接受字符串，用来执行vimL，把来不及修改的地方，先用这个函数包裹。就可以先去喝一杯咖啡并且使用你已经配置好的nvim了。下面是一个例子。两个中括号`[[]]`是Lua跨行字符串的语法（类似Python中的"""）

```lua
vim.cmd([[
autocmd FileType go let b:go_fmt_options = {
\ 'goimports': '-local ' .
  \ trim(system('{cd '. shellescape(expand('%:h')) .' && go list -m;}')),
\ }
]])
```

另外我还用这一段代码引入其他文件夹中的配置，如果你也只有一点时间，在引入Lua模块概念之前，可以这样粘合多个文件中配置。
```Lua
vim.cmd([[
for f in split(glob('~/.config/nvim/config/*.vim'), '\n')
    exe 'source' f
endfor
]])
```
