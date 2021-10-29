# 一、基础算法

## 1.快速排序

1.先确定边界x (数组中随机值)

2.**调整区间，使得左边小于x，右边大于x**

3.递归求解左右两边

```cpp
void quick_sort(int q[], int l, int r)
{
	if (l >= r)return; 

	int x = q[l], i = l - 1, j = r + 1;//确定边界
	while (i < j)//调整区间
	{
		do i++; while (q[i] < x);
		do j--; while (q[j] > x);
		if (i < j)swap(q[i], q[j]);
	}
	quick_sort(q, l, j);//递归求解左右两段
	quick_sort(q, j+1, r);//PS:此时用j则x不能取到q[r]，否则会发生死循环
}
```

### 1.1 题目 - -第k个数

**输入格式**

第一行包含两个整数 n 和 k。

第二行包含 n 个整数（所有整数均在 1∼1091∼109 范围内），表示整数数列。

**输出格式**

输出一个整数，表示数列的第 k 小数。

**数据范围**

1≤n≤100000,
1≤k≤n

**输入样例**：

```
5 3
2 4 1 5 3
```

**输出样例**：

```
3
```

### 1.2 题解

​	先对数组进行快排，然后记录下排序后边界的位置j或者i，计算出左半边的元素个数sl，与k相比，当k<=sl时，则第k大的数一定在左半边，只需要递归排序左半边即可，否则递归排序右半边，此时，第k大的数在右半边是第k-sl个数。

```cpp
#include<iostream>
using namespace std;

const int N = 100010;

int n,k;
int q[N];

int quick_sort(int l,int r,int k)
{
    if(l>=r)return q[l];
    
    int x=q[l],i=l-1,j=r+1;
    while(i<j)
    {
        while(q[++i]<x);//q[i]比x大时才会停下来
        while(q[--j]>x);
        if(i<j)swap(q[i],q[j]);
    }
    
    int sl=j-l+1;//计算左半边元素个数
    if(k<=sl)return quick_sort(l,j,k);
    return quick_sort(j+1,r,k-sl);//注意第K大的数在右半边是第k-sl个数
}

int main()
{
    cin>>n>>k;
    for(int i=0;i<n;i++)cin>>q[i];
    
    cout<<quick_sort(0,n-1,k)<<endl;
    return 0;
   
}
```



## 2.归并排序

1. [L,R] =>[L,mid]，[mid+1，R]

2. 递归排序[L,mid]，[mid+1，R]

3. **归并左右两个有序数组**

```cpp
void merge_sort(int q[], int l, int r)
{
	if (l >= r)return;
	int mid = (l + r) >> 1;//确定分界点

	merge_sort(q, l, mid), merge_sort(q, mid + 1, r);//递归排序左边和右边

	int k = 0, i = l, j = mid + 1;
	while (i <= mid && j <= r)//归并左右两个有序数组
	{
		if (q[i] <= q[j])tmp[k++] = q[i++];
		else tmp[k++] = q[j++];
	}
	while (i <= mid)tmp[k++] = q[i++];
	while (j <= r) tmp[k++] = q[j++];

	for (i = l, j = 0; i <= r; i++, j++)q[i] = tmp[j];
}
```

### 2.1 题目

给定一个长度为 n 的整数数列，请你计算数列中的逆序对的数量。

逆序对的定义如下：对于数列的第 ii 个和第 jj 个元素，如果满足 i<j 且 a[i]>a[j]，则其为一个逆序对；否则不是。

**输入格式**

第一行包含整数 n，表示数列的长度。

第二行包含 n 个整数，表示整个数列。

输出格式

输出一个整数，表示逆序对的个数。

**数据范围**

1≤n≤1000001≤n≤100000

**输入样例：**

```
6
2 3 4 5 6 1
```

**输出样例：**

```c++
5
```

### 2.2 题解

​		归并排序时，分成左右两部分，再进行最后一步合并数组时，当q[i]>q[j]的时候，因为q[i]之前的数都小于q[j]，所以此时的逆序对数为 mid - i + 1。



