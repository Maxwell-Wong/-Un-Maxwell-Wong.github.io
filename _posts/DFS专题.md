[TOC]

# P1706全排列问题

## 题目描述

输出自然数 1到 n所有不重复的排列，即 n*n 的全排列，要求所产生的任一数字序列中不允许出现重复的数字。

## 输入格式	

一个整数 *n*。

## 输出格式

由 1∼*n* 组成的所有不重复的数字序列，每行一个序列。

每个数字保留 55 个场宽。

## 输入输出样例

**输入 #1**

```
3
```

**输出 #1**

```
    1    2    3
    1    3    2
    2    1    3
    2    3    1
    3    1    2
    3    2    1
```

## 题解

思路：

以n=3举例：

首先，分别建立两个数组，一个int型的数组a，用于记录每种情况，一个bool型数组vis，用于判断当前数字是否使用过。

之后，编写dfs函数：先判断当前的vis[i]是否为0，如果为0则说明未被访问过，则进入递归程序。（具体实现在代码解读）

最后，如果当前搜索层数大于n时，退出递归。

```c++
#include<iostream>
#include<iomanip>
using namespace std;
int a[20];
bool vis[20];
int n;
void print()
{
    for(int i=1;i<=n;i++)
    {
        cout<<setw(5)<<a[i];//setw()指定宽度
    }
    cout<<endl;
}
void dfs(int x)
{
    if(x>n) print();
    for(int i=1;i<=n;i++)
    {
        if(!vis[i])
        {
            vis[i]=1;
            a[x]=i;
            dfs(x+1);
            vis[i]=0;
        }
    }
}
int main()
{
    cin>>n;
    dfs(1);
}

```



## 代码解读：

```
 //递归（dfs）主体
 if(!vis[i])
        {
            vis[i]=1;//未访问过的现在要标记已访问
            a[x]=i;//注意是a[x]，x代表当前搜索的层数，在当前这一层选一个数i
            dfs(x+1);//继续往下一层搜索
            vis[i]=0;//搜索完之后，要释放上一层的数，以便下一次使用
        }
        
```

![image-20210125105823187](/Users/maxwell/Library/Application Support/typora-user-images/image-20210125105823187.png)

![image-20210125105731059](/Users/maxwell/Library/Application Support/typora-user-images/image-20210125105731059.png)

# P1605 迷宫

## 题目背景

给定一个N*M方格的迷宫，迷宫里有T处障碍，障碍处不可通过。给定起点坐标和终点坐标，问: 每个方格最多经过1次，有多少种从起点坐标到终点坐标的方案。在迷宫中移动有上下左右四种方式，每次只能移动一个方格。数据保证起点上没有障碍。

## 题目描述

无

## 输入格式

第一行N、M和T，N为行，M为列，T为障碍总数。第二行起点坐标SX,SY，终点坐标FX,FY。接下来T行，每行为障碍点的坐标。

## 输出格式

给定起点坐标和终点坐标，问每个方格最多经过1次，从起点坐标到终点坐标的方案总数。

## 输入输出样例

**输入 #1**

```
2 2 1
1 1 2 2
1 2
```

**输出 #1**复制

```
1
```

## 说明/提示

【数据规模】

1≤N,M≤5



## 题解

```c++
#include<cstdio>
#include<iostream>
using namespace std;
int n,m,t,sx,sy,fx,fy,ans;
bool vis[10][10];
bool  mp[10][10];
int Xx[]={1,0,-1,0};
int Yy[]={0,-1,0,1};
void dfs(int x,int y)
{
    if(x==fx&&y==fy)
    {
        ans++;
        return;
    }
    for(int i=0;i<4;i++)
    {
        int dx=Xx[i]+x;
        int dy=Yy[i]+y;
        if(dx>=1&&dx<=n&&dy>=1&&dy<=m&&!vis[dx][dy]&&!mp[dx][dy])
        {
            vis[x][y]=1;
            dfs(dx, dy);
            vis[x][y]=0;
        }
    }
}
int main()
{
    cin>>n>>m>>t;
    cin>>sx>>sy>>fx>>fy;
    while(t--)
    {
        int x,y;
        cin>>x>>y;
        mp[x][y]=1;
    }
    dfs(sx,sy);
    cout<<ans;
    return 0;
}


```

