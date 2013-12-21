---
layout: post
title: "macro &amp; defmacro in Emacs"
date: 2013-11-23 16:42
comments: true
categories: [Emacs]
---

学习下Lisp的宏（macro）。

首先，区别：Lisp宏和键盘宏（keyboard macro）是两个不同的概念。

一个LISP宏(Lisp Macro)是一个扩展LISP语言的用户定义的结构.
 宏不像函数那样对参数执行表达式. 它们只是在构造一个包括这些参数的表达式.

> 也就是，宏是一个结构，是在构造。就是Lisp非常牛逼的一点：Code as data。

首先看下语法定义：


`defmacro` is a Lisp macro in `byte-run.el'.

```
(defmacro NAME ARGLIST &optional DOCSTRING DECL &rest BODY)
```

Define NAME as a macro.
When the macro is called, as in (NAME ARGS...),
the function (lambda ARGLIST BODY...) is applied to
the list ARGS... as it appears in the expression,
and the result should be a form to be evaluated instead of the original.
DECL is a declaration, optional, of the form (declare DECLS...) where
DECLS is a list of elements of the form (PROP . VALUES).  These are
interpreted according to `macro-declarations-alist'.
The return value is undefined.

好长的解释。

看一个实例：

```
(defmacro inc (var)
  (list 'setq var (list '1+ var)))

```

> 注意一点：`(list '1+ var)` 开始时写成`(list '1+var)`，少写了一个空格。

定义了一个Lisp宏：`inc (var)`。这个macro的BODY是（按照list函数拼出来）:

```
(setq var (1+ var))
```

当然也可以这样展开宏：

```
(macroexpand '(inc x))
```

现在就可以用宏了：

```
(setq x 1)
(inc x)
```


> 解释:

`list` 的用法

```
list is a built-in function in `C source code'.

(list &rest OBJECTS)

Return a newly created list with specified arguments as elements.
Any number of arguments, even zero arguments, are allowed.

```

宏还有一种写法，就是使用反引用（Backquote）。

我们可以用 ' 引用一个对象并不执行.我们还可以用 反引用 ` 引用一个列表, 但是只是选择性的执行列表的元素.

