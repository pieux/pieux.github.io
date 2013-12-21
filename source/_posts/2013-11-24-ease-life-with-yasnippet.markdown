---
layout: post
title: "Ease Life with YASnippet"
date: 2013-11-24 01:44
comments: true
categories: [Emacs]
---

安装使用`package-install`，实际上可以写一个简易方法避免重复劳动：

```
(defun require-package (package)
  "Install given PACKAGE."
  (unless (package-installed-p package)
    (unless (assoc package package-archive-contents)
      (package-refresh-contents))
    (package-install package)))

```

学习下其中的1个function

`assoc` is a built-in function in `C source code'.

```
(assoc KEY LIST)
```

Return non-nil if KEY is `equal' to the car of an element of LIST.
The value is actually the first element of LIST whose car equals KEY.

开始学习如何使用YASnippet。

**1 组织你的 Snippets** (*Organizing Snippets*)

Snippet的定义是储存在文件系统的文件中。YASnippet的触发机制会找到这些snippet tables，然后展开。

首先，新建一个目录：my-snippets，用来存放自定义的snippets。

```
(require 'yasnippet)

(setq yas-snippet-dirs (concat user-emacs-directory "my-snippets"))

(add-hook 'prog-mode-hook 'yas-minor-mode)
```

看下是怎么组织snippets的（这是YASnippet自带的）：

`.yas.parent`文件可以共享其他目录下的snippets。

如果有一个空的`.yas-make-groups`文件，会在 YASnippet Menu按照目录结构组织地更清晰。


**2 展开你的 Snippets** (*Traiggering expansion*)

很多中方式能展开snippets，包括：

- By typing an abbrev, the snippet trigger key, and then pressing the key defined in yas/trigger-key (which defaults to "TAB"). This works in buffers where the minor mode yas/minor-mode is active;

- By invoking the command yas/insert-snippet (either by typing M-x yas/insert-snippet or its keybinding). This does not require yas/minor-mode to be active.

- By using hippie-expand

以上是3中最常见的方法（虽然我不知道什么是 hippie-expand）。


