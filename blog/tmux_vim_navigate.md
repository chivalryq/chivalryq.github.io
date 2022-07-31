---
title: "在vim和tmux之间无缝导航"
date: 2022-03-13T18:26:26+08:00
description: 'Seemless Navigate between tmux and nvim'
image: img/vim-windows.jpg
---

我的工作流中会同时使用Vim（Neovim）和tmux来完成代码的“编写、运行、修改“这个循环，有时会开启多个tmux窗格和vim窗口。这篇文章介绍我最近发现的如何在这二者窗格之间无缝切换。


### Vim

我会在vim中开启多个窗口完成代码编辑工作，例如在另一个窗口中参考API或者是两个有关系的代码块在两个buffer中。我使用`<Leader>h/j/k/l`
来在vim中切换多个窗口。我的`<Leader>`为空格键。

![Windows in Vim](/img/vim-windows.jpg) 

在上图中，我使用`<Leader>j`和`<Leader>k`从上下两个窗口切换。

### tmux

虽然各类浮动或靠边的终端已经可以比较容易的运行在Neovim中，有时仍有其他需求让我需要使用tmux：例如
1. 退出Neovim之后去看一下其他shell的运行结果。
2. 希望分割不同层次的工作。
3. 没有使用Neovim的时候。

我在tmux的配置为：前缀键为`<Ctrl-Space>`，使用vi mode-keys，所以我在不同窗口之间切换的方式是：`<Ctrl-Space>j/k/h/l`

```tmux
set-window-option -g mode-keys vi

# change default prefix
unbind-key C-b
set-option -g prefix C-Space
bind-key C-Space send-prefix
```

### 隔阂

1. 这两者之间的体验不同，有时候我希望切换到某个窗口按了几次键没有作用才发现我混淆了二者。这很容易让我从上述循环中分神。
2. tmux的前缀键需要按到ctrl，如果经常按过去，小拇指保护协会发出强烈抗议。尤其是，试一试ctrl+space这个按键，如果还是大拇指按下space，小拇指则会非常痛苦。
3. 由于我不想使用Ctrl作为修饰键，所以我一直避免在我的Neovim配置中使用Ctrl键，这导致我的Vim命令空间的有很大一个损失，经常找不到还能绑定的键。

### 解决

我找到了一个非常棒的Vim/tmux插件：[vim-tmux-navigator](https://github.com/christoomey/vim-tmux-navigator)，这个仓库既作为一个vim插件，也作为一个tmux插件，我想要的二者之间的无缝切换。

太长不看版：这使得你可以使用同一套窗口导航快捷键，例如我现在使用`<Ctrl-h/j/k/l>`这一套快捷键来导航。具体实现可以参考仓库代码，其实是给导航的快捷键包了一层“检验是否在Vim中以及是否需要跳出Vim”的逻辑。

配置并不复杂：

- 使用你的Vim插件管理器安装该插件（Vim-Plug/Packer/...）
- 使用TPM（Tmux插件管理器）安装该插件
- 使用`<Ctrl-h/j/k/l>` 作为你的Vim窗口导航快捷键。（也许可以设置为其他值，但是这对我而言已经足够好了，我还没有了解更多）

Ohhh，另外第三个缺点怎么办？Ctrl键仍然在非常反人类的位置，我们这么大规模的使用Ctrl，对小拇指可不是个好消息。今天上午我在想这个问题，结合以前的经验，我找到了一个很多人用的修改键盘映射的软件。也许不同平台有不同的这样的软件，我在Mac上使用的是Karabiner-Elements。

把中英文切换键（也就是Capslock键）映射为Ctrl，这估计是很多人的操作了。再使用右Command键作为中英文切换，OK，我还多利用起了一个几乎没有作用的右Command键。

### 效果

我现在并不Care我在使用什么软件，只要是分割窗口，我使用同一套快捷键切过去就可以。这极大减少了我工作流的中断，也许这对你也有用！
