# algorithm

acwing算法基础课的一些模板​，​笔记:smiley:

## 整数二分

```C++
bool check(int x) {/* ... */} // 检查x是否满足某种性质

// 区间[l, r]被划分成[ l, mid ]和[ mid + 1, r ]时使用：
int bsearch_1(int l, int r)
{
    while (l < r)
    {
        int mid = l + r >> 1;
        if (check(mid)) r = mid;    // check()判断mid是否满足性质
        else l = mid + 1;
    }
    return l;
}
// 区间[l, r]被划分成[l, mid - 1]和[mid, r]时使用：
int bsearch_2(int l, int r)
{
    while (l < r)
    {
        int mid = l + r + 1 >> 1;
        if (check(mid)) l = mid;
        else r = mid - 1;
    }
    return l;
}
```

## 高精度

* A + B

  ```C++
  #include <iostream>
  #include <vector>
  #include <string>
  
  using namespace std;
  
  const int N = 1e6 + 10;
  
  // C = A + B;
  vector<int> add(vector<int>& A, vector<int>&B) {
      vector<int> C;
      
      int t = 0;
      
      for (int i = 0; i < A.size() || i < B.size(); ++i) {
          if (i < A.size()) t += A[i];
          if (i < B.size()) t += B[i];
          C.push_back(t % 10);
          t /= 10;
      }
      if (t) C.push_back(1);
      return C;
  }
  
  int main() {
      string a, b;
      cin >> a >> b;
      
      vector<int> A, B;
      
      for (int i = a.size() - 1; i >= 0; --i) A.push_back(a[i] - '0');
      for (int i = b.size() - 1; i >= 0; --i) B.push_back(b[i] - '0');
      
      auto C = add(A, B);
      for (int i = C.size() - 1; i >= 0; --i) cout << C[i];
      return 0;
  }
  ```

* A - B

* A * b

* A / b

> A, B的位数超过10的6次方，一般到不了这么长、



## 双指针

利用某种性质将复杂度从$O(n^2)$降低到$O(n)$





## 单调栈

给定一个长度为 NN 的整数数列，输出每个数左边第一个比它小的数，如果不存在则输出 −1。

```c++
/*
	输入样例：
	5
	3 4 2 7 5
	输出样例：
	-1 3 -1 2 2
*/
#include <iostream>
#include <stack>

using namespace std;

const int N = 1e5 + 10;
int q[N];
stack<int> stk;
int main() {
    int n;
    cin >> n;
    for (int i = 0; i < n; ++i) {
        int x;
        cin >> x;
        while (!stk.empty() && stk.top() >= x) stk.pop();
        if(stk.empty()) cout << -1 << ' ';
        else cout << stk.top() << ' ';
        
        stk.push(x);
    }
    return 0;
}
```





## 背包问题

动态规划问题：

1. 状态表示$f(i, j)$；

   1. 集合：每个状态表示一个集合，$f(i, j)$表示哪一个集合
   2. 集合的属性：$f(i, j)$存的是一个数，表示集合的某一个属性，比如最大值，最小值，元素的数量

   比如背包问题，集合就是所有的选择方法，$f(i, j)$表示只从前$i$个物品里选，体积不超过$j$的集合，$f(i, j)$存的数就是这个集合中所有选择方法中的价值最大值。

2. 状态计算

   一般对应的是集合的划分，背包问题可以划分为两个子集，一个是选法包括第i个物品，一个是不包括第i个物品。对应$f(i, j)$划分为$f(i - 1, j)$和$f(i - 1, j - v_{i})$（曲线救国，这个子集中都包含第$i$个物品，去掉第$i$个物品，体积不超过$j - v_{i}$，计算属性时，加上第$i$个物品的价值即可，即$f(i - 1, j - v_{i}) + w_i$）。最后取这两个子集中的价值最大值。

### 0 1 背包

每件物品最多只能用一次