# P2392 kkksc03考前临时抱佛脚

（DFS或者01背包）

## 题目背景

kkksc03 的大学生活非常的颓废，平时根本不学习。但是，临近期末考试，他必须要开始抱佛脚，以求不挂科。

## 题目描述

这次期末考试，kkksc03 需要考 44 科。因此要开始刷习题集，每科都有一个习题集，分别有 s1,s2,s3,s4*s*1,*s*2,*s*3,*s*4 道题目，完成每道题目需要一些时间，可能不等（A1,A2,…,As1*A*1,*A*2,…,*A**s*1，B1,B2,…,Bs2*B*1,*B*2,…,*B**s*2，C1,C2,…,Cs3*C*1,*C*2,…,*C**s*3，D1,D2,…,Ds4*D*1,*D*2,…,*D**s*4）。

kkksc03 有一个能力，他的左右两个大脑可以同时计算 22 道不同的题目，但是仅限于同一科。因此，kkksc03 必须一科一科的复习。

由于 kkksc03 还急着去处理洛谷的 bug，因此他希望尽快把事情做完，所以他希望知道能够完成复习的最短时间。

## 输入格式

本题包含 55 行数据：第 11 行，为四个正整数 s1,s2,s3,s4*s*1,*s*2,*s*3,*s*4。

第 22 行，为 A1,A2,…,As1*A*1,*A*2,…,*A**s*1 共 s1*s*1 个数，表示第一科习题集每道题目所消耗的时间。

第 33 行，为 B1,B2,…,Bs2*B*1,*B*2,…,*B**s*2 共 s2*s*2 个数。

第 44 行，为 C1,C2,…,Cs3*C*1,*C*2,…,*C**s*3 共 s3*s*3 个数。

第 55 行，为 D1,D2,…,Ds4*D*1,*D*2,…,*D**s*4 共 s4*s*4 个数，意思均同上。

## 输出格式

输出一行,为复习完毕最短时间。

## 输入输出样例

**输入 #1**复制

```
1 2 1 3		
5
4 3
6
2 4 3
```

**输出 #1**复制

```
20
```

## 说明/提示

1≤s1,s2,s3,s4≤201≤*s*1,*s*2,*s*3,*s*4≤20。

1≤A1,A2,…,As1,B1,B2,…,Bs2,C1,C2,…,Cs3,D1,D2,…,Ds4≤601≤*A*1,*A*2,…,*A**s*1,*B*1,*B*2,…,*B**s*2,*C*1,*C*2,…,*C**s*3,*D*1,*D*2,…,*D**s*4≤60。

## 题解

根据题目可知，完成某一科目的题可以左右脑并用，也就是说可以在同一时间里面完成两个题目（如数学有三道题，分别需要2，3，5的时间来完成，那么如果选择2，5一起做，则2分钟后做完一道题，另一道题还有3分钟，此时再加入需要3分钟的那道题，因此总的需要时间是5分钟）按照这个逻辑可以利用DFS来进行搜索出最短的时间

```c++
#include<iostream>
using namespace std;
int s[5];
int a[21][5];
int Left,Right;
int minn,ans;
void My_search(int x,int y)//x是表示当前是第几道题，y表示当前的科目序号
{
    if(x>s[y]){//当前的题号超过了当前科目的题数
        minn=min(minn,max(Left,Right));//为什么是max(Left,Right)呢，因为左右脑同时完成一道题肯定是需要时间更大的那个时间
        return;
    }
    Left+=a[x][y];
    My_search(x+1,y);
    Left-=a[x][y];
    Right+=a[x][y]
    My_search(x+1, y);
    Right-=a[x][y];
}
int main()
{
    cin>>s[1]>>s[2]>>s[3]>>s[4];//这边用数组进行存放每个科目的题目数
    for(int i=1;i<=4;i++){//循环，分开不同科目进行搜索，最后将各个科目的最短时间加起来
        Left=Right=0;//记得每次搜索都要设为0
        minn=19260817;
        for(int j=1;j<=s[i];j++)
        cin>>a[j][i];//行为题目数，列为科目
        My_search(1,i);
        ans+=minn;
    }
    cout<<ans;
    return 0;
}

```

