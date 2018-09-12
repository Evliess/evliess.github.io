---
layout: index
title: "Algorithm"
category: java
date: 2018-02-22 11:03:55
---

# Common algorithm

> 二分查找算法

```java
//递归实现
private int binarySearch(int arr[], int start, int end, int target) {
    int middle = (start + end) / 2;   //算middle的位置一定要是(start + end)/2 , 想想为什么不能是end/2?
    if (target == arr[middle]) {
      return middle;
    }
    //middle >=1 保证end的最小值是0
    if (target < arr[middle] && middle >= 1) {
      return binarySearch(arr, 0, middle - 1, target);
      //middle + 1 <=end 保证起始位置应该<=结束位置
    } else if (middle + 1 <= end) {
      return binarySearch(arr, middle + 1, end, target);
    } else {
      return -1;
    }
  }
 
 //非递归实现
 private int binarySearch(int[] arr, int target) {
    int low = 0;
    int hight = arr.length;
    while (low <= hight) {
      int middle = (low + hight) / 2;
      if (target == arr[middle]) {
        return middle;
      } else if (target > arr[middle]) {
        low = middle + 1;
      } else {
        hight = middle - 1;
      }
    }
    return -1;
  }

```

> 快速排序算法

```java
  private int getBasePosition(int[] array, int start, int end) {
    int base = array[start];
    int temp;
    while (start < end) {
      // 1.从右边找到比key小的数
      while (start < end && array[end] >= base) {
        end--;
      }
      // 交换
      if (start < end) {
        temp = array[start];
        array[start] = array[end];
        array[end] = temp;
      }
      // 2.从左边找比key大的数
      while (start < end && array[start] < base) {
        start++;
      }
      // 交换
      if (start < end) {
        temp = array[start];
        array[start] = array[end];
        array[end] = temp;
      }
    }
    // 此时start==end
    return end;

  }

  private void quickSort(int[] array, int start, int end) {
    if (start > end) {
      return;
    } else {
      //1.找到basePosition
      int basePosition = getBasePosition(array, start, end);
      //2.递归排序base左边的子数组
      quickSort(array, start, basePosition - 1);
      //3.递归排序base右边的子数组
      quickSort(array, basePosition + 1, end);
    }
  }

```

串的查找算法

```java
//如果target在source中, 返回target在source中的起始位置(从0算起)，如果不存在，返回-1
    public static int strMatch_1(char[] source, char[] target) {
        int i = 0, j = 0;
        int sourceLength = source.length;
        int targetLength = target.length;
        while (i < sourceLength && j < targetLength ) {
            if (source[i] == target[j]) {
                if (j == targetLength - 1) {
                    return i - targetLength + 1;
                } else {
                    i++;
                    j++;
                }
            } else {
                //如果source中剩余的字符串长度小于target，那么直接返回 -1
                if(i > sourceLength - targetLength) {
                    break;
                } else {
                    j = 0;
                    i = i - j + 1;
                }
            }
        }
        return -1;
    }

    /**
     * 字符串匹配还有KMP算法，BM算法以及Sunday算法
     */

```

#### 参考链接  

[参考链接1](https://www.zhihu.com/question/37601861/answer/145545371)



