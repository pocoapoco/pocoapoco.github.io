---
title: Twitter Design
date: 2016-06-26 22:02
---

leetcode新出了一道很有趣的题355.Design Twitter。这个简易版的twitter需要支持如下几个功能：

1. postTweet(userId, tweetId): Compose a new tweet.
2. getNewsFeed(userId): Retrieve the 10 most recent tweet ids in the user's news feed. Each item in the news feed must be posted by users who the user followed or by the user herself. Tweets must be ordered from most recent to least recent.
3. follow(followerId, followeeId): Follower follows a followee.
4. unfollow(followerId, followeeId): Follower unfollows a followee.

一个最直观的做法是：对每个用户维护一个听众集合, 其post只对听众定向发布； 每个听众拥有一个list of news，每当follow的人里有新鲜事发布，就自动append到自己的新鲜事列表尾部。对某用户调用getNewsFeed时，就从他的新鲜事列表尾部复制10个出来。这种情境相当于每个包在发布时就保证其即刻抵达目标地址，并在目标处保存一个此包的引用，因此post这个操作的时间开销比较大；此外对同一个tweet对象会生成许多不必要的引用。好处是getNewsFeed这个操作很快。

然而这道题tricky的点在于，用户a follow／unfollow用户b后，不仅仅是b此后发布的post，而是连同他曾经发布过的所有post 都要按照universal时间顺序出现／消失在a的新鲜事列表里。假设每个人平均发帖N条，follow X人，每follow一个人将造成worst case O(X*N^2)的时间开销。因此需要用一个priority queue，而不是list，作为存储新鲜事的数据结构，并给每个tweet贴一个timestamp，作为priority 的比较标准。于是，a follow b时，不仅要把a加入b的听众列表，而且要把b发过的所有post加入a的priority queue of news。这样一来，又需要给每个用户维护一个自己发过的贴的列表，因为在这种“即时收听”模式下，用户所有的news feed连同自己发的贴都混在一起无法区分。

假设每人平均发帖N，follow X人， 被Y人follow，那么这种方法所需的资源和开销：

需要维护的field：

Map<Integer, Set<Integer>>; // 用户map成用户的followers

Map<Integer, PriorityQueue<Tweet>>; // 用户map成该用户收到的news feed

Map<Integer, List<Integer>>； // 用户map成该用户自己发的贴

时间开销：

postTweet(): O(Y\*lg(N\*X))

getNewsFeed(): O(lg(N\*X))

follow()/unfollow(): O(N\*lg(N\*X))


这个做法有明显的愚蠢之处。

除了之前提到过的1. post这个想必会很频繁的操作开销过大、2. 对同一个tweet的许多不同引用到处乱飞，之外，笨想一下：向一个听众发布一条新鲜事这么简单的、本来常数开销就能做到的事情，仅仅因为follow／unfollow某人时需要的更新，就沦为了lg级别－－ 感觉很不划算啊。。

所以如果我的用户对“即时收听”的需求并没有那么强烈，如果我们等到主动调用getNewsFeed（譬如用户刷新timeline）时才去收集自己的新鲜事呢？这就需要记住我都follow了谁－－以便确定我要去哪里收集；同时，为了能让我的听众收集到我发的贴，我需要自己维护一个列表记住自己发过的贴。由于我只需要保存自己发的贴，而且它们一定是按时间顺序发的，用一个list就可以了。在这种“延迟收听”模式下，post、follow／unfollow的时间开销都变为了常数。调用getNewsFeed时，我面前放着自己follow的X个人发布的X个新鲜事列表，每个列表都是按时间顺序sort好的；我在每个列表尾部放一个指针，用类似merge sort的方法依次从每个列表中取出最近的post放入一个priority queue里，放完一轮poll一个，刚poll的那个来自哪个列表，就把那个列表的下一个post取出来放进priority queue里，同时指针往前挪一步，依此类推直到收集够10条或者priority queue被取空。为了知道每次被poll的来自哪个列表，可以给每条tweet贴一个标签记住它是谁发的。

假设每人平均发帖N，follow X人， 被Y人follow，这种方法所需的资源和开销：

需要维护的field：

Map<Integer, Set<Integer>>; // 用户map成用户follow了谁

Map<Integer, List<Integer>>: // 用户map成该用户自己发的贴

时间开销：

