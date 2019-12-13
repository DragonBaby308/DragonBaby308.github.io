---
title: 算法专题：排序
author: DragonBaby308
avatar: 'https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/header/db3Avatar.jpeg'
authorLink: www.dragonbaby308.com
authorAbout:
authorDesc:
categories: 算法
comments: true
date: 2019-08-16 20:24:26
tags:
- 算法
- 排序
- 位运算
- 分治法
- 递归
- 冒泡排序
- 选择排序
- 插入排序
- 希尔排序
- 归并排序
- 堆排序
- 基数排序
- 计数排序
- 桶排序
- 快排
- 图
- 拓扑排序
- BFS
- 队列
- 彩虹排序
- 双指针
- ArrayList
- Arrays
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/posts/sort.jpg
---

#  排序

---

##  稳定性

一个排序算法的稳定性是指，如果**a=b**，排序前a在b前面，那么排序后a也要在b的前面。即**排序后元素的相对位置不变**。  

简单来说：
* **如果一个排序算法会将某个元素单独提到某个位置，那么它就是不稳定的**；
* **如果一个排序算法只涉及两两相邻元素之间的交换，那么它就是稳定的**。

1. 稳定排序：`冒泡排序`、`插入排序`、`希尔排序`、`归并排序`
2. 不稳定排序：`选择排序`、`快排`、`堆排序`

---

##  1.选择排序（`O(N^2)`）

* 选择排序（Selection Sort）每次排序时，从未排序序列中“选择”一个元素加入排序序列，因此得名。  
* 选择排序需要两次遍历 —— 第一遍是遍历所有元素，确认已排序的序列范围[0,i)；第二遍是**遍历所有未排序序列[i,lastIndex]，每次将当前位置与队首（i）元素比较，选择最小元素，交换到队首**，所以选择排序的时间复杂度是`O(N^2)`
* 选择排序是**不稳定排序**，因为存在将元素单独提到某个位置的情况

```java
//选择排序
public static int[] selectionSort(int[] in) {
    int lastIndex = in.length - 1;  //即：循环遍历次数
    for (int i = 0; i <= lastIndex; i++) {  //每次遍历，将最小元素浮动到最左边
        //0~i是已排序序列，i~lastIndex是未排序序列，将未排序中的最小元素放在i位置
        for(int j = i; j <= lastIndex; j++) {
            //默认in[i]是最小元素，如果in[j]小于in[i]，则交换
            if (in[j] < in[i]) {    //如果i=j，那么in[i]=in[j]
                //所以可以保证in[i]、in[j]是不同对象，可以通过异或进行交换
                in[i] = in[i] ^ in[j];
                in[j] = in[i] ^ in[j];
                in[i] = in[i] ^ in[j];
            }
        }
    }
    return in;
}
```

> 选择排序 & 插入排序 的区别：  
**选择排序是每次从未排序序列中选择最小/最大的元素，加入已排序序列**；  
**插入排序是每次按顺序从未排序序列中选择一个元素，加入已排序序列中的合适位置**；  
**也就是说，选择排序遍历的未排序序列，插入排序遍历的已排序序列**。

---

##  2.冒泡排序（`O(N^2)`）


* 冒泡排序（Bubble Sort）每次排序时，将未排序序列最大元素“浮动”到尾部，因此得名。  
* 冒泡排序需要两次遍历 —— 第一遍是遍历所有元素，确认已排序的序列范围[lastIndex - i, lastIndex]；第二遍是**遍历所有未排序序列（0, lastIndex - i），相邻元素两两比较，将最大元素浮动到队尾**，所以冒泡排序的时间复杂度是`O(N^2)`
* 冒泡排序是**稳定排序**，因为只涉及相邻元素两两比较

```java
//冒泡排序
public static int[] bubbleSort(int[] in) {
    int lastIndex = in.length - 1;
    for (int i = 0; i <= lastIndex; i++) {
        //[lastIndex - i,lastIndex]是排好序的序列
        //[0,lastIndex - i)是未排好序的序列
        for(int j = 0; j <= lastIndex - i - 1; j++) {
            //与选择排序不同，冒泡排序是两两相邻元素比较后交换
            //将较大的交换到后面
            if (in[j + 1] < in[j]) {    //不同元素异或交换
                in[j + 1] = in[j] ^ in[j + 1];
                in[j] = in[j] ^ in[j + 1];
                in[j + 1] = in[j] ^ in[j + 1];
            }
        }
    }
    return in;
}
```

