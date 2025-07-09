# KamaCamp Day1



## 数组理论基础

**数组是存放在连续内存空间上的相同类型数据的集合**

两点注意：

- **数组下标都是从0开始的**

- **数组内存空间的地址是连续的**

因为数组在内存空间的地址是连续的，所以我们在删除或者增添元素的时候，就难免要移动其他元素的地址。

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250709180136463.png" alt="image-20250709180136463" />

C++中，要注意vector 和 array的区别，vector的底层实现是array，严格来讲vector是容器，不是数组。

**数组的元素是不能删的，只能覆盖。**

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250709180227630.png" alt="image-20250709180227630" style="zoom: 67%;" />

**在C++中二维数组在地址空间上是连续的。**



## 701. Binary Search

这道题目的前提是数组为**有序数组**，同时题目还强调**数组中无重复元素**，因为一旦有重复元素，使用二分查找法返回的元素下标可能不是唯一的，这些都是使用二分法的前提条件。

写二分法经常写乱，主要是因为对区间的定义没有想清楚，**区间的定义就是不变量**。要在二分查找的过程中，保持不变量，就是在while寻找中每一次边界的处理都要坚持根据区间的定义来操作，这就是**循环不变量规则**。

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250709182420134.png" alt="image-20250709182420134" style="zoom: 50%;" />

### 第一种 左闭右闭 [Left, Right]

```cpp
int left = 0;
int right = nums.size() - 1;  // 定义target在左闭右闭的区间里，[left, right]
```

```cpp
while (left <= right)	//要使用 <= ，因为left == right是有意义的，所以使用 <=
```

```cpp
if (nums[middle] > target) {
	right = middle - 1; // target 在左区间，所以[left, middle - 1]
} else if (nums[middle] < target) {
    left = middle + 1; // target 在右区间，所以[middle + 1, right]
```

- 时间复杂度：O(log n)
- 空间复杂度：O(1)

### 第二种 左闭右开 [Left, Right)

```cpp
int left = 0;
int right = nums.size(); // 定义target在左闭右开的区间里，即：[left, right)
```

```cpp
while (left < right) { // 因为left == right的时候，在[left, right)是无效的空间，所以使用 <
```

```cpp
if (nums[middle] > target) {
    right = middle; // target 在左区间，在[left, middle)中
} else if (nums[middle] < target) {
    left = middle + 1; // target 在右区间，在[middle + 1, right)中
```

- 时间复杂度：O(log n)
- 空间复杂度：O(1)

> [!CAUTION]
>
> 1. #### Return
>
>    ```cpp
>    return middle;
>    ```
>
>    此处 left、right、middle都是数组下标
>
> 2. #### `vector<int>& nums` 
>
>    **各部分含义：**
>
>    - `vector<int>` - 这是一个整数类型的动态数组（向量）
>    - `&` - 这是引用符号，表示这是一个**引用**
>    - `nums` - 这是变量名
>
>    **整体意思：** `nums` 是一个指向 `vector<int>` 类型对象的引用。
>
>    > [!NOTE]
>    >
>    > `Rvector<int>& nums` **引用**
>    >
>    > **特点：**
>    >
>    > - 引用是已存在对象的**别名**，不是拷贝
>    > - 必须在声明时初始化
>    > - 一旦绑定到某个对象，就不能再引用其他对象
>    > - 对引用的操作就是对原对象的操作
>    > - 更高效（无拷贝开销）
>    >
>    > ##### `vector<int> nums` 值传递
>    >
>    > **特点：**
>    >
>    > - 会创建原始vector的完整**拷贝**
>    > - 可以在声明后再赋值
>    > - 对`nums`的修改**不会影响**原始数据
>    > - 效率较低（需要拷贝所有元素）
>
> 3. #### Middle溢出问题
>
>    ```cpp
>    int middle = left + ((right - left)/2);
>    ```
>
> <img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250709182856656.png" alt="image-20250709182856656" style="zoom:50%;" />



## 27. Remove Element

数组的元素在内存地址中是连续的，不能单独删除数组中的某个元素，只能覆盖。

### 第一种 暴力解法

两层for循环，一个for循环遍历数组元素 ，第二个for循环更新数组。

```cpp
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int size = nums.size();
        for (int i = 0; i < size; i++) {
            if (nums[i] == val) { // 发现需要移除的元素，就将数组集体向前移动一位
                for (int j = i + 1; j < size; j++) {	//j指针先往右移动一位
                    nums[j - 1] = nums[j];
                }
                i--; // 因为下标i以后的数值都向前移动了一位，所以i也向前移动一位
                size--; // 此时数组的大小-1
            }
        }
        return size;
    }
};
```

- 时间复杂度：O(n^2)
- 空间复杂度：O(1)

### 第二种 双指针法

双指针法（快慢指针法）： **通过一个快指针和慢指针在一个for循环下完成两个for循环的工作。**

定义快慢指针

- 快指针：寻找新数组的元素 ，新数组就是不含有目标元素的数组
- 慢指针：指向更新 新数组下标的位置

快指针可以理解为在旧数组中找非目标元素，然后赋值给慢指针指向的新数组，虽然都指向一个数组。

```cpp
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int slowIndex = 0;
        for (int fastIndex = 0; fastIndex < nums.size(); fastIndex++) {
            if (val != nums[fastIndex]) {
                nums[slowIndex++] = nums[fastIndex];
            }
        }
        return slowIndex;
    }
};
```

- 时间复杂度：O(n)
- 空间复杂度：O(1)

> [!CAUTION]
>
> ### <img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250709191450307.png" alt="image-20250709191450307" style="zoom: 50%;" />



## 977. Squares of a Sorted Array

### 第一种 暴力解法

每个数平方之后，排个序。

```cpp
sort(A.begin(), A.end()); // 快速排序
```

时间复杂度是 O(n + nlogn)， 可以说是O(nlogn)的时间复杂度

### 第二种 双指针法

数组其实是有序的， 只不过负数平方之后可能成为最大数了。那么数组平方的最大值就在数组的两端，不是最左边就是最右边，不可能是中间。

双指针法：**i**指向起始位置，**j**指向终止位置。

定义一个**新数组result**，和nums数组一样的大小，让k指向result数组终止位置。

如果`A[i] * A[i] <  A[j] * A[j]` 那么`result[k--] = A[j] * A[j];` 

如果`A[i] * A[i] >= A[j] * A[j]` 那么`result[k--] = A[i] * A[i];` 

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250709192140223.png" alt="image-20250709192140223" style="zoom:50%;" />

```cpp
class Solution {
public:
    vector<int> sortedSquares(vector<int>& A) {
        int k = A.size() - 1; //k指向result数组终止位置
        vector<int> result(A.size(), 0);
        for (int i = 0, j = A.size() - 1; i <= j;) { // 注意这里要i <= j，因为最后要处理两个元素
            if (A[i] * A[i] < A[j] * A[j])  {	//为避免正负符号的影响，A[i] * A[i]代替A[i] 
                result[k--] = A[j] * A[j];
                j--;	//右指针左移
            }
            else {
                result[k--] = A[i] * A[i];
                i++;	//左指针右移
            }
        }
        return result;
    }
};
```

时间复杂度为O(n)