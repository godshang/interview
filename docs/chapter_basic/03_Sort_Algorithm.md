# 排序算法

排序算法可以分为内部排序和外部排序，内部排序是数据记录在内存中进行排序，而外部排序是因排序的数据很大，一次不能容纳全部的排序记录，在排序过程中需要访问外存。常见的内部排序算法有：插入排序、希尔排序、选择排序、冒泡排序、归并排序、快速排序、堆排序、基数排序等。用一张图概括：

<img src="https://www.runoob.com/wp-content/uploads/2019/03/sort.png" />

## 冒泡排序

冒泡排序（Bubble Sort）也是一种简单直观的排序算法。它重复地走访过要排序的数列，一次比较两个元素，如果他们的顺序错误就把他们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。

算法步骤：

1. 比较相邻的元素。如果第一个比第二个大，就交换他们两个。
2. 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。这步做完后，最后的元素会是最大的数。
3. 针对所有的元素重复以上的步骤，除了最后一个。
4. 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。

<img src="https://www.runoob.com/wp-content/uploads/2019/03/bubbleSort.gif" />

```java
public void bubbleSort(int[] nums) {
    for (int i = 1; i < nums.length; i++) {
        for (int j = 0; j < nums.length - i; j++) {
            if (nums[j] > nums[j + 1]) {
                swap(nums, j, j + 1);
            }
        }
    }
}
```

## 选择排序

选择排序是一种简单直观的排序算法，无论什么数据进去都是 O(n²) 的时间复杂度。所以用到它的时候，数据规模越小越好。唯一的好处可能就是不占用额外的内存空间了吧。

算法步骤：

1. 首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置。
2. 再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。
3. 重复第二步，直到所有元素均排序完毕。

<img src="https://www.runoob.com/wp-content/uploads/2019/03/selectionSort.gif" />

```java
public void selectSort(int[] nums) {
    for (int i = 0; i < nums.length - 1; i++) {
        int minIdx = i;
        for (int j = i + 1; j < nums.length; j++) {
            if (nums[j] < nums[minIdx]) {
                minIdx = j;
            }
        }
        if (i != minIdx) {
            swap(nums, i, minIdx);
        }
    }
}
```

## 插入排序

插入排序的代码实现虽然没有冒泡排序和选择排序那么简单粗暴，但它的原理应该是最容易理解的了，因为只要打过扑克牌的人都应该能够秒懂。插入排序是一种最简单直观的排序算法，它的工作原理是通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。

插入排序和冒泡排序一样，也有一种优化算法，叫做拆半插入。

算法步骤：

1. 将第一待排序序列第一个元素看做一个有序序列，把第二个元素到最后一个元素当成是未排序序列。
2. 从头到尾依次扫描未排序序列，将扫描到的每个元素插入有序序列的适当位置。
3. 如果待插入的元素与有序序列中的某个元素相等，则将待插入元素插入到相等元素的后面。

<img src="https://www.runoob.com/wp-content/uploads/2019/03/insertionSort.gif" />

```java
public void insertSort(int[] nums) {
    for (int i = 1; i < nums.length; i++) {
        int tmp = nums[i];
        int j = i;
        while (j > 0 && tmp < nums[j - 1]) {
            nums[j] = nums[j - 1];
            j--;
        }
        if (j != i) {
            nums[j] = tmp;
        }
    }
}
```

## 希尔排序

希尔排序，也称递减增量排序算法，是插入排序的一种更高效的改进版本。但希尔排序是非稳定排序算法。

希尔排序是基于插入排序的以下两点性质而提出改进方法的：

* 插入排序在对几乎已经排好序的数据操作时，效率高，即可以达到线性排序的效率；
* 但插入排序一般来说是低效的，因为插入排序每次只能将数据移动一位；

希尔排序的基本思想是：先将整个待排序的记录序列分割成为若干子序列分别进行直接插入排序，待整个序列中的记录"基本有序"时，再对全体记录进行依次直接插入排序。

算法步骤：