```cpp
#include <iostream>
#include <cstring>
#include <algorithm>
using namespace std;

typedef long long LL;
const int N = 1e5+10;
int q[N],tmp[N];
int n;

LL merge_sort(int l,int r)
{
    if(l>=r)return 0;
    
    int mid=l+r>>1;
    LL res=merge_sort(l,mid)+merge_sort(mid+1,r);
    
    
    //归并过程
    int k=0,i=l,j=mid+1;
    while(i<=mid&&j<=r)
    {
        if(q[i]<=q[j])tmp[k++]=q[i++];
        else
        {
            tmp[k++]=q[j++];
            res+=mid-i+1;//q[i]之前的数都小于q[j]，所以逆序数对为 mid-i+1
        }
    } 
    //扫尾过程
    while(i<=mid)tmp[k++]=q[i++];
    while(j<=r)tmp[k++]=q[j++];
    
    //物归原主
    for(int i=l,j=0;i<=r;i++,j++)q[i]=tmp[j];
    
    return res;
}

int main()
{

    cin>>n;
    for(int i=0;i<n;i++)
    {
        cin>>q[i];
    }
    cout<<merge_sort(0,n-1)<<endl;
    
    return 0;
}
```



## 3.二分查找

​	区间中存在某一个性质，使得区间左边不满足而右边满足，此时二分就可以找到这个性质的边界

**PS:有单调性一定可以二分，没有单调性可能可以二分**

```cpp
bool check(int x) {/* ... */} // 检查x是否满足某种性质

// 区间[l, r]被划分成[l, mid](r=mid)和[mid + 1, r]时使用：
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
// 区间[l, r]被划分成[l, mid - 1]和[mid, r](l=mid)时使用：
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

### 3.1 题目--数的三次方根

给定一个浮点数 nn，求它的三次方根。

**输入格式**

共一行，包含一个浮点数 nn。

**输出格式**

共一行，包含一个浮点数，表示问题的解。

注意，结果保留 66 位小数。

**数据范围**

−10000≤n≤10000−10000≤n≤10000

**输入样例**：

```
1000.00
```

**输出样例**：

```
10.000000
```

### 3.2 题解

数据跨度为-100000到100000，采用二分查找，令m=(l+r)/2，其二分条件是当m^3>=x时，此时x的三次方根在左半边负轴上，则区间更新为r=m;否则x映射在右半边正轴上，区间更新为l=m

```cpp
#include<iostream>
using namespace std;
int main()
{
    double x;
    cin>>x;
    
    double l=-100000,r=100000;
    while(r-l>1e-8)
    {
        double mid=(l+r)/2;
        if(mid*mid*mid>=x)r=mid;
        else l=mid;
    }
    
    printf("%lf",l);
    return 0; 
}
```



## 4.高精度运算

### 4.1加减法

用字符串接收输入，数组存放数据，两个数组元素进行运算。

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
#include<vector>
#include<string>
using namespace std;

vector<int> add(vector<int>&A, vector<int>&B)
{
	vector<int>c;

	int t = 0;
	for (int i = 0; i < A.size() || i < B.size(); i++)
	{
		if (i < A.size())t += A[i]; 
		if (i < B.size())t += B[i];
		c.push_back(t % 10);
		t /= 10;
	}
	if (t)c.push_back(1);//最高位时t有进位，则在最高位+1
	return c;
}


bool cmp(vector<int>& A, vector<int>& B)//判断AB大小
{
	if (A.size() != B.size()) return A.size() > B.size();
	for (int i = A.size() - 1; i >= 0; i--)
	{
		//最高位不相等
		if (A[i] != B[i]) return A[i] > B[i];
	}
	return true;
}
vector<int> sub(vector<int>& a, vector<int>& b)
{
	vector<int> result;
	int t = 0;//进位
	for (int i = 0; i < a.size() || t; i++)
	{
		t = a[i] - t;
		if (i < b.size()) t -= b[i];
		result.push_back((t + 10) % 10);//(t+10)%10 可以实现t>0和t<0的综合
		if (t < 0) t = 1;//如果t<0就进一位,否则不进
		else t = 0;
	}
	//清除前缀0
	while (result.size() > 1 && result.back() == 0) result.pop_back();
	return result;
}


int main()
{
	string a, b;
	vector<int>A, B;

	cin >> a >> b;//a='123456'

	for (int i = a.size()-1; i >= 0; i--)A.push_back(a[i] - '0');//A = [6,5,4,3,2,1]
	for (int i = b.size()-1; i >= 0; i--)B.push_back(b[i] - '0');

	////auto c = add(A,B);
	if (cmp(A, B))
	{
		auto c = sub(A, B);
		for (int i = c.size() - 1; i >= 0; i--)printf("%d", c[i]);
	}
	else
	{
		auto c = sub(B, A);
		printf("-");
		for (int i = c.size() - 1; i >= 0; i--)printf("%d", c[i]);
	}
	

	system("pause");
	return EXIT_SUCCESS;
}
```



