# tirpitz9992-foxmail.com
Simply Playing
#zkw线段树

标签（空格分隔）： 数据结构

---
这里感谢thu的张昆伟大佬！
[->原ppt的地址，用gzu的公网就可以免费下载<-][1]
学完我人傻了
zkw线段树比线段树要快2-3倍，这里我刚刚在某oj交了一个题
普通线段树是2k多ms
zkw线段树是1k多ms
快太多了。。。
~~当然也有可能是我线段树写的丢人hhhhhh~~
代码量和效率要强很多
又好写，又好用
当然。。大佬写的肯定比我好，还是推荐去看原稿
我上面那个是超链接，可以点的，嗯
##构造方式
线段树的构造方式
![image_1dvqo513a11uf48k1svk1lfntd9.png-164.7kB][2]
然后我们换算成二进制来康康
![image_1dvqmrc81u4p1vmikemejtphqm.png-208.4kB][3]
规律显然是明显的
在此，我们刨析线段树
```
void build_tree(int arr[] , int tree[] , int node , int start , int end)
{
    if(start == end)
        tree[node] = arr[start];
    else
    {
        int mid = (start + end) / 2;
        int left_node = 2 * node + 1;
        int right_node = 2 * node + 2;
        build_tree(arr , tree , left_node , start , mid);
        build_tree(arr , tree , right_node , mid + 1 , end);
        tree[node] = tree[left_node] + tree[right_node];
    }
}
```
我们观察一下代码，线段树的建树实现是依靠递归的方法来实现建树的，而这样的方法在一些情况中，例如zkw大佬所说的poj某题，常数太大导致tle
```
int left_node = 2 * node + 1;
int right_node = 2 * node + 2;
```
左节点和右节点的父节点的之间的关系是什么呢？
如果我们对于位运算有一些了解的话
很轻松的就可以得出结论
一个节点的父节点就是这个数左移1
那么子节点和父节点之前的关系呢？
很显然，相反过来，左节点就是右移1，右节点就是右移1后+1
我们可以简单回忆一下bfs的搜索顺序，我们可以明了的发现，同一层的节点之间的关系是依次递增的
一层有多少个节点呢?
就是$2^{(n-1)}$个节点，其中$n$就是我们的层数
那么我们所需要的空间大小就是最后一层的节点数

有了这些规律我们就可以开始建树了
```
#include <iostream>
#include <cstdio>
#include <fstream>
#include <algorithm>
#include <cmath>
#include <deque>
#include <vector>
#include <queue>
#include <string>
#include <cstring>
#include <iomanip>
#include <map>
#include <random>
#include <stack>
#include <set>
#include <sstream>
#pragma GCC optimize(2)
#define IOS ios_base::sync_with_stdio(0); cin.tie(0);
#define Mod 1000000007
#define eps 1e-6
#define ll long long
#define INF 0x3f3f3f3f
#define MEM(x,y) memset(x,y,sizeof(x))
#define maxn 100000 * 2 + 10
using namespace std;
const int N = 16;
int n , m ;
int M;
int tree[N<<1];
inline void upd(int x)
{
    tree[x] = tree[x<<1] + tree[x<<1 | 1];
}
inline void build_tree(int n)
{
    for(M = 1 ; M < n ; M <<= 1);
    for(int i = M + 1 ; i <= M + n ; i++) 
    {
        int t; cin>>t;
        tree[i] = t;
    }
    for(int j = M - 1 ; j ; j--) upd(j);
}
```
略去头文件之后， 是不是发现。。。
tiz啊，你是不是没写完就发上来了啊？
我不是，我没有，别瞎说啊.jpg
这还是我写的比较复杂。。刚学，，，那些大佬写的更短
我们简要观察一下这份代码
中间变量M记录我们树的第$log_2(n) - 1$层的最后一个节点的下标
接下来的内容就很清晰明了了
我们把数据输入最后一层，那么，如果不够$2^{n-1}$怎么办啊？
大佬的处理方法就是不管。。直接当虚点
然后，我们进行区间合并操作，左移节点得到子节点
```
inline void upd(int x)
{
    tree[x] = tree[x<<1] + tree[x<<1 | 1];
}
```
你康，这不是我们上面的结论吗
>一个节点的父节点就是这个数左移1

