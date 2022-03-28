剑指offer

## 算法面试介绍

### 难易程度

* 跟工程结合紧密的算法
* 难度高的不会考 [了解性能优化的发展历史及目标-课前预习.pdf](..\..\..\..\..\Downloads\了解性能优化的发展历史及目标-课前预习.pdf) 
* 难度在于中等左右

### 算法特点

* 门槛高
* coding最高
* 准备和学习起来无法突击，真的需要话时间（ACM专业的问题）
* 摸清楚考察范围需要长时间累积
* 真正具备算法能力需要大量练习

### 算法面试特点

* 公司都爱考，并且大厂更喜欢考察
* 真的考出东西吗？因为是纯分析能力+coding能力
* 是聪明或者努力的证明
* 无法通过沟通能力化解，不会就是不会，写不出来就是写不出来

### 左神算法课的帮助

* 视频地址：https://www.bilibili.com/video/BV13g41157hK
* 解决：
  * 1）划清领域：大厂需要考的和比赛需要考的，差别很大，左神的课程能帮助我们省去大量时间去准备最应该的内容。
  * 2）有效练习
  * 3）实战经验

## 线性结构

### 栈

### 队列

### 链表

### 数组

### 字符串处理

## 排序算法

### 排序算法对比

| 算法中文名称 | 算法英文名称 | 平均时间复杂度 | 最坏时间复杂度 | 最好时间复杂度 | 空间复杂度 | 稳定性 |
| ------------ | ------------ | -------------- | -------------- | -------------- | ---------- | ------ |
| 选择排序     | Selection    | $n^2$          | $n^2$          | $n^2$          | 1          | 不稳定 |
| 冒泡排序     | Bubble       | $n^2$          | $n$            | $n^2$          | 1          | 稳定   |
| **插入排序** | Insertion    | $n^2$          | $n^2$          | $n$            | 1          | 稳定   |
| **堆排序**   | heap         | $n\log_2n$     | $n\log_2n$     | $n\log_2n$     | 1          | 不稳定 |
| 希尔排序     | Shell        | $n^{1.3}$      | $n^2$          | $n$            | 1          | 不稳定 |
| **归并排序** | Merge        | $n\log_2n$     | $n\log_2n$     | $n\log_2n$     | $n$        | 稳定   |
| **快速排序** | Quick        | $n\log_2n$     | $n^2$          | $n\log_2n$     | $\log_2n$  | 不稳定 |
| 桶排序       | Bucket       | $n$            | $n$            | $n$            | $n$        | 稳定   |
| 计数排序     | Counting     | $n+k$          | $n+k$          | $n+k$          | $n+k$      | 稳定   |
| 基数排序     | Radix        | $n\times k$    | $n\times k$    | $n\times k$    | $n+k$      | 稳定   |

排序算法的稳定性及其汇总

同样值的个体之间，如果不因为排序而改变相对次序，就是这个排序是有稳定性的;否则就没有。

不具备稳定性的排序：选择排序、快速排序、堆排序

具备稳定性的排序：冒泡排序、插入排序、归并排序、一切桶排序思想下的排序

**目前没有找到时间复杂度0(N*logN)，额外空间复杂度0(1)，又稳定的排序。**

### 认识时间复杂度

![image-20220321153935039](../../../../../Pictures/assets/剑指offer/image-20220321153935039.png)

两种交换方法

```java
public void swap(int[] arr,int i,int j){
    int temp = arr[i];
    arr[i] = arr[j];
    arr[j] = temp;
}

public void swap2(int[] arr,int i,int j){
    arr[i] = arr[i] ^ arr[j];
    arr[j] = arr[i] ^ arr[j];
    arr[i] = arr[i] ^ arr[j];
}
```

### 选择排序

每次选择当前剩余数中最小的数(或者最大的数)，然后与当前位置进行交换。

```java
public void selectionSort(int[] arr) {
    for(int i=0;i<arr.length-1;i++){
        int minIndex = i; //每次选择当前剩余数中最小的数(或者最大的数)，然后与当前位置进行交换。
        for(int j=i+1;j<arr.length;j++){
            minIndex = arr[j] < arr[minIndex]?j:minIndex;
        }
        // 找到了就两两交换
        swap(arr,i,minIndex);
    }
}
```

### 冒泡排序

排序从n开始，依次递减，在每轮循环中，不满足条件的数，两两进行交换。

```java
public void bubbleSort(int[] arr){
    for(int i=arr.length-1;i>=0;i--){
        for(int j=0;j<i;j++){
            if(arr[j] > arr[j+1]){
                swap(arr,j,j+1);
            }
        }
    }
}
```

### 插入排序

从当前元素，往前面进行比较，并同时将数据往后移，比较完后，就能直接插入到满足条件的位置。

```java
public void insertSort(int[] arr){
    for(int i=1;i<arr.length;i++){

        // 前面已经有序了，只是当前来了新的数，只需要插入到合适的位置即可，其他往后移，一直往前交换即可
        for(int j=i-1;j>=0 && arr[j] > arr[j+1];j--){
            swap(arr,j,j+1);
        }
    }
}

```

### 二分查找

![image-20220321170509481](../../../../../Pictures/assets/剑指offer/image-20220321170509481.png)

```java
// 分查找不需要数组一定有序
// 比如通过二分查找查找最大值
public int getMaxNumber(int[] arr,int L,int R){

    if(L == R) return arr[L];

    int mid = L + (R - L) /2;
    int leftMax = getMaxNumber(arr,L,mid);
    int rightMax =getMaxNumber(arr,mid+1,R);

    return Math.max(leftMax,rightMax);
}
```

![image-20220321232836321](../../../../../Pictures/assets/剑指offer/image-20220321232836321.png)

### **快速排序** 

随机选取一个数，做划分，比该数大的放在右边，比该数小的放在左边。递归重复。

```java
public int[] partition(int[] arr,int left,int right){

    int less = left-1; // 小于区域
    int more = right;  // 大于区域

    // 使用最后一个数，作为划分值
    while (left < more){
        if(arr[left] < arr[right]){
            swap(arr,++less,left++); // 当前下标值与小于区域的值，进行交换，然后划到下一个，
            // 因为前面的已经比较过，是知道比当前划分值小的
        }else if(arr[left] > arr[right]){
            swap(arr,--more,left);  // 当前下标值，大于划分值，与大于区域的数的左边界前一个数，进行交换
            // 交换后，不向右移动，因为不知道当前值情况，需要下一次进行比较
        }else{
            left++;
        }
    }
    // 把最后一个值，放到右边界处
    swap(arr,more,right);

    // 当前划分值的相同数的范围限制
    return new int[]{less+1,more};
}

public void quickSort(int[] arr,int left,int right){
    if(left < right){
        // 随机选取一个数，因为一般是通过比较最后一个数的值，因为交换到最后一个数即可。
        swap(arr,left+(int)(Math.random()*(right-left+1)), right);
        // 划分至区域的左边界和右边界
        // 比如 3 6 2 5 7 5 =>比如 比较5 => 3 2 5 5 6 7 => partition就返回5的左边界和右边界线
        int[] p = partition(arr,left,right);
        quickSort(arr,left,p[0]-1);
        quickSort(arr,p[1]+1,right);
    }
}

public void quickSort(int[] arr) {
    if(arr.length <= 1 || arr == null) return;

    quickSort(arr,0,arr.length-1);
}
```

### **归并排序**

递归将数组一分为二，将左边和右边数组都进行排序，然后进行合并即可。排序的时候，使用一个数组装起来，最后重新赋值回去。

```java
public void merge(int[] arr, int left, int mid, int right) {
    int[] container = new int[right - left + 1];
    int i = 0;
    int p1 = left;
    int p2 = mid + 1;

    while (p1 <= mid && p2 <= right) {
        container[i++] = arr[p1] <= arr[p2] ? arr[p1++] : arr[p2++];
    }

    while (p1 <= mid) {
        container[i++] = arr[p1++];
    }

    while (p2 <= right) {
        container[i++] = arr[p2++];
    }

    for(int k=0;k<container.length;k++){
        arr[k+left] = container[k];
    }
}

public void mergeSort(int[] arr, int left, int right) {
    if (left == right) return;
    int mid = left + ((right - left) >> 1) ;
    mergeSort(arr, left, mid);
    mergeSort(arr, mid + 1, right);
    merge(arr, left, mid, right);
}

public void mergeSort(int[] arr) {
    if (arr.length <= 1 || arr == null) return;
    mergeSort(arr, 0, arr.length - 1);
}

```

![image-20220321235804938](../../../../../Pictures/assets/剑指offer/image-20220321235804938.png)

```java
// 不需要动其他，只需要记录即可
if(arr[p2] > arr[p1]){
    ansMap.put(p1, (ansMap.get(p1)==null?0:ansMap.get(p1))+(right-p2+1)*arr[p1]);
}
```

![image-20220322105759736](../../../../../Pictures/assets/剑指offer/image-20220322105759736.png)

```tex
问题1：数组 3 5 6 7 4 3 5 8 ,数 5
    1) arr[i] <= num => arr[i]和小于等于num的下一个数交换，并右移，i++
    2) arr[i] > num,i++
    
问题2：3 5 6 3 4 5 2 6 9 0 ，数5
》 使用双指针，大于5的放在右边，小于5的数放在左边

1）arr[i] < num，arr[i]和小于区域下一个交换，小于区域右扩，i++
2）arr[i] == num，i++
3）arr[i] > num，arr[i]和大于区域前一个数交换，大于区域左扩，i不动

```

### **堆排序**

小根堆：从小到大，每个子树的根节点，是最小的

大根堆：从大到小，每个子树的根节点，是最大的

```tex
 			0
        1		2
	  3   4   5   6
```

数组中，左节点与右节点位置关系：

左孩子节点：2*n+2，偶数位置

右孩子节点：2*n+1，奇数位置

当前节点父节点位置i，其子节点位置index：index = 2 * i=> i = idnex / 2；index = 2*i+1 => (index-1)/2

=> 因此可知，当前父节点位置为(index - 1) / 2

