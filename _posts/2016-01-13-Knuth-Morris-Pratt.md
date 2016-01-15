---
title: Knuth-Morris-Pratt
date: 2016-01-14 12:58
---

## substring search 问题

问题可以想象为：给定的pattern是一个画着有限个symbol的窗口W。让W从左向右经过一条上面画着无限个symbol的tape T。当W与T相对静止在T的index m时，当前一轮匹配开始：一个局部指针i从W头部开始向后扫描当前窗口内的tape segment；遇到第一个mismatch，该轮匹配即告终，W相对T向右滑动一格，局部指针i退回到W头部准备开始下一轮匹配。如此这般持续进行，直到某个时刻i第一次成功扫完整个W没有一个mismatch，宣告成功，程序结束；或者W尾部移出T边界（如果tape长度有限的话），匹配失败。

如果你不知道你的pattern长啥样（比方说只有匹配成功时它会“叮！”一下你这样），这将是唯一的算法。因为tape上的symbol不可预测且无限长，你不能keep memory of it；每轮局部匹配一旦结束，你完全不知道之前发生过什么。只能盲目地一次一格往后爬，并且每次都退回到窗口的头部重新比起。

然而pattern长什么样你其实是知道的。一个直观的例子：你的pattern="ABBAB"，如果发生了mismatch，你一点儿都不想回头看，希望最好能直接把窗口头部滑动到当前局部指针所在的位置。假设mismatch出现在pattern[4] = "B"，那么0~3对应的tape上的symbol一定是“ABBA”, 你有足够的信心把窗口的头部直接拖到pattern[3] = "A"开始下一轮匹配，而无需look back further。“<b>从当前的mismatch发生在pattern的什么位置推断出下一轮匹配中窗口相对tape最多有必要回退多少格</b>”这个信息，就encode在你的pattern的具体排列方式里；而且这个信息完全独立于你的input string长啥样。基本上，从pattern里compile这个信息就是KMP做的事情。

## KMP search method

辣末。现在的情况是：W的头部位于T上的index m，局部指针扫描到W上的index i时发现了mismatch。

假设我已经有了一个黑盒子F替我计算i -> F(i) = 如果将窗口头部一口气拖到m+i，最多有必要回退多少格（而不总是i-1）without leaving any potential match unchecked。F(i)可分为两种情况：

1. edge case: i = 0。局部指针还没来得及动本轮匹配就fail了，回到全局看下一个。F(i)记为-1;
2. otherwise, mismatch前的全部为match，由此可推断出最多有必要回退的格数F(i)。如果回退格数不为0，意味着之前已匹配好的pattern segment中存在重复的sub-pattern，我们不敢全部跳过，必须回到这个sub-pattern重复的地方再检查一遍。

下一轮匹配开始时：

如果F(i) == -1，如1所述，窗口直接向右挪动一格；

如果F(i) != -1，窗口头部将被拖动到T上的m+i-F(i)处。此时，局部指针没有必要非得从0开始——如2所述，只要回退格数不为0，就意味着需要退回去检查已匹配过的pattern segment中重复的sub-pattern。如上例，"ABBAB"中mismatch发生在pattern[4] = "B"，下一轮匹配开始时，窗口头部从理想位置4退回到3，即重复的sub-pattern开始的地方；而正是因为这是已匹配过的重复sub-pattern，这一次没有必要再从0匹配一遍，局部指针直接跳过刚才退回的长度，即F(i)，开始扫描就可以了。


```
public int strStr(String haystack, String needle) 
{  
        int m = 0;
        int i = 0;
                
        int[] F = new int[needle.length()];
        constructTable(needle, F);
        
        while (m+i < haystack.length())
        {
            if (needle.charAt(i) == haystack.charAt(m+i))
            {
                if (i == needle.length()-1) return m;
                else                        ++i;
            }
            else
            {
                if (F[i] != -1)
                {
                    m = m + i - F[i];
                    i = F[i];
                }
                else
                {
                    ++m;
                    i = 0;
                }
            }
        }
               
        return -1;
    }
    
```

## KMP table construction


    









