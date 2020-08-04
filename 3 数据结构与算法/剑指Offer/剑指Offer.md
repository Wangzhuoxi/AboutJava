### 1 找出数组中重复的数字

在一个长度为n的数组里的所有数字都在0到n-1的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。例如，如果输入长度为7的数组{2, 3, 1, 0, 2, 5, 3}，那么对应的输出是重复的数字2或者3。
```java
public boolean duplicate(int nums[], int len, int[] duplication) {
    if (nums == null || nums.length < 2 || len < 2 || duplication == null || duplication.length == 0)
        return false;
    boolean hasDuplicate = false;
    int i = 0; // 遍历索引
    while (i < len) {
        int m = nums[i];
        if (m == i) { // 下标和值相等
            ++i;
        } else if (nums[m] != m) { // 把m交换成相等
            nums[i] = nums[m];
            nums[m] = m;
        } else { // 找到了重复元素(i和m的值都是m)
            hasDuplicate = true;
            duplication[0] = m;// duplication数组相当于全局变量，保存重复的值
            break;
        }
    }
    return hasDuplicate;
}
```

### 2 不修改数组找出重复的数字

题目：在一个长度为n+1的数组里的所有数字都在1到n的范围内，所以数组中至少有一个数字是重复的。请找出数组中任意一个重复的数字，但不能修改输入的数组。例如，如果输入长度为8的数组{2, 3, 5, 4, 3, 2, 6,7}，那么对应的输出是重复的数字2或者3。

```java
public int getDuplicate(int[] arr) {
    int low = 1;
    int high = arr.length - 1; // high即为题目的n
    int mid, count;
    // 利用二分查找
    while (low <= high) {
        mid = ((high - low) >> 2) + low;
        count = countRange(arr, low, mid);
        // 跳出条件
        if (low == high) {
            if (count > 1)
                return low;
            else // 表示没有重复值
                break;
        }
        if (count > mid - low + 1) { // 在左半区
            high = mid;
        } else { // 在右半区
            low = mid + 1;
        }
    }
    return -1;
}

// 返回范围内数字的出现次数
public int countRange(int[] arr, int low, int high) {
    if (arr == null)
        return 0;
    int count = 0;
    for (int a : arr) {
        if (a >= low && a <= high)
            count++;
    }
    return count;
}
```

### 3 替换空格

请实现一个函数，将一个字符串中的每个空格替换成“%20”。例如，当字符串为We Are Happy.则经过替换之后的字符串为We%20Are%20Happy。

```java
public String replaceSpace(StringBuffer str) {
    StringBuilder builder = new StringBuilder();
    if (str == null || str.length() == 0) {
        return builder.toString();
    }
    for (int i = 0; i < str.length(); i++) {
        if (str.charAt(i) == ' ') {// 遇到空格直接添加%20
            builder.append("%20");
        } else { // 非空格原样添加
            builder.append(str.charAt(i));
        }
    }
    return builder.toString();
}
```

### 4 从尾到头打印链表

输入一个链表，按链表值从尾到头的顺序返回一个ArrayList。

```java
public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
    ArrayList<Integer> arrayList = new ArrayList<>();
    if (listNode == null) {
        return arrayList;
    }
    ListNode cur = listNode;
    while (cur != null) {
        arrayList.add(cur.val);
        cur = cur.next;
    }
    Collections.reverse(arrayList);	//利用Collections的翻转功能
    return arrayList;
}
```

### 5 重建二叉树

输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。

```java
public TreeNode reConstructBinaryTree(int[] pre, int[] in) {
    if (pre == null || in == null || pre.length == 0 || in.length == 0) {
        return null;
    }
    TreeNode root = new TreeNode(pre[0]);// 先序遍历第一个点为根节点
    for (int i = 0; i < in.length; i++) {
        if (pre[0] == in[i]) {// 中序中找到根节点
            // 递归左子树（i个节点）
            root.left = reConstructBinaryTree(Arrays.copyOfRange(pre, 1, i + 1), Arrays.copyOfRange(in, 0, i));// copyOfRange方法不包括to下标
            // 递归右子树（n-i个节点）
            root.right = reConstructBinaryTree(Arrays.copyOfRange(pre, i + 1, pre.length),Arrays.copyOfRange(in, i + 1, in.length));
        }
    }
    return root;
}
```

### 6 二叉树的下一个结点

给定一棵二叉树和其中的一个节点， 如何找出中序遍历的下一个节点？树中的节点除了有两个分别指向左、右节点的指针，还有一个指向父节点的指针

```java
public TreeLinkNode GetNext(TreeLinkNode pNode) {
    if (pNode == null) {
        return null;
    }
    // 1.右子树不为空则下一个是右子树最左节点
    if (pNode.right != null) {
        return findLeft(pNode.right);
    }
    // 2.右子树为空且是父节点的左孩子
    if (pNode.parent != null && pNode == pNode.parent.left) {
        return pNode.parent;
    }
    // 3.向上遍历查找左孩子结构
    TreeLinkNode cur = pNode;
    while (cur.parent != null && cur != cur.parent.left) {
        cur = cur.parent;
    }
    // 遍历到根节点还是未找到
    if (cur.parent == null) {
        return null;
    }
    return cur.parent;
}
// 找到某子树的最左节点
private TreeLinkNode findLeft(TreeLinkNode pNode) {
    TreeLinkNode cur = pNode;
    while (cur.left != null) {
        cur = cur.left;
    }
    return cur;
}
```

### 7 用两个栈实现队列

用两个栈来实现一个队列，完成队列的Push和Pop操作。 队列中的元素为int类型。

```java
Stack<Integer> stack1 = new Stack<Integer>();// 用来push
Stack<Integer> stack2 = new Stack<Integer>();// 用来pop

public void push(int node) {
    stack1.push(node);
}

public int pop() {
    // 条件：1.栈2为空 2.一次转移完
    if (stack2.empty()) {
        while (!stack1.isEmpty()) {
            stack2.push(stack1.pop());
        }
    }
    if (stack2.empty())
        throw new RuntimeException("Queue is empty!");
    return stack2.pop();
}
```

### 8 斐波那契数列

写一个函数，输入n，求斐波那契数列的第n项（从第0项开始，第0项为0）。

```java
public int Fibonacci(int n) {
    if (n == 0) {
        return 0;
    }
    if (n == 1) {
        return 1;
    }
    return Fibonacci(n - 1) + Fibonacci(n - 2);
}
```

### 9 跳台阶

一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法（先后次序不同算不同的结果）。

```java
public int JumpFloor(int target) {
    if (target == 1) {
        return 1;
    }
    if (target == 2) {
        return 2;
    }
    return JumpFloor(target - 1) + JumpFloor(target - 2);// 上一步可能是跳1或者跳2，递归思想
}
```

### 10 变态跳台阶

一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

```java
// 设跳上n级台阶有f(n)种跳法
// f(n)种方法包括：(按照最后跳1步分类)
// 1. 跳上1级台阶后直接跳上n级台阶
// 2. 跳上2级台阶后直接跳上n级台阶
// 3. 跳上3级台阶后直接跳上n级台阶
// 4. ……
// n-1. 跳上n-1级台阶后直接跳上n级台阶
// n. 直接跳上n级台阶
public int JumpFloor(int target) {
    if (target <= 0) {
        return 0;
    }
    int result = 1;
    // f(n) = f(n-1)+f(n-2)+...+f(2)+f(1) = 2f(n-1) =...= 2^(n-1) * f(1)
    for (int i = 0; i < target - 1; i++) {
        result *= 2;
    }
    return result;
}
```

### 11 旋转数组的最小数字

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。例如数组 {3, 4, 5, 1, 2}为{1, 2, 3, 4,5}的一个旋转，该数组的最小值为1。
```java
public int minNumberInRotateArray(int[] array) {
    if (array.length == 0) {
        return 0;
    }
    if (array.length == 1) {
        return array[0];
    }
    int low = 0;
    int high = array.length - 1;
    // 二分查找思想
    while (low < high) {
        int mid = low + (high - low) / 2;
        if (array[mid] > array[high]) {
            low = mid + 1;// 最小值在右半区
        } else if (array[mid] == array[high]) {
            high = high - 1;// 无法判断肯定不是最后一个试着缩小范围
        } else {
            high = mid;// 最小值在左半区
        }
    }
    return array[low];
}
```

### 12 矩阵中的路径

请设计一个函数，用来判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中任意一格开始，每一步可以在矩阵中向左、右、上、下移动一格。如果一条路径经过了矩阵的某一格，那么该路径不能再次进入该格子。

```java
// 回溯法典型题
public boolean hasPath(char[] matrix, int rows, int cols, char[] str) {
    if (matrix == null || str == null || matrix.length == 0 || str.length == 0) {
        return false;
    }
    int len = matrix.length;// 此处用一维数组表示矩阵
    boolean[] flags = new boolean[len];// 标记元素是否被访问过

    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
            if (matrix[r * cols + c] == str[0]) {// 找到字符串的首字符（很可能不止一个）
                if (hasPath(matrix, rows, cols, r, c, str, 0, flags)) {
                    return true;
                }
            }
        }
    }
    return false;
}

private boolean hasPath(char[] matrix, int rows, int cols, int r, int c, char[] str, int index, boolean[] flags) {
    if (index == str.length) {
        return true; // 匹配到字符串的末尾成功
    }
    if (r >= rows || r < 0 || c >= cols || c < 0) {
        return false; // 遍历出了矩阵的边界false
    }
    if (flags[r * cols + c] == true || matrix[r * cols + c] != str[index]) {
        return false; // 当前矩阵元素已经被访问过或者不等于字符串对应值false
    }
    flags[r * cols + c] = true;// 设置当前元素被访问过
    // 上下左右四个方向遍历
    boolean hp = hasPath(matrix, rows, cols, r + 1, c, str, index + 1, flags)
        || hasPath(matrix, rows, cols, r - 1, c, str, index + 1, flags)
        || hasPath(matrix, rows, cols, r, c + 1, str, index + 1, flags)
        || hasPath(matrix, rows, cols, r, c - 1, str, index + 1, flags);
    if (hp == true) {
        return true;
    }
    // 未返回说明当前行不通，将访问位归位
    flags[r * cols + c] = false;
    return false;
}
```