```java
// 大根堆：如果插入当前位置的节点，比父节点的值大，那么久和父节点交换。
// 如果想建立小根堆，就和大根堆相反来，父节点是最小的，调整时，也要保持该结构
public void heapInsert(int[] arr,int index){
    while(arr[index] > arr[(index-1)/2]){
        swap(arr,index,(index-1)/2);
        index = (index-1)/2;
    }
}

// 调整当前index位置的堆结构
public void heapify(int[] arr,int index,int heapSize){
    int left = 2*index + 1;// 左孩子的下标

    while (left < heapSize){

        // 如果有右孩子，比较两个孩子之间的大小，谁大选谁
        int largest = left + 1 < heapSize && arr[left+1] > arr[left]?left+1:left;

        // 比较父和孩子节点之间的值得大小，如果当前父节点值最大，则不需要改变，跳出循环
        // 如果当前父比孩子小，则交换，继续往下调整
        largest = arr[largest] > arr[index]?largest:index;

        if(largest == index) return;
        // 交换
        swap(arr,largest,index);
        // 继续比较
        index = largest;
        left = 2*index+1;
    }
}

public void heapSort(int[] arr) {

    if(arr == null || arr.length <= 1) return;

    // 建立堆结构
    for(int i=0;i<arr.length;i++)
        heapInsert(arr,i);

    int heapSize = arr.length;
    // 如何有序排序，每次将堆顶元素，放到最后即可
    swap(arr,0,--heapSize);
    while (heapSize > 0){
        heapify(arr,0,heapSize); // 调整堆结构，因为堆顶元素不是最大了
        swap(arr,0,--heapSize); //继续
    }
}

public void heapSort2(int[] arr) {

    if(arr == null || arr.length <= 1) return;

    // 可以从堆底开始调整堆结构，由于叶子节点没有孩子，因为不需要做调整
    for(int i=(arr.length+1)/2;i>=0;i--){
        heapify(arr,i,arr.length);
    }

    int heapSize = arr.length;
    // 如何有序排序，每次将堆顶元素，放到最后即可
    swap(arr,0,--heapSize);
    while (heapSize > 0){
        heapify(arr,0,heapSize); // 调整堆结构，因为堆顶元素不是最大了
        swap(arr,0,--heapSize); //继续
    }

}
```

![image-20220322171931081](../../../../../Pictures/assets/剑指offer/image-20220322171931081.png)

![image-20220322172053359](../../../../../Pictures/assets/剑指offer/image-20220322172053359.png)

```java
前7个数，建立成小根堆，把堆顶最小的数，与第一个数交换，之后加入7，依次如此。
```

### 比较器

配合系统自带的排序。不能用于基本类型，因此基本类型需要转换为包装类型。

```java
static class MyComparator implements Comparator<Integer>{

    @Override
    public int compare(Integer o1, Integer o2) {
        return  o1-o2;
    }
}

Integer[] array = Arrays.stream(arr).boxed().toArray(Integer[]::new);
Arrays.sort(array, new MyComparator());
solution.printArr(Arrays.stream(array).mapToInt(Integer::valueOf).toArray());
```

### 桶排序

用一个数组，记录

```tex
17 13 25 100 72 其中100最大，有三位数，因此其他补齐相应位数，不足补0

017 013 025 100 072，从个位到高位依次装入桶中，重复操作，每一位上的数字大小在0~9，因此准备10个队列即可。
```

```java
public void bucketSort(int[] arr){

    int maxNumber = Arrays.stream(arr).max().getAsInt();
    int count = getNumberBit(maxNumber);

    List<Queue<Integer>> queues = new ArrayList<>();
    for(int i=0;i<10;i++)
        queues.add(new LinkedList<>());

    for(int c=0;c<count;c++){
        int m = (int)Math.pow(10,c);
        for(int i=0;i<arr.length;i++){
            // 从个位开始，依次到高位
            int index = (arr[i] / m) % 10;
            queues.get(index).add(arr[i]); // 开始将相同位上的数，装入进去
        }
        int k =0;
        for(int i=0;i<queues.size();i++){ //重新把桶的数，放回去
            while(!queues.get(i).isEmpty()) {
                arr[k++] = queues.get(i).poll();
            }
        }
    }
}
```

![image-20220322201918293](../../../../../Pictures/assets/剑指offer/image-20220322201918293.png)

### 基数排序

```java
public void countSort(int[] arr){
    int maxNumber = Arrays.stream(arr).max().getAsInt();
    int digit = getNumberBit(maxNumber);

    int[] buckets = new int[arr.length];

    for(int d=1;d<=digit;d++){

        int[] count = new int[10];

        for (int a : arr) count[getDigit(a, d)]++;

        for(int i=1;i<10;i++)//为当前相同数，分配空间，通过前缀和来分配
            count[i] = count[i] + count[i-1];

        for(int i=arr.length-1;i>=0;i--) // 类似分片，因此倒着来存进去
            buckets[--count[getDigit(arr[i],d)]] = arr[i];

        // 重新装回去
        for(int i=0;i<arr.length;i++) arr[i] = buckets[i];
    }

}
```

### 计算排序

当桶数量达到最大就是计数排序。

## 位运算

### 运算符号

* 异或运算`^`：两个位相同为0，相异为1

```tex
a ^ b = b ^ a
0 ^ a = a
a ^ a = 0

=> 因此有个很巧妙的交换方法
比如交换a和b
a = 甲
b = 乙

a = a ^ b = 甲^乙
b=  a ^ b = 甲^乙^乙=甲
a = a ^ b = 甲^乙^甲=乙
```

* 与运算`&`： 两个位都为1时，结果才为1

* 或运算`|`：两个位都为0时，结果才为0

* 取反运算`~`：0变1，1变0

* 左移运算`<<`：各二进位全部左移若干位，高位丢弃，低位补0

* 右移运算`>>`：各二进位全部右移若干位，对无符号数，高位补0，有符号数，各编译器处理方法不一样，有的补符号位（算术右移），有的补0（逻辑右移）

### 逻辑操作

| 去掉最后一位  | 101101——10110   | x >> 1     |
| ------------- | --------------- | ---------- |
| 最后加一个0   |                 | x << 1     |
| 最后加一个1   | 101101——1011011 | x << 1 + 1 |
| 最后一位变成1 |                 | x \| 1     |
| 最后一位变成0    |                    | x \| 1 - 1        |
| 最后一位取反     |                    | x ~ 1             |
| 把右边第k位变成1 | 101001——101101 k=3 | x \| (1 << (k-1)) |
| 把右边第k位变成0 | 101101——101001 k =3 | x & ~(1 << (k-1)) |
| 右数第k位取反    | 101001——101101 k=3  | x ~ (1 << (k-1))  |
| 取末三位         | 101101——101         | x & 7             |
| 取末k位      | 关键是要找到末尾k个1    | x & (~(~0 << k))  |
| 取右边第k位  |                         | x & (1 << ( k-1)) |
| 把末k位变成1 | 找到末尾的1，然后或一下 | x \| ((1<<k) -1 ) |
| 取最右边的1 |  | x & (~x + 1 ) |

## 链表



### 题目

![image-20220303223044412](../../../../../Pictures/assets/剑指offer/image-20220303223044412.png)

* 思路

```html
判断一条循环链表：
	可以使用哈希表，就是hashset就可以了。
	可以使用快慢指针。快指针走两步，慢指针走一步。
	1. 一直走下去，走到两个指针相遇，就退出去。
    2. 然后快指针重新变成头结点，和慢指针一起迈一步，直到相遇就是循环节点的第一个节点。

两条链表是否相交：第一步，由于相交，肯定最少有一个节点地址相同，比如末尾节点，可以先判断，末尾节点是否相等，然后记录链表长度。
第二步，长的链表先走比短的链表多出来的哪几步。然后一起走，最后一定能相遇。

无法相交：
一个链表为null，另外一个链表不为null，因为如果相交，肯定有一个节点是同一个节点（比如末尾节点）
（1） 两个循环链表 不想交
（2） 两个循环链表相交，只有一个相交节点处
（3） 两个循环链表相交，有两个相交节点处。
```

![image-20220303232028889](../../../../../Pictures/assets/剑指offer/image-20220303232028889.png)

## 二叉树

```java
class Node<V>{
    V value;
    Node<V> left;
    Node<V> right;
}
```

### 递归遍历

#### 先序遍历

```java
// 根左右
```

#### 中序遍历

```java
// 左根右
```

#### 后序遍历

```java
// 左右根
```

### 非递归遍历

#### 先序遍历

![image-20220303233507014](../../../../../Pictures/assets/剑指offer/image-20220303233507014.png)

```java
// 准备一个栈：后处理的先压入栈
/**
*  0. 第一步先把根节点压入栈中
*  1. 从栈中弹出一个节点cur
*  2. 打印（处理）cur
*  3. 如果有孩子，先把右再左（压入栈中）
*  4. 重复1~3
*/
```

#### 中序遍历

```java
// 准备两个栈，其中一个栈专门用来收集元素
/**
*  0. 第一步先把根节点压入栈中
*  1. 从栈中弹出一个节点cur
*  2. cur节点放到收集栈中
*  3. 如果有孩子，先左后右（压入栈中）
*  4. 重复1~3
*/
```

#### 后序遍历

```java
// 准备两个栈，其中一个栈专门用来收集元素(打印出来就是左右根)
/**
*  0. 第一步先把根节点压入栈中
*  1. 整颗树左边进栈，只管左子树
*  2. 依次弹出的过程中，打印，对弹出的节点的右树进行判断，周而复始（这里才看右子树）
*/
```

### 层级遍历

常见题目：求一个二叉树的深度