### 4.2乘除法

乘法：

A为较大的数，B为较小的数，A中的每个元素与B相乘取个位并进位

```cpp
vector<int>mul(vector<int>& A, int b)
{
	vector<int>c;
	int t = 0;
	for (int i = 0; i <=  A.size() - 1||t; i++)
	{
		if(i<A.size())t += A[i] * b;
		c.push_back(t % 10);//只取最后一位
		t /= 10;//取进位数
	}
	return c;
}
void test()
{
	string a;
	int b;
	cin >> a >> b;
	vector<int>A;
	for (int i = a.size() - 1; i >= 0; i--)A.push_back(a[i] - '0');
	
	auto c = mul(A, b);
	for (int i = c.size() - 1; i >= 0; i--)printf("%d", c[i]);
}
int main()
{
    test();
    return 0;
}
```



除法：

注意数组顺序，重点在每一次的被除数r（也是余数，是个传出参数），用r除以b，得到的商进数组，余数继续做除法。

```cpp
vector<int>div(vector<int>&A,int b,int &r)
{
	vector<int>c;
	r = 0;
	for (int i = A.size() - 1; i >= 0; i--)
	{
		r = r * 10 + A[i];
		c.push_back(r / b);
		r %= b;
	}
	reverse(c.begin(), c.end());  
	while (c.size() > 1 && c.back() == 0) c.pop_back();
	return c;
}

void test01()
{
	string a;
	int b;
	int r = 0;
	cin >> a >> b;
	vector<int>A;
	for (int i = a.size() - 1; i >= 0; i--)A.push_back(a[i] - '0');

	auto c = div(A, b,r);
	for (int i = c.size() - 1; i >= 0; i--)printf("%d", c[i]);
	cout << endl;
	cout << r << endl;
}
int main()
{
    test01();
    return 0;
}
```

## 5. 前缀和

一维前缀和：

s[i]表示前i项的和，a[i]表示该项的值
$$
s[i]=s[i-1]+a[i]
$$

对于区间l,r的前缀和，s[r]-s[l-1]即可

```cpp
ios::sync_with_stdio(false);//提高cin的读取速度，但是不能再用scanf
```

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
using namespace std;

const int N = 100010;