1. 选择一个增量序列 t1，t2，……，tk，其中 ti > tj, tk = 1；
2. 按增量序列个数 k，对序列进行 k 趟排序；
3. 每趟排序，根据对应的增量 ti，将待排序列分割成若干长度为 m 的子序列，分别对各子表进行直接插入排序。仅增量因子为 1 时，整个序列作为一个表来处理，表长度即为整个序列的长度。

<img src="https://www.runoob.com/wp-content/uploads/2019/03/Sorting_shellsort_anim.gif" />

```java
public void shellSort(int[] nums) {
    int length = nums.length;
    for (int step = length / 2; step >= 1; step /= 2) {
        for (int i = step; i < length; i++) {
            int temp = nums[i];
            int j = i - step;
            while (j >= 0 && temp < nums[j]) {
                nums[j + step] = nums[j];
                j -= step;
            }
            nums[j + step] = temp;
        }
    }
}
```

## 归并排序

归并排序（Merge sort）是建立在归并操作上的一种有效的排序算法。该算法是采用分治法（Divide and Conquer）的一个非常典型的应用。

作为一种典型的分而治之思想的算法应用，归并排序的实现由两种方法：

* 自上而下的递归（所有递归的方法都可以用迭代重写，所以就有了第 2 种方法）；
* 自下而上的迭代；

<img src="https://www.runoob.com/wp-content/uploads/2019/03/mergeSort.gif" />

```java
public int[] mergeSort(int[] nums) {
    if (nums.length < 2) return nums;
    int middle = (int) Math.floor(nums.length / 2);
    int[] left = Arrays.copyOfRange(nums, 0, middle);
    int[] right = Arrays.copyOfRange(nums, middle, nums.length);
    return merge(mergeSort(left), mergeSort(right));
}

private int[] merge(int[] left, int[] right) {
    int[] res = new int[left.length + right.length];
    int i = 0, l = 0, r = 0;
    while (l < left.length && r < right.length) {
        if (left[l] < right[r]) {
            res[i++] = left[l++];
        } else {
            res[i++] = right[r++];
        }
    }
    while (l < left.length) {
        res[i++] = left[l++];
    }
    while (r < right.length) {
        res[i++] = right[r++];
    }
    return res;
}
```

## 快速排序

快速排序是由东尼·霍尔所发展的一种排序算法。在平均状况下，排序 n 个项目要 Ο(nlogn) 次比较。在最坏状况下则需要 Ο(n2) 次比较，但这种状况并不常见。事实上，快速排序通常明显比其他 Ο(nlogn) 算法更快，因为它的内部循环（inner loop）可以在大部分的架构上很有效率地被实现出来。

快速排序使用分治法（Divide and conquer）策略来把一个串行（list）分为两个子串行（sub-lists）。

快速排序又是一种分而治之思想在排序算法上的典型应用。本质上来看，快速排序应该算是在冒泡排序基础上的递归分治法。

快速排序的名字起的是简单粗暴，因为一听到这个名字你就知道它存在的意义，就是快，而且效率高！它是处理大数据最快的排序算法之一了。虽然 Worst Case 的时间复杂度达到了 O(n²)，但是人家就是优秀，在大多数情况下都比平均时间复杂度为 O(n logn) 的排序算法表现要更好。

算法步骤：

1. 从数列中挑出一个元素，称为 "基准"（pivot）;
2. 重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面（相同的数可以到任一边）。在这个分区退出之后，该基准就处于数列的中间位置。这个称为分区（partition）操作；
3. 递归地（recursive）把小于基准值元素的子数列和大于基准值元素的子数列排序；

<img src="https://www.runoob.com/wp-content/uploads/2019/03/quickSort.gif" />

```java
public void quickSort(int[] nums) {
    quickSort(nums, 0, nums.length - 1);
}

private void quickSort(int[] nums, int left, int right) {
    if (left < right) {
        int i = left, j = right;
        int pivot = nums[left];
        while (i < j) {
            while (i < j && nums[j] >= pivot) {
                j--;
            }
            while (i < j && nums[i] <= pivot) {
                i++;
            }
            if (i < j) {
                swap(nums, i, j);
            }
        }
        nums[left] = nums[i];
        nums[i] = pivot;
        
        quickSort(nums, left, i - 1);
        quickSort(nums, i + 1, right);
    }
}
```