```java
// 通过让先让左子树节点入队，最后右子树节点入队，并记录，这样就可以每次保证什么时候就可以找到当前层的最后一个节点（右右子树节点）
public int maxLevelWidth2(Node<V> root) {
    Node<V> currentNode = null; // 当前层最后一个节点
    Node<V> nextNode = null;    // 下一层的最后一个节点
    Node<V> curNode = null;
    int currentLevelNodes = 0;
    int maxLevelNodes = Integer.MIN_VALUE;

    Queue<Node<V>> queue = new LinkedList<>();
    queue.add(root);
    currentNode = root;

    while (queue.size() > 0) {

        do{ // 每层节点遍历
            curNode = queue.poll();//取出当前队首元素，并删除
            currentLevelNodes++;

            if (curNode.left != null) {
                queue.add(curNode.left);
                nextNode = curNode.left;
            }

            if (curNode.right != null) {
                queue.add(curNode.right);
                nextNode = curNode.right;
            }


        }while (currentNode != curNode);// currentNode当前末尾节点，curNode当前遍历节点

        maxLevelNodes = Math.max(maxLevelNodes,currentLevelNodes);
        currentNode = nextNode;
        currentLevelNodes = 0;

    }
    System.out.println(maxLevelNodes);
    return maxLevelNodes;
}
```

### [Morris Traversal方法遍历二叉树（非递归，不用栈，O(1)空间）](https://www.cnblogs.com/anniekim/archive/2013/06/15/morristraversal.html)

```java
// 线索二叉树
public void morris(Node root){

    if(root == null) return;

    Node cur = root;
    Node mostRight = null;
    while (cur != null){

        // 寻找左子树的最右孩子
        mostRight = cur.left;

        if(mostRight != null) {
            while (mostRight.right != null && mostRight.right != cur) {
                mostRight = mostRight.right;
            }

            // mostRight变成了cur左子树上，最右节点
            if (mostRight.right == null) {
                mostRight.right = cur;
                cur = cur.left;
                continue;
            } else {
                mostRight.right = null;
            }
        }
        cur = cur.right;
    }
}

// 辅助函数，用于逆序打印的
public void printEdge(Node x){
    Node tail = reverseEdge(x);
    Node cur = tail;
    while (cur != null){
        System.out.println(cur.value);
        cur = cur.right;
    }
    reverseEdge(tail);
}

public Node reverseEdge(Node from){
    Node pre = null;
    Node next = null;
    while(from != null){
        next = from.right;
        from.right = pre;
        pre = from;
        from = next;
    }
    return pre;
}
```



### 二叉树题目

![image-20220304212137588](../../../../../Pictures/assets/剑指offer/image-20220304212137588.png)

* 思路

```html
搜索二叉树：
1. 通过中序遍历，只要是左树小，右树大（左根右）只要是升序就是搜索二叉树。（把中序遍历打印的存储，然后遍历查看是否是升序）（可以递归和非递归实现）
2. 搜索二叉树，每颗子树需要满足，左小右大。
3. 递归遍历的时候，需要记住左子树的最大值一定比根节点值小，右子树的最小值一定比根节点值大。

完全二叉树：按层级遍历，（1）任意一个节点有右无左子节点，返回false；（2）如果当前节点没有左右节点，则后面节点必须全是叶子节点。

满二叉树：求二叉树的深度level以及二叉树的所有节点数量nodes，两者关系nodes=2^level-1。（最大宽度的变体）

平衡二叉树：左右子树高度差小于等于1

// 树型DP，都是根据左树和右树找，然后进行合并
```

![image-20220304225016479](../../../../../Pictures/assets/剑指offer/image-20220304225016479.png)

<img src="../../../../../Pictures/assets/剑指offer/image-20220304230306601.png" alt="image-20220304230306601" style="zoom:33%;" />

* 思路

```html
比如：D和F网上找最低公共祖先节点是R。

可以使用Map存储Map(subNode,fatherNode);这样就能可以从子节点查找父节点。
比如D到根节点：D->R->A;F到根节点：D->E->R->A
这样就简化为了两个相交的单链表问题。。。。然后通过Hashset存储其中一条路径，然后遍历另外一条路径进行判断即可。

另一种思路：
（1）s是e的最低公共祖先，或者e是s的最低公共祖先。
（2）s和e彼此不会对方的最低公共祖先
```

![image-20220304234744805](../../../../../Pictures/assets/剑指offer/image-20220304234744805.png)

<img src="../../../../../Pictures/assets/剑指offer/image-20220304234826995.png" alt="image-20220304234826995" style="zoom:33%;" />

* 思路

```html
中序遍历：DBEAFCG，其中B为D的后继节点，E是B的后续节点...

但是成本高，因为需要每次都要中序遍历。

// 二叉树的线索化
找x的后继
（1）如果x有右树，则右子树节点，就是x的后继节点
（2）如果x没有右树，则找x的祖先节点，如果那个祖先节点是某一个节点y的左孩子节点，那么当前y就是x的后续节点。
（3）整颗树的右节点没有后续节点
```

![image-20220305000721159](../../../../../Pictures/assets/剑指offer/image-20220305000721159.png)

<img src="../../../../../Pictures/assets/剑指offer/image-20220305000831007.png" alt="image-20220305000831007" style="zoom:33%;" />

* 思路

```html
先序遍历：1_#_1_1_#_#_#_
```

微软折纸

* 思路

```html
动手找规律：开局折一次凹
			左凹      右凸
	      左凹 右凸  左凹 右凸
```

### 树形dp

![image-20220320091515211](../../../../../Pictures/assets/剑指offer/image-20220320091515211.png)

![image-20220320091633398](../../../../../Pictures/assets/剑指offer/image-20220320091633398.png)

```tex
以x为头节点

1）x不参与
左子树的最大距离
右子树的最大距离

2）x参与
左子树最远距离-右子树最远距离=》整颗树上的最大距离
=> 左子树高度+右子树高度+1

=>三者之间取最大值
```

```java
static class Info{
    public int maxDistance;
    public int height;

    public Info(int distance, int height) {
        this.maxDistance = distance;
        this.height = height;
    }
}

public static Info maxDistance(Node x){

    if(x == null) return new Info(0,0);

    Info left = maxDistance(x.left);
    Info right = maxDistance(x.right);

    // Info
    int p1 = left.maxDistance;
    int p2 = right.maxDistance;
    int p3 = left.height + right.height + 1;

    int maxDistance = Math.max(p1,Math.max(p2,p3));
    int height = Math.max(left.height ,right.height)+1;

    return new Info(maxDistance,height);
}
```

![image-20220320095709907](../../../../../Pictures/assets/剑指offer/image-20220320095709907.png)

![image-20220320094652391](../../../../../Pictures/assets/剑指offer/image-20220320094652391.png)

```tex
x 参与
x乐+a整颗树（a不来的最大快乐）+b整颗树（b不来的最大快乐）+c整颗树（c不来的最大快乐）

x 不参与
0+a整颗树（最大快乐(a不来，a来)）+b整颗树（最大快乐(b不来，b来)）+c整颗树（最大快乐(b不来，b来)）
```

```java
    public static class HappyInfo{
        public int laiHappy;
        public int buHappy;
        public HappyInfo(int lai,int bu){
            laiHappy = lai;
            buHappy = bu;
        }
    }
    class Employee {
        public int happy;
        public List<Employee> nexts;
    }
    public HappyInfo maxHappy(Employee x){
        if(x.nexts.isEmpty()) return new HappyInfo(x.happy,0);

        int lai = x.happy;
        int bu = 0;

        for(Employee next:x.nexts){
            HappyInfo nextInfo = maxHappy(next);
            lai += nextInfo.buHappy;
            bu += Math.max(nextInfo.laiHappy,nextInfo.buHappy);
        }

        return new HappyInfo(lai,bu);
    }
```

### 红黑树

#### 概述

1. 每个结点或是红色的，或是黑色的 
2. 根节点是黑色的 
3. 每个叶结点（NIL）是黑色的 
4. 如果一个节点是红色的，则它的两个儿子都是黑色的。 
5. 对于每个结点，从该结点到其叶子结点构成的所有路径上的黑结点个数相同。

#### 插入过程

**默认插入的结点为红色。**为何？ 
因为红黑树中黑节点至少是红节点的两倍，因此插入节点的父节点为黑色的概率较大，而此时并不需要作任何调整，因此效率较高。

* 父为黑

![image-20220302145556586](../../../../../Pictures/assets/剑指offer/image-20220302145556586.png)

插入后无需任何操作。由于黑节点个数至少为红节点的两倍，因此父为黑的情况较多，而这种情况在插入后无需任何调整，这就是红黑树比AVL树插入效率高的原因！

* 父为红

![image-20220302145621918](../../../../../Pictures/assets/剑指offer/image-20220302145621918.png)

父为红的情况破坏了红黑树的性质，此时需要根据叔叔的颜色来做不同的处理。

1. 叔叔为红 

![image-20220302145742412](../../../../../Pictures/assets/剑指offer/image-20220302145742412.png)



此时很简单，只需交换爸爸、叔叔和爷爷的颜色即可。 
此时若爷爷节点和太爷爷节点颜色相同，再以爷爷节点为起始节点，进行刚才相同的操作，即：根据爷爷的兄弟颜色做相应的操作。

2. 叔叔为黑 

此时较为复杂，分如下四种情况： 
**a）爸爸在左、叔叔在右、我在左** (右旋转)

![image-20220302150432119](../../../../../Pictures/assets/剑指offer/image-20220302150432119.png)



以爸爸为根节点，进行一次R旋转。 

**b）爸爸在左、叔叔在右、我在右** （左右旋转）

![image-20220302150558398](../../../../../Pictures/assets/剑指offer/image-20220302150558398.png)

先以我为根节点，进行一次L旋转； 再以我为根节点，进行一次R旋转。 

**c）叔叔在左、爸爸在右、我在左** （右左旋转）

![image-20220302150926831](../../../../../Pictures/assets/剑指offer/image-20220302150926831.png)

先以我为根节点，进行一次R旋转； 
再以我为根节点，进行一次L旋转。 

**d）叔叔在左、爸爸在右、我在右** 

![image-20220302151121696](../../../../../Pictures/assets/剑指offer/image-20220302151121696.png)

以爸爸为根节点，进行一次L旋转。

#### 删除过程

红黑树删除有6种情况，分别是：

##### 情况一：

