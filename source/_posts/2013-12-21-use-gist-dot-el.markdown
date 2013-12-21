---
layout: post
title: "use gist.el"
date: 2013-12-21 21:05:30 +0800
comments: true
categories: [Emacs]
---

[github repo](https://github.com/defunkt/gist.el)

gist buffer:

```
g : reload the gist list from server
e : edit current gist description
k : delete current gist
+ : add a file to the current gist
- : remove a file from the current gist
```

in-place edition. While viewing a gist file buffer, you can:

```
C-x C-s : save a new version of the gist
C-x C-w : rename some file
```

Functions:

```
gist-list - Lists your gists in a new buffer. Use arrow keys
to browse, RET to open one in the other buffer.

gist-region - Copies Gist URL into the kill ring.
With a prefix argument, makes a private gist.

gist-region-private - Explicitly create a private gist.

gist-buffer - Copies Gist URL into the kill ring.
With a prefix argument, makes a private gist.

gist-buffer-private - Explicitly create a private gist.

gist-region-or-buffer - Post either the current region, or if mark
is not set, the current buffer as a new paste at gist.github.com .
Copies the URL into the kill ring.
With a prefix argument, makes a private paste.

gist-region-or-buffer-private - Explicitly create a gist from the
region or buffer.
```
