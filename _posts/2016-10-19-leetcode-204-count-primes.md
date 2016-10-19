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
public static void Bit_Sieve(int nth)
{
    int startTime = Environment.TickCount;
    int limit = nth &lt; 6 ? 25 : (int)(nth * (Math.Log(nth) + Math.Log(Math.Log(nth))));
    int count = 0;

    int total = limit + 1;
    int sqrt = (int)Math.Sqrt(limit) + 1;

    //[31 30 29 ... 0] every number maps to a bit in uint.
    List&lt;uint&gt; is_prime = new List&lt;uint&gt;((total &gt;&gt; 5) + 1);

    for (int i = 0; i &lt; (total &gt;&gt; 5) + 1; i++)
        is_prime.Add(0x0);


    for (int i = 2; i &lt;= sqrt; i++)
        // is_prime[i&gt;&gt;5] bit i % 32 == 0 means it is a prime
        if ((is_prime[i &gt;&gt; 5] &amp; (1 &lt;&lt; (i &amp; 31))) == 0)
        {
            for (int j = i * i; j &lt;= total; j += i)
                // is_prime[j&gt;&gt;5] bit j % 32 = 1;
                is_prime[j &gt;&gt; 5] |= (uint)1 &lt;&lt; (j &amp; 31);
        }

    for (int i = 2; i &lt; total; i++)
    {
        if ((is_prime[i &gt;&gt; 5] &amp; (1 &lt;&lt; (i &amp; 31))) == 0)
        {
            count++;
            if (count == nth)
            {
                Console.WriteLine(&quot;The {0}th_prime is:{1} SpentTime:{2}ms&quot;,nth , i, Environment.TickCount - startTime);
                break;
            }
        }
    }
}
```