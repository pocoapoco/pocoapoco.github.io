---
title: Inverse index mapping
date: 2016-09-11 15:40
---

今天看到一道很有趣的题目：

'''

给定 String[] s, int[] n, n[i]的含义为：s中第n[i]个string应该在位置i上。求被n map 过的s。要求O(n) time, O(1) space。

e.g.

s:     cat  dog  rat  bear

n:      2    0    3    1

idx:    0    1    2    3

result:  rat  cat  bear  dog

'''

关键在于如何实现O(1) space。

如果是要把i位置的string map到n[i]上，就简单了：由于n中的expected position是和s中的content associate在一起的，所以每次把i对应的n[i]和s[i]一起交换到正确的位置，直到每个i都等于n[i]就可以了。

而这道题是要把当前在n[i]的元素inversely map回i的位置。

如果可以把n的index和value调换一下就很方便了。然而并不知道有什么方便的O(1) space的方法可以做到这个。

说白了我们想要做的其实就是把n[i]对应的字符串s[j]放到i这里来，然而i上原来的字符串s[i]怎么办呢？有三种方法：

1. 跟s[j]交换。

2. 把s[i]和与它associate的index i cache起来。

显然选2。然后每次访问新的n[i]时，先检查一下这个n[i]是否等于cache里的index。如果等于，就把当前的s[i]及其对应的i跟cache里的交换一下。

这样一来又有个问题：如果n[i]不等于cache里存的index，那可以直接用j = n[i]上的s[j]覆盖吗？会不会把s[i]给丢了？我觉得是有可能丢的。

所以对n[i]不应该顺序访问，而应该这样：j = n[i], 当s[j]覆盖了s[i]，s[i]和i被存入cache后，下一个应该跳转到j = n[i]，因为我们知道这里的元素已经在别处存下来了，它一定需要被别人覆盖。

但是问题又来了：这样如何保证所有的n[i]都被访问过呢？（目前还没想出什么好办法。。）

实在不行我就把s里的字符串map成base 26 integer存进n，再从n map回字符串。。你不是让我O(1) space嘛。。
