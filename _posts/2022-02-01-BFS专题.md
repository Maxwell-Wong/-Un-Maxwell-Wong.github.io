# 什么时候用BFS呢？

**BFS是用来搜索最短路径的解**

##### BFS的一般步骤：

```cpp
广度优先搜索算法的基本思想：
1、对于初始状态入队，设置初始状态为已访问
2、如果队列不为空时，出队队头元素，否则跳到第5步
3、检查出队的元素是否为最终解，如果是则跳到第5步。
4、对于出队的元素，检查所有相邻状态，如果有效并且未访问，则将
   所有有效的相邻状态进行入队，并且设置这些状态为已访问，然后
   跳到第2步重复执行
5、检查最后出队的元素是否为最终解，如果是输出结果，否则说明无解

广度优先搜索是借助于队列这种数据结构进行搜索的，队列的特点是先
进先出（FIFO），通过包含queue这个队列模板头文件，就可以利用c++
的队列模板定义自己的队列了，队列的操作非常简单，主要有以下几个：
q.push() 入队操作
q.front() 取队头元素
q.pop() 队头元素出队
q.size() 获取队列的元素个数
q.empty() 判断队列是否为空，为空返回true，不为空返回false

广度优先搜索算法的关键是要搞清楚求解过程中每一步的相邻状态有哪些，
每个状态需要记录什么信息，在搜索过程中如何标记这些状态为已访问。
```

##### BFS的缺点：

**在搜索过程中，BFS必须要保存搜索过程中的状态，用于判重。**

