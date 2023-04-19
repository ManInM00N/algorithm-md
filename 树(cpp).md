## 树

### 		树状数组

​		适用于单点修改区间查询<u>或者</u>区间修改单点查询（差分法）。



![https://github.com/ManInM00N/algorithm-pic/blob/master/graph.gif]()

​												递推示意图↑（下图a数组对应上图C数组）

```c++
ll a[maxn];
ll lowbit(ll x){
    return x & (-x);
}//递推距离为概数的最低位  
eg.   (19)D = (10011)B  lowbit(19) = 1;
	->(20)D = (10100)B lowbit(20) = 4;
	->(24)D = (11000)B lowbit(24) = 8;
	->(32)D = (100000)B 到达父节点
void update(ll i,ll k){
    while (i<=n)
    {
        a[i] += k;
        i += lowbit(i);
    }
}
ll getnum(ll i){
    ll res = 0;
    while (i>0)
    {
        res += a[i];
        i -= lowbit(i);
    }
    return res;
}//getnum返回的值是[1,i]的值 根据题目需求再做额外计算
```



### 		线段树

​		适用于单点/区间修改，单点/区间查询(范围>树状数组) 

![](C:%5CUsers%5C666%5CDesktop%5C%E5%9B%BE%5Cgraph.gif)

​														建树顺序↑（灵魂画师来咯）

关键在Lazytag的迭代关系优化

```c++
#define ls p << 1
#define rs p << 1 | 1
using ll = long long;
const int maxn = 1e5+7;
ll k,arr[maxn],model=1000000007, x, y, n, m, z, cnt, sum;
struct tree
{
    ll l, r, add, sum, mul;
};
tree tr[maxn * 4+5];
void pushup(ll p){
    tr[p].sum = (tr[ls].sum + tr[rs].sum)%model;
    //刷新父亲节点的值
}
void pushdown(ll p){
    tr[ls].sum = tr[ls].sum * tr[p].mul + (tr[ls].r - tr[ls].l + 1) * tr[p].add;
    tr[ls].sum %= model;
    tr[rs].sum = tr[rs].sum * tr[p].mul + (tr[rs].r - tr[rs].l + 1) * tr[p].add;
    tr[rs].sum %= model;
    //数据迭代遵循先乘后加
    tr[ls].add = (tr[p].mul*tr[ls].add+tr[p].add)%model;
    tr[rs].add = (tr[p].mul*tr[rs].add+tr[p].add)%model;
    //Lazytag一同遵循先乘后加
    tr[ls].mul = tr[ls].mul*tr[p].mul%model;
    tr[rs].mul = tr[p].mul*tr[rs].mul%model;
    tr[p].add = 0;
    tr[p].mul = 1;
    //父节点Lazytag初始化
}
void build (ll p,ll l ,ll r){
    tr[p] = {l, r, 0, arr[l], 1};
    //初始化赋值很重要！！！对于Lazytag的初始化与迭代关系重大
    if ( l== r)
        return;
    ll mid = tr[p].l + tr[p].r >> 1;
    build(ls, l, mid);
    build(rs, mid + 1, r);
    pushup(p);
}
void updateadd(ll p,ll x,ll y ,ll k){

    if ( tr[p].l>=x && tr[p].r<=y)
    {
        tr[p].sum += (tr[p].r - tr[p].l + 1) * k;
        tr[p].sum %= model;
        tr[p].add += k;
        tr[p].add %= model;
        return;
    }
    pushdown(p);
    ll mid = tr[p].l + tr[p].r >> 1;
    if (mid>=x)
        updateadd(ls, x, y, k);
    if (mid<y)
        updateadd(rs, x, y, k);
    pushup(p);
}
void updatemul(ll p ,ll x ,ll y ,ll k){
    if ( tr[p].l>=x && tr[p].r<=y)
    {
        tr[p].sum =tr[p].sum*k;
        tr[p].sum %= model;
        tr[p].mul *= k;
        tr[p].mul %= model;
        tr[p].add *= k;
        tr[p].add %= model;
        return;
    }
    pushdown(p);
    ll mid = tr[p].l + tr[p].r >> 1;
    if (mid>=x)
        updatemul(ls, x, y, k);
    if (mid<y)
        updatemul(rs, x, y, k);
    pushup(p);
}
ll query(ll p,ll x, ll y){
     if (tr[p].l > y || tr[p].r<x)
        return 0;//可以不写
    if ( tr[p].l>=x && tr[p].r<=y)
    {
        return tr[p].sum;
    }
    ll sum = 0;
    ll mid = tr[p].l + tr[p].r >> 1;
    pushdown(p);
    if (mid>=x)
        sum+=query(ls, x, y);
    if (mid < y)
        sum += query(rs, x, y);
    sum %= model;
    return sum;
}
//结果对model取余可以不看，应题目做改动
```