> N 是新的根。在这种情形下，我们就做完了。我们从所有路径去除了一个黑色节点，而新根是黑色的，所以性质都保持着。

上面是维基百科中关于红黑树删除的情况一说明，由于没有配图，看的有点晕。经过思考，我觉得可能会是下面这种情形：

要删除的节点 X 是根节点，且左右孩子节点均为空节点，此时将节点 X 用空节点替换完成删除操作。

可能还有其他情形，大家如果知道，烦请告知。

##### 情况二：

S 为红色，其他节点为黑色。这种情况下可以对 N 的父节点进行左旋操作，然后互换 P 与 S 颜色。但这并未结束，经过节点 P 和 N 的路径删除前有3个黑色节点（`P -> X -> N`），现在只剩两个了（`P -> N`）。比未经过 N 的路径少一个黑色节点，性质5仍不满足，还需要继续调整。不过此时可以按照情况四、五、六进行调整。

![640?wx_fmt=jpeg](../../../../../Pictures/assets/剑指offer/afba258b8b1f76871981906bc95bfc86.png)

###### 情况三：

N 的父节点，兄弟节点 S 和 S 的孩子节点均为黑色。这种情况下可以简单的把 S 染成红色，所有经过 S 的路径比之前少了一个黑色节点，这样经过 N 的路径和经过 S 的路径黑色节点数量一致了。但经过 P 的路径比不经过 P 的路径少一个黑色节点，此时需要从情况一开始对 P 进行平衡处理。

![640?wx_fmt=jpeg](../../../../../Pictures/assets/剑指offer/e58a6a2a6fed17464bacca704d108e2f.png)

##### 情况四：

N 的父节点是红色，S 和 S 孩子为黑色。这种情况比较简单，我们只需交换 P 和 S 颜色即可。这样所有通过 N 的路径上增加了一个黑色节点，所有通过 S 的节点的路径必然也通过 P 节点，由于 P 与 S 只是互换颜色，并不影响这些路径。

![640?wx_fmt=jpeg](../../../../../Pictures/assets/剑指offer/ea1e8b91e8b939d6139a1395410180fc.png)

这里需要特别说明一下，上图中的节点 N 并非是新插入的节点。当 P 为红色时，P 有两个孩子节点，且孩子节点均为黑色，这样从 G 出发到各叶子节点路径上的黑色节点数量才能保持一致。既然 P 已经有两个孩子了，所以 N 不是新插入的节点。情况四是由以 N 为根节点的子树中插入了新节点，经过调整后，导致 N 被变为红色，进而导致了情况四的出现。考虑下面这种情况（PR 节点就是上图的 N 节点）：

![640?wx_fmt=jpeg](../../../../../Pictures/assets/剑指offer/0059a042d3bfa4c927144065c0c94e6d.png)

##### 情况五：

S 为黑色，S 的左孩子为红色，右孩子为黑色。N 的父节点颜色可红可黑，且 N 是 P 左孩子。这种情况下对 S 进行右旋操作，并互换 S 和 SL 的颜色。此时，所有路径上的黑色数量仍然相等，N 兄弟节点的由 S 变为了 SL，而 SL 的右孩子变为红色。接下来我们到情况六继续分析。

![640?wx_fmt=jpeg](../../../../../Pictures/assets/剑指offer/f7608901e07e183bb8d01bb6f07d0def.png)

##### 情况六：

S 为黑色，S 的右孩子为红色。N 的父节点颜色可红可黑，且 N 是其父节点左孩子。这种情况下，我们对 P 进行左旋操作，并互换 P 和 S 的颜色，并将 SR 变为黑色。因为 P 变为黑色，所以经过 N 的路径多了一个黑色节点，经过 N 的路径上的黑色节点与删除前的数量一致。对于不经过 N 的路径，则有以下两种情况：

1. 该路径经过 N 新的兄弟节点 SL ，那它之前必然经过 S 和 P。而 S 和 P 现在只是交换颜色，对于经过 SL 的路径不影响。
2. 该路径经过 N 新的叔叔节点 SR，那它之前必然经过 P、 S 和 SR，而现在它只经过 S 和 SR。在对 P 进行左旋，并与 S 换色后，经过 SR 的路径少了一个黑色节点，性质5被打破。另外，由于 S 的颜色可红可黑，如果 S 是红色的话，会与 SR 形成连续的红色节点，打破性质4（每个红色节点必须有两个黑色的子节点）。此时仅需将 SR 由红色变为黑色即可同时恢复性质4和性质5（从任一节点到其每个叶子的所有简单路径都包含相同数目的黑色节点。）。

![640?wx_fmt=jpeg](../../../../../Pictures/assets/剑指offer/3fc373511b51a906887e62a60482ad14.png)

##### 删除总结

红黑树删除的情况比较多，大家刚开始看的时候可能会比较晕。可能会产生这样的疑问，为啥红黑树会有这种删除情况，为啥又会有另一种情况，它们之间有什么联系和区别？

和大家一样，我刚开始看的时候也有这样的困惑，直到我把所有情况对应的图形画在一起时，拨云见日，一切都明了了。此时天空中出现了4个字，原来如此、原来如此、原来如此。所以，请看图吧：

![640?wx_fmt=jpeg](../../../../../Pictures/assets/剑指offer/5aeeaf096931a84a86808ee29257bc98.png)



## 图

节点定义：

```java
public class GraphNode {
    public int value; // 节点值
    public int in; //节点入度
    public int out;//节点出度
    public ArrayList<GraphNode> neighbors;// 当前节点的所有邻居集合
    public ArrayList<GraphEdge> edges;//与节点相连的所有边集合

    public GraphNode(int value){
        this.value = value;
        this.in = 0;
        this.out = 0;
        this.neighbors = new ArrayList<>();
        this.edges = new ArrayList<>();
    }
    
}
```

边的定义：

```java
public class GraphEdge {
    private int weight; // 边权值
    private GraphNode from; //边的起始节点，在无向图中，默认为小的节点即可
    private GraphNode to;  // 边的末尾节点

    public GraphEdge(int weight,GraphNode from,GraphNode to){
        this.weight = weight;
        this.from = from;
        this.to = to;
    }
}
```

图的定义：

```java
public class Graph {
    public HashMap<Integer,GraphNode> nodes;
    public HashSet<GraphEdge> edges;

    public Graph(){
        nodes = new HashMap<>();
        edges = new HashSet<>();
    }
}
```

* 邻接表法：每个点后面跟着相邻的节点，一般通过链表实现，或者队列
* 邻接矩阵：通过二维数组将每个点的信息进行记录
* 三元组：(v,n1,n2)其中n1与n2的值为v。

### 图遍历

![image-20220305205018228](../../../../../Pictures/assets/剑指offer/image-20220305205018228.png)

### 拓扑排序

![image-20220306191149069](../../../../../Pictures/assets/剑指offer/image-20220306191149069.png)

```html
先输出所有入度为0的节点，然后消除当前节点的影响即入度，出度。
依次循环。
```

### 生成最小生成树

![image-20220306193347099](../../../../../Pictures/assets/剑指offer/image-20220306193347099.png)



```html
将所有边进行排序，然后从最小权值的边进行选取。每次选取后，判断是否有环。

通过并查集，这样被选取的边上的节点，就可以连起来了；下次选取边的时候，就可以判断当前边的节点是否已经相连了。
```

![image-20220306200028732](../../../../../Pictures/assets/剑指offer/image-20220306200028732.png)

![image-20220306200558649](../../../../../Pictures/assets/剑指offer/image-20220306200558649.png)

```html
1. 将解锁的边放到优先队列中
2. 将使用过的节点放到集合中
3. 每次遍历优先选取最小的边，以及判断当前边的节点是否使用过
```

### 最短路径算法

```html
Dijkstra（权值不能为负数）算法采用的是一种贪心的策略，声明一个数组dis来保存源点到各个顶点的最短距离和一个保存已经找到了最短路径的顶点的集合：T，初始时，原点 s 的路径权重被赋为 0 （dis[s] = 0）。若对于顶点 s 存在能直接到达的边（s,m），则把dis[m]设为w（s, m）,同时把所有其他（s不能直接到达的）顶点的路径长度设为无穷大。初始时，集合T只有顶点s。
然后，从dis数组选择最小值，则该值就是源点s到该值对应的顶点的最短路径，并且把该点加入到T中，OK，此时完成一个顶点，
然后，我们需要看看新加入的顶点是否可以到达其他顶点并且看看通过该顶点到达其他点的路径长度是否比源点直接到达短，如果是，那么就替换这些顶点在dis中的值。
然后，又从dis中找出最小值，重复上述动作，直到T中包含了图的所有顶点。


void dijkstra(int s){
    //默认对pair.first 的大小进行排序，greater<class>构成一个小顶堆
    priority_queue<P,vector<P>,greater<P> > que;
    //初始化距离
    memset(d,INF,sizeof d);
    d[s] = 0;
    que.push(P(0,s)); //把起点推入队列
    while(!que.empty())
    {
        P p = que.top(); que.pop();
        int v = p.second; //顶点的编号
        if (d[v] < p.first) continue;//d[v]可能经过松弛后变小了，原压入堆中的路径失去价值
        for(int i = 0; i < G[v].size(); i++){//利用最短边进行松弛
            edge e = G[v][i];
            if (d[e.to] > d[v] + e.cost){
                d[e.to] = d[v] + e.cost;
                que.push(P(d[e.to],e.to));
            }
        }
    }
}



floyd：通过三个循环i,j,k

第1步：初始化map矩阵。矩阵中map[i][j]的距离为顶点i到顶点j的权值；
	如果i和j不相邻，则map[i][j]=∞。
	如果i==j，则map[i][j]=0;                                          
第2步：以顶点A(假设是第1个顶点)为中介点，若a[i][j] > a[i][1]+a[1][j]，则设置a[i][j]=a[i]
[1]+a[1][j]。
```

## 贪心算法

在某一个标准下，优先考虑最满足标准的样本，最后考虑最不满足标准的样本，最终得到一个答案的算法，叫作贪心算法。