- 注意，用于保存搜索过程的点的数据结构，要是hashset类型的。至于为什么，参考[Python 中list ,set,dict的大规模查找效率](https://blog.csdn.net/jmh1996/article/details/78481365)总结的很到位了。**如果用in 方法去判断元素是否在list中时，会超时。所以最好用set。**
- 即使用了set，也是需要一定的时间复杂度的。因此势必会花掉一些时间和空间。

##### BFS的适用情况

**一般在树或者图中，用BFS的可能性比较大。**

> **1.BFS是用来搜索最短径路的解是比较合适的，比如求最少步数的解，最少交换次数的解，因为BFS搜索过程中遇到的解一定是离根最近的，所以遇到一个解，一定就是最优解，此时搜索算法可以终止。**
> 这个时候不适宜使用DFS，因为DFS搜索到的解不一定是离根最近的，只有全局搜索完毕，才能从所有解中找出离根的最近的解。（当然这个DFS的不足，可以使用迭代加深搜索ID-DFS去弥补）

# P1443 马的遍历

#### **注：其实就是把棋盘上的每一个点按照规则入队，第一次到达该点时的步数一定是最优步数**，因此用BFS

## 题目描述

有一个n*m的棋盘(1<n,m<=400)，在某个点上有一个马,要求你计算出马到达棋盘上任意一个点最少要走几步

## 输入格式

一行四个数据，棋盘的大小和马的坐标

## 输出格式

一个n*m的矩阵，代表马到达某个点最少要走几步（左对齐，宽5格，不能到达则输出-1）

## 输入输出样例

**输入**

```
3 3 1 1
```

**输出**

```
0    3    2    
3    -1   1    
2    1    4    
```

```c++
#include<cstdio>
#include<string.h>
#include<queue>
#include <iostream>
using namespace std;
int a[401][401];
bool b[401][401];//用来判重
int dx[4]={1,-1,2,-2};//马走日字，照理来说有8个坐标，不过可以用绝对值不相等的规律来判断
int dy[4]={1,-1,2,-2};
int n,m;
struct xy{//为了用STL中的queue，因此要弄一个结构体来存放坐标，以此作为queue容器中的元素
    int x,y;
}node,top//node用于访问此结构体（更新值），top是队列头
void bfs(int x,int y)
{
    node.x=x;
    node.y=y;
    a[x][y]=0;
    b[x][y]=true;
    queue<xy>Q;
    Q.push(node);//将马的坐标进队列
    while(!Q.empty())//当队列不为空的时候
    {//之后就不断地把符合条件的点放入队列，再把队列的头弹出，直到队列为空
        top=Q.front();//将队列的头用top保存
        Q.pop();//弹出队列的头
        for(int i=0;i<4;i++)
        {
            for(int j=0;j<4;j++)
            {
                if(abs(dx[i])!=abs(dy[j])){//这里用马的移动的规律，进行枚举每个符合条件的方向坐标
                    int newx=top.x+dx[i];//进行位移，取得新的坐标
                    int newy=top.y+dy[j];
                    if(newx<1||newx>n||newy<1||newy>m) continue;//判断越界，如果越界就略过
                    if(!b[newx][newy]){//如果当前的点没有被访问过
                    b[newx][newy]=true;//设置为已访问
                    node.x=newx;//将新的点放入结构体，以便进入队列尾
                    node.y=newy;
                    Q.push(node);//新点进入队列
                    a[newx][newy]=a[top.x][top.y]+1;//将新的点处的值设为前一个点+1
                    }
                }
            }
        }
    }
}
int main()
{
    memset(a, -1, sizeof(a));//棋盘格式化为-1
    int x,y;
    cin>>n>>m>>x>>y;
    bfs(x,y);
    for(int i=1;i<=n;i++)
    {
        for(int j=1;j<=m;j++)
        {
            printf("%-5d",a[i][j]);//格式化输出
            
        }
        printf("\n");
    }
}

```

### 写法二

介绍一下用STL模板库 <queue>来广搜这道题

广搜什么的自然不同我介绍； 我来介绍一下非常好用但没人用的

### pair

```
queue<pair<int,int> > q;
```

它可以将两种数据类型的值组合成一个值存入
队列中大体是这样操作：

```
queue<pair<int,int> > q;//定义

q.push(make_pair(x,y));//入队
//取队首
xx=q.front().first;//第一个值
yy=q.front().second;//第二个值

q.pop();//出队
```

```c++
#include<iostream>//P1443
#include<cstdio>
#include<cstring>
#include<string>
#include<algorithm>
#include<queue>
#include<cmath>
using namespace std;
const int dx[8]={-1,-2,-2,-1,1,2,2,1};
const int dy[8]={2,1,-1,-2,2,1,-1,-2};//8个方向
queue<pair<int,int> >q;
int a[500][500];//存步数
bool vis[500][500];//走没走过
int main()
{
    int n,m,x,y;
    memset(a, -1, sizeof(a));
    cin>>n>>m>>x>>y;
    a[x][y]=0;vis[x][y]=true;
    q.push(make_pair(x, y));
    while (!q.empty()) {
        int xx=q.front().first;//取出队列头元素
        int yy=q.front().second;
        q.pop();//弹出
        for(int i=0;i<8;i++)//8个方向进行搜索
        {
            int u=xx+dx[i];
            int v=yy+dy[i];
            if(u<1||u>n||v<1||v>m||vis[u][v]) continue;
            vis[u][v]=true;
            q.push(make_pair(u, v));
            a[u][v]=a[xx][yy]+1;
        }
    }
    for(int i=1;i<=n;i++)
    {
        for(int j=1;j<=m;j++)
        {
            printf("%-5d",a[i][j]);
        }
        printf("\n");
    }
}

```

# P1135 奇怪的电梯

## 题目描述

呵呵，有一天我做了一个梦，梦见了一种很奇怪的电梯。大楼的每一层楼都可以停电梯，而且第i*i*层楼(1≤i≤N)(1≤*i*≤*N*)上有一个数字Ki(0≤Ki≤N)*K**i*(0≤*K**i*≤*N*)。电梯只有四个按钮：开，关，上，下。上下的层数等于当前楼层上的那个数字。当然，如果不能满足要求，相应的按钮就会失灵。例如：3，3，1，2，5代表了Ki(K1=3,K2=3,…)*K**i*(*K*1=3,*K*2=3,…)，从11楼开始。在11楼，按“上”可以到44楼，按“下”是不起作用的，因为没有−2−2楼。那么，从A*A*楼到B*B*楼至少要按几次按钮呢？

## 输入格式

共二行。

第一行为3个用空格隔开的正整数，表示N,A,B(1≤N≤200,1≤A,B≤N)*N*,*A*,*B*(1≤*N*≤200,1≤*A*,*B*≤*N*)。

第二行为N个用空格隔开的非负整数，表示Ki。

## 输出格式

一行，即最少按键次数,若无法到达，则输出−1−1。

## 输入输出样例

**输入 #1**复制

```
5 1 5
3 3 1 2 5
```

**输出 #1**复制

```
3
```

### 题解：

```c++
//由于题目求的是从电梯起点到终点需要按下按钮的最少次数，因此考虑BFS
//BFS算法的关键是要搞清楚求解过程中每一步的相邻状态有哪些，每个状态需要记录什么信息，在搜索过程中如何标记这些状态为已访问。
//既然要用BFS，那么就要考虑各个状态，我们在每一层楼都有两种选择，要么上要么下，同时又有两个必要的参数，一个是当前的楼层编号一个是当前按下了多少次电梯按钮。
//即在本题中，相邻状态为当前所在楼层通过按向上或向下按钮所能到达的楼层，每个状态要记录的信息包括楼层编号和按按钮的次数。
#include<iostream>
#include<queue>
using namespace std;
int a[201];
bool vis[201];
int n,f,l;
int ans;
struct xy{
    int floor;//当前楼层
    int cnt;//按下的按钮次数
}e,top;
queue<xy>q;
void bfs(int x,int y)
{
    int now;
    e.floor=x;
    e.cnt=0;
    q.push(e);
    vis[x]=true;
    while(!q.empty())
    {
        top=q.front();
        q.pop();
        if(top.floor==y)break;
        now=top.floor+a[top.floor];//电梯往上所能到达的层数
        if(now<=n&&!vis[now])
        {
            e.floor=now;
            e.cnt=top.cnt+1;
            q.push(e);
            vis[now]=true;
        }
        now=top.floor-a[top.floor];//电梯往下所能到达的层数
        if(now>=0&&!vis[now])
        {
            e.floor=now;
            e.cnt=top.cnt+1;
            q.push(e);
            vis[now]=true;
        }
    }
}
int main()
{
    cin>>n>>f>>l;
    for(int i=1;i<=n;i++)
    cin>>a[i];
    bfs(f,l);
    if(top.floor==l)cout<<top.cnt;
    else cout<<-1;
}
```

# P2895 [USACO08FEB]Meteor Shower S

## 题目描述

Bessie hears that an extraordinary meteor shower is coming; reports say that these meteors will crash into earth and destroy anything they hit. Anxious for her safety, she vows to find her way to a safe location (one that is never destroyed by a meteor) . She is currently grazing at the origin in the coordinate plane and wants to move to a new, safer location while avoiding being destroyed by meteors along her way.

The reports say that M meteors (1 ≤ M ≤ 50,000) will strike, with meteor i will striking point (Xi, Yi) (0 ≤ Xi ≤ 300; 0 ≤ Yi ≤ 300) at time Ti (0 ≤ Ti ≤ 1,000). Each meteor destroys the point that it strikes and also the four rectilinearly adjacent lattice points.

Bessie leaves the origin at time 0 and can travel in the first quadrant and parallel to the axes at the rate of one distance unit per second to any of the (often 4) adjacent rectilinear points that are not yet destroyed by a meteor. She cannot be located on a point at any time greater than or equal to the time it is destroyed).

