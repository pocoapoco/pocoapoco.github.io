---
title: Things I learned from cpp std
date: 2016-01-05 22:37
---

## Remove duplicates from sorted array in place

e.g. 1 1 2 2 2 3 3 4
关键在于覆盖重复元素时，不要对后面尚未访问的元素进行不必要的移动。
使用两个指针：result用于标记当前正在查重的目标元素，first用于向后移动访问新元素。

loop invariant:
result左边一定不存在任何重复的元素；
first右边所有的元素一定未被访问；
result和first中间所有的元素一定是重复和多余的。

```
template<class ForwardIt>
ForwardIt unique(ForwardIt first, ForwardIt last)
{
    if (first == last)
        return last;
 
    ForwardIt result = first;
    while (++first != last) {
        if (!(*result == *first) && ++result != first) {
            *result = std::move(*first); [^1]
        }
    }
    return ++result;
}
```

[^1]: „std::move是用于减少copy的verbosity或runtime overhead的一种可称之为destructive read的操作——将资源从地址A“倾倒”进地址B。[Move semantics][1]“

[1]: http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2006/n2027.html#Move_Semantics