int n, m;
int a[N], s[N];
int main()
{
	scanf("%d%d", &n, &m);
	for (int i = 1; i <= n; i++)scanf("%d", &a[i]);
	for (int i = 1; i <= n; i++)s[i]=s[i-1]+a[i];//前缀和初始化
	while (m--)
	{
		int l, r;
		scanf("%d%d", &l, &r);
		printf("%d", s[r] - s[l - 1]);//区间和的计算
	}
	system("pause");
	return EXIT_SUCCESS;
}
```

二维：

sum(L,R) = aL+aL+1+aL+2+……+aR=Sr - SL -1

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
using namespace std;

const int N = 1010;
int n, m,q;
int a[N][N], s[N][N];
void test02()
{
	scanf("%d%d%d",&n,&m,&q);
	for (int i = 1; i <= n; i++)
	{
		for (int j = 1; j <= m; j++)scanf("%d", &a[i][j]);
	}

	for (int i = 1; i <= n; i++)
	{
		for (int j = 1; j <= m; j++)
			s[i][j] = s[i - 1][j] + s[i][j - 1] - s[i - 1][j - 1] + a[i][j];//求前缀和
	}
	while(q--) 
	{
		int x1, y1, x2, y2;
		scanf("%d%d%d%d", &x1, &y1, &x2, &y2);

		printf("%d",s[x2][y2]-s[x1-1][y2]-s[x2][y1-1]+s[x1-1][y1-1] );//算子矩阵和
	}
}
int main()
{
	test02();
	system("pause");
	return EXIT_SUCCESS;
}
```

### 5.1 题目--前缀和

输入一个长度为 n 的整数序列。

接下来再输入 m 个询问，每个询问输入一对 l,r。

对于每个询问，输出原序列中从第 l 个数到第 r 个数的和。

**输入格式**

第一行包含两个整数 n 和 m。

第二行包含 n 个整数，表示整数数列。

接下来 m 行，每行包含两个整数 l和 r，表示一个询问的区间范围。

**输出格式**

共 m 行，每行输出一个询问的结果。

**数据范围**

1≤l≤r≤n,
1≤n,m≤100000,
−1000≤数列中元素的值≤1000−1000≤数列中元素的值≤1000

**输入样例**：

```
5 3
2 1 3 6 4
1 2
1 3
2 4
```

**输出样例**：

```
3
6
10
```

### 5.2 题解

1. 预处理前缀和数组

2. 用公式求区间和 s[r]-s[l-1]

```cpp
#include <iostream>
using namespace std;
const int N=100010;

int n,m;
int a[N],s[N];

int main()
{
    cin>>n>>m;
    for(int i=1;i<=n;i++)cin>>a[i];
    
    //求前缀和数组
    for(int i=1;i<=n;i++)s[i]=s[i-1]+a[i];//因为存在s[i-1]，所以i从1开始
    while(m--)
    {
        int l,r;
        cin>>l>>r;
        cout<<s[r]-s[l-1]<<endl;
    }
    return 0; 
}
```

### 5.3 题目---子矩阵的和

输入一个 nn 行 mm 列的整数矩阵，再输入 qq 个询问，每个询问包含四个整数 x1,y1,x2,y2x1,y1,x2,y2，表示一个子矩阵的左上角坐标和右下角坐标。

对于每个询问输出子矩阵中所有数的和。

**输入格式**

第一行包含三个整数 n，m，qn，m，q。

接下来 nn 行，每行包含 mm 个整数，表示整数矩阵。

接下来 qq 行，每行包含四个整数 x1,y1,x2,y2x1,y1,x2,y2，表示一组询问。

**输出格式**

共 qq 行，每行输出一个询问的结果。

**数据范围**

1≤n,m≤10001≤n,m≤1000,
1≤q≤2000001≤q≤200000,
1≤x1≤x2≤n1≤x1≤x2≤n,
1≤y1≤y2≤m1≤y1≤y2≤m,
−1000≤矩阵内元素的值≤1000−1000≤矩阵内元素的值≤1000

**输入样例**：

```
3 4 3
1 7 2 4
3 6 2 8
2 1 2 3
1 1 2 2
2 1 3 4
1 3 3 4
```

**输出样例**：

```
17
27
21
```

### 5.4 题解

s[i] [j] = s [ i - 1] [j] + s[i] [j - 1] - s[i - 1] [j - 1] + a[i] [j];//求前缀和

s[x2] [y2]-s[x1-1] [y2]-s[x2] [y1-1]+s[x1-1] [y1-1] );//算子矩阵和