Determine the minimum time it takes Bessie to get to a safe place.

## 输入格式

\* Line 1: A single integer: M

\* Lines 2..M+1: Line i+1 contains three space-separated integers: Xi, Yi, and Ti

## 输出格式

\* Line 1: The minimum time it takes Bessie to get to a safe place or -1 if it is impossible.

## 题意翻译

贝茜听说了一个骇人听闻的消息：一场流星雨即将袭击整个农场，由于流星体积过大，它们无法在撞击到地面前燃烧殆尽，届时将会对它撞到的一切东西造成毁灭性的打击。很自然地，贝茜开始担心自己的安全问题。以 Farmer John 牧场中最聪明的奶牛的名誉起誓，她一定要在被流星砸到前，到达一个安全的地方（也就是说，一块不会被任何流星砸到的土地）。如果将牧场放入一个直角坐标系中，贝茜现在的位置是原点，并且，贝茜不能踏上一块被流星砸过的土地。 根据预报，一共有 M*M* 颗流星 (1≤M≤50,000)(1≤*M*≤50,000) 会坠落在农场上，其中第i颗流星会在时刻 Ti*T**i* (0≤Ti≤1,000)(0≤*T**i*≤1,000) 砸在坐标为 (Xi,Yi)(*X**i*,*Y**i*) (0≤Xi≤300(0≤*X**i*≤300，0≤Yi≤300)0≤*Y**i*≤300) 的格子里。流星的力量会将它所在的格子，以及周围 44 个相邻的格子都化为焦土，当然贝茜也无法再在这些格子上行走。

