---
layout: page
title: Java 数组刷题
permalink: /interview/java-array/
---

# Java 数组刷题常用方法

数组是定长对象，自带成员只有 `length` 字段；常用方法来自 `java.util.Arrays`，剩下的靠套路。

## 1. 初始化与填充

```java
int[] a = new int[n];                 // 全 0
int[] b = new int[n];
Arrays.fill(b, -1);                   // 全 -1，DP / visited 常用
int[][] g = new int[m][n];            // g.length = m, g[0].length = n
boolean[] seen = new boolean[n];
char[] cs = s.toCharArray();          // String → char[]
String t = new String(cs);            // char[] → String
```

## 2. 排序

```java
Arrays.sort(a);                                  // int[]，O(n log n)
Arrays.sort(a, l, r);                            // 排 [l, r)
Arrays.sort(intervals, (x, y) -> x[0] - y[0]);   // 二维按首列
// 防溢出写法：
Arrays.sort(intervals, (x, y) -> Integer.compare(x[0], y[0]));
```

`int[]` 不能直接降序，需要装箱：

```java
Integer[] boxed = Arrays.stream(a).boxed().toArray(Integer[]::new);
Arrays.sort(boxed, Collections.reverseOrder());
```

## 3. 拷贝与扩容

```java
int[] c = Arrays.copyOf(a, n);          // 截断或补 0
int[] d = Arrays.copyOfRange(a, l, r);  // 左闭右开
int[] e = a.clone();                    // 一维深拷贝
System.arraycopy(src, sp, dst, dp, len);
```

二维数组没有现成深拷贝，只能逐行 `clone()`：

```java
int[][] copy = new int[m][];
for (int i = 0; i < m; i++) copy[i] = g[i].clone();
```

## 4. 查找 / 比较 / 打印

```java
Arrays.binarySearch(a, target);   // 必须先排序，找不到返回 -(插入点)-1
Arrays.equals(a, b);
Arrays.deepEquals(g1, g2);
Arrays.toString(a);               // 调试用
Arrays.deepToString(g);
```

## 5. 数组 ↔ List

```java
List<Integer> list = new ArrayList<>(Arrays.asList(1, 2, 3)); // 可变
List<int[]> list2 = new ArrayList<>();                        // 存区间 / 坐标
list2.add(new int[]{l, r});
```

注意 `Arrays.asList(int[])` 是坑，拿到的是 `List<int[]>` 而不是 `List<Integer>`。

## 6. 刷题高频套路

- **双指针**：有序两数之和、原地删除、盛水、三数之和
- **滑动窗口**：最长无重复、最小覆盖子串、定长窗口最大值
- **前缀和**：`pre[i+1] = pre[i] + a[i]`，子数组和 `pre[r] - pre[l]`；配合 `HashMap` 做「和为 K」
- **差分数组**：区间加减，最后前缀和还原
- **二分**：`mid = l + (r - l) / 2` 防溢出；找左边界 `if (a[mid] >= t) r = mid; else l = mid + 1;`
- **原地哈希**：把 `x` 放到下标 `x - 1`，找缺失正数
- **方向数组**：`int[][] dirs = {{0,1},{1,0},{0,-1},{-1,0}};` 走矩阵
- **堆**：`PriorityQueue<int[]> pq = new PriorityQueue<>((x,y) -> x[0] - y[0]);` TopK / 合并链表
- **单调栈**：下一个更大元素、接雨水、柱状图最大矩形

## 7. 性能与坑

- `length` 是字段，不是方法。
- LeetCode 里少用 `stream`，常数大，热路径容易 TLE。
- 比较器别写 `(a, b) -> a - b`，遇到 `Integer.MIN_VALUE` 会溢出，用 `Integer.compare`。
- `Arrays.sort` 对基本类型不稳定，对对象稳定。
- 返回结果常要求新数组，别直接返回入参（有些题会校验引用）。
- `char` 数组做计数时 `cnt[c - 'a']++` 比 `HashMap` 快一个量级。

## 8. 常用模板

### 二分查找左边界

```java
int l = 0, r = n;
while (l < r) {
    int mid = l + (r - l) / 2;
    if (a[mid] >= target) r = mid;
    else l = mid + 1;
}
// l 为第一个 >= target 的下标，可能等于 n
```

### 滑动窗口

```java
int l = 0, ans = 0;
Map<Character, Integer> cnt = new HashMap<>();
for (int r = 0; r < n; r++) {
    cnt.merge(s.charAt(r), 1, Integer::sum);
    while (/* 窗口不合法 */) {
        char c = s.charAt(l++);
        cnt.merge(c, -1, Integer::sum);
    }
    ans = Math.max(ans, r - l + 1);
}
```

### 前缀和 + 哈希

```java
Map<Integer, Integer> pre = new HashMap<>();
pre.put(0, 1);
int sum = 0, ans = 0;
for (int x : a) {
    sum += x;
    ans += pre.getOrDefault(sum - k, 0);
    pre.merge(sum, 1, Integer::sum);
}
```