```C++
#include <iostream>

using namespace std;

const int N = 1010;

int n, m;
int v[N], w[N];
int f[N][N]; // 全局变量 初始化为0 不用初始化

int main() {
    cin >> n >> m;
    for (int i = 1; i <= n; ++i) cin >> v[i] >> w[i];
    for (int i = 1; i <= n; ++i) {
        for (int j = 0; j <= m; ++j) {
            f[i][j] = f[i - 1][j];
            if (j >= v[i]) f[i][j] = max(f[i][j], f[i - 1][j - v[i]] + w[i]);
        }
    }
    cout << f[n][m] << endl;
    return 0;
}


// 滚动数组优化版本
#include <iostream>

using namespace std;

const int N = 1010;

int n, m;
int v[N], w[N];
int f[N]; // 全局变量 初始化为0 不用初始化

int main() {
    cin >> n >> m;
    for (int i = 1; i <= n; ++i) cin >> v[i] >> w[i];
    
    for (int i = 1; i <= n; ++i) {
        for (int j = m; j >= v[i]; --j) {
            f[j] = max(f[j], f[j - v[i]] + w[i]);
        }
    }
    cout << f[m] << endl;
    return 0;
}

/************************************
注意一维动态规划的状态转移方程（ jj 采取逆序的方式）

假如枚举到：i = 3, j = 8, v[3] = 5, w[3] = 1

二维：dp[3][8] = max(dp[2][8], dp[2][3] + w[3])   此时的dp[2][8]和dp[2][3]都是上一轮的状态值

一维：dp[8] = max(dp[8], dp[3] + w[3])      我们要保证dp[8]和dp[3]都是上一轮的状态值

按照逆序的顺序，一维dp数组的更新顺序为：dp[8], dp[7], dp[6], ... , dp[3]

也就是说，在本轮更新的值，不会影响本轮中其他未更新的值！较小的index对应的状态是上一轮的状态值！

如果按照顺序进行更新，dp[3] = max(dp[3], dp[0] + w[0])，对dp[3]的状态进行了更新，那么在更新dp[8]时，用到的dp[3]
就不是上一轮的状态了，不满足动态规划的要求。
****************************************/
```



### 完全背包

每件物品可以用无限次

```C++
// 朴素做法，O(n^3)，超时
#include <iostream>

using namespace std;

const int N = 1e4 + 10;

int f[N][N];
int v[N], w[N];
int n, m;

int main(){
    cin >> n >> m;
    for (int i = 0; i < n; ++i) cin >> v[i] >> w[i];
    
    for (int i = 1; i <= n; ++i) 
        for (int j = 0; j <= m; ++j) 
            for (int k = 0; k * v[i] <= j; ++k)
                f[i][j] = max(f[i][j], f[i - 1][j - v[i] * k] + w[i] * k);
    
    cout << f[n][m] << endl;
    return 0;
}
```

优化：

```
f[i , j ] = max( f[i-1,j] , f[i-1,j-v]+w ,  f[i-1,j-2*v]+2*w , f[i-1,j-3*v]+3*w , .....)

f[i , j-v]= max(            f[i-1,j-v]   ,  f[i-1,j-2*v] + w , f[i-1,j-2*v]+2*w , .....)

由上两式，可得出如下递推关系： 
                        f[i][j]=max(f[i,j-v]+w , f[i-1][j]) 
```

```C++
#include <iostream>

using namespace std;

const int N = 1e4 + 10;

int f[N][N];
int v[N], w[N];
int n, m;

int main(){
    cin >> n >> m;
    for (int i = 1; i <= n; ++i) cin >> v[i] >> w[i];
    
    for (int i = 1; i <= n; ++i) 
        for (int j = 0; j <= m; ++j) {
            f[i][j] = f[i - 1][j];
            if (j >= v[i]) f[i][j] = max(f[i][j], f[i][j - v[i]] + w[i]);
            // 可再次优化，如0 1背包的优化
            // 但此时j从小到大遍历即可，因为用的就是第i个物品，而不是i-1
        }
    
    cout << f[n][m] << endl;
    return 0;
}

// 进一步优化
#include <iostream>

using namespace std;

const int N = 1e4 + 10;

int f[N];
int v[N], w[N];
int n, m;

int main(){
    cin >> n >> m;
    for (int i = 1; i <= n; ++i) cin >> v[i] >> w[i];
    
    for (int i = 1; i <= n; ++i) 
        for (int j = v[i]; j <= m; ++j) {
            f[j] = max(f[j], f[j - v[i]] + w[i]);
        }
    
    cout << f[m] << endl;
    return 0;
}
```



### 多重背包

每件物品能用的次数有限

```C++ 
// 朴素算法 O(n ^ 3)
#include <iostream>

using namespace std;

const int N = 110;

int v[N], w[N], s[N];
int n, m;
int f[N][N];

int main(){
    cin >> n >> m;
    for (int i = 1; i <= n; ++i) cin >> v[i] >> w[i] >> s[i];
    
    for (int i = 1; i <= n; ++i) {
        for (int j = 0; j <= m; ++j) {
            for (int k = 0; k <= s[i] && k * v[i] <= j; ++k) {
                f[i][j] = max(f[i][j], f[i - 1][j - k *v[i]] + k * w[i]);
            }
        }
    }
    cout << f[n][m] << endl;
    return 0;
}
```