### 13 机器人的运动范围 

地上有一个m行n列的方格。一个机器人从坐标(0, 0)的格子开始移动，它每一次可以向左、右、上、下移动一格，但不能进入行坐标和列坐标的数位之和大于k的格子。请问该机器人能够达到多少个格子？

```java
// 回溯法问题
public int movingCount(int threshold, int rows, int cols) {
    if (threshold < 0 || rows <= 0 || cols <= 0) {
        return 0;
    }
    boolean[][] visited = new boolean[rows][cols]; // 标志是否被访问过
    return movingCount(threshold, rows, cols, 0, 0, visited);
}

private int movingCount(int threshold, int rows, int cols, int r, int c, boolean[][] visited) {
    // 遍历到了边界
    if (r >= rows || r < 0 || c >= cols || c < 0) {
        return 0;
    }
    // 已经被访问过或者不满足条件
    if (visited[r][c] || isBiggerThanThreshold(r, c, threshold)) {
        return 0;
    }
    visited[r][c] = true;// 当前元素满足条件标记未被访问过
    // 向上下左右四个方向遍历
    return 1 + movingCount(threshold, rows, cols, r + 1, c, visited)
        + movingCount(threshold, rows, cols, r - 1, c, visited)
        + movingCount(threshold, rows, cols, r, c + 1, visited)
        + movingCount(threshold, rows, cols, r, c - 1, visited);
}

// 判断坐标(r,c)是否满足各数位和不大于阈值
private boolean isBiggerThanThreshold(int r, int c, int threshold) {
    int sum = 0;
    while (r > 0) {
        sum += r % 10;// 每次取最低位
        r /= 10;
    }
    while (c > 0) {
        sum += c % 10;// 每次取最低位
        c /= 10;
    }
    return sum > threshold;
}
```

### 14 剪绳子

给你一根长度为n绳子，请把绳子剪成m段（m、n都是整数，n>1并且m>1）。每段的绳子的长度记为k[0]、k[1]、……、k[m]。k[0]\*k[1]\*…\*k[m]可能的最大乘积是多少？例如当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到最大的乘积18。
```java
// 解法一：动态规划
// 时间O(n^2)，空间O(n)
// f(n) = max(f(i), f(n-i)) 
public static int cuttingRopeInDynamic(int length) {
    if (length < 2) {
        return 0;
    }
    if (length == 2) {
        return 1;
    }
    if (length == 3) {
        return 2;
    }
    int[] max = new int[length + 1];	// 记录每个长度的最大值
    // 初始值的设定（注意内部不用再切割，最大值就是长度本身）
    max[0] = 0;
    max[1] = 1;
    max[2] = 2;
    max[3] = 3;

    for (int i = 4; i <= length; i++) {
        int maxValue = 0;
        // 枚举分段情况(注意只用判断一半)
        for (int j = 1; j <= i / 2; j++) {
            int temp = max[j] * max[i - j];
            maxValue = temp > maxValue ? temp : maxValue;
        }
        max[i] = maxValue;
    }
    return max[length];
}
```

### 15 二进制中1的个数

请实现一个函数，输入一个整数，输出该数二进制表示中1的个数。

```java
public int NumberOf1(int n) {
    int count = 0;
    for (int i = 0; i < 32; i++) {
        count += n & 1;// 和1与判断最低位是否位1
        n >>= 1;// 右移一位
    }
    return count;
}
```

### 16 数值的整数次方 

实现函数double Power(double base, int exponent)，求base的exponent次方。

```java
public double Power(double base, int exponent) {
    if (base == 0) {
        return 0; // 底数为0返回0
    }
    if (exponent == 0) {
        return 1; // 指数为0返回1
    }
    // 获取指数的符号
    boolean positive = exponent > 0;
    exponent = positive ? exponent : -exponent;

    // exp可能为负数，负数时先将exp转换成绝对值，求出答案res，最终答案为1/res
    if (positive) {
        return PowerCore(base, exponent);
    } else {
        return 1 / PowerCore(base, exponent);
    }
}

// 递归思想：base ^ exp
// 如果exp为偶数，则拆分成(base ^ (exp/2)) * (base ^ (exp/2))
// 如果exp为奇数，则拆分成(base ^ (exp/2)) * (base ^ (exp/2)) * base
private double PowerCore(double base, int exponent) {
    if (exponent == 0) {
        return 1;
    }
    if (exponent == 1) {
        return base;
    }
    double tmp = Power(base, exponent >> 1);

    if ((exponent & 1) == 0) { // exp为偶数
        return tmp * tmp;
    } else {// exp为奇数
        return tmp * tmp * base;
    }
}
```

### 17 打印1到最大的n位数 

输入数字n，按顺序打印出从1最大的n位十进制数。 比如输入3，则打印出1、2、3一直到最大的3位数即999。

```java
// 解法二：递增+进位判断
public void print1ToMaxOfNDigits_2( .int n) {
    if (n <= 0) {
        return;
    }
    char[] digits = new char[n];
    for (int i = 0; i < digits.length; i++) {
        digits[i] = '0';
    }
    while (increment(digits)) {
        printDigit(digits);
    }
}

private boolean increment(char[] digits) {
    digits[0] += 1; // 每次加1
    // for循环处理一次的进位
    for (int i = 0; i < digits.length; i++) {
        int num = digits[i] - '0';
        if (num == 10) {
            // 产生了进位但是已经超出了最大范围返回false.
            if (i == digits.length - 1) {
                return false;
            }
            digits[i + 1] += 1;// 产生进位
            digits[i] = '0';
        } else {
            break;
        }
    }
    return true;
}

// 打印digits，注意开头不要打印0
private void printDigit(char[] digits) {
    boolean zeroFlag = true;
    // 主要digtis数组要倒序打印
    for (int i = digits.length - 1; i >= 0; --i) {
        if (digits[i] == '0' && zeroFlag) {
            continue; // 忽略掉开头的0
        }
        System.out.print(digits[i]);
        zeroFlag = false;
    }
    // 打印完每个数后打印一个空格
    if (zeroFlag == false) {
        System.out.print(' ');
    }
}
```

### 18 在O(1)时间删除链表结点

给定单向链表的头指针和一个结点指针，定义一个函数在O(1)时间删除该结点。

```java
public void deleteNode(ListNode head, ListNode toBeDeleted) {
    if (head == null || toBeDeleted == null) {
        return;
    }
    // 链表中只有一个节点
    if (head == toBeDeleted && head.next == null) {
        head = null;
    } else {
        // 待删除的节点是尾节点
        if (toBeDeleted.next == null) {
            ListNode temp = head;
            while (temp.next != toBeDeleted) { // 找到删除节点的前一个
                temp = temp.next;
            }
            temp.next = null;
        } else { // 待删除的节点不是尾节点
            toBeDeleted.value = toBeDeleted.next.value; // 拷贝下一个节点的value
            toBeDeleted.next = toBeDeleted.next.next;// 拷贝下一个节点的next
        }
    }
}
```

### 19 删除链表中重复的结点

在一个排序的链表中，存在重复的结点，请删除该链表中重复的结点，重复的结点不保留，返回链表头指针。例如，链表1->2->3->3->4->4->5 处理后为 1->2->5；

```java
public ListNode deleteDuplication(ListNode pHead) {
    if (pHead == null || pHead.next == null) {
        return pHead;
    }
    ListNode preNode = new ListNode(0);// 前一个节点
    ListNode tmp = preNode; // 保存一个新节点防止头节点被删除
    preNode.next = pHead;
    ListNode nowNode = pHead;// 当前节点
    // 遍历链表
    while (nowNode != null) {
        // 判断是否存在重复节点，是否应该删除
        if (nowNode.next != null && nowNode.val == nowNode.next.val) {
            while (nowNode.next != null && nowNode.val == nowNode.next.val) {
                nowNode = nowNode.next; // 查找重复值
            }
            preNode.next = nowNode.next; // 删除相应的重复节点
        } else {// 无重复节点直接右移
            preNode = nowNode;
        }
        nowNode = nowNode.next;
    }
    return tmp.next;	//返回头节点
}
```

### 20 正则表达式匹配

请实现一个函数用来匹配包含'.'和'\*'的正则表达式。模式中的字符'.'表示任意一个字符，而'\*'表示它前面的字符可以出现任意次（含0次）。在本题中，匹配是指字符串的所有字符匹配整个模式。例如，字符串"aaa"与模式"a.a" 和"ab\*ac\*a"匹配，但与"aa.a"及"ab*a"均不匹配。