## 堆排序

堆排序（Heapsort）是指利用堆这种数据结构所设计的一种排序算法。堆积是一个近似完全二叉树的结构，并同时满足堆积的性质：即子结点的键值或索引总是小于（或者大于）它的父节点。堆排序可以说是一种利用堆的概念来排序的选择排序。分为两种方法：

1. 大顶堆：每个节点的值都大于或等于其子节点的值，在堆排序算法中用于升序排列；
2. 小顶堆：每个节点的值都小于或等于其子节点的值，在堆排序算法中用于降序排列；

堆排序的平均时间复杂度为 Ο(nlogn)。

算法步骤：

1. 创建一个堆 H[0……n-1]；
2. 把堆首（最大值）和堆尾互换；
3. 把堆的尺寸缩小 1，并调用 shift_down(0)，目的是把新的数组顶端数据调整到相应位置；
4. 重复步骤 2，直到堆的尺寸为 1。

<img src="https://www.runoob.com/wp-content/uploads/2019/03/heapSort.gif" />
<img src="https://www.runoob.com/wp-content/uploads/2019/03/Sorting_heapsort_anim.gif" />

```java
public void heapSort(int[] nums) {
    MaxHeap maxHeap = new MaxHeap(nums);
    for (int i = nums.length - 1; i >= 0; i--) {
        swap(nums, 0, i);
        maxHeap.heapify(0, i);
    }
}

public static class MaxHeap {
    int[] nums;

    public MaxHeap(int[] nums) {
        this.nums = nums;
        buildHeap();
    }

    private void buildHeap() {
        for (int i = (int) Math.floor(nums.length / 2); i >= 0; i--) {
            heapify(i, nums.length);
        }
    }
    
    public void heapify(int i, int len) {
        int left = left(i);
        int right = right(i);
        int largest = i;
        if (left < len && nums[left] > nums[largest]) {
            largest = left;
        }
        if (right < len && nums[right] > nums[largest]) {
            largest = right;
        }
        if (largest != i) {
            swap(i, largest);
            heapify(largest, len);
        }
    }

    private int parent(int i) {
        return (i - 1) / 2;
    }

    private int left(int i) {
        return i * 2 + 1;
    }

    private int right(int i) {
        return i * 2 + 2;
    }

    private void swap(int i, int j) {
        int tmp = nums[i];
        nums[i] = nums[j];
        nums[j] = tmp;
    }
}
```

## 计数排序

计数排序的核心在于将输入的数据值转化为键存储在额外开辟的数组空间中。作为一种线性时间复杂度的排序，计数排序要求输入的数据必须是有确定范围的整数。

计数排序的特征

* 当输入的元素是 n 个 0 到 k 之间的整数时，它的运行时间是 Θ(n + k)。计数排序不是比较排序，排序的速度快于任何比较排序算法。
* 由于用来计数的数组C的长度取决于待排序数组中数据的范围（等于待排序数组的最大值与最小值的差加上1），这使得计数排序对于数据范围很大的数组，需要大量时间和内存。例如：计数排序是用来排序0到100之间的数字的最好的算法，但是它不适合按字母顺序排序人名。但是，计数排序可以用在基数排序中的算法来排序数据范围很大的数组。

通俗地理解，例如有 10 个年龄不同的人，统计出有 8 个人的年龄比 A 小，那 A 的年龄就排在第 9 位,用这个方法可以得到其他每个人的位置,也就排好了序。当然，年龄有重复时需要特殊处理（保证稳定性），这就是为什么最后要反向填充目标数组，以及将每个数字的统计减去 1 的原因。

算法的步骤如下：

1. 找出待排序的数组中最大和最小的元素
2. 统计数组中每个值为i的元素出现的次数，存入数组C的第i项
3. 对所有的计数累加（从C中的第一个元素开始，每一项和前一项相加）
4. 反向填充目标数组：将每个元素i放在新数组的第C(i)项，每放一个元素就将C(i)减去1

<img src="https://www.runoob.com/wp-content/uploads/2019/03/countingSort.gif" />

