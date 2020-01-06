---
title: Linear Algebra
date: 2020-01-05 00:45
---

2019年12月学习了一下mit网红教授gilbert strang给数学系本科生讲的18.06。果然轻松愉快，名不虚传。相比之下我本科时那种一上来先塞行列式的教法应该可以直接烧掉吧。据说在线性代数还不成气候的古时候，行列式was the big thing；后来随着数学的发展行列式反而变成线性代数里的一个小小的、当然也还是很重要的部分了。既然知识体系进步了那么知识传播的方法也需要进步吧，否则迟早会有一天任何人类个体终其一生也来不及掌握任何领域已经积累的知识总和了。当然也有可能未来人类的学习方法就是打一针啥都会了；然后掌握打针技术的实体成为了邪恶的行星统治者什么的。真是令人不寒而栗的黑暗世界啊，还是活在21世纪比较幸福

这门课我比较喜欢的是视频和quiz，另外用粉笔写板书不知道为什么看起来特别让人愉快。不太喜欢的是strang写的教材那种比较意识流的风格。所以更好的服用方法大概是要配合一本更成体系一点的数学书吧。anyhoo～这里来写一下这门课的笔记：

## 前1/3: Ax = b in steady state problems

### Chapter 1 - 2: row form & column form, 一些花式matrix factorization

row form: components of Ax are inner products of rows of A. （R^n内多条直线的交点

column form: Ax is a combination of columns of A. （向量加法

### Chapter 3: vector space, subspace, Ax = 0, Ax = b, linear independence

For mxn matrix A: N(A) is the orthogonal complement of row space C(A<sup>T</sup>) (in R<sup>n</sup>), N(A<sup>T</sup>) is the orthogonal complement of the column space C(A) (in R<sup>m</sup>)

reduced row echelon form: quiz里有一道很好的概念题-- forward elimination changes Ax = b to a row reduced Rx = d, the complete solution is x = [4, 0, 0]<sup>T</sup> + c<sub>1</sub>[2, 1, 0]<sup>T</sup> + c<sub>2</sub>[5, 0, 1]<sup>T</sup>. 问：1）what is the 3x3 reduced row echelon matrix R and what is d? 2) if the process of elimination subtracted 3 times row 1 from row 2 and then 5 times row 1 from row 3, what matrix connects R and d to the original A and b? Use this matrix to find A and b.

### Chapter 4: 最小二乘法, gram-schmidt

A<sup>T</sup>A**x* = A<sup>T</sup>b where A<sup>T</sup>A is invertible iff A has linearly independent columns.

If Q is square then transpose = inverse.

A = QR

### Chapter 5 ：行列式, cramer's rule

cramer's rule is good for algebra but terrible for computation

## 中间1/3: Ax = &lambda;x for dynamic problems

### Chapter 6：eigenvalues and eigenvectors

det(A - &lambda;I) = 0

invertibility vs. diagonalizability: invertible if eigenvalue != 0; diagonalizable if we have n independent eigenvectors

矩阵乘法 using eigenvalue matrix and eigenvector matrix: Fibonacci numbers (turn a second order problem into a first order problem by letting u<sub>k</sub> = [F<sub>k+1</sub>, F<sub>k</sub>]<sup>T</sup>, then u<sub>k+1</sub> = Au<sub>k</sub> 啊写不动了明天再接着写







