
class Solution {
public:
    long long countNonDecreasingSubarrays(vector<int>& nums, int k) {
        int n = nums.size();

        deque<int> dq;
        long long ans = 0;
        long long cost = 0;

        int j = n - 1;

        for (int i = n - 1; i >= 0; i--) {

            // Maintain monotonic deque
            while (!dq.empty() && nums[dq.back()] < nums[i]) {

                int l = dq.back();
                dq.pop_back();

                int r = dq.empty() ? j + 1 : dq.back();

                cost += 1LL * (r - l) * (nums[i] - nums[l]);
            }

            dq.push_back(i);

            // Reduce window if operations exceed k
            while (cost > k) {

                cost -= nums[dq.front()] - nums[j];

                if (dq.front() == j) {
                    dq.pop_front();
                }

                j--;
            }

            // Count valid subarrays
            ans += j - i + 1;
        }

        return ans;
    }
};

class Solution {
public:
    int minimumChanges(string s, int k) {
        int n = s.size();
        const int INF = 1e9;

        vector<vector<int>> cost(n, vector<int>(n, INF));

        // Precompute minimum changes for every substring
        for (int i = 0; i < n; i++) {
            for (int len = 2; i + len <= n; len++) {

                for (int d = 1; d < len; d++) {
                    if (len % d != 0) continue;

                    int changes = 0;

                    // Check each group of positions
                    for (int r = 0; r < d; r++) {
                        int l = i + r;
                        int h = i + r + ((len - 1 - r) / d) * d;

                        while (l < h) {
                            if (s[l] != s[h])
                                changes++;

                            l += d;
                            h -= d;
                        }
                    }

                    cost[i][i + len - 1] =
                        min(cost[i][i + len - 1], changes);
                }
            }
        }

        // DP: dp[i][j] = minimum changes to divide
        // first i characters into j semi-palindromes
        vector<vector<int>> dp(n + 1, vector<int>(k + 1, INF));

        dp[0][0] = 0;

        for (int j = 1; j <= k; j++) {
            for (int i = 2 * j; i <= n; i++) {
                for (int p = 2 * (j - 1); p <= i - 2; p++) {

                    if (dp[p][j - 1] == INF) continue;

                    dp[i][j] = min(dp[i][j],
                        dp[p][j - 1] + cost[p][i - 1]);
                }
            }
        }

        return dp[n][k];
    }
};

class Solution {
public:
    int maxSelectedElements(vector<int>& nums) {
        sort(nums.begin(), nums.end());

        unordered_map<int, int> dp;
        int ans = 0;

        for (int x : nums) {
            dp[x + 1] = max(dp[x + 1], dp[x] + 1);

            dp[x] = max(dp[x], dp[x - 1] + 1);

            ans = max({ans, dp[x], dp[x + 1]});
        }

        return ans;
    }
};
