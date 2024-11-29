# 杂项

## 缺生源

```c++
#include<bits/stdc++.h>
//#pragma GCC optimize("Ofast")
using namespace std;
template<typename T>
inline bool cmax(T&x,const T& y){return x<y?x=y,1:0;}
template<typename T>
inline bool cmin(T&x,const T& y){return y<x?x=y,1:0;}
typedef long long ll;
typedef pair<int,int> pii;
typedef pair<ll,ll> pll;
typedef vector<int> vi;
typedef vector<vector<int> > vii; 
const int mod=998244353;
inline void MOD(int&x){x-=mod,x+=x>>31&mod;}
inline void MOD(ll& x){x-=mod,x+=x>>63&mod;}
inline int add(int x,int y){MOD(x+=y);return x;}
inline int mul(int x,int y){return 1ll*x*y%mod;}
template<typename ... A>inline int mul(const int& x,const A&... p){return 1ll*x*mul(p...)%mod;}
inline ll ksm(ll a,ll p=mod-2){ll ans=1;for(;p;p>>=1,a=a*a%mod)if(p&1)ans=ans*a%mod;return ans;}
typedef long double LD;
const int MAXN=2e5+10;
```



## 圆方树

```c++
const intMAXN=2e6+10;
struct edge{int from,v;}e[MAXN*2];
int head[MAXN],cnt,n,m;
inline void addd(int u,int v)
{e[++cnt]=(edge){head[u],v},head[u]=cnt;}
#define tree(u) for(int i=head[u],v=e[i].v;i;i=e[i].from,v=e[i].v)
vector<int>g[MAXN];
int tt,tot,S;
int dfn[MAXN],low[MAXN],w[MAXN],s[MAXN],top;
inline void add(int u,int v){g[u].push_back(v),g[v].push_back(u);}
void tarjan(int u,int fa)
{
	dfn[u]=low[u]=++tt,s[++top]=u,S++;w[u]=-1;
	tree(u)
	{
		if(!dfn[v])
		{
			tarjan(v,u);
			if(low[v]>=dfn[u])
			{
				tot++;
				add(u,tot);w[tot]=1;
				int x=0;
				do
				{
					x=s[top--];w[tot]++;
					add(x,tot);
				}while(x!=v);
			}
			cmin(low[u],low[v]);
		}
		else if(v!=fa)cmin(low[u],dfn[v]);
	}
```

## 李超线段树

```c++
 struct node{LD k,b;inline LD val(ll x){return k*x+b;}};
node f[MAXN];
int id[MAXN];
const LD eps=1e-6;
inline bool better(int x,int y,int p)
{
	auto a=f[x].val(p),b=f[y].val(p);
	if(a-b>eps)return 1;
	else if(fabs(a-b)<eps&&x<y)return 1;
	else return 0;
}
struct xds
{
	xds *ls,*rs;
	int l,r;
	int id;
	xds(int L,int R):l(L),r(R),id(0)
	{
		if(L==R)return;
		int mid=(L+R)>>1;
		ls=new xds(L,mid),rs=new xds(mid+1,R);
	}
	void update(int ql,int qr,int u)
	{
		int mid=(l+r)>>1;
		if(ql<=l&&r<=qr)
		{
			if(!id)return id=u,void();
			if(l==r)
			{
				if(better(u,id,l))id=u;
				return;
			}
			if(f[u].val(mid)>f[id].val(mid))swap(u,id);
			if(better(u,id,l)) ls->update(ql,qr,u);
			else if(better(u,id,r)) rs->update(ql,qr,u);
			return;
		}
		if(ql<=mid) ls->update(ql,qr,u);
		if(qr >mid) rs->update(ql,qr,u);
		
	}
	int ask(int qq)
	{
		if(l==r)return id;
		int mid=(l+r)>>1;
		int ans=qq<=mid?ls->ask(qq):rs->ask(qq);
		if(better(id,ans,qq))ans=id;
		
		return ans;
	}
};
```

## 斯坦纳树