```java
public boolean match(char[] str, char[] pattern) {
    if (str == null || pattern == null) {
        return false;
    }
    return matchCore(str, 0, pattern, 0);
}

private boolean matchCore(char[] str, int strIndex, char[] pattern, int pIndex) {
    // 字符串和模式都已匹配完，返回true
    if (strIndex >= str.length && pIndex >= pattern.length) {
        return true;
    }
    // 字符串还没结束，模式已经结束，返回false
    if (strIndex < str.length && pIndex >= pattern.length) {
        return false;
    }
    // 字符串已结束，模式还没结束
    if (strIndex >= str.length && pIndex < pattern.length) {
        // 处理模式的剩余字符(*及其前面的字符出现0次)
        if (pIndex + 1 < pattern.length && pattern[pIndex + 1] == '*') {
            return matchCore(str, strIndex, pattern, pIndex + 2);
        } else {
            return false;
        }
    }
    // 字符串和模式都没处理完的情况
    // 如果模式的下一个字符是*
    if (pIndex + 1 < pattern.length && pattern[pIndex + 1] == '*') {
        // 字符串和模式的当前字符能够匹配
        if (str[strIndex] == pattern[pIndex] || pattern[pIndex] == '.') {
            return matchCore(str, strIndex, pattern, pIndex + 2) // 0次
                || matchCore(str, strIndex + 1, pattern, pIndex + 2) // 1次
                || matchCore(str, strIndex + 1, pattern, pIndex);// 多次（不消耗pattern）
        } else {
            // 不匹配则跳过，即出现0次
            return matchCore(str, strIndex, pattern, pIndex + 2);
        }
    } else {// 下一个字符不是*
        // 下一个字符是.
        if (str[strIndex] == pattern[pIndex] || pattern[pIndex] == '.') {
            return matchCore(str, strIndex + 1, pattern, pIndex + 1);
        } else {
            return false;
        }
    }
}
```

### 21 表示数值的字符串

请实现一个函数用来判断字符串是否表示数值（包括整数和小数）。例如，字符串  “+100”、 “5e2”、 “-123”、 “3.1416” 及“-1E-16”都表示数值，但“12e”、“1a3.14”、“1.2.3”、“+-5”及“12e+5.4”都不是。

```java
private int index;// 全局变量表示比较的字符下标

// 数字有如下两种情况：
// ①A[.B][e|EC]
// ②[+|-].B[e|EC]
// 其中A、C表示数字（带符号或不带符号），B表示不带符号的数字，[]包含的整个部分可有可无。
public boolean isNumeric(char[] str) {
    if (str == null || str.length == 0) {
        return false;
    }
    index = 0;
    // 判断整数部分
    boolean flag = scanInteger(str);
    // 判断小数部分
    if (index < str.length && str[index] == '.') {
        index++;
        flag = scanUnsignedInteger(str) || flag; // 有可能无整数部分，所以是或操作
    }
    // 判断指数部分
    if (index < str.length && (str[index] == 'e' || str[index] == 'E')) {
        index++;
        flag = scanInteger(str) && flag; // 有指数符号e的前提下这部分是是必须的所以是与操作
    }
    return index >= str.length && flag; // 遍历完且flag为true
}

// 扫描整数部分
private boolean scanInteger(char[] str) {
    // 跳过符号位
    if (index < str.length && (str[index] == '+' || str[index] == '-')) {
        index++;
    }
    // 判断剩下的无符号部分
    return scanUnsignedInteger(str);
}

// 扫描无符号整数部分
private boolean scanUnsignedInteger(char[] str) {
    int temp = index;
    while (index < str.length && str[index] >= '0' && str[index] <= '9') {
        index++;
    }
    return index > temp;
}
```

### 22 调整数组顺序使奇数位于偶数前面

输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有奇数位于数组的前半部分，所有偶数位于数组的后半部分。并保证奇数和奇数，偶数和偶数之间的相对位置不变。

```java
// 解法二；利用队列
// 时间：o(N)，空间：o(N)
public void reOrderArray_2(int[] array) {
    if (array == null || array.length == 0) {
        return;
    }
    Queue<Integer> queue = new LinkedList<>();
    int index = 0;
    for (int val : array) {
        if ((val & 1) == 1) {
            array[index++] = val;// 奇数跳过
        } else {
            queue.add(val);// 偶数添加到队列
        }
    }
    while (index != array.length) {
        array[index++] = queue.poll(); // 偶数添加到后面
    }
}
```

### 23 链表中倒数第k个结点

输入一个链表，输出该链表中倒数第k个结点。

```java
// 先找到第k个，然后让第1和第k同步移动，第k到最后则第1到倒数第k
public ListNode FindKthToTail(ListNode head, int k) {
    if (head == null || k <= 0) {
        return null;
    }
    ListNode node1 = head;
    ListNode node2 = head;
    // 找到第K个元素，注意下标从1开始
    for (int i = 0; i < k - 1; i++) {
        node1 = node1.next;
        if (node1 == null) {
            return null;
        }
    }
    // 两个指针同步移动
    while (node1.next != null) {
        node1 = node1.next;
        node2 = node2.next;
    }
    return node2;
}
```

### 24 链表中环的入口结点

一个链表中包含环，如何找出环的入口结点？

```java
public ListNode EntryNodeOfLoop(ListNode pHead) {
    if (pHead == null || pHead.next == null || pHead.next.next == null) {
        return null;
    }
    ListNode fast = pHead.next.next; // 快走2
    ListNode slow = pHead.next;// 慢走1
    // 有环一定在环上相遇
    while (fast != slow) {
        if (fast.next == null || fast.next.next == null) {
            return null; // 无环
        }
        fast = fast.next.next;
        slow = slow.next;
    }
    fast = pHead; // 快指针重新指回头部
    while (fast != slow) {// 快慢会在入环节点相遇
        fast = fast.next;// 快走1
        slow = slow.next; // 慢走1
    }
    return slow;
}
```

### 25 反转链表

定义一个函数，输入一个链表的头结点，反转该链表并输出反转后链表的头结点。

```java
public ListNode ReverseList(ListNode head) {
    if (head == null || head.next == null) {
        return head;// 长度为0或1
    }
    ListNode pre = null; // 前驱
    ListNode next = null; // 后继
    while (head != null) {
        next = head.next;
        head.next = pre;
        pre = head;
        head = next;
    }
    return pre;
}
```

### 26 合并两个排序的链表

输入两个递增排序的链表，合并这两个链表并使新链表中的结点仍然是按照递增排序的。

```java
public ListNode Merge(ListNode list1, ListNode list2) {
    if (list1 == null || list2 == null) {
        return list1 != null ? list1 : list2;
    }
    ListNode head = list1.val < list2.val ? list1 : list2; // 较小的作为头部
    ListNode cur1 = head == list1 ? list1 : list2; // cur1指向较小链表
    ListNode cur2 = head == list1 ? list2 : list1; // cur2指向较大链表
    ListNode pre = null;
    ListNode next = null;
    while (cur1 != null && cur2 != null) {// 都未到尾部
        if (cur1.val <= cur2.val) {
            pre = cur1;
            cur1 = cur1.next;
        } else {
            next = cur2.next;
            pre.next = cur2;
            cur2.next = cur1;
            pre = cur2;
            cur2 = next;
        }
    }
    pre.next = cur1 == null ? cur2 : cur1; // 剩余的部分直接连接
    return head;
}
```

### 27 树的子结构

输入两棵二叉树A和B，判断B是不是A的子结构。我们约定空树不是任意一个树的子结构

```java
public boolean HasSubtree(TreeNode root1, TreeNode root2) {
    if (root1 == null || root2 == null) {
        return false;
    }
    // 对应三种比较
    return check(root1, root2) || HasSubtree(root1.left, root2) || HasSubtree(root1.right, root2);
}
// 判断以节点h和节点为t2的子数是否具有相同的结构
private boolean check(TreeNode h, TreeNode t2) {
    if (t2 == null) {
        return true; // t2树已经比较结束
    }
    if (h == null || h.val != t2.val) {
        return false;
    }
    // h和t2是相等的，继续比较子树
    return check(h.left, t2.left) && check(h.right, t2.right);
}
```

### 28 二叉树的镜像

请完成一个函数，输入一个二叉树，该函数输出它的镜像。

```java
// 解法一：递归实现
public void Mirror(TreeNode root) {
    if (root == null) {
        return;
    }
    TreeNode tmp = root.left;
    root.left = root.right;
    root.right = tmp;
    // 递归
    Mirror(root.left);
    Mirror(root.right);
}
```

### 29 对称的二叉树

请实现一个函数，用来判断一棵二叉树是不是对称的。 如果一棵二叉树和它的镜像一样，那么它是对称的。

```java
public boolean isSymmetrical(TreeNode pRoot) {
    if (pRoot == null) {
        return true;
    }
    return isSymmetrical(pRoot.left, pRoot.right);
}
private boolean isSymmetrical(TreeNode root1, TreeNode root2) {
    if (root1 == null && root2 == null) {
        return true; // 都是空为true
    }
    if (root1 == null || root2 == null) {
        return false; // 只有一个是空为false
    }
    if (root1.val != root2.val) {
        return false; // 值不相等
    }
    // 递归考察对称性
    return isSymmetrical(root1.left, root2.right) && isSymmetrical(root1.right, root2.left);
}
```

### 30 顺时针打印矩阵

输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字。

```java
public ArrayList<Integer> printMatrix(int[][] matrix) {
    ArrayList<Integer> res = new ArrayList<>();
    if (matrix == null || matrix.length == 0) {
        return res;
    }
    int tR = 0;
    int tC = 0;// 左上角
    int dR = matrix.length - 1;
    int dC = matrix[0].length - 1;// 右下角
    while (tR <= dR && tC <= dC) {
        printEdge(matrix, tR++, tC++, dR--, dC--, res);
    }
    return res;
}
// 打印子矩阵
private void printEdge(int[][] matrix, int tR, int tC, int dR, int dC, ArrayList<Integer> res) {
    if (tR == dR) { // 子矩阵只有一行
        for (int i = tC; i <= dC; i++) {
            res.add(matrix[tR][i]);
        }
    } else if (tC == dC) { // 子矩阵只有一列
        for (int i = tR; i <= dR; i++) {
            res.add(matrix[i][tC]);
        }
    } else { // 一般情况
        int curR = tR;
        int curC = tC;
        while (curC != dC) {
            res.add(matrix[tR][curC]);
            curC++;
        }
        while (curR != dR) {
            res.add(matrix[curR][dC]);
            curR++;
        }
        while (curC != tC) {
            res.add(matrix[dR][curC]);
            curC--;
        }
        while (curR != tR) {
            res.add(matrix[curR][tC]);
            curR--;
        }
    }
}
```

