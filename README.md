# my-leetcode-sessions
i am solving leetcode problems 
class Solution {
public:
    int totalNumbers(vector<int>& digits) {
        
        set<int> st;

        for (int i = 0; i < digits.size(); i++) {
            
            // First digit cannot be 0
            if (digits[i] == 0)
                continue;

            for (int j = 0; j < digits.size(); j++) {
                
                // Same copy of digit cannot be used twice
                if (j == i)
                    continue;

                for (int k = 0; k < digits.size(); k++) {
                    
                    // Same copy cannot be reused
                    if (k == i || k == j)
                        continue;

                    // Last digit must be even
                    if (digits[k] % 2 != 0)
                        continue;

                    int num = digits[i] * 100 
                            + digits[j] * 10 
                            + digits[k];

                    st.insert(num);
                }



                DAY 2
                class Solution {
public:

    struct State {
        long long score;
        vector<int> indices;
    };

    vector<vector<State>> dp;
    vector<vector<int>> intervals;

    // Lexicographically smaller vector
    vector<int> addIndex(vector<int> v, int idx) {
        v.push_back(idx);
        sort(v.begin(), v.end());
        return v;
    }

    bool better(State a, State b) {
        if (a.score != b.score)
            return a.score > b.score;

        return a.indices < b.indices;
    }

    State solve(int i, int k) {

        if (i >= intervals.size() || k == 0)
            return {0, {}};

        if (dp[i][k].score != -1)
            return dp[i][k];

        // Don't choose current interval
        State skip = solve(i + 1, k);

        // Find next non-overlapping interval
        int l = intervals[i][0];
        int r = intervals[i][1];
        int weight = intervals[i][2];

        int low = i + 1;
        int high = intervals.size();

        while (low < high) {
            int mid = low + (high - low) / 2;

            if (intervals[mid][0] > r)
                high = mid;
            else
                low = mid + 1;
        }

        int next = low;

        // Choose current interval
        State take = solve(next, k - 1);

        take.score += weight;
        take.indices = addIndex(take.indices, intervals[i][3]);

        if (better(take, skip))
            return dp[i][k] = take;

        return dp[i][k] = skip;
    }

    vector<int> maximumWeight(vector<vector<int>>& intervals) {

        // Add original index
        for (int i = 0; i < intervals.size(); i++) {
            intervals[i].push_back(i);
        }

        // Sort according to starting point
        sort(intervals.begin(), intervals.end(),
             [](const vector<int>& a, const vector<int>& b) {
                 if (a[0] != b[0])
                     return a[0] < b[0];

                 return a[1] < b[1];
             });

        this->intervals = intervals;

        int n = intervals.size();

        dp.assign(n, vector<State>(5, {-1, {}}));

        return solve(0, 4).indices;
    }
};
            }
        }

        return st.size();
    }
};