```c++
vector<pii>e[MAXN];
int F[1<<10][MAXN];
bool is[MAXN];
signed main()
{	
	ios::sync_with_stdio(0),cin.tie(0),cout.tie(0);
	int n,m,p;
	cin>>n>>m>>p;
	int u,v,w;
	for(int i=1;i<=m;i++)
	{
		cin>>u>>v>>w;
		e[u].emplace_back(v,w);
		e[v].emplace_back(u,w);
	}
	memset(F,0x3f,sizeof(F));
	for(int i=0;i<p;i++)
	{
		int x;cin>>x;
		F[1<<i][x]=0;
	}
	for(int s=1;s<(1<<p);s++)
	{
		for(int x=s&(s-1);x;x=s&(x-1))
			for(int i=1;i<=n;i++)
				cmin(F[s][i],F[x][i]+F[s^x][i]);
		queue<int>q;
		for(int i=1;i<=n;i++) q.push(i),is[i]=1;
		while(!q.empty())
		{
			int u=q.front();q.pop();is[u]=0;
			for(auto&p:e[u])if(cmin(F[s][p.X],F[s][u]+p.Y)) 
			{
				if(!is[p.X])q.push(p.X),is[p.X]=1;	
			}
		}
	}
	int ans=1e9;
	for(int i=1;i<=n;i++)cmin(ans,F[(1<<p)-1][i]);
	cout<<ans;
	return 0;
} 
```

## 虚树

```c++
const int MAXN=3e5+10;
vector<pii> e[MAXN];
vi g[MAXN];
const int K=20;
pii F[K+1][MAXN*2];
int tt;
int dfn[MAXN],d[MAXN];
int mmin[MAXN];
int h[MAXN*2];
void dfs(int u,int fa)
{
	dfn[u]=++tt;
	F[0][tt]={d[u],u};
	for(const pii&p:e[u])
	{
		int v=p.X;if(v!=fa)d[v]=d[u]+1,mmin[v]=min(mmin[u],p.Y),dfs(v,u),F[0][++tt]={d[u],u};
	}
	
}
inline int LCA(int x,int y)
{
	x=dfn[x],y=dfn[y];
	if(x>y)swap(x,y);int k=31-__builtin_clz(y-x+1);
	return min(F[k][x],F[k][y-(1<<k)+1]).Y;
}
int s[MAXN],top;
bool is[MAXN];
ll dp(int u)
{
	ll sum=0;
	for(const int&v:g[u])sum+=dp(v);
	if(u==1)
	{
		if(is[u])sum=mmin[u];
		g[u].clear(),is[u]=0;
		return sum;
	}
	if(is[u])sum=mmin[u];
	else cmin(sum,(ll)mmin[u]);
	g[u].clear(),is[u]=0;
	return sum;
}
signed main()
{	
	ios::sync_with_stdio(0),cin.tie(0),cout.tie(0);
	int n,m;
	cin>>n;
	int u,v,w;
	for(int i=1;i<n;i++)cin>>u>>v>>w,e[u].emplace_back(v,w),e[v].emplace_back(u,w);
	mmin[1]=1e9;
	dfs(1,0);
	for(int i=1;i<=K;i++)
		for(int j=1;j+(1<<i)-1<=tt;j++)
			F[i][j]=min(F[i-1][j],F[i-1][j+(1<<(i-1))]);
	cin>>m;
	while(m--)
	{
		int k;cin>>k;
		for(int i=1;i<=k;i++)cin>>h[i],is[h[i]]=1;
		sort(h+1,h+k+1,[&](const int&x,const int& y){return dfn[x]<dfn[y];});
		s[top=1]=h[1];
		for(int i=2;i<=k;i++)
		{
			int u=h[i];
			int l=LCA(u,s[top]);
			while(1)
			{
				if(d[l]>=d[s[top-1]])
				{
					if(l==s[top])break;
					g[l].push_back(s[top]);
					if(l!=s[top-1])s[top]=l;
					else top--;
					break;
				}
				else g[s[top-1]].push_back(s[top]),top--;
			}
			s[++top]=u;
		}
		while(top>1)g[s[top-1]].push_back(s[top]),top--;
		cout<<dp(s[1])<<'\n';
	}
	return 0;
} 
```

## 支配树