好了，现在已经解释完毕了
##更新
```
inline void update(int x , int y)
{
    for(tree[x += M] += y , x >>= 1 ; x ; x>>=1)
        upd(x);
}
```
M依然还是上面的数字，我们再次不做解释。。
emmmm。。。这个更新应该不用解释吧
不过需要注意的是
```
tree[x += M] += y
```
你们如果是直接套板子的，看好啊！
是+=，+=，不是赋值啊！
##查询
正如zkw大佬所说，数据结构的本质是统计
查询这里比较难理解
我们首先需要考虑需要查询的节点的位置关系
令始节点为s，末节点为e
如果s是左节点，那么s的父节点就会被s->e这个范围包含，反之该区间内只有s被包含
e也是同理
当s和e是兄弟节点的时候，说明区间中的数已经全部被包含
本质就是不断的上移操作
```
inline int query(int s , int e)
{
    int ans = 0;
    s = s + m - 1 ; t = t + M + 1;
    for( ; s^t^1 ; s >>= 1 , t >>= 1)
    {
        if(~s & 1) ans += tree[s ^ 1];
        if(t & 1) ans += tree[t ^ 1];
    }
    return ans;
}
```
我们依然用zkw大佬的图
![image_1dvqmrc81u4p1vmikemejtphqm.png-208.4kB][3]
通过一些观查，即可得出此结论

基本内容已经介绍完毕
在此我贴上一些大佬些的常用操作的代码，便于理解
##常用操作
###维护区间和
```
for(int i=M-1;i;--i) d[i]=d[i<<1]+d[i<<1|1];
```
###维护最大值
```
for(int i=M-1;i;--i) d[i]=max(d[i<<1],d[i<<1|1]);
```
###维护最小值
```
for(int i=M-1;i;--i) d[i]=min(d[i<<1],d[i<<1|1]);
```
###区间最小值
```
void Sum(int s,int t,int L=0,int R=0)
{
    for(s=s+M-1,t=t+M+1;s^t^1;s>>=1,t>>=1){
        L+=d[s],R+=d[t];
        if(~s&1) L=min(L,d[s^1]);
        if(t&1) R=min(R,d[t^1]);
    }
    int res=min(L,R);while(s) res+=d[s>>=1];
}
```
###区间最大值
```
void Sum(int s,int t,int L=0,int R=0)
{
    for(s=s+M-1,t=t+M+1;s^t^1;s>>=1,t>>=1){
        L+=d[s],R+=d[t];
        if(~s&1) L=max(L,d[s^1]);
        if(t&1) R=max(R,d[t^1]);
    }
    int res=max(L,R);while(s) res+=d[s>>=1];
}
```
###区间加法
```
void Add(int s,int t,int v,int A=0)
{
    for(s=s+M-1,t=t+M+1;s^t^1;s>>=1,t>>=1){
        if(~s&1) d[s^1]+=v;if(t&1) d[t^1]+=v;
        A=min(d[s],d[s^1]);d[s]-=A,d[s^1]-=A,d[s>>1]+=A;
        A=min(d[t],d[t^1]);d[t]-=A,d[t^1]-=A,d[t>>1]+=A;
    }
    while(s) A=min(d[s],d[s^1]),d[s]-=A,d[s^1]-=A,d[s>>=1]+=A;
}
```
文章就到这里，如果想进行深入了解的话，请去阅读zkw大佬的原稿
肯定比我写的好hhhhh
[1]: https://wenku.baidu.com/view/f27db60ee87101f69e319544.html
  [2]: http://static.zybuluo.com/tirpitz/ixglstehhuqno9eolds9b4b1/image_1dvqo513a11uf48k1svk1lfntd9.png
  [3]: http://static.zybuluo.com/tirpitz/4ko9gysi89vpjfkjbvzx2k0e/image_1dvqmrc81u4p1vmikemejtphqm.png
