---
title: Twitter Design
date: 2016-06-26 22:02
---

leetcode新出了一道很有趣的题355.Design Twitter。这个简易版的twitter需要支持如下几个功能：

1. postTweet(userId, tweetId): Compose a new tweet.
2. getNewsFeed(userId): Retrieve the 10 most recent tweet ids in the user's news feed. Each item in the news feed must be posted by users who the user followed or by the user herself. Tweets must be ordered from most recent to least recent.
3. follow(followerId, followeeId): Follower follows a followee.
4. unfollow(followerId, followeeId): Follower unfollows a followee.

一个最直观的做法是：对每个用户维护一个听众集合, 其post只对听众定向发布； 每个听众拥有一个list of news，每当follow的人里有新鲜事发布，就自动append到自己的新鲜事列表尾部。对某用户调用getNewsFeed时，就从他的新鲜事列表尾部复制10个出来。这种情境相当于每个包在发布时就保证其即刻抵达目标地址，并在目标处保存一份副本，因此post这个操作的时间开销比较大；此外每条post会被复制很多次，空间开销也比较大。好处是getNewsFeed这个操作很快。

然而这道题tricky的点在于，用户a follow／unfollow用户b后，不仅仅是b此后发布的news，而是连同他曾经发布过的所有news 都要按照universal 时间顺序出现／消失在a的新鲜事列表里。假设每个人平均发帖N条，follow X人，每follow一个人将造成worst case O(X*N^2)的时间开销。因此需要用一个priority queue，而不是list，作为存储新鲜事的数据结构，并给每个tweet贴一个timestamp，作为priority 的比较标准。于是，a follow b时，不仅要把a加入b的听众列表，而且要把b自己发过的所有post加入a的priority queue of news。这样一来，又需要给每个用户维护一个自己发过的贴的列表，因为在这种“即时收听”模式下，用户所有的news feed包括自己发的贴都混在一起无法区分。

假设每人平均发帖N，follow X人， 被Y人follow，那么这种方法所需的资源和开销：

需要维护的field：
Map<Integer, Set<Integer>>; // 用户map成用户的followers
Map<Integer, PriorityQueue<Tweet>>; // 用户map成该用户收到的news feed
Map<Integer, List<Integer>>: // 用户map成该用户自己发的贴

postTweet(): O(Y*lg(N*X))
getNewsFeed(): O(lg(N*X))
follow()/unfollow(): O(N*lg(N*X))

这个方法里有明显的愚蠢之处。今天太晚了明天再接着写。