```cpp
#include<iostream>
using namespace std;
const int N =1010;
int n ,m ,q;
int a[N][N],s[N][N];

int main()
{
    scanf("%d%d%d",&n,&m,&q);
    for(int i =1;i<=n;i++)
        for(int j=1;j<=m;j++)scanf("%d",&a[i][j]);
    
    
    //求前缀和
    for(int i=1;i<=n;i++)
        for(int j=1;j<=m;j++)
        {
            s[i][j]=s[i-1][j]+s[i][j-1]-s[i-1][j-1]+a[i][j];
        }
    
    while(q--)
    {
       int x1, y1, x2, y2;
		scanf("%d%d%d%d", &x1, &y1, &x2, &y2);
        
        //计算子矩阵的和
        printf("%d\n",s[x2][y2]-s[x1-1][y2]-s[x2][y1-1]+s[x1-1][y1-1]);
    }
    
    return 0;
    
}
```

## 6. 差分

**一维**： 给定a[1],a[2], ....a[n]，构造差分数组b[N],使得a[i]=b[1]+b[2]+....+b[i]；此时a数组就是b的前缀和，b是a的差分；

​	**核心操作  ：将a[L~R]全部加上C，等价于在b[L]+=C，b[R+1]-=C**

**二维**：给定原矩阵a[i, j]，构造差分矩阵b[i, j]，使得a[] []是b[] []的二位前缀和。

​	**核心操作  ： 	给以(x1,y1)为左上角，(x2,y2)为右下角的子矩阵中的所有数a[i, j]，加上C，等价于 s[x1,y1]+=C；s[x1,y2+1] -=C；s[x2+1,y1] -=C；s[x2+1,y2+1]+=C。**

对差分数组的影响：

### 6.1 题目--差分

输入一个长度为 nn 的整数序列。

接下来输入 mm 个操作，每个操作包含三个整数 l,r,cl,r,c，表示将序列中 [l,r][l,r] 之间的每个数加上 cc。

请你输出进行完所有操作后的序列。

**输入格式**

第一行包含两个整数 nn 和 mm。

第二行包含 nn 个整数，表示整数序列。

接下来 mm 行，每行包含三个整数 l，r，cl，r，c，表示一个操作。

**输出格式**

共一行，包含 nn 个整数，表示最终序列。

**数据范围**

1≤n,m≤1000001≤n,m≤100000,
1≤l≤r≤n1≤l≤r≤n,
−1000≤c≤1000−1000≤c≤1000,
−1000≤整数序列中元素的值≤1000−1000≤整数序列中元素的值≤1000

**输入样例**：

```
6 3
1 2 2 1 2 1
1 3 1
3 5 1
1 6 1
```

**输出样例**：

```
3 4 5 3 4 2
```

### 6.2 题解

难点在差分数组的初始化

```cpp
#include<iostream>
using namespace std;
const int N=100010;

int a[N],b[N];
int n,m;

void insert(int l,int r,int c)
{
    b[l]+=c;
    b[r+1]-=c;
}

int main()
{
    cin>>n>>m;
    for(int i=1;i<=n;i++)cin>>a[i];
    
	for(int i=1;i<=n;i++)insert(i,i,a[i]);//初始化差分数组b[]，原理是b[i+1]=a[i+1]-a[i],且b[1]=a[1];
    
    while(m--)
    {
        int l,r,c;
        cin>>l>>r>>c;
        insert(l,r,c);
    }
    
    for(int i=1;i<=n;i++)a[i]=a[i-1]+b[i];
    
    for(int i=1;i<=n;i++)printf("%d ",a[i]);
    
    return  0;
    
}

```

### 6.3. 题目 --差分矩阵

输入一个 nn 行 mm 列的整数矩阵，再输入 qq 个操作，每个操作包含五个整数 x1,y1,x2,y2,cx1,y1,x2,y2,c，其中 (x1,y1)(x1,y1) 和 (x2,y2)(x2,y2) 表示一个子矩阵的左上角坐标和右下角坐标。

每个操作都要将选中的子矩阵中的每个元素的值加上 cc。

请你将进行完所有操作后的矩阵输出。

**输入格式**

