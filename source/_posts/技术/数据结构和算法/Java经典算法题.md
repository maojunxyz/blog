---
title: Java经典算法题
date: 2023/10/22 21:32
updated: 2023/10/22 21:32
categories:
- [技术, 数据结构和算法]
tags:
- 数据结构和算法
- Java
---

## 二分查找法

```java
class Solution {
    public int search(int[] nums, int target) {
      int min = 0;
      int max = nums.length - 1;
      
      while(min <= max){
        int mid = min + (max - min) / 2;
        int midVal = nums[mid];
        if(midVal < target){
          min = mid + 1;
        }else if(midVal > target){
          max = mid -1;
        }else{
          return mid;
        }
      }

      return -1;
    }
}
```

> 注意：第4行使用 int mid = min + (max - min) / 2;避免数值溢出（min+max)/2 问题。



### 源码实现

```java
private static int binarySearch0(int[] a, int fromIndex, int toIndex,
                                 int key) {
    int low = fromIndex;
    int high = toIndex - 1;

    while (low <= high) {
        int mid = (low + high) >>> 1;
        int midVal = a[mid];

        if (midVal < key)
            low = mid + 1;
        else if (midVal > key)
            high = mid - 1;
        else
            return mid; // key found
    }
    return -(low + 1);  // key not found.
}
```

`Arrays.binarySearch` 是 Java 中用于在有序数组中执行二分查找的方法。它接受一个有序数组和要查找的元素作为参数，并返回元素在数组中的索引（如果找到）或应该插入的索引位置（如果未找到）。

以下是 `Arrays.binarySearch` 方法的签名：

```java
public static int binarySearch(Object[] a, Object key)
```

`Arrays.binarySearch` 方法有以下几个重要特点：

1. **有序数组要求**：传递给 `binarySearch` 方法的数组必须是有序的。如果数组无序，结果是不确定的。

2. **返回值说明**：如果找到元素 `key`，它将返回元素的索引。如果未找到元素 `key`，则返回一个负数值，该值是应该插入元素 `key` 以维持数组的有序性的位置。负数值的计算方式为：- (插入点索引 + 1)。这是一个将元素插入到有序数组的标准方式。



### 使用示例

下面是一个示例，演示如何使用 `Arrays.binarySearch` 方法：

```java
import java.util.Arrays;

public class BinarySearchExample {
    public static void main(String[] args) {
        int[] numbers = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
        int target = 6;
        
        // 使用binarySearch查找元素
        int index = Arrays.binarySearch(numbers, target);
        
        if (index >= 0) {
            System.out.println(target + " found at index " + index);
        } else {
            System.out.println(target + " not found. It should be inserted at index " + (-index - 1));
        }
    }
}
```

在这个示例中，我们将 `target`（要查找的元素）设为6，并使用 `Arrays.binarySearch` 在有序数组 `numbers` 中查找它。如果找到，它将返回该元素的索引；如果未找到，它将返回应该插入该元素的位置。

要注意的是，如果数组中有多个相同的元素，`binarySearch` 方法只返回最前面匹配的索引。



## 冒泡排序

冒泡排序（Bubble Sort）是一种简单的排序算法，它反复地遍历要排序的列表，比较相邻元素并交换它们，直到整个列表按照升序或降序排列。

其时间复杂度和空间复杂度如下：

1. 时间复杂度：

   - 最好情况：$O(n)$
   - 平均情况：$O(n^2)$
   - 最坏情况：$O(n^2)$

   冒泡排序的最好情况发生在输入数组本身已经有序的情况下，此时只需要进行一次遍历，时间复杂度为O(n)。然而，最坏情况发生在输入数组完全逆序的情况下，需要进行n-1轮比较和交换，每轮比较需要遍历整个数组，因此时间复杂度为O(n^2)。平均情况下，冒泡排序的时间复杂度也是O(n^2)。

2. 空间复杂度：

   - 冒泡排序是一种原地排序算法，不需要额外的内存空间，因此空间复杂度为O(1)。

尽管冒泡排序的时间复杂度在最坏情况下较高，但它是一种简单的排序算法，适用于小型数据集或在特殊情况下。在实际应用中，通常会使用更高效的排序算法，如快速排序或归并排序，以提高排序性能。



### Java实现

```java
class Solution {
    public void sortColors(int[] nums) {
        for(int i = 0; i < nums.length - 1; i++){
            for(int j = 0; j < nums.length - 1 - i; j++){
                int l = nums[j];
                int r = nums[j + 1];
                if(l > r){
                    nums[j + 1] = l;
                    nums[j] = r;
                }
            }
        }
    }
}
```

在这段代码中，外层循环的次数是 `nums.length - 1`，内层循环的次数也是 `nums.length - 1 - i`，其中`i`是外层循环的迭代变量。内层循环中执行了比较和可能的元素交换操作。



### 优化实现

```java
class Solution {
    public void sortColors(int[] nums) {
        int n = nums.length - 1;
        while(true){
            int last = 0;
            for(int j = 0; j < n; j++){
                int l = nums[j];
                int r = nums[j + 1];
                if(l > r){
                    nums[j + 1] = l;
                    nums[j] = r;
                    last = j;
                }
            }
            // 记录最后交换时的索引
            n = last;
            if (n == 0){
                break;
            }
        }
    }
}
```

在这段代码中，有一个外层while循环和一个内层for循环。外层while循环的终止条件是 `n == 0`，即没有发生交换的情况下终止。内层for循环遍历数组，并在需要的情况下进行元素交换。



### 源码实现

Java标准库中没有使用冒泡排序算法来实现排序方法。标准库中提供了`java.util.Arrays`类，其中包含了用于对数组进行排序的方法，最常见的是`Arrays.sort`方法，它使用更高效的排序算法，如快速排序和归并排序，而不是冒泡排序。



## 在线题库

- [Binary Search - LeetCode](https://leetcode.com/problems/binary-search/description/)
- [Sort Colors - LeetCode](https://leetcode.com/problems/sort-colors/)



（本文完）

