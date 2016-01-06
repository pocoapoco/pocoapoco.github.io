---
title: Things I learned from cpp std
date: 2016-01-05 19:37
---

## Remove duplicates from sorted array in place

e.g. 1 1 2 2 2 3 3 4
关键在于覆盖重复元素时，不要对后面尚未访问的元素进行不必要的移动。
使用两个指针：res用来标记当前正在查重的目标元素，i用来向后移动访问新元素。

loop invariant:
res左边一定不存在任何重复的元素；
i右边所有的元素一定尚未被访问；
res和i中间的元素是已发现待覆盖的重复元素。

```
template<class ForwardIt>
ForwardIt unique(ForwardIt first, ForwardIt last)
{
    if (first == last)
        return last;
 
    ForwardIt result = first;
    while (++first != last) {
        if (!(*result == *first) && ++result != first) {
            *result = std::move(*first);
        }
    }
    return ++result;
}
```

[1]: http://leo.im
[2]: /assets/static/xian.png

[^1]: „Well honestly, anything mala enough will do! (๑•̀ㅂ•́)و“
