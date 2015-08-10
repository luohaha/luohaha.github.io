title: ANSI-Common-Lisp-exercises:Chapter3
date: 2015-05-21 23:15:18
tags: Lisp
categories: code
---
因为最近在学习lisp，使用的教程是网上的[《ANSI Common Lisp 中文版》](http://acl.readthedocs.org/en/latest/index.html)，对应每张后面都会有一些习题，我在学完每章后，都会把自己的答案粘上来，希望能和大家一起讨论学习。
![](http://img3.douban.com/lpic/s10385963.jpg)
###ANSI Common Lisp exercises: Chapter 3###
*因为前4题都比较简单，这里就从第五题开始吧*

5.递归：
```
(defun pos+ (lst)
   (doloop lst 0))

(defun doloop (lst pos)
   (if lst
       (cons (+ pos (car lst)) (doloop (cdr lst) (+ 1 pos)))))
```
迭代：
```
(defun pos+ (lst)
   (let ((flag 0))
        (let ((tmp (list (+ 1 (car lst)))))
             (let ((tmplist (cdr lst)))
                  (dolist (obj tmplist)
                          (setf tmp (append tmp (list (+ obj (+ 1 flag)))))))
             tmp)))
```
使用mapcar：
```
(defun pos+ (lst)
   (let ((flag -1))
        (mapcar #'(lambda (x) (+ x (incf flag))) lst)))
```
6.(a)cons的本意就是将左指针指向第一个第一个参数，右指针指向第二个参数。实现如下
```
(defun my-cons (x y)
   (let ((lst '(nil . nil)))
        (setf (car lst) x
              (cdr lst) y)
        lst))
```
将car和cdr反过来就是要求的答案啦。
```
(defun my-cons (x y)
   (let ((lst '(nil . nil)))
        (setf (cdr lst) x
              (car lst) y)
        lst))
```
(b)这个会用到后面的知识点，所以先不实现。
(c)
```
(defun my-length (lst)
   (mloop lst 0))

(defun mloop (lst count)
   (if (null lst)
       count
       (mloop (car lst) (+ 1 count))))
```
(d)
```
(defun my-member (obj lst)
   (if (null lst)
         nil
         (if (equal obj (cdr lst))
             lst
             (my-member obj (car lst)))))
```
7.这里题目需要修改教程中提到的游程编码的代码，如下：
```
(defun compress (x)
  (if (consp x)
      (compr (car x) 1 (cdr x))
      x))

(defun compr (elt n lst)
  (if (null lst)
      (list (n-elts elt n))
      (let ((next (car lst)))
        (if (eql next elt)
            (compr elt (+ n 1) (cdr lst))
            (cons (n-elts elt n)
                  (compr next 1 (cdr lst)))))))

(defun n-elts (elt n)
  (if (> n 1)
      (list n elt)
      elt))
```
使它使用更少的cons核，这里就需要比较list和cons的区别了。list就是由cons核连接起来的，list是以nil结尾的，但是cons是将两个参数分别作为左右指针指向的对象。因此使用cons可以出现提示里说的点状列表，所以使用cons取代list，可以减少cons核。
所以
```
(defun n-elts (elt n)
  (if (> n 1)
      (list n elt)
      elt))
```
改为
```
(defun n-elts (elt n)
  (if (> n 1)
      (cons n elt)
      elt))
```
就可以了。
8.