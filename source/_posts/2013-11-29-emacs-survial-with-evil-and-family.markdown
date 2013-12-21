---
layout: post
title: "Emacs Survial with Evil &amp; Family"
date: 2013-11-29 23:33
comments: true
categories: [Emacs]
---

# 1 概览

## 1.1 安装 evil

从网上找到的一个简易的方法，easy to be understood:


```
(defun require-package (package)
  "Install given PACKAGE."
  (unless (package-installed-p package)
    (unless (assoc package package-archive-contents)
      (package-refresh-contents))
    (package-install package)))
```

```
(require-package 'evil)
(require 'evil)
(evil-mode 1)
```

## 1.2 modes and states

光标上用点颜色，看起来舒服点。红色是退回Emacs（使用`C-z`，再按一次`C-z`回到evil），

```
(setq evil-emacs-state-cursor '("red" box))
(setq evil-normal-state-cursor '("green" box))
(setq evil-insert-state-cursor '("orange" bar))
```

> 这里有个terminology的区别：vim中的*mode*指的是Normal, Insert, Visual等，vim是模式编辑器。但是在Emacs中，*mode*是指对特定文本定义的一组快捷键。所以，evil把vim中的*mode*称作*state*。

# 2 设置

通过一堆variable设置evil，可以通过 `M-x customize-group RET evil RET` 查看当前的设置。

`setq`设置全局变量，`setq-default`设置buffer-local的变量，而且要在evil加载前修改[^1]。

[^1]: Strictly speaking, the order only matters if the variable affects the way Evil is loaded. This is the case with some of the ‘evil-want-’ variables.

```
(setq evil-shift-width 8) 
;; Load Evil
(require ’evil) . . .
```

下面列出的variable，基本上默认值和vim的行为类似，但是*evil-want-C-u-scroll*默认不是t。

*evil-auto-indent* [Variable]
> t(default), nil，类似vim中的autoindent。

*evil-shift-width* [Variable]
> The number of columns a line is shifted by the commands > and <.

*evil-repeat-move-cursor* [Variable]
> 如果t(default)，使用`.`重复时光标改变位置。

*evil-find-skip-newlines* [Variable]
> 如果t，那么*f*，*F*，*t*，*T*会查找到其他行。nil(default)。

*evil-move-cursor-back* [Variable]
> t(default)，和vim的行为类似，退出insert state时，光标前移一格。

*evil-want-fine-undo* [Variable]
> If t, then a change-based action like cw may be undone in several steps. If nil (the default), then it is undone in one step.

*evil-regexp-search* [Variable]
> t(default)，`/`，`?`使用正则表达式

*evil-search-wrap* [Variable]
> t(default)，`/`，`?`搜索是到底后从头再搜索。

*evil-flash-delay* [Variable]
> The number of seconds to flash search matches when pressing n and N.

*evil-want-C-i-jump* [Variable]
> If t (the default), then C-i jumps forwards in the jump list. If nil, then C-i inserts a tab.

*evil-want-C-u-scroll* [Variable]
> If t, then C-u scrolls the buffer. If nil (the default), then C-u begins a numeric prefix argument.

## 2.1 The cursor

这个之前也已经涉及过，现在全面了解下：


```
(setq evil-emacs-state-cursor '("red" box))
(setq evil-normal-state-cursor '("green" box))
(setq evil-insert-state-cursor '("orange" bar))
```

一共这么多：

```
evil-default-cursor
	The default cursor.
evil-normal-state-cursor
	The cursor for Normal state.
evil-insert-state-cursor
	The cursor for Insert state.
evil-visual-state-cursor
	The cursor for Visual state.
evil-replace-state-cursor
	The cursor for Replace state.
evil-operator-state-cursor
	The cursor for Operator-Pending state.
evil-motion-state-cursor
	The cursor for Motion state.
evil-emacs-state-cursor
	The cursor for Emacs state.
```

## 2.2 The initial state

默认是进入 Normal State。我也就不修改了。

# 3 Keymaps
Evil 的键映射存储在多个keymaps中，每个 state 有一个全局的 keymap，比如对应于Normal State 的`evil-normal-state-map`。
通过 Emacs 的 `define-key` 来修改。

```
;; bind key "w" to command foo
(define-key evil-normal-state-map "w" ’foo)
```

`evil-maps.el` 包含所有的键绑定。

```
evil-normal-state-map
	The global keymap for Normal state.
evil-insert-state-map
	The global keymap for Insert state.
evil-visual-state-map
	The global keymap for Visual state.
evil-replace-state-map
	The global keymap for Replace state.
evil-operator-state-map
	The global keymap for Operator-Pending state.
evil-motion-state-map
	The global keymap for Motion state.
```

每个 state 还有一个 buffer-local 的 keymap。也就是特定于该 buffer，优先于 global keymap。这些可以通过 mode hook 修改。

```
evil-normal-state-local-map
	Buffer-local keymap for Normal state.
evil-insert-state-local-map
	Buffer-local keymap for Insert state.
evil-visual-state-local-map
	Buffer-local keymap for Visual state.
evil-replace-state-local-map
	Buffer-local keymap for Replace state.
evil-operator-state-local-map
	Buffer-local keymap for Operator-Pending state.
evil-motion-state-local-map
	Buffer-local keymap for Motion state.
```

## 3.1 ‘evil-define-key’
Evil 提供的方法，可以往 Emacs 的 keymap 中添加特定 state 的键绑定。比如:

定义了一个 minor mode，叫做 foo-mode。然后往该 mode 的 normal state 下修改键绑定。

```
(define-minor-mode foo-mode
       "Foo mode."
       :keymap (make-sparse-keymap))
     (evil-define-key ’normal foo-mode-map "w" ’bar)
     (evil-define-key ’normal foo-mode-map "e" ’baz)
```

然后用 hook，添加到 text-mode-hook 里。

```
(add-hook ’text-mode-hook ’foo-mode) ; enable alongside text-mode
```

## Appendix 1

什么是 vim 的 magic？

主要涉及到正则表达式。先留几个参考资料：

1. [vimdoc](http://vimdoc.sourceforge.net/htmldoc/pattern.html#/magic)
2. [vim.vikia](http://vim.wikia.com/wiki/Simplifying_regular_expressions_using_magic_and_no-magic)


**To Be Continued**