### 01背包解法

思路：  separating the subject to several situation and then suppose this amount of time is T in a fast situation is that t/2， next we need to make every situation extremely close to t/2， so this is 01 bag problem.

So the way to finish this program will be least after this.

Firstly, we need to set up a DP array,the row is the number of subjects, the column is the value equal to the time of subject

Secondly, reduce the 01 bag problem's notion to find the number which is closest to t/2.



```c++
#include<iostream>
using namespace std;
int len[4];
int a[21];
int dp[1000][1000];
int ans;
int main()
{
    for(int i=0;i<4;i++)cin>>len[i];
    for(int i=0;i<4;i++)
    {
        int v=0;
        for(int j=1;j<=len[i];j++){
            cin>>a[j];
            v+=a[j];
        }
        //sort(a,a+len[i]);
        int t1=0;//以下是选择其中一半大脑来分析
        for(int j=1;j<=len[i];j++)
        {
            for(int k=0;k<=v/2;k++)
            {
                dp[j][k]=dp[j-1][k];
                if(k>=a[j]) dp[j][k]=max(dp[j][k],dp[j-1][k-a[j]]+a[j]);
                t1=max(t1,dp[j][k]);//t1是当前最接近t/2的数
            }
        }
        ans+=max(t1,v-t1);//另一半自然就是v-t1
    }
    cout<<ans;
}

```

# P1019 [NOIP2000 提高组] 单词接龙

**此题有难度，需要认真琢磨**

## 题目背景

注意：本题为上古 NOIP 原题，不保证存在靠谱的做法能通过该数据范围下的所有数据。

## 题目描述

单词接龙是一个与我们经常玩的成语接龙相类似的游戏，现在我们已知一组单词，且给定一个开头的字母，要求出以这个字母开头的最长的“龙”（每个单词都最多在“龙”中出现两次），在两个单词相连时，其重合部分合为一部分，例如 `beast` 和 `astonish`，如果接成一条龙则变为 `beastonish`，另外相邻的两部分不能存在包含关系，例如 `at` 和 `atide` 间不能相连。

## 输入格式

输入的第一行为一个单独的整数 n*n* 表示单词数，以下 n*n* 行每行有一个单词，输入的最后一行为一个单个字符，表示“龙”开头的字母。你可以假定以此字母开头的“龙”一定存在。

## 输出格式

只需输出以此字母开头的最长的“龙”的长度。

## 输入输出样例

**输入 #1**复制

```
5
at
touch
cheat
choose
tact
a
```

**输出 #1**

```
23
```

## 说明/提示

样例解释：连成的“龙”为 `atoucheatactactouchoose`。

n≤20*n*≤20

### 题解

```c++
#include<iostream>
#include<string>
using namespace std;
int n;
string a[21];
int vis[21];
int lengthmax;
int canlink(string str1,string str2)
{
    for(int i=1;i<min(str1.size(),str2.size());i++)//i表示重叠字母的个数，只需比较 较短的字符串的部分就可以了
    {
        int flag=1;
        for(int j=0;j<i;j++)//用j来循环第二个字符串
        if(str1[str1.size()-i+j]!=str2[j]) flag=0;//如果字母不相同
        if(flag) return i;//如果相同立即返回，就能保证是最小的重叠部分
    }
    return 0;
}
void dfs(string strnow,int lengthnow)
{
    lengthmax=max(lengthmax,lengthnow);//每次记录最长的字符串长度
    for(int i=0;i<n;i++)
    {
        if(vis[i]>=2)continue;//最多只能重复使用2次（0，1）
        int c=canlink(strnow,a[i]);
        if(c>0)//如果有重叠部分，进行dfs
        {
            vis[i]++;
            dfs(a[i],lengthnow+a[i].size()-c);
            vis[i]--;//回溯
        }
    }
}
int main()
{
    cin>>n;
    for(int i=0;i<=n;i++)
    cin>>a[i];
    dfs(' '+a[n],1);//因为那个canlink函数的for循环是从1开始的，然后dfs是以一个字母开始搜索的，所以就执行不了那个for循环，所以要再加一个空白字符增加1个长度（长度等于2），才可以执行for循环
    cout<<lengthmax;
}
```