也就是说，不从整体最优上加以考虑，所做出的是在某种意义上的局部最优解。

局部最优 -?-> 整体最优

![image-20220313170641313](../../../../../Pictures/assets/剑指offer/image-20220313170641313.png)

```html
思路-贪心：那个会议的宣讲时间先介绍，就先进行安排
```

```java
public class Greedy {

    public static class Program{
        public int start;
        public int end;
    }

    public static class ProgramCompartor implements Comparator<Program> {
        @Override
        public int compare(Program o1, Program o2) {
            return o1.end - o2.end;
        }
    }


    public int bestArray(Program[] programs,int timePoint){
        Arrays.sort(programs,new ProgramCompartor());

        int result = 0;

        for(int i = 0;i < programs.length;i++){
            if(timePoint <= programs[i].start){
                result++;
                timePoint = programs[i].end;
            }
        }
        return result;
    }
    
}
```

![image-20220313171839543](../../../../../Pictures/assets/剑指offer/image-20220313171839543.png)

```html
字符串拼接，将字符串数组进行拼接为一个字典序最小的字符串。

思路：使用比较器

public class Greedy {

    public static class MinStrCompartor implements Comparator<String>{

        @Override
        public int compare(String o1, String o2) {

            // "b"+"ba" ? "ba" + "b"
            return (o1+o2).compareTo(o2+o1);
        }
    }

    public void bestMinStr(String[] words){
        Arrays.sort(words,new MinStrCompartor());
        String str = "";
        for(String word:words){
            str += word;
        }
        System.out.println(str);
    }

    public static void main(String[] args) {
        Greedy greedy = new Greedy();
        String[] words = {"b","ba"};

        greedy.bestMinStr(words);
    }

}
```

![image-20220313200045678](../../../../../Pictures/assets/剑指offer/image-20220313200045678.png)

```html
思路：哈夫曼树，选出最小的两个数，然后相加，又返回去，继续...
```

![image-20220313210930844](../../../../../Pictures/assets/剑指offer/image-20220313210930844.png)

```html
通过使用两个堆，即大根堆和小根堆

比如 5 3 7 4

5，进入大根堆（5）
3，3《5，进入大根堆（5，3），由于数量比小跟堆多2，因此为了平衡，将堆顶给小根堆（5），大根堆（3）
7，7》3，因此将7进入到小根堆（5，7）
4，4》3，因此将4进入到小根堆（4，5，7），比大根堆多2，因此将小根堆（5，7）堆顶给大根堆（4，3）

因此可以发现中位数，一直在两个堆的堆顶。
```

![image-20220313212522192](../../../../../Pictures/assets/剑指offer/image-20220313212522192.png)

```html
思路：深度优先遍历

1. 搜索当前行是否有棋子
2. 搜索当前列是否有棋子
3. 搜索当前斜线上是否有棋子

通过record[n]记录每个皇后摆放的位置。
```

![image-20220313215923510](../../../../../Pictures/assets/剑指offer/image-20220313215923510.png)

```html
但是耗时，使用位运算进行优化

1表示存在，0表示不存在

比如，列在 	0000100000000...000
比如，行在	0000010000000...000
比如，斜线在 0000001000000...000

异或运算  0000111000000...000 表示当前皇后的三个限制
```

![image-20220313234619866](../../../../../Pictures/assets/剑指offer/image-20220313234619866.png)

## 暴力递归

暴力递归就是尝试

1. 把问题转化为规模缩小了的同类问题的子问题

2. 有明确的不需要继续进行递归的条件(base case)

3. 有当得到了子问题的结果之后的决策过程

4. 不记录每一个子问题的解

![image-20220314145252182](../../../../../Pictures/assets/剑指offer/image-20220314145252182.png)

```html
每次只能移动一次，需要保证小盘一定在大盘上方。一共有三个杆。
初始值：左边杆有三个，从小到大。
要求：需要全部移动到最右边杆
```

![image-20220314160525722](../../../../../Pictures/assets/剑指offer/image-20220314160525722.png)

```java
public void allSubsequence(char[] word,int i,List<Character> res){

    if(i == word.length){
        //            this.print(res);
        System.out.println(String.valueOf(word).replace("\0", ""));
        return;
    }

    //        List<Character> newRes = new ArrayList<>(res);
    //        newRes.add(word[i]));
    //        allSubsequence(word,i+1,newRes);
    //        allSubsequence(word,i+1,res);

    allSubsequence(word,i+1,res);
    char tmp = word[i];
    word[i] = '\0';
    allSubsequence(word,i+1,res);
    word[i] = tmp;
}
```

![image-20220314162618188](../../../../../Pictures/assets/剑指offer/image-20220314162618188.png)

```java
    public void fullPermutation(char[] word,int i,HashSet<String> res){

        if(i == word.length){
            res.add(String.valueOf(word).replace("\0",""));
        }

        // 元素可能有重复元素，导致排列重复，通过设置访问标记进行控制
        boolean[] visit = new boolean[26];

        for(int j = i ;j<word.length; j++){
            if(word[j] != '\0' && !visit[word[j] - 'a']) {
                visit[word[j]-'a'] = true;
                swap(word, i, j);
                fullPermutation(word, i + 1, res);
                swap(word, i, j);
            }
        }

    }
```

![image-20220314165147339](../../../../../Pictures/assets/剑指offer/image-20220314165147339.png)

```java
public int f(int[] arr,int i,int j){
    if(i == j) return arr[i]; // 这个表示只有一个数了，那么当前直接拿
    return Math.max(arr[i]+s(arr,i+1,j),arr[j]+s(arr,i,j-1));
}

public int s(int[] arr,int i,int j){
    if(i == j) return 0;
    return Math.min(f(arr,i+1,j),f(arr,i,j-1));
}

public int gameProblem(int[] arr){

    if(arr.length == 0 || arr == null) return 0;

    return Math.max(f(arr,0,arr.length-1),s(arr,0,arr.length-1));

}
```

![image-20220314174201841](../../../../../Pictures/assets/剑指offer/image-20220314174201841.png)

```java
public void recurStack(Stack stack){

    if(stack.isEmpty()){
        return;
    }

    Object cur = stack.pop();

    recurStack(stack);

    stack.push(cur);

    while (!stack.isEmpty()){
        System.out.println(stack.pop());
    }

}
```

![image-20220314181241845](../../../../../Pictures/assets/剑指offer/image-20220314181241845.png)

```java
public int switchCharacter(char[] arr,int i){
    if(arr.length <= 1) return arr.length;
    if(i == arr.length) return 1;
    if(arr[i] == '0') return 0;

    int res = switchCharacter(arr,i+1);// 如果每个数字都转换为一个字母，那么只能有一种结果
    if(i+1<arr.length){
        int a = arr[i] - '0';
        int b = arr[i+1] - '0';
        int c = a*10+b;

        if(c>=10&&c<=26){
            res+=switchCharacter(arr,i+2);
        }
    }
    return res;
}
```

![image-20220314195115442](../../../../../Pictures/assets/剑指offer/image-20220314195115442.png)

```java
public int knapsackProblem(int[] weights,int[] values,int bag,int i,int alreadyWeight){
    if(i == weights.length) return 0;
    if(alreadyWeight > bag) return 0;

    return Math.max(
        knapsackProblem(weights,values,bag,i+1,alreadyWeight+weights[i])+values[i],
        knapsackProblem(weights,values,bag,i+1,alreadyWeight)
    );
}
```

## 字符串算法

### 前缀树（字典树）

何为前缀树?如何生成前缀树?

例子:一个字符串类型的数组arr1，另一个字符串类型的数组arr2。arr2中有哪些字符，是arr1中出现的?请打印。arr2中有哪些字符，是作为arr1中某个字符串前缀出现的?请打印。arr2中有哪些字符，是作为arr1中某个字符串前缀出现的?请打印 arr2中出现次数最大的前缀。

```html
class TrieNode{

    public int pass;    //用于记录当前字符经过的次数
    public int end;     // 用于判断是否是单词末尾字符，可以统计当前单词个数
    public TrieNode[] nexts; // 当前字符的所有子路径，HashMap<Char,Node> nexts;

    public TrieNode(){
        pass = 0;
        end = 0;
        nexts = new TrieNode[26]; // 每个字符的所有子路径
    }

}

public class TrieTree {

    private TrieNode root;

    public TrieTree(){
        root = new TrieNode();
    }

    // 将字符串数组，构建成字典树
    public void buildTrieTree(String[] words){
        for(String word:words){
            insertTrieNode(word);
        }
    }

    public void insertTrieNode(String word){
        if(word == null) return;

        char[] arr = word.toCharArray(); // 转换为字符数组

        TrieNode node = root;
        node.pass++;  //这样可以记录总共有多少单词

        int index = 0;

        for(int i=0;i<arr.length;i++){
            index = arr[i] - 'a';

            if(node.nexts[index] == null){
                node.nexts[index] = new TrieNode();
            }
            node = node.nexts[index]; // 切换到一个字符路径
            node.pass++;
        }
        node.end++;
    }

    public void deleteTrieNode(String word){
        if(search(word) == 0) return; // 先判断当前字符串是否存在，不存在就不继续进行

        char[] arr = word.toCharArray();
        TrieNode node = root;
        node.pass--; // 删除后，就少一个单词

        int index = 0;

        for(int i=0;i<arr.length;i++){
            index = arr[i] - 'a';
            node.nexts[index].pass--;

            if(node.nexts[index].pass == 0){
               node.nexts[index] = null;
               return;
            }

            node = node.nexts[index];

        }
        node.end--;
    }

    // 统计单词加入过几次
    public int search(String word){
        if(word == null) return 0;

        char[] arr = word.toCharArray();

        TrieNode node = root;

        int index = 0;

        for(int i=0;i<arr.length;i++){
            index = arr[i] - 'a';
            if(node.nexts[index] == null){
                return 0;
            }
            node = node.nexts[index];
        }
        return node.end;
    }

    // 统计以pre字符串作为前缀的单词数量
    public int prefixNumber(String pre){
        if(pre == null) return 0;

        char[] arr = pre.toCharArray();

        TrieNode node = root;
        int index = 0;

        for(int i=0;i<arr.length;i++){
            index = arr[i] - 'a';
            if(node.nexts[index] == null) {
                return 0;
            }
            node = node.nexts[index];

        }
        return node.pass;
    }

    public static void main(String[] args) {
        String[] arr1 = {"abc","bck","abd","ace"};
        TrieTree tree = new TrieTree();
        tree.buildTrieTree(arr1);
    }

}
```

