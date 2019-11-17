---
layout: index
title: "Algorithm"
category: java
date: 2018-02-22 11:03:55
---

# Common algorithm


> 压缩算法

```java
  public static void unzip(File zipFile, String destPath) throws Exception {
    File dest = new File(destPath);
    if (!dest.exists()) {
      dest.mkdirs();
    }
    if (!zipFile.exists()) {
      return;
    }
    FileInputStream fileInputStream = new FileInputStream(zipFile);
    ZipInputStream zipInputStream = new ZipInputStream(fileInputStream);
    ZipEntry zipEntry = zipInputStream.getNextEntry();
    byte[] buffer = new byte[8092];
    while (zipEntry != null) {
      String fileName = zipEntry.getName();
      File newFile = new File(destPath + File.separator + fileName);
      new File(newFile.getParent()).mkdirs();
      FileOutputStream fileOutputStream = new FileOutputStream(newFile);
      int len = 0;
      while ((len = zipInputStream.read(buffer)) > 0) {
        fileOutputStream.write(buffer, 0, len);
      }
      fileOutputStream.close();
      zipInputStream.closeEntry();
      zipEntry = zipInputStream.getNextEntry();
    }
    zipInputStream.closeEntry();
    zipInputStream.close();
    fileInputStream.close();
  }

  public static void zipDir(File dir, String zipFilePath) throws Exception {
    FileOutputStream fileOutputStream = new FileOutputStream(zipFilePath);
    ZipOutputStream zipOutputStream = new ZipOutputStream(fileOutputStream);
    compress(dir, zipOutputStream, "");
    zipOutputStream.close();
    fileOutputStream.close();
  }

  private static void compress(File file, ZipOutputStream zipOutputStream, String baseDir) {
    if (file.isFile()) {
      FileUtil.compressFile(file, zipOutputStream, baseDir);
    } else {
      if (file.exists()) {
        File[] children = file.listFiles();
        if (children != null && children.length > 0) {
          Stream.of(children)
              .forEach(child -> {
                FileUtil.compress(child, zipOutputStream,
                    baseDir + file.getName() + File.separator);
              });
        }
      }
    }
  }

  private static void compressFile(File file, ZipOutputStream zipOutputStream, String basedir) {
    if (!file.exists()) {
      return;
    }
    int buffer = 8192;
    try {
      BufferedInputStream bufferedInputStream = new BufferedInputStream(
          new FileInputStream(file));
      ZipEntry entry = new ZipEntry(basedir + file.getName());
      zipOutputStream.putNextEntry(entry);
      int count;
      byte data[] = new byte[buffer];
      while ((count = bufferedInputStream.read(data, 0, buffer)) != -1) {
        zipOutputStream.write(data, 0, count);
      }
      bufferedInputStream.close();

    } catch (Exception e) {
      logger.error("Compress File Error!", e);
    }
  }

```

> 归并排序算法

```java
  public static void mergeSort(int array[], int start, int end) {
      if (start == end) {
          //分解的最终结果，每个数组只有一个元素
          return;
      }
      int middle = start + ((end - start) >> 1);
      mergeSort(array, start, middle);
      mergeSort(array, middle + 1, end);
      merge(array, start, middle, end);
  }


  /**
    *
    * @param array
    * @param start
    * @param middle
    * @param end
    *
    */
  private static void merge(int array[], int start, int middle, int end) {
      int[] help = new int[end - start + 1];
      int p1 = start;
      int p2 = middle + 1;
      int i = 0;
      //另个数组逐次比较，先将较小的数归位。  例如产生的递增的中间产物数组，[5, 10], [6,7]，经过该循环后help = {5, 6, 7}
      while (p1 <= middle && p2 <= end) {
          help[i++] = array[p1] < array[p2] ? array[p1++] : array[p2++];
      }

      //以下的两个while只会执行其中一个
      while (p1 <= middle) {
          help[i++] = array[p1++];
      }
      while (p2 <= end) {
          help[i++] = array[p2++];
      }

      for (int j = 0; j < help.length; j++) {
          //将排序了的结果放入数组
          array[start + j] = help[j];
      }

  }


```

> 快速排序算法

```java
  private int getBasePosition(int array[], int L, int R) {
    int base = array[L];
    int temp, i, j;
    i = L;
    j = R;
    while (i < j) {
      // 1.从右边找到比base小的数
      while (i < j && base <= array[j])
        j--;
      // 2.从左边找到比base大的数
      while (i < j && array[i] <= base)
        i++;
      //交换位置
      if (i < j) {
        temp = array[i];
        array[i] = array[j];
        array[j] = temp;
      }
    }
    //当i==j时，需要将array[i]的值与array[L]的值进行交换，因此引入变量i,j。如果直接使用L，R，那么当L==R的时候，base在数组中的初始位置L就会丢失。
    temp = array[i];
    array[i] = array[L];
    array[L] = temp;
    return i;
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

> 串的查找算法

```java
//如果target在source中, 返回target在source中的起始位置(从0算起)，如果不存在，返回-1
    public static int strMatch_1(char[] source, char[] target) {
        int i = 0, j = 0;
        int sourceLength = source.length;
        int targetLength = target.length;
        while (i < sourceLength && j < targetLength ) {
            if (source[i] == target[j]) {
                if (j == targetLength - 1) {
                    return i - j;
                } else {
                    i++;
                    j++;
                }
            } else {
                //如果source中剩余的字符串长度小于target，那么直接返回 -1
                if(sourceLength  < i + targetLength) {
                    break;
                } else {
                    j = 0;
                    //主串需要移动到下一位，然后和字串再次匹配
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





