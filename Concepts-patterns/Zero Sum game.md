### Overview
[As per wikipedia](https://en.wikipedia.org/wiki/Zero-sum_game)

Zero-sum game is a mathematical representation in game theory and economic theory of a situation that involves two sides, where the result is an advantage for one side and an equivalent loss for the other. In other words, player one's gain is equivalent to player two's loss, with the result that the net improvement in benefit of the game is zero.

If the total gains of the participants are added up, and the total losses are subtracted, they will sum to zero. Thus, cutting a cake, where taking a more significant piece reduces the amount of cake available for others as much as it increases the amount available for that taker, is a zero-sum game if all participants value each unit of cake equally. Other examples of zero-sum games in daily life include games like poker, chess, and bridge where one person gains and another person loses, which results in a zero-net benefit for every player. In the markets and financial instruments, futures contracts and options are zero-sum games as well.

In contrast, non-zero-sum describes a situation in which the interacting parties' aggregate gains and losses can be less than or more than zero. A zero-sum game is also called a strictly competitive game, while non-zero-sum games can be either competitive or non-competitive. Zero-sum games are most often solved with the minimax theorem which is closely related to linear programming duality, or with Nash equilibrium. Prisoner's Dilemma is a classic non-zero-sum game.

----
After understanding what a zero sum game is, let's understand how to solve this problem. I encountered this problem while solving [Stone Game 2](https://leetcode.com/problems/stone-game-ii/). It was quite difficult for me to solve before understanding the algorithm for solving it. It is good to go through the DP tree of this problem. 

You can indetify a zero-sum problem by looking at how the players are playing. Let me present you with the problem:

```
Alice and Bob continue their games with piles of stones.  There are a number of piles arranged in a row, and each pile has a positive integer number of stones piles[i].  The objective of the game is to end with the most stones. 

Alice and Bob take turns, with Alice starting first.  Initially, M = 1.

On each player's turn, that player can take all the stones in the first X remaining piles, where 1 <= X <= 2M.  Then, we set M = max(M, X).

The game continues until all the stones have been taken.

Assuming Alice and Bob play optimally, return the maximum number of stones Alice can get.

Example 1:

Input: piles = [2,7,9,4,4]
Output: 10
Explanation:  If Alice takes one pile at the beginning, Bob takes two piles, then Alice takes 2 piles again. Alice can get 2 + 4 + 4 = 10 piles in total. If Alice takes two piles at the beginning, then Bob can take all three piles left. In this case, Alice get 2 + 7 = 9 piles in total. So we return 10 since it's larger. 

```

For starting, I tried to solve this problem only by maximizing what Alice can get and minimizing what Bob will get. This looks like a greedy solution, which this problem is not.

The tricky thing is, both players are playing optimally. Which means that both are trying to minimize the score of the other person, or trying to maximize their score. At each point in the DP tree, Alice wants to maximize her score and chooses the sub tree which might maximize her score. Likewise, Bob chooses the subtree which minimizes her score. Note that there is one function which returns the payoff of only one player. In this case, we have Alice's payoff only. 

This is how the solution looks like. Here the function f gives the Alice's payoff only and that's why we have only included the pile when it is Alice's turn.

```cpp
class Solution {
public:
    int stoneGameII(vector<int>& piles) {
        int n = piles.size();
        function<int(int, int, int)> f = [&](int p, int i, int m) -> int {
            if (i == n) {
                return 0;
            }
            int res = p == 1 ? 1000000 : -1, s = 0;
            for (int x = 1; x <= min(2 * m, n - i); x++) {
                s += piles[i + x - 1];
                if (p == 0) {
                    res = max(res, s + f(1, i + x, max(m, x)));
                }
                else {
                    res = min(res, f(0, i + x, max(m, x)));
                }
            }
            return res;
        };
        return f(0, 0, 1);
    }
};
```

#### Main Elements
1. Payoff measurement: fucntion or table