### 31 包含min函数的栈

定义栈的数据结构，请在该类型中实现一个能够得到栈的最小元素的min函数。在该栈中，调用min、push及pop的时间复杂度都是O(1)。

```java
Stack<Integer> stackData = new Stack<>();
Stack<Integer> stackMin = new Stack<>();	// 栈顶保留当前最小值
public void push(int node) {
    if (this.stackMin.isEmpty()) {
        this.stackMin.push(node);
    } else if (node <= this.min()) {
        this.stackMin.push(node);
    }
    this.stackData.push(node);
}
public void pop() {
    if (this.stackData.isEmpty()) {
        throw new RuntimeException("Your stack is empty.");
    }
    int value = this.stackData.pop();
    if (value == this.min()) {
        this.stackMin.pop();
    }
}
public int top() {
    return this.stackData.peek();
}
public int min() {
    if (this.stackMin.isEmpty()) {
        throw new RuntimeException("Your stack is empty.");
    }
    return this.stackMin.peek();
}
```

### 32 栈的压入、弹出序列

输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1、2、3、4、5是某栈的压栈序列，序列4、5、3、2、1是该压栈序列对应的一个弹出序列，但4、3、5、1、2就不可能是该压栈序列的弹出序列。

```java
public boolean IsPopOrder(int[] pushA, int[] popA) {
    if ((pushA == null && popA == null) || (pushA.length == 0 && popA.length == 0)) {
        return true;
    }
    if (pushA == null || popA == null || pushA.length == 0 || popA.length == 0) {
        return false;
    }
    Stack<Integer> stack = new Stack<>(); // 辅助栈
    int popIndex = 0;
    for (int i = 0; i < pushA.length; i++) {
        stack.push(pushA[i]);// 压栈
        while (!stack.isEmpty() && stack.peek() == popA[popIndex]) {	// 出栈
            stack.pop();
            popIndex++;
        }
    }
    return stack.isEmpty() ? true : false;
}
```

### 33 从上往下打印二叉树 

不分行：从上往下打印出二叉树的每个结点，同一层的结点按照从左到右的顺序打印。

```java
// 经典层序遍历二叉树
public ArrayList<Integer> PrintFromTopToBottom(TreeNode root) {
    ArrayList<Integer> list = new ArrayList<>();
    if (root == null) {
        return list;
    }
    Queue<TreeNode> queue = new LinkedList<>();
    queue.add(root);
    while (!queue.isEmpty()) {
        TreeNode node = queue.poll();
        list.add(node.val);
        if (node.left != null) {
            queue.add(node.left);
        }
        if (node.right != null) {
            queue.add(node.right);
        }
    }
    return list;
}
```

### 34 分行从上往下打印二叉树

从上到下按层打印二叉树，同一层的结点按从左到右的顺序打印，每一层打印到一行。

```java
// 解法一：保存每行的节点数
ArrayList<ArrayList<Integer>> Print(TreeNode pRoot) {
    ArrayList<ArrayList<Integer>> res = new ArrayList<>();
    if (pRoot == null) {
        return res;
    }
    Queue<TreeNode> queue = new LinkedList<>();
    queue.add(pRoot);

    ArrayList<Integer> linRes = new ArrayList<>();
    int numOfThisLine = 1; // 本行节点数
    int numOfNextLine = 0; // 下一行节点数

    while (!queue.isEmpty()) {
        TreeNode node = queue.poll();
        linRes.add(node.val);
        numOfThisLine--;
        if (node.left != null) {
            queue.add(node.left);
            numOfNextLine++;
        }
        if (node.right != null) {
            queue.add(node.right);
            numOfNextLine++;
        }
        if (numOfThisLine == 0) { // 遍历到行尾
            res.add(linRes);
            linRes = new ArrayList<>();
            numOfThisLine = numOfNextLine;
            numOfNextLine = 0;
        }
    }
    return res;
}
```

### 35 之字形打印二叉树 

请实现一个函数按照之字形顺序打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右到左的顺序打印，第三行再按照从左到右的顺序打印， 其他行以此类推。

```java
public ArrayList<ArrayList<Integer>> Print(TreeNode pRoot) {
    ArrayList<ArrayList<Integer>> res = new ArrayList<>();
    if (pRoot == null) {
        return res;
    }
    Deque<TreeNode> deque = new LinkedList<>();
    boolean lr = true;	// 方向标志
    TreeNode last = pRoot;
    TreeNode nLast = null;
    deque.offerFirst(pRoot);
    ArrayList<Integer> curLine = new ArrayList<>();
    while (!deque.isEmpty()) {
        TreeNode node = null;
        if (lr) { // 左到右打印
            node = deque.pollFirst(); // 从头部弹出
            curLine.add(node.val);
            if (node.left != null) {
                nLast = nLast == null ? node.left : nLast; // 确定下一行的最后一个节点
                deque.offerLast(node.left); // 从尾部插入
            }
            if (node.right != null) {
                nLast = nLast == null ? node.right : nLast; // 确定下一行的最后一个节点
                deque.offerLast(node.right); // 从尾部插入
            }
        } else { // 右到左打印
            node = deque.pollLast(); // 从尾部弹出
            curLine.add(node.val);
            if (node.right != null) {
                nLast = nLast == null ? node.right : nLast; // 确定下一行的最后一个节点
                deque.offerFirst(node.right); // 从头部插入
            }
            if (node.left != null) {
                nLast = nLast == null ? node.left : nLast; // 确定下一行的最后一个节点
                deque.offerFirst(node.left); // 从头部插入
            }
        }
        if (node == last) {	//到了行尾
            lr = !lr;
            last = nLast;
            nLast = null;
            res.add(curLine);
            curLine = new ArrayList<>();
        }
    }
    return res;
}
```

### 36 二叉搜索树的后序遍历序列

输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则返回true，否则返回false。假设输入的数组的任意两个数字都互不相同。

```java
private boolean VerifySquenceOfBST(int[] sequence, int start, int end) {
    if (start >= end) {
        return true;
    }
    int root = sequence[end]; // 子树根节点
    // 找到右子树的第一个节点
    int index = start;
    for (; index <= end - 1; index++) {
        if (sequence[index] > root) { // 第一个出现的大于根节点的值
            break;
        }
    }
    int preEnd = index - 1;
    int nextStart = index;
    // 看右子树的值是否全部大于根节点
    while (index <= end - 1) {
        if (sequence[index] < root) {
            return false;
        }
        index++;
    }
    return VerifySquenceOfBST(sequence, start, preEnd) && VerifySquenceOfBST(sequence, nextStart, end - 1);
}
```

### 37 二叉树中和为某一值的路径 

输入一棵二叉树和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。 注意: 在返回值的list中，数组长度大的数组靠前。
```java
public ArrayList<ArrayList<Integer>> FindPath(TreeNode root, int target) {
    ArrayList<ArrayList<Integer>> res = new ArrayList<>();
    if (root == null) {
        return res;
    }
    Stack<Integer> stack = new Stack<>(); // 保存路径上的节点
    findPath(root, target, stack, res);
    return res;
}

private void findPath(TreeNode root, int target, Stack<Integer> stack, ArrayList<ArrayList<Integer>> listPath) {
    if (root == null) {
        return;
    }
    if (root.left == null && root.right == null) { // 叶节点
        if (root.val == target) { // 路径满足条件
            ArrayList<Integer> list = new ArrayList<>();
            for (int i : stack) {
                list.add(new Integer(i));
            }
            list.add(new Integer(root.val));
            listPath.add(list);
        }
    } else { // 非叶节点
        stack.push(new Integer(root.val));
        findPath(root.left, target - root.val, stack, listPath);
        findPath(root.right, target - root.val, stack, listPath);
        stack.pop(); // 回退
    }
}
```

### 38 复杂链表的复制

输入一个复杂链表（每个节点中有节点值，以及两个指针，一个指向下一个节点，另一个特殊指针指向任意一个节点），返回结果为复制后复杂链表的head。注意，输出结果中请不要返回参数中的节点引用，否则判题程序会直接返回空）

```java
// 解法一：哈希
public RandomListNode Clone_1(RandomListNode pHead) {
    HashMap<RandomListNode, RandomListNode> map = new HashMap<>();
    RandomListNode cur = pHead;
    // 生成对应哈希表
    while (cur != null) {
        map.put(cur, new RandomListNode(cur.label));
        cur = cur.next;
    }
    cur = pHead;
    // 重新遍历为指针赋值
    while (cur != null) {
        map.get(cur).next = map.get(cur.next);
        map.get(cur).random = map.get(cur.random);
        cur = cur.next;
    }
    return map.get(pHead);
}
```

### 39 二叉搜索树与双向链表

输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。

```java
TreeNode lastNodeInList = null; // 链表中的最后一个节点
public TreeNode Convert(TreeNode pRootOfTree) {
    convertNode(pRootOfTree);
	// 找到链表的头
    TreeNode head = lastNodeInList;
    while (head != null && head.left != null) {
        head = head.left; 
    }
    return head;
}
// 中序遍历
private void convertNode(TreeNode node) {
    if (node == null) {
        return;
    }
    TreeNode cur = node;
    if (cur.left != null) {
        convertNode(node.left);
    }
    cur.left = lastNodeInList;
    if (lastNodeInList != null) {
        lastNodeInList.right = cur;
    }
    lastNodeInList = cur;
    if (cur.right != null) {
        convertNode(node.right);
    }
}
```

### 40 序列化二叉树

请实现两个函数，分别用来序列化和反序列化二叉树。

