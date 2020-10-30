[ [面试经验] 亚麻oa2 ng ](https://www.1point3acres.com/bbs/thread-661768-1-1.html)
[ref: Count of ways to split a given number into prime segments](https://www.geeksforgeeks.org/count-of-ways-to-split-a-given-number-into-prime-segments/?ref=rp)

[split to primes - leetcode playground](https://leetcode.com/playground/new/empty)

[count the number of ways the string can split to get pime number](https://leetcode.com/discuss/interview-question/593211/count-the-number-of-ways-the-string-can-split-to-get-pime-number)

* A split that contains numbers with leading zeros will be invalid and the initial string does not contain leading zeroes.
    * 以 `0` 为开始的数字的 invalid;
    * 题目给的 String 首字母不是 `0`.


## Method 1. Naive Approach: Use **Recursion**
* Start recursing from ending index of the given string and consider every suffix up to 6 digits (given that the prime number must be in the range of `[1, 10^6]`) and check if it is a prime number of not. []
* If the suffix doesn't contain a leading zero and it is a prime number, then recursively call the function to count the ways for the remaining string and add to the total count []
* When the index reaches 0, we reach the base case and return 1 to consider current splits as a valid count.
* Take mode of the count at each iteration and return the count at the end.

```java
// Ref: https://www.geeksforgeeks.org/find-all-possible-ways-to-split-the-given-string-into-primes/
// Find all the ways to split the given string into Primes.
public class Main {    
    static int MOD = 1000000007; 
    static boolean[] sieve = new boolean[1000000];
    
    // Function to count the number of prime strings
    public static int countPrimeStrings(String str) {
        int n = str.length();
        int[] dp = new int[n+1];
        Arrays.fill(dp, -1);
        dp[0] = 1;
        return rec(str, n, dp);
    }
    
    // Function to find the count of ways to split string into prime numbers
    private static int rec(String str, int i, int[] dp) {
        if(dp[i] != -1) {
            return dp[i];
        }
        int cnt = 0;
        for(int j=1; j<=6; j++) {   // the maximum prime number in the range 1-1000000 is 999983
            // Number should not have a leading zero an it should be a prime number
            // str[j, i), i.e. str.substring(j, i) = str[j, i) ==> then `len = i - j`, and str.charAt(i-j) will be the start character.
            // i - j >= 0 : the length of substring
            // str.charAt(i-j) != '0' : the leading digit is not zero
            // isPrime(str.substring(i-j, i)) : i.e. str[j, i) is a prime number
            if(i - j >= 0 && str.charAt(i - j)!='0' && isPrime(str.substring(i-j, i))) {
                cnt += rec(str, i - j, dp);
                cnt %= MOD;
            }
        }
        // System.out.println("res: " + cnt);
        dp[i] = cnt;
        return dp[i];
    }
    
    // Function to check whether a number is a prime number of not
    private static boolean isPrime(String number) {
        int num = Integer.valueOf(number);  // Integer.valueOf(String s);
        return sieve[num];
    }
    
    // Function to build sieve
    private static void buildSieve() {
        Arrays.fill(sieve, true);
        sieve[0] = false;
        sieve[1] = false;
        
        for(int p=2; p*p<=1000000; p++) {
            if(sieve[p] == true) {  // If p is a prime
                // Update all multiples of p as non prime
                for(int i=p*p; i<1000000; i+=p) {
                    sieve[i] = false;
                }
            }
        }
    }
    
    public static void main(String[] args) {
        System.out.println("Hello World!");
        buildSieve();
        String s1 = "3175";
        System.out.println(countPrimeStrings(s1));
    }
}
```


## Method 2. [return number of ways and split strings](https://leetcode.com/playground/new/empty)
[count the number of ways the string can split to get pime number](https://leetcode.com/discuss/interview-question/593211/count-the-number-of-ways-the-string-can-split-to-get-pime-number)
```java
// "static void main" must be defined in a public class.
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Set;

public class SplitIntoPrimes {
    private Set map = new HashSet();
    private boolean[] sieve = new boolean[1000000];
    public int findNumberOfWays(String input) {
        List<List<Integer>> ways = new ArrayList<>();
        List<Integer> res = new ArrayList<>();
        findSplitWays(input, res, ways);
        return ways.size();
    }
    
    public void findSplitWays(String suffix, List<Integer> res, List<List<Integer>> resSet) {
        if(suffix.length() == 0) {
            resSet.add(new ArrayList<Integer>(res));
            System.out.println(res.toString());
            return;
        }
        
        // for(int i=0; i<Math.min(6, suffix.length()); i++) {
        for(int i=0; i<suffix.length(); i++) {
            String snum = suffix.substring(0, i+1);
            int numb = Integer.parseInt(snum);
            boolean isPrime = isPrimeNumber(numb);
            if(isPrime) {
                res.add(numb);
                findSplitWays(suffix.substring(i+1), res, resSet);
                res.remove(res.size() - 1); // backtracking
            }
        }
    }
    
    private boolean isPrimeNumber(int number) {
        if(map.contains(number)) {
            return true;
        }
        boolean isPrime = isPrime(number);
        if(isPrime) {
            map.add(number);
        }
        return isPrime;
    }
    
    // private boolean isPrime(int n) {
    //     if(n <= 1) {
    //         return false;
    //     }
    //     if(n == 2) {
    //         return true;
    //     }
    //     if(n % 2 == 0) {
    //         return false;
    //     }
    //     for(int i=3; i<=n/2; i=i+2) {
    //         if(n % i == 0) {
    //             return false;
    //         }
    //     }
    //     return true;
    // }
    
    private void buildSieve() {
        Arrays.fill(sieve, true);
        sieve[0] = false;
        sieve[1] = false;
        
        for(int i=2; i*i<=1000000; i++) {
            // If p is a prime
            if(sieve[i] == true) {
                // Update all multiples of p as non prime
                for(int j=i*i; j<100000; j+=i) {
                    sieve[j] = false;
                }
                // or
                // for(int j=2; i * j < 1000000; j++) {
                //     sieve[i * j] = false;
                // }
            }
        }
    }
    
    private boolean isPrime(int n) {
        return sieve[n];
    }
    
    public static void main(String[] args) {
        System.out.println("Hello World!");
        SplitIntoPrimes obj = new SplitIntoPrimes();
        obj.buildSieve();
	    System.out.println(obj.findNumberOfWays("11373"));
	    System.out.println(obj.findNumberOfWays("3175"));
    }
}
```