贝茜在时刻 00 开始行动，它只能在第一象限中，平行于坐标轴行动，每 11 个时刻中，她能移动到相邻的（一般是 44 个）格子中的任意一个，当然目标格子要没有被烧焦才行。如果一个格子在时刻 t*t* 被流星撞击或烧焦，那么贝茜只能在 t*t* 之前的时刻在这个格子里出现。 贝西一开始在(0,0)(0,0)。

请你计算一下，贝茜最少需要多少时间才能到达一个安全的格子。如果不可能到达输出 −1−1。

Translated by @奆奆的蒟蒻 @跪下叫哥

## 输入输出样例

**输入 #1**复制

```
4
0 0 2
2 1 2
1 1 2
0 3 5
```

**输出 #1**复制

```
5
```

```c++
#include<iostream>
#include<queue>
using namespace std;
int n,nx,ny;
int ans;
int timemin[305][305],vis[305][305];
int dx[]={0,0,1,-1};
int dy[]={1,-1,0,0};
struct xy{
    int x,y;
    int t;
}top,node;
queue<xy>q;
void bfs(int sx,int sy,int st)
{
    node.x=sx;
    node.y=sy;
    node.t=st;
    vis[sx][sy]=1;
    q.push(node);
    while(!q.empty())
    {
        node=q.front();
        q.pop();
        for(int i=0;i<4;i++)
        {
            nx=node.x+dx[i];
            ny=node.y+dy[i];
            if(nx>=0&&ny>=0&&vis[nx][ny]==0&&(timemin[nx][ny]==-1||node.t+1<timemin[nx][ny]))
            {
                top.x=nx;top.y=ny;top.t=node.t+1;vis[nx][ny]=1;
                q.push(top);
                if(timemin[nx][ny]==-1)
                {
                    cout<<top.t<<endl;
                    return;
                }
            }
        }
    }
    cout<<-1<<endl;
}
int main()
{
    int x,y,t;
    cin>>n;
    for(int i=0;i<=301;i++)
        for(int j=0;j<=301;j++)
            timemin[i][j]=-1;
    for(int i=1;i<=n;i++)
    {
        cin>>x>>y>>t;
        if(t<timemin[x][y]||timemin[x][y]==-1)
            timemin[x][y]=t;
        for(int i=0;i<4;i++)
        {
            nx=x+dx[i];ny=y+dy[i];
            if(nx>=0&&ny>=0&&(timemin[nx][ny]==-1||t<timemin[nx][ny]))
                timemin[nx][ny]=t;
        }
    }
    bfs(0, 0, 0);
}

```