```java
// 先序序列化
String Serialize(TreeNode root) {
    if (root == null) {
        return "#!";
    }
    String res = root.val + "!";
    res += Serialize(root.left);
    res += Serialize(root.right);
    return res;
}
// 先序反序列化
TreeNode Deserialize(String str) {
    String[] values = str.split("!");
    Queue<String> queue = new LinkedList<>();
    for (int i = 0; i < values.length; i++) {
        queue.offer(values[i]);
    }
    return reconPreOrder(queue);
}
private TreeNode reconPreOrder(Queue<String> queue) {
    String value = queue.poll();
    if (value.equals("#")) {
        return null;
    }
    TreeNode head = new TreeNode(Integer.valueOf(value));
    head.left = reconPreOrder(queue);
    head.right = reconPreOrder(queue);
    return head;
}
```

### 41 字符串的排列

输入一个字符串，打印出该字符串中字符的所有排列。例如输入字符串abc，则打印出由字符a、b、c所能排列出来的所有字符串abc、acb、bac、bca、cab和cba。

```java
TreeSet<String> set;// TreeSet能够去掉重复字符串，并且按字典序排序
public ArrayList<String> Permutation(String str) {
    if (str == null || str.trim().length() == 0) {
        return new ArrayList<>();
    }
    char[] chars = str.toCharArray();
    set = new TreeSet<>();
    Permutation(chars, 0);
    return new ArrayList<>(set);
}
// 字符串的全排列，等于所有可能的首字符，加上剩下的字符的全排列的组合。
private void Permutation(char[] chars, int index) {
    if (index == chars.length - 1) {
        set.add(new String(chars));
        return;
    }
    for (int i = index; i < chars.length; i++) {
        swap(chars, i, index);	// 交换首字符
        Permutation(chars, index + 1);
        swap(chars, i, index);	// 恢复
    }
}
private void swap(char[] chars, int indexA, int indexB) {
    char c = chars[indexA];
    chars[indexA] = chars[indexB];
    chars[indexB] = c;
}
```

### 42 数组中出现次数超过一半的数字

数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。 例如输入一个长度为9的数组{1, 2, 3, 2, 2, 2, 5, 4, 2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。

```java
// 解法三：摩尔投票法
public int MoreThanHalfNum_Solution_3(int[] array) {
    if (array == null || array.length == 0) {
        return 0;
    }
    int res = array[0];
    int times = 1;
    for (int i = 1; i < array.length; i++) {
        if (times == 0) {	// 新值出现
            res = array[i];
            times = 1;
        } else if (array[i] == res) {
            times++;
        } else {
            times--;
        }
    }
    // 验证是否满足要求
    times = 0;
    for (int i = 0; i < array.length; i++) {
        if (array[i] == res) {
            times++;
        }
    }
    return (times > array.length / 2) ? res : 0;
}
```

### 43 最小的k个数

输入n个整数，找出其中最小的k个数。 例如输入4、5、1、6、2、7、3、8 这8个数字，则最小的4个数字是1、2、3、4。

```java
// 解法四：利用Arrays的排序算法
public ArrayList<Integer> GetLeastNumbers_Solution_4(int[] input, int k) {
    if (input == null || input.length == 0 || k <= 0 || k > input.length) {
        return new ArrayList<>();
    }
    ArrayList<Integer> resArrList = new ArrayList<>();
    Arrays.sort(input);	// 升序排序

    for (int i = 0; i < k; i++) {
        resArrList.add(input[i]);
    }
    return resArrList;
}
```

### 44 数据流中的中位数

如何得到一个数据流中的中位数？ 如果从数据流中读出奇数个数值，那么中位数就是所有数值排序之后位于中间的数值。如果从数据流中读出偶数个数值，那么中位数就是所有数值排序之后中间两个数的平均值。

```java
// 优先级队列（改为TreeSet就变为了红黑树）
PriorityQueue<Integer> maxQ = new PriorityQueue<Integer>(Collections.reverseOrder()); // 大根堆（数值小的区域）
PriorityQueue<Integer> minQ = new PriorityQueue<Integer>(); // 小根堆（数值大的区域）
// 读取数据
public void Insert(Integer num) {
    // 保证大根堆和小根堆大小相等或者大1
    if (((maxQ.size() + minQ.size()) & 1) == 0) { // 偶数个数据
        maxQ.offer(num);// 入大根堆
        minQ.offer(maxQ.remove());// 大根堆顶部元素放到小根堆里
    } else {
        minQ.offer(num);// 入小根堆
        maxQ.offer(minQ.remove());// 小根堆顶部元素放到大根堆里
    }
}
// 获取数据的中位数
public Double GetMedian() {
    if (maxQ.size() == 0 && minQ.size() == 0) {
        return new Double(0.0);
    }
    if (((maxQ.size() + minQ.size()) & 1) == 0) {
        return (double) (minQ.peek() + maxQ.peek()) / 2;
    } else {
        return (double) minQ.peek();
    }
}
```

### 45 连续子数组的最大和 

输入一个整型数组，数组里有正数也有负数。数组中一个或连续的多个整数组成一个子数组。求所有子数组的和的最大值。要求时间复杂度为O(n)。

```java
// 动态规划
public int FindGreatestSumOfSubArray(int[] array) {
    if (array == null || array.length == 0) {
        return 0;
    }
    int sum = array[0]; // 当前和（相当于dp数组）
    int max = array[0]; // 最大和
    for (int i = 1; i < array.length; i++) {
        sum = sum > 0 ? sum + array[i] : array[i];
        if (sum > max) {
            max = sum;
        }
    }
    return max;
}
```

### 46 从1到n整数中1出现的次数

输入一个整数n，求从1到n这n个整数的十进制表示中1出现的次数。例如 输入12，从1到12这些整数中包含1的数字有1，10，11和12，1一共出现了5次。

```java
// 解法一：遍历判断
public int NumberOf1Between1AndN_Solution(int n) {
    int res = 0;
    for (int i = 1; i <= n; i++) {
        res += number1(i);
    }
    return res;
}
// 判断数字n是否包含几次1
private int number1(int n) {
    int res = 0;
    while (n > 0) {
        if (n % 10 == 1) {
            res++;
        }
        n /= 10;
    }
    return res;
}
```

### 47 数字序列中某一位的数字

数字以0123456789101112131415…的格式序列化到一个字符序列中。在这个序列中，第5位（从0开始计数）是5，第13位是1，第19位是4，等等。请写一个函数求任意位对应的数字。

```java
public static int digitAtIndex(int index){
    if(index<0)
        return -1;
    if(index<10)
        return index;
    int curIndex = 10,length = 2;
    int boundNum = 10;
    while (curIndex+lengthSum(length)<index){
        curIndex+=lengthSum(length);	//curIndex表示每个位数的起始索引位
        boundNum*=10;	//每个位数的起始值
        length++;
    }
    int addNum = (index-curIndex)/length;	//偏移
    int curNum = boundNum + addNum;		//index位所在的数字
    return Integer.toString(curNum).charAt(index-curIndex-addNum*length)-'0';
}
// length位数一共有多少个数
public static int lengthSum(int length){
    int count = 9;
    for(int i=1;i<length;i++)
        count*=10;
    return count*length;
}
```

### 48 把数组排成最小的数

输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3, 32, 321}，则打印出这3个数字能排成的最小数字321323。

```java
public String PrintMinNumber(int[] numbers) {
    if (numbers == null || numbers.length == 0) {
        return "";
    }
    // 自定义的红黑树set(定义比较规则)
    TreeSet<String> set = new TreeSet<String>(new Comparator<String>() {
        @Override
        public int compare(String o1, String o2) {
            return (o1 + o2).compareTo(o2 + o1) > 0 ? 1 : -1;
        }
    });
    // 利用红黑树的排序性质
    for (int number : numbers) {
        set.add(String.valueOf(number));
    }
    StringBuilder sb = new StringBuilder();
    for (String s : set) {
        sb.append(s);
    }
    return sb.toString();
}
```

### 49 把数字翻译成字符串

给定一个数字，我们按照如下规则把它翻译为字符串：0翻译成"a"，1翻译成"b"，……，11翻译成"l"，……，25翻译成"z"。一个数字可能有多个翻译。例如12258有5种不同的翻译，它们分别是"bccfi"、 "bwfi"、 "bczi"、 "mcfi" 和"mzi"。请编程实现一个函数用来计算一个数字有多少种不同的翻译方法。
```java
public int getTranslationCount(int number) {
    if (number < 0) {
        return -1;
    }
    if (number == 1) {
        return 1;
    }
    return getTranslationCount(Integer.toString(number));
}
// 动态规划，从右到左计算。
// f(r-2) = f(r-1)+g(r-2,r-1)*f(r);
// 如果r-2，r-1能够翻译成字符，则g(r-2,r-1)=1，否则为0
public static int getTranslationCount(String number) {
    int f1 = 0, f2 = 1, g = 0;
    int temp;
    for (int i = number.length() - 2; i >= 0; i--) {
        // 前后能够组成字母
        if (Integer.parseInt(number.charAt(i) + "" + number.charAt(i + 1)) < 26) {
            g = 1;
        } else {
            g = 0;
        }
        temp = f2;
        f2 = f2 + g * f1;
        f1 = temp;
    }
    return f2;
}
```

### 50 礼物的最大价值

在一个m×n的棋盘的每一格都放有一个礼物，每个礼物都有一定的价值（价值大于0）。你可以从棋盘的左上角开始拿格子里的礼物，并每次向右或者向下移动一格直到到达棋盘的右下角。给定一个棋盘及其上面的礼物，请计算你最多能拿到多少价值的礼物？
```java
// 动态规划
public int getMaxValue(int[][] arr) {
    if (arr == null || arr.length == 0) {
        return 0;
    }
    int rows = arr.length;
    int cols = arr[0].length;
    int[] maxValue = new int[cols];// 最大值数组
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            int left = 0;
            int up = 0;
            if (i > 0) {
                up = maxValue[i - 1];	//上面的值
            }
            if (j > 0) {
                left = maxValue[j - 1];	//左侧的值
            }
            // 更新
            maxValue[j] = Math.max(up, left) + arr[i][j];
        }
    }
    return maxValue[cols - 1];
}
```

