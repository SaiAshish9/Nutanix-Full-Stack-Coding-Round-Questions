# Nutanix-Full-Stack-Coding-Round-Questions

#Q1

```
in a manufacturing unit, there are n machines where the ith machine consumes power[i] amount of power and produces quantity[i] number of goods. Find the maximum total quantity of goods that can be produced such that the total power consumed does not exceed maxPower if the machines used are chosen optimally.

Example
n=3
power=[2,2,2]
quantity=[1,2,3]
maxPower=4

usee machines 2 and 3 s total power used = 2+2 = 4.  This doesnot exceed maxPower, and the total quantity produced = 2+3 = 5. This is the maximum possible hence the answer is 5.

Function definition :

public static long getMaximumQuantity(List<Integer> power, List<Integer> quantity, int maxPower) 

constraints

1<=n<=36
1<=maxPower<=10^6
1<=power[i]<=10^9
1<=quantity[i]<=10^9

import java.util.List;

public class ManufacturingUnit {
    public static long getMaximumQuantity(List<Integer> power, List<Integer> quantity, int maxPower) {
        int n = power.size();
        long[][] dp = new long[n + 1][maxPower + 1];

        for (int i = 1; i <= n; i++) {
            for (int j = 0; j <= maxPower; j++) {
                dp[i][j] = dp[i - 1][j];

                if (j >= power.get(i - 1)) {
                    dp[i][j] = Math.max(dp[i][j], dp[i - 1][j - power.get(i - 1)] + quantity.get(i - 1));
                }
            }
        }

        return dp[n][maxPower];
    }

    public static void main(String[] args) {
        List<Integer> power = List.of(2, 2, 2);
        List<Integer> quantity = List.of(1, 2, 3);
        int maxPower = 4;

        long result = getMaximumQuantity(power, quantity, maxPower);
        System.out.println(result);
    }
}

dp[i][j] represents the maximum total quantity of goods that can be produced using the first i machines (from power and quantity lists) and considering a total power constraint of j.


import java.util.List;

public class ManufacturingUnit {
    public static long getMaximumQuantity(List<Integer> power, List<Integer> quantity, int maxPower) {
        int n = power.size();
        long[] dp = new long[maxPower + 1];

        for (int i = 0; i < n; i++) {
            for (int j = maxPower; j >= power.get(i); j--) {
                dp[j] = Math.max(dp[j], dp[j - power.get(i)] + quantity.get(i));
            }
        }

        return dp[maxPower];
    }

    public static void main(String[] args) {
        List<Integer> power = List.of(2, 2, 2);
        List<Integer> quantity = List.of(1, 2, 3);
        int maxPower = 4;

        long result = getMaximumQuantity(power, quantity, maxPower);
        System.out.println(result);
    }
}


import java.util.List;

public class ManufacturingUnit {
    public static long getMaximumQuantity(List<Integer> power, List<Integer> quantity, int maxPower) {
        int n = power.size();
        long left = 1, right = (long) 1e18; // set upper bound as a large number

        while (left < right) {
            long mid = left + (right - left) / 2;

            if (isFeasible(power, quantity, maxPower, mid)) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }

        return left - 1;
    }

    private static boolean isFeasible(List<Integer> power, List<Integer> quantity, int maxPower, long targetQuantity) {
        long totalPower = 0;

        for (int i = 0; i < power.size(); i++) {
            if (quantity.get(i) >= targetQuantity) {
                totalPower += (long) power.get(i) * targetQuantity;
                if (totalPower > maxPower) {
                    return false;
                }
            }
        }

        return true;
    }

    public static void main(String[] args) {
        List<Integer> power = List.of(2, 2, 2);
        List<Integer> quantity = List.of(1, 2, 3);
        int maxPower = 4;

        long result = getMaximumQuantity(power, quantity, maxPower);
        System.out.println(result);
    }
}

```

#Q2

```
consider a social networking service consisting of n users. for creating an account the user needs a username but the service does not check the uniqueness of the username. Thus any user can have any username. Also a user can create multiple accounts and these accounts may also share the same username. You are assigned a task to group users sharing at least one common username.

Task: the task is to determine the count of such groups of users such thay two different groups do not have any users sharing a common username.

Input 
first line contains an integer T denoting the number of test cases.

For each test case:

the first line contains a number N denoting the number of users.

Next, the n line contains an integer M denoting the number of accoounts a user has and M-separated strings in upper case representing the username.

sum of the length of all the usernames over all the test cases does not exceed 5 * 10"^6, and the username are always in upper case.

Returns
for each test case print in a new line, a single integer denoting the count of such groups of users

Sample input
1
3
2 ALICE BOB
2 ALEX ALEX
1 ALICE

Sample output
2

Explaination
the first and third share a common usernames ['ALICE']. Hence, they for a group
```

```
import java.util.*;

class UnionFind {
    private int[] parent;
    private int[] rank;

    public UnionFind(int n) {
        parent = new int[n];
        rank = new int[n];

        for (int i = 0; i < n; i++) {
            parent[i] = i;
            rank[i] = 0;
        }
    }

    public int find(int x) {
        if (parent[x] != x) {
            parent[x] = find(parent[x]);
        }
        return parent[x];
    }

    public void union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);

        if (rootX != rootY) {
            if (rank[rootX] < rank[rootY]) {
                int temp = rootX;
                rootX = rootY;
                rootY = temp;
            }
            parent[rootY] = rootX;
            if (rank[rootX] == rank[rootY]) {
                rank[rootX]++;
            }
        }
    }
}

public class UserGroups {
    public static List<Integer> countUserGroups(List<List<String>> testCases) {
        List<Integer> results = new ArrayList<>();

        for (List<String> users : testCases) {
            int n = Integer.parseInt(users.get(0));
            List<String> usernames = users.subList(1, users.size());

            UnionFind uf = new UnionFind(n);

            for (int i = 0; i < n; i++) {
                for (int j = i + 1; j < n; j++) {
                    if (usernames.get(j).contains(usernames.get(i))) {
                        uf.union(i, j);
                    }
                }
            }

            Set<Integer> groups = new HashSet<>();
            for (int i = 0; i < n; i++) {
                groups.add(uf.find(i));
            }

            results.add(groups.size());
        }

        return results;
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int numTestCases = scanner.nextInt();
        List<List<String>> testCases = new ArrayList<>();

        for (int t = 0; t < numTestCases; t++) {
            int n = scanner.nextInt();
            List<String> testCase = new ArrayList<>();
            testCase.add(Integer.toString(n));

            for (int i = 0; i < n; i++) {
                testCase.add(scanner.nextInt() + scanner.nextLine().trim());
            }

            testCases.add(testCase);
        }

        List<Integer> output = countUserGroups(testCases);

        for (int result : output) {
            System.out.println(result);
        }
    }
}
```