# P2404 自然数的拆分问题

## 题目描述

任何一个大于1的自然数n，总可以拆分成若干个小于n的自然数之和。现在给你一个自然数n，要求你求出n的拆分成一些数字的和。每个拆分后的序列中的数字从小到大排序。然后你需要输出这些序列，其中字典序小的序列需要优先输出。

## 输入格式

输入：待拆分的自然数n。

## 输出格式

输出：若干数的加法式子。

## 输入输出样例

**输入 #1**复制

```
7
```

**输出 #1**复制

```
1+1+1+1+1+1+1
1+1+1+1+1+2
1+1+1+1+3
1+1+1+2+2
1+1+1+4
1+1+2+3
1+1+5
1+2+2+2
1+2+4
1+3+3
1+6
2+2+3
2+5
3+4
```

## 说明/提示

用回溯做。。。。

n≤8*n*≤8

```c++
#include<iostream>
#include<cstdio>
using namespace std;
int n;
int a[10]={1};//用数组来记录拆分出来的每一个数，这里让a[0]为1，每个数都可以从1开始拆
void print(int t)
{
    for(int i=1;i<t;i++)
        cout<<a[i]<<"+";
    cout<<a[t]<<endl;
}
void dfs(int s,int x)//s是当前要拆分的数，x用来记数（拆了多少个数）
{
    for(int i=a[x-1];i<=s;i++)//i=a[x-1]是因为输出的时候拆分出来的每个数都要大于等于前一个数
    {
        if(i<n)//并且拆分出来的每个数不能比n大
        {
            a[x]=i;//记录这个拆分出来的数
            s-=i;//拆分出来了就要减掉这个数
            if(s==0)print(x);//如果减完了就输出这个序列
            else dfs(s,x+1);
            s+=i;//回溯
        }
    }
}
int main()
{
    cin>>n;
    dfs(n,1);
}

```

# P1162 填涂颜色

## 题目描述

由数字00组成的方阵中，有一任意形状闭合圈，闭合圈由数字11构成，围圈时只走上下左右44个方向。现要求把闭合圈内的所有空间都填写成22.例如：6×66×6的方阵（n=6*n*=6），涂色前和涂色后的方阵如下：

```plain
0 0 0 0 0 0
0 0 1 1 1 1
0 1 1 0 0 1
1 1 0 0 0 1
1 0 0 0 0 1
1 1 1 1 1 1
0 0 0 0 0 0
0 0 1 1 1 1
0 1 1 2 2 1
1 1 2 2 2 1
1 2 2 2 2 1
1 1 1 1 1 1
```

## 输入格式

每组测试数据第一行一个整数n(1≤n≤30)*n*(1≤*n*≤30)

接下来n*n*行，由00和11组成的n×n*n*×*n*的方阵。

方阵内只有一个闭合圈，圈内至少有一个00。

//感谢黄小U饮品指出本题数据和数据格式不一样. 已修改(输入格式)

## 输出格式

已经填好数字22的完整方阵。

## 输入输出样例

**输入 #1**复制

```
6
0 0 0 0 0 0
0 0 1 1 1 1
0 1 1 0 0 1
1 1 0 0 0 1
1 0 0 0 0 1
1 1 1 1 1 1
```

**输出 #1**复制

```
0 0 0 0 0 0
0 0 1 1 1 1
0 1 1 2 2 1
1 1 2 2 2 1
1 2 2 2 2 1
1 1 1 1 1 1
```

## 说明/提示

1≤n≤301≤*n*≤30

### 题解

#### 思路：利用“染色法”，首先明确地图的范围是【1，n】，我们要采用的方法是：将所有墙外的数字染色（赋其他的值），之后利用return，凡是遇到墙或者出界就return，那么为了能够遍历到所有的点，我们就要在地图外再增加一圈，使得目前地图范围是【0，n+1】，这样就可以搜到每一个点，最后如果没有搜到的点（仍然是0）则意味着它处于墙内（因为一遇到墙就return，进不去墙里面），那么再在输出的时候判断，如果地图上的某些点如果仍为0，则输出2，如果被染色的则输出0，其余的就是墙了。

