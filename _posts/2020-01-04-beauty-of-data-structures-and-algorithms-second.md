---
layout: post
title: 《数据结构与算法之美》笔记二
date: 2020-01-04 21:59:56 +0800
tags: [阅读笔记, Algorithm]
categories: [Code, Algorithm]
---

递归
[Recursion - LeetCode](https://leetcode.com/tag/recursion/)

对于重复子问题，可以使用备忘录优化。

## 排序
### 冒泡排序
原地排序，稳定的排序算法。

冒泡排序算法的运作如下：
1. 比较相邻的元素。如果第一个比第二个大，就交换他们两个。
2. 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。这步做完后，最后的元素会是最大的数。
3. 针对所有的元素重复以上的步骤，除了最后一个。
4. 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。

```Ruby
class Array
  def bubble_sort!
    (0...(size - 1)).each do |i|
      (0...(size - i - 1)).each do |j|
        self[j], self[j + 1] = self[j + 1], self[j] if self[j] > self[j + 1]
      end
    end
    self
  end
end
```

最坏时间复杂度 O(n^2)
最优时间复杂度 O(n^2)
平均时间复杂度 O(n^2)
最坏空间复杂度 O(n) 需要辅助空间 O(1) 

### 插入排序

核心：通过构建有序序列，对于未排序序列，从后向前扫描(对于单向链表则只能从前往后遍历)，找到相应位置并插入。实现上通常使用in-place排序(需用到O(1)的额外空间)
1. 从第一个元素开始，该元素可认为已排序
2. 取下一个元素，对已排序数组从后往前扫描
3. 若从排序数组中取出的元素大于新元素，则移至下一位置
4. 重复步骤3，直至找到已排序元素小于或等于新元素的位置
5. 插入新元素至该位置
6. 重复2~5
性质：
* 交换操作和数组中导致的数量相同
* 比较次数>=倒置数量，<=倒置的数量加上数组的大小减一
* 每次交换都改变了两个顺序颠倒的元素的位置，即减少了一对倒置，倒置数量为0时即完成排序。
* 每次交换对应着一次比较，且1到N-1之间的每个i都可能需要一次额外的记录(a[i]未到达数组左端时)
* 最坏情况下需要 ~N^2/2 次比较和 ~N^2/2次交换，最好情况下需要N-1次比较和0次交换。
* 平均情况下需要 ~N^2/4 次比较 和 ~N^2/4次交换

```ruby
class Array
  def insert_sort!
    n = size
    return self if n <= 1

    (1...n).each do |i|
      i.downto(1).each do |j|
        if self[j] < self[j - 1]
          self[j], self[j - 1] = self[j - 1], self[j]
        else
          break
        end
      end
    end
    self
  end
end
```

如果目标是把n个元素的序列升序排列，那么采用**插入排序**存在最好情况和最坏情况。最好情况就是，序列已经是升序排列了，在这种情况下，需要进行的比较操作需 n - 1 次即可。最坏情况就是，序列是降序排列，那么此时需要进行的比较共有 n(n -1) / 2 次。**插入排序**的赋值操作是比较操作的次数减去 n - 1 次，（因为 n - 1 次循环中，每一次循环的比较都比赋值多一个，多在最后那一次比较并不带来赋值）。平均来说**插入排序**算法复杂度为 O(n^2) 。因而，**插入排序**不适合对于数据量比较大的排序应用。但是，如果需要排序的数据量很小，例如，量级小于千；或者若已知输入元素大致上按照顺序排列，那么**插入排序**还是一个不错的选择。 插入排序在工业级库中也有着广泛的应用，在STL的sort算法和stdlib的qsort算法中，都将插入排序作为快速排序的补充，用于少量元素的排序（通常为8个或以下）。

### 选择排序
**选择排序**（Selection sort）是一种简单直观的 [排序算法](https://zh.wikipedia.org/wiki/%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95) 。它的工作原理如下。首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕。
选择排序的主要优点与数据移动有关。如果某个元素位于正确的最终位置上，则它不会被移动。选择排序每次交换一对元素，它们当中至少有一个将被移到其最终位置上，因此对 n 个元素的表进行排序总共进行至多 n - 1 次交换。在所有的完全依靠交换去移动元素的排序方法中，选择排序属于非常好的一种。

```ruby
class Array
  def selection_sort!
    (0...(size - 1)).each do |i|
      min_index = i
      (i...(size - 1)).each do |j|
        min_index = j if self[j] < self[min_index]
      end
      self[min_index], self[i] = self[i], self[min_index]
    end
    self
  end
end
```

选择排序的**交换操作**介于 0 和 n - 1 次之间。选择排序的**比较操作**为 n(n - 1)/2 次。选择排序的**赋值操作**介于 0 和 3(n - 1) 次之间。

比较次数 O(n^2) ，比较次数与关键字的初始状态无关，总的比较次数 N = (n - 1) + (n - 2) + … + 1 = n * (n - 1)/2。交换次数 O(n)，最好情况是，已经有序，交换0次；最坏情况是，逆序，交换 n - 1次。交换次数比冒泡排序较少，由于交换所需CPU时间比比较所需的CPU时间多，n 值较小时，选择排序比冒泡排序快。
原地操作几乎是选择排序的唯一优点，当空间复杂度要求较高时，可以考虑选择排序；实际适用的场合非常罕见。

最坏时间复杂度：О(n²)
最优时间复杂度：О(n²)
平均时间复杂度：О(n²)

### 归并排序

**归并排序**（英语：Merge sort，或mergesort），是创建在归并操作上的一种有效的 [排序算法](https://zh.wikipedia.org/wiki/%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95) ， [效率](https://zh.wikipedia.org/wiki/%E6%97%B6%E9%97%B4%E5%A4%8D%E6%9D%82%E5%BA%A6) 为 O(n log n)（ [大O符号](https://zh.wikipedia.org/wiki/%E5%A4%A7O%E7%AC%A6%E5%8F%B7) ）。1945年由 [约翰·冯·诺伊曼](https://zh.wikipedia.org/wiki/%E7%BA%A6%E7%BF%B0%C2%B7%E5%86%AF%C2%B7%E8%AF%BA%E4%BC%8A%E6%9B%BC) 首次提出。该算法是采用 [分治法](https://zh.wikipedia.org/wiki/%E5%88%86%E6%B2%BB%E6%B3%95) （Divide and Conquer）的一个非常典型的应用，且各层分治递归可以同时进行。

采用分治法:
* 分割：递归地把当前序列平均分割成两半。
* 集成：在保持元素顺序的同时将上一步得到的子序列集成到一起（归并）。

```ruby
def merge(array, low, mid, high)
  i = low
  j = mid + 1
  aux = []
  (low..high).each do |k|
    aux[k] = array[k]
  end

  (low..high).each do |k|
    if i > mid
      # low 到 mid 部分已完全插入到 array 中
      array[k] = aux[j]
      j += 1
    elsif j > high
      # mid + 1 到 high 部分已完全插入到 array 中
      array[k] = aux[i]
      i += 1
    elsif aux[j] < aux[i]
      # 两者比较取最小值
      array[k] = aux[j]
      j += 1
    else
      array[k] = aux[i]
      i += 1
    end
  end
end
```

归并操作（merge），也叫归并算法，指的是将两个已经排序的序列合并成一个序列的操作。归并排序算法依赖归并操作。

**递归法（Top-down）**
1. 申请空间，使其大小为两个已经排序序列之和，该空间用来存放合并后的序列
2. 设定两个指针，最初位置分别为两个已经排序序列的起始位置
3. 比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动指针到下一位置
4. 重复步骤3直到某一指针到达序列尾
5. 将另一序列剩下的所有元素直接复制到合并序列尾

```ruby
# Top - Down
def merge_sort_recursive(array, low, high)
  return if high <= low

  mid = low + (high - low) / 2
  merge_sort_recursive(array, low, mid)
  merge_sort_recursive(array, mid + 1, high)
  merge(array, low, mid, high)
  array
end
```

**迭代法（Bottom-up）**
原理如下（假设序列共有 n 个元素）：
1. 将序列每相邻两个数字进行归并操作，形成 ceil(n/2) 个序列，排序后每个序列包含两/一个元素
2. 若此时序列数不是1个则将上述序列再次归并，形成 ceil(n/4) 个序列，每个序列包含四/三个元素
3. 重复步骤2，直到所有元素排序完毕，即序列数为1

```ruby
def merge_sort(array)
  sub_size = 1
  size = array.size
  while sub_size < size
    low = 0
    while low < size - sub_size
      merge(array, low, low + sub_size - 1, [low + sub_size * 2 - 1, size - 1].min)
      low += sub_size * 2
    end
    sub_size += sub_size
  end
  array
end
```

最坏时间复杂度：О(n log n)
最优时间复杂度：О(n log n)
平均时间复杂度：О(n log n)
最坏空间复杂度：О(n)

### 快速排序

快速排序使用 [分治法](https://zh.wikipedia.org/wiki/%E5%88%86%E6%B2%BB%E6%B3%95) （Divide and conquer）策略来把一个 [序列](https://zh.wikipedia.org/wiki/%E5%BA%8F%E5%88%97) （list）分为较小和较大的2个子序列，然后递归地排序两个子序列。
步骤为：
1. 挑选基准值：从数列中挑出一个元素，称为“基准”（pivot），
2. 分割：重新排序数列，所有比基准值小的元素摆放在基准前面，所有比基准值大的元素摆在基准后面（与基准值相等的数可以到任何一边）。在这个分割结束之后，对基准值的排序就已经完成，
3. 递归排序子序列： [递归](https://zh.wikipedia.org/wiki/%E9%80%92%E5%BD%92) 地将小于基准值元素的子序列和大于基准值元素的子序列排序。
递归到最底部的判断条件是数列的大小是零或一，此时该数列显然已经有序。

```
 function quicksort(q)
 {
     var list less, pivotList, greater
     if length(q) ≤ 1 
         return q
     else 
     {
         select a pivot value pivot from q
         for each x in q except the pivot element
         {
             if x < pivot then add x to less
             if x ≥ pivot then add x to greater
         }
         add pivot to pivotList
         return concatenate(quicksort(less), pivotList, quicksort(greater))
     }
 }
```

### 原地分割 （in-place）版本
上面简单版本的缺点是，它需要 O(n) 的额外存储空间，也就跟 [归并排序](https://zh.wikipedia.org/wiki/%E5%BD%92%E5%B9%B6%E6%8E%92%E5%BA%8F) 一样不好。额外需要的 [存储器](https://zh.wikipedia.org/wiki/%E8%A8%98%E6%86%B6%E9%AB%94) 空间配置，在实际上的实现，也会极度影响速度和 [缓存](https://zh.wikipedia.org/wiki/%E5%BF%AB%E5%8F%96) 的性能。有一个比较复杂使用 [原地](https://zh.wikipedia.org/wiki/%E5%8E%9F%E5%9C%B0%E7%AE%97%E6%B3%95) （in-place）分割 [算法](https://zh.wikipedia.org/wiki/%E7%AE%97%E6%B3%95) 的版本，且在好的基准选择上，平均可以达到 O(log n) 空间的使用复杂度。

```
function partition(a, left, right, pivotIndex)
{
     pivotValue = a[pivotIndex]
     swap(a[pivotIndex], a[right]) // 把pivot移到結尾
     storeIndex = left
     for I from left to right-1
     {
         if a[I] < pivotValue
          {
             swap(a[storeIndex], a[I])
             storeIndex = storeIndex + 1
          }
     }
     swap(a[right], a[storeIndex]) // 把pivot移到它最後的地方
     return storeIndex
 }
```

这是原地分割算法，它分割了标示为”左边（left）”和”右边（right）”的序列部分，借由移动小于a[pivotIndex]的所有元素到子序列的开头，留下所有大于或等于的元素接在他们后面。在这个过程它也为基准元素找寻最后摆放的位置，也就是它回传的值。它暂时地把基准元素移到子序列的结尾，而不会被前述方式影响到。由于算法只使用交换，因此最后的数列与原先的数列拥有一样的元素。要注意的是，一个元素在到达它的最后位置前，可能会被交换很多次。

1. 归并排序将数组分成两个子数组分别排序，并将有序的子数组归并以将整个数组排序。递归调用发生在处理整个数组之前。
2. 快速排序将一个数组分成两个子数组并对这两个子数组独立地排序，两个子数组有序时整个数组也就有序了。递归调用发生在处理整个数组之后。

### 归并排序与快速排序比较

归并排序是由下到上，先处理子问题，然后再合并。快排则是由上到下，先分区，再处理子问题。归并排序虽然是稳定的排序算法，但是不是原地排序算法，需要 O(n) 的空间。快排则是原地，不稳定的算法。

快排找第 K 大的数。
[Kth Largest Element in an Array - LeetCode](https://leetcode.com/problems/kth-largest-element-in-an-array/)

[Sort - LeetCode](https://leetcode.com/tag/sort/)
