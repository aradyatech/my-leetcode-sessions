DAY 5 OF LEETCODE
class Solution {
public:
    bool isRectangleOverlap(vector<int>& rec1, vector<int>& rec2) {

        // No overlap in X direction
        if (rec1[2] <= rec2[0] || rec2[2] <= rec1[0])
            return false;

        // No overlap in Y direction
        if (rec1[3] <= rec2[1] || rec2[3] <= rec1[1])
            return false;

        return true;
    }
};
day 7 
class Solution {
public:
    long long power(long long a, long long b, long long mod) {
        long long res = 1;

        while (b > 0) {
            if (b & 1)
                res = res * a % mod;

            a = a * a % mod;
            b >>= 1;
        }

        return res;
    }

    int numberOfSets(int n, int k) {
        const long long MOD = 1000000007;

        int N = n + k - 1;
        int R = 2 * k;

        // factorial
        vector<long long> fact(N + 1, 1);

        for (int i = 1; i <= N; i++)
            fact[i] = fact[i - 1] * i % MOD;

        // C(N,R) = fact[N] / (fact[R] * fact[N-R])
        long long numerator = fact[N];

        long long denominator =
            fact[R] * fact[N - R] % MOD;

        // Modular inverse using Fermat's Little Theorem
        long long inverse =
            power(denominator, MOD - 2, MOD);

        return numerator * inverse % MOD;
    }
};
class Solution {
    struct Node {
        int prod;
        int cnt[5];

        Node(int k = 5) {
            prod = 1;
            for (int i = 0; i < 5; i++)
                cnt[i] = 0;
        }
    };

    int k;
    vector<Node> tree;

    Node merge(Node &L, Node &R) {
        Node res;

        // Product of complete segment
        res.prod = (L.prod * R.prod) % k;

        // Prefixes completely inside left segment
        for (int r = 0; r < k; r++) {
            res.cnt[r] = L.cnt[r];
        }

        // Prefixes that take all of left
        // and some prefix of right
        for (int r = 0; r < k; r++) {
            int newRem = (L.prod * r) % k;
            res.cnt[newRem] += R.cnt[r];
        }

        return res;
    }

    void build(int node, int l, int r, vector<int>& nums) {
        if (l == r) {
            int rem = nums[l] % k;

            tree[node].prod = rem;
            tree[node].cnt[rem] = 1;

            return;
        }

        int mid = (l + r) / 2;

        build(node * 2, l, mid, nums);
        build(node * 2 + 1, mid + 1, r, nums);

        tree[node] = merge(tree[node * 2], tree[node * 2 + 1]);
    }

    void update(int node, int l, int r, int idx, int val) {
        if (l == r) {
            int rem = val % k;

            tree[node] = Node();
            tree[node].prod = rem;
            tree[node].cnt[rem] = 1;

            return;
        }

        int mid = (l + r) / 2;

        if (idx <= mid)
            update(node * 2, l, mid, idx, val);
        else
            update(node * 2 + 1, mid + 1, r, idx, val);

        tree[node] = merge(tree[node * 2], tree[node * 2 + 1]);
    }

    Node query(int node, int l, int r, int ql, int qr) {
        // Completely inside
        if (ql <= l && r <= qr)
            return tree[node];

        int mid = (l + r) / 2;

        // Only left
        if (qr <= mid)
            return query(node * 2, l, mid, ql, qr);

        // Only right
        if (ql > mid)
            return query(node * 2 + 1, mid + 1, r, ql, qr);

        // Both sides
        Node L = query(node * 2, l, mid, ql, qr);
        Node R = query(node * 2 + 1, mid + 1, r, ql, qr);

        return merge(L, R);
    }

public:
    vector<int> resultArray(vector<int>& nums, int K,
                            vector<vector<int>>& queries) {

        k = K;

        int n = nums.size();

        tree.resize(4 * n);

        build(1, 0, n - 1, nums);

        vector<int> ans;

        for (auto &q : queries) {

            int index = q[0];
            int value = q[1];
            int start = q[2];
            int x = q[3];

            // Permanent update
            update(1, 0, n - 1, index, value);

            // Query [start ... n-1]
            Node res = query(1, 0, n - 1, start, n - 1);
README.md

class Solution {
public:
    static const int MOD = 1e9 + 7;

    struct Node {
        long long sum = 0;
        long long sq = 0;
        long long lazy = 0;
    };

    vector<Node> seg;
    int n;

    void apply(int node, int l, int r, long long v) {
        long long len = r - l + 1LL;
        seg[node].sq = (seg[node].sq
            + 2LL * v % MOD * seg[node].sum % MOD
            + v * v % MOD * len % MOD) % MOD;

        seg[node].sum = (seg[node].sum + v * len) % MOD;
        seg[node].lazy = (seg[node].lazy + v) % MOD;
    }

    void push(int node, int l, int r) {
        if (seg[node].lazy == 0 || l == r) return;

        int mid = (l + r) / 2;
        apply(node * 2, l, mid, seg[node].lazy);
        apply(node * 2 + 1, mid + 1, r, seg[node].lazy);

        seg[node].lazy = 0;
    }

    void update(int node, int l, int r, int ql, int qr) {
        if (qr < l || r < ql) return;

        if (ql <= l && r <= qr) {
            apply(node, l, r, 1);
            return;
        }

        push(node, l, r);

        int mid = (l + r) / 2;
        update(node * 2, l, mid, ql, qr);
        update(node * 2 + 1, mid + 1, r, ql, qr);

        seg[node].sum = (seg[node * 2].sum
            + seg[node * 2 + 1].sum) % MOD;

        seg[node].sq = (seg[node * 2].sq
            + seg[node * 2 + 1].sq) % MOD;
    }

    int sumCounts(vector<int>& nums) {
        n = nums.size();
        seg.assign(4 * n + 5, Node());

        unordered_map<int, int> last;
        long long ans = 0;

        for (int i = 0; i < n; i++) {
            int left = last.count(nums[i]) ? last[nums[i]] + 1 : 0;

            update(1, 0, n - 1, left, i);

            ans = (ans + seg[1].sq) % MOD;
            last[nums[i]] = i;
        }

        return ans;
    }
};


            ans.push_back(res.cnt[x]);
        }

        return ans;
    }
};

class Solution {
public:
    int minMoves(vector<int>& nums) {
        int mn = *min_element(nums.begin(), nums.end());
        
        int sum = 0;
        for (int x : nums) {
            sum += x - mn;
        }
        
        return sum;
    }
};

class Solution {
public:
    long long minimumCost(int cost1, int cost2, int costBoth,
                          int need1, int need2) {

        long long common = min(need1, need2);

        long long ans = common * min(costBoth, cost1 + cost2);

        if (need1 > need2) {
            ans += (long long)(need1 - need2) * min(cost1, costBoth);
        } 
        else {
            ans += (long long)(need2 - need1) * min(cost2, costBoth);
        }

        return ans;
    }
};

class Solution {
public:
    long long minimumCost(int cost1, int cost2, int costBoth,
                          int need1, int need2) {

        long long common = min(need1, need2);

        long long ans = common * min(costBoth, cost1 + cost2);

        if (need1 > need2) {
            ans += (long long)(need1 - need2) * min(cost1, costBoth);
        } 
        else {
            ans += (long long)(need2 - need1) * min(cost2, costBoth);
        }

        return ans;
    }
};
