---
title: Knuth-Morris-Pratt
date: 2016-01-14 12:58
---

## substring search 问题

问题可以想象为：给定的pattern是一个画着有限个symbol的窗口W。让W从左向右经过一条画着无限个symbol的tape T。当W头部停留在T的index m时，当前一轮匹配开始：一个局部指针i从m开始向后扫描当前窗口内的tape segment；遇到第一个mismatch，该轮匹配即告终，W相对T向右滑动一格，局部指针i退回到W头部准备开始下一轮匹配。如此这般持续进行，直到某个时刻i第一次成功扫完整个W没有一个mismatch，宣告成功，程序结束；或者W尾部移出T边界（如果T长度有限的话），匹配失败。

如果你不知道你的pattern长啥样（比方说只有匹配成功时它会“叮！”一下你这样），这将是唯一的算法。因为tape上的symbol不可预测且无限长，你不能keep memory of it；每轮局部匹配一旦结束，你完全不知道之前发生过什么。只能盲目地一次一格往后爬，并且每次都退回到窗口的头部重新比起。

然而pattern长什么样你其实是知道的。一个直观的例子：你的pattern="ABBAB"，如果发生了mismatch，你一点儿都不想回头看，希望最好能直接把窗口头部滑动到当前局部指针所在的位置。假设mismatch出现在pattern[4] = "B"，那么0~3对应的tape上的symbol一定是“ABBA”, 你有足够的信心不用把窗口的头部拖回pattern[0+1] = "B"那么左，但是你的信心只够你把它拖到pattern[3] = "A"，再往右就不再安全了。“<b>从当前的mismatch发生在pattern的什么位置推断出下一轮匹配中窗口相对tape最多有必要回退多少格</b>”这个信息，就encode在你的pattern的具体排列方式里；而且这个信息完全独立于你的input string长啥样。基本上，从pattern里compile这个信息就是KMP做的事情。

## KMP search method

辣末。现在的情况是：W的头部位于T上的index m，局部指针扫描到W上的index i时发现了mismatch。

假设我已经有了一个黑盒子F替我计算i -> F(i) = 如果将窗口头部一口气拖到m+i，最多有必要回退多少格（而不总是i-1）without leaving any potential matches unchecked。F(i)可分为两种情况：

1. edge case: i = 0。局部指针还没来得及动这轮匹配就fail了，回到全局看下一个。F(i)记为-1;
2. otherwise, mismatch前的pattern segment全部match，根据这部分partial match长啥样便可推断出最多有必要回退的格数F(i)——这才是KMP的精髓。在明白这个精髓到底是咋回事之前，现在就让我们暂且相信黑盒子的计算是正确的——如果F(i) = 0，黑盒子告诉你前面没有一个symbol值得回头（Intuitively这只有一个可能：前面的partial match中没有一个重复的symbol）；如果F(i)不为0，说明之前的partial match中存在重复的sub-pattern，最好回到这个sub-pattern重复的地方检查一下。

下一轮匹配开始时：

如果F(i) == -1，如1所述，窗口直接向右挪动一格；

如果F(i) != -1，窗口头部将被拖动到T上的m+i-F(i)处。此时，局部指针没有必要非得从0开始——如2所述，当我们决定退回去一点时，一定是为了检查前面的partial match中重复的sub-pattern。而正因为这重复的sub-pattern已确认匹配过，这一次局部指针没有必要再从0开始匹配一遍，它将直接跳过刚才已确认匹配的长度F(i)，开始下一轮匹配。或者你可以这样想象：mismatch发生后，局部指针卡在tape上保持不动，窗口整体向右滑动；滑着滑着它发现诶，滑进了一个之前已经匹配过的片段，直到被mismatch发生的那个点绊住——这时，下一轮匹配就从这个绊住的点开始。

如上例，"ABBAB"中mismatch发生在pattern[4] = "B",假设对应的tape是"ABBAC"。下一轮匹配开始时，窗口头部从“期望位置”4退1回到3，即重复的sub-pattern"A"开始的地方；局部指针跳过sub-pattern中已确认匹配的长度1，从pattern[0+1]开始。


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

之前的search method中，我们把i -> F(i)看做一个黑盒子。辣末，怎么利用pattern自带的信息创建这个盒子，就是KMP的精髓了！( •̀ᄇ• ́)ﻭ✧

之前提到过，“检查已匹配好的pattern segment中的重复sub-pattern”。基本上，就是这个。

全剧终。( ･ω･)ﾉ

-

KMP快就快在尽量不回头看。如果它回头看，那一定是因为非这么做不可。也就是说，第一个mismatch之前的partial match中存在一个重复的sub-pattern。这个sub-pattern一定是pattern从头部开始的某个长度的片段——称之为prefix。KMP要做的就是在下一轮匹配开始时把窗口的头部拖到这个重复的prefix开始的地方。

prefix里可能还有nested sub-pattern，往回拖多少才能保证安全而又不多余呢？

答案就是：length of <b>longest</b> proper prefix of pattern segment that is also a proper suffix of it, for index 0 ~ i-1.

longest听起来简单嗦？事实上各种情况组合千变万化，试一下就知道，只要稍微复杂一点儿，连肉眼都很难识别。

-

现在这里有一个pattern，用一个指针cnd指向头部的symbol，然后从0开始向后逐个计算mismatch发生在i时，i之前的partial match中的F(i) = longest prefix that is also a suffix，保存在一个table中。

i = 0时，前面已经分析过，F(i) = -1;

i = 1时，只有第0个symbol被匹配，prefix不能自己重复自己(否则与brute force有何异)，F(i) = 0; 

从i = 2开始，因为mismatch发生在i，我们比较i-1(potential suffix的latest candidate)和cnd(potential suffix匹配的prefix对应的target)指向的symbol：

1. 如果二者相等，意味着一个等于prefix的suffix已经找到。此时F(i) = 当前suffix的长度，然后cnd和i一起后移，期待找到更长的suffix；
2. 如果二者不相等，而cnd已不在头部，意味着之前已经找到了一个等于prefix的suffix，而此刻走出了它的边界。这时，如果找到的这个suffix内部不存在重复的nested sub-pattern，那么确认匹配的片段决定了suffix已经没可能等于prefix，cnd可以直接roll back到0了。可是如果suffix内部也存在重复的sub-pattern呢？这就需要roll back到当初创建这个nest在suffix里的sub-pattern的时点，看看退到哪里是安全的。此时cnd = F(cnd);
3. 如果二者不相等且cnd在头部，意味着当前并没有任何potential suffix等于pattern prefix，没必要回头检查。此时F(i) = 0。

```
private void constructTable(String word, int[] F)
{
    F[0] = -1;
    F[1] = 0;
    
    int i = 2;
    int cnd = 0;    
    while (i < word.length())
    {
        if (word.charAt(i-1) == word.charAt(cnd))
            F[i++] = ++cnd;
        else if (cnd > 0)
            cnd = F[cnd];
        else //cnd == 0
            F[i++] = 0;
    }
}
    
```
上述推理中唯一比较绕的是当cnd > 0时，cnd = F[cnd]。如果用deterministic finite automaton的思维方式来看这个算法，就容易理解多了。这个明天再接着写

## KMP in terms of DFA

### DFA and a look-up table

### DFA simulation


    