### 51 最长不含重复字符的子字符串

请从字符串中找出一个最长的不包含重复字符的子字符串，计算该最长子字符串的长度。假设字符串中只包含从'a'到'z'的字符。
```java
// 解法一：动态规划
public int longestSubstringWithoutDup_1(String str) {
    if (str == null || str.length() == 0) {
        return 0;
    }
    // 以每一个位置作为结尾的最长不重复字符长度
    int[] dp = new int[str.length()];
    dp[0] = 1;
    int maxdp = 1;
    for (int dpIndex = 1; dpIndex < dp.length; dpIndex++) {
        int i = dpIndex - 1;// 前一个字符
        // 遍历以前一个字符结尾的最长不重复字符
        for (; i >= dpIndex - dp[dpIndex - 1]; i--) {
            if (str.charAt(dpIndex) == str.charAt(i)) {
                break;// 前一个的最长结构中存在当前值
            }
        }
        dp[dpIndex] = dpIndex - i; // 更新
        maxdp = dp[dpIndex] > maxdp ? dp[dpIndex] : maxdp;
    }
    return maxdp;
}
```

### 52 丑数

我们把只包含因子2、3和5的数称作丑数（Ugly Number）。求按从小到大的顺序的第1500个丑数。例如6、8都是丑数，但14不是，因为它包含因子7。习惯上我们把1当做第一个丑数。

```java
public int GetUglyNumber_Solution(int index) {
    if (index <= 0) {
        return 0;
    }
    int[] res = new int[index];
    res[0] = 1;	// 习惯定义1是第一个丑数
    int t2 = 0;
    int t3 = 0;
    int t5 = 0;
    // 我们只求丑数，不要去管非丑数
    for (int i = 1; i < index; i++) {
        // 每个丑数必然是由小于它的某个丑数乘以2，3或5得到的
        res[i] = Math.min(res[t2] * 2, Math.min(res[t3] * 3, res[t5] * 5));
        if (res[i] == res[t2] * 2)
            t2++;
        if (res[i] == res[t3] * 3)
            t3++;
        if (res[i] == res[t5] * 5)
            t5++;
    }
    return res[index - 1];
}
```

### 53 字符串中第一个只出现一次的字符 

在字符串中找出第一个只出现一次的字符。如输入"abaccdeff"，则输出 'b'。

```java
public int FirstNotRepeatingChar(String str) {
    if (str == null || str.length() == 0)
        return -1;
    int[] ch = new int[256];// 统计次数
    for (int i = 0; i < str.length(); ++i) {
        ++ch[str.charAt(i)];
    }
    for (int i = 0; i < str.length(); ++i) {
        if (ch[str.charAt(i)] == 1) {
            return i;
        }
    }
    return -1;
}
```

### 54 字符流中第一个只出现一次的字符

请实现一个函数用来找出字符流中第一个只出现一次的字符。例如，当从字符流中只读出前两个字符"go"时，第一个只出现一次的字符是'g'。当从该字符流中读出前六个字符"google"时，第一个只出现一次的字符是'l'。
```java
public class Code_50_02_FirstCharacterInStream {
    int[] chars;
    int index;
    public Code_50_02_FirstCharacterInStream() {
        chars = new int[256];
        index = 0;
        for (int i = 0; i < chars.length; i++) {
            chars[i] = -1;
        }
    }
    // 字符流插入一个字符
    public void Insert(char ch) {
        if (chars[ch] == -1) {
            chars[ch] = index; // 第一次出现保存的是出现的下标
        } else if (chars[ch] >= 0) {
            chars[ch] = -2; // 已经重复了，置一个无效的数字
        }
        index++;	//插入的顺序下标
    }
    // 返回当前字符流中第一个只出现一次的字符
    public char FirstAppearingOnce() {
        int minIndex = Integer.MAX_VALUE;
        char ch = '#'; // 没有则返回#
        for (int i = 0; i < chars.length; i++) {
            if (chars[i] >= 0 && chars[i] < minIndex) {
                minIndex = chars[i]; // 保证是第一个
                ch = (char) i; // 直接用下标代替字符
            }
        }
        return ch;
    }
}
```

### 55 数组中的逆序对

在数组中的两个数字如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组，求出这个数组中的逆序对的总数。

```java
int count;

public int InversePairs(int[] array) {
    if (array == null || array.length < 2) {
        return 0;
    }
    count = 0;
    mergeSort(array, 0, array.length - 1);
    return count;
}
// 归并排序
private void mergeSort(int[] array, int start, int end) {
    if (start >= end) {
        return;
    }
    int mid = start + (end - start) / 2;
    mergeSort(array, start, mid);
    mergeSort(array, mid + 1, end);
    merge(array, start, mid, end);
}
private void merge(int[] arr, int l, int m, int r) {
    int[] help = new int[r - l + 1];
    int i = 0;
    int p1 = l;		// 左半区
    int p2 = m + 1;	// 右半区
    while (p1 <= m && p2 <= r) {
        count += arr[p1] > arr[p2] ? (m - p1 + 1) : 0;	// 统计逆序对信息
        if (count >= 1000000007) {
            count %= 1000000007;// 额外的取余要求
        }
        help[i++] = arr[p1] < arr[p2] ? arr[p1++] : arr[p2++];
    }
    while (p1 <= m) {
        help[i++] = arr[p1++];
    }
    while (p2 <= r) {
        help[i++] = arr[p2++];
    }
    for (i = 0; i < help.length; i++) {
        arr[l + i] = help[i];
    }
}
```

### 56 两个链表的第一个公共结点

输入两个链表，找出它们的第一个公共结点。

```java
public ListNode FindFirstCommonNode(ListNode pHead1, ListNode pHead2) {
    ListNode node1 = pHead1;
    ListNode node2 = pHead2;
    int size1 = 0;
    int size2 = 0;
    // 分别统计两个链表的长度
    while (node1 != null) {
        node1 = node1.next;
        size1++;
    }
    while (node2 != null) {
        node2 = node2.next;
        size2++;
    }
    node1 = pHead1;
    node2 = pHead2;
    // 长链表先走几步
    while (size1 > size2) {
        node1 = node1.next;
        size1--;
    }
    while (size2 > size1) {
        node2 = node2.next;
        size2--;
    }
    while (node1 != null) {
        if (node1 != node2) {
            node1 = node1.next;
            node2 = node2.next;
        } else {
            break; // 找到了第一个公共点
        }
    }
    return node1;
}
```

### 57 数字在排序数组中出现的次数

统计一个数字在排序数组中出现的次数。例如输入排序数组{1, 2, 3, 3, 3, 3, 4, 5}和数字3，由于3在这个数组中出现了4次，因此输出4。

```java
public int GetNumberOfK(int[] array, int k) {
    if (array == null || array.length == 0) {
        return 0;
    }
    int start = getStartOfK(array, k);
    if (start == -1) {
        return 0;
    }
    int end = getEndOfK(array, start, k);
    return end - start + 1;
}

// 二分查找
private int getStartOfK(int[] array, int k) {
    if (k < array[0] || k > array[array.length - 1]) {
        return -1;
    }
    int left = 0;
    int right = array.length - 1;
    int mid;
    while (left <= right) {
        if (left == right) {
            if (array[left] == k) {
                return left;
            } else {
                return -1;
            }
        }
        // 当长度为2，mid取左值
        mid = left + (right - left) / 2;
        if (array[mid] > k) {
            right = mid - 1;
        } else if (array[mid] < k) {
            left = mid + 1;
        } else {
            right = mid; // 相同的值时向左边继续查找第一次出现的下标
        }
    }
    return -1;
}

private int getEndOfK(int[] array, int start, int k) {
    if (k < array[0] || k > array[array.length - 1]) {
        return -1;
    }
    int left = start;
    int right = array.length - 1;
    int mid;
    while (left <= right) {
        if (left == right) {
            if (array[left] == k) {
                return left;
            } else {
                return -1;
            }
        }
        // 当长度为2，mid取右值
        mid = left + (right - left + 1) / 2;
        if (array[mid] > k) {
            right = mid - 1;
        } else if (array[mid] < k) {
            left = mid + 1;
        } else {
            left = mid; // 相同的值时向右边继续查找最后一次出现的下标
        }
    }
    return -1;
}
```

### 58 0到n-1中缺失的数字

一个长度为n-1的递增排序数组中的所有数字都是唯一的，并且每个数字都在范围0到n-1之内。在范围0到n-1的n个数字中有且只有一个数字不在该数组中，请找出这个数字。

```java
// 二分法
public int getMissingNumber(int[] data) {
    int left = 0;
    int right = data.length - 1;
    int mid;
    while (left <= right) {
        mid = left + (right - left) / 2;
        // 目标找到第一个数字和下标不相等的元素
        if (data[mid] == mid) {
            left = mid + 1;// 相等向右找
        } else {
            right = mid - 1;// 不等向左找（丢失的是第一个不等的元素）
        }
    }
    return left;
}
```

### 59 数组中数值和下标相等的元素

假设一个单调递增的数组里的每个元素都是整数并且是唯一的。请编程实现一个函数找出数组中任意一个数值等于其下标的元素。例如，在数组{-3, -1, 1, 3, 5}中，数字3和它的下标相等。
```java
// 很基本的二分查找
public int getNumberSameAsIndex(int[] data) {
    if (data == null || data.length == 0) {
        return -1;
    }
    int left = 0;
    int right = data.length - 1;
    int mid;
    while (left <= right) {
        mid = left + (right - left) / 2;
        if (data[mid] == mid) {
            return mid;
        } else if (data[mid] > mid) {
            right = mid - 1;
        } else {
            left = mid + 1;
        }
    }
    return -1;
}
```