postTweet(): O(1)

getNewsFeed(): O(X\*lgN)

follow()/unfollow(): O(1)

经测试该解法速度超过leetcode上97%的java解法。代码如下：


```
public class Twitter {
    
    private static int TIMESTAMP = 0;
    Map<Integer, List<Tweet>> usersPost;
    Map<Integer, Set<Integer>> usersFollowees;

    public Twitter() {
        usersPost = new HashMap<Integer, List<Tweet>>();
        usersFollowees = new HashMap<Integer, Set<Integer>>();
    }
    
    private static class Tweet implements Comparable<Tweet> {
        int time;
        int userId;
        int tweetId;

        Tweet(int userId, int tweetId) {
            this.time = TIMESTAMP++;
            this.userId = userId;
            this.tweetId = tweetId;
        }
        
        @Override
        public int compareTo(Tweet t2) {
            return time < t2.time ? 1 : -1; 
        }
    }
    
    public void postTweet(int userId, int tweetId) {
        // create tweet
        Tweet tweet = new Tweet(userId, tweetId);
        
        // add tweet into op's pq of post
        List<Tweet> post = usersPost.getOrDefault(userId, new ArrayList<Tweet>());
        post.add(tweet);
        usersPost.put(userId, post);
    }
    
    public List<Integer> getNewsFeed(int userId) {
        List<Integer> res = new ArrayList<>();
        
        // get list of followees, i.e. sources that generate user's feed
        Set<Integer> feedSources = usersFollowees.getOrDefault(userId, new HashSet<Integer>());
        if (!feedSources.contains(userId)) feedSources.add(userId);
        
        PriorityQueue<Tweet> feed = new PriorityQueue<>(); 
        Map<Integer, Integer> latestIdx = new HashMap<>(); // pointers pointing to next tweet to add into pq
        Iterator<Integer> it = feedSources.iterator(); // iterate over sources
        while (it.hasNext()) {
            int source = it.next();
            // if this source ever posted, add his latest post into pq
            if (usersPost.containsKey(source)) {
                List<Tweet> post = usersPost.get(source);
                feed.add(post.get(post.size()-1));
                latestIdx.put(source, post.size()-1);
            }
        }
        
        while (feed.size() > 0 && res.size() < 10) {
            Tweet latest = feed.poll();
            res.add(latest.tweetId);
            
            int idx = latestIdx.get(latest.userId);
            if (idx == 0) continue; // this source has been exhausted
            latestIdx.replace(latest.userId, --idx);
            feed.add(usersPost.get(latest.userId).get(idx)); 
        }
        
        return res;
    }
    
    public void follow(int followerId, int followeeId) {
        if (followerId == followeeId) return;
        
        // add follower to followee's follower set
        Set<Integer> followees = usersFollowees.getOrDefault(followerId, new HashSet<Integer>());
        if (followees.contains(followeeId)) return;
        followees.add(followeeId);
        usersFollowees.put(followerId, followees);
    }
    
    public void unfollow(int followerId, int followeeId) {
        if (followerId == followeeId) return;
        
        // remove follower from followee's follower set
        Set<Integer> followees = usersFollowees.getOrDefault(followerId, new HashSet<Integer>());
        if (!followees.contains(followeeId)) return;
        followees.remove(followeeId);
        usersFollowees.put(followerId, followees);
    }
}

```

--

总结一下：解法1用一个整体而言更笨拙的模型、更大的空间开销、post和follow／unfollow的低效换来了getNewsFeed lg级别的时间复杂度。这种牺牲并不值得，因为post、follow／unfollow比getNewsFeed频繁得多这样的假设，对于twitter这样的社交平台来说似乎是非常合理的（如果是qq的话那就是另一回事了）。基于这个假设，解法2把开销迁移到了getNewsFeed上；对于那些没发过几个贴也没什么人follow、然而一口气follow了超多人的用户，解法2的优越性就弱得多了。（这样说起来的话每个社交平台似乎有相当一部分用户是由这种人构成的。。）

用一个不太精确的类比：第一个开销大的“即时收听”模式相当于TCP，每个包都要当场保证精确发送到目标地址；第二个开销小的“延迟收听”模式相当于UDP,包就在系统里到处飞，需要的时候再自己去捡。不过需要重新复习一下网络知识才能确定这个类比到底有多不恰当。。