第一行包含整数 n,m,qn,m,q。

接下来 nn 行，每行包含 mm 个整数，表示整数矩阵。

接下来 qq 行，每行包含 55 个整数 x1,y1,x2,y2,cx1,y1,x2,y2,c，表示一个操作。

**输出格式**

共 nn 行，每行 mm 个整数，表示所有操作进行完毕后的最终矩阵。

**数据范围**

1≤n,m≤10001≤n,m≤1000,
1≤q≤1000001≤q≤100000,
1≤x1≤x2≤n1≤x1≤x2≤n,
1≤y1≤y2≤m1≤y1≤y2≤m,
−1000≤c≤1000−1000≤c≤1000,
−1000≤矩阵内元素的值≤1000−1000≤矩阵内元素的值≤1000

**输入样例**：

```
3 4 3
1 2 2 1
3 2 2 1
1 1 1 1
1 1 2 2 1
1 3 2 3 2
3 1 3 4 1
```

**输出样例**：

```
2 3 4 1
4 3 4 1
2 2 2 2
```

### 6.4 题解

**s[x1,y1]+=C；s[x1,y2+1] -=C；s[x2+1,y1] -=C；s[x2+1,y2+1]+=C**



```cpp
#include<iostream>
using namespace std;
const int N=1010;

int a[N][N],b[N][N];
int n,m,q;

void insert(int x1,int y1,int x2,int y2,int c)
{
    b[x1][y1]+=c;
    b[x1][y2+1]-=c;
    b[x2+1][y1]-=c;
    b[x2+1][y2+1]+=c;  
}
int main()
{
    cin>>n>>m>>q;
    for(int i=1;i<=n;i++)
        for(int j=1;j<=m;j++)cin>>a[i][j];
   
     for(int i=1;i<=n;i++)
        for(int j=1;j<=m;j++)
            insert(i,j,i,j,a[i][j]);//初始化差分数组
    
    while(q--)
    {
        int x1,y1,x2,y2,c;
        cin>>x1>>y1>>x2>>y2>>c;
        insert(x1,y1,x2,y2,c);
    }
    
    //求加了之后的数组
   for(int i=1;i<=n;i++)
        for(int j=1;j<=m;j++)
            a[i][j]=a[i-1][j]+a[i][j-1]-a[i-1][j-1]+b[i][j];//求前缀和
    
    for(int i=1;i<=n;i++)
    {
        for(int j=1;j<=m;j++)cout<<a[i][j]<<' ';
        cout<<endl;
    }
    return 0;
}
```

## 7.双指针

```cpp
for (int i = 0, j = 0; i < n; i ++ )
{
    while (j < i && check(i, j)) j ++ ;

    // 具体问题的逻辑
}
常见问题分类：
    (1) 对于一个序列，用两个指针维护一段区间
    (2) 对于两个序列，维护某种次序，比如归并排序中合并两个有序序列的操作
```

### 1. 例题

给定一个长度为 nn 的整数序列，请找出最长的不包含重复的数的连续区间，输出它的长度。

**输入格式**

第一行包含整数 nn。

第二行包含 nn 个整数（均在 0∼1050∼105 范围内），表示整数序列。

**输出格式**

共一行，包含一个整数，表示最长的不包含重复的数的连续区间的长度。

**数据范围**

1≤n≤1051≤n≤105

**输入样例**：

```
5
1 2 2 3 5
```

**输出样例**：

```
3
```

双指针解法：

i用做枚举，遍历整个数组，j表示左边最远的不重复的位置，i和j之间不存在重复的数据

i先往前走，i每前进一步，都判断j与i之间是否有重复（另外开辟一个数组，记录对应数组元素出现的次数--哈希表），如果没有则i++;如果有重复，因为i是一步一步前进 ，则出现重复时，所重复的数一定是a[i]。结果返回res=max(res,i-j+1)；



