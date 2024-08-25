## GCD

```cpp
int FindGCD(int a, int b) {
    if (a == 0) return b;
    return FindGCD(b % a, a);
}
```