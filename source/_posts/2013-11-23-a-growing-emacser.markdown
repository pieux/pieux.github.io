---
layout: post
title: "A Growing Emacser"
date: 2013-11-23 16:00
comments: true
categories: [Emacs]
---

---

`when:` when is a lisp macro

```
(when COND BODY...)
```
If COND yields non-nil, do BODY, else return nil.
When COND yields non-nil, eval BODY forms sequentially and return
value of last one, or nil if there are none.

---

`fboundp` is a built-in function in `C source code'.

```
(fboundp SYMBOL)
```

---

`add-to-list` is a compiled Lisp function in `subr.el'.

```
(add-to-list LIST-VAR ELEMENT &optional APPEND COMPARE-FN)
```

Add ELEMENT to the value of `LIST-VAR` if it isn't there yet.
The test for presence of `ELEMENT` is done with `equal`,
or with `COMPARE-FN` if that's non-nil.
If `ELEMENT` is added, it is added at the beginning of the list,
unless the optional argument `APPEND` is non-nil, in which case
ELEMENT is added at the end.

The return value is the new value of LIST-VAR.

---

`provide` is a built-in function in `C source code'.

```
(provide FEATURE &optional SUBFEATURES)
```

Announce that FEATURE is a feature of the current Emacs.
The optional argument SUBFEATURES should be a list of symbols listing
particular subfeatures supported in this version of FEATURE.

---

`C-x h` runs the command mark-whole-buffer

`C-w` runs the command kill region

---

use `C-x <right>`, `C-x <left>` to cycle around in the buffer ring.

`iswitchb-mode` replaces the default C-x b behaviour with a very intuitive buffer-switching-with-completion system.

The related commands are `C-x b`, `C-s` and `C-r`.

---

Dired Mode 非常别扭，很多无用的快捷键。

emacs `dired` (Directory Editor)mode.

`C-x d`: enter dired mode(use `M-x RET dired RET` is quick too)

`C-x 4 d`: `dired-other-window`

`C-x C-j`: (dired-jump to current file)

`q`: quit

`R` to rename the file (or 'dired-do-rename').

`C-x k RET` to go back to the (renamed) buffer

---