### 60 二叉搜索树的第k个结点

给定一棵二叉搜索树，请找出其中的第k大的结点。

```java
private int kth = 0;
public TreeNode KthNode(TreeNode pRoot, int k) {
    if (pRoot == null || k <= 0) {
        return null;
    }
    TreeNode result = KthNode(pRoot.left, k);
    if (kth == k) {
        return result;// 左子树已经找到，递归返回
    }
    if (++kth == k) {	//进行了递增操作
        return pRoot;// 根节点
    }
    result = KthNode(pRoot.right, k);// 右子树查找
    return result;// 返回最后的结果
}
```

### 61 二叉树的深度

输入一棵二叉树的根结点，求该树的深度。从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。

```java
public int TreeDepth(TreeNode root) {
    if (root == null) {
        return 0;
    }
    int leftDepth = TreeDepth(root.left);
    int rightDepth = TreeDepth(root.right);
    return Math.max(leftDepth, rightDepth) + 1;
}
```

### 62 平衡二叉树

输入一棵二叉树的根结点，判断该树是不是平衡二叉树。如果某二叉树中任意结点的左右子树的深度相差不超过1，那么它就是一棵平衡二叉树。
```java
// 利用高度判断是否平衡
public boolean IsBalanced_Solution(TreeNode root) {
    if (root == null) {
        return true;
    }
    int left = TreeDepth(root.left);
    int right = TreeDepth(root.right);
    if (Math.abs(left - right) > 1) {
        return false;
    }
    return IsBalanced_Solution(root.left) && IsBalanced_Solution(root.right);
}
// 求二叉树高度
public int TreeDepth(TreeNode root) {
    if (root == null) {
        return 0;
    }
    int leftDepth = TreeDepth(root.left);
    int rightDepth = TreeDepth(root.right);
    return Math.max(leftDepth, rightDepth) + 1;
}
```

### 63 数组中只出现一次的两个数字

一个整型数组里除了两个数字之外，其他的数字都出现了两次。请写程序找出这两个只出现一次的数字。要求时间复杂度是O(n)，空间复杂度是O(1)。

```java
// 利用了异或操作的性质
public void FindNumsAppearOnce(int[] array, int num1[], int num2[]) {
    int result = 0;
    for (int i = 0; i < array.length; i++) {
        result ^= array[i];// 全体异或
    }
    int indexOf1 = findFirstBit1(result);
    if (indexOf1 < 0) {
        return;// 不包含只出现一次的数字
    }
    for (int i = 0; i < array.length; i++) {
        if ((array[i] & indexOf1) == 0) {	//index位是0的一组
            num1[0] ^= array[i];
        } else {	//index位是1的一组
            num2[0] ^= array[i];
        }
    }
}
// 找到num的二进制表示中最右是1的一位
private int findFirstBit1(int num) {
    if (num < 0) {
        return -1;
    }
    int indexOf1 = 1;
    while (num != 0) {
        if ((num & 1) == 1) {
            return indexOf1;// 找到1的位置
        } else {
            num = num >> 1;// 右移
            indexOf1 *= 2; // 索引相应的左移（是将1左移最后结果是二进制只包含1个1的数）
        }
    }
    return -1;
}
```

### 64 数组中唯一只出现一次的数字

在一个数组中除了一个数字只出现一次之外，其他数字都出现了三次。 请找出那个只出现一次的数字。

```java
// 异或操作：两个数字的每一位对应进行加法后对2取模
// 出现三次：改为对3取模
public int findNumberAppearOnce(int[] data) {
    if (data == null || data.length < 3) {
        return 0;
    }
    int[] bitSum = new int[32]; // 每一个bit的状态
    int k = 3;
    for (int i = 0; i < data.length; i++) {
        int indexOfBit1 = 1;
        // 针对每一个数
        for (int j = 31; j >= 0; j--) {
            if ((data[i] & indexOfBit1) != 0) {
                bitSum[j] += 1;
            }
            indexOfBit1 <<= 1; // 左移一位
        }
    }
    int result = 0;
    for (int i = 0; i < 32; i++) {
        result <<= 1;
        result += bitSum[i] % k; // 出现三次的最后会变为0
    }
    return result;
}
```

### 65 和为s的两个数字

输入一个递增排序的数组和一个数字s，在数组中查找两个数，使得它们的和正好是s。如果有多对数字的和等于s，输出两个数的乘积最小的。

```java
public ArrayList<Integer> FindNumbersWithSum(int [] array,int sum) {
    ArrayList<Integer> res = new ArrayList<>();
    if (array == null || array.length < 2) {
        return res;
    }
    int low = 0;
    int high = array.length - 1;
    while (low < high) {
        if (array[low] + array[high] > sum) {
            high--;
        }else if (array[low] + array[high] < sum) {
            low++;
        }else {
            // 两个数相差越大乘积就越小
            res.add(array[low]);
            res.add(array[high]);
            break;
        }
    }
    return res;
}
```

### 66 和为s的连续正数序列

输入一个正数s，打印出所有和为s的连续正数序列（至少含有两个数）。例如输入15，由于1+2+3+4+5=4+5+6=7+8=15，所以结果打印出3个连续序列1～5、 4～6和7～8。

```java
// 双指针
public ArrayList<ArrayList<Integer>> FindContinuousSequence(int sum) {
    if (sum < 3) {
        return new ArrayList<>();
    }
    ArrayList<ArrayList<Integer>> res = new ArrayList<>();
    int low = 1;
    int high = 2;
    int sumOfSequence = low + high;
    while (high <= (sum + 1) / 2) {// 只能存在于[1,(s+1)/2]
        if (sumOfSequence < sum) {
            high++;
            sumOfSequence += high;
        } else if (sumOfSequence > sum) {
            sumOfSequence -= low;
            low++;
        } else {
            ArrayList<Integer> oneOfRes = new ArrayList<>();
            for (int i = low; i <= high; i++) {
                oneOfRes.add(i);
            }
            res.add(oneOfRes);
            high++;
            sumOfSequence += high;
            sumOfSequence -= low;
            low++;
        }
    }
    return res;
}
```

### 67 翻转单词顺序

输入一个英文句子，翻转句子中单词的顺序，但单词内字符的顺序不变。为简单起见，标点符号和普通字母一样处理。例如输入字符串"I am a student. "， 则输出"student. a am I"。

```java
public String ReverseSentence(String str) {
    if (str == null || str.trim().length() <= 1) {
        return str;
    }
    char[] sentence = str.toCharArray();
    // 翻转整个句子
    reverse(sentence, 0, sentence.length - 1);
    int low = 0;
    int high = 0;
    // 翻转每个单词
    while (low != sentence.length && high != sentence.length) {
        // 找到第一个非空格字符
        while (low != sentence.length && sentence[low] == ' ') {
            ++low;
        }
        if (low == sentence.length) {
            break;
        }
        // 找到low后面第一个空格字符
        high = low + 1;
        while (high != sentence.length && sentence[high] != ' ') {
            ++high;
        }
        reverse(sentence, low, high - 1);
        low = high + 1;// 继续查找下一个单词
    }
    return new String(sentence);
}
// 翻转区间字符
private void reverse(char[] str, int start, int end) {
    for (int low = start, high = end; low < high; low++, high--) {
        char t = str[low];
        str[low] = str[high];
        str[high] = t;
    }
}
```

### 68 左旋转字符串

字符串的左旋转操作是把字符串前面的若干个字符转移到字符串的尾部。请定义一个函数实现字符串左旋转操作的功能。比如输入字符串"abcdefg"和数字2，该函数将返回左旋转2位得到的结果"cdefgab"。
```java
public String LeftRotateString(String str, int n) {
    if (str == null || str.length() < 2 || n < 1) {
        return str;
    }
    n %= str.length(); // 有效移动次数
    char[] chars = str.toCharArray();
    reverse(chars, 0, n - 1);// 翻转前k个
    reverse(chars, n, str.length() - 1);// 翻转后n-k个
    reverse(chars, 0, str.length() - 1);// 翻转整个字符串
    return new String(chars);
}
// 翻转区间字符
private void reverse(char[] str, int start, int end) {
    for (int low = start, high = end; low < high; low++, high--) {
        char t = str[low];
        str[low] = str[high];
        str[high] = t;
    }
}
```

### 69 滑动窗口的最大值

给定一个数组和滑动窗口的大小，请找出所有滑动窗口里的最大值。 例如， 如果输入数组{2, 3, 4, 2, 6, 2, 5, 1}及滑动窗口的大小3，那么一共存在6个滑动窗口，它们的最大值分别为{4, 4, 6, 6, 6, 5}.

```java
public ArrayList<Integer> maxInWindows(int[] num, int size) {
    if (num == null || num.length == 0 || size < 1 || size > num.length)
        return new ArrayList<>();
    ArrayList<Integer> result = new ArrayList<>();
    LinkedList<Integer> deque = new LinkedList<>();	//头部表示当前最大值
    // 初始化滑动窗口
    for (int i = 0; i < size; ++i) {
        while (!deque.isEmpty() && num[deque.getLast()] < num[i])
            deque.removeLast();	// 相等时也要弹出，保留更新的值
        // 将数字对应的下标添加到双端队列中
        deque.addLast(i);
    }
    // 第一个最大值放入结果
    result.add(num[deque.getFirst()]);	// 队列中是从大到小
    // 右移滑动窗口
    for (int i = size; i < num.length; ++i) {
        // 判断最大值是否已滑出滑动窗口
        if (i - deque.getFirst() >= size)
            deque.removeFirst();
        while (!deque.isEmpty() && num[deque.getLast()] < num[i])
            deque.removeLast();
        deque.addLast(i);
        result.add(num[deque.getFirst()]);
    }
    return result;
}
```

### 70 队列的最大值