```java
public void countingSort(int[] nums) {
    int max = getMaxValue(nums);
    
    int[] bucket = new int[max + 1];
    for (int num : nums) {
        bucket[num]++;
    }
    
    int idx = 0;
    for (int i = 0; i <= max; i++) {
        while (bucket[i] > 0) {
            nums[idx++] = i;
            bucket[i]--;
        }
    }
}
```

## 桶排序

桶排序是计数排序的升级版。它利用了函数的映射关系，高效与否的关键就在于这个映射函数的确定。为了使桶排序更加高效，我们需要做到这两点：

1. 在额外空间充足的情况下，尽量增大桶的数量
2. 使用的映射函数能够将输入的 N 个数据均匀的分配到 K 个桶中

同时，对于桶中元素的排序，选择何种比较排序算法对于性能的影响至关重要。

当输入的数据可以均匀的分配到每一个桶中时最快，当输入的数据被分配到了同一个桶中时最慢。

<img src="https://www.runoob.com/wp-content/uploads/2019/03/Bucket_sort_1.svg_.png" />

```java
public void bucketSort(int[] nums) {
    int bucketSize = 5; // 桶大小
    
    int minValue = nums[0], maxValue = nums[0];
    for (int num : nums) {
        if (num < minValue) minValue = num;
        else if (num > maxValue) maxValue = num;
    }
    
    int bucketCount = (int) Math.floor((maxValue - minValue) / bucketSize) + 1;
    int[][] buckets = new int[bucketCount][0];
    
    for (int i = 0; i < nums.length; i++) {
        int index = (int) Math.floor((nums[i] - minValue) / bucketSize);
        buckets[index] = arrAppend(buckets[index], nums[i]);
    }
    
    int idx = 0;
    for (int[] bucket : buckets) {
        if (bucket.length <= 0) continue;
        insertSort(bucket);
        for (int value : bucket) {
            nums[idx++] = value;
        }
    }
}

private int[] arrAppend(int[] arr, int value) {
    arr = Arrays.copyOf(arr, arr.length + 1);
    arr[arr.length - 1] = value;
    return arr;
}
```

## 计数排序

基数排序是一种非比较型整数排序算法，其原理是将整数按位数切割成不同的数字，然后按每个位数分别比较。由于整数也可以表达字符串（比如名字或日期）和特定格式的浮点数，所以基数排序也不是只能使用于整数。

基数排序、计数排序、桶排序这三种排序算法都用到了桶，但对桶的使用方法上有明显差异：

* 基数排序：根据键值的每位数字来分配桶。
* 计数排序：每个桶只存储单一键值。
* 桶排序：每个桶存储一定范围的数值。

<img src="https://www.runoob.com/wp-content/uploads/2019/03/radixSort.gif" />

```java
public void radixSort(int[] nums) {
    int maxDigit = getMaxDigit(nums);
    
    int mod = 10, dev = 1;
    for (int i = 0; i < maxDigit; i++, mod *= 10, dev *= 10) {
        int[][] counter = new int[mod * 2][0];
        
        for (int j = 0; j < nums.length; j++) {
            int bucket = ((nums[j] % mod) / dev) + mod;
            counter[bucket] = arrAppend(counter[bucket], nums[j]);
        }
        
        int idx = 0;
        for (int[] bucket : counter) {
            for (int value : bucket) {
                nums[idx++] = value;
            }
        }
    }
}

private int getMaxDigit(int[] nums) {
    int maxVlaue = getMaxValue(nums);
    int length = getNumberLength(maxVlaue);
    return length;
}

private int getMaxValue(int[] nums) {
    int max = nums[0];
    for (int num : nums) {
        if (num > max) max = num;
    }
    return max;
}

private int getNumberLength(int num) {
    if (num == 0) return 0;
    int length = 0;
    while (num != 0) {
        length++;
        num /= 10;
    }
    return length;
}

private int[] arrAppend(int[] arr, int value) {
    arr = Arrays.copyOf(arr, arr.length + 1);
    arr[arr.length - 1] = value;
    return arr;
}
```