---
layout: index
title: "Algorithm"
category: java
date: 2018-02-22 11:03:55
---

# Common algorithm


> 文件大小为2G，统计文件中出现次数为top100的单词，单词以空格分开。JVM限制使用2M，最后输入格式从小到大排列打印，例如 hello: 100, world: 99, java: 98

```java
public class CountWords {

private static final int CHUNK_SIZE = 1024 * 1024; // 1MB chunk size
    private static final int TOP_WORDS_LIMIT = 10000;
    private static final int MEMORY_THRESHOLD = 500000; // Flush to disk after 500,000 unique words

    public static void main(String[] args) throws IOException {
        String filePath = "C:\\Users\\xiao fei\\Desktop\\words.txt.txt";
        List<File> intermediateFiles = new ArrayList<>();
        Map<String, Integer> wordCounts = new HashMap<>();

        // Process the file in chunks
        try (BufferedReader reader = new BufferedReader(new FileReader(filePath))) {
            char[] buffer = new char[CHUNK_SIZE];
            int bytesRead;
            StringBuilder leftover = new StringBuilder();

            while ((bytesRead = reader.read(buffer)) != -1) {
                String chunk = leftover + new String(buffer, 0, bytesRead);
                int lastSpace = chunk.lastIndexOf(' ');
                if (lastSpace == -1) {
                    leftover.setLength(0);
                    leftover.append(chunk);
                    continue;
                }

                String processableChunk = chunk.substring(0, lastSpace);
                leftover.setLength(0);
                leftover.append(chunk.substring(lastSpace + 1));

                // Count words in the current chunk
                String[] words = processableChunk.split("\\s+");
                for (String word : words) {
                    wordCounts.put(word, wordCounts.getOrDefault(word, 0) + 1);
                }

                // Flush to disk if memory threshold is reached
                if (wordCounts.size() >= MEMORY_THRESHOLD) {
                    File tempFile = flushToDisk(wordCounts);
                    intermediateFiles.add(tempFile);
                    wordCounts.clear();
                }
            }

            // Process the remaining leftover
            if (leftover.length() > 0) {
                String[] words = leftover.toString().split("\\s+");
                for (String word : words) {
                    wordCounts.put(word, wordCounts.getOrDefault(word, 0) + 1);
                }
            }

            // Flush any remaining word counts to disk
            if (!wordCounts.isEmpty()) {
                File tempFile = flushToDisk(wordCounts);
                intermediateFiles.add(tempFile);
                wordCounts.clear();
            }
        }

        // Merge intermediate files to get the top 10,000 words
        Map<String, Integer> finalWordCounts = mergeIntermediateFiles(intermediateFiles);

        // Find the top 10,000 words
        PriorityQueue<Map.Entry<String, Integer>> topWords = new PriorityQueue<>(
                Comparator.comparingInt(Map.Entry::getValue)
        );

        for (Map.Entry<String, Integer> entry : finalWordCounts.entrySet()) {
            topWords.offer(entry);
            if (topWords.size() > TOP_WORDS_LIMIT) {
                topWords.poll();
            }
        }

        // Print the top 10,000 words
        List<Map.Entry<String, Integer>> result = new ArrayList<>();
        while (!topWords.isEmpty()) {
            result.add(topWords.poll());
        }
        Collections.reverse(result);

        for (Map.Entry<String, Integer> entry : result) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }

        // Clean up intermediate files
        for (File file : intermediateFiles) {
            file.delete();
        }
    }

    private static File flushToDisk(Map<String, Integer> wordCounts) throws IOException {
        File tempFile = File.createTempFile("wordCounts", ".txt");
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(tempFile))) {
            for (Map.Entry<String, Integer> entry : wordCounts.entrySet()) {
                writer.write(entry.getKey() + " " + entry.getValue());
                writer.newLine();
            }
        }
        return tempFile;
    }

    private static Map<String, Integer> mergeIntermediateFiles(List<File> intermediateFiles) throws IOException {
        Map<String, Integer> finalWordCounts = new HashMap<>();
        for (File file : intermediateFiles) {
            try (BufferedReader reader = new BufferedReader(new FileReader(file))) {
                String line;
                while ((line = reader.readLine()) != null) {
                    String[] parts = line.split(" ");
                    String word = parts[0];
                    int count = Integer.parseInt(parts[1]);
                    finalWordCounts.put(word, finalWordCounts.getOrDefault(word, 0) + count);
                }
            }
        }
        return finalWordCounts;
    }
}

```


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

> 插入排序

```java
public static void insertSort(int[] arr) {
    // 从第二个元素开始排序，假设第一个元素已排序
    for (int i = 1; i < arr.length; i++) {
        int current = arr[i];
        int lastSortPos = i - 1;
        while (lastSortPos >= 0 && arr[lastSortPos] > current) {
            //将大于current的元素右移一位
            arr[lastSortPos + 1] = arr[lastSortPos];
            lastSortPos--;
        }
        // lastSortPos 记录的是排序好的最后一个元素的下标，所以current的位置是lastSortPos + 1
        arr[lastSortPos + 1] = current;
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
public static void sort(int[] arr, int low, int high) {
        if (low >= high) return;
        int pos = partition(arr, low, high);
        sort(arr, low, pos - 1);
        sort(arr, pos + 1, high);
    }

public static int partition(int[] arr, int low, int high) {
    int key = arr[low];
    int left = low;
    int right = high;
    while (left < right) {
        while (left < right && arr[right] >= key) right--;
        while (left < right && arr[left] <= key) left++;
        if (left < right) {
            int temp = arr[right];
            arr[right] = arr[left];
            arr[left] = temp;
        }
    }
    //left == right, return pos and swap key and arr[left]
    int temp = arr[left];
    arr[left] = key;
    arr[low] = temp;
    return left;
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
  public static int strMatch_1(char[] source, char[] target) {
    int i = 0, j = 0;
    int sourceLength = source.length;
    int targetLength = target.length;
    while (i < sourceLength && j < targetLength) {
      if (source[i] == target[j]) {
        if (j == targetLength - 1) {
          return i - j;
        } else {
          i++;
          j++;
        }
      } else {
        i = i - j + 1;
        j = 0;
      }
    }
    return -1;
  }
}
    /**
     * 字符串匹配还有KMP算法，BM算法以及Sunday算法
     */

```

#### 参考链接
- https://leetcode.cn/problem-list/2cktkvj/