### KMP算法

![image-20220318231845021](../../../../../Pictures/assets/剑指offer/image-20220318231845021.png)

```tex
abbabb

前缀 a ab abb abba abbab
后缀 b bb abb babb bbabb
=> 最大子序相等长度为3


aaaaa
前缀 a aa aaa aaaa
后缀 a aa aaa aaaa
=> 最大子序相等长度为4

=》 计算的是str2

===========================================

aabaabsaabaabst

-1 =>由于第一个a前面没有字符，人为规定-1
0  =>由于第二个a前面只有一个a，但是不能取整体，因此为0
1  =>由于第三个b前面为aa，但是不能取整体，因此为1，aa的前后缀子序字符相等的最大长度
=>[-1,0,1,0,1,2,3.....] =>nexts数组
```

![image-20220319124651312](../../../../../Pictures/assets/剑指offer/image-20220319124651312.png)



```java
public int[] getNext(char[] str){

    int n = str.length;
    int[] next = new int[n];
    next[0] = -1; // 表示第一个字符前面没有可匹配
    next[1] = 0;  // 表示第二字符前面只有一个字符前缀

    int i = 2;    // next数组的位置
    int j = 0;

    while(i < next.length){
        if(str[i-1] == str[j]){
            next[i++] = ++j;
        }else if(j > 0){ // 当前跳到j位置的字符，和i-1位置的字符匹配不上
            j = next[j];
        }else{ // 从0开始进行匹配
            next[i++] = 0;
        }
    }
    return next;
}

public int kmp(String str1,String str2){

    if(str1.length() < str2.length() || str1 == null || str2 == null || str2.length() <= 0){
        return -1;
    }

    int i = 0,j = 0;
    int[] next = getNext(str2.toCharArray());

    while(i < str1.length() && j < str2.length()){
        if(str1.charAt(i) == str2.charAt(j)){
            i++;
            j++;
        }else if(next[j] == -1){ // 当前str2中字符串无法往前跳了，就表示需要将i往前走一步，重新匹配
            i++;
        }else{
            j = next[j];
        }
    }

    return j == str2.length()?i-j:-1;
}
```

### Manacher算法

![image-20220319151220329](../../../../../Pictures/assets/剑指offer/image-20220319151220329.png)

传统算法：对于源字符串中插入`#`字符，这样就很好进行判断，最后除以2就是当前的回文字符串长度。时间复杂度为$Big\ O(N^2)$

![image-20220319152224671](../../../../../Pictures/assets/剑指offer/image-20220319152224671.png)

manacher算法：与kmp相似，原来算法不同，只是在原来基础上加速。

```java
    public int manacher(String str){

        // 预处理：将121 转换为 #1#2#1#
        char[] chrs = new char[str.length()*2+1];
        for(int i=0;i < chrs.length;i++){
            chrs[i] = (i&1) == 0 ?'#':str.charAt(i/2);
        }

//        System.out.println(String.valueOf(chrs));

        // 申请一个记录回文半径的数组
        int[] radius = new int[chrs.length];
        int C = -1; // 当前回文中心点
        int R = -1; // 回文右边界的再往右一个位置，最右的有效区是R-1位置
        int maxRadius = Integer.MIN_VALUE; // 记录最大回文字符串长度

        for(int i=0;i != chrs.length;i++){ // 每一个位置都求回文半径

            // 至少的回文区域
            // 1. i在R外面，至少有一个回文
            // 2. 如果不是，就进行讨论
            //      1) i'在回文区域内部，即在(L,R)
            //      2) i'在回文区域，但是有一部分在(L,R)外
            //          C - i' = i - C=> i'=2C-i，当前i'的回文字符串长度
            //          R-i，有可能i'的回文字符串长度在(L,R)外

            radius[i] = R > i?Math.min(radius[2*C-i],R-i):1;

            // i在R外面，那么就从i开始两边暴力扩 || 3) i'在回文区域内，但是半径长度正好在左边界上，那么就从R之外的字符开始，往外扩
            // str.charAt(i+radius[i]) == str.charAt(i-radius[i]) 当前末尾字符是否相等，相等就往扩，记住这里的R是有效边界+1的位置，所以直接在这里判断
            // i + radius[i] < str.length() && i - radius[i] >= 0 这个是[L,R]范围半径
            while (i + radius[i] < chrs.length && i - radius[i] >=0 && chrs[i+radius[i]] == chrs[i-radius[i]]){
                radius[i]++;
            }

            // 如果半径扩大，就更新
            if(i + radius[i] > R){
                R = i + radius[i];
                C = i;
            }

            maxRadius = Math.max(maxRadius,radius[i]);
        }

        maxRadius = maxRadius < 0?-1:maxRadius-1;

//        System.out.println(maxRadius);

        return maxRadius ;
    }
```

## 滑动窗口

![image-20220319171447969](../../../../../Pictures/assets/剑指offer/image-20220319171447969.png)

```tex
双端队列：从大到小进行排列，保证双端队列的大头方向
滑动窗口就是一个双端队列
    
3 2 4 6 3 5 4 3 5
```

## 单调栈 

![image-20220319224818846](../../../../../Pictures/assets/剑指offer/image-20220319224818846.png)

```tex
5 4 6 7 2 3 0 1

单调栈：需要保证从大到小

stack:5 4 3 ，放6，需要依次弹出3 4 5并记录左右距离最大的数
依次如此
```

 ![image-20220319233823599](../../../../../Pictures/assets/剑指offer/image-20220319233823599.png)

```java
5 3 2 1 6 7 8 4
```

## 有序表

何为具有平衡性的树？

介绍树的左旋

![image-20220327205330818](../../../../../Pictures/assets/剑指offer/image-20220327205330818.png)

```tex
左旋：指的是头结点往那边倒，往左倒，就是左旋

C的左孩子由A来接管
```

介绍树的右旋

![image-20220327205455445](../../../../../Pictures/assets/剑指offer/image-20220327205455445.png)

```tex
右旋：头节点倒向右边

B的右孩子交给根节点C来 接管
```

### 搜索二叉树

左小右大  

删除一个节点a：如果有左右孩子，直接找到右孩子的最左孩子节点b，然后将该节点b下面的所有节点给其父节点c，然后节点b替换节点a即可。

### 红黑树

 

### AVL

### SB

### 跳表skiplist

## 并查集

![image-20220318101021823](../../../../../Pictures/assets/剑指offer/image-20220318101021823.png)

```java
public class UnionFind {

    HashMap<Integer, Integer> sizeMap = new HashMap<>();

    // 寻找当前节点的祖先节点-根节点
    public int find(int[] f, int i) {

        Stack<Integer> path = new Stack<>();
        while (f[i] != i) {
            path.push(i);
            i = f[i];
        }
        // 扁平化管理
        while (!path.isEmpty()) {
            f[path.pop()] = i;
        }

        return i;
    }

    //找到两个节点的祖先节点，然后让其中一个祖先节点挂在另一个祖先节点上
    public void union(int[] f, int i, int j) {
        int af = find(f, i);
        int bf = find(f, j);
        if (af != bf) {
            int big = sizeMap.get(af) >= sizeMap.get(bf) ? af : bf;
            int small = big == af ? bf : af;

            f[small] = big;

            sizeMap.put(big, sizeMap.get(af) + sizeMap.get(bf));
            sizeMap.remove(small);
        }
    }

    // 判断两个节点是否在同一个集合上
    public boolean isSameSet(int f[], int i, int j) {
        return find(f, i) == find(f, j);
    }

    public void solution(int[][] arr) {

        int n = arr.length;
        int m = arr[0].length;

        int[] f = new int[n*m];
        Arrays.fill(f, -1); // 全部赋值为1，表示没有使用过

        for(int i=0;i<n;i++) {
            for (int j = 0; j < m; j++){
                f[i * m + j] = i * m + j;
                if(arr[i][j] == 1)
                    sizeMap.put(i*m+j, 1);
            }
        }

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {

                int idx =i*m+j;
                if(arr[i][j] == 1) {
                    if(i+1<n && arr[i+1][j] == 1){
                        this.union(f,idx, (i+1)*m+j);
                    }
                    if(j+1<m && arr[i][j+1] == 1){
                        this.union(f,idx, i*m+j+1);
                    }
                }
            }
        }

        System.out.println(sizeMap.size());

    }

    public static void main(String[] args) {
        int[][] arr = {
                {0, 0, 1, 0, 0, 1, 0},
                {1, 1, 1, 0, 0, 1, 0},
                {1, 0, 0, 0, 0, 0, 0},
                {0, 0, 0, 0, 0, 0, 0}
        };
        UnionFind unionFind = new UnionFind();
        unionFind.solution(arr);
    }

}
```

## 线段树

## 哈希算法

```tex
40亿个数，只有1G内存，返回出现最多次数的数/查询某个值

通过hash算法将40亿个数据，分开存储，比如分成100个文件，逐一读取单个数进行计算hash值
然后hash值一样会在统一个表中，然后再使用hashmap进行统计，这样内存就不会爆掉了
```

![image-20220317150604389](../../../../../Pictures/assets/剑指offer/image-20220317150604389.png)

![image-20220317151610022](../../../../../Pictures/assets/剑指offer/image-20220317151610022.png)

```tex
用来解决去重问题...

需要先知道什么是bitmap（位图） 二进制

一般情况使用基础类型进行拼接。

重点位图的长度的设置以及hash函数的个数设置

长度设置短，会导致hash碰撞
```

