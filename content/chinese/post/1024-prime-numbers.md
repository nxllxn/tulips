---
title: "1024 - Prime Number"
date: 2022-05-06T21:13:33+08:00
# post thumb
images:
- "images/funny-ideas/1024-prime-number.png"
#author
author: "郁金香啊"
# description
description: "1024 - Prime Number"
# Taxonomies
categories: ["FunnyIdeas"]
tags: ["FunnyIdeas"]
type: "regular"
draft: false
---

```java
import java.util.ArrayList;
import java.util.BitSet;

public class Solution {
    public List<Integer> primeNumbers(int range) {
        BitSet bitSet = new BitSet(range + 1);

        for (int num = 2; num <= range >> 1; num++) {
            int times = 2;
            while (num * times <= range) {
                bitSet.set(num * times);
                times++;
            }
        }

        List<Integer> res = new ArrayList<>();
        for (int num = 1; num < range; num++) {
            if(!bitSet.get(num)) {
                res.add(num);
            }
        }
        
        return res;
    }

    public List<Integer> steps(int range) {
        List<Integer> primeNumbers = primeNumbers(range);
        
        for(int index = primeNumbers.size() - 1;index > 0; index--) {
            primeNumbers.set(index, primeNumbers.get(index) - primeNumbers.get(index - 1));
        }
        
        return primeNumbers.subList(0, primeNumbers.size());
    }
}
```

```shell
$ ==> [1, 2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97, 101, 103, 107, 109, 113, 127, 131, 137, 139, 149, 151, 157, 163, 167, 173, 179, 181, 191, 193, 197, 199, 211, 223, 227, 229, 233, 239, 241, 251, 257, 263, 269, 271, 277, 281, 283, 293, 307, 311, 313, 317, 331, 337, 347, 349, 353, 359, 367, 373, 379, 383, 389, 397, 401, 409, 419, 421, 431, 433, 439, 443, 449, 457, 461, 463, 467, 479, 487, 491, 499, 503, 509, 521, 523, 541, 547, 557, 563, 569, 571, 577, 587, 593, 599, 601, 607, 613, 617, 619, 631, 641, 643, 647, 653, 659, 661, 673, 677, 683, 691, 701, 709, 719, 727, 733, 739, 743, 751, 757, 761, 769, 773, 787, 797, 809, 811, 821, 823, 827, 829, 839, 853, 857, 859, 863, 877, 881, 883, 887, 907, 911, 919, 929, 937, 941, 947, 953, 967, 971, 977, 983, 991, 997, 1009, 1013, 1019, 1021]
$ ==> 173 
$ ==> 173 / 1024.0 = 0.1689453125
$ ==> (1024 - 173) / 1024.0 = 0.8310546875
$ ==> 1 / 0.8310546875 = 1.2032902467685076
$ ==> [1, 1, 1, 2, 2, 4, 2, 4, 2, 4, 6, 2, 6, 4, 2, 4, 6, 6, 2, 6, 4, 2, 6, 4, 6, 8, 4, 2, 4, 2, 4, 14, 4, 6, 2, 10, 2, 6, 6, 4, 6, 6, 2, 10, 2, 4, 2, 12, 12, 4, 2, 4, 6, 2, 10, 6, 6, 6, 2, 6, 4, 2, 10, 14, 4, 2, 4, 14, 6, 10, 2, 4, 6, 8, 6, 6, 4, 6, 8, 4, 8, 10, 2, 10, 2, 6, 4, 6, 8, 4, 2, 4, 12, 8, 4, 8, 4, 6, 12, 2, 18, 6, 10, 6, 6, 2, 6, 10, 6, 6, 2, 6, 6, 4, 2, 12, 10, 2, 4, 6, 6, 2, 12, 4, 6, 8, 10, 8, 10, 8, 6, 6, 4, 8, 6, 4, 8, 4, 14, 10, 12, 2, 10, 2, 4, 2, 10, 14, 4, 2, 4, 14, 4, 2, 4, 20, 4, 8, 10, 8, 4, 6, 6, 14, 4, 6, 6, 8, 6, 12, 4, 6, 2]
```