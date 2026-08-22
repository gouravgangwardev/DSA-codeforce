## ICPC 2026 Online Challenge 1 powered by Huawei  

### Question
```
This is an interactive scheduling problem. The "interactor" sends events as they happen; after each event group, you choose ready tasks to start. You never predict future requests. For a fixed test it is deterministic and non-adaptive: the same legal responses produce the same events and score.

IMPORTANT: YES, THERE WILL BE A SYSTEM TEST AFTER THE CHALLENGE.

No AI Background Is Needed

Treat this as jobs moving through computers; no text-generation knowledge is needed. A token is one output unit. Commands P PRE/P PROC/P POST prepare a request once; D PRE/D PROC/D POST produce one token. Both follow local → remote → local.


| Term                  | Meaning                                                       |
| :-------------------- | :------------------------------------------------------------ |
| edge / local          | the one computer beside users                                 |
| cloud / remote        | one of K worker computers                                     |
| token                 | one output unit                                               |
| prefill / input stage | one-time input preparation                                    |
| decode / output step  | repeated work producing one token                             |
| uplink / UP           | local-to-remote transfer                                      |
| downlink / DOWN       | remote-to-local transfer                                      |
| FIFO queue            | first transfer queued is the first transfer completed         |
| batch / group         | requests combined in one output task                          |
| chunk / piece         | a consecutive range of input-stage parts                      |
| activations           | transferred request data; its size is defined by the protocol |
| model layers / parts  | the numbered input-stage range [0,num_layers)                 |


The Problem in One Minute

One local computer and several remote computers handle requests. Each request first prepares its input by making this trip:

local computer → remote computer → local computer.

The same trip is then repeated once per token. The first trip is the input stage; each later trip is an output step. Transfers are automatic; you choose ready tasks for free computers.

The score rewards output rate and short waits. Time to decode ready (TDR) ends when the first output step can begin, before a token is produced. Time per output token (TPOT) is the mean gap between consecutive tokens.

Challenge Format

Two worked examples appear at the end of the statement. Test 1 is the public worked Example 1, tests 2–22 are hidden preliminary tests, and finals use a separate frozen set. Per-test scores are reported on the 0–1000 scale defined below, and the contest system aggregates them according to the contest rules; no hacks are used.

Guide: blue is local, green is the shared two-way link, and orange is remote; computation and transfer may overlap.

What You Control

You choose:

- which legal task to start on each free computer;
- the remote computer assigned to a request when scheduling its P PRE;
- how to split an input-stage P PROC into ranges of numbered parts; and
- which ready requests to combine into each output group.

Transfers are automatic. For a first solution, use one full input-stage piece and groups of size 1.


System Model

- There is one local computer and K identical remote computers, numbered 0 through K−1 and written C0, C1, …. They work independently and may run while data moves.
- There are R requests in total, but R is not announced. Request ids are i=0,1,…,R−1 in arrival order. The arrival time of request i is the timestamp of its ARR event. That event reveals its input length Lin[i]; its outp
```
---
### code {language C++}