```c++
#include<iostream>
using namespace std;
int n;
int dx[]={0,0,1,-1};//这里的方向数字要一一对应
int dy[]={1,-1,0,0};
int map[50][50];
void dfs(int x,int y)
{
    if(x<0||x>n+1||y<0||y>n+1||map[x][y]!=0) return;
    map[x][y]=3;
    for(int i=0;i<4;i++) dfs(x+dx[i], y+dy[i]);//因为是上下左右，则要让方向处于同一个for循环
}
int main()
{
    cin>>n;
    for(int i=1;i<=n;i++)
    {
        for(int j=1;j<=n;j++)
        {
            cin>>map[i][j];
        }
    }
    dfs(0, 0);//从0，0开始搜，若从（1，1）开始，如果这个点是墙，则无法搜索，动都动不了
    for(int i=1;i<=n;i++)
    {
        for(int j=1;j<=n;j++)
        {
            if(map[i][j]==0) cout<<2<<" ";//如果没有搜到，则说明在墙里面
            else if(map[i][j]==3) cout<<0<<" ";//如果被染色，则在墙外
            else cout<<map[i][j]<<" ";//剩下的是墙
        }
        cout<<endl;
    }
}

```

# P1596 [USACO10OCT]Lake Counting S

注：这是一道用DFS判断连通块的题目

## 题目描述

Due to recent rains, water has pooled in various places in Farmer John's field, which is represented by a rectangle of N x M (1 <= N <= 100; 1 <= M <= 100) squares. Each square contains either water ('W') or dry land ('.'). Farmer John would like to figure out how many ponds have formed in his field. A pond is a connected set of squares with water in them, where a square is considered adjacent to all eight of its neighbors. Given a diagram of Farmer John's field, determine how many ponds he has.

由于近期的降雨，雨水汇集在农民约翰的田地不同的地方。我们用一个NxM(1<=N<=100;1<=M<=100)网格图表示。每个网格中有水('W') 或是旱地('.')。一个网格与其周围的八个网格相连，而一组相连的网格视为一个水坑。约翰想弄清楚他的田地已经形成了多少水坑。给出约翰田地的示意图，确定当中有多少水坑。

## 输入格式

Line 1: Two space-separated integers: N and M * Lines 2..N+1: M characters per line representing one row of Farmer John's field. Each character is either 'W' or '.'. The characters do not have spaces between them.

第1行：两个空格隔开的整数：N 和 M 第2行到第N+1行：每行M个字符，每个字符是'W'或'.'，它们表示网格图中的一排。字符之间没有空格。

## 输出格式

Line 1: The number of ponds in Farmer John's field.

一行：水坑的数量

## 输入输出样例

**输入 #1**复制

```
10 12
W........WW.
.WWW.....WWW
....WW...WW.
.........WW.
.........W..
..W......W..
.W.W.....WW.
W.W.W.....W.
.W.W......W.
..W.......W.
```

**输出 #1**复制

```
3
```

## 说明/提示

OUTPUT DETAILS: There are three ponds: one in the upper left, one in the lower left, and one along the right side.

```c++
#include<iostream>
using namespace std;
int dx[]={0,0,1,-1,1,1,-1,-1};
int dy[]={1,-1,0,0,1,-1,1,-1};
char map[101][101];
int n,m,ans;
void dfs(int x,int y)
{
    if(x<=0||x>=n+1||y<=0||y>=m+1||map[x][y]=='.') return;
    map[x][y]='.';
    for(int i=0;i<8;i++)dfs(x+dx[i], y+dy[i]);
}
int main()
{
    cin>>n>>m;
    for(int i=1;i<=n;i++)
    for(int j=1;j<=m;j++)
    {
        cin>>map[i][j];
    }
    for(int i=1;i<=n;i++)
    {
        for(int j=1;j<=m;j++)
        {
            if(map[i][j]=='W')
            {
                dfs(i,j);
                ans++;
            }
        }
    }
    cout<<ans;
}

```