---

###  2.1 冒泡排序优化：交换标志位

对冒泡排序的优化是加入一个**交换标志位，如果某一次循环中没有进行数据交换，说明剩余的序列已经是有序的了，可以不再进行后续的循环**。

```java
//优化冒泡排序——加入交换标志位
public static int[] flagBubbleSort(int[] in) {
    //标志位：本趟排序是否进行了交换
    //如果本趟排序没有进行元素交换，说明已经有序了，可以提前结束循环
    boolean exchange;
    int lastIndex = in.length - 1;
    for (int i = 0; i <= lastIndex; i++) {
        exchange = false;
        //[lastIndex - i,lastIndex]是排好序的序列
        //[0,lastIndex - i)是未排好序的序列
        for(int j = 0; j <= lastIndex - i - 1; j++) {
            //与选择排序不同，冒泡排序是两两相邻元素比较后交换
            //将较大的交换到后面
            if (in[j + 1] < in[j]) {    //不同元素异或交换
                in[j + 1] = in[j] ^ in[j + 1];
                in[j] = in[j] ^ in[j + 1];
                in[j + 1] = in[j] ^ in[j + 1];
                exchange = true;    //发生了交换
            }
        }
        if(!exchange) break;    //无交换直接退出循环
    }
    return in;
}
```

---

##  3.插入排序（`O(N^2)`）

> 在形容插入排序时，一般都会类比摸扑克。你的手牌就是已排序序列，牌堆就是未排序序列，每次摸牌时会选择手排中的合适位置插入。  
我的习惯就是强迫按顺序插好牌 - - ||| ，后来我才发现其他孩子摸牌根本不会像我一样，他们都是一把抓在手里面的，所以每次玩扑克他们都要等我抓好牌=====(￣▽￣*)b

* 插入排序（Insertion Sort）的原理就是将序列分为两个部分，一部分是已排序序列，另一部分是未排序序列，每次从未排序序列中选择一个元素插入已排序序列中的合适位置
* 插入排序需要两次循环——第一次是遍历所有元素，确定已排序序列[0,i)；第二次是**遍历所有已排序序列[0,i)，将位置i的元素插入已排序序列的合理位置**——由于遍历的序列是已经排好序的，所以这一步只需要用到一个临时变量。所以插入排序的时间复杂度是`O(N^2)`
* **使用一个临时变量`temp`存储位置i的元素。从j=i位置开始向前遍历，如果`in[j] > temp`，那么`j--`；如果`in[j] <= temp`，那么交换j位置元素和`temp`，然后`j--`，继续比较`in[j]`和`temp`**。
* 插入排序是**稳定性排序**，因为只涉及到相邻元素两两比较

```java
    //插入排序
    public static int[] insertionSort(int[] in) {
        int lastIndex = in.length - 1;
        //从1开始遍历，是默认最开始的已排序序列就是第一个元素，index=0
        for (int i = 1; i <= lastIndex; i++) {
            int temp = in[i];   //待比较元素
            //注意i不能比1小，否则就会数组越界
            while (i >= 1 && in[i - 1] > temp) {  //已排序序列[0,i)，最大下标i - 1
                //i - 1位置元素大于待比较元素，i - 1位置元素后移
                in[i] = in[i - 1];
                i--;
            }
            //退出循环说明找到了合适的位置
            in[i] = temp;
        }
        return in;
    }
```

---

###  3.1 希尔排序（`O(NlogN)`）/缩小增量排序

* 希尔排序（Shell Sort）是一种改进的插入排序，也成为缩小增量排序，1959年由Donald Shell提出。**希尔排序采用了分治的思想，将原序列分为若干个子序列，对子序列分别进行插入排序，所以它的时间复杂度也理所应当是`O(NlogN)`**
* **初始增量`gap = len / 2`，随后每次循环`gap = gap / 2`，直到减为1，循环终止**。增量`gap`的意义是将序列分为多少子序列。
* 希尔排序存在非相邻元素交换位置，所以希尔排序是**不稳定排序**。

