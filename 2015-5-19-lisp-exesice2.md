title: ANSI-Common-Lisp-exercises:Chapter2
date: 2015-05-19 10:32:29
tags: Lisp
categories: code
---
因为最近在学习lisp，使用的教程是网上的[《ANSI Common Lisp 中文版》](http://acl.readthedocs.org/en/latest/index.html)，对应每张后面都会有一些习题，我在学完每章后，都会把自己的答案粘上来，希望能和大家一起讨论学习。
![](http://img3.douban.com/lpic/s10385963.jpg)
###ANSI Common Lisp exercises: Chapter 2###
1.这一题比较简单，具体的答案是：
（a）14 。
（b）（1 5），因为这里是作为列表打印出来的，所以（+ 2 3）是运算后的结果。
（c）这里是一个判断语句，（listp 1）判断1是不是列表，因为1不是列表，所以把后一个打印出来，也就是7。
（d）这里先来看list中的第一个，它是一个与运算，和上题一样，listp判断3是不是列表，答案当然不是，所以and运算结果为NIL。第二个（+ 1 2）结果为3。所以最后的答案为（NIL 3）。

2.这里首先来看，cons可以用来构造列表，要表示（a b c）的话，可以有很多种玩法。但是要注意cons只能接受两个参数，比如我刚才想`>（cons 'a 'b 'c）`是不行的。我想到的有这三个：
```
>(cons 'a '(b c))
>(cons 'a (cons 'b (cons 'c nil)))
>(cons 'a (list 'b 'c))
```
这里还要注意一个问题，cons是将第一个参数加上第二个参数，所以两个参数的位置不能弄反，否则，会输不出想要的结果。

3.需要返回第四个元素，而car运算返回第一个，cdr返回其余的。我的答案是：
```
(defun get-four (lst)
   (car (cdr (cdr (cdr lst)))))
```
这里如果给的参数中，不够四个，则返回NIL。

4.我的答案如下：
```
(defun bigger (a b)
   (if (> a b)
     a b))
```

5.（a）题目：
```
(defun enigma (x)
      (and (not (null x))
           (or (null (car x))
               (enigma (cdr x)))))
```
首先观察一下，这是个递归的函数。先看and的第一个参数，（null x）当x为nil时，返回真，not取反，所以总的来说就是不空就返回真。and的第二个参数，是一个或运算，（null (car x)）的意思是当列表x的第一个数为nil时，返回真，然后是一个递归调用，此时调用时的列表去掉头元素。
这个逻辑比较绕，可以这么看，只要x不为nil，与运算取得正值的第一条件符合，但是第二个条件中包含一个递归，每一次递归列表都会减少一个，如果先不考虑或运算，那么第二个条件是恒为nil的，也就是说最终结果为nil。但是或运算的存在使得我们可以跳出这个怪圈。只要列表的第一个元素为nil，那么或运算就可以取得T。从而使整体为T。
这个函数是判断列表中是否存在nil元素，但又不是只有一个nil元素而已。

（b）题目：
```
(defun mystery (x y)
      (if (null y)
          nil
          (if (eql (car y) x)
              0
              (let ((z (mystery x (cdr y))))
                (and z (+ z 1))))))
```
这个比上一个要简单不少，从if (eql (car y) x)就可以看出，只是一个列表元素查找比较的这么一个运算，and z (+ z 1)返回后一个，也就是说，判断x的元素在y中存在，则返回位置，位置从0开始。如果x包含超过一个元素，或者x在y中不存在，则返回nil。

6.（a）这个比较简单，使用car就可以。
  （b）这个有点意思，因为很明显（/ 1 0）是除零操作是要报错的，只有使用or的时候当第一个元素为T，就没有必要去判断第二个元素了。
  （c）这里调用函数的话有两种选择，apply和funcall，apply最后一个实参当成列表，就可以把nil省去了。
  
7.我的解答如下：
```
(defun own-list (lst)
   (if (null lst)
       nil
       (if (listp (car lst))
            T
            (is-list (cdr lst)))))
```
8.（a）
这道题没看懂问的啥，什么叫数字数量的点，懂的小伙伴求教。。
（b）
递归：
```
(defun count-time (a lst)
   (if (null lst)
       0
       (if (eql a (car lst))
           (+ 1 (count-time a (cdr lst)))
           (count-time a (cdr lst)))))
```
迭代：
```
(defun count-time (x lst)
   (let ((z 0))
       (dolist (obj lst)
           (if (eql obj x)
               (setf z (+ z 1))))
       z))
```
