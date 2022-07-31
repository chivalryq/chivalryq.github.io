---
title: "Coc_to_lsp"
date: 2022-02-20T17:20:21+08:00
description: '我为什么从Coc.nvim迁移到neovim lsp'
image: images/cctv.jpeg
draft: true
---

在配置neovim过程中，我首先接触了coc.nvim插件体系。为了代码自动补全、代码段补全等功能。现在还在用coc-preview。coc有一个很棒的体系，但是我最近在查看neovim LSP相关的插件，并且决定迁移到neovim LSP，这篇文章介绍我为什么要这样做，还有我操作的过程。

## 原因

在reddit有一个[帖子](https://www.reddit.com/r/neovim/comments/rr6npy/question_coc_vs_lsp_whats_exactly_the_difference/)，讨论了coc和LSP的区别。太长不看：

1. Coc是和built-in LSP都是为了同一个目的：通过LSP协议（vscode用的就是这个）来完成对内容的补全等操作。
2. Coc.nvim是一个用Typescript构建的插件体系（例如coc-xxx都是coc的插件）,并且在LSP协议之外，还有很多好用的插件，例如coc-preview，一个类似NerdTree的插件）。因为没有对vim有什么要求，所以在neovim和vim上都可以运行。
3. 在neovim 0.5发布之后，内置了一个LSP client。导致出现了围绕其构建相关插件的可能性。
4. neovim内置的LSP client性能更好，neovim内置的LuaJIT是一个很快的Lua解析器。没有外部依赖（coc需要依赖node）

我个人而言，已经在Coc体系上做了一定配置。但是下面几个原因让我选择围绕neovim LSP进行我的补全配置。
1. neovim是一个活跃的社区，LSP作为native的实现方式，肯定受到更多人的欢迎，在这个基础上涌现更多优秀插件。换句话说，一个更光明的未来。
2. 性能更好。
3. 我不喜欢插件套插件的拓扑方式，这并不美观。在文件上会凭空多出一个coc-settings.json（这是coc对自己插件的一个配置方式）

> 参考：nvim-lspconfig项目wiki对二者的比较：https://github.com/neovim/nvim-lspconfig/wiki/Comparison-to-other-LSP-ecosystems-(CoC,-vim-lsp,-etc.)

## 步骤

1. 移除coc相关配置。
2. 







