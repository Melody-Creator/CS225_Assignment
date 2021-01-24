## [题目传送门](http://www.lydsy.com/JudgeOnline/problem.php?id=2005)


----------
## Solution
首先将题目转换，容易发现一个点$(x,y)$的$k$为$gcd(x,y)-1$。

然后就变成了求
$$\sum_{i=1}^n \sum_{j=1}^m{2*gcd(i,j)-1}$$
它等于
$$(2*\sum_{i=1}^n \sum_{j=1}^m{gcd(i,j))-n*m}$$
于是我们只需求
$$\sum_{i=1}^n \sum_{j=1}^m{gcd(i,j)}$$

这是一个**莫比乌斯反演**的经典问题。

在解决这个问题之前，先来复习一下莫比乌斯反演的有关知识：

![这里写图片描述](https://img-blog.csdnimg.cn/img_convert/e018c3a5b9a7e49fbdf76d65d4096a62.png)

设$F(n)$和$f(n)$是两个定义在正整数域上的数论函数，且$F(n)=\sum_{d|n}f(d)$,那么有

$f(n)=\sum_{d|n}\mu(d)F({n\over d})$

$f(n)=\sum_{n|d}\mu({d\over n})F(d)$

如何证明？下面引用LYW大神的证明：
>![这里写图片描述](https://img-blog.csdnimg.cn/img_convert/b8d22cc7f4f0030549de3172493b1937.png)

 倒数第二步如何推到最后一步呢？我们知道狄利克雷卷积的元函数$e(n)=[n=1]$。而$\mu * I = e$，就是$\sum_{d|n}\mu(d)=e(n)$。
 
这是莫比乌斯函数的一个重要性质。
如何证明？

n=1明显成立。
其他情况把n分解质因数得到：
$n=p_1^{x_1}p_2^{x_2}p_3^{x_3}...p_k^{x_k}$
因为d是n的约数，那么d的所有质因数也只有p1到pk。
因为只要有一个质因数的指数大于1，μ(d)就是0，所以系数只可能是0或者1。
如果有r个质因子的指数是1，那么$\mu(d)=(-1)^r$
在k个质因子中选出r个，所以d有$C_k^r$个。
那么总的公式就是：
$$\sum_{d|n}\mu(d)=\sum_{r=0}^kC_k^r*(-1)^r$$
我们再乘上一个1，得到
$$\sum_{d|n}\mu(d)=\sum_{r=0}^kC_k^r*(-1)^r*1^{k-r}$$
观察最右边的公式，似乎和
$$(a+b)^n=\sum_{r=0}^nC_n^r*a^r*b^{n-r}$$
这个公式形式相同。
所以 
$$\sum_{d|n}\mu(d)=(-1+1)^k=0(n>1)$$

得到这个后，其实还有另一种证明前面的公式的方法，这里借用了狄利克雷卷积的一些变换(狄利克雷卷积的定义等省略)。

因为$F(n)=\sum_{d|n}f(d)=f*I$,$\mu * I = e$,所以$F * \mu = f * I * \mu = f * e = f$, 即$f(n)=\sum_{d|n}\mu(d)f({n \over d})$。

到这里，我们简要复习了一下莫比乌斯反演。

回到题目(假设m$<$n)，先枚举gcd，然后就是
$\sum_{g=1}^mg\sum_{i=1}^{\lfloor{n \over g}\rfloor}\sum_{j=1}^{\lfloor{m \over g}\rfloor}e(gcd(i,j))$
这里的做法类似于统计gcd为g的数对的数量。

然后开始变形：
原式=$\sum_{g=1}^mg\sum_{i=1}^{\lfloor{n \over g}\rfloor}\sum_{j=1}^{\lfloor{m \over g}\rfloor}e(gcd(i,j))$

$=\sum_{g=1}^mg\sum_{i=1}^{\lfloor{n \over g}\rfloor}\sum_{j=1}^{\lfloor{m \over g}\rfloor}\sum_{t|gcd(i,j)}\mu(t)$

$=\sum_{g=1}^mg\sum_{t=1}^{\lfloor{m \over g}\rfloor}\mu(t)\lfloor{n \over gt}\rfloor\lfloor{m \over gt}\rfloor$

令 $s=gt$，
$=\sum_{s=1}^m\lfloor{n \over s}\rfloor\lfloor{m \over s}\rfloor\sum_{t|s}\mu(t){s \over t}$

到这里，引入第二条式子$\phi(n)=\sum_{d|n}\mu(d){n \over d}$
这个证明就用$\sum_{d|n}\phi(d)=n(想想就知道)$,即$\phi * I = id$, 然后就
$$\mu * id = \mu * \phi * I
=\phi * e = \phi$$

接下来：
$$Ans=\sum_{s=1}^m\lfloor{n \over s}\rfloor \lfloor{m \over s}\rfloor\phi(s)$$

大道至简。

在此之前，时间复杂度为$O(m log m)$
后来就是$O(m)$

然而多组数据时，我们要加上**分块优化**。就是将下底函数进行分组。

当我们计算$\sum_{i=1}^n\lfloor{n \over i}\rfloor$时，可以在根号n的时间内搞定。

因为随着$i$长，$i<=\sqrt{n}$，有$\sqrt{n}$个取值，当i变大，$\lfloor{n \over i}\rfloor$也只有$\sqrt{n}$个取值，所以最多是$2\sqrt{n}$个取值。

然后对于$\lfloor{n \over i}\rfloor$与$\lfloor{m \over i}\rfloor$一对一组合，时间复杂度是$O(\sqrt{n}+\sqrt{m})$。处理多组询问就没问题了。

对于同样的$\lfloor{n \over i}\rfloor$与$\lfloor{m \over i}\rfloor$，我们预处理出$\phi$的前缀和，然后就可以一起算了。这就是算出每一块的开头结尾，同一块的一起算的的分块优化方法。

说了那么多，不如多熟记几条公式。以下必记：

$f(i)=\sum_{d=1}^{\lfloor{n\over i}\rfloor}g(d*i)$$=>g(i)=\sum_{d=1}^{\lfloor{n \over i}\rfloor} f(d*i)\mu(d)$

$f(x)=\sum_{x|d,d<=n}g(d)$$=>g(x)=\sum_{x|d,d<=n}f(d)\mu({d \over x})$

我们可以直接使用上面的公式直接跳到中间的某步运算过程：

首先，设$f(d)$表示有多少个$gcd(i,j)=d$，$g(d)$表示有多少个$gcd(i,j)$是$d$的倍数，$g(d)=\lfloor{n \over d}\rfloor\lfloor{m \over d}\rfloor$ 

因为$g(d)=\sum_{i=1}^{\lfloor{n\over d}\rfloor}f(d*i)$,

则根据公式得
$f(d)=\sum_{i=1}^{\lfloor{n \over d}\rfloor} g(d*i)\mu(i)$

即
$f(d)=\sum_{i=1}^{\lfloor{n \over d}\rfloor}\mu(i)\lfloor{n \over {di}}\rfloor\lfloor{m \over {di}}\rfloor$

而答案就等于
$\sum_{g=1}^ng\sum_{i=1}^{\lfloor{n \over d}\rfloor}\mu(i)\lfloor{n \over {di}}\rfloor\lfloor{m \over {di}}\rfloor(n<m)$

这里就到了“令$s=gt$”之前了，然后就可以推下去，记住几条常见的公式推得会更快。

好了，就到这里了(数学公式敲到我快吐血)。


----------
##Code

```c++
#include <iostream>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <cmath>
#include <algorithm>
#define N 100010

using namespace std;

typedef long long LL;
int n, m, cnt;
bool vis[N];
int prime[N];
LL phi[N], sum[N];

void Get_Phi(){
	vis[1] = true;
	phi[1] = 1ll;
	for(int i = 2; i <= n; i++){
	  if(!vis[i]){
	    prime[++cnt] = i;
	    phi[i] = i - 1;
	  }
	  for(int j = 1; j <= cnt && i * prime[j] <= n; j++){
	  	vis[i * prime[j]] = true;
	    if(i % prime[j] == 0){
		  phi[i * prime[j]] = phi[i] * prime[j];
		  break;
		}
		else  phi[i * prime[j]] = phi[i] * (prime[j] - 1);
	  }
	}
	sum[0] = 0ll;
	for(int i = 1; i <= n; i++)  sum[i] = sum[i-1] + phi[i];
}

LL Get_Ans(){
	LL res = 0ll;
	int last;
	for(int i = 1; i <= n; i = last+1){
	  last = min(n/(n/i), m/(m/i));
	  res += (LL)(n/i) * (LL)(m/i) * (sum[last] - sum[i-1]);
	}
	return res * 2 - (LL)n * (LL)m; 
}

int main(){
	
	
	freopen("bzoj2005.in", "r", stdin);
	freopen("bzoj2005.out", "w", stdout);
	
	
	scanf("%d%d", &n, &m);
	if(n > m)  swap(n, m);
	
	Get_Phi();
	
	printf("%lld\n", Get_Ans());
	
	return 0;
}

```


----------
**多希望我的人生是大梦一场，梦醒时分你依旧在我身旁。**
