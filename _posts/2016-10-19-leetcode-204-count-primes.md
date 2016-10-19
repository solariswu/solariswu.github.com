---
layout: post
title: "leetcode #204 Count Primes"
description: ""
category: 
tags: [leetcode, algorithm]
---
### Description:

  Count the number of prime numbers less than a non-negative number, n.

### Credits:
  Special thanks to @mithmatt for adding this problem and creating all test cases.

### resolvation
  1. Sieve of Eratoshenes
  2. Test from p*p till sqr(n), remove all the times times of p
  3. Optimized -> 
        Use array to store the primes [2, sqr(n)]
        Use these primes to sieve [sqr(n)+1, n]
        Can separate the target array for sieving, segment size could refer to the systme cache size

```java
public static void Local_Bit_Sieve(int nth)
{
    int startTime = Environment.TickCount;
    int limit = nth < 6 ? 25 :(int)(nth * (Math.Log(nth) + Math.Log(Math.Log(nth))));
    int sqrt = (int) Math.Sqrt(limit) + 1;
    
    //Get all primes which are less than \sqrt{limit}
    List<uint> isPrime = new List<uint>((sqrt >> 5) +1);
    for (int i = 0; i < (sqrt >> 5) + 1; i++)
        isPrime.Add(0x0);
    for (int i = 2; i * i <= sqrt; i++)
        // is_prime[i>>5] bit i % 32 == 0 means it is a prime
        if ((isPrime[i >> 5] & (1 << (i & 31))) == 0)
        {
            for (int j = i * i; j <= sqrt; j += i)
                // is_prime[j>>5] bit j % 32 = 1;
                isPrime[j >> 5] |= (uint)1 << (j & 31);
        }

    //smallPrimes store the primes
    List<int> smallPrimes = new List<int>();
    for (int i = 2; i < sqrt; i++)
    {
        if ((isPrime[i >> 5] &amp; (1 << (i & 31))) == 0)
        {
            smallPrimes.Add(i);
        }
    }

    int segSize = Math.Max(sqrt,256 * 256);

    uint[] primeSeg = new uint[segSize];
    
    //allPrimes store all primes which are found.
    List<int> allPrimes = new List<int>();
    allPrimes.AddRange(smallPrimes);

    int high = segSize << 5;
    //chunk [2,limit] into different segments
    for (int low = sqrt; low <= limit; low += segSize << 5)
    {
        Array.Clear(primeSeg,0,segSize);
        //for each prime, use them to mark the [low,low + segSize]
        foreach (var sPrime in smallPrimes)
        {
            int initValue = low % sPrime;
            for (int i = (initValue == 0 ? initValue : sPrime - initValue); i < high; i += sPrime)
            {
                primeSeg[i >> 5] |= (uint) 1 << (i & 31);
            }
        }

        for (int i = 0; i < high; i++)
        {
            if ((primeSeg[i >> 5] & (1 << (i & 31))) == 0)
                allPrimes.Add(i + low);
        }

        if (allPrimes.Count() > nth)
        {
            Console.WriteLine("The {0}th_prime is:{1} SpentTime:{2}ms",nth, allPrimes[nth-1],
                Environment.TickCount - startTime);
            break;
        }
    }
}
```