![](https://tva2.sinaimg.cn/large/007DFXDhgy1g62psnakjbj30nv0n20te.jpg)
如图：
1. 初始分组`gap = len / 2 = 5`，将原序列分为5组，要求每组元素之间间隔尽可能大，所以是{i, i + gap, i + 2 * gap, ..., i + n * gap}
2. 对5组子序列分别进行插入排序，将较小的元素交换到前面
3. 交换完后，`gap /= 2`，将原序列重新分为2组，要求每组元素之间间隔尽可能大，所以是{i, i + gap, i + 2 * gap, ..., i + n * gap}
4. 对2组子序列分别进行插入排序，将较小的元素交换到前面
5. `gap = 2 / 1 = 1`，进行最后一次插入排序，结束循环

```java
//希尔排序
public static int[] shellSort(int[] in) {
    int gap = in.length >> 1;   //初始值，gap = len / 2
    while (gap > 0) {
        //从gap开始是因为默认[0,gap)是排好序的
        //即每个子序列第一个元素是排好序的，一共gap个元素
        //将序列分为gap组，对子序列进行插入排序
        for (int i = gap; i < in.length; i++) {     
            int temp = in[i];
            //限制了gap>0，所以只需要 i-gap>=0 而非 1
            while ((i - gap >= 0) && (in[i - gap] > temp) ) {   
                in[i] = in[i - gap];
                i -= gap;   //每次跨度为一个gap
            }
            //退出循环说明找到了合适位置
            in[i] = temp;
        }
        gap = gap >> 1; //gap = gap / 2
    }
    return in;
}
```

> 希尔排序 vs 插入排序：  
插入排序是稳定的，希尔排序不稳定；  
**希尔排序比较次数和移动次数都要明显小于插入排序，N越大越明显**；  
**希尔排序不适用于链式结构**

---

##  4.归并排序（`O(NlogN)`）

* **合并两个有序子序列的过程：创建一个和两个字序列长度和等长的序列，分别用一个指针指向两个子序列的头部，将两个子序列头部较小的那个加入到新序列，最后一个序列全部被加入新序列，就可以把另一个序列剩下的元素加入新序列。所以归并排序需要额外的内存空间**

```java
//合并两个有序子序列
//@params: left[]   左子序列
//@params: right[]  右子序列
public static void merge(int[] left, int[] right) {
  //结果序列，用于合并两个子有序序列
  int[] rst = new int[left.length + right.length];
  //左子序列、右子序列、总序列当前位置
  int l = 0, r = 0, curr = 0;
  //遍历，直到某个子序列被遍历完，将较小的放前面
    while (l < left.length && r < right.length) {
        if (left[l] < right[r]) {
            rst[curr] = left[l];
            l++;
            curr++;
        }else{
            rst[curr] = right[r];
            r++;
            curr++;
        }
    }
    //剩余序列直接加入结果序列
    while(l < left.length) {
        rst[curr] = left[l];
        l++;
        curr++;
    }
    while (r < right.length) {
        rst[curr] = right[r];
        r++;
        curr++;
    }
    return rst;
}
```

* **归并排序（Merge Sort）利用了分治的思想，将序列不断进行二分，直到每组子序列只剩下两个元素（“分”），然后对这两个元素进行排序（“治”），最后将所有子序列两两进行合并，合并时重新排序（“合”），所以时间复杂度是`O(NlogN)`**
* 归并排序只涉及相邻元素之间的交换，所以是**稳定性排序**

![](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/algorithm/mergeSort.png)

```java
//归并排序：递归地使用二分法，将一个序列分解为两个子序列，直到子序列只剩下一个元素
//@params:  in[]
public static int[] mergeSort(int[] in) {
    //退出递归的条件：子序列只剩下一个元素
    if(in == null || in.length < 2) return in;
    int mid = in.length >> 1;   //从中间将序列切分
    //Arrays.copyOfRange(int[] a, int from, int to);    复制范围[from,to)
    int[] left = Arrays.copyOfRange(in, 0, mid);    //不包括mid
    int[] right = Arrays.copyOfRange(in, mid, in.length);
    //递归调用mergeSort，然后将结果merge合并
    return merge(mergeSort(left), mergeSort(right) );
}
```

测试：
```java
int[] in = {999, 1, 2, 4, 3, -1, 0, 0};
System.out.println("归并排序： " + Arrays.toString(mergeSort(in)));
//归并排序： [-1, 0, 0, 1, 2, 3, 4, 999]
```

---

##  5.快速排序（`O(NlogN)`）

> 参考了[https://cloud.tencent.com/developer/article/1403566](https://cloud.tencent.com/developer/article/1403566)，说的很详细。

* 快速排序（Quick Sort）的中心思想是**每次选取一个基准值（`pivot`），然后通过比较与交换，使得`pivot`左边的元素都小于`pivot`，`pivot`右边的元素都大于`pivot`；然后再通过二分法，对`pivot`左右的子序列分别进行快排。所以快排的时间复杂度是`O(NlogN)`**
* **`pivot`一般直接选择最左元素**
* 交换`pivot`左右元素用到了**快慢指针法——快指针从右往左遍历，直到找到比`pivot`小的元素；慢指针从左往右遍历，直到找到比`pivot`大的元素。由于我们的目的是让左边的更小，右边的更大，所以我们将快慢指针元素进行交换（交换之前要判断慢指针是否超过了快指针）。**

如下图：
1. 基准值是最左端元素6。慢指针i，从左往右；快指针j，从右往左

![1](https://ask.qcloudimg.com/http-save/yehe-3158494/p9633kvbb4.png?imageView2/2/w/1620)

2. j先走，当遇到比基准值小的元素时停止（5<6）；然后i再走，遇到比基准值大的元素时停止（7>6）

![2](https://ask.qcloudimg.com/http-save/yehe-3158494/796vehfe4u.png?imageView2/2/w/1620)

3. 如果**慢指针没有超过快指针**，就将快慢指针所在元素进行交换

![3](https://ask.qcloudimg.com/http-save/yehe-3158494/smty0jsupr.png?imageView2/2/w/1620)
![4](https://ask.qcloudimg.com/http-save/yehe-3158494/1b3sxbdx3p.png?imageView2/2/w/1620)
![5](https://ask.qcloudimg.com/http-save/yehe-3158494/3sylc5gclx.png?imageView2/2/w/1620)

4. 重复以上步骤，直到快慢指针相遇，**将快慢指针所在位置元素与基准值进行交换，基准值指针指向快慢指针所在位置**

![6](https://ask.qcloudimg.com/http-save/yehe-3158494/wi69z7vc16.png?imageView2/2/w/1620)
![7](https://ask.qcloudimg.com/http-save/yehe-3158494/t4rtww7ipb.png?imageView2/2/w/1620)
![8](https://ask.qcloudimg.com/http-save/yehe-3158494/nwqmb1jkj0.png?imageView2/2/w/1620)

5. 交换完成后，基准值左边的元素就都小于基准值，基准值右边的元素都大于基准值

* 以上操作都是相对的，**如果`pivot`选择了最右元素，那么快指针就应该是从左往右遍历的那个**
* **递归**地对基准值左右序列进行快排，直到元素全部有序。**退出递归的条件——子序列只剩下一个元素，即存在`low > high`**
> 同样是上述序列{6,1,2,7,9,3,4,5,10,8}，如果慢指针i先走会发生什么?  
（1）pivot=6，i=7>6，j=5<6，交换i与j：`{6,1,2,"5",9,3,4,"7",10,8}`；  
（2）i=9>6，j=4<6，交换i与j：`{6,1,2,5,"4",3,"9",10,8}`；  
（3）i=9=j，交换i/j与pivot：`{"9",1,2,5,4,3,"6",10,8}`，9>6，很明显结果不对。  
结论：**如果快指针先走，可以保证快慢指针相遇时元素值小于pivot，交换过来后可以保证pivot左边元素都小于pivot；但是如果慢指针先走，很可能相遇时元素值就大于pivot，交换后pivot左边就会存在比pivot大的元素。**

```java
public static void quickSort(int[] in, int low, int high) {
    //退出递归条件：子序列只有一个元素，此时low = high
    if(low > high) return;
    int pivot = in[low];    //基准值，选最左边元素
    int quick = high;   //快指针，比慢指针先走一步，从右往左
    int slow = low;     //慢指针，从左往右
    //当quick遇到了比pivot小的元素，
    //同时slow遇到了比pivot大的元素，那么就交换两个元素
    while (slow < quick) {
        //快指针要先走一步
        //快指针向左，直到找到比pivot小的元素
        while (in[quick] >= pivot && slow < quick) {
            quick--;
        }
        //慢指针往右，直到找到比pivot大的元素
        while (in[slow] <= pivot && slow < quick) {
            slow++;
        }
        //需要再次判断slow是否已经遇到或超过了quick
        if (slow < quick) {
            //交换两个元素 —— 可以肯定slow!=quick，是不同元素，
            //所以可以用异或交换两个元素
            in[slow] = in[slow] ^ in[quick];
            in[quick] = in[slow] ^ in[quick];
            in[slow] = in[slow] ^ in[quick];
        }
    }
    //将快慢指针所在位置元素与基准值进行交换
    //slow与quick相遇的位置，就是基准值指针应该在的位置
    in[low] = in[slow];
    in[slow] = pivot;
    //slow或者quick就是新的pivot，左边元素都比它小，右边元素都比它大
    //递归对pivot的左右序列进行快排
    quickSort(in, low, slow - 1);   //基准值不需要再排序，所以是slow-1
    quickSort(in, slow + 1, high);  //基准值不需要再排序，所以是slow+1
}
```

测试：
```java
int[] in = {999, 1, 2, 4, 3, -1, 0, 0};
quickSort(in, 0, in.length - 1);
System.out.println(Arrays.toString(in));
//结果为：[-1, 0, 0, 1, 2, 3, 4, 999]
```

---

###  5.1 彩虹排序（`NlogK`，K是元素种类）

彩虹排序适用于**序列中元素种类是已知常数**的情况。

* 彩虹排序（Rainbow Sort）是一种变种的快排，快排是从元素N中选择基准值进行分治，所以时间复杂度是`O(NlogN)`，而彩虹排序则是从元素种类K中选择基准值进行分治，所以时间复杂度是`O(NlogK)`，而K是常数。
* 具体的分治策略是：**递归地从种类K中选择一个基准值，每次使得基准值左边的种类编码小于基准值，右边的种类编码大于基准值**
* 结束递归的条件：
1. 普通快排的结束递归条件：序列中只有一个元素，`low == high`（或者`low > high`）
2. **彩虹排序中额外的结束递归条件：序列中只有一种元素，`elementFrom == elementTo`（或者`elementFrom > elementTo`）**
> `elementFrom`、`elementTo`是指子序列（下标从`start`到`end`）中元素种类**从第`elementFrom`种到第`elementTo`种，即元素种类范围**。

参考[LintCode 143. Sort Colors II](https://www.lintcode.com/problem/sort-colors-ii/description)这题，看看彩虹排序的实际应用：
```java
/**
* @param colors: A list of integer
* @param k: An integer
* @return: nothing
*/
public static void sortColors2(int[] colors, int k) {
    if (colors == null || colors.length == 0) return;
    rainbowSort(colors, 0, colors.length - 1, 1, k);
}

//彩虹排序
//@params: colors[]     输入序列，包含从colorFrom到colorTo种类的颜色
//@params: start        子序列开始位置
//@params: end          子序列结束位置
//@params: colorFrom    颜色种类开始位置
//@params: colorTo      颜色种类结束位置
private static void rainbowSort(int[] colors,
                             int start,
                             int end,
                             int colorFrom,
                             int colorTo) {
    //结束递归的条件：
    // 1.传统快排结束条件：子序列只有一个元素；
    // 2.彩虹排序结束条件：子序列只有一种元素
    if (start == end || colorFrom == colorTo) return;
    //中间颜色，即基准值pivot
    int colorMid = (colorFrom + colorTo) >> 1;
    int left = start, right = end;
    while (left <= right) {
        //找到基准值左边大于基准值的元素
        while (left <= right && colors[left] <= colorMid) left++;
        //找到基准值右边小于基准值的元素
        while (left <= right && colors[right] > colorMid) right--;
        //再次判断，如果此时left指针没有超过right指针，就交换两个位置元素
        if (left <= right) {    //可能相等，不能用异或
            int temp = colors[left];
            colors[left] = colors[right];
            colors[right] = temp;
            left++;
            right--;
        }
    }
    //由于基准值选择的是中间位置，所以不需要将基准值交换到left所在位置
    rainbowSort(colors, start, right, colorFrom, colorMid);
    rainbowSort(colors, left, end, colorMid + 1, colorTo);  //基准值可以不作为子序列参与下一轮循环
}
```

测试：
```java
public static void main(String[] args) {
    int[] in = {3,2,2,1,4};
    sortColors2(in, 4);
    System.out.println(Arrays.toString(in));    //[1,2,2,3,4]
}
```

---

##  6.堆排序（`O(N + KlogN)`，K是排序序列长度）
**如果想得到一个序列中第K个最小元素之前的部分序列，最好采用堆排序！！！**

* 堆排序存在非相邻元素之间的交换，所以是**非稳定排序**
* 堆的特定：
1. 堆是一棵[完全二叉树](http://www.dragonbaby308.com/Algorithm-BinaryTree/)。可以用**数组**表示，节点k有如下特点：**父节点为`(k-1)/2`，左孩子为`2k + 1`，右孩子为`2k + 2`**。
2. 如果是大根堆，那么**对于任何节点，其父节点都会比所有子节点大**；如果是小根堆，那么**对于任何节点，其父节点都会比所有子节点小**

![](https://tva3.sinaimg.cn/large/007DFXDhly1g63zjv24xij30vx0nrabf.jpg)

参考上图，理解了下面3个问题，也就理解了堆排序：
1. 给定一个数组表示的满二叉树，如何构建一个堆？或者说，对一个堆进行了修改后，如何保证修改后的序列仍是一个堆？
* `heapify`：对于任意节点来说，堆都有父亲节点大于（或小于）左右儿子的特点，所以，**对于任意节点，将其与其左右儿子比较大小，将最大的元素替换到父亲节点，对替换下去的节点重复进行此操作**，即可将满二叉树构建成堆。
> 比如说上图，对于5-7-8这棵子树来说，5<8，所以将5和8交换位置，然后重新对替换下去的5节点进行`heapify`

```java
//heapify：将满二叉树构建为堆
//@param: tree[]    表示堆的数组
//@param: n         节点总数
//@param: i         对哪个节点进行heapify
public static void heapify(int[] tree, int n, int i) {
  if(i >= n) return; //结束递归条件：建堆节点下标超过节点总数，i>=n
  //根据满二叉树的特点，有：
  int left = 2 * i + 1; //左子树
  int right = 2 * i + 2;  //右子树
  //将父亲、左右子树3者中最大的数替换到父亲节点
  int max = i;
  //注意left 和 right不能越界
  if(left < n && tree[left] > tree[max]) max = left;
  if (right < n && tree[right] > tree[max]) max = right;
  if(max != i) {  //如果父亲节点就是最大节点，就不需要重新建堆了
    //交换max和i的元素
    tree[max] = tree[max] ^ tree[i];
    tree[i] = tree[max] ^ tree[i];
    tree[max] = tree[max] ^ tree[i];
    //对替换下去的节点重新建堆
    heapify(tree, n, max);
  }
}
```

2. 通过步骤1，我们有了将一个修改后的堆还原成堆的能力，那么给定一个数组表示的满二叉树，如何构建出初始的堆？
* 我们可以从树的**倒数第二层（h-1层）逆序地对每个节点进行`heapify`操作，直到根节点**，这样我们就能得到一个初始堆。
* 为什么不从树的倒数第一层（h层）开始`heapify`呢？因为**倒数第一层没有子节点，本身就是满二叉树，没有`heapify`的必要**。
* 那么如何确定倒数第二层的下标呢？**我们知道满二叉树中，k节点的父亲节点是`(k-1)/2`，我们可以确定倒数第一层的最后一个节点（即数组尾部），该节点的父亲节点正好就位于倒数第二层，这就是我们`heapify`的起点**
```java
//初始建堆
//@params: tree[] 满二叉树
//@params: n      节点总数
public static void buildHeap(int[] tree, int n) {
  int last = tree.length;
  int parent = (last - 1) >> 1; //倒数第二层
  for(int i = parent; i >= 0; i--) {
    heapify(tree, n, i);
  }
}
```

3. 现在我们有了一个初始堆，有了能够将更改后的堆构建成新堆的能力，如何进行堆排序？
* **堆的特点是父亲节点大于（或小于）儿子节点，那么其根节点必定是最大的（最小的），也就是说我们每次构建好堆，都能得到一个剩余元素的最大值（最小值）**
* **将根节点（最大值/最小值）与堆尾元素（也就是数组队尾）进行交换，然后将堆尾剔除并记录下来（当然这是逻辑上的剔除，实际上你可以在`heapify`的时候不传入堆尾即可），并对剩余元素重新进行`heapify`。重复此步骤，直到已排序的序列长度满足要求。**
```java
//堆排序
//@params: tree[] 满二叉树
//@params: n      节点总数
public static void heapSort(int[] tree, int n) {
  //构建初始堆
  buildHeap(tree, n);
  //如果只需要k个有序序列，可以提前结束循环
  for(int i = n - 1; i >= 0; i--) {
    //将根节点与堆尾进行交换
    int temp = tree[i];
    tree[i] = tree[0];
    tree[0] = temp;
    //剔除堆尾的最大值，对剩余元素重新进行heapify
    heapify(tree, i, 0);
  }
}
```

---

##  归并排序 vs 快排 vs 堆排序

1. 如果从**空间复杂度**来考虑，**首选堆排序，然后是快排，最后是归并排序（需要额外的内存空间）**
2. 如果是从**稳定性**来考虑，应选择**归并排序**，因为快排和堆排序并不稳定
3. 如果从**平均情况下的排序速度**来考虑，应该选择**快排**

---

##  7.基数排序（`O(N*D)`，D为位数，N为元素个数）

* 通过对排序的N个元素进行若干趟“分配”与“收集”来实现排序
* 基数排序（Radix Sort）不需要交换元素位置，所以是**稳定性排序**
* **基数排序效率与初始序列是否有序没有关联**
* 在基数排序中，**对于任何位数上的基数进行装桶操作时，都需要n + r个临时空间**

任何一个阿拉伯数字，它的各个位数上的基数都是以0 ~ 9 来表示的，我们将0 ~ 9视为10个桶。  
如：给定序列{50,123,543,187,49,30,0,2,11,100}
![](https://tva4.sinaimg.cn/large/007DFXDhly1g647knrqf3j30uw0t5wgc.jpg)  

1. 首先根据个位数元素分到指定的桶中。
2. 将桶中元素按顺序依次弹出，即{50,30,0,100,11,2,123,543,187,49}
3. 再根据十位数将元素分到指定桶中。
4. 将桶中元素按顺序依次弹出，即{0,100,2,11,123,30,543,49,50,187}
5. 再根据百位数将元素分到指定桶中。
6. 将桶中元素按顺序依次弹出，此时元素就已经有序了

```java
//基数排序
//不考虑负数的情况
public static int[] radixSort(int[] in) {
    int len = in.length;
    if (len < 2 || in == null) return in;
    //0~9：10个桶，每一个桶是一个不定长的ArrayList
    ArrayList<ArrayList<Integer>> buckets = new ArrayList<ArrayList<Integer>>();
    for (int i = 0; i < 10; i++) buckets.add(new ArrayList<Integer>());
    //找出序列中的最大值——不能用排序算法，不然还基数排序个什么劲？
    int max = in[0];
    for (int i = 1; i < len; i++) max = in[i] > max ? in[i] : max;
    //根据最大值，判断位数——即需要循环入桶、出桶的次数
    int digit = 0;
    while (max != 0) {
        max /= 10;
        digit++;
    }

    //入桶 & 出桶
    int mod = 10, div = 1;
    for (int i = 0; i < digit; i++, mod *= 10, div *= 10) {
        //遍历序列，入桶
        for (int j = 0; j < len; j++) {
            int temp = (in[j] % mod) / div;
            //判断对应位的元素，存入桶中
            //ArrayList API: T get(int index);  &   add(T t);
            buckets.get(temp).add(in[j]);
        }
        //遍历所有桶，出桶
        int[] newArray = new int[len];  //出桶序列
        int cnt = 0;
        for (int outArrayIndex = 0; outArrayIndex < buckets.size(); outArrayIndex++) {
            for (int inArrayIndex = 0; inArrayIndex < buckets.get(outArrayIndex).size(); inArrayIndex++) {
                newArray[cnt++] = buckets.get(outArrayIndex).get(inArrayIndex);
                //等价于：
                //newArray[cnt] = buckets.get(outArrayIndex).get(inArrayIndex);
                //cnt++;
            }
            //记得出桶后要清空桶，不能影响下一次循环
            buckets.get(outArrayIndex).clear();
        }
        //出桶序列替换原序列
        for (int j = 0; j < len; j++) in[j] = newArray[j];
        }
        return in;
    }
```

---

##  8.计数排序

计数排序（Counting Sort）

---

##  9.桶排序

桶排序（Bucket Sort）

---

##  10.拓扑排序

* 入度：**指向节点的边的个数（比如图中节点1的入度就是0）**
![](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/algorithm/%E6%8B%93%E6%89%91%E6%8E%92%E5%BA%8F.png)

1. 使用`BFS`进行拓扑排序
2. 找到图中**入度为0**的节点，**从图中移除**
3. **被移除节点指向的节点，入度减1**
4. 重复2、3
5. **如果最后不存在入度为0的节点，说明图中有环，这时候拓扑排序无解**