布隆过滤器，$1)\ n=$样本量，$2)\ p=$失误率，$3)\ m=-\frac{n\times \ln{p}}{(\ln{2})^2}$，$4)\ k=\frac{m}{n}*\ln{2}$，$5)\ p真 = (1-e ^{-\frac{n*k{真}}{m{真}}})^{k{真}}$

一致性哈希原理：

在分布式中数据，保持数据一致性。对于一系列数据，通过hash函数进行计算hashcode，将当前数据分配到其他机器上，便于管理。 

高频数据，中频数据，低频数据都是均匀分布的，就是均衡负载。

增加机器后，数据需要重新迁移。可以通过一致性哈希进行解决。

```text
将所有机器计算hashcode，根据hashcode放到一个环的位置上。
现在来了一个数，计算hashcode，后放在一个环上，然后找到离该位置最近的机器。

但是存在两个问题：加上一个机器可能会导致不均衡。
```

![image-20220318001611136](../../../../../Pictures/assets/剑指offer/image-20220318001611136.png)

```tex
每个机器生成1000个hashcode，这样就能很好方便放在环上了。
高频、低频也能解决了。如果是高频就多生成1000个hashcode，如果是低频就生成500个hashcode。
```



![image-20220318002111275](../../../../../Pictures/assets/剑指offer/image-20220318002111275.png)



## 大数据题目

![image-20220320134923153](../../../../../Pictures/assets/剑指offer/image-20220320134923153.png)

bitmap和布隆过滤器

从海量数据查找对应值，比如查找QQ，使用bitmap

```html
申请21位，大约41亿数据，够用了，当前QQ进行映射为hashcode，然后再对应的bit位上赋值为1。就类似打卡。
```

![image-20220320134707639](../../../../../Pictures/assets/剑指offer/image-20220320134707639.png)

如果只有3kb的，使用整形数组，需要申请3kb/4>512位，然后将$2^{32}$按照512位等量划分为838608个。然后使用二分查找法，进行查找。

![image-20220320134555679](../../../../../Pictures/assets/剑指offer/image-20220320134555679.png)

```text
万能方法：哈希表分流--
```



![image-20220320135717976](../../../../../Pictures/assets/剑指offer/image-20220320135717976.png)

```tex
如果内存降低到10kb，怎么做
```

现有10G文件，只有5G内存，怎么做到有符号数排序？

```
 5G = 5*2^30
 每条记录大概占用4字节，但是由额外空间消耗，5G/(8*2)=5*2^26 》 2^27
 
整数范围 -2^31 ~ 2^31-1 => 一共 2^32

将记录可以等分为 2^32 / 2^27 = 2^5

就可以哈希分流使用小根堆排序并记录词频。


最优解：
10亿个数，假设小根堆只能装下500万个数，设置一条记录为(val,count)
```

比较a和b的值，不要使用条件判断

```java
class Compare {
public:
    int getMax(int a, int b) {
        int c = a - b;
        int sa = sign(a);
        int sb = sign(b);
        int sc = sign(c);
        int diffab = sa ^ sb;
        int sameab = filp(diffab);
        int returnA = sameab * sc + diffab * sa;
        int returnB = filp(returnA);
        return returnA * a + returnB * b;
    }
private:
    int sign(int n){
        return filp((n >> 31) & 1);  //注意这里有一次filp，因为正数为1
    }
    int filp(int n){
        return n ^ 1;
    }
};
```

![image-20220321100505304](../../../../../Pictures/assets/剑指offer/image-20220321100505304.png)

```tex
2的幂，只有一位上是1，其余是0，就是取最右边的1，然后与原来的数相比是否相等。
可以通过num & (num-1)消去num最后一位1进行快速比较，因为只有一位1，消去后，就为0。

4的幂，同理，(num & (num-1)) ==0 && (num & 0x55555555) !=0
```

![image-20220321102046305](../../../../../Pictures/assets/剑指offer/image-20220321102046305.png)

```tex
前提：
			13:0 1 1 0 1
			7 :0 0 1 1 1
异或^           0 1 0 1 0
与运算&          0 0 1 0 1

加法:(a^b) + (a&b)<<1，一直重复计算下去，直到没有进位
减法：就是a+b的相反数，一个数num的相反数为(~num+1)
乘法：就是b的每一位乘以a，并且左移i位，然后相加。
除法：
```

```java
public int add(int a,int b){
    //加法
    int a1 = a ^ b;
    int b1 = (a & b) << 1;

    while(a1!=0 && b1 != 0){
        a = a1 ^ b1;
        b = (a1 & b1) << 1;

        a1 = a;
        b1 = b;
    }
    return a1 ^ b1;
}
public int subtraction(int a,int b){
    b = add(~b,1);
    return add(a,b);
}

public int multi(int a,int b){
    int res = 0;
    while(b != 0){
        if((b & 1) != 0){
            res = add(res,a);
        }
        a <<= 1;
        b >>>= 1;
    }
    return res;
}
public int div(int a,int b){

    int ans = 0;
    int residue = 0;

    if(b == 0) return 0;

    int ag = (a > 0?1:-1);
    int bg = (b > 0?1:-1);
    int c = ag == bg?1:-1;

    a = ag > 0?a:add(~a,1);
    b = bg > 0?b:add(~b,1);

    while(a >= b){
        a = this.subtraction(a,b);
        ans++;
    }
    residue = a;//余数

    if(c < 0) ans = add(~ans,1);

    System.out.println(ans+":"+residue);
    return ans;
}
```

## 暴力递归到动态规划

递归 `->` 记忆化搜索dp`->`严格结构dp （将递归画成表格，进行研究，找规律）

> 空间换时间

机器人指定k步，从s位置到e位置范围能有多少中走法

```java
public int f(int n,int e,int rest,int cur,int[][] dp){
    if(dp[rest][cur] != -1) return dp[rest][cur];

    int ans = 0;
    if(rest == 0){
        ans = cur == e?1:0;
    } else if(cur == 1){
        ans = f(n,e,rest-1,cur+1,dp);
    } else if(cur == n){
        ans = f(n,e,rest-1,cur-1,dp);
    }else {
        ans = f(n,e,rest-1,cur-1,dp) + f(n,e,rest-1,cur+1,dp);
    }
    dp[rest][cur] = ans;
    return dp[rest][cur];
}

public void way(int n,int e,int k,int s){
    int[][] dp = new int[k+1][n+1];
    for(int i=0;i<=k;i++){
        for(int j=0;j<=n;j++){
            dp[i][j] = -1;
        }
    }
    System.out.println(f(n,e,k,s,dp));
    for(int i=0;i<=k;i++){
        for(int j=0;j<=n;j++){
            System.out.print(dp[i][j] + " ");
        }
        System.out.println();
    }
}
```

画出table表：动态规划是直接把暴力递归的思路直接架空，拿出所对应的确定的值进行计算。

给定指定硬币，从一堆硬币中选出最少硬币进行换取。

````java
public int minCoins(int[] arr,int aim){
    return minCoins(arr,0,aim);
}

// index：当前选取的硬币位置
// rest：当前剩余没有换取的硬币值
public int minCoins(int[] arr,int index,int rest){
    if(rest < 0) return -1; //无法换取
    if(rest == 0) return 0;//解决了，不再需要硬币了
    if(index == arr.length) return -1;//没有硬币可选，无法换取

    int first = minCoins(arr,index+1,rest);
    int second = minCoins(arr,index+1,rest-arr[index]);

    if(first == -1 && second == -1) return -1;
    else if(first == -1) return second+1;
    else if(second == -1) return first;
    return Math.min(first,second+1);
}
````

博弈问题，[3,100,4,50]，A先手，B后手，谁最后拿到的分数最大

```java
public int f(int[] arr,int i,int j){
    if(i == j) return arr[i];
    return Math.max(arr[i]+s(arr,i+1,j),arr[j]+s(arr,i,j-1));
}

public int s(int[] arr,int i,int j){
    if(i == j) return 0;
    return Math.min(s(arr,i+1,j),s(arr,i,j-1));
}

public int win(int[] arr){
    return Math.max(f(arr,0,arr.length-1), s(arr,0,arr.length-1));
}
```

![image-20220327141421155](../../../../../Pictures/assets/剑指offer/image-20220327141421155.png)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       



一个棋盘10x9大小，其中一个棋子马位置在(0,0)，目标位置(a,b)，指定跳动的k步，在这个范围中，有多少中跳法。

![image-20220327141854350](../../../../../Pictures/assets/剑指offer/image-20220327141854350.png)

```java
    public int process(int x,int y,int step){

        if(x < 0 || y < 0 || x > 8 || y > 9) return 0;

        if(step == 0) return (x == 0 && y == 0) ?1:0;

        return process(x-1,y+2,step-1)
                + process(x+1,y+2,step-1)
                + process(x+2,y+1,step-1)
                + process(x+2,y-1,step-1)
                + process(x+1,y-2,step-1)
                + process(x-1,y-2,step-1)
                + process(x-2,y-1,step-1)
                + process(x-2,y+1,step-1);                        

    }
 
    public int getWays(int x,int y,int k){
        return process(x,y,k);
    }
```

## 算法的三大技巧总结

## 左神算法课

### 中级提升

**滑动窗口问题不回退：给定一个有序数组arr，代表数轴上从左到右有n个点arr[0]、arr[1]...arr[n-1]，给定一个正数L，代表一根长度为L的绳子，求绳子最多能覆盖其中的几个点。****

* 思路：**滑动窗口问题不回退**

```html
滑动窗口：绳子两端，绳子右边末尾放在数轴上的一点上，然后只需知道左侧被覆盖了多少个点，以此类推

比如绳子长度为5，如果右侧在坐标2上，则左侧只能落在-3；如果右侧落在4，则左侧落在-1，以此类推；

可以通过二分查找知道大于等于左侧的数的位置，O(log n)
由于要遍历n个点，因此为O(n*log n)

现在设置绳子左边为L，右边为R，距离一定为R-L <= 5，因为绳子就这么长。
```

