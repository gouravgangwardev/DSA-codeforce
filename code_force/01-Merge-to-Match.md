# 1. Merge to Match

## Problem

You are given two arrays `a` and `b`.

You can repeatedly choose two elements `x` and `y` from `a`, delete them, and insert a value `z` such that:

`x <= z <= y`

Determine whether it is possible to transform `a` into `b`.

## Key Observation

Sort both arrays.

Every element of `b` must be produced by merging elements from `a`.

For every `b[i]`, there must be:

`a[i] < b[i] < a[n-m+i]`

Also, at least `2*m` elements are required in `a`.

Therefore:

- If `n < 2*m`, the answer is `NO`.
- Otherwise, check the above condition for every element of `b`.

## Algorithm

1. Sort `a`.
2. Sort `b`.
3. If `n < 2*m`, print `NO`.
4. For every `i` from `0` to `m-1`, check:
   `a[i] < b[i] < a[n-m+i]`
5. If every condition is satisfied, print `YES`.
6. Otherwise, print `NO`.

## Complexity

- Time: `O(n log n + m log m)`
- Space: `O(n + m)`

---

### solution
```

#include<bits/stdc++.h>
using namespace std;
int main(){
ios::sync_with_stdio(false);
cin.tie(nullptr);
int t;
cin>>t;
while(t--){
int n,m;
cin>>n>>m;
vector<long long>a(n),b(m);
for(auto&x:a)cin>>x;
for(auto&x:b)cin>>x;
sort(a.begin(),a.end());
sort(b.begin(),b.end());
if(n<2*m){
cout<<"NO\n";
continue;
}
bool ok=true;
for(int i=0;i<m;i++){
if(!(a[i]<b[i]&&b[i]<a[n-m+i])){
ok=false;
break;
}
}
cout<<(ok?"YES\n":"NO\n");
}
}
```
