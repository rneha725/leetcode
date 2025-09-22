## GCD

```cpp
int FindGCD(int a, int b) {
    if (a == 0) return b;
    return FindGCD(b % a, a);
}
```

### Prime Factor of a number:

```cpp
List<Integer> primeDecompose(int num) {
    List<Integer> primeFactors = new ArrayList<Integer>();
    int factor = 2;

    while (num >= factor * factor) {
        if (num % factor == 0) {
            primeFactors.add(factor);
            num = num / factor;
        } else {
            factor += 1;
        }
    }
    primeFactors.add(num);
    return primeFactors;
}

```