```c++
const int MAXN=65534+10;
const int K=16;
int F[K+1][MAXN],d[MAXN];
vi e[MAXN];
inline int LCA(int u,int v)
{
    if(!u||!v)return u|v;
    if(d[u]<d[v])swap(u,v);
    for(int i=K;~i;i--)if(d[F[i][u]]>=d[v])u=F[i][u];
    if(u==v)return u;
    for(int i=K;~i;i--)if(F[i][u]!=F[i][v])u=F[i][u],v=F[i][v];
    return F[0][u];
}
int in[MAXN],siz[MAXN];
int main()
{
	ios::sync_with_stdio(0),cin.tie(0),cout.tie(0);cout<<fixed<<setprecision(10);
	int n;cin>>n;
    for(int i=1,x;i<=n;i++)
    {
        cin>>x;
        while(x)
        {
            e[x].push_back(i);
            in[i]++;
            cin>>x;
        }
    }
    for(int i=1;i<=n;i++)if(!in[i])in[i]=1,e[n+1].push_back(i);
    vi q;q.push_back(n+1);int h=0;
    while(h<q.size())
    {
        int u=q[h++];
        d[u]=d[F[0][u]]+1;
        for(int i=1;i<=K;i++)F[i][u]=F[i-1][F[i-1][u]];
        for(auto&v:e[u])
        {
            F[0][v]=LCA(F[0][v],u);
            if(!--in[v])q.push_back(v);
        }
    }
    for(int i=q.size()-1;~i;i--)
    {
        siz[F[0][q[i]]]+=siz[q[i]]+1;
    }
    for(int i=1;i<=n;i++)cout<<siz[i]<<'\n';
	return 0;
} 
```

## LCA

```c++
 vi e[MAXN];
int dfn[MAXN],tt,F[K+1][MAXN];
void dfs(int u,int fa)
{
    F[0][dfn[u]=++tt]=fa;
    for(auto&v:e[u])if(v!=fa)dfs(v,u);
}
inline int Min(int u,int v){return dfn[u]<dfn[v]?u:v;}
inline int LCA(int u,int v)
{
    if(u==v)return u;
    u=dfn[u],v=dfn[v];
    if(u>v)swap(u,v);
    int k=__lg(v-u++);
    return Min(F[k][u],F[k][v-(1<<k)+1]);
}
```

## LCT

```c++
const int MAXN=3e5+10;
struct node
{
	int son[2],fa;
	bool rev;
	int w,mmin;
}t[MAXN];
#define ls t[p].son[0]
#define rs t[p].son[1]
inline bool isroot(int p){return p!=t[t[p].fa].son[0]&&p!=t[t[p].fa].son[1];}
const int INF=2e9;
int n,m,T;
inline void push_up(int p)
{
	t[p].mmin=p>n?t[p].w:INF;
	if(ls)cmin(t[p].mmin,t[ls].mmin);
	if(rs)cmin(t[p].mmin,t[rs].mmin);
}
inline void rev(int p){swap(ls,rs),t[p].rev^=1;}
inline void push_down(int p)
{
	if(t[p].rev)
	{
		if(ls)rev(ls);if(rs)rev(rs);
		t[p].rev=0;
	}
}
inline void rotate(int x)
{
	int y=t[x].fa,z=t[y].fa;
	if(!isroot(y)) t[z].son[y==t[z].son[1]]=x;t[x].fa=z;
	int r=x==t[y].son[1];z=t[x].son[r^1],t[x].son[r^1]=y;
	t[y].fa=x,t[y].son[r]=z,t[z].fa=y;push_up(y);
}
inline void splay(int p)
{
	static int s[MAXN],top;
	s[top=1]=p;int x=p;
	while(!isroot(p))s[++top]=p=t[p].fa;
	while(top)push_down(s[top--]);
	while(!isroot(x))
	{
		int y=t[x].fa;if(!isroot(y))rotate(((x==t[y].son[0])!=(y==t[t[y].fa].son[0]))?x:y);
		rotate(x);
	}push_up(x);
}
inline void access(int p)
{
	for(int i=0;p;p=t[i=p].fa)splay(p),rs=i,push_up(p);
}
inline void makeroot(int p){access(p),splay(p),rev(p);}
inline void link(int x,int y)
{
	makeroot(x),t[x].fa=y;
}
inline void cut(int x,int y)
{
	makeroot(x),access(y),splay(y);
	t[x].fa=0,t[y].son[0]=0;
}
```