```cpp
#include<iostream>
using  namespace std;
const int N=100010;
int n;
int a[N],s[N];
int main()
{
    cin>>n;
    for(int i =0;i<n;i++)cin>>a[i];
    
    
    int res=0;
    for(int i=0,j=0;i<n;i++)
    {
        s[a[i]]++;
        while(s[a[i]]>1)
        {
            s[a[j]]--;//出现重复时，将j~i之间的计数还原，s[a[i]]会作为下一个计数开始位置，所以s[a[i]]为1；
            j++; 
        }
        res=max(res,i-j+1);
    }
    cout<<res<<endl;
    return 0;	
    
}



```



## 8.位运算

x=1010

源码：000……1010

反码：111……0101

补码：111……0110（反码+1）

1.n的二进制表示中，第k位是几？

​	1.先把第k位移到最后一位 -----n>>k;

​	2.看个位是几 ----x&1；

​	总结：n>>k&1; // &--与操作

2.lowbit(x)返回x的最后一位1.(核心操作： x & -x)

如：x=1010，则lowbit(x)=10；x=1011000，则lowbit(x)=1000;

原因： -x的补码为 x取反+1，所以 x&-x = x&(~x+1),

举例：x=10101000，~x=01010111,~x+1=01011000

则x&(-x+1) = 00001000.

### 1. 例题---二进制中1的个数                                                        

给定一个长度为 n 的数列，请你求出数列中每个数的二进制表示中 11 的个数。

**输入格式**

第一行包含整数 n。

第二行包含 n 个整数，表示整个数列。

**输出格式**

共一行，包含 n 个整数，其中的第 i 个数表示数列中的第 i个数的二进制表示中 11 的个数。

**数据范围**

1≤n≤1000001≤n≤100000,
0≤数列中元素的值≤1090≤数列中元素的值≤109

**输入样例**：

```
5
1 2 3 4 5
```

**输出样例**：

```
1 1 2 1 2
```

### 2. 题解

```cpp
#include<iostream>
using namespace std;
const int N=100010;
int a[N];

int lowbit(int x)
{
    return x & -x;
}


int main()
{
    int n;
    cin>>n;
    while(n--)
    {
        int x;
        cin>>x;
    
        int res=0;
        while(x)x-=lowbit(x)，res++;//每次减去x的最后一位1，因为lowbit返回的是最后一位1
        cout<<res<<endl;
    }
    return 0;
    
}
```

## 9. 离散化

模板：

```cpp
vector<int> alls; // 存储所有待离散化的值
sort(alls.begin(), alls.end()); // 将所有值排序
alls.erase(unique(alls.begin(), alls.end()), alls.end());   // 去掉重复元素

// 二分求出x对应的离散化的值
int find(int x) // 找到第一个大于等于x的位置
{
    int l = 0, r = alls.size() - 1;
    while (l < r)
    {
        int mid = l + r >> 1;
        if (alls[mid] >= x) r = mid;
        else l = mid + 1;
    }
    return r + 1; // 返回找到数的位置下标，加1是映射到1, 2, ...n，不加则映射到0，1，2
}
```

### 9.1 例题---区间和

假定有一个无限长的数轴，数轴上每个坐标上的数都是 0。

现在，我们首先进行 n 次操作，每次操作将某一位置 x 上的数加 c。

接下来，进行 m 次询问，每个询问包含两个整数 l 和 r，你需要求出在区间 [l,r][l,r] 之间的所有数的和。

**输入格式**

第一行包含两个整数 n 和 m。

接下来 n 行，每行包含两个整数 x 和 c。

再接下来 m 行，每行包含两个整数 l 和 r。

**输出格式**

共 m 行，每行输出一个询问中所求的区间内数字和。

**数据范围**

−109≤x≤109,
1≤n,m≤10^5,
−10^9≤l≤r≤10^9,
−10000≤c≤10000

**输入样例**：

```
3 3
1 2
3 6
7 5
1 3
4 6
7 8
```

**输出样例**：

```
8
0
5
```

### 9.2 题解