请定义一个队列并实现函数max得到队列里的最大值。要求函数max、push_back和pop_front的时间复杂度都是o(1).

```java
public static class QueueWithMax {
    private Deque<InternalData> queueData;
    private Deque<InternalData> queueMax;
    private int currentIndex;	// 每一个插入队列中的元素索引

    public QueueWithMax() {
        this.queueData = new LinkedList<>();
        this.queueMax = new LinkedList<>();
        this.currentIndex = 0;
    }
    
    public int max() {
        if (queueMax.isEmpty())
            return 0;
        return queueMax.getFirst().value;
    }
    public void pushBack(int value) {
        InternalData addData = new InternalData(value, currentIndex);
        queueData.addLast(addData);

        while (!queueMax.isEmpty() && queueMax.getLast().value < value)
            queueMax.removeLast();

        queueMax.addLast(addData);
        currentIndex++;
    }
    public void popFront() {
        if (queueData.isEmpty())
            return;
        InternalData delData = queueData.removeFirst();
        if (delData.index == queueMax.getFirst().index)
            queueMax.removeFirst();
    }
    private class InternalData {
        public int value;
        public int index;

        public InternalData(int value, int index) {
            this.value = value;
            this.index = index;
        }
    }
}
```

### 71 n个骰子的点数

把n个骰子扔在地上，所有骰子朝上一面的点数之和为s。输入n，打印出s的所有可能的值出现的概率。

```java
public void printProbability(int number) {
    if (number <= 0) {
        return;
    }
    final int point = 6;// 骰子最大点数
    // 两个数组分别存储前后两次的结果
    int[][] result = new int[2][6 * number + 1];// 位置0不用
    for (int i = 1; i <= point; i++) {
        result[1][i] = 1;// 投1个骰子的时候1-6都出现一次
    }
    // 投其他骰子
    for (int num = 2; num < number; num++) {// 遍历所有骰子
        // 投出num个骰子，所有和的可能值(num到point*num)
        for (int i = num; i < point * num + 1; i++) {
            // 对应本次的6个值前一次能够取到的结果值
            for (int j = i - 6; j < i; j++) {
                // 有效值
                if (j > 0) {
                    // num%2切换上下两个数组
                    result[num % 2][i] += result[(num - 1) % 2][j];
                }
            }
        }
    }
    double sum = 0;
    for (int i = number; i < point * number + 1; i++) {
        sum += result[number % 2][i];// 所有和的结果求和
    }
    // 求解概率
    for (int i = number; i < point * number + 1; i++) {
        System.out.println("Probability " + i + ":" + result[number % 2][i] / sum);
    }
}
```

### 72 扑克牌的顺子

从扑克牌中随机抽5张牌，判断是不是一个顺子，即这5张牌是不是连续的。2～10为数字本身，A为1，J为11，Q为12，K为13，而大、小王可以看成任意数字。

```java
public boolean isContinuous(int[] numbers) {
    if (numbers == null || numbers.length == 0) {
        return false;
    }
    Arrays.sort(numbers);	//先进行排序
    int num1 = 0;
    int num2 = 0;
    // 统计0（大小王）的个数
    while (num1 != numbers.length && numbers[num1] == 0) {
        num1++;
    }
    // 统计其余数字之间的空缺数
    for (int i = num1 + 1; i < numbers.length; i++) {
        if (numbers[i] == numbers[i - 1]) {
            return false;// 出现了非0的重复数字
        }
        num2 += numbers[i] - numbers[i - 1] - 1;// 空缺数
    }
    if (num1 >= num2) {
        return true;// 大小王能够填补出现的空缺从而组成顺子
    }
    return false;
}
```

### 73 圆圈中最后剩下的数字

0, 1, …, n-1这n个数字排成一个圆圈，从数字0开始每次从这个圆圈里删除第m个数字。求出这个圆圈里剩下的最后一个数字。

```java
// 约瑟夫问题
public int LastRemaining_Solution(int n, int m) {
    if (n < 1 || m < 1) {
        return -1;
    }
    Node head = new Node(0);
    Node cur = head;
    // 初始化环形单链表
    for (int i = 1; i < n; i++) {
        Node node = new Node(i);
        cur.next = node;
        cur = cur.next;
    }
    cur.next = head;
    cur = head;

    while (true) {
        if (cur.next == cur) {
            return cur.value;
        }
        // 向后移动
        for (int i = 1; i < m; i++) {
            cur = cur.next;
        }
        // 删除当前节点(通过拷贝下一个)
        cur.value = cur.next.value;
        cur.next = cur.next.next;
        // 删除后，cur停在被删节点的下一个
    }
}
```

### 74 股票的最大利润

假设把某股票的价格按照时间先后顺序存储在数组中，请问买卖交易该股票可能获得的利润是多少？例如一只股票在某些时间节点的价格为{9, 11, 8, 5, 7, 12, 16,14}。如果我们能在价格为5的时候买入并在价格为16时卖出，则能收获最大的利润11。
```java
public int maxDiff(int[] data) {
    if (data == null || data.length < 2) {
        return 0;
    }
    int min = data[0];
    int maxDiff = 0;
    for (int i = 1; i < data.length; i++) {
        if (data[i] - min > maxDiff) {
            maxDiff = data[i] - min;// 更新差值
        }
        if (data[i] < min) {
            min = data[i];// 更新最小值
        }
    }
    return maxDiff;
}
```

### 75 求1+2+…+n 

求1+2+…+n，要求不能使用乘除法、for、while、if、else、switch、case等关键字及条件判断语句（A?B:C）。

```java
// 整体思路：递归
public int Sum_Solution(int n) {
    int t = 0;
    // 替换if判断
    @SuppressWarnings("unused")
    boolean b = (n > 0) && ((t = n + Sum_Solution(n - 1)) > 0);	// &&操作符的短路特性
    return t;
}
```

### 76 不用加减乘除做加法

写一个函数，求两个整数之和，要求在函数体内不得使用＋、－、×、÷ 四则运算符号。

```java
public int Add(int num1, int num2) {
    if (num2 == 0) {
        return num1;
    }
    int sum = 0;
    int carry = 0;// 进位
    while (num2 != 0) {
        sum = num1 ^ num2;// 各个位置相加，不考虑进位
        carry = (num1 & num2) << 1;// 与操作表示需要进位，并向前一位进位
        num1 = sum;
        num2 = carry;
    }
    return num1;
}
```

### 77 构建乘积数组

给定一个数组A[0, 1, …, n-1]，请构建一个数组B[0, 1, …, n-1]，其中B中的元素B[i]=A[0]×A[1]×… ×A[i-1]×A[i+1]×…×A[n-1]。不能使用除法。

```java
// 分割成三角形求解
public int[] multiply(int[] A) {
    if (A == null) {
        return null;
    }
    if (A.length == 0) {
        return new int[0];
    }
    // 下半部分三角形
    int[] result = new int[A.length];
    result[0] = 1;
    for (int i = 1; i < A.length; i++) {
        result[i] = result[i - 1] * A[i - 1];
    }
    // 上半部分三角形
    int tmp = 1;
    for (int i = A.length - 2; i >= 0; i--) {
        tmp *= A[i + 1];
        result[i] *= tmp;
    }
    return result;
}
```

### 78 把字符串转换成整数

请你写一个函数StrToInt，实现把字符串转换成整数这个功能。不能使用库函数。数值为0或者字符串不是一个合法的数值则返回0。

```java
public int StrToInt(String str) {
    if (str == null || str.trim().length() == 0) {
        return 0;
    }
    int index = 0;
    long result = 0;
    boolean isPositive = true;
    // 处理符号位
    if (str.charAt(0) == '+' || str.charAt(0) == '-') {
        index++;
        if (index == str.length()) {
            return 0; // 只有一个符号
        }
        if (str.charAt(0) == '-') {
            isPositive = false;
        }
    }

    for (int i = index; i < str.length(); i++) {
        if (str.charAt(i) < '0' || str.charAt(i) > '9') {
            return 0; // 无效字符
        }
        result *= 10;
        result += (str.charAt(i) - '0') * (isPositive ? 1 : -1);
        if (result > Integer.MAX_VALUE || result < Integer.MIN_VALUE) {
            return 0;
        }
    }
    return (int) result;
}
```

### 79 树中两个结点的最低公共祖先

输入两个树结点，求它们的最低公共祖先。此树不是二叉树，并且没有指向父节点的指针。

```java
// 普通树节点定义
public class TreeNode {
    public int val;
    public List<TreeNode> children = new LinkedList<>();

    public TreeNode(int val) {
        this.val = val;
    }
}
public TreeNode getLastParent(TreeNode root, TreeNode node1, TreeNode node2) {
    List<TreeNode> path1 = new ArrayList<>();
    List<TreeNode> path2 = new ArrayList<>();
    getPath(root, node1, path1);
    getPath(root, node2, path2);
    TreeNode lastParent = null;
    for (int i = 0; i < path1.size() && i < path2.size(); i++) {
        if (path1.get(i) == path2.get(i)) {
            lastParent = path1.get(i);
        } else {
            break;
        }
    }
    return lastParent;
}
// 查找从根节点到node的路径
public boolean getPath(TreeNode root, TreeNode node, List<TreeNode> path) {
    if (root == node) {
        return true;
    }
    path.add(root);
    // 查找路径
    for (TreeNode child : root.children) {
        if (getPath(child, node, path)) {
            return true;
        }
    }
    path.remove(path.size() - 1);// 回退
    return false;
}
```

### 80 矩形覆盖

我们可以用2\*1的小矩形横着或者竖着去覆盖更大的矩形。请问用n个2\*1的小矩形无重叠地覆盖一个2*n的大矩形，总共有多少种方法？

```java
// 递归问题
public int RectCover(int target) {
    if (target < 3) {
        return target;
    }else {
        return RectCover(target - 1) + RectCover(target - 2);
    }
}
```

