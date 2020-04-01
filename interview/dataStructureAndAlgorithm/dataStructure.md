---
title: 数据结构
permalink: /interview/dataStructureAndAlgorithm/dataStructure
key: dataStructureAndAlgorithm-dataStructure
---

## 数组  

### 【更大的数】

**题目描述**

数组里面的每一个元素，找到该元素后面第一个比他大的数 

**解题思路**

迭代查找即可

**代码实现**

~~~java
private static int[] large(int[] array){
    int[] large = new int[array.length];
    for (int i = 0; i < array.length ; i++) {
        for (int j = i + 1; j < array.length ; j++) {
            if(array[j] > array[i]){
                large[i] = array[j];
                break;
            }
        }
    }
    return large;
}

public static void main(String[] args) {
    System.out.println(Arrays.toString(large(new int[]{4,3,5,7,1})));
}
~~~

**输出结果**

~~~
[5, 5, 7, 0, 0]
~~~



### 【旋转排序数组中的最小值】 

原题：[旋转排序数组中的最小值](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/) / [旋转数组的最小数字](https://www.nowcoder.com/practice/9f3231a991af4f55b95579b44b7a01ba?tpId=13&tqId=11159&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

**题目描述**

假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。

请找出其中最小的元素。

你可以假设数组中不存在重复元素。

**示例**

示例 1:

输入: [3,4,5,1,2]
输出: 1

示例 2:

输入: [4,5,6,7,0,1,2]
输出: 0

**解题思路**

二分查找

1. 找到数组的中间元素 mid。

2. 如果中间元素 > 数组第一个元素，我们需要在 mid 右边搜索变化点。

3. 如果中间元素 < 数组第一个元素，我们需要在 mid 做边搜索变化点。

4. 当我们找到变化点时停止搜索，当以下条件满足任意一个即可：

   nums[mid] > nums[mid + 1]，因此 mid+1 是最小值。

   nums[mid - 1] > nums[mid]，因此 mid 是最小值。

**代码实现**

~~~java
private static int findMin(int[] nums) {
    if (nums.length == 1) {
        return nums[0];
    }
    int left = 0;
    int right = nums.length - 1;
    if (nums[right] > nums[left]) {
        return nums[0];
    }
    // Binary search way
    while (right >= left) {
        // Find the mid element
        int mid = left + (right - left) / 2;
        if (nums[mid] > nums[mid + 1]) {
            return nums[mid + 1];
        }
        if (nums[mid - 1] > nums[mid]) {
            return nums[mid];
        }
        if (nums[mid] > nums[0]) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return -1;
}

private static int minNumberInRotateArray3(int [] array) {
    if(array.length <= 0){
        return 0;
    }
    int left = 0;
    int right = array.length - 1;
    while (left < right){
        // 如果没有旋转过，直接返回
        if (array[left] < array[right]) {
            return array[left];
        }
        // 取中间值
        int mid = (left + right) >> 1;
        // 如果中间值大于起始值，说明一直在递增
        if (array[mid] > array[left]) {
            left = mid + 1;
        } else if (array[mid] < array[right]) {
            // 如果是mid-1，则可能会错过最小值，因为找的就是最小值
            right = mid;
        } else {
            // 巧妙避免了offer书上说的坑点（1 0 1 1 1）
            left++;
        }
    }
    return array[left];
}

public static void main(String[] args) {
    System.out.println(findMin(new int[]{7,2,3,4,5}));
}
~~~

**输出结果**

~~~
2
~~~



### 【数组的交集】

**题目描述**

有两个整数数组，计算两个数组的交集

**解题思路**

利用`retainAll`或者`contains`接口实现

**代码实现**

~~~java
/**
  * 利用 list.retainAll求交集
  * @param A
  * @param B
  * @return
  */
private static HashSet<Integer> mix(int[] A,int[] B){
    HashSet<Integer> setA = new HashSet<>();
    HashSet<Integer> setB = new HashSet<>();
    for (int val : A){
        setA.add(val);
    }
    for (int val : B){
        setB.add(val);
    }
    setA.retainAll(setB);
    return setA;
}

private static HashSet<Integer> mix2(int[] A,int[] B){
    HashSet<Integer> setA = new HashSet<>();
    HashSet<Integer> result = new HashSet<>();
    for (int val : A){
        setA.add(val);
    }
    for (int val : B){
        if(setA.contains(val)){
            result.add(val);
        }
    }
    return result;
}

public static void main(String[] args) {
    int[] A = {3,1,2,2};
    int[] B = {1,3,2,7};
    HashSet<Integer> set = mix2(A, B);
    set.forEach(System.out::println);
}
~~~

**输出结果**

~~~
1
2
3
~~~



### 【数组合并去重】

**题目描述**

两个有序数组，数组中包括一些重复元素，合并成一个有序数组，并且去重

**解题思路**

**代码实现**

**输出结果**



### 【第K个最大元素】

**题目描述**

**解题思路**

**代码实现**

**输出结果**

数组中的第K个最大元素  

### 【三数之和】

**题目描述**

**解题思路**

**代码实现**

**输出结果**

给定一个数组a[N]和一个整数P，求a[i] + a[j] + a[k] =P，保证i<j<k ；

### 【数组移动】

**题目描述**

**解题思路**

**代码实现**

**输出结果**

将数组里面非0的元素移到前面，0元素移到后面（不改变相对位置）。

### 【奇偶分类】

原题：[调整数组顺序使奇数位于偶数前面](https://www.nowcoder.com/practice/beb5aa231adc45b2a5dcc5b62c93f593?tpId=13&tqId=11166&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

**题目描述**

数组A，2n个元素，n个奇数、n个偶数，设计一个算法，使得数组奇数下标位置放置的都是奇数，偶数下标位置放置的都是偶数。

**解题思路**

**代码实现**

~~~java
/**
  * my result(once pass) : 利用额外两个数组分别存储奇偶数，然后进行数组复制
  * 缺点是利用了额外空间
  * 时间复杂度：O(n)
  * 空间复杂度：O(n)
  * @param array
  */
private static void reOrderArray(int [] array) {
    int[] odd = new int[array.length];
    int[] even = new int[array.length];
    int oddIndex = 0;
    int evenIndex = 0;
    for (int val : array){
        if(val % 2 == 0){
            even[evenIndex ++] = val;
        }else {
            odd[oddIndex ++] =val;
        }
    }
    System.arraycopy(odd, 0, array, 0, oddIndex);
    System.arraycopy(even, 0, array, oddIndex, evenIndex);
}


/**
  * other : 插入排序
  * @param array
  */
private static void reOrderArray2(int [] array) {
    if (array == null || array.length < 2) {
        return;
    }
    int n = array.length;
    for (int i = 1; i < n; i++) {
        // 当前元素是奇数，就移动到奇数序列
        if (array[i] % 2 != 0) {
            int value = array[i];
            int cur = i;
            while (cur > 0 && (array[cur - 1] % 2 == 0)) {
                array[cur] = array[cur - 1];
                cur--;
            }
            array[cur] = value;
        }
        // 当前元素是偶数，无须移动
    }
}

public static void main(String[] args) {
    int[] array = {2,1,4,3,7,6,9};
    reOrderArray2(array);
    System.out.println(Arrays.toString(array));
}
~~~

**输出结果**

~~~
[1, 3, 7, 9, 2, 4, 6]
~~~



### 【概率】

**题目描述**

**解题思路**

**代码实现**

**输出结果**

已知一个正整数组，一k，任取两个数，问两个数之和大于k的概率，手写  

### 【矩阵打印】

**题目描述**

**解题思路**

**代码实现**

**输出结果**

给我一张纸，画了一个九方格，都填了数字，给一个MN矩阵，从1开始逆时针打印这MN个数，要求时间复杂度尽可能低，可以先说下思路   

### 【反转矩阵】

**题目描述**

**解题思路**

**代码实现**

**输出结果**

反转矩阵  



无序数组，将数据处理成无重复，有序状态，需要什么数据结构   

## 链表

Leetcode 2 链表相加  
Leetcode 206 链表反转  
不额外的空间，进行俩排序链表的合并  
单链表排序，奇数位升序，偶数位降序  
反转单链表中指定start到end的节点  
判断链表是否有环？(快慢指针/单指针)  
跳表的查询过程是怎么样的，查询和插入的时间复杂度  
如何设计拥有高效的随机读取能力的的链表（跳表）  
如何检测链表死循环  

## 树  

各种树的时间复杂度  
只给先序和后序遍历能确定唯一的树结构吗？不能，举个例子说明  

### **二叉树**  

LeetCode 863 二叉树中所有距离为K的结点  
二叉树的镜像  
二叉树中的最大路径和  
一个二叉树给一个target，找到所有sum==target的路径  
二叉树宽度   
二叉树最长路径  
判断是不是二叉搜索树  

### **红黑树 AVL树**  

讲一下红黑树   
红黑节点的个数  
红黑树的插入删除查询时间复杂度  
简单说说avl树，有什么特征。  
红黑树，avl区别  

### **B树 B+树**  

讲一下B+树  
B+树的插入删除查询时间复杂度  
介绍B树、B+树、红黑树  

哈夫曼树和应用  
哈夫曼树原理树的遍历，深度优先，手写，用到了什么数据结构   
判断树状结构有没有环  
多叉树的最大深度   
判断完全n叉树（自己设计数据结构）  