先把要插入数的位置下标x，以及要查询数的左右区间下标l，r加入到离散化数组中，该数组存放的是待映射的下标；然后对该离散化数组进行去重，并通过二分对需要添加和查询的数组下标完成映射，此时对原数列求区间和，等价于对映射之后的下标使用前缀和公式即可；

还有add操作和query操作，add是给x位置加上c，query是计算l，r之间的区间和；

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

typedef pair<int, int> PII;

const int N = 300010;

int n, m;
int a[N], s[N];

vector<int> alls;
vector<PII> add, query;

int find(int x)
{
    int l = 0, r = alls.size() - 1;
    while (l < r)
    {
        int mid = l + r >> 1;
        if (alls[mid] >= x) r = mid;
        else l = mid + 1;
    }
    return r + 1;
}

vector<int>::iterator unique(vector<int> &a)
{
    int j = 0;
    for (int i = 0; i < a.size(); i ++ )
        if (!i || a[i] != a[i - 1])
            a[j ++ ] = a[i];
    // a[0] ~ a[j - 1] 所有a中不重复的数

    return a.begin() + j;
}

int main()
{
    cin >> n >> m;
    for (int i = 0; i < n; i ++ )
    {
        int x, c;
        cin >> x >> c;
        add.push_back({x, c});

        alls.push_back(x);
    }

    for (int i = 0; i < m; i ++ )
    {
        int l, r;
        cin >> l >> r;
        query.push_back({l, r});

        alls.push_back(l);
        alls.push_back(r);
    }

    // 去重
    sort(alls.begin(), alls.end());
    alls.erase(unique(alls), alls.end());

    // 处理插入
    for (auto item : add)
    {
        int x = find(item.first);
        a[x] += item.second;
    }

    // 预处理前缀和
    for (int i = 1; i <= alls.size(); i ++ ) s[i] = s[i - 1] + a[i];

    // 处理询问
    for (auto item : query)
    {
        int l = find(item.first), r = find(item.second);
        cout << s[r] - s[l - 1] << endl;
    }

    return 0;
}
```

## 10.区间合并





### 1.例题--区间合并

给定 n 个区间 [li,ri] ，要求合并所有有交集的区间。

注意如果在端点处相交，也算有交集。

输出合并完成后的区间个数。

例如：[1,3] 和 [2,6]可以合并为一个区间 [1,6]。

**输入格式**

第一行包含整数 n。

接下来 n 行，每行包含两个整数 l 和 r。

**输出格式**

共一行，包含一个整数，表示合并区间完成后的区间个数。

**数据范围**

1≤n≤100000,
−109≤li≤ri≤109

**输入样例**：

```
5
1 2
2 4
5 6
7 8
7 9
```

**输出样例**：

```
3
```

### 2.题解

1.按区间左端点排序；

2.遍历区间，每次维护一个区间，与下一个区间作比较，确定是合并还是不变，如果下一个区间的左端点比之前所有区间都大，则把下一个区间变为维护区间，继续往下遍历；

```cpp
#include<iostream>
#include<algorithm>
#include<vector>
using namespace std;

const int N=100010;
typedef pair<int,int>PII;

int n;
vector<PII> segs;

void merge(vector<PII>&segs)
{
    vector<PII> res;//答案数组
    
    sort(segs.begin(),segs.end());//区间排序，默认先以左端点排序
    int st=-2e9,ed=2e9;
    
    for(auto seg:segs)
    {
        if(ed<seg.first)//维护区间右端点ed小于下一区间左端点，两区间无交集
        {
            if(st!=-2e9)res.push_back({st,ed});
            st=seg.first,ed=seg.second;
        }
        
       else//有交集
       {
           ed=max(ed,seg.second);
       }
        if(st!=-2e9)res.push_back({st,ed});//防止输入区间为空
        segs=res;
    }
    
    
}


int main()
{
    cin>>n;
    for(int i =0;i<=n;i++)
    {
        int l,r;
        cin>>l>>r;
        segs.push_back({l,r});
        
        merge(segs);
        cout<<segs.size()<<endl;
        return 0;
        
    }
    
}

```