```
#include <bits/stdc++.h>
using namespace std;
 
struct R {
    int a, b;
    int c = -1;
    int d = 0;
    bool e = false;
    double s = 0;
 
    R(int a, int b) {
        this->a = a;
        this->b = b;
    }};
int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int K, a, b;
    double S, l, w;
    cin >> K >> S >> l >> w >> a >> b;
    double s1, s2, t1, t2, d1, w1, w2;
    cin >> s1 >> s2 >> t1 >> t2 >> d1 >> w1 >> w2;
 
    const int A = 0, B = 1, C = 2;
    const int D = 3, E = 4, F = 5;
 
    int n;
    cin >> n;
 
    vector<pair<int, double>> v[6];
 
    for (int i = 0; i < n; i++) {
        int x;
        cin >> x;
 
        for (int j = 0; j < 6; j++) {
            double y;
            cin >> y;
 
            if (y >= 0)
                v[j].push_back({x, y});
        }
    }
 
    for (int i = 0; i < 6; i++)
        sort(v[i].begin(), v[i].end());
 
    auto f = [&](int x, double y) {
        auto &q = v[x];
        int n = q.size();
 
        if (y <= q[0].first)
            return q[0].second;
 
        if (y >= q[n - 1].first)
            return q[n - 1].second;
 
        int l = 0, r = n - 1;
 
        while (r - l > 1) {
            int m = (l + r) / 2;
 
            if (q[m].first <= y)
                l = m;
            else
                r = m;
        }
 
        double x1 = q[l].first;
        double y1 = q[l].second;
        double x2 = q[r].first;
        double y2 = q[r].second;
 
        return y1 + (y2 - y1) * (y - x1) / (x2 - x1);
    };
 
    auto g = [&](double x) {
        return l + 8.0 * x * a / (w * 1e6);
    };
 
    string z;
    getline(cin, z);
 
    vector<R> q;
    q.reserve(4096);
 
    bool my = false;
    vector<bool> busy(K, false);
 
    double now = 0;
 
    vector<double> rf(K, 0);
    double lf = 0;
 
    vector<int> cnt(K, 0);
 
    set<int> p1, p5, p6, p11;
    vector<set<int>> p3(K), p9(K);
 
    auto st = [&](R &r, int x) {
        switch (r.d) {
            case 1: p1.erase(r.a); break;
            case 5: p5.erase(r.a); break;
            case 6: p6.erase(r.a); break;
            case 11: p11.erase(r.a); break;
            case 3: p3[r.c].erase(r.a); break;
            case 9: p9[r.c].erase(r.a); break;
        }
 
        r.d = x;
 
        switch (x) {
            case 1:
                p1.insert(r.a);
                r.s = now;
                break;
 
            case 5:
                p5.insert(r.a);
                r.s = now;
                break;
 
            case 6:
                p6.insert(r.a);
                r.s = now;
                break;
 
            case 11:
                p11.insert(r.a);
                r.s = now;
                break;
 
            case 3:
                p3[r.c].insert(r.a);
                r.s = now;
                break;
 
            case 9:
                p9[r.c].insert(r.a);
                r.s = now;
                break;
        }
    };
 
    auto old = [&](const set<int> &x) {
        int z = -1;
        double y = 1e300;
 
        for (int i : x) {
            if (q[i].s < y) {
                y = q[i].s;
                z = i;
            }
        }
 
        return z;
    };
 
    auto batch = [&](const set<int> &x, int c) {
        int n = x.size();
 
        int k = 1;
        double best = S + f(c, 1);
 
        for (int i = 2; i <= n; i++) {
            double cur = (S + f(c, i)) / i;
 
            if (cur <= best / k + 1e-9) {
                best = S + f(c, i);
                k = i;
            }
        }
 
        vector<int> a(x.begin(), x.end());
 
        sort(a.begin(), a.end(), [&](int x, int y) {
            return q[x].s < q[y].s;
        });
 
        if ((int)a.size() > k)
            a.resize(k);
 
        return a;
    };
 
    while (true) {
        getline(cin, z);
 
        if (z == "END")
            break;
 
        if (z.empty())
            continue;
 
        now = stod(z);
 
        int n;
        getline(cin, z);
        n = stoi(z);
 
        vector<string> frame(n);
 
        for (int i = 0; i < n; i++)
            getline(cin, frame[i]);
 
        for (string &x : frame) {
            stringstream ss(x);
 
            string type;
            ss >> type;
 
            if (type == "ARR") {
                int id, len;
                ss >> id >> len;
 
                q.emplace_back(id, len);
                st(q[id], 1);
            }
 
            else if (type == "TDN") {
                string server, p, step;
                ss >> server >> p >> step;
 
                if (server == "E") {
                    my = false;
                    lf = now;
                }
                else {
                    int k = stoi(server.substr(1));
                    busy[k] = false;
                    rf[k] = now;
                }
 
                if (p == "P") {
                    if (step == "PRE") {
                        int r, id;
                        ss >> r >> id;
                        st(q[id], 2);
                    }
 
                    else if (step == "PROC") {
                        int x, y, r, id;
                        ss >> x >> y >> r >> id;
                        st(q[id], 4);
                    }
 
                    else if (step == "POST") {
                        int r, id;
                        ss >> r >> id;
                        st(q[id], 6);
                    }
                }
 
                else if (p == "D") {
                    int r, n;
                    ss >> r >> n;
 
                    vector<int> ids(n);
 
                    for (int i = 0; i < n; i++)
                        ss >> ids[i];
 
                    if (step == "PRE") {
                        for (int id : ids)
                            st(q[id], 8);
                    }
 
                    else if (step == "PROC") {
                        for (int id : ids)
                            st(q[id], 10);
                    }
 
                    else if (step == "POST") {
                        for (int id : ids) {
                            if (!q[id].e)
                                st(q[id], 6);
                        }
                    }
                }
            }
 
            else if (type == "XDN") {
                string dir, rem, size, step;
                int n;
 
                ss >> dir >> rem >> size >> step >> n;
 
                for (int i = 0; i < n; i++) {
                    int id;
                    ss >> id;
 
                    if (step == "PRE") {
                        if (dir == "UP")
                            st(q[id], 3);
                        else
                            st(q[id], 5);
                    }
                    else {
                        if (dir == "UP")
                            st(q[id], 9);
                        else
                            st(q[id], 11);
                    }
                }
            }
 
            else if (type == "FIN") {
                int id;
                ss >> id;
 
                q[id].e = true;
 
                if (q[id].c >= 0)
                    cnt[q[id].c]--;
 
                st(q[id], 12);
            }
        }
 
        vector<string> ans;
 
        bool idle = false;
 
        for (int k = 0; k < K; k++) {
            if (!busy[k] && p3[k].empty() && p9[k].empty()) {
                idle = true;
                break;
            }
        }
 
        auto estimate = [&](int k, double len) {
            double x = g(len);
 
            double ps = max(rf[k], now + x);
            double pe = ps + f(B, len);
 
            double post = pe + x;
 
            double pd = max(lf, post) + f(C, len);
 
            double dp = f(D, 1);
            double dx = g(1);
 
            double ds = max(rf[k], pd + dp + dx);
            double de = f(E, cnt[k] + 1.0);
 
            return ds + de + dx + f(F, 1);
        };
 
        auto admit = [&](int id) {
            int best = 0;
            double val = estimate(0, q[id].b);
 
            for (int k = 1; k < K; k++) {
                double x = estimate(k, q[id].b);
 
                if (x < val) {
                    val = x;
                    best = k;
                }
            }
 
            q[id].c = best;
            cnt[best]++;
 
            double x = g(q[id].b);
 
            rf[best] = max(rf[best], now + x)
                     + f(B, q[id].b);
 
            st(q[id], 2);
 
            my = true;
 
            lf = max(lf, now)
               + f(A, q[id].b);
 
            ans.push_back(
                "E P PRE " +
                to_string(best) + " " +
                to_string(id)
            );
        };
 
        if (!my) {
            if (!p5.empty()) {
                int id = old(p5);
                int r = q[id].c;
 
                st(q[id], 6);
 
                my = true;
 
                lf = max(lf, now)
                   + f(C, q[id].b);
 
                ans.push_back(
                    "E P POST " +
                    to_string(r) + " " +
                    to_string(id)
                );
            }
 
            else if (!p11.empty()) {
                vector<int> ids = batch(p11, F);
 
                for (int id : ids)
                    st(q[id], 6);
 
                my = true;
 
                lf = max(lf, now)
                   + f(F, ids.size());
 
                string x =
                    "E D POST -1 " +
                    to_string(ids.size());
 
                for (int id : ids)
                    x += " " + to_string(id);
 
                ans.push_back(x);
            }
 
            else if (!p1.empty()) {
                int id = old(p1);
 
                if (idle ||
                    now - q[id].s >= s1) {
                    admit(id);
                }
            }
 
            if (!my && !p6.empty()) {
                vector<int> ids = batch(p6, D);
 
                for (int id : ids)
                    st(q[id], 7);
 
                my = true;
 
                lf = max(lf, now)
                   + f(D, ids.size());
 
                string x =
                    "E D PRE -1 " +
                    to_string(ids.size());
 
                for (int id : ids)
                    x += " " + to_string(id);
 
                ans.push_back(x);
            }
 
            if (!my && !p1.empty()) {
                admit(old(p1));
            }
        }
 
        for (int k = 0; k < K; k++) {
            if (busy[k])
                continue;
 
            if (!p3[k].empty()) {
                int id = old(p3[k]);
 
                st(q[id], 4);
                busy[k] = true;
 
                ans.push_back(
                    "C" + to_string(k) +
                    " P PROC 0 " +
                    to_string(b) + " " +
                    to_string(k) + " " +
                    to_string(id)
                );
 
                continue;
            }
 
            if (!p9[k].empty()) {
                vector<int> ids = batch(p9[k], E);
 
                for (int id : ids)
                    st(q[id], 10);
 
                busy[k] = true;
 
                rf[k] = max(rf[k], now)
                     + f(E, ids.size());
 
                string x =
                    "C" + to_string(k) +
                    " D PROC " +
                    to_string(k) + " " +
                    to_string(ids.size());
 
                for (int id : ids)
                    x += " " + to_string(id);
 
                ans.push_back(x);
            }
        }
 
        cout << ans.size() << '\n';
 
        for (auto &x : ans)
            cout << x << '\n';
 
        cout.flush();
    }
 
    return 0;}
```
