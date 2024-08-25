## For counting the number of prime numbers

```

I have seen people claim that the time complexity of this problem is O(nlog(log(n))) & O(sqrt(n)log(log(n))). The intention of this post is to clarify why that is the case.

Below is my O(nlog(log(n))) solution.

I have outlined where the time complexity of O(n) and O(log(log(n)) comes from:

class Solution {
    public int countPrimes(int n) {
        if (n < 2) return 0;
        boolean[] nonPrime = new boolean[n];
        nonPrime[1] = true;
        int numNonPrimes = 1;
        for (int i=2; i < n; i++) { // O(n)
            if (nonPrime[i]) continue;
            int j = i * 2;
            while (j < n) { // O(log(log(n)))
                if (!nonPrime[j]) {
                    nonPrime[j] = true;
                    numNonPrimes++;
                }
                j += i;
            }
        }
        return (n-1) - numNonPrimes;
    }
}
We're going through the whole array at least once. That gives us the O(n). But what about the O(log(log(n))? Where does that come from?

Each time we hit a prime, we have to launch a search through the rest of the array to "cross out" the multiples of that prime because we know they aren't prime (The Sieve of Erothostanes). But how many "cross outs" do we have to do for each prime?

Well,

For 2, we have to cross out n/2 numbers.
For 3, we have to cross out n/3 numbers.
For 5, we have to cross out n/5 numbers.
...etc for each prime less than n.

This means that the time complexity of "crossing out" is O(n/2 + n/3 + n/5 + ... + n/(last prime before n)).

What can this sum be simplified to in terms of n?

Well, for starters, we can factor out the n:

O(n/2 + n/3 + n/5 + ... + n/(last prime before n))
= O(n) * O(1/2 + 1/3 + 1/5 + ... + 1/(last prime before n))

What can O(1/2 + 1/3 + 1/5 + ... + 1/(last prime before n)) be simplified to in terms of n?

I won't go into detail here but the answer is essentially bounded by log(log(n)). The proof for this is summed up well here for those interested: http://www.cs.umd.edu/~gasarch/BLOGPAPERS/sump.pdf

So now we have that the time complexity of "crossing out" is:

O(n) * O(1/2 + 1/3 + 1/5 + ... + 1/(last prime before n))
= O(n) * O(log(log(n)))
= O(nlog(log(n)))

Thus, the nlog(log(n)) simply comes from the sum: n/2 + n/3 + n/5 + ... + n/(last prime before n) when we're crossing out numbers in the Sieve algorithm.

What about when people say the time complexity is O(sqrt(n)log(log(n)))?

That is simply an optimization to this same solution, replacing the n in the outer for loop with sqrt(n). (You can try it with my code and it will run faster). This post isn't designed to explain the logic behind that one but you can probably google it or find it explained elsewhere if you're interested.

```