**打表法：小虎去附近的商店买苹果，奸诈的商贩使用了捆绑交易，只提供6个每袋和8个每袋的包装包装不可拆分。可是小虎现在只想购买恰好n个苹果，小虎想购买尽量少的袋数方便携带。如果不能购买恰好n个苹果，小虎将不会购买。输入一个整数n，表示小虎想购买的个苹果，返回最小使用多少袋子。如果无论如何都不能正好装下，返回-1。**

* 思路：**打表法**

```html
比如现在有100个苹果。
普通办法：先看看最大能够使用多少个8类型袋子，最大可以使用12个即96个苹果，因此不合适；往下使用，11个，10个，慢慢试，，直到结束
数学办法：找最小公倍数，小于24就不需要再去测试了。
打表法：规律-当发现面试题输入输出都是整数，可以找规律优化代码，查看输出，找规律
```

**打表法**：有先手和后手两只动物吃草，每个动物只能池$4^n$的草。谁先吃完，谁赢。

* 思路

```html
只有1~3份草 ==》 先手只能吃1份草，输了
利用博弈论：由于只能吃4的n次方的草，
	因此先手从1份开始吃，不行就吃4份草
	通过递归，在子过程里面后手就是先手，递归下去，一直结束。两者交替下去。

打表法：找规律
```

**预处理：牛牛有一些排成一行的正方形。每个正方形已经被染成红色或者绿色。牛牛现在可以选择任意一个正方形然后用这两种颜色的任意一种进行染色,这个正方形的颜色将会被覆盖。牛牛的目标是在完成染色之后,每个红色R都比每个绿色G，距离最左侧近。牛牛想知道他最少需要涂染几个正方形。**
如样例所示:s = RGRGR
我们涂染之后变成RRRGG满足要求了,涂染的个数为2,没有比这个更好的涂染方案。

* 思路：

```html
RGGRRGG


预处理，利用枚举：枚举左侧部分大小为L，统计左侧有多少个G，染色成R；右侧部分为N-L，统计右侧有多少个R，染成G。然后用数组把这些数据装起来，就可以直接拿答案。（空间换时间的概念）
kog
    第一种方案：把左侧的都染成R，需要染4个
    第二种方案：把左侧只有一个染成R，剩下全是右侧，需要染成2个
    ....
    一次次尝试
```

![image-20220302202148622](../../../../../Pictures/assets/剑指offer/image-20220302202148622.png)

* 思路：

![image-20220302202631962](../../../../../Pictures/assets/剑指offer/image-20220302202631962.png)

```html
第一种，子矩阵是长方形，需要通过上下两个点来固定长方形，由于上下两个点，都有n^2种可能，因此O(n^4)的复杂度
```

![image-20220302202938225](../../../../../Pictures/assets/剑指offer/image-20220302202938225.png)

```html
第二种，子矩阵是正方形，先找到一个A点，但是由于正方形是被固定的，长宽一样的，只需围绕着A找出正方形即可，因此只需要遍历n次找到B的可能，因此为O(n^3)。

虽然已经确定了正方形形状，但是会发现判断边框是否为1，还需要4个for循环进行判断边框。这是O(n^4)

可以通过预处理，提前判断边框：这是O(1)
	通过设置一个与原数组大小一样的数组right，该数组记录包括自己在内右边有多少个1。
	通过设置一个与原数组大小一样的数组down，该数组记录包括自己在内下边有多少个1。
```

![image-20220302230604649](../../../../../Pictures/assets/剑指offer/image-20220302230604649.png)

![image-20220302230726829](../../../../../Pictures/assets/剑指offer/image-20220302230726829.png)

**二进制拼接：**

![image-20220302230911398](../../../../../Pictures/assets/剑指offer/image-20220302230911398.png)

* 思路

```html
用二进制来拼。

比如：现在有1~5，如果当前数字为3，就一直调取f函数，用来获取1，2，4，5；然后如果数字为1,2就返回0，如果数字为4,5就返回1。这样就有了等概率的0和1返回值。

然后就用0,1来拼接1~7，就是二进制。

001 =》 1
010 =》 2
011 =》 3
100 =》 4
101 =》 5
110 =》 6
111 =》 7
```

**动态规划**：

![image-20220303180959375](../../../../../Pictures/assets/剑指offer/image-20220303180959375.png)

* 思路

<img src="../../../../../Pictures/assets/剑指offer/image-20220303181205816.png" alt="image-20220303181205816" style="zoom:50%;" />

```html
1. 左子树没有节点，右子树有n-1个节点
2. 左子树只有1个节点，右子树有n-2个节点
3. 左子树只有2个节点，右子树有n-3个节点
...
i 左子树只有i个节点，右子树有n-i个节点
```

![image-20220303181928486](../../../../../Pictures/assets/剑指offer/image-20220303181928486.png)

* 思路

```html
从左往右遍历字符串，遇到'(',count++，遇到')'count--，要保证一定要大于>=0，因为只有()才完整，说明左括号一定在右括号前面。
比如：())(，这里出现了-1，就说了需要左括号，通过right进行记录，然后将count=0,继续
     1 0 -1(0) 1=> count=1,right=1=>count+right=2
```

![image-20220303182731715](../../../../../Pictures/assets/剑指offer/image-20220303182731715.png)

* 思路

```html
[3,2,5,7,0,0]
使用哈希表：[3,2,5,7,0]，遍历数组+k，是否在哈希表中存在。
```

![image-20220303190530254](../../../../../Pictures/assets/剑指offer/image-20220303190530254.png)

* 思路：先是范围分析，然后发现最小贪心选择需要的数。

```html
比如:
	A集合和B集合平均数都等于100，
		首先可以从A集合中拿出三种级别数：大于100，=100，小于100，但是题目要求两个集合都要提高，因此没有magic操作;
    A集合平均数为100，B集合平均数为80，
		首先可以从A集合中拿出：
			大于100：给B，发现A集合变小了
			等于100：给B，发现B集合变大了，但是A没有变化，因此不可以
			小于100大于80：给B，发现B集合变大了，可以magic
			等于80：B集合没有变化，因此不可以
			小于80：给B，发现B变小了
	因此从大集合中拿数给小集合的数，需要符合"小集合平均数<拿的数<大集合平均数"，还需要在B中不存在。
	由于题目是最多可以多少次magic操作，如果有多个数可以拿，比如A集合有[60,70,80,90]，拿60，对于A是最大幅度的提升，B有最小幅度的提升，这样就可以能够尽量多次magic。                                               
```

![image-20220303194019829](../../../../../Pictures/assets/剑指offer/image-20220303194019829.png)

* 思路

```html
个人思路使用count变量进行记录，在使用maxCount记录count最大值。

算法变体：最长的合法子串长度:()) ()(())() ))(())
思路1：使用start是起始指针，end是移动指针，一个个向下移动，进行判断，通过count判断是否合法，如果遇到-1，就重置为0；然后将start移动当前end+1位置。如果count=0，就进行end-start，进行保存，然后继续向下执行。

思路2：通过dp数组进行记录，dp[i]中如果当前i字符为'('直接赋值为0。
如果前面的括号长度已经记录过了，后面只需加上。
举个例子：(()())()()
		0123456789
dp数组： 00204608
```

* 代码

通过两个指针以及count判断变量进行记录。

```java
public int longestValidParentheses(String s) {

    int count = 0; // 用来判断符号匹配是否合法
    int end = 0;   // 两个指针，用于进行记录当前符合字符串的位置
    int start = 0;
    int len = s.length(); // 字符串长度
    int maxSubLen = 0;

    while(end < len){

        if(s.charAt(end) == '('){
            count++;
        }else{
            count--;
        }

        if(count == -1){
            start = end+1;
            count = 0;//重新记录，前面已经记录过了
        }else if(count == 0){
            maxSubLen = Math.max(maxSubLen, end - start+1);
        }

        end++;
    }

    return maxSubLen;
}
```

通过dp数组进行记录

````java
public int longestValidParentheses2(String s) {

    int[] dp = new int[s.length()];
    int pre = 0;
    int res = 0;

    char[] str = s.toCharArray();

    for (int i = 0; i < str.length; i++) {

        if (str[i] == ')') {
            pre = i -  dp[i - 1] - 1;
            if (pre >= 0 && str[pre] == '(') {
                dp[i] = dp[i - 1] + 2 + (pre > 0 ? dp[pre - 1] : 0);
            }

        }

        res = Math.max(res, dp[i]);
    }
    return res;
}
````

![image-20220303205525984](../../../../../Pictures/assets/剑指offer/image-20220303205525984.png)

* 思路

```html
首先一个栈保存了[6,4,2,8,5]的数据

弹出一个数据5给额外栈，里面没数据，直接就放进去
弹出一个数据8给额外站，里面有一个5数据，因此使用temp变量保存8，然后将数据弹回给栈中
在把8放到额外栈中，然后把栈中数据放回到额外栈中。

以此类推。。。
```

![image-20220303213852084](../../../../../Pictures/assets/剑指offer/image-20220303213852084.png)

* 思路：从左往右的尝试模型

```html
从str[i]到后面所有的，里面有多少种有效数据。
因此可以这样，从1开始，后面f(i+1)自己决定
或者从12开始，后面f(i+2)自己决定。
以此类推。

可以得出，如果f[i] = '0'，就有0种，因此0无法转换。
如果f[i] != '0' 		    f[i+1]
=> f[i][i+1] <= 26 		 f[i+2]
```

![image-20220303220713436](../../../../../Pictures/assets/剑指offer/image-20220303220713436.png)

* 思路

```html
x为头的整棵树上，最大路径和是多少,返回。路径要求，一定从×出发,到叶节点，算做一个路径。
套路：只知道当前节点值，再比较左右子树大小。。。

一般：从头开始往下进行计算权值和，然后进行记录最大值。
```

![image-20220303222326182](../../../../../Pictures/assets/剑指offer/image-20220303222326182.png)

* 思路

![image-20220303222501152](../../../../../Pictures/assets/剑指offer/image-20220303222501152.png)

```html
找一个数字7，从右上角开始找，不合适就往左走，合适就往下走。

变体算法：如下
```

![image-20220303222916201](../../../../../Pictures/assets/剑指offer/image-20220303222